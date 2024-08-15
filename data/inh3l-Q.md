
### 1. ChainlinkOracle.sol's `_authorizeUpgrade` doesn't perform a status check before upgrading.

Links to affected code *

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/ChainlinkOracle.sol#L69-L70

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/ChainlinkOracle.sol#L38-L39

#### Impact


```solidity

    function _authorizeUpgrade(address /*implementation*/) internal virtual override onlyRole(MANAGER_ROLE) {} 
```


As an additional result, this declared error is unused.
```solidity

    error ChainlinkOracle__authorizeUpgrade_validStatus();
```


#### Recommended Mitigation Steps

Recommend checking for the status.
***



### 2. No check for min-max values when handling chainlink prices

Links to affected code *

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/ChainlinkOracle.sol#L96-L110

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/PendleLPOracle.sol#L120-L121

#### Impact

ChainlinkOracle.sol and PendleOracle.sol both query token prices from chainlink. Depending on the token, When its deviates significantly from a predefined price range, Chainlink aggregators activate a circuit breaker mechanism. In crash situations, this mechanism causes the oracle to consistently return the minimum price instead of the actual price of the asset. Consequently, protcol wil, continue to work with this asset but at an incorrect price.

```solidity
    function _fetchAndValidate(address token) internal view returns (bool isValid, uint256 price) {
        Oracle memory oracle = oracles[token];
        try AggregatorV3Interface(oracle.aggregator).latestRoundData() returns (
            uint80 /*roundId*/,
            int256 answer,
            uint256 /*startedAt*/,
            uint256 updatedAt,
            uint80 /*answeredInRound*/
        ) {
            isValid = (answer > 0 && block.timestamp - updatedAt <= oracle.stalePeriod);
            return (isValid, wdiv(uint256(answer), oracle.aggregatorScale));
```

```solidity
        try AggregatorV3Interface(aggregator).latestRoundData() returns (
            uint80 roundId, int256 answer, uint256 /*startedAt*/, uint256 updatedAt, uint80 answeredInRound
        ) {
            isValid = (answer > 0 && answeredInRound >= roundId && block.timestamp - updatedAt <= stalePeriod);
            return (isValid, wdiv(uint256(answer), aggregatorScale));
```
#### Recommended Mitigation Steps

Recomend checking the returned answer against the minPrice/maxPrice and revert if the answer is outside of the bounds.



***

### 3. No check for roundid in ChainlinkOracle.sol

Links to affected code *

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/ChainlinkOracle.sol#L95-L107

#### Impact

ChainlinkOracle.sol doesn't validate the round at which the answer is retuned like is done in [PendleLPOracle.sol](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/PendleLPOracle.sol#L120-L121)
```solidity
    function _fetchAndValidate(address token) internal view returns (bool isValid, uint256 price) {
        Oracle memory oracle = oracles[token];
        try AggregatorV3Interface(oracle.aggregator).latestRoundData() returns (
            uint80 /*roundId*/,
            int256 answer,
            uint256 /*startedAt*/,
            uint256 updatedAt,
            uint80 /*answeredInRound*/
        ) {
            isValid = (answer > 0 && block.timestamp - updatedAt <= oracle.stalePeriod);
            return (isValid, wdiv(uint256(answer), oracle.aggregatorScale));
```

#### Recommended Mitigation Steps

Recommend introducing the check for roundid

***

### 4. CoolDown period design overwirtes previous periods and can lead to longer times for users

Links to affected code *

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L92

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L109

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L122

#### Impact

In `cooldownAssets` and `cooldownShares`, the `cooldownEnd` timing is overwritten when a new cool down request is made. As a reuslt, users that are very active in the staking, and withdrawing could be forced to endure much longer times since they'll have to wait for longer after they've made an initial request/

If for instance, a user cools down half their assets, and waits for the `cooldownDuration`, sometime later before cooldown is over, they decide to cooldown more assets, they'll have to start the wait all over again which can be really incovieneient.
```solidity
    function cooldownAssets(uint256 assets) external ensureCooldownOn returns (uint256 shares) {
        if (assets > maxWithdraw(msg.sender)) revert ExcessiveWithdrawAmount();

        shares = previewWithdraw(assets);

        cooldowns[msg.sender].cooldownEnd = uint104(block.timestamp) + cooldownDuration;
        cooldowns[msg.sender].underlyingAmount += uint152(assets);

        _withdraw(msg.sender, address(silo), msg.sender, assets, shares);
    }
```
```solidity
    function cooldownShares(uint256 shares) external ensureCooldownOn returns (uint256 assets) {
        if (shares > maxRedeem(msg.sender)) revert ExcessiveRedeemAmount();

        assets = previewRedeem(shares);

        cooldowns[msg.sender].cooldownEnd = uint104(block.timestamp) + cooldownDuration;
        cooldowns[msg.sender].underlyingAmount += uint152(assets);

        _withdraw(msg.sender, address(silo), msg.sender, assets, shares);
    }
```

#### Recommended Mitigation Steps

Recommend using a cooldown id method instead. Mapping the id to the user, and to the cooldown details. Everytime a new cooldown request is made, a new id will be attacked to the request. That way, users will not be forced to endure longer times.

***

### 5. Consider introducing functions to cancel cooldowns too.

Links to affected code *

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L104

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L117

#### Impact

It can be useful incase users change their mind about unstaking.


### 6. Use of CREATE1 when deploying silos mKake deployment vulnerable to reorg issues

Links to affected code *

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L59

#### Impact

In the rare case that a [reorg](https://decrypt.co/101390/ethereum-beacon-chain-blockchain-reorg) occurs when StakingLPEth contract is deployed, the silo may be deployed with an address different from the one expected and pre-sent funds may be directed to the wrong silo instead, which can lead to fund loss.

```solidity
    constructor(
        address _liquidityPool,
        string memory _name,
        string memory _symbol
    ) ERC4626(IERC20(_liquidityPool)) ERC20(_name, _symbol) {
        silo = new Silo(address(this), _liquidityPool);
        cooldownDuration = MAX_COOLDOWN_DURATION;
    }
```

#### Recommended Mitigation Steps

Recommend using CREATE2 instead with salt.

***

### 7. `availableLiquidity` can be manipulated through donations

Links to affected code *

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L202-L204

#### Impact

Due to tracking of balance of token, any user who wants to manipulate values depending on the `availableLiquidity` param can do so by sending the underlying token directly to the contract. It's more advisable to use intrnal accounting instead.

```solidity
    function availableLiquidity() public view override returns (uint256) {
        return IERC20(underlyingToken).balanceOf(address(this)); // U:[LP-3]
    }
```

***

### 8. Getting tokens/user funds out of protocol should not be pausable

Links to affected code *

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L239

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L299

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L323

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L518
#### Impact

In PoolV3.sol and CDPVault.sol, the `withdraw` and `redeem` functions can be paused. Ideally, this shouldn't be so because in case of an emergency, users should be able to get their funds out when the contract is paused. Also, if the admin gets malicious and pauses the contract while renouncing his admin role, the tokens risk being stuck in the contract forever. The same can also be observed in ChefIncentivesController.sol in which the `claim` function can also be paused.

```solidity
   function withdraw(
        uint256 assets,
        address receiver,
        address owner
    )
        public
        override(ERC4626, IERC4626)
        whenNotPaused // U:[LP-2A]
        whenNotLocked
        nonReentrant // U:[LP-2B]
        nonZeroAddress(receiver) // U:[LP-5]

```
```solidity    
    function redeem(
        uint256 shares,
        address receiver,
        address owner
    )
        public
        override(ERC4626, IERC4626)
        whenNotPaused // U:[LP-2A]
        whenNotLocked
        nonReentrant // U:[LP-2B]
        nonZeroAddress(receiver) // U:[LP-5]
        returns (uint256 assets)
    {
```

```solidity
    function claim(address _user, address[] memory _tokens) public whenNotPaused {
//...
    }
```
#### Recommended Mitigation Steps

Recommend removing the `whenNotPaused` modifier from the functions.
***

### 9. `flashLoan` fee calculation and check can be optimized

Links to affected code *

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/Flashlender.sol#L93-L94

#### Impact

In the `flashLoan` function, the function checks if token being flashloaned is the same as underlying token, then attempts to calculates the fee. This, as can be seen has also been done in the [`flashFee`](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/Flashlender.sol#L75) function. So the flashfee function can be called directly instead.

```solidity
    function flashLoan(
        IERC3156FlashBorrower receiver,
        address token,
        uint256 amount,
        bytes calldata data
    ) external override nonReentrant returns (bool) {
        if (token != address(underlyingToken)) revert Flash__flashLoan_unsupportedToken();
        uint256 fee = wmul(amount, protocolFee);
        uint256 total = amount + fee;
...
    }
```

#### Recommended Mitigation Steps

```solidity
    function flashLoan(
        IERC3156FlashBorrower receiver,
        address token,
        uint256 amount,
        bytes calldata data
    ) external override nonReentrant returns (bool) {
        uint256 fee = flashFee(token, amount);
        uint256 total = amount + fee;
...
    }
```
***

### 10. Querying curve's `get_virtual_price` should be done carefully due to its vulnerability to read-only reentrancy.

Links to affected code *

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/ICurvePool.sol#L4-L8

#### Impact

While not in use in the codebase as far I can tell, it's still in scope, so I figured its worth mentioning. See [here](https://chainsecurity.com/heartbreaks-curve-lp-oracles/) for more info. Depending on its eventual integration into the codebase, it's best to use the function carefully to avoid future manipulations.

```solidity
/// @notice Lightweight interface used to interrogate Curve pools
interface ICurvePool {
    function get_virtual_price() external view returns (uint256); 
}

```

#### Recommended Mitigation Steps

Recommendation is to always call a reentrancy protected function on the Curve pool, e.g. `remove_liquidity` with zero amounts. This will revert if the Curve pool is locked and succeed when not locked without actually altering state in the Curve pool. This can be done before actually calling the `get_virtual_price` function, and for this reason, I'd recommend introducing it into the interface.
***

### 11. Consider using safeTransfer instead of approval + safeTransferFrom

Links to affected code *

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction.sol#L556-L557

#### Impact

In PositionAction.sol, the `_borrow` function attempts to handle underlying token swaps by first approving the itself to spend the token, then safe transferring the token from itself to the creditor. This is unneccessary, since the underlying token is being transferred from the contract. The `safeTransfer` method handles this just fine. Also, since the contract may be working with any ERC20 tokens, some ERC20 tokens do not require the token owner to approve itself before being able to call the `safeTransferFrom` method, making the call to `forceApprove` more or less redundant.

> Pool and swap actions can use any ERC-20 to perform swaps

```solidity
    function _borrow(address vault, address position, CreditParams calldata creditParams) internal {
        ICDPVault(vault).modifyCollateralAndDebt(position, address(this), address(this), 0, toInt256(creditParams.amount));
        if (creditParams.auxSwap.assetIn == address(0)) {
            underlyingToken.forceApprove(address(this), creditParams.amount);
            underlyingToken.safeTransferFrom(address(this), creditParams.creditor, creditParams.amount);
//...
    }
```
#### Recommended Mitigation Steps

Recommend just using the `safeTransfer` method instead.
```diff
    function _borrow(address vault, address position, CreditParams calldata creditParams) internal {
        ICDPVault(vault).modifyCollateralAndDebt(position, address(this), address(this), 0, toInt256(creditParams.amount));
        if (creditParams.auxSwap.assetIn == address(0)) {
-            underlyingToken.forceApprove(address(this), creditParams.amount);
-            underlyingToken.safeTransferFrom(address(this), creditParams.creditor, creditParams.amount);
+            underlyingToken.safeTransfer(creditParams.creditor, creditParams.amount);
//...
    }
```
***

### 12. Calls to balancer's `getPoolTokens` should use reentrancy protection 

Links to affected code *

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/BalancerOracle.sol#L82

#### Impact

Balancer disclosed a read-only reentrancy [vulnerability](https://forum.balancer.fi/t/reentrancy-vulnerability-scope-expanded/4345) in the Balancer Vault. The effect of this reentrancy is that Balancer pools are susceptible to manipulation of their external queries. What this means is that integrations with balancer vaults must carefully assess the data returned from the vaults. Some protocols, were unaware of this got [hacked](https://twitter.com/spreekaway/status/1643313471180644360) as a result. One of the functions affected is the `getPoolTokens` function which is queried when BalancerOracle.sol is deployed. Via reentrancy, an attacker can force token balances and BPT supply to be out of sync, creating very inaccurate BPT prices.


```solidity
    constructor(
//...
        (address[] memory tokens, , ) = balancerVault.getPoolTokens(poolId);
//...
```

Also, price updates are made quering various price calculations using pool totalSupply, normalized weights, so it is clearly vulnerable to synchronization issues between the data points. 

```solidity
    function update() external virtual onlyRole(KEEPER_ROLE) returns (uint256 safePrice_) {
        if (block.timestamp - lastUpdate < updateWaitWindow) revert BalancerOracle__update_InUpdateWaitWindow();
        // update the safe price first
        safePrice = safePrice_ = currentPrice;
        lastUpdate = block.timestamp;

        uint256[] memory weights = IWeightedPool(pool).getNormalizedWeights();
        uint256 totalSupply = IWeightedPool(pool).totalSupply();

        uint256 totalPi = WAD;
        uint256[] memory prices = new uint256[](weights.length);
        // update balances in 18 decimals
        for (uint256 i = 0; i < weights.length; i++) {
            // reverts if the price is invalid or stale
            prices[i] = _getTokenPrice(i);
            uint256 val = wdiv(prices[i], weights[i]);
            uint256 indivPi = uint256(wpow(int256(val), int256(weights[i])));

            totalPi = wmul(totalPi, indivPi);
        }

        currentPrice = wdiv(wmul(totalPi, IWeightedPool(pool).getInvariant()), totalSupply);
    }
```

#### Recommended Mitigation Steps

Recommend using the provided [balancer library](https://github.com/balancer/balancer-v2-monorepo/blob/3ce5138abd8e336f9caf4d651184186fffcd2025/pkg/pool-utils/contracts/lib/VaultReentrancyLib.sol) to protect from these manipulations.

***

### 13. Keeper role not immediately declared upon contract initialization.

Links to affected code *

#### Impact

During initialization of BalancerOracle.sol, we can see that the manager and admin roles are granted. The Keeper role is however skipped.

```solidity
    function initialize(address admin, address manager) external initializer {
        // init. Access Control
        __AccessControl_init();
        // Role Admin
        _grantRole(DEFAULT_ADMIN_ROLE, admin);
        // Credit Manager
        _grantRole(MANAGER_ROLE, manager);
    }
```

The `update` function can only be called by the keeper. So the function will be unavailable until the default admin directly grants the role. As a result, any contracts depending on the Balancer Oracle will be temporarily unusable.

```solidity
    function update() external virtual onlyRole(KEEPER_ROLE) returns (uint256 safePrice_) {
//...
    }
```

#### Recommended Mitigation Steps

Recommend granting the role upon initialization.


***

### 14. Twap window should include a check to make sure its not less than 30 minutes, since its immutable and cannot be changed

Links to affected code *

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/PendleLPOracle.sol#L60-L61

#### Impact

When PendleLPOracle.sol is deloyed, the twap window is set. There's no check for how long or short the window can be. I'd recommend adding a check to ensure that its not set less than 30 minutes or 1800 seconds. 1800 seconds is the typical that is used by [Uniswap](https://blog.uniswap.org/uniswap-v3-oracles) in their studies. This is done because it makes manipulations very expensive and manipulators risk heavy losses. Using a lower twap window risks actual manipulations taking place as attackers have been known to use their own capital (instead of flash loan) to keep the price manipulated for more than a block, making them vulnerable to arbitrage as shown in [Rari's Fuse hack](https://cmichel.io/replaying-ethereum-hacks-rari-fuse-vusd-price-manipulation/), where the attacker risked their capital and waited for multiple blocks. The root cause of that hack was due to price manipulation of the Uniswap V3 TWAP oracle, which had a TWAP duration of 600 secs.


```solidity
        market = IPMarket(market_);
        twapWindow = twap_;
```

#### Recommended Mitigation Steps

Add a check to ensure that the `twap_` paramter is not less than 1800 seconds.
***


### 15. Unused role can be removed

Links to affected code *

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L53

#### Impact

In CDPVault.sol, the vault unwinder role is declared.

```solidity
bytes32 constant VAULT_UNWINDER_ROLE = keccak256("VAULT_UNWINDER_ROLE");

```

But using the search functionality, we can see that it's not used anywhere (only declared in CDPVault.sol) in the protocol making it redudndant and can be safely removed.

<img width="1198" alt="search" src="https://gist.github.com/user-attachments/assets/a07b63be-2c6e-481c-a94e-7d02dfa4a21e">

***

