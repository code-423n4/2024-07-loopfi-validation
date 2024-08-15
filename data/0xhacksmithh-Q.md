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


### [Low-] no need for +
`calcDecrease()`

### [Low-]  Check if it is ever increasing array or no
registeredTokens - rewardController - _updateRegisteredBalance()

### [Low-] Little concern about _lockedUsdValue() of eligibleDataProvider

### [Low-] NC / G : only add where debt != 0 vaultRegistry.getUserTotalDebt()

### [Low-] this hs no usecase, as it updated in _updateInterestRate() function
PoolV3.updateQuotaRevenue

### [Low-] liquidatePosition is subject to Front-runn attack

### [Low-] liquidatePosition - should here takeCollateral calculated after - penalty 

### [Low-]  In pendel case no deadline checks appear
SwapAction.swap()

### [Low-] Is here token transfered to proxy or collateralizer check
Confused

### [Low-] Registered vault check not present here
increaseLever()

### [Low-] transferFrom() used flashlender.flashLoan()

### [Low-] no fee considered in case of `onCreditFlashloan()`

### [Low-] before chainlink call ensure that token is != 0
BalancerOracle - L172

### [Low-] `setOracle()` don't have any duplication checks
ChainlinkOracle - L45

### [Low-] chainlinkAgrregator.lastestRoundData() related check refer PendleOracle.sol's L120

### [Low-] Balancer incompatible with chainlink oracle

### [Low-] import {IERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/draft-IERC20Permit.sol";
TransferAction- L5

### [Low-]Replay attack possible should include chainId L16 TransferAction.sol

### [Low-] before deposit should check vault existance check
L40, PositionAction20

### [Low-] consider is there any case of slipage possible L161 PositionAction

### [Low-] Execute a transfer from an EOA and then join via `PoolActionParams` // @audit L:: is it necessary to check caller is EOA or not
L70 - PoolAction.sol

### [Low-] does this expect any eth, cause parent function `transferAndJoin()` is non-payable
L172 - poolAction.sol

### [Low-] comment mismatched with code L42 - multiFeeDistribution.sol

### [Low-] No checks for staleness L374 - AuraVault.sol

### [Low-] No check for prie L384 - Auravault

### [Low-] No validation check constructor - CDPVault

### [Low-] takeCollateral should considered after - penalty

### [Low-]made prior checks before storing L773

### [Low-] before transfer check for 0 address

### [Low-]

### [Low-]

### [Low-]

### [Low-]

### [Low-]

### [Low-]

### [Low-]

### [Low-]

### [Low-]

### [Low-]

### [Low-]

### [Low-]

### [Low-]