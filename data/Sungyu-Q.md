## Another instance of [NC-24] Incomplete NatSpec: @param is missing on actually documented functions.

This is another instance of the Incomplete NatSpec non-critical QA issue from the automated findings report. 

The calcDecrease() function in CDPVault.sol is missing a @param NatSpec for the uint128 cumulativeQuotaInterest parameter. However, it is not included as an instance in the provided Automated Report : 
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L657

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
        ....
    }
```



## Inaccurate comments/function names in ChefIncentivesController, EligibilityDataProvider, and MultiFeeDistribution

LoopFi implements its reward contracts heavily based on the Radiant protocol. However, it is mentioned in the documentation that it does not use the Radiant token for rewards but instead uses its own dLP token. However, it looks like the comments in many of these contracts were just directly copied over from the Radiant protocol. This leads to inaccurate comments regarding the functionality of many of the functions in the listed contracts. The comments found in these contracts should be updated to reflect the expected behavior in the Loopfi protocol instead of directly copying over the comments from the Radiant protocol. This will drastically help improve the readability of these contracts. 

A quick example is listed below : 
```
    /// @notice Duration of vesting RDNT
    uint256 public vestDuration;
```
The RDNT token is not the actual token that is vested in this contract. Instead it is the dLP token. Therefore, the comment should be updated to "Duration of vesting dLP". Directly copying the comments over from the Radiant protocol hurts the readability of the code, which goes against the core purpose of adding comments to our code which is to improve readability.

The same applies for many of the function names provided in these contracts. Even if the functionality stays the same, the variable/function names should be updated to reflect the behavior of the LoopFi protocol to improve the readability of the code. 

Instances : 
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/MultiFeeDistribution.sol#L75
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/MultiFeeDistribution.sol#L64
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/MultiFeeDistribution.sol#L209
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/MultiFeeDistribution.sol#L1218C8-L1221C28
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L213
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L289
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L389
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L787
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L851
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L947
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/EligibilityDataProvider.sol#L39
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/EligibilityDataProvider.sol#L48
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/EligibilityDataProvider.sol#L174