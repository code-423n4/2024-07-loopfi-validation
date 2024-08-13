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

# [L-2] Admin can set arbitrary `vaultConfig` /`LiquidityConfig` value
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L195
Admin can set the parameters for a particular vault by calling `setparameter` , but there is very little to no sanitisation done to protect it from arbitrary values.
## Impact 
Having a high `liqdiscount` will result in high discount in case of liquidation
Having a high `liqrate` will result in high chances of getting liquidated

## Recommendation
Have a threshold for the values, which if set outside the threshold should revert

# [L-3] Low level calls to address should be checked
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/BaseAction.sol#L20
It is a known issue that addresses where low level calls are occurring
According to the [Solidity docs](https://docs.soliditylang.org/en/develop/control-structures.html#error-handling-assert-require-revert-and-exceptions), "The low-level functions call, delegatecall and staticcall return true as their first return value if the account called is non-existent, as part of the design of the EVM. Account existence must be checked prior to calling if needed"
## Recommendation
Add a check for contract existance before the low level calls, so that failures are not missed.