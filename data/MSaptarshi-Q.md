# [L-1] Flashloan amount should check for maxFlashloan
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/Flashlender.sol#L87

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/Flashlender.sol#L64
In maxflashloan the amount of borrowableToken is checked with this function
```solidity
function maxFlashLoan(address token) external view override returns (uint256 max) {
        if (token == address(underlyingToken)) {
            max = pool.creditManagerBorrowable(address(this));
        }
    }
```
which calls `Poolv3::creditManagerBorrowable` and the the maximum borrowable amonunt is `min(borrowable, available)`
```
 borrowable = Math.min(borrowable, _borrowable(_creditManagerDebt[creditManager])); // U:[LP-12]
        if (borrowable == 0) return 0; // U:[LP-12]

        uint256 available = ILinearInterestRateModelV3(interestRateModel).availableToBorrow({
            expectedLiquidity: expectedLiquidity(),
            availableLiquidity: availableLiquidity()
        }); // U:[LP-12]

        borrowable = Math.min(borrowable, available);
```

Which means while borrowing a flashloan a user could borrow the amount `max(borrowable, available)`

## Recomendation
Query the `maxFlashloan` function in `flashloan`
Also query the flashloanFee function in the flashloan, instead of calculating it