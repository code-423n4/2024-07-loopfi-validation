### [Low-] When Protocol is in `paused` mode `Borrower` unable to repay their `Loans`

`repay() - CDPVault.sol`, 

### [Low-] Check Prev value before chage state
`setPermissionAgent` , `modifyPermission` - Permission.sol

### [Low-] If `agent` goes malicious then he can set permission to any address who can Borrow max on behalf of Owner which subject to liquidation probability.

`setPermissionAgent`

### [Low-]  variable naming could be improved cause it could easily confused with cumulativeIndex
PoolKepperQuotaV3 - L268

### [Low-] `quotaIncreaseFee` variable not correct right as there are more 3 parameters present after this
PoolKeeperQuotaV3 - L269

### [Low-] Could lead to dosed, only addition no removal in quotaTokenSet
PoolKeeperQuotaV3 - L200, 161, 135

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