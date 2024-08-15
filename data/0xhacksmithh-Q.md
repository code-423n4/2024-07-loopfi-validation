### [Low-1] When Protocol is in `paused` mode `Borrower` unable to repay their `Loans`

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



### [Low-2] Value for `quotaIncreaseFee` variable during low level memory fetch not correct as there are more 3 more parameters present after `fee`

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

### [Low-3] Could lead to dosed, only addition no removal in quotaTokenSet
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



### [Low-4] liquidatePosition is subject to Front-runn attack
`liquidatePosition()` has a following require check

```solidity
if (takeCollateral > position.collateral) revert CDPVault__tooHighRepayAmount();
```
here Position.collateral is ovious collatera hold by positio
where `takeCollateral` is based on liquidater's entered `repayAmount`
```solidity
        uint256 takeCollateral = wdiv(repayAmount, discountedPrice);
```

- Let consider a situation when a Position is liquidatable
- A liquidator try to liqudate whole Position
- So he entered `repayAmount`, when `takeCollateral` calculated based on it(repayAmount), result will be equal or near equal to position.collateral
```solidity
uint256 takeCollateral = wdiv(repayAmount, discountedPrice);
```
- Position holder or anyone see this Tx, And front-run liquidator's Tx and he partially(slightly) liquidate this position.
- Now when Liquidators Tx comes into existance it will failed due to check
```solidity
if (takeCollateral > position.collateral) revert CDPVault__tooHighRepayAmount();
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/CDPVault.sol#L529-L532

#### Mitigation
Even if any frontrun appear, and position.collateral get reduced, allow liquidator to liquidate position

A similar approch can taken that present in `liquiateBadDebt()`

`repayamount` and `takecollateral` automatically adjusted according to `position.collateral`, if `takeCollateral > position.collateral` instead of reverting
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/CDPVault.sol#L601-L606





### [Low-5] Shouldn't here takeCollateral calculated after minusing penalty from repayAmount

According to my understanding `During the liquidation process in the Loop protocol, penalties are applied to the position being liquidated to mitigate against profitable self-liquidations.`

Here's how the penalty is calculated and applied:
- specific percentage deducted from the repay amount during liquidation
-  The penalty is calculated as a percentage of the amount the liquidator wants to repay. The specific percentage is defined in the LiquidationConfig of the CDPVault smart contract.

while deep diving i see that
- `takeCollateral`, amount of collateral that will transfered to a Liquidator is calculated based on `repayAmount`
- after that deltaDebt
- after that penalty calculated

```solidity
        uint256 takeCollateral = wdiv(repayAmount, discountedPrice);
        uint256 deltaDebt = wmul(repayAmount, liqConfig_.liquidationPenalty); 
        uint256 penalty = wmul(repayAmount, WAD - liqConfig_.liquidationPenalty);
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/CDPVault.sol#L529-L531

- So basically Token In here

```solidity
poolUnderlying.safeTransferFrom(msg.sender, address(pool), repayAmount - penalty)
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/CDPVault.sol#L539

```solidity
poolUnderlying.safeTransferFrom(msg.sender, address(pool), penalty);
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/CDPVault.sol#L568

Token Out here

```solidity
token.safeTransfer(msg.sender, takeCollateral);
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/CDPVault.sol#L565

According to my understanding here penlty has no effect on `takeCollateral` calculation. As it calculated after takeCollateral calculation and token send to contract in 2 step.

Liquidator getting `collateral` according to his entered repayAmount, so no penalty effect on `colateral` he receiving


#### Mitigation
I think `takeCollateral` should calculated after minusing penalty from repay amount like follow

```solidity
+       uint256 penalty = wmul(repayAmount, WAD - liqConfig_.liquidationPenalty);
+       uint256 takeCollateral = wdiv(repayAmount - penalty, discountedPrice); 
```


### [Low-6]  In pendel case no deadline checks appear
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






### [Low-7] transferFrom() used flashlender.flashLoan()

Instead use `safeTransferFrom()`

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/Flashlender.sol#L133


### [Low-8] In `BalancerOracle` before making call to Chainlink ensure that token is != 0

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


### [Low-9] `setOracle()` don't have any duplication checks
While setting Aggregator for different token via `setOracle()` there should be duplication check which ensure that no cases of overriding or duplicate settings

```solidity
    function setOracles(address[] calldata _tokens, Oracle[] calldata _oracles) external onlyRole(DEFAULT_ADMIN_ROLE) { // @audit L:: duplication check
        for (uint256 i = 0; i < _tokens.length; i++) {
            oracles[_tokens[i]] = _oracles[i];
        }
    }
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/oracle/ChainlinkOracle.sol#L45-L48

### [Low-10] Draft version used
`IERC20Permit` imported in `TransferAction.sol` contract is a Draft version
```solidity
import {IERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/draft-IERC20Permit.sol";
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/TransferAction.sol#L5

#### mitigation 
Try to avoid use of Draft versions



### [Low-11] Before deposit should check vault existance check 
There is modifier `onlyRegisteredVault(vault)` which ensure that the vault already registered in `vaultRegistry contract`

when a callback came `_onDeposit()` should ensure that Vault exists

So I think `_onDeposit()` should have `onlyRegisteredVault(vault)` incoporate with it

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PositionAction20.sol#L39

#### Mitigation

```diff
-   function _onDeposit(address vault, address position, address /*src*/, uint256 amount) internal override returns (uint256) {

+   function _onDeposit(address vault, address position, address /*src*/, uint256 amount) internal onlyRegisteredVault(vault) override returns (uint256) {

```


### [Low-12] Considering `NatSpec` Comments, Corresponding checks absent in function
In `PoolAction.transferAndJoin()` there is a Comment that says 
```
/// @notice Execute a transfer from an EOA and then join via `PoolActionParams`
```
So I believe Here transfer occurs from a EOA, But corresponding ceck for it, like is here form address is an EOA or not not present in that function code.

So here code is incompatible with code-comment.

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PoolAction.sol#L70

#### Mitigation
Implement Corresponding checks,



### [Low-13] `pendleRouter.addLiquiditySingleToken()` called with a msg.value, but its parent function `transferAndJoin()` is non-payable in nature
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


### [Low-14] Irregularities while setting `constants` variable

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

### [Low-15] comment mismatched with code L42 - multiFeeDistribution.sol
Like below other `constants` store value in BIPs and their respective comments shows that Percentage representation of those BIPs value

But in case of `MAX_SLIPPAGE` its 9000 which will be 90%, But Comment says 10%

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/reward/MultiFeeDistribution.sol#L42-L43

```solidity

    // Maximum slippage allowed to be set by users (used for compounding).
    uint256 public constant MAX_SLIPPAGE = 9000; //10% // @audit L: wrong comment
    uint256 public constant PERCENT_DIVISOR = 10000; //100%
```

### [Low-16] No checks for staleness in AuraVault.sol

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

### [Low-17] No sanity checks for price and others like `Round completion`, `Stale Price` and `valide time period`

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

### [Low-18] No validation check parameters in `constructor` of `CDPVault.sol` 
There should proper validation checks for `addresses` and `numarical values` while assigning values to state variables inside constructor

Its a very crusial step, if any wrong value stored it may cause
- a several Tx to set those correctly, if possible
- Contract deployment againg
- Could cause some sorts of exploaitaion in future

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/CDPVault.sol#L164-L185



### [Low-19] made prior checks before storing `newPoolQuotaKeeper` to storage that new value is not same as previous stored value

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

### [Low-20] before transfer check for 0 amount

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

### [Low-21] no fee considered in case of `onCreditFlashloan()`
unlike `onFlashLoan()` here amount and fee not taken into consideration

at the end of function approval given to flashlender as follows
```solidity
       underlyingToken.forceApprove(address(flashlender), subDebt); 
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PositionAction.sol#L447

where subDebt is 
```solidity
        uint256 subDebt = leverParams.primarySwap.amount;
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PositionAction.sol#L445

its not necessary that `subDebt` will equal to `amount + fee` from `creditFlashLoan()`

```solidity
    function creditFlashLoan(
...
    ) external override nonReentrant returns (bool) {
        uint256 fee = wmul(amount, protocolFee);
        uint256 total = amount + fee;
...
...

        if (receiver.onCreditFlashLoan(msg.sender, amount, fee, data) != CALLBACK_SUCCESS_CREDIT)
            revert Flash__creditFlashLoan_callbackFailed();

        underlyingToken.transferFrom(address(receiver), address(pool), total);
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PositionAction.sol#L123

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PositionAction.sol#L129

#### Mitigation
`onCreditFlashLoan()` also have checks like `onFlashLoan()` which ensure that approval amount to `flashlender` i.e subDebt == amount + fee.

### [Low-22] Check Prev value before chage state

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


### [Low-23] If `agent` goes malicious then he can set permission to any address who can Borrow max on behalf of Owner which subject to liquidation probability.

In this situation Owner positions will be at risk, as permited address can max amount possible against the Owner which may cause to liquidation

```solidity
 function setPermissionAgent(address agent, bool permitted) external {
        _permittedAgents[msg.sender][agent] = permitted;
        emit SetPermittedAgent(msg.sender, agent, permitted);
    }
```
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/utils/Permission.sol#L57-L60

### [Low-24]  variable naming could be improved cause it could easily confused with cumulativeIndex
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

