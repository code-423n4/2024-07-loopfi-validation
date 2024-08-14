
## `CDPVault` Contract: Gas Optimization

### 1. Function: `_getQuotedTokensData`

**Original Function Implementation:**

```solidity
function _getQuotedTokensData(
    DebtData memory cdd
) internal view returns (uint128 outstandingQuotaInterest, uint192 cumulativeQuotaIndexNow) {
    cumulativeQuotaIndexNow = IPoolQuotaKeeperV3(poolQuotaKeeper()).cumulativeIndex(address(token));
    uint128 outstandingInterestDelta = QuotasLogic.calcAccruedQuotaInterest(
        uint96(cdd.debt),
        cumulativeQuotaIndexNow,
        cdd.cumulativeQuotaIndexLU
    );

    outstandingQuotaInterest = outstandingInterestDelta; // U:[CM-24]
}
```

**Issues Identified:**

- **Redundant Variable Initialization:** The variable `outstandingQuotaInterest` is initialized redundantly as it directly mirrors the value of `outstandingInterestDelta`.

**Optimized Function Implementation:**

```solidity
function _getQuotedTokensData(
    DebtData memory cdd
) internal view returns (uint128 outstandingInterestDelta, uint192 cumulativeQuotaIndexNow) {
    cumulativeQuotaIndexNow = IPoolQuotaKeeperV3(poolQuotaKeeper()).cumulativeIndex(address(token));
    outstandingInterestDelta = QuotasLogic.calcAccruedQuotaInterest(
        uint96(cdd.debt),
        cumulativeQuotaIndexNow,
        cdd.cumulativeQuotaIndexLU
    );

    // Removed redundant assignment line
}
```

### Gas Optimization Details:

- **Eliminated Redundant Assignment:** The removal of the line `outstandingQuotaInterest = outstandingInterestDelta;` reduces the function's gas consumption by avoiding an unnecessary variable assignment.

**Permalink:**

[Link to Code](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L484-L495)

---

### 2. Function: `liquidatePosition`

The function makes two `transferFrom` calls to send funds from the user to the pool, whereas one can be made.

- [Initial TransferFrom](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L539)
- [Second TransferFrom](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L568)

---

### 3. Unused Function: `calcAccruedInterest`

The `calcAccruedInterest` function is not used and can be removed.

**Permalink:**

[Link to Function](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L716-L725)

---
