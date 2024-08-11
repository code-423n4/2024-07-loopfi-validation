## [L-01] Potential over-distribution of rewards in `claim` function

The `claim` function in the `AuraVault` contract may lead to over-distribution of rewards due to incorrect handling of incentives. The function calculates `amountIn` using `_previewReward(amounts[0], amounts[1], _config)`, which already considers the incentives. However, when distributing the rewards, the incentives are applied again, leading to potential over-distribution.

Relevant code:
```solidity

function claim(uint256[] memory amounts, uint256 maxAmountIn) external returns (uint256 amountIn) {
    // Claim rewards from Aura reward pool
    IPool(rewardPool).getReward();

    // Compute assets amount to be sent to the Vault
    VaultConfig memory _config = vaultConfig;
    amountIn = _previewReward(amounts[0], amounts[1], _config);

    // Transfer assets to Vault
    require(amountIn <= maxAmountIn, "!Slippage");
    IERC20(asset()).safeTransferFrom(msg.sender, address(this), amountIn);

    // Compound assets into "asset" balance
    IERC20(asset()).safeApprove(rewardPool, amountIn);
    IPool(rewardPool).deposit(amountIn, address(this));

    // Distribute BAL rewards
    IERC20(BAL).safeTransfer(_config.lockerRewards, (amounts[0] * _config.lockerIncentive) / INCENTIVE_BASIS);
    IERC20(BAL).safeTransfer(msg.sender, amounts[0]);

    // Distribute AURA rewards
    if (block.timestamp <= INFLATION_PROTECTION_TIME) {
        IERC20(AURA).safeTransfer(_config.lockerRewards, (amounts[1] * _config.lockerIncentive) / INCENTIVE_BASIS);
        IERC20(AURA).safeTransfer(msg.sender, amounts[1]);
    } else {
        // after INFLATION_PROTECTION_TIME
        IERC20(AURA).safeTransfer(_config.lockerRewards, IERC20(AURA).balanceOf(address(this)));
    }

    emit Claimed(msg.sender, amounts[0], amounts[1], amountIn);
}
```

### Recommendation:
Ensure that incentives are not double-counted during the reward distribution. Adjust the reward distribution logic to account for the incentives correctly.

```solidity
    // Distribute BAL rewards
    uint256 balClaimerIncentive = (amounts[0] * _config.claimerIncentive) / INCENTIVE_BASIS;
    uint256 balLockerIncentive = (amounts[0] * _config.lockerIncentive) / INCENTIVE_BASIS;
    IERC20(BAL).safeTransfer(_config.lockerRewards, balLockerIncentive);
    IERC20(BAL).safeTransfer(msg.sender, amounts[0] - balClaimerIncentive - balLockerIncentive);

    // Distribute AURA rewards
    uint256 auraClaimerIncentive = (amounts[1] * _config.claimerIncentive) / INCENTIVE_BASIS;
    uint256 auraLockerIncentive = (amounts[1] * _config.lockerIncentive) / INCENTIVE_BASIS;
    if (block.timestamp <= INFLATION_PROTECTION_TIME) {
        IERC20(AURA).safeTransfer(_config.lockerRewards, auraLockerIncentive);
        IERC20(AURA).safeTransfer(msg.sender, amounts[1] - auraClaimerIncentive - auraLockerIncentive);
    } else {
        // after INFLATION_PROTECTION_TIME
        IERC20(AURA).safeTransfer(_config.lockerRewards, IERC20(AURA).balanceOf(address(this)));
    }
```


## [L-02] Unchecked negative input in `calcActualQuotaChange()` may lead to unexpected quota increases

The `calcActualQuotaChange()` function in the QuotasLogic library assumes that the `requestedChange` parameter is non-negative, as indicated by the comment:

"The function is never called with `requestedChange < 0`, so casting it to `uint96` is safe"

However, this assumption is not enforced within the function. If a negative value is passed, the unchecked cast to `uint96` will result in a large positive number, potentially leading to an unexpected increase in quota instead of a decrease.

While the impact is mitigated by the `maxQuotaCapacity` check, it's still possible for the function to return a positive quota change when a decrease was intended.

### Recommendation:
Add a check at the beginning of the function to ensure `requestedChange` is non-negative:

```solidity
function calcActualQuotaChange(uint96 totalQuoted, uint96 limit, int96 requestedChange)
    internal
    pure
    returns (int96 quotaChange)
{
    if (requestedChange < 0) {
        return 0; // Or handle negative changes differently if needed
    }
    // Rest of the function remains unchanged
    ...
}
```

This ensures that the function behaves as expected even if the calling code violates the assumed precondition.




## [L-03] Unnecessary check prevents adding pool's underlying token as quota token

The `addQuotaToken()` function in the `GaugeV3` contract includes a condition that prevents the pool's underlying token from being added as a quota token:

```solidity
if (isTokenAdded(token) || token == IPoolV3(pool).underlyingToken()) {
    revert TokenNotAllowedException();
}
```

However, according to the protocol team, the pool's underlying token should be allowed as a quota token. This unnecessary check restricts the intended functionality of the contract.

### Recommendation:
Remove the `token == IPoolV3(pool).underlyingToken()` check from the condition, allowing the underlying token to be added as a quota token when needed:

```solidity
if (isTokenAdded(token)) {
    revert TokenNotAllowedException();
}
```



## [L-04] Unchecked array lengths in multisend function can lead to out-of-bounds errors

The `multisend()` function in the `PositionAction` contract does not validate that the lengths of the `targets`, `data`, and `delegateCall` arrays are equal before processing them. This oversight can potentially lead to out-of-bounds errors or unexpected behavior if the arrays have mismatched lengths.

The function iterates through the `targets` array length, assuming the other arrays have the same length:

```solidity
function multisend(
    address[] calldata targets,
    bytes[] calldata data,
    bool[] calldata delegateCall
) external onlyDelegatecall {
    uint256 totalTargets = targets.length;
    for (uint256 i; i < totalTargets; ) {
        // ... function body ...
    }
}
```

If `data` or `delegateCall` arrays are shorter than `targets`, the function will attempt to access non-existent array elements, potentially causing the transaction to revert or, worse, execute unintended operations.

### Recommendation:
To mitigate this issue, add a check at the beginning of the function to ensure all input arrays have the same length:

```solidity
function multisend(
    address[] calldata targets,
    bytes[] calldata data,
    bool[] calldata delegateCall
) external onlyDelegatecall {
    require(targets.length == data.length && targets.length == delegateCall.length, "Array lengths must match");
    uint256 totalTargets = targets.length;
    for (uint256 i; i < totalTargets; ) {
        // ... rest of the function ...
    }
}
```

This check will prevent potential out-of-bounds errors and ensure the function behaves as intended with properly matched input arrays.


## [L-05] Lack of validation in repayCreditAccount() may lead to inconsistent pool state

The `repayCreditAccount()` function in the `PoolV3` contract updates the pool's state based on `repaidAmount`, `profit`, and `loss` values provided by the trusted credit manager. However, these values are not validated against the actual transferred amounts, which could potentially lead to inconsistencies in the pool's state if there are any discrepancies in the provided values.

While the credit manager is a trusted role, adding a validation step would enhance the robustness of the system and prevent any unintentional errors. This can be achieved by calculating the actual transferred amount and comparing it with the sum of `repaidAmount`, `profit`, and `loss`.

### Recommendation:
Add a validation check in the `repayCreditAccount()` function:

```solidity
function repayCreditAccount(uint256 repaidAmount, uint256 profit, uint256 loss) external {
    // ... existing code ...

    uint256 actualTransferred = IERC20(underlyingToken).balanceOf(address(this)) - availableLiquidity();
    require(actualTransferred == repaidAmount + profit - loss, "Inconsistent profit/loss values");

    // ... rest of the function ...
}
```

This validation ensures that the provided values are consistent with the actual transferred amounts, maintaining the integrity of the pool's state.





## [L-06] Lack of liquidity check in PoolV3's lendCreditAccount function may cause unnecessary reverts

The `lendCreditAccount()` function in the `PoolV3` contract lacks a check to ensure that the `borrowedAmount` does not exceed the available liquidity in the pool. While credit managers are trusted roles, this oversight could lead to unnecessary transaction reverts if a credit manager attempts to borrow more than the available liquidity.

The function performs checks for zero amount and debt limits but does not verify if the pool has sufficient funds:

```solidity
function lendCreditAccount(
    uint256 borrowedAmount,
    address creditAccount
) external override creditManagerOnly whenNotPaused nonReentrant {
    // ... existing checks ...
    IERC20(underlyingToken).safeTransfer({to: creditAccount, value: borrowedAmount});
}
```

### Recommendation:
To improve the robustness of the contract and prevent potential issues, it is recommended to add a liquidity check before the transfer:

```solidity
if (borrowedAmount > availableLiquidity()) {
    revert InsufficientLiquidityException();
}
```

This addition would ensure that the function reverts with a clear error message if there's insufficient liquidity, preventing unnecessary gas costs and potential confusion for credit managers.





## [L-07] Lack of validation in `setInterestRateModel()` may allow setting of incompatible models

The `setInterestRateModel()` function in the `PoolV3` contract does not validate that the new interest rate model conforms to the expected `ILinearInterestRateModelV3` interface. This could potentially allow setting an incompatible or malicious contract as the interest rate model, which may lead to unexpected behavior or errors when the model's functions are called.

While the function is protected by the `configuratorOnly` modifier, which limits the risk, it's still recommended to add a basic interface compliance check to prevent potential issues.

### Recommendation:
Consider adding a simple validation check in the `setInterestRateModel()` function:

```solidity
function setInterestRateModel(address newInterestRateModel) external override configuratorOnly nonZeroAddress(newInterestRateModel) {
    require(ILinearInterestRateModelV3(newInterestRateModel).calcBorrowRate(0, 0, false) >= 0, "Invalid interest rate model");
    // ... rest of the function
}
```

This ensures that the new model at least implements the expected `calcBorrowRate()` function before setting it.



## [L-08] Unrestricted profit minting in PoolV3 contract may lead to share dilution

The `mintProfit()` function in the PoolV3 contract allows any credit manager to mint an arbitrary amount of profit to the treasury without any checks or balances. This could potentially be abused by a malicious credit manager to mint excessive amounts of profit, leading to share dilution and financial harm to the pool.

### Recommendation:
To mitigate this risk, consider implementing checks and balances to ensure that the amount of profit being minted is legitimate and justified. This could include adding a cap on the amount of profit that can be minted or requiring approval from a governance mechanism. For example:

```solidity
function mintProfit(uint256 amount) external creditManagerOnly {
    require(amount <= maxMintableProfit(), "Profit exceeds maximum allowed");
    // ... rest of the function
}

function maxMintableProfit() public view returns (uint256) {
    return expectedLiquidity() / 10; // Example: cap at 10% of expected liquidity
}
```



## [L-09] Lack of total debt limit check in `setCreditManagerDebtLimit()` may lead to over-borrowing

The `setCreditManagerDebtLimit()` function in the `PoolV3` contract allows setting debt limits for individual credit managers without checking if the sum of all credit managers' debt limits exceeds the pool's total debt limit. While the controller is a trusted role, this oversight could potentially lead to a situation where the pool allows more borrowing than intended, risking financial instability.

### Recommendation:

To mitigate this risk, consider implementing a check to ensure that the sum of all credit managers' debt limits does not exceed the pool's total debt limit when setting a new debt limit for a credit manager. This can be achieved by maintaining a running total of all credit manager debt limits and checking against the pool's total debt limit in the `setCreditManagerDebtLimit()` function:

```solidity
uint128 private _totalCreditManagerDebtLimit;

function setCreditManagerDebtLimit(address creditManager, uint256 newLimit) external override controllerOnly {
    // ... existing code ...

    uint128 newLimitU128 = _convertToU128(newLimit);
    uint128 oldLimit = _creditManagerDebt[creditManager].limit;
    uint128 newTotalCreditManagerDebtLimit = _totalCreditManagerDebtLimit + newLimitU128 - oldLimit;

    if (_totalDebt.limit != type(uint128).max) {
        require(newTotalCreditManagerDebtLimit <= _totalDebt.limit, "Total debt limit exceeded");
    }

    _creditManagerDebt[creditManager].limit = newLimitU128;
    _totalCreditManagerDebtLimit = newTotalCreditManagerDebtLimit;

    // ... rest of the function ...
}
```

This change would add an extra layer of safety to prevent potential over-borrowing scenarios, even with a trusted controller.