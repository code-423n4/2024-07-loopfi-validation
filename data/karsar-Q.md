# [L-1] A malicious user can drain accumlated rewards in the AuraVault.
 ## Impact 
In AuraVault:claim expects user to deposit WETH and claim rewards accumulated rewards .
If anyone can claim by depositing  there will no incentive for user to deposit in AuraVault . 

 ## Proof of Concept
 1. user deposit in AuraVault 
 2. its accumulating rewards for the Vault not itself 
 3. so when claim user wont get rewards until weth is deposited 
There is no check wether how much a user has rewards accumulated .So user who has deposited in AuraVault wont get any rewards for themselves.
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/vendor/AuraVault.sol#L280
 ## Tools 
 manual review 
 ## Recommended Mitigation Steps
 add a mechanism to check how amount has user deposited in the AuraVault and distribute rewards for the deposited amount.

# [L-2] when repay small amount < interest accrued  can change newCumulativeIndex which could result in quota interest changed   
 ## Impact 
This index change could result in quota interest being greater or lesser.
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/CDPVault.sol#L703

```Solidity
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
```

 ## Tools 
 manual review 
 ## Recommended Mitigation Steps
dont change index when repayamount is < interest accrued

# [L-3] newCumulativeQuotaInterest is not future proof when its changed in future it wont added interest when maxRepayment is paid 
 ## Impact 
>  // All interest accrued on the quota interest is taken by the DAO to be distributed to LP stakers, dLP stakers and the DAO
 
Loss of quota interest for stakers and the DAO
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/CDPVault.sol#L672
```Solidity 
  if (cumulativeQuotaInterest != 0 && amountToRepay != 0) {
            // All interest accrued on the quota interest is taken by the DAO to be distributed to LP stakers, dLP stakers and the DAO

            if (amountToRepay >= cumulativeQuotaInterest) {
                amountToRepay -= cumulativeQuotaInterest; // U:[CL-3]
                profit += cumulativeQuotaInterest; // U:[CL-3]
                //@audit
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
```

 ## Tools 
 manual review 
 ## Recommended Mitigation Steps
quota interest should also be added when repaid max amount as the quota interest is used when its partial and not for max amount

# [L-4]  quotarevenue() is not added in supplyRate as added in gearbox:Poolv3
 ## Impact 
As the quotarevenue() is not added which will provide incorrect supplyRate().
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/PoolV3.sol#L603

```Solidity 
    function supplyRate() external view override returns (uint256) {
        uint256 assets = expectedLiquidity();
        uint256 baseInterestRate_ = baseInterestRate();
        if (assets == 0) return baseInterestRate_;
        return
            //@audit-info quotarevenue() is not added
            ((baseInterestRate_ * _totalDebt.borrowed) * (PERCENTAGE_FACTOR - withdrawFee)) /
            PERCENTAGE_FACTOR /
            assets; // U:[LP-15]
    }
```
 ## Tools 
 manual review 
 ## Recommended Mitigation Steps
 add quotaRevenue() which will provide correct supplyRate()
```solidity
    function supplyRate() external view override returns (uint256) {
        uint256 assets = expectedLiquidity();
        uint256 baseInterestRate_ = baseInterestRate();
        if (assets == 0) return baseInterestRate_;
        return (baseInterestRate_ * _totalDebt.borrowed + quotaRevenue() * RAY) * (PERCENTAGE_FACTOR - withdrawFee)
            / PERCENTAGE_FACTOR / assets; // U:[LP-15]
    } 
```


