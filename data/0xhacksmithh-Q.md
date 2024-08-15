### [Low-] When Protocol is in `paused` mode `Borrower` unable to repay their `Loans`

During `pause` mode a Borrower can't able to pay his debt.

So its possible that during that period his position will move towards liquidation due to market movement and he will not able to secure his position.
And when Protocol resumed again then his position could liquidated 

The Base cause as following
- `repay()` don't have any `whenNotPaused` modifier 
- But `repay()` internally call `modifyCollateralAndDebt()` and it calls `repayCreditAccount()` of `PoolV3.sol` contract
- This `repayCreditAccount()` have a `whenNotPaused` modifier
- so in paused state whole `repay()` will get Reverted

```solidity
    function repayCreditAccount(
        uint256 repaidAmount,
        uint256 profit,
        uint256 loss
    )
        external
        override
        creditManagerOnly // U:[LP-2C]
        whenNotPaused // U:[LP-2A]
        nonReentrant // U:[LP-2B]
    {
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/PoolV3.sol#L529-L538

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/CDPVault.sol#L428

#### Mitigation
Protocol should allow some grace period in this pause-unpause type situation to borrower, so that they can add funds in this type of situation if they wants

OR,

Crusial function like `Rapay()` should work as it is even in Pause mode, those functions should not effected by `whenNotPaused` modifier

 

### [Low-] Check Prev value before chage state

```solidity
    function setPermissionAgent(address agent, bool permitted) external {
        _permittedAgents[msg.sender][agent] = permitted;
        emit SetPermittedAgent(msg.sender, agent, permitted);
    }
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/utils/Permission.sol#L57-L60

```solidity
    function modifyPermission(address owner, address caller, bool permitted) external {
        if (owner != msg.sender && !_permittedAgents[owner][msg.sender])
            revert Permission__modifyPermission_notPermitted();
        _permitted[owner][caller] = permitted;
        emit ModifyPermission(msg.sender, owner, caller, permitted);
    }
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/utils/Permission.sol#L47-L52


### [Low-] If `agent` goes malicious then he can set permission to any address who can Borrow max on behalf of Owner which subject to liquidation probability.

In this situation Owner positions will be at risk, as permited address can max amount possible against the Owner which may cause to liquidation

```solidity
 function setPermissionAgent(address agent, bool permitted) external {
        _permittedAgents[msg.sender][agent] = permitted;
        emit SetPermittedAgent(msg.sender, agent, permitted);
    }
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/utils/Permission.sol#L57-L60

### [Low-]  variable naming could be improved cause it could easily confused with cumulativeIndex
```solidity
    function _getTokenQuotaParamsOrRevert(
        TokenQuotaParams storage tokenQuotaParams
    ) internal view returns (uint16 rate, uint192 cumulativeIndexLU, uint16 quotaIncreaseFee) {
        assembly {
            let data := sload(tokenQuotaParams.slot)
            rate := and(data, 0xFFFF)
            cumulativeIndexLU := and(shr(16, data), 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF) // @audit
            quotaIncreaseFee := shr(208, data)
        }

```
Here actually we fetching values for `Quota`
so `quotaCumulativeIndexLU` could used instead of `cumulativeIndexLU`

cause `cumulativeIndexLU` used multiple times in code base for Normal BaseInterst calculation.

So it create much confusion in during code audits

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/quotas/PoolQuotaKeeperV3.sol#L268

### [Low-] Value for `quotaIncreaseFee` variable during low level memory fetch not correct as there are more 3 more parameters present after `fee`

Low level code is as follows 
```solidity
        assembly {
            let data := sload(tokenQuotaParams.slot)
            rate := and(data, 0xFFFF)
            cumulativeIndexLU := and(shr(16, data), 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF)
            quotaIncreaseFee := shr(208, data)
        }
```
here parameters or variable extracted from a `tokenQuotaParams` struct
and `tokenQuotaParams` is consist of 
```solidity
struct TokenQuotaParams {
    uint16 rate;
    uint192 cumulativeIndexLU;
    uint16 quotaIncreaseFee;
    uint96 totalQuoted;
    uint96 limit;
}
```
So technically here fetched `quotaIncreaseFee` value not correct.

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/quotas/PoolQuotaKeeperV3.sol#L269

### [Low-] Could lead to dosed, only addition no removal in quotaTokenSet
In `PoolQuotaKeeperV3.sol` there is only method for addtion of `QuoteTokens` to contract but no removal method present.

`addQuotaToken()` helps to add `QuotaToken` to `quotaTokensSet`

`updateRates()` will help to calculate `quotaRevenue` but iteriting over these whole tokens, so other calculation also present there

So If `quotaTokensSet` increase significantly in size then It may lead to a dos situation

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/quotas/PoolQuotaKeeperV3.sol#L183-L216

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/quotas/PoolQuotaKeeperV3.sol#L161-L177

### Mitigation
Should limit no. of Quota Token addition to `QuotaTokenSet`

Or

Preform looping in segment




### [Low-] Little concern about _lockedUsdValue() of eligibleDataProvider
### [Low-] Is here token transfered to proxy or collateralizer check
Confused
### [Low-] no fee considered in case of `onCreditFlashloan()`

### [Low-] chainlinkAgrregator.lastestRoundData() related check refer PendleOracle.sol's L120







### [Low-] this hs no usecase, as it updated in _updateInterestRate() function
PoolV3.updateQuotaRevenue

### [Low-] liquidatePosition is subject to Front-runn attack

### [Low-] liquidatePosition - should here takeCollateral calculated after - penalty 


### [Low-]  In pendel case no deadline checks appear
In `SwapAction.swap()` preform swap via 3 protocol depending upon which `swapProtocol` type passed to the function
- Balncer (balancerSwap)
- UniswapV3 (UniswapV3)
- Pendle (pendelJoin, pendelExit)

unlike other 2 `(balancerSwap & uniV3Swap)`, PndleIn/pendleOut missing checks for Tx deadline.

So if Tx ramain in mempool for significant amount of time then OutCome result may be quite different user expection.

```solidity
...
...
        } else if (swapParams.swapProtocol == SwapProtocol.PENDLE_IN) {
            retAmount = pendleJoin(swapParams.recipient, swapParams.limit, swapParams.args);
        } else if (swapParams.swapProtocol == SwapProtocol.PENDLE_OUT) {
            retAmount = pendleExit(swapParams.recipient, swapParams.amount, swapParams.args);
        } else revert SwapAction__swap_notSupported();
        // Transfer any remaining tokens to the recipient
        if (swapParams.swapType == SwapType.EXACT_OUT && swapParams.recipient != address(this)) {
            IERC20(swapParams.assetIn).safeTransfer(swapParams.recipient, swapParams.limit - retAmount);
        }
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/SwapAction.sol#L134-L142

#### Mitigation 
Incorporate Tx deadline check in case of Pendle case in own (swapAction.sol) level



### [Low-] Registered vault check not present here
increaseLever()





### [Low-] transferFrom() used flashlender.flashLoan()

Instead use `safeTransferFrom()`

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/Flashlender.sol#L133


### [Low-] In `BalancerOracle` before making call to Chainlink ensure that token is != 0

```solidity
    function _getTokenPrice(uint256 index) internal view returns (uint256 price) {
        address token;
        if (index == 0) token = token0;
        else if (index == 1) token = token1;
        else if (index == 2) token = token2; // @audit 
        else revert BalancerOracle__getTokenPrice_invalidIndex();

        return chainlinkOracle.spot(token);
    }
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/oracle/BalancerOracle.sol#L165-L172

Here we see `index` used and depending on `index`, `token` get decided

Here `token0`, `token1` and `token2` are Balancer Pool Token which are set in constructor as follows
```solidity
    ) initializer {
        balancerVault = IVault(balancerVault_);
        updateWaitWindow = updateWaitWindow_;
        stalePeriod = stalePeriod_;
        chainlinkOracle = IOracle(chainlinkOracle_);
        pool = pool_;
        poolId = IWeightedPool(pool).getPoolId();

        (address[] memory tokens, , ) = balancerVault.getPoolTokens(poolId);

        // store the tokens
        uint256 len = tokens.length;
        token0 = (len > 0) ? tokens[0] : address(0);
        token1 = (len > 1) ? tokens[1] : address(0);
        token2 = (len > 2) ? tokens[2] : address(0);
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/oracle/BalancerOracle.sol#L85-L89

So its possible that depending on Pool type on balancer some indexed `tokens` could be 0address

So Before making Chainlink call ensure that entered token is not address(0)


### [Low-] `setOracle()` don't have any duplication checks
While setting Aggregator for different token via `setOracle()` there should be duplication check which ensure that no cases of overriding or duplicate settings

```solidity
    function setOracles(address[] calldata _tokens, Oracle[] calldata _oracles) external onlyRole(DEFAULT_ADMIN_ROLE) { // @audit L:: duplication check
        for (uint256 i = 0; i < _tokens.length; i++) {
            oracles[_tokens[i]] = _oracles[i];
        }
    }
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/oracle/ChainlinkOracle.sol#L45-L48

### [Low-] Draft version used
`IERC20Permit` imported in `TransferAction.sol` contract is a Draft version
```solidity
import {IERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/draft-IERC20Permit.sol";
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/TransferAction.sol#L5

#### mitigation 
Try to avoid use of Draft versions



### [Low-] Before deposit should check vault existance check 
There is modifier `onlyRegisteredVault(vault)` which ensure that the vault already registered in `vaultRegistry contract`

when a callback came `_onDeposit()` should ensure that Vault exists

So I think `_onDeposit()` should have `onlyRegisteredVault(vault)` incoporate with it

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PositionAction20.sol#L39

#### Mitigation

```diff
-   function _onDeposit(address vault, address position, address /*src*/, uint256 amount) internal override returns (uint256) {

+   function _onDeposit(address vault, address position, address /*src*/, uint256 amount) internal onlyRegisteredVault(vault) override returns (uint256) {

```


### [Low-] Considering `NatSpec` Comments, Corresponding checks absent in function
In `PoolAction.transferAndJoin()` there is a Comment that says 
```
/// @notice Execute a transfer from an EOA and then join via `PoolActionParams`
```
So I believe Here transfer occurs from a EOA, But corresponding ceck for it, like is here form address is an EOA or not not present in that function code.

So here code is incompatible with code-comment.

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PoolAction.sol#L70

#### Mitigation
Implement Corresponding checks,



### [Low-] `pendleRouter.addLiquiditySingleToken()` called with a msg.value, but its parent function `transferAndJoin()` is non-payable in nature
- In `PoolAction.sol` contract it has a function `transferAndJoin()`
which internally call `join()`
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PoolAction.sol#109
- `join()` is a public payable function
- `join()` internally calls `_pendleJoin()` when some condition met
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PoolAction.sol#L118
- `_pendleJoin()` further call to `addliquiditySingleToken()` of `pendleRouter` as follows
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PoolAction.sol#L172
```solidity
    function _pendleJoin(PoolActionParams memory poolActionParams) internal {
...
...

        pendleRouter.addLiquiditySingleToken{value: msg.value}(poolActionParams.recipient, market, poolActionParams.minOut, guessPtReceivedFromSy, input, limit); // @audit 
    }
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PoolAction.sol#L159-L173

Here we see that a `msg.value` parameter passed to call,

But Point is when Tx initiated from `poolAction.transferAndJoin()` as it is not `payable` in nature tx will revert here

#### Mitigation
Make `poolAction.transferAndJoin()` `payable`


### [Low-] Irregularities while setting `constants` variable

Here we can see that above 3 `%` setted value differ from below 2

Below 2 are set in BIPs, but not above one. This create Irregularity and confusion in calculation and could lead to some calculation mistakes.

Set all under a single unit measurement.

```solidity
    uint256 public constant QUART = 25000; //  25%
    uint256 public constant HALF = 65000; //  65%
    uint256 public constant WHOLE = 100000; // 100%

    uint256 public constant MAX_SLIPPAGE = 9000; //10% 
    uint256 public constant PERCENT_DIVISOR = 10000; //100%
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/reward/MultiFeeDistribution.sol#L37-L43

### [Low-] comment mismatched with code L42 - multiFeeDistribution.sol
Like below other `constants` store value in BIPs and their respective comments shows that Percentage representation of those BIPs value

But in case of `MAX_SLIPPAGE` its 9000 which will be 90%, But Comment says 10%

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/reward/MultiFeeDistribution.sol#L42-L43

```solidity

    // Maximum slippage allowed to be set by users (used for compounding).
    uint256 public constant MAX_SLIPPAGE = 9000; //10% // @audit L: wrong comment
    uint256 public constant PERCENT_DIVISOR = 10000; //100%
```

### [Low-] No checks for staleness in AuraVault.sol

In the `Aura Vault` contract, the protocol uses a ChainLink aggregator to fetch the `latestRoundData()`, but there is no check if the return value indicates stale data.
The only check present is for the quoteAnswer to be > 0; however, this alone is not sufficient.

```solidity
    function _chainlinkSpot() private view returns (uint256 price) {
        bool isValid;
        try AggregatorV3Interface(BAL_CHAINLINK_FEED).latestRoundData() returns (
            uint80 /*roundId*/,
            int256 answer,
            uint256 /*startedAt*/,
            uint256 /*updatedAt*/,
            uint80 /*answeredInRound*/
        ) {
            price = wdiv(uint256(answer), BAL_CHAINLINK_DECIMALS); // @audit no checks for staleness
            isValid = (price > 0);
        } catch {}

        if (!isValid) revert AuraVault__chainlinkSpot_invalidPrice();
    }
```

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/vendor/AuraVault.sol#L367-L376

### Mitigation
```diff

        try AggregatorV3Interface(BAL_CHAINLINK_FEED).latestRoundData() returns (
-           uint80 /*roundId*/,
-           int256 answer,
-           uint256 /*startedAt*/,
-           uint256 /*updatedAt*/,
-           uint80 /*answeredInRound*/

+           uint80 roundId,
+           int256 answer,
+           uint256 startedAt,
+           uint256 updatedAt,
+           uint80 answeredInRound
        ) {
            price = wdiv(uint256(answer), BAL_CHAINLINK_DECIMALS);
            isValid = (price > 0);
+           require(answeredInRound >= roundId, "Stale price!");
+           require(updatedAt != 0, "Round not complete!");
+           require(block.timestamp - updatedAt <= VALID_TIME_PERIOD);
```

### [Low-] No sanity checks for price and others like `Round completion`, `Stale Price` and `valide time period`

```solidity
    function _getAuraSpot() internal view returns (uint256 price) {
        uint256 ethPrice;
        (, int256 answer, , , ) = AggregatorV3Interface(ETH_CHAINLINK_FEED).latestRoundData();
        ethPrice = wdiv(uint256(answer), ETH_CHAINLINK_DECIMALS); // @audit no checks for answer
...
...
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/vendor/AuraVault.sol#L383

### Mitigation 
Should have proper checks like below

```diff

-       (, int256 answer, , , ) = AggregatorV3Interface(ETH_CHAINLINK_FEED).latestRoundData();

+       (uint80 roundId, int256 answer, uint256 startedAt , uint256 updatedAt , uint80 answeredInRound) = AggregatorV3Interface(ETH_CHAINLINK_FEED).latestRoundData();
+           require(answer > 0);
+           require(answeredInRound >= roundId, "Stale price!");
+           require(updatedAt != 0, "Round not complete!");
+           require(block.timestamp - updatedAt <= VALID_TIME_PERIOD);
```

### [Low-] No validation check parameters in `constructor` of `CDPVault.sol` 
There should proper validation checks for `addresses` and `numarical values` while assigning values to state variables inside constructor

Its a very crusial step, if any wrong value stored it may cause
- a several Tx to set those correctly, if possible
- Contract deployment againg
- Could cause some sorts of exploaitaion in future

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/CDPVault.sol#L164-L185





### [Low-] takeCollateral should considered after - penalty






### [Low-] made prior checks before storing `newPoolQuotaKeeper` to storage that new value is not same as previous stored value

```solidity
    function setPoolQuotaKeeper(
        address newPoolQuotaKeeper
...
...
        if (IPoolQuotaKeeperV3(newPoolQuotaKeeper).pool() != address(this)) {
            revert IncompatiblePoolQuotaKeeperException(); // U:[LP-23C]
        }

        poolQuotaKeeper = newPoolQuotaKeeper;
```

### [Low-] before transfer check for 0 amount

before calling `silo.withdraw()` there should be a check which make sure that withdrawal amount is not 0.

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/StakingLPEth.sol#L96

```solidity
    function unstake(address receiver) external {
        UserCooldown storage userCooldown = cooldowns[msg.sender];
        uint256 assets = userCooldown.underlyingAmount;

        if (block.timestamp >= userCooldown.cooldownEnd || cooldownDuration == 0) {
            userCooldown.cooldownEnd = 0;
            userCooldown.underlyingAmount = 0;

            silo.withdraw(receiver, assets); // @audit L:: befoe trnaser check for zero amount check
        } else {
            revert InvalidCooldown();
        }
    }
```

### [Low-] no need for +
`calcDecrease()`

### [Low-]  Check if it is ever increasing array or no
registeredTokens - rewardController - _updateRegisteredBalance()

### [Low-]Replay attack possible should include chainId L16 TransferAction.sol

### [Low-] NC / G : only add where debt != 0 vaultRegistry.getUserTotalDebt()