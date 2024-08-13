# QA Report for **LoopFi**

<details>
<summary>Disclaimer</summary>

**Note:** This report contains two types of information:

1. Classic QA (Low/NC) issues
2. Notes on recommended implementations from all 5 previous Watchpug audits (C/H/M findings) that have been missed, not fixed, or rerouted

As per the guidelines in the [README](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/README.md#L23), fresh findings related to the previous audits are OOS, which is why they've been seperated. To facilitate easier navigation, this report includes two separate tables:

1. A table for the contents of the QA reports
2. A table for the notes on Watchpug's audit findings `(C/H/M)`

Please refer to the appropriate table to ease navigation for either of the sections

</details>


## Table of Contents for QAs

| Issue ID | Description |
| -------- | ----------- |
| [QA-01](#qa-01-chefincentivescontroller#batchupdateallocpoint()-chefincentivescontroller#_massupdatepools()-would-be-broken-when-protocol-heavily-scales) | `ChefIncentivesController#batchUpdateAllocPoint()` `ChefIncentivesController#_massUpdatePools()` would be broken when protocol heavily scales |
| [QA-02](#qa-02-make-repay()-and-liquidations-available-in-the-same-scenario) | Make `repay()` and liquidations available in the same scenario |
| [QA-03](#qa-03-consider-allowing-price-updates-within-the-wait-window-if-the-price-has-moved-beyond-a-certain-threshold) | Consider allowing price updates within the wait window if the price has moved beyond a certain threshold |
| [QA-04](#qa-04-consider-having-a-safety-buffer-applied-to-liquidations) | Consider having a safety buffer applied to liquidations |
| [QA-05](#qa-05-potential-mis-pricing-when-a-token-gets-removed/deregistered-from-a-balancer-pool) | Potential mis-pricing when a token gets removed/deregistered from a Balancer pool |
| [QA-06](#qa-06-users-can-sidestep-the-cooldownduration-in-an-edge-case) | Users can sidestep the `cooldownDuration` in an edge case |
| [QA-07](#qa-07-consider-not-relying-on-an-infrequently-updated-oracle-for-aura-spot-pricing) | Consider not relying on an infrequently updated oracle for AURA spot pricing |
| [QA-08](#qa-08-fallback-oracles-should-be-implemented-in-the-cdpvault) | Fallback oracles should be implemented in the `CDPVault` |
| [QA-09](#qa-09-configuration-changes-could-drastically-affect-users-and-should-be-behind-a-timelock) | Configuration changes could drastically affect users and should be behind a timelock |
| [QA-10](#qa-10-using-spot-prices-directly-for-liquidations-might-be-unfair-for-users-and-leaves-them-at-risk) | Using spot prices directly for liquidations might be unfair for users and leaves them at risk |
| [QA-11](#qa-11-no-checks-for-min/max-circuit-breakers-while-querying-chainlink-data) | No checks for min/max circuit breakers while querying chainlink data |
| [QA-12](#qa-12-tech-savvy-users-have-an-incentive-to-front-run-auravault#claim()) | Tech savvy users have an incentive to front run `AuraVault#claim()` |
| [QA-13](#qa-13-ensure-the-use-of-a-shorter-stale-period-in-production) | Ensure the use of a shorter stale period in production |
| [QA-14](#qa-14-rewards-could-be-emitted-to-some-contracts-unintentionally) | Rewards could be emitted to some contracts unintentionally |
| [QA-15](#qa-15-consider-making-stakinglpeth#unstake()-and-stakinglpeth.sol-as-a-whole-backed-by-pausable-modifiers) | Consider making `StakingLPEth#unstake()` and `StakingLPEth.sol` as a whole backed by pausable modifiers |
| [QA-16](#qa-16-lack-of-slippage-protection-in-auravaults-mint/deposit) | Lack of slippage protection in `AuraVault's` mint/deposit |
| [QA-17](#qa-17-liquidations-could-be-front-ran) | Liquidations could be front ran |
| [QA-18](#qa-18-consider-making-withdrawal-of-assets-via-stakinglpeth#unstake()-a-one-step-process-when-cooldownduration-is-set-to-0) | Consider making withdrawal of assets via `StakingLPEth#unstake()` a one step process when `cooldownDuration` is set to 0 |
| [QA-19](#qa-19-users-can-be-liquidated-in-the-next-block) | Users can be liquidated in the next block |
| [QA-20](#qa-20-restrict-calling-chefincentivescontroller#recovererc20()-on-the-underlying-token) | Restrict calling `ChefIncentivesController#recoverERC20()` on the underlying token |
| [QA-21](#qa-21-consider-relaxing-the-hardcoded-slippage-for-auto-compounding) | Consider relaxing the hardcoded slippage for auto compounding |
| [QA-22](#qa-22-consider-not-having-chainlinks-oracle-address-as-an-immutable-var) | Consider not having Chainlink's oracle address as an immutable var |
| [QA-23](#qa-23-setters-should-always-have-equality-checkers) | Setters should always have equality checkers |
| [QA-24](#qa-24-incorrect-slippage-logic-in-setautocompound()) | Incorrect slippage logic in `setAutocompound()` |
| [QA-25](#qa-25-erroneous-reward-tokens-should-not-be-added) | Erroneous reward tokens should not be added |
| [QA-26](#qa-26-fix-typos) | Fix typos |
| [QA-27](#qa-27-follow-chainlink-best-practices-and-use-proxy-instead-of-price-aggregator-directly) | Follow chainlink best practices and use proxy instead of price aggregator directly |
| [QA-28](#qa-28-incorrect-storage-gap-sizes-are-not-advised) | Incorrect storage gap sizes are not advised |
| [QA-29](#qa-29-import-declarations-should-import-specific-identifiers-rather-than-the-whole-file) | Import declarations should import specific identifiers, rather than the whole file |
| [QA-30](#qa-30-consider-renaming-max_slippage-to-min_slippage) | Consider renaming `MAX_SLIPPAGE` to `MIN_SLIPPAGE` |


## Table of Contents for Notes on WatchPug's `C/H/M` Findings

| Issue ID                                                 | Description                                     |
| -------------------------------------------------------- | ----------------------------------------------- |
| [N-01](#n-01-cc-wp-h4-from-the-watchpug-first-report)    | CC `WP-H4` from the WatchPug first report       |
| [N-02](#n-02-cc-wp-h7-from-the-watchpug-first-report)    | CC `WP-H7` from the WatchPug first report       |
| [N-03](#n-03-cc-wp-m10-from-the-watchpug-first-report)   | CC `WP-M10` from the WatchPug first report      |
| [N-04](#n-04-cc-wp-h3-from-the-watchpug-second-report)   | CC `WP-H3` from the WatchPug second report      |
| [N-05](#n-05-cc-wp-m5-from-the-watchpug-second-report)   | CC `WP-M5` from the WatchPug second report      |
| [N-06](#n-06-cc-re-wp-c2-from-the-watchpug-third-report) | CC `Re: [WP-C2]` from the WatchPug third report |
| [N-07](#n-07-cc-wp-m1-from-the-watchpug-fourth-report)   | CC `WP-M1` from the WatchPug fourth report      |
| [N-08](#n-08-cc-wp-m1-from-the-watchpug-fifth-report)    | CC `WP-M1` from the WatchPug fifth report       |
| [N-09](#n-09-cc-wp-m2-from-the-watchpug-fifth-report)    | CC `WP-M2` from the WatchPug fifth report       |


## QA-01 `ChefIncentivesController#batchUpdateAllocPoint()` `ChefIncentivesController#_massUpdatePools()` would be broken when protocol heavily scales

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L318-L334

```solidity
function batchUpdateAllocPoint(address[] calldata _tokens, uint256[] calldata _allocPoints)
  external
  onlyOwner
{
  if (_tokens.length != _allocPoints.length) revert ArrayLengthMismatch();
  _massUpdatePools();
  uint256 _totalAllocPoint = totalAllocPoint;
  uint256 length = _tokens.length;
  for (uint256 i; i < length; ) {
    VaultInfo storage pool = vaultInfo[_tokens[i]];
    if (pool.lastRewardTime == 0) revert UnknownPool();
    _totalAllocPoint = _totalAllocPoint - pool.allocPoint + _allocPoints[i];
    pool.allocPoint = _allocPoints[i];
    unchecked {
      i++;
    }
  }
  totalAllocPoint = _totalAllocPoint;
  emit BatchAllocPointsUpdated(_tokens, _allocPoints);
}

```

Also see the `_massUpdatePools()` this function calls:https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L452-L462

```solidity
function _massUpdatePools() internal {
  uint256 totalAP = totalAllocPoint;
  uint256 length = poolLength();
  for (uint256 i; i < length; ) {
    _updatePool(vaultInfo[registeredTokens[i]], totalAP);
    unchecked {
      i++;
    }
  }
  lastAllPoolUpdate = block.timestamp;
}

```

The functions iterate through all pools to update their states. As the number of pools grows which is expected as protocol scales, these functions may require gas that exceeds the block gas limit, rendering them unusable.

### Impact

QA, since this relies on the protocol heavily scaling and having pools to the extent where the functions fail due to OOG.

### Recommended Mitigation Steps

Consider handling a fixed number of updates per transaction.

## QA-02 Make `repay()` and liquidations available in the same scenario

### Proof of Concept

The current logic of the `CDPVault`, allows [for the pausing of liquidations](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L509), but not of repayments of debt.

Now whereas this logic is safe and best for the user, this is extremely bad for the protocol, considering, naturally the protocol is to be set in a paused state when a black swan event occurs and until this gets sorted out it wouldn't be unpaused, due to this however a very large loan that's now liquidatable and accruing bad debt can never get liquidated, cause the inherited `whenNotPaused` modifier always causes it to revert.

### Impact

Protocol would be put in an unwanted state, when the Vault is paused and large loans start accruing bad debt since liquidating these loans would not work.

### Recommended Mitigation Steps

Consider removing the `whenNotPaused` from the liquidation logic.
Here's a shortened version of the report:

## QA-03 Consider allowing price updates within the wait window if the price has moved beyond a certain threshold

### Proof of Concept

When updating the price, the keeper is forced to wait for
`updateWaitWindow` even if there's been a massive jump/drop in price.

```solidity
if (block.timestamp - lastUpdate < updateWaitWindow) revert BalancerOracle__update_InUpdateWaitWindow();
```

The fixed update window prevents price updates during rapid market changes, potentially leading to the use of stale prices for an extended period.

### Impact

> Borderline low/med.

### Recommended Mitigation Steps

Consider allowing price updates within the wait window if the price has moved beyond a certain threshold.## QA Consider allowing price updates within the wait window if the price has moved beyond a certain threshold

## QA-04 Consider having a safety buffer applied to liquidations

### Proof of Concept

The [current liquidation implementation ](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L509-L632) allows for a full liquidation of a position, even if repaying a little debt puts the position back afloat see https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L528-L533

```solidity
        // compute collateral to take, debt to repay and penalty to pay
        uint256 takeCollateral = wdiv(repayAmount, discountedPrice);
        uint256 deltaDebt = wmul(repayAmount, liqConfig_.liquidationPenalty);
        uint256 penalty = wmul(repayAmount, WAD - liqConfig_.liquidationPenalty);
        if (takeCollateral > position.collateral) revert CDPVault__tooHighRepayAmount();

```

The liquidator provides a `repayAmount` that covers any amount of debt, which then means that a position that just gets liquidatable even by `1%` can be fully liquidated if the liquidator can provide to [cover the full amount of collateral via the `repayAmount` var](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L509).

### Impact

> Borderline low/medium
> Borrowers of the protocol may be unfairly liquidated due to minor movements in the market when taking out the max loan. Considering the crypto market is very volatile, _In the worst case scenario, a user could be subject to a forced liquidation by the attacker by trying to exploit the spot prices returned from either uniswap/pendle.._

### Recommended Mitigation Steps

Consider giving some sort of buffer like other protocols can be use here to mitigate this issue, e.g AAVE https://docs.aave.com/faq/liquidations. They implement a 50% liquidation if health factor is between 0.9 to 1 and then after 0.9 full liquidation so user will have some buffer time to respond.

## QA-05 Potential mis-pricing when a token gets removed/deregistered from a Balancer pool

### Proof of Concept

In the constructor:

```solidity
(address[] memory tokens, , ) = balancerVault.getPoolTokens(poolId);

uint256 len = tokens.length;
token0 = (len > 0) ? tokens[1] : address(0);
token1 = (len > 1) ? tokens[2] : address(0);
token2 = (len > 2) ? tokens[3] : address(0);
```

These token addresses are then used in price calculations:

```solidity
function _getTokenPrice(uint256 index) internal view returns (uint256 price) {
  address token;
  if (index == 0) token = token0;
  else if (index == 1) token = token1;
  else if (index == 2) token = token2;
  else revert BalancerOracle__getTokenPrice_invalidIndex();

  return chainlinkOracle.spot(token);
}

```

The BalancerOracle contract stores token addresses as immutable variables in the constructor. However, if a token is deregistered from the Balancer pool later, the oracle will continue to assume the old status of the pools, leading to incorrect price calculations.

### Impact

If a token is deregistered, the oracle will use outdated token addresses, resulting in incorrect price calculations and updates.

### Recommended Mitigation

Retrieve the Balancer pool tokens via `getPoolTokens()` whenever needed instead of caching them, to ensure the latest pool composition is always used.

## QA-06 Users can sidestep the `cooldownDuration` in an edge case

### Proof of Concept

`StakingLPEth.sol` enforces a cooldown period before users can finalize withdrawals: https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L88-L114

```solidity
function unstake(address receiver) external {
  UserCooldown storage userCooldown = cooldowns[msg.sender];
  uint256 assets = userCooldown.underlyingAmount;

  if (block.timestamp >= userCooldown.cooldownEnd || cooldownDuration == 0) {
    userCooldown.cooldownEnd = 0;
    userCooldown.underlyingAmount = 0;

    silo.withdraw(receiver, assets);
  } else {
    revert InvalidCooldown();
  }
}

/// @notice redeem assets and starts a cooldown to claim the converted underlying asset
/// @param assets assets to redeem
function cooldownAssets(uint256 assets) external ensureCooldownOn returns (uint256 shares) {
  if (assets > maxWithdraw(msg.sender)) revert ExcessiveWithdrawAmount();

  shares = previewWithdraw(assets);

  cooldowns[msg.sender].cooldownEnd = uint104(block.timestamp) + cooldownDuration;
  cooldowns[msg.sender].underlyingAmount += uint152(assets);

  _withdraw(msg.sender, address(silo), msg.sender, assets, shares);
}

```

But if `cooldownDuration` is reset to 0 after a cooldown initiation, users can bypass this wait immediately. Initially, users must wait until the cooldown period ends to withdraw assets. However, resetting `cooldownDuration` to 0 allows immediate withdrawals, circumventing the intended delay.

### Impact

Users can sidestep the cooldown period if `cooldownDuration` is reset to 0, allowing immediate withdrawals despite the initial requirement to wait, compromising the protocol's risk management strategy if specifically set for these previously attempted withdrawals.

> _NB: This has been attached to the report as I couldn't reach the sponsors to verify if this is intended or not._

### Recommended Mitigation Steps

Modify the withdrawal condition to separately handle active cooldowns and zero `cooldownDuration`, preventing immediate withdrawals unless no cooldown is pending.

## QA-07 Consider not relying on an infrequently updated oracle for AURA spot pricing

### Proof of Concept

The `AuraVault` contract relies on an oracle for calculating the AURA spot price, which is not updated frequently, potentially leading to inaccurate price reflections and impacting various operations within the vault.

The `_getAuraSpot` function in the `AuraVault` contract calculates the AURA spot price by fetching the time-weighted average price from a custom oracle. This oracle is not updated frequently, which leads to discrepancies between the calculated price and the actual market value of AURA.

Here we have an infrequent updates, cause for Balancer/Aura oracles the update to the price is only done whenever a transaction (e.g. swap) within the pool is triggered (see https://etherscan.io/address/0x32296969Ef14EB0c6d29669C550D4a0449130230 for example). So due to the lack of updates, the price provided by the Oracle might not reflect the true value of the assets. Attached in the linked oracle too we can see how even for days no updates were made to the price if tx were not processed.

```solidity
function _getAuraSpot() internal view returns (uint256 price) {
  uint256 ethPrice;
  (, int256 answer, , , ) = AggregatorV3Interface(ETH_CHAINLINK_FEED).latestRoundData();
  ethPrice = wdiv(uint256(answer), ETH_CHAINLINK_DECIMALS);

  IPriceOracle.OracleAverageQuery[] memory queries = new IPriceOracle.OracleAverageQuery[](1);
  queries[0] = IPriceOracle.OracleAverageQuery(IPriceOracle.Variable.PAIR_PRICE, 1800, 0);
  uint256[] memory results = IPriceOracle(auraPriceOracle).getTimeWeightedAverage(queries);

  price = wmul(results[0], ethPrice);
}

```

### Impact

The infrequent updates to the oracle used for AURA spot price calculation can result in the price not accurately reflecting the true market value of AURA. This can affect:

- **Vault Settlement**: Inaccurate asset valuation can lead to incorrect settlement values, impacting the distribution of rewards and potentially causing losses.
- **Deleverage/Liquidation of Accounts**: Users close to the liquidation threshold might face premature deleveraging or liquidation due to undervalued assets.
- **Borrowing**: Overvaluation could allow users to borrow more than they should, based on inaccurate asset prices.

### Recommended Mitigation Steps

Consider using more frequently updated oracles, such as Chainlink/Pyth, as the primary source for price information. If a secondary oracle is needed, ensure it provides timely updates to reflect market conditions accurately.

## QA-08 Fallback oracles should be implemented in the `CDPVault`

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L52-L53

```solidity
    /// @notice Oracle of the collateral token
    IOracle public immutable oracle;
```

Now whenever any pricing logic is to be implemented, this function is called https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L289-L291

```solidity
function spotPrice() public view returns (uint256) {
  return oracle.spot(address(token));
}

```

Problem however is that if anything happens to the underlying oracle then the Vault is completely bricked, for example if the attached oracle is a Chainlink oracle that gets put down for maintenance or for change of address the whole vault's logic that relies on pricing is broken.

### Impact

QA, due to the very low likelihood, however if this occurs, liquidations are completely broken allowing users to accrue bad debt, so is the `modifyCollateralAndDebt()` and every other function that relies on using the spot prices.

### Recommended Mitigation Steps

Since there is already a support of multiple price providers in protocol, consider modifying `CDPVault#spotPrice()` to first try to get the price from the current/primary oracle set, then set a secondary/fallback oracle for the collateral incase the first one fails, this can easily be achieved by a try/catch logic.
To mitigate the risk associated with relying solely on a primary oracle for pricing information in the `CDPVault`, implementing a fallback oracle mechanism is crucial. This approach ensures that if the primary oracle fails or undergoes maintenance, the system can still function by relying on a secondary oracle. This fallback mechanism can be implemented using Solidity's `try`/`catch` error handling mechanism introduced in Solidity version 0.6.x.

Here's how you can modify the `spotPrice()` function in the `CDPVault.sol` contract to incorporate a fallback oracle:

```diff
contract CDPVault {

..snip
    // CDPVault Parameters
    /// @notice Oracle of the collateral token
    IOracle public immutable oracle;
+    /// @notice Secondary/fallback oracle in case the primary oracle fails
+    IOracle public immutable fallbackOracle;

..snip

    function spotPrice() public view returns (uint256) {
-        return oracle.spot(address(token));
+        // Try getting the price from the primary oracle
+        try oracle.spot(address(token)) returns (uint256 price) {
+            return price;
+        } catch {
+            // If the primary oracle call fails, fall back to the secondary oracle
+            return fallbackOracle.spot(address(token));
+        }
+    }


..snip
}


```

## QA-09 Configuration changes could drastically affect users and should be behind a timelock

### Proof of Concept

Several configuration update actions within the protocol can negatively impact users' transactions if changes occur just prior to transaction execution.

For example, take a look at https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/MultiFeeDistribution.sol#L304-L317

```solidity
function setLockTypeInfo(uint256[] calldata lockPeriod_, uint256[] calldata rewardMultipliers_)
  external
  onlyOwner
{
  if (lockPeriod_.length != rewardMultipliers_.length) revert InvalidLockPeriod();
  delete _lockPeriod;
  delete _rewardMultipliers;
  uint256 length = lockPeriod_.length;
  for (uint256 i; i < length; ) {
    _lockPeriod.push(lockPeriod_[i]);
    _rewardMultipliers.push(rewardMultipliers_[i]);
    unchecked {
      i++;
    }
  }
  emit LockTypeInfoUpdated(lockPeriod_, rewardMultipliers_);
}

```

This function updates the lock periods/multipliers, which could then have users committing to transactions under different terms than expected due to these configuration changes.

### Impact

Users may face unexpected terms in their transactions.

### Recommended Mitigation Steps

Allow users to specify expected configuration values as parameters. Transactions should revert if the actual configuration does not match the user-specified expectations or these changes should be applied via a timelock.

## QA-10 Using spot prices directly for liquidations might be unfair for users and leaves them at risk

### Proof of Concept

When liquidating a position, [spot prices are used to check if indeed the position is liquidatable](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L509-L575).

Spot pries in whatever provider are always the latest/easiest manipulatable point of pricing which then means that if a manipulated price is returned a user could be unfailry liquidated.

Alternatively a malicious liquidator could take these steps using one of the decentralised providers:

- Oracle for a collateral token is set to decentralized provider.
- Liquidator manipulates the price
- Unfairly liquidates users

### Impact

> Borderline low/medium, though this seems as a design choice, but it could cause for unfair liquidations for users.

### Recommended Mitigation Steps

Consider using a TWAP logic when liquidating.

## QA-11 No checks for min/max circuit breakers while querying chainlink data

### Proof of Concept

Take a look at the functionality used for getting prices from Chainlink https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/ChainlinkOracle.sol#L87-L110

```solidity
function spot(address token) external view virtual override returns (uint256 price) {
  bool isValid;
  (isValid, price) = _fetchAndValidate(token);
  if (!isValid) revert ChainlinkOracle__spot_invalidValue();
}

function _fetchAndValidate(address token) internal view returns (bool isValid, uint256 price) {
  Oracle memory oracle = oracles[token];
  try AggregatorV3Interface(oracle.aggregator).latestRoundData() returns (
    uint80, /*roundId*/
    int256 answer,
    uint256, /*startedAt*/
    uint256 updatedAt,
    uint80 /*answeredInRound*/
  ) {
    isValid = (answer > 0 && block.timestamp - updatedAt <= oracle.stalePeriod);
    return (isValid, wdiv(uint256(answer), oracle.aggregatorScale));
  } catch {
    // return the default values (false, 0) on failure
  }
}

```

Would be key to note that Chainlink's `latestRoundData` is being queried, but no min/max checks are applied, leading to contracts working with flawed pricing.

Would also be key to note that the price returned from this [is directly used to check the accounts status before liquidations](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L508-L574).

### Impact

If the prices ever go over the min/max boundary then protocol is going to ingest heavily inflated/deflated pricing data as the prices returned would be wrong.

Which in our case would mean that an account that could be unsafe would be assumed to be safe when the price being reported is at the _min_ boundary and way higher than the real price, which means this has quite a high impact with a very low likelihood, but would be key to note that this has happened before with `LUNA`.

### Recommended Mitigation Steps

Consider attaching the min/max checkers for aggregators that have it

## QA-12 Tech savvy users have an incentive to front run `AuraVault#claim()`

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L262-L300

```solidity
function claim(uint256[] memory amounts, uint256 maxAmountIn) external returns (uint256 amountIn) {
  // Claim rewards from Aura reward pool
  IPool(rewardPool).getReward();

  // Compute assets amount to be sent to the Vault
  VaultConfig memory _config = vaultConfig;
  amountIn = _previewReward(amounts[0], amounts[1], _config);

  // Transfer assets to Vault
  require(amountIn <= maxAmountIn, '!Slippage');
  IERC20(asset()).safeTransferFrom(msg.sender, address(this), amountIn);

  // ... (rest of the function)
}

```

This function is used for claiming accumulated rewards by depositing WETH, considering the prices used in the conversion stored for the amount users would pay to get the accumulated rewards [are the ones & also depending on the cliff, the inflation might no longer be protected](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L301-L308), so there is a valid window for tech savvy users to try to front run other honest users attempt at claiming.

### Impact

A tech-savvy user could front-run this transaction, potentially getting the reward amounts at juicy asset prices, leading to unfair advantage or loss for the original claimer, since their tx might revert if not enough rewards are left, or even they might get a worse deal, depending on the [position of the _cliff_](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L347-L362).

### Recommended Mitigation Steps

Consider implementing a commit-reveal scheme for claiming rewards. This approach makes it much harder for attackers to front-run the claim process.

## QA-13 Ensure the use of a shorter stale period in production

### Proof of Concept

When getting the status of the oracle there is a check used before ensuring if the status is valid or not, see https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/BalancerOracle.sol#L151-L153

```solidity
function _getStatus() internal view returns (bool status) {
  status = (safePrice != 0) && block.timestamp - lastUpdate < stalePeriod;
}

```

Issue however is that, based on[examination of test files](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/test/unit/BalancerOracle.t.sol#L33-L35) the value of `stalePeriod` is planned to be `1 day` which is quite long for safe defi contexts and as such should be reconsidered and shortened.

### Impact

QA, but this could lead to an extensive use of faulty prices for a long duration.

### Recommended Mitigation Steps

Consider shortening the duration for `stalePeriod`.
In this case, the contract uses one storage slot for the `oracles` mapping. Therefore, the storage gap should be adjusted to 49 instead of 50. Here's the report in the requested format:

## QA-14 Rewards could be emitted to some contracts unintentionally

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L595-L616

```solidity
function handleActionAfter(
  address _user,
  uint256 _balance,
  uint256 _totalSupply
) external {
  if (!validRTokens[msg.sender] && msg.sender != address(mfd)) revert NotRTokenOrMfd();

  if (_user == address(mfd) || eligibilityExempt[_user]) {
    return;
  }
  if (eligibilityMode == EligibilityModes.FULL) {
    bool lastEligibleStatus = eligibleDataProvider.lastEligibleStatus(_user);
    bool isCurrentlyEligible = eligibleDataProvider.refresh(_user);
    if (isCurrentlyEligible) {
      if (lastEligibleStatus) {
        _handleActionAfterForToken(msg.sender, _user, _balance, _totalSupply);
      } else {
        _updateRegisteredBalance(_user);
      }
    } else {
      _processEligibility(_user, isCurrentlyEligible, true);
    }
  } else {
    _handleActionAfterForToken(msg.sender, _user, _balance, _totalSupply);
  }
}

```

This function is a hook which is used to monitor balance changes in users following certain actions (`_transfer`, `mint`, and `burn`). However, it intentionally excludes tracking balances for `rewardMinter` (the `MiddleFeeDistribution` contract) and the `MultiFeeDistribution` contract to prevent reward emissions to these contracts. Despite this precaution, functions like `requalifyFor`, `stake`, `withdrawExpiredLocksFor`, and `withdrawExpiredLocksForWithOptions` can inadvertently include these distribution contracts if called with their addresses, bypassing the intended restriction.

### Impact

Potential oversight, which could lead to unintended reward emissions to the `MiddleFeeDistribution` and `MultiFeeDistribution` contracts, disrupting the protocol's reward distribution mechanisms and affecting its economic model.

### Recommended Mitigation Steps

Consider implementing additional safeguards or modifiers to restrict certain function calls involving these contracts directly.

## QA-15 Consider making `StakingLPEth#unstake()` and `StakingLPEth.sol` as a whole backed by pausable modifiers

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L88-L100

```solidity
function unstake(address receiver) external {
  UserCooldown storage userCooldown = cooldowns[msg.sender];
  uint256 assets = userCooldown.underlyingAmount;

  if (block.timestamp >= userCooldown.cooldownEnd || cooldownDuration == 0) {
    userCooldown.cooldownEnd = 0;
    userCooldown.underlyingAmount = 0;

    silo.withdraw(receiver, assets);
  } else {
    revert InvalidCooldown();
  }
}

```

This function is used to claim the staking amount after the cooldown has finished or if a cooldown is not set.

Now, the `setCooldownDuration()` function is used to update the cooldown duration: https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L130-L139

```solidity
function setCooldownDuration(uint24 duration) external onlyOwner {
  if (duration > MAX_COOLDOWN_DURATION) {
    revert InvalidCooldown();
  }

  uint24 previousDuration = cooldownDuration;
  cooldownDuration = duration;
  emit CooldownDurationUpdated(previousDuration, cooldownDuration);
}

```

This means if there is a decision to set back a duration for cooldown in other to stop any black swan event or whatsoever users should not be allowed to sidestep this, issue however is that since the `setCooldownDuration` function and the contract as a whole is not pausable, this allows a user to always watch the mempool after the duration is initially set to `0` for when it would be updated to a real value, allowing them to completely sidestep the duration by frontrunning the call to the `setCooldownDuration` function and withdraw without any delays.

### Impact

Low, likelihood is quite low, but a malicious user can frontrun the call to and sidestep the cooldown duration.

### Recommended Mitigation Steps

Consider making the contrcat pausable and then having the `whenNotPaused` modifier to `unstake()` this way the function could be first paused and then.

## QA-16 Lack of slippage protection in `AuraVault's` mint/deposit

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L214-L222

```solidity
function deposit(uint256 assets, address receiver)
  public
  virtual
  override(IERC4626, ERC4626)
  returns (uint256)
{
  uint256 shares = previewDeposit(assets);
  _deposit(_msgSender(), receiver, assets, shares);

  // Deposit  in reward pool
  IERC20(asset()).safeApprove(rewardPool, assets);
  IPool(rewardPool).deposit(assets, address(this));

  return shares;
}

function mint(uint256 shares, address receiver)
  public
  virtual
  override(IERC4626, ERC4626)
  returns (uint256)
{
  uint256 assets = previewMint(shares);
  _deposit(_msgSender(), receiver, assets, shares);

  // Deposit assets in reward pool
  IERC20(asset()).safeApprove(rewardPool, assets);
  IPool(rewardPool).deposit(assets, address(this));

  return assets;
}

```

Both functions help with minting vault shares to the receiver, however there are no slippage protection provided, leaving the depositor exposed to front/run lack of slippage risks

### Impact

The lack of slippage protection in the `mint` and `deposit` functions could lead to front-running attacks & unexpected losses for users.

### Recommended Mitigation Steps

Implement a slippage parameter in the `mint` and `deposit` functions.

## QA-17 Liquidations could be front ran

### Proof of Concept

The current logic of the `CDPVault`, allows [for liquidations to be processed via two different functions](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L509), issue however is that these functions do not protect honest users that are trying to ensure the protocol is always safe, in the sense that a tech savvy user can just front run other honest users attempt at liquidating and steal their rewards.

### Impact

QA, this is a quite popular design logic, but some protocols consider a commit-reveal scheme.

### Recommended Mitigation Steps

Consider implementing a commit-reveal scheme for liquidations.

## QA-18 Consider making withdrawal of assets via `StakingLPEth#unstake()` a one step process when `cooldownDuration` is set to 0

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L88-L100

```solidity
function unstake(address receiver) external {
  UserCooldown storage userCooldown = cooldowns[msg.sender];
  uint256 assets = userCooldown.underlyingAmount;

  if (block.timestamp >= userCooldown.cooldownEnd || cooldownDuration == 0) {
    userCooldown.cooldownEnd = 0;
    userCooldown.underlyingAmount = 0;

    silo.withdraw(receiver, assets);
  } else {
    revert InvalidCooldown();
  }
}

```

The current implementation of `unstake()` requires users to first call either `cooldownAssets` or `cooldownShares` to initiate a cooldown period before they can withdraw their assets, adding an unnecessary step to the withdrawal process when the cooldownDuration is `0`. This design forces users to interact with the contract twice—once to start the cooldown and again to actually withdraw their assets—which increases transaction costs and complexity for the user.

That's to say, the `unstake` function allows withdrawals only after the cooldown period has passed or if `cooldownDuration` is set to 0, as shown in the provided code snippet. However, this process can be streamlined by integrating the cooldown initiation directly into the `unstake` function itself, eliminating the need for separate cooldown function calls.

### Impact

QA, design improvement.

> However the existing process of initiating a cooldown through `cooldownAssets` or `cooldownShares` and then calling `unstake` adds unnecessary steps for users when the duration gets set to `0`.

### Recommended Mitigation Steps

Consider modifying the `unstake` function to automatically initiate the cooldown if the cooldown duration is `0`

## QA-19 Users can be liquidated in the next block

### Proof of Concept

When [borrowing](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L256-L265), users are allowed to provide their collateral == debt they want to take, since this check would then pass when modifying the position https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L451-L455

```solidity
    function modifyCollateralAndDebt(){
        ..snip
        if (
            (deltaDebt > 0 || deltaCollateral < 0) &&
            !_isCollateralized(calcTotalDebt(_calcDebt(position)), collateralValue, config.liquidationRatio)
        ) revert CDPVault__modifyCollateralAndDebt_notSafe();

        ..snip
    }
```

Issuw however is that the iscollaterized check would allow the hinted scenario since the check is inclusive: https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L345-L351

```solidity
function _isCollateralized(
  uint256 debt,
  uint256 collateralValue,
  uint256 liquidationRatio
) internal pure returns (bool) {
  return (wdiv(collateralValue, liquidationRatio) >= debt); //@audit qa users could be liquidatable in the next block
}

```

Which then means that users passing in `(wdiv(collateralValue, liquidationRatio) == debt` would take the debt, but can immediately be liquidatable in the next block.

### Impact

QA

### Recommended Mitigation Steps

Consider making the check strict, this way there is quite a buffer:

```diff
    function _isCollateralized(
        uint256 debt,
        uint256 collateralValue,
        uint256 liquidationRatio
    ) internal pure returns (bool) {
-        return (wdiv(collateralValue, liquidationRatio) >= debt);
+        return (wdiv(collateralValue, liquidationRatio) > debt);
    }
```

## QA-20 Restrict calling `ChefIncentivesController#recoverERC20()` on the underlying token

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L430-L433

```solidity
function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyOwner {
  _recoverERC20(tokenAddress, tokenAmount);
}

```

This function allows the owner to withdraw an arbitrary amount of an arbitrary ERC20 token (tokenAddress) and [transfer it to the msg.sender](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/RecoverERC20.sol#L19-L22). While this functionality provides flexibility for the owner to recover funds, there is a flaw in how it handles withdrawals of the contract's underlying Token.

### Impact

QA, since the function is `onlyOwner` protected, however since the contract's accounting is likely based on the underlying token, this withdrawal would lead to inconsistencies or incorrect accounting records.

### Recommended Mitigation Steps

Modify the `recoverERC20` function to prevent the withdrawal of the underlying token.

## QA-21 Consider relaxing the hardcoded slippage for auto compounding

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/MultiFeeDistribution.sol#L41-L42

```solidity
    // Maximum slippage allowed to be set by users (used for compounding).
    uint256 public constant MAX_SLIPPAGE = 9000; //10%
```

Evidently, there is a hardcoded max as to the amount of slippage a user can set which in our case is used for [compounding](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/MultiFeeDistribution.sol#L41), however this limits functionality, considering some users might want to risk more to ensure they are in the system and can auto compound.

### Impact

QA _seems as a design choice_.

### Recommended Mitigation Steps

Consider relaxing the limit, and the slippage value should be user provided so there shouldn't be any issue since this downside is acceptable to them.

## QA-22 Consider not having Chainlink's oracle address as an immutable var

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/BalancerOracle.sol#L23

```solidity
IOracle public immutable chainlinkOracle;
```

### Impact

Using an immutable reference for the Chainlink oracle address reduces flexibility and could lead to issues if the Chainlink oracle address needs to be updated or if it becomes compromised.

### Recommended Mitigation Steps

Replace the immutable reference with a mutable state variable and implement a function to update the Chainlink oracle address:

```diff
- IOracle public immutable chainlinkOracle;
+ IOracle public chainlinkOracle;
..snip
+function updateChainlinkOracle(address newOracle) external onlyRole(MANAGER_ROLE) {
+    require(newOracle != address(0), "Invalid oracle address");
+    chainlinkOracle = IOracle(newOracle);
+}
```

## QA-23 Setters should always have equality checkers

### Proof of Concept

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L342-L369

```solidity
function setRewardsPerSecond(uint256 _rewardsPerSecond, bool _persist) external onlyOwner {
  _massUpdatePools();
  rewardsPerSecond = _rewardsPerSecond;
  persistRewardsPerSecond = _persist;
  emit RewardsPerSecondUpdated(_rewardsPerSecond, _persist);
}

function setScheduledRewardsPerSecond() internal {
  if (!persistRewardsPerSecond) {
    uint256 length = emissionSchedule.length;
    uint256 i = emissionScheduleIndex;
    uint128 offset = uint128(block.timestamp - startTime);
    for (; i < length && offset >= emissionSchedule[i].startTimeOffset; ) {
      unchecked {
        i++;
      }
    }
    if (i > emissionScheduleIndex) {
      emissionScheduleIndex = i;
      _massUpdatePools();
      rewardsPerSecond = uint256(emissionSchedule[i - 1].rewardsPerSecond);
    }
  }
}

```

These functions are used to make updates to already set values but there are no checks to see if the states are not the already passed in values.

### Impact

QA

### Recommended Mitigation Steps

Consider checking if the value being set is what's already stored and just skip this attempt instead.

## QA-24 Incorrect slippage logic in `setAutocompound()`

### Proof of Concept

Take a look at [this code snippet](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/MultiFeeDistribution.sol#L414-L421):

```solidity
/**
 * @notice Set what slippage to use for tokens traded during the auto compound process on behalf of the user
 * @param slippage the maximum amount of slippage that the user will incur for each compounding trade
 */
function setAutocompound(bool status, uint256 slippage) external {
  autocompoundEnabled[msg.sender] = status;
  if (slippage < MAX_SLIPPAGE || slippage >= PERCENT_DIVISOR) {
    revert InvalidAmount();
  }
  userSlippage[msg.sender] = slippage;
}

```

### Impact

The current implementation of the slippage check is incorrect. It reverts if the slippage is less than `MAX_SLIPPAGE`, which is the opposite of the intended behavior. This means users cannot set a slippage value that is less than the maximum allowed, which is not the desired functionality.

### Recommended Mitigation Steps

Update the slippage check to ensure that the slippage value provided by the user is less than the maximum allowed (`MAX_SLIPPAGE`). The corrected implementation should be:

```solidity
if (slippage >= MAX_SLIPPAGE) {
    revert InvalidAmount();
}
```

This ensures that the user can set a slippage value up to (but not including) `MAX_SLIPPAGE`.

If this the intended behaviour then consider correctly documenting it/naming the functionality.

## QA-25 Erroneous reward tokens should not be added

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/MultiFeeDistribution.sol#L347-L360

```solidity
function addReward(address _rewardToken) external {
  if (_rewardToken == address(0)) revert AddressZero();
  if (!minters[msg.sender]) revert InsufficientPermission();
  if (rewardData[_rewardToken].lastUpdateTime != 0) revert AlreadyAdded();
  rewardTokens.push(_rewardToken);

  Reward storage rd = rewardData[_rewardToken];
  rd.lastUpdateTime = block.timestamp;
  rd.periodFinish = block.timestamp;

  isRewardToken[_rewardToken] = true;
  emit RewardAdded(_rewardToken);
}

```

Evidently, tokens can be added to the reward emission list via the `addReward` function in either the `MultiFeeDistribution` contract by addresses with the minter role. Although these tokens are expected to be `AToken` instances or the `RDNT` token, there's no validation beyond checking against the zero address, allowing for the potential addition of incorrect tokens.

### Impact

> QA, protected by the admin.

> However, adding an unsupported token could cause various protocol components to fail due to non-compliance with the `IAToken` interface and issues with price retrieval via the `AaveOracle`. Moreover, the inability to remove mistakenly added reward tokens necessitates an emergency upgrade to rectify the situation.

### Recommended Mitigation Steps

Implement comprehensive validation logic to prevent the addition of unsupported tokens, ensuring they conform to the required interfaces and standards.

## QA-26 Fix typos

### Proof of Concept

- Take a look at https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L302-L304

```solidity
/**
 * @dev Assumes 0 AURA rewards after INFLATION_PROTECTION_TIME since amount minted is unkown //@audit qa typo, should be unknown
 */
```

- https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L257-L258

```solidity
    /// @notice Deposits underlying tokens to the pool in exhcange for given number of pool shares

```

### Impact

Minor issue... QA that doesn't affect functionality but may cause confusion for developers reading the code.

### Recommended Mitigation Steps

- Correct the typo from "unkown" to "unknown" in the comment.

- Correct the typo from "exhcange" to "exchange" in the second comment.

## QA-27 Follow chainlink best practices and use proxy instead of price aggregator directly

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/ChainlinkOracle.sol#L91-L111

```solidity
function _fetchAndValidate(address token) internal view returns (bool isValid, uint256 price) {
  Oracle memory oracle = oracles[token];
  try AggregatorV3Interface(oracle.aggregator).latestRoundData() returns (
    uint80, /*roundId*/
    int256 answer,
    uint256, /*startedAt*/
    uint256 updatedAt,
    uint80 /*answeredInRound*/
  ) {
    isValid = (answer > 0 && block.timestamp - updatedAt <= oracle.stalePeriod);
    return (isValid, wdiv(uint256(answer), oracle.aggregatorScale));
  } catch {
    // return the default values (false, 0) on failure
  }
}

```

This function fetches and validates the latest price from Chainlink, problem however is that Chainlink recommends using the proxy and not the priceAggregator directly as a best practice.

### Impact

QA - best practice

### Recommended Mitigation Steps

Follow the mentioned [best practices from Chainlink](https://docs.chain.link/data-feeds):
You can call the latestRoundData() function directly on the aggregator, but it is a best practice to use the proxy instead so that changes to the aggregator do not affect your application. Similar to the proxy contract, the aggregator contract has a latestAnswer variable, owner address, latestTimestamp variable, and several others.

## QA-28 Incorrect storage gap sizes are not advised

### Proof of Concept

Multiple instances of this, for example see https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/PendleLPOracle.sol#L17-L45

```solidity
contract PendleLPOracle is IOracle, AccessControlUpgradeable, UUPSUpgradeable {
    using PendleLpOracleLib for IPMarket;
    /*//////////////////////////////////////////////////////////////
                               CONSTANTS
    //////////////////////////////////////////////////////////////*/

    /// @notice Chainlink aggregator address
    AggregatorV3Interface public immutable aggregator;
    /// @notice Stable period in seconds
    uint256 public immutable stalePeriod;
    /// @notice Aggregator decimal to WAD conversion scale
    uint256 public immutable aggregatorScale;
    /// @notice Pendle Market
    IPMarket public immutable market;
    /// @notice TWAP window in seconds
    uint32 public immutable twapWindow;
    /// @notice Pendle Pt Oracle
    IPPtOracle public immutable ptOracle;

    /*//////////////////////////////////////////////////////////////
                              STORAGE GAP
    //////////////////////////////////////////////////////////////*/

    uint256[50] private __gap;

    /*//////////////////////////////////////////////////////////////
                                 ERRORS
    //////////////////////////////////////////////////////////////*/

```

Or even https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/ChainlinkOracle.sol#L28-L34

```solidity
    /*//////////////////////////////////////////////////////////////
                              STORAGE GAP
    //////////////////////////////////////////////////////////////*/

    uint256[50] private __gap;
```

Evidently, more than one storage slot has been used in these contracts and as such the gap `var` should reflect this

### Impact

The incorrect storage gap size could lead to potential issues during future upgrades. If new variables are added in upgraded versions, they might overwrite the storage gap, potentially causing storage collisions and unexpected behavior.

### Recommended Mitigation Steps

Adjust the storage gap size to account for the storage slot already used in all instances were upgrades could occur.

This ensures that the total number of storage slots reserved for the contract (including both used slots and the gap) remains at 50, maintaining the intended/safe storage layout for future upgrades.

## QA-29 Import declarations should import specific identifiers, rather than the whole file

### Proof of Concept

Multiple instances in scope, for example take a look at https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L4-L12

```solidity
import './Interfaces/IBorrowerOperations.sol';
import './Interfaces/IERC3156FlashLender.sol';
import './Interfaces/IEBTCToken.sol';
import './Interfaces/ICdpManager.sol';
import './Interfaces/ISortedCdps.sol';
import './Interfaces/IPriceFeed.sol';
import './Dependencies/ICollateralToken.sol';
import { ICdpManagerData } from './Interfaces/ICdpManagerData.sol';
import './Dependencies/SafeERC20.sol';

```

Evidently, the imports being done is not name specific, but this is not the best implementation cause this could lead to polluting the symbol namespace.

### Impact

QA, albeit this could lead to the potential pollution of the symbol namespace and a slower compilation speed.

### Recommended Mitigation Steps

Consider using import declarations of the form `import {<identifier_name>} from "some/file.sol"` which avoids polluting the symbol namespace making flattened files smaller, and speeds up compilation (but does not save any gas)

## QA-30 Consider renaming `MAX_SLIPPAGE` to `MIN_SLIPPAGE`

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/MultiFeeDistribution.sol#L41-L43

```solidity
    // Maximum slippage allowed to be set by users (used for compounding).
    uint256 public constant MAX_SLIPPAGE = 9000; //10%
    uint256 public constant PERCENT_DIVISOR = 10000; //100%
```

The naming of the `MAX_SLIPPAGE` constant is misleading. It suggests that this value represents the maximum slippage allowed, but the actual logic in the `setAutocompound` function checks if the slippage is less than this value. This can cause confusion for developers and users interacting with the contract.
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/MultiFeeDistribution.sol#L414-L420

```solidity
function setAutocompound(bool status, uint256 slippage) external {
  autocompoundEnabled[msg.sender] = status;
  if (slippage < MAX_SLIPPAGE || slippage >= PERCENT_DIVISOR) {
    revert InvalidAmount();
  }
  userSlippage[msg.sender] = slippage;
}

```

### Impact

QA

### Recommended Mitigation Steps

Rename the `MAX_SLIPPAGE` constant to `MIN_SLIPPAGE` to accurately reflect its purpose. The updated code should look like this:

```diff
    // Minimum slippage allowed to be set by users (used for compounding).
-    uint256 public constant MAX_SLIPPAGE = 9000; //10%
+    uint256 public constant MIN_SLIPPAGE = 9000; //10%
    uint256 public constant PERCENT_DIVISOR = 10000; //100%
```

Additionally, update the `setAutocompound` function to use the new constant name:

```diff
- if (slippage < MAX_SLIPPAGE || slippage >= PERCENT_DIVISOR) {
+ if (slippage < MIN_SLIPPAGE || slippage >= PERCENT_DIVISOR) {
    revert InvalidAmount();
}
```

This change will make the code more intuitive and reduce the likelihood of misunderstandings.

# Notes

## Links to CC'd audits reports here:

- Report #1 https://notes.watchpug.com/p/18ea3089e2esgBHp
- Report #2 https://notes.watchpug.com/p/1909aa8a565HVvGe
- Report #3 https://notes.watchpug.com/p/190becc04cemgrXz
- Report #4 https://notes.watchpug.com/p/190c8fbf44ek5zZ4
- Report #5 https://notes.watchpug.com/p/190dd9d39acrEJAv

## N-01 CC `WP-H4 `from the WatchPug first report

There is still no definite code for interest accounting & the share price math used in PoolV3 deposit and withdrawal operations is always `1.0`, not taking into account

## N-02 CC `WP-H7` from the WatchPug first report

The issue [WP-H7] still seems unfixed because the contract continues to use the `calcDecrease` function for profit calculations in liquidation scenarios, including cases of bad debt. This function assumes all debt can be repaid, which **may not be true when liquidating underwater positions**. The contract still lacks a separate mechanism for accurately calculating losses in bad debt situations, leading to incorrect profit or loss calculations during liquidations.

## N-03 CC `WP-M10 `from the WatchPug first report

When liquidating an insolvent position the `loss` parameter is still not attached to the query for [pool.repayCreditAccount()](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L563), which causes the protocol to undermine the losses it gets.

## N-04 CC `WP-H3` from the WatchPug second report

The [current implementation](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L607) still lacks taking into account any accumulated penalty when calculating the loss, leaving the window still open.

## N-05 CC `WP-M5` from the WatchPug second report

There is [still no approval mechanism](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/Flashlender.sol#L105) for when sending back the tokens to the flashlender, which could bubble up a revert.

## N-06 CC `Re: [WP-C2]` from the WatchPug third report

Neither of the suggested fixes have been applied, since the msg.sender can still be a different account rather than the receiver in [Flashlender#flashloan()](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/Flashlender.sol#L87-L110) and there is no initiator check in [PositionAction#onFlashloan()](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction.sol#L378-L427).

## N-07 CC WP-M1 from the WatchPug fourth report

BalancerOracle [still uses the legacy `totalSupply()` with updating the `currentPrice` ](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/BalancerOracle.sol#L114-L137) which would mean the wrong amount of supplied would be included in the calculations when pools with preminted BPT are integrated.

## N-08 CC `WP-M1` from the WatchPug fifth report

When [liquidating positions via `liquidatePosition()` ](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L509) `discountedPrice` is still not being used when determining if a position is in bad debt status, which means that this functionality can still be used for bad debt positions instead of offloading it to only `liquidatePositionBadDebt()` can be used for bad debt positions.

## N-09 CC `WP-M2` from the WatchPug fifth report

No documentation was attached on this, the implementation was left as is, I'm thinking on the assumption that the decimals would always be `18` but this should be explicitly stated/documented.
