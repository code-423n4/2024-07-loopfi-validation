## [L-01] Math: Wrong error selector

- File:     src/utils/Math.sol
- Contract: Math.sol           
- Function: wexp              
- Line:     226 
- Link:     https://github.com/code-423n4/2024-07-loopfi/blob/4f508781a49ffa53511e7e5ed6cda0ff0eb5bdc5/src/utils/Math.sol#L226

The custom error selector `0xa37bfec9` (`ExpOverflow()`) is part of the original code and does not exist in the Math.sol contract.

Should be replaced by e.g. adding `error Math__exp_overflow();` and updating the selector the selector.


## [L-02] Math: Wrong error selector

- File:     src/utils/Math.sol
- Contract: Math.sol           
- Function: wln
- Line:     287
- Link:     https://github.com/code-423n4/2024-07-loopfi/blob/4f508781a49ffa53511e7e5ed6cda0ff0eb5bdc5/src/utils/Math.sol#L287

The custom error selector `0x1615e638` (`LnWadUndefined()`) is part of the original code and does not exist in the Math.sol contract.

Should be replaced by e.g. adding `error Math__ln_undefined();` and updating the selector the selector.


## [L-03] CDPVault: Missing guardrails

- File:     src/CDPVault.sol
- Contract: CDPVault.sol
- Function: setParameter
- Line:     195
- Link:     https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L195-L202

No input validation/guardrails on parameters. 

Consider adding checks to avoid errors when changing parameters and to protect users from rouge owners (e.g. setting liquidationDiscount to 0).


## [L-04] CDPVault: Inconsistent definition/usage of bad debt

- File:     src/CDPVault.sol
- Contract: CDPVault.sol
- Function: liquidatePosition
  - Line:     526
  - Link:     https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L526
- Function: liquidatePositionBadDebt
  - Line:     599
  - Link:     https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L599

The `liquidatePosition` function reverts with the error `CDPVault__BadDebt` if the total debt exceeds the product of the collateral and the price.  On the other hand, the `liquidatePositionBadDebt` function reverts with the same error if the total debt exceeds the product of the collateral and the discounted price.

This leads to inconsistencies in the bad debt definition between these functions. Consider revising one of them to ensure more consistent error messaging.


## [L-05] PoolV3: supplyRate is not accurate

- File:     src/PoolV3.sol
- Contract: PoolV3.sol
- Function: supplyRate
- Line:     607
- Link:     https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L607-L610

The supply rate function currently does not take quota revenue into account when calculating the result. Consider updating it to include quota revenue, for example, by:
```
(baseInterestRate_ * _totalDebt.borrowed + quotaRevenue() * RAY) * (PERCENTAGE_FACTOR - withdrawFee) / PERCENTAGE_FACTOR / assets;
```

Adding `quotaRevenue() * RAY` would make the supply rate more accurate.


## [L-06] PoolV3: potential underflow

- File:     src/PoolV3.sol
- Contract: PoolV3.sol
- Function: updateQuotaRevenue
- Line:     700
- Link:     https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L691-L701

The functions adds the current `quotaRevenue` and the `quotaRevenueDelta` and converts it to a uint256:
```
uint256(quotaRevenue().toInt256() + quotaRevenueDelta)
```

This can underflow, if the absolute value of `quotaRevenueDelta` is greater as the current `quotaRevenue`. As this is a restricted function, only credit managers can call it. The current manager implementations correctly calculate the delta `quotaRevenueDelta`. To avoid issues later (i.e. if the credit managers get updated), consider improving the input validation, for example by adding a check if the delta is too small:
```
uint256 quotaRevenue = quotaRevenue();
if (Math.abs(quotaRevenueDelta) > quotaRevenue) {
  revert QuotaRevenueDeltaTooSmall();
}
_setQuotaRevenue(uint256(quotaRevenue + quotaRevenueDelta));
```


## [L-07] PoolV3: expectedLiquidityDelta is wrong

- File:     src/PoolV3.sol
- Contract: PoolV3.sol
- Function: repayCreditAccount
- Line:     567
- Link:     https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L567

`repayCreditAccount` can be called with `profit` and `loss`. At line 566 the function calls `_updateBaseInterest`. One parameter of that function is `expectedLiquidityDelta`. Based on `expectedLiquidityDelta` it then updates `_expectedLiquidityLU` and `_baseInterestRate` [see](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L642-L668).
At line [567](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L567) the `expectedLiquidityDelta` is set as:
```
  expectedLiquidityDelta: -loss.toInt256(),
```
This does not consider the potential profit and therefor creates a wrong pool state. It should be updated to include the profit as well, for example:
 
```
_updateBaseInterest({
  expectedLiquidityDelta: profit.toInt256() - loss.toInt256(),
  availableLiquidityDelta: 0,
  checkOptimalBorrowing: false
});
```


## [L-08] PoolV3: Inconsistent Handling of Profit and Loss in repayCreditAccount

- File:     src/PoolV3.sol
- Contract: PoolV3.sol
- Function: repayCreditAccount
- Line:     548
- Link:     https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L548-L564

The `repayCreditAccount` function allows both profit and loss to be set to values greater than zero, but it currently only updates the profit side if both are set. As this is a restricted function, only credit managers can call it. The current manager implementations correctly only use one at a time. To avoid issues later (i.e. if the credit managers get updated), consider improving the input validation, for example:
```
if (profit > 0 && loss > 0) {
  revert CantUpdateProfitAndLoss();
}
```


## [L-09] Flashlender: Confusing variable names

- File:     src/Flashlender.sol
- Contract: Flashlender.sol
- Link:     https://github.com/code-423n4/2024-07-loopfi/blob/main/src/Flashlender.sol

The flashlender calls the token a stable coin. This is confusing as the loop protocol does not use a stable coin as per the docs.


## [L-10] VaultRegistry: Wrong natspec

- File:     src/VaultRegistry.sol
- Contract: VaultRegistry.sol
- Function: getUserTotalDebt
- Line:     63
- Link:     https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/VaultRegistry.sol#L63-L65

The natspec states:
```
/// @notice Returns the aggregated position stats for a user across all vaults
```

This does not match the implementation of the function. It aggregates the user's total debt across all positions in all vaults. It also misses the return spec. Consider updating the natspec to:
```
/// @notice Returns the aggregated total debt of the user across all positions in all vaults
/// @param user The position owner
/// @return totalNormalDebt aggregated user debt
```


## [L-11] PoolAction: Wrong natspec

- File:     src/proxy/PoolAction.sol
- Contract: PoolAction.sol
- Function: updateLeverJoin
- Line:     181
- Link:     https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PoolAction.sol#L181

Missing `address poolToken` on param and `PoolActionParams memory outParams` on return natspec.


## [L-12] ChefIncentivesController: anyone can call claim

- File:     src/reward/ChefIncentivesController.sol
- Contract: ChefIncentivesController.sol
- Function: claim
- Line:     518
- Link:     https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L518

Consider changing the claim function to a restricted function that only the user can call themselves. Since some jurisdictions consider this a taxable event, allowing anyone to claim on behalf of another user could lead to potential issues, such as griefing.


## [L-13] ChefIncentivesController: setEmissionSchedule 

- File:     src/reward/ChefIncentivesController.sol
- Contract: ChefIncentivesController.sol
- Function: setEmissionSchedule
- Line:     394
- Link:     https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L394-L423

The function does not verify if the new `startTimeOffsets` are greater than the existing entries in the `emissionSchedule`. While it ensures that the new array is in ascending order, it does not confirm that the new data is added in the correct position. Instead, the new entries are simply appended to the end of the existing list ([L#412](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L412-L416)), which can result in an unsorted `emissionSchedule` array. Consequently, this could cause the `setScheduledRewardsPerSecond` function ([L#352](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L352C14-L352C42)) to skip those unsorted entries.
