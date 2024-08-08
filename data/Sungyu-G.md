## Unnecessary variable assignment in CDPVault::calcDecrease()

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L663

The calcDecrease() function in the CDPVault contract is used to compute the new debt principal and interest index (and other values) after decreasing debt : 

```
/// @dev Computes new debt principal and interest index (and other values) after decreasing debt
    ///      - Debt comprises of multiple components which are repaid in the following order:
    ///        quota update fees => quota interest => base interest => debt principal.
    ///        New values for all these components depend on what portion of each was repaid.
    ///      - Debt principal, for example, only decreases if all previous components were fully repaid
    ///      - The new credit account's interest index stays the same if base interest was not repaid at all,
    ///        is set to the current interest index if base interest was repaid fully, and is a solution to
    ///        the equation `debt * (indexNow / indexLastUpdate - 1) - delta = debt * (indexNow / indexNew - 1)`
    ///        when only `delta` of accrued interest was repaid
    /// @param amount Amount of debt to repay
    /// @param debt Debt principal before repayment
    /// @param cumulativeIndexNow The current interest index
    /// @param cumulativeIndexLastUpdate Credit account's interest index as of last update
    /// @return newDebt Debt principal after repayment
    /// @return newCumulativeIndex Credit account's quota interest after repayment
    /// @return profit Amount of underlying tokens received as fees by the DAO
    /// @return newCumulativeQuotaInterest Credit account's accrued quota interest after repayment
    // @return newQuotaFees Amount of unpaid quota fees left after repayment
function calcDecrease(
        uint256 amount,
        uint256 debt,
        uint256 cumulativeIndexNow,
        uint256 cumulativeIndexLastUpdate,
        uint128 cumulativeQuotaInterest
    )
        internal
        pure
        returns (uint256 newDebt, uint256 newCumulativeIndex, uint256 profit, uint128 newCumulativeQuotaInterest)
    {
        uint256 amountToRepay = amount;
        ....
     }
```

The amount parameter is meant to represent the amount of debt to repay. Notice how the amount variable is used to initialize the amountToRepay variable at the top of the calcDecrease() function. This is unnecessary behavior, as the amount parameter value is not used ever again in the function call. This results in unnecessary computation which will increase the gas cost for users. 

Instead, just directly use the amount parameter value for the calculation wherever the amountToRepay variable is used. This will allow you to avoid unnecessary mload, mstore, and assignment operations. It will also help avoid memory expansion costs per the solidity docs : "Memory is expanded by a word (256-bit), when accessing (either reading or writing) a previously untouched memory word (i.e. any offset within a word). At the time of expansion, the cost in gas must be paid. Memory is more costly the larger it grows (it scales quadratically)."

Here is an edit more efficient implementation of the same function. The only difference is that we directly edit the provided amount parameter : 

```
//Implementation which removes unncessary variable assignment. 
    //Instead directly edit the amount paramter. 
    function calcDecrease(
        uint256 amountToRepay,
        uint256 debt,
        uint256 cumulativeIndexNow,
        uint256 cumulativeIndexLastUpdate,
        uint128 cumulativeQuotaInterest
    )
        internal
        pure
        returns (uint256 newDebt, uint256 newCumulativeIndex, uint256 profit, uint128 newCumulativeQuotaInterest)
    {
        //uint256 amountToRepay = amount;

        if (cumulativeQuotaInterest != 0 && amountToRepay != 0) {
            // All interest accrued on the quota interest is taken by the DAO to be distributed to LP stakers, dLP stakers and the DAO

            if (amountToRepay >= cumulativeQuotaInterest) {
                amountToRepay -= cumulativeQuotaInterest; // U:[CL-3]
                profit += cumulativeQuotaInterest; // U:[CL-3]

                newCumulativeQuotaInterest = 0; // U:[CL-3]
            } else {
                // If amount is not enough to repay quota interest + DAO fee, then send all to the stakers
                uint256 quotaInterestPaid = amountToRepay; // U:[CL-3]
                profit += amountToRepay; // U:[CL-3]
                amountToRepay = 0; // U:[CL-3]

                newCumulativeQuotaInterest = uint128(cumulativeQuotaInterest - quotaInterestPaid); // U:[CL-3]
            }
        } else {
            newCumulativeQuotaInterest = cumulativeQuotaInterest;
        }

        //saying if there is any money left after paying the cumulative quota interest
        if (amountToRepay != 0) {
            uint256 interestAccrued = CreditLogic.calcAccruedInterest({
                amount: debt,
                cumulativeIndexLastUpdate: cumulativeIndexLastUpdate,
                cumulativeIndexNow: cumulativeIndexNow
            });
            // All interest accrued on the base interest is taken by the DAO to be distributed to LP stakers, dLP stakers and the DAO
            if (amountToRepay >= interestAccrued) {
                amountToRepay -= interestAccrued;

                profit += interestAccrued;

                newCumulativeIndex = cumulativeIndexNow;
            } else {
                // If amount is not enough to repay interest, then send all to the stakers and update index
                profit += amountToRepay; // U:[CL-3]
                amountToRepay = 0; // U:[CL-3]

                newCumulativeIndex =
                    (INDEX_PRECISION * cumulativeIndexNow * cumulativeIndexLastUpdate) /
                    (INDEX_PRECISION *
                        cumulativeIndexNow -
                        (INDEX_PRECISION * profit * cumulativeIndexLastUpdate) /
                        debt); // U:[CL-3]
            }
        } else {
            newCumulativeIndex = cumulativeIndexLastUpdate;
        }
        newDebt = debt - amountToRepay;
    }
```

When doing "forge snapshot --diff" on some of the test functions which uses the calcDecrease() function, we notice that all tests pass and a small reduction in gas costs was made when using the new implementation which just removes the unnecessary variable assignment : 
test_modifyCollateralAndDebt_repayPositionAndWithdraw() (gas: -600 (-0.012%)) 



## Unnecessary computation performed in CDPVault.sol on calls to deposit() and repay() leading to higher gas costs

The CDPVault::modifyCollateralAndDebt() function is the function responsible for modifying a Position's collateral and debt balances. This function is the core logic behind the external repay(), withdraw(), deposit(), and borrow() functions. 

```
function modifyCollateralAndDebt(
        address owner,
        address collateralizer,
        address creditor,
        int256 deltaCollateral,
        int256 deltaDebt
    ) public {

         ..........

        uint256 collateralValue = wmul(position.collateral, spotPrice_);

        if (
            (deltaDebt > 0 || deltaCollateral < 0) &&
            !_isCollateralized(calcTotalDebt(_calcDebt(position)), collateralValue, config.liquidationRatio)
        ) revert CDPVault__modifyCollateralAndDebt_notSafe();

        //quotaRevenueChange will always be equal to default uint value 0 for call originating from deposit. 
        if (quotaRevenueChange != 0) {
            IPoolV3(pool).updateQuotaRevenue(quotaRevenueChange); // U:[PQK-15]
        }
        emit ModifyCollateralAndDebt(owner, collateralizer, creditor, deltaCollateral, deltaDebt);
    }
```

Notice the following variable declaration : 
```
uint256 collateralValue = wmul(position.collateral, spotPrice_);
```

The variable collateralValue is declared outside the scope of the following if statement, but this variable is not utilized in the code anywhere else except for inside the scope of the following if statement. 

The if statement is only executed when the provided deltaDebt parameter is greater than 0 or if the provided deltaCollateral parameter is less than 0. Otherwise, it will short circuit the && and therefore also short circuit the following !_isCollateralized(calcTotalDebt(_calcDebt(position)), collateralValue, config.liquidationRatio), which happens to be the only place in the code that the collateralValue memory variable is used. Therefore, in situations where the if statement short circuits, the computation done to initialize the collateralValue memory variable was a waste since the variable is never used in the code. 

The function calls to CDPVault::deposit() and CDPVault::repay() will always call modifyCollateralAndDebt() with a deltaDebt value <=0 and a deltaCollateral parameter >= 0. Therefore, calls to CDPVault::deposit() and CDPVault::repay() will always short circuit the logic in the if statement and therefore there is no need to compute the value of collateralValue for these variables. In doing so we can save gas on the opcodes that would have been used to compute the value of collateralValue as well as avoid potential memory expansion costs. 

A more gas efficient implementation of the modifyCollateralAndDebt() is provided below. 

```
function modifyCollateralAndDebt(
        address owner,
        address collateralizer,
        address creditor,
        int256 deltaCollateral,
        int256 deltaDebt
    ) public {
        
    .......

        if ((deltaDebt > 0 || deltaCollateral < 0)){
            uint256 collateralValue = wmul(position.collateral, spotPrice_);
            if(!_isCollateralized(calcTotalDebt(_calcDebt(position)), collateralValue, config.liquidationRatio)){
                revert CDPVault__modifyCollateralAndDebt_notSafe();
            }
        }

        //quotaRevenueChange will always be equal to default uint value 0 for call originating from deposit. 
        if (quotaRevenueChange != 0) {
            IPoolV3(pool).updateQuotaRevenue(quotaRevenueChange); // U:[PQK-15]
        }
        emit ModifyCollateralAndDebt(owner, collateralizer, creditor, deltaCollateral, deltaDebt);
    }
``` 

Here the calculations for the collateralValue variable is moved inside an if statement to avoid having to perform the calculation in situations where it is unnecessary. 

Running "forge snapshot --diff" on the testing suite with the only difference being that the new more gas optimized implementation is used shows that this approach leads to a small gas cost decrease in a large number of tests which utilize the modifyCollateralAndDebt() function while not showing an increase in gas costs for any test. 