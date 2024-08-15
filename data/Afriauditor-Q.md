
[L-1]
TITLE: No Way to Remove Credit Manager from Set

IMPACT: Inability to remove a credit manager from the set can lead to outdated or invalid credit managers remaining in the system, potentially causing operational issues or security risks.

VULNERABILITY DETAIL: The current implementation of the PoolV3 contract does not provide a mechanism to remove a credit manager from the _creditManagerSet. Once a credit manager is added using the setCreditManagerDebtLimit function, it remains in the set indefinitely. This limitation can result in the persistence of obsolete or compromised credit managers, which could negatively impact the system's integrity and performance.

RECOMMENDATION: Introduce a function to remove a credit manager from the _creditManagerSet. Ensure this function can only be called by authorized roles, such as the controller.


[L-2]
TITLE: Missing Event Emission After Transfer to Treasury

IMPACT:Lack of transparency and traceability for transfers to the treasury, making it difficult to audit and track these transactions.

VULNERABILITY DETAIL: In the _withdraw function, after transferring the remaining assets to the treasury using IERC20(underlyingToken).safeTransfer({to: treasury, value: assetsSent - amountToUser});, no event is emitted to log this transfer. This omission can lead to a lack of visibility into important fund movements, potentially obscuring critical financial operations and making auditing more challenging.

RECOMMENDATION:Add an event emission after the transfer to the treasury to ensure all significant transactions are logged. 


[L-3]
TITLE: whenNotLocked Modifier Should Allow controllerOnly Access

IMPACT:Controllers cannot bypass the whenNotLocked restriction, potentially hindering critical operations during locked states.

VULNERABILITY DETAIL: The whenNotLocked modifier currently only allows addresses in the _allowed mapping to bypass the locked state. Controllers, who should have overarching control, are not included in this exception. This oversight can prevent controllers from performing necessary actions when the pool is locked.

RECOMMENDATION:Modify the whenNotLocked modifier to include a check for controllerOnly access. Update the modifier as follows:
```
modifier whenNotLocked() {
    if (_allowed[msg.sender] || isController(msg.sender)) {
        _;
    } else {
        _revertIfLocked();
        _;
    }
}
```
This ensures that controllers can bypass the locked state and perform essential operations.

[L-4] 
TITLE: Changing CooldownDuration Period Won't Affect Existing Cooldowns

IMPACT: This vulnerability leads to inconsistencies between the current cooldown duration and the cooldown duration applied to users after a change. It potentially allows some users to bypass the intended cooldown period or forces others to wait longer than necessary. Users who initiated cooldowns before a period increase can bypass the new, longer cooldown, while users who initiated cooldowns before a period decrease are forced to wait longer than necessary. This issue can also be exploited through front-running, where users anticipate changes to the cooldown period and act to gain an advantage. This is particularly important as the cooldown duration can range anywhere from 1 day to 30 days, making it crucial to ensure that each user's cooldown period is accurately applied.

VULNERABILITY DETAIL: The issue occurs when a new cooldown duration is set via the function setCooldownDuration. This function only updates the cooldownDuration state variable. However, existing cooldowns are stored in the cooldowns mapping. The cooldownEnd for each user is set when they initiate a cooldown. Once a new cooldown duration is set, it is not immediately enforced because cooldownEnd is unaffected by the current cooldownDuration. As a result, if the cooldown duration is increased, users who have already started their cooldown will be able to unstake earlier than intended. Conversely, if the cooldown duration is decreased, users who have already started their cooldown will have to wait longer than the new intended period. Users with knowledge of an impending cooldown period change can initiate cooldowns just before the change, allowing them to lock in a shorter cooldown period before it is increased. They can also front-run the transaction to change the duration in the mempool.

RECOMMENDATION: Modify the UserCooldown struct to store the duration and start time instead of the end time. Update the unstake function to calculate the end time based on the stored duration and start time.
 
LINK- https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L109

[L-5]:
TITLE: Cooldown Period Extension on Subsequent Staking

IMPACT:When users attempt to cool down additional shares while an existing cooldown is in progress, the entire cooldown period is reset for all shares, including those already in the cooldown process. This can lead to extended lock-up periods for users, potentially beyond their expectations.

VULNERABILITY DETAIL:
The vulnerability lies in the cooldownShares function:
```
function cooldownShares(uint256 shares) external ensureCooldownOn returns (uint256 assets) {
    if (shares > maxRedeem(msg.sender)) revert ExcessiveRedeemAmount();

    assets = previewRedeem(shares);

    cooldowns[msg.sender].cooldownEnd = uint104(block.timestamp) + cooldownDuration;
    cooldowns[msg.sender].underlyingAmount += uint152(assets);

    _withdraw(msg.sender, address(silo), msg.sender, assets, shares);
}

```
Consider the following scenario:

The cooldown duration is set to 30 days.
User A calls cooldownShares with 100 shares on Day 1.
On Day 20, User A decides to cool down an additional 50 shares.
Upon calling cooldownShares again, the cooldownEnd is reset to 30 days from Day 20.
User A now has to wait until Day 50 to unstake any shares, including the initial 100 shares that have already been cooling for 20 days.

RECOMMENDATION:
To address this issue, consider implementing the following changes:

Modify the UserCooldown struct to track multiple cooldown entries:
```
struct CooldownEntry {
    uint104 cooldownEnd;
    uint256 assets;
}

struct UserCooldown {
    CooldownEntry[] entries;
    uint256 totalAssets;
}

```
LINK-https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L109

[L-6]
TITLE:Unfair Loss Distribution and Withdrawal Blocking in Emergency Scenarios due to Minimum Shares Requirement
IMPACT: The current implementation of the StakingLPEth contract includes a minimum shares requirement that, while intended to prevent donation attacks, introduces a vulnerability in emergency withdrawal scenarios. This issue can lead to:

Unfair loss distribution among users, with the last withdrawers bearing the brunt of the loss.
Blocking of full withdrawals for the last users.

VULNERABILITY DETAIL:The vulnerability stems from the _checkMinShares() function and its usage in withdrawal operations:
```
uint256 private constant MIN_SHARES = 0.01 ether;

function _checkMinShares() internal view {
    uint256 _totalSupply = totalSupply();
    if (_totalSupply > 0 && _totalSupply < MIN_SHARES) revert MinSharesViolation();
}

function _withdraw(
    address caller,
    address receiver,
    address _owner,
    uint256 assets,
    uint256 shares
) internal override nonReentrant {
    super._withdraw(caller, receiver, _owner, assets, shares);
    _checkMinShares();
}

```
Consider the following scenario:

The contract has a total of 100 ether worth of shares distributed among 100 users, each with 1 ether worth of shares.
An emergency situation occurs, and users start withdrawing their funds.
The first 99 users successfully withdraw their full 1 ether worth of shares each.
The last user attempts to withdraw their 1 ether worth of shares.
This withdrawal would bring the total supply below MIN_SHARES (0.01 ether), causing the transaction to revert due to MinSharesViolation.
The last user is forced to leave at least 0.01 ether worth of shares in the contract, bearing the entire loss alone.

RECOMMENDATION: Mint MIN_SHARES to the zero address during contract deployment:
```
constructor(
    address _liquidityPool,
    string memory _name,
    string memory _symbol
) ERC4626(IERC20(_liquidityPool)) ERC20(_name, _symbol) {
    silo = new Silo(address(this), _liquidityPool);
    cooldownDuration = MAX_COOLDOWN_DURATION;
    
    // Mint MIN_SHARES to zero address
    _mint(address(0), MIN_SHARES);
}

```
Remove the _checkMinShares() function entirely.
Remove all calls to _checkMinShares() from the _deposit and _withdraw functions.

LINK:https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L174

[L-7]
TITLE: Incorrect Supply Rate Calculation in CDPVault

IMPACT:The supplyRate() function calculates the interest rate for depositors using expectedLiquidity(), which includes both the principal and the accrued interest. This results in an inaccurate supply rate. The correct calculation should use the sum of availableLiquidity() and _totalDebt.borrowed to accurately reflect the total amount of capital deposited (principal only).

VULNERABILITY DETAIL: The current implementation of the supplyRate() function is as follows:
```
function supplyRate() external view override returns (uint256) {
    uint256 assets = expectedLiquidity();
    uint256 baseInterestRate_ = baseInterestRate();
    if (assets == 0) return baseInterestRate_;
    return
        ((baseInterestRate_ * _totalDebt.borrowed) * (PERCENTAGE_FACTOR - withdrawFee)) /
        PERCENTAGE_FACTOR /
        assets;
}

```
where _totalDebt.borrowed is the total deposited amount borrowed out and availableLiquidity() is the amount deposited by users left in the contract (excluding interest).

To demonstrate:

Assume availableLiquidity = 1000 ETH
_totalDebt.borrowed = 500 ETH
Accrued interest = 50 ETH
Current calculation: assets = 1550 ETH (includes interest)
Correct calculation: assets = 1500 ETH (excludes interest)
This leads to an artificially DECREASED supply rate, as the denominator in the rate calculation is larger than it should be.

RECOMMENDATION:
Recommendation: Modify the supplyRate() function to use the correct calculation for assets:
```
function supplyRate() external view override returns (uint256) {
    uint256 assets = availableLiquidity() + _totalDebt.borrowed;
    uint256 baseInterestRate_ = baseInterestRate();
    if (assets == 0) return baseInterestRate_;
    return
        ((baseInterestRate_ * _totalDebt.borrowed) * (PERCENTAGE_FACTOR - withdrawFee)) /
        PERCENTAGE_FACTOR /
        assets;
}

```

LINK:https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L603

[L-8] 
TITLE:Incorrect Use of convertToShares Instead of Custom _convertToShares in `repayCreditAccount` Function

IMPACT: Using convertToShares from OpenZeppelin's ERC4626 contract instead of a custom _convertToShares function defined on the contract can lead to incorrect calculations of the number of shares to be minted or burned in the repayCreditAccount function. This can result in an inaccurate number of shares being minted or burned.

VULNERABILITY DETAIL:
 The PoolV3 contract overrides the totalAssets() function to include not just the current assets in the contract but also unrealized potential earnings (accrued interest and quota revenue):
```
function totalAssets() public view override(ERC4626, IERC4626) returns (uint256 assets) {
    return expectedLiquidity();
}

function expectedLiquidity() public view override returns (uint256) {
    return _expectedLiquidityLU + _calcBaseInterestAccrued() + _calcQuotaRevenueAccrued();
}

```
This leads to an incorrect calculation because:

totalAssets() includes unrealized interest and revenue.
The total supply of shares doesn't reflect these unrealized gains.
Only realized interest has been minted as shares, not unrealized interest.
Dividing new assets by the inflated totalAssets() value results in fewer shares being minted than should be.

RECOMMENDATION: To ensure accurate calculations of the number of shares to be minted or burned, replace the usage of convertToShares with the custom _convertToShares function in the repayCreditAccount function. This will ensure that the calculations are based on the correct asset-to-share conversion logic, excluding unrealized interest.

LINK:https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L549

[L-9]
TITLE:Missing Check for borrowedAmount Exceeding availableToBorrow in lendCreditAccount Function

IMPACT:he lendCreditAccount function lacks a crucial check against the availableToBorrow limit defined by the interest rate model, potentially allowing the contract to lend out more than what is considered safe, leading to excessive borrowing beyond permissible limits.

VULNERABILITY DETAIL: The lendCreditAccount function does not verify if the borrowedAmount surpasses the availableToBorrow threshold from the interestratemodel, unlike creditManagerBorrowable, which includes this essential safety measure.

```
function lendCreditAccount(uint256 borrowedAmount, address creditAccount) external override {
    // Checks are present but missing verification against `availableToBorrow` from the interest rate model.
}

function creditManagerBorrowable(address creditManager) external view override returns (uint256 borrowable) {
    // Includes a check against `availableToBorrow`, indicating its importance.

```
RECOMMENDATION: Incorporate availableToBorrow validation in lendCreditAccount to prevent unsafe lending practices.

LINK:https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L484

[L-10]
TITLE:Unauthorized Access to Deposit and Withdraw Functions When Contract is Paused

IMPACT:The deposit and withdraw functions are designed to be restricted during a paused state to prevent alterations to collateral and debt balances, safeguarding against unintended changes. However, the modifyCollateralAndDebt function, which underpins these operations, lacks the necessary whenNotPaused modifier, allowing direct modifications even when the contract is paused, undermining pause effectiveness.

VULNERABILITY DETAIL: Despite deposit and withdraw functions being correctly restricted during pauses via the whenNotPaused modifier, modifyCollateralAndDebt, accessible publicly, permits bypassing these restrictions, enabling unauthorized transactions.

RECOMMENDATION: Incorporate whenNotPaused in modifyCollateralAndDebt to align with security protocols.

LINK:https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L367

[L-11]
TITLE:Suboptimal Borrowing Conditions not checked in `setInterestRateModel` Function. 

IMPACT:The setInterestRateModel function fails to ensure optimal borrowing conditions upon updating the interest rate model, potentially leading to suboptimal borrowing states due to not verifying against the new model's criteria, risking inefficiencies and misalignments in borrowing strategies.

VULNERABILITY DETAIL: The setInterestRateModel function updates the interest rate model without assessing whether the current borrowing aligns with the new model's optimal borrowing parameters, as it incorrectly sets the checkOptimalBorrowing flag to false in the _updateBaseInterest call, omitting a critical check for optimal borrowing conditions.
```
function setInterestRateModel(
    address newInterestRateModel
)
    external
    override
    configuratorOnly
    nonZeroAddress(newInterestRateModel)
{
    interestRateModel = newInterestRateModel;
    _updateBaseInterest(0, 0, false); // Incorrectly sets checkOptimalBorrowing to false.
}

```
This oversight bypasses essential validation against the new model's optimal borrowing logic, potentially leading to suboptimal borrowing states post-update.

RECOMMENDATION:Adjust the _updateBaseInterest call within setInterestRateModel to enable optimal borrowing checks:
```
function setInterestRateModel(
    address newInterestRateModel
)
    external
    override
    configuratorOnly
    nonZeroAddress(newInterestRateModel)
{
    interestRateModel = newInterestRateModel;
    _updateBaseInterest(0, 0, true); // Correctly enables optimal borrowing assessment.
}

```

LINK:https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L755

[L-12]
TITLE:Incorrect Borrowable Amount Calculation in edge case.

IMPACT:The creditManagerBorrowable function calculates borrowable amounts without comparing against the actual available liquidity (availableLiquidity()), potentially suggesting borrowers can exceed the pool's real liquidity, leading to discrepancies between theoretical borrowable amounts and practical availability.

VULNERABILITY DETAIL: The function computes borrowable amounts using various parameters but neglects to validate these figures against the pool's actual available liquidity, risking overestimation of borrowable funds.
```
function creditManagerBorrowable(address creditManager) external view override returns (uint256 borrowable) {
    // Calculations without considering actual available liquidity.
}

```
this method might suggest higher borrowable amounts than physically present, misleading borrowers about available funds.

RECOMMENDATION: Incorporate an availableLiquidity() check to ensure borrowable amounts don't surpass actual liquidity:
```
function creditManagerBorrowable(address creditManager) external view override returns (uint256 borrowable) {
    // Existing calculations...
    borrowable = Math.min(borrowable, availableLiquidity()); // Ensures borrowable doesn't exceed real liquidity.
}

```

LINK:https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L466

