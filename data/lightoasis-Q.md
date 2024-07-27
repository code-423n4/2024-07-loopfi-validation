## Low Bugs.
### 1. Unused return values are redundant and can be removed.
[PoolV3.sol#L223](https://github.com/code-423n4/2024-07-loopfi/blob/4f508781a49ffa53511e7e5ed6cda0ff0eb5bdc5/src/PoolV3.sol#L223)
```solidity
    function totalAssets() public view override(ERC4626, IERC4626) returns (uint256 assets) {
        return expectedLiquidity();
    }
```
## Recommendation
```solidity
+   function totalAssets() public view override(ERC4626, IERC4626) returns (uint256) {
        return expectedLiquidity();
    }
```

### 2. The liquidator's amount; repayAmount is not scaled.

The underlying token may not be the same decimal as the collateral token. Hence, collateral tokens are scaled, to prevent their value from being too small or inconsistent. The liquidator's amount; `takeCollateral`, is gottent from `repayAmount` which is not scaled. This might make it to be smaller than expected or inconsistent with the borrowed underlying token amount.

If the collateral token's decimals are not scaled, and the collaterl's token's decimals are smaller than that of the underlying token's decimals, the collateral amount sent to the the liquidator will be smaller than expected.

## Proof Of Concept
Underlying tokens are 18 decimals at deployment. [PoolV3.sol#L182-#L184](https://github.com/code-423n4/2024-07-loopfi/blob/4f508781a49ffa53511e7e5ed6cda0ff0eb5bdc5/src/PoolV3.sol#L182-#L184)
```solidity
        if (ERC20(underlyingToken_).decimals() != 18) {
            revert IncompatibleDecimalsException();
        }
```

But as seen below, the liquidator's amount; `takeCollateral` which is gotten from `repayAmount`, is not scaled. This will lead to the liquidator recieving a smaller/incorrect amount.
[CDPVault.sol#L565](https://github.com/code-423n4/2024-07-loopfi/blob/4f508781a49ffa53511e7e5ed6cda0ff0eb5bdc5/src/CDPVault.sol#L565)
```solidity
    function liquidatePosition(address owner, uint256 repayAmount) external whenNotPaused {
        // validate params
        if (owner == address(0) || repayAmount == 0) revert CDPVault__liquidatePosition_invalidParameters();
            ...
        uint256 takeCollateral = wdiv(repayAmount, discountedPrice);
        uint256 deltaDebt = wmul(repayAmount, liqConfig_.liquidationPenalty);

            ...

        // transfer the collateral amount from the vault to the liquidator
        // @audit takeCollateral is not scaled, hence it might be smaller.
        token.safeTransfer(msg.sender, takeCollateral);

        // Mint the penalty from the vault to the treasury
        poolUnderlying.safeTransferFrom(msg.sender, address(pool), penalty);
        IPoolV3Loop(address(pool)).mintProfit(penalty);

        if (debtData.debt - newDebt != 0) {
            IPoolV3(pool).updateQuotaRevenue(_calcQuotaRevenueChange(-int(debtData.debt - newDebt))); // U:[PQK-15]
        }
    }
```

## Recommendation
`repayAmount` should be scaled to the collateral token's decimals.
```solidity
    function liquidatePosition(address owner, uint256 repayAmount) external whenNotPaused {
        // validate params
        if (owner == address(0) || repayAmount == 0) revert CDPVault__liquidatePosition_invalidParameters();
+       repayAmount = wdiv(repayAmount, tokenScale);
        ...
    }
```