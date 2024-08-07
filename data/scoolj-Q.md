## Mismatch between NatSpec and Function Implementation

### Summary:
The function calcDecrease has a mismatch between its implementation and the NatSpec documentation. The NatSpec comment does not include the parameter cumulativeQuotaInterest, leading to potential misunderstandings for developers and auditors.

### Description:
The function calcDecrease computes new debt principal and interest index after decreasing debt. However, the NatSpec documentation does not mention the cumulativeQuotaInterest parameter, which is critical for understanding the function's behavior.

### Impact:
The missing parameter in the NatSpec documentation can lead to:

   * Developers misunderstanding how to properly use the function.
   * Auditors missing crucial checks related to cumulativeQuotaInterest.
   * Increased risk of introducing bugs due to incomplete understanding of the function's parameters and their roles.

### Root Cause:
The NatSpec documentation was not updated to reflect the addition of the cumulativeQuotaInterest parameter in the function signature.

### Proof of Concept:

    Current Function Implementation:

```
/**
 * @dev Computes new debt principal and interest index (and other values) after decreasing debt
 *      - Debt comprises of multiple components which are repaid in the following order:
 *        quota update fees => quota interest => base interest => debt principal.
 *        New values for all these components depend on what portion of each was repaid.
 *      - Debt principal, for example, only decreases if all previous components were fully repaid
 *      - The new credit account's interest index stays the same if base interest was not repaid at all,
 *        is set to the current interest index if base interest was repaid fully, and is a solution to
 *        the equation `debt * (indexNow / indexLastUpdate - 1) - delta = debt * (indexNow / indexNew - 1)`
 *        when only `delta` of accrued interest was repaid
 * @param amount Amount of debt to repay
 * @param debt Debt principal before repayment
 * @param cumulativeIndexNow The current interest index
 * @param cumulativeIndexLastUpdate Credit account's interest index as of last update
 * @return newDebt Debt principal after repayment
 * @return newCumulativeIndex Credit account's quota interest after repayment
 * @return profit Amount of underlying tokens received as fees by the DAO
 * @return newCumulativeQuotaInterest Credit account's accrued quota interest after repayment
 */
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

    if (cumulativeQuotaInterest != 0 && amountToRepay != 0) {
        // All interest accrued on the quota interest is taken by the DAO to be distributed to LP stakers, dLP stakers and the DAO

        if (amountToRepay >= cumulativeQuotaInterest) {
            amountToRepay -= cumulativeQuotaInterest;
            profit += cumulativeQuotaInterest;

            newCumulativeQuotaInterest = 0;
        } else {
            // If amount is not enough to repay quota interest + DAO fee, then send all to the stakers
            uint256 quotaInterestPaid = amountToRepay;
            profit += amountToRepay;
            amountToRepay = 0;

            newCumulativeQuotaInterest = uint128(cumulativeQuotaInterest - quotaInterestPaid);
        }
    } else {
        newCumulativeQuotaInterest = cumulativeQuotaInterest;
    }

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
            profit += amountToRepay;
            amountToRepay = 0;

            newCumulativeIndex =
                (INDEX_PRECISION * cumulativeIndexNow * cumulativeIndexLastUpdate) /
                (INDEX_PRECISION *
                    cumulativeIndexNow -
                    (INDEX_PRECISION * profit * cumulativeIndexLastUpdate) /
                    debt);
        }
    } else {
        newCumulativeIndex = cumulativeIndexLastUpdate;
    }
    newDebt = debt - amountToRepay;
}
```

### Recommended Mitigation:

    * Update NatSpec Documentation: Add the missing cumulativeQuotaInterest parameter in the NatSpec comment to describe the function's parameters accurately.

Missing Parameter in NatSpec:

    The cumulativeQuotaInterest parameter is not documented in the function's NatSpec comments.

Recommended Updated NatSpec Documentation:

```
    /**
     * @dev Computes new debt principal and interest index (and other values) after decreasing debt
     *      - Debt comprises of multiple components which are repaid in the following order:
     *        quota update fees => quota interest => base interest => debt principal.
     *        New values for all these components depend on what portion of each was repaid.
     *      - Debt principal, for example, only decreases if all previous components were fully repaid
     *      - The new credit account's interest index stays the same if base interest was not repaid at all,
     *        is set to the current interest index if base interest was repaid fully, and is a solution to
     *        the equation `debt * (indexNow / indexLastUpdate - 1) - delta = debt * (indexNow / indexNew - 1)`
     *        when only `delta` of accrued interest was repaid
     * @param amount Amount of debt to repay
     * @param debt Debt principal before repayment
     * @param cumulativeIndexNow The current interest index
     * @param cumulativeIndexLastUpdate Credit account's interest index as of last update
     * @param cumulativeQuotaInterest Credit account's accrued quota interest before repayment
     * @return newDebt Debt principal after repayment
     * @return newCumulativeIndex Credit account's quota interest after repayment
     * @return profit Amount of underlying tokens received as fees by the DAO
     * @return newCumulativeQuotaInterest Credit account's accrued quota interest after repayment
     */
```

