# [QA-01] Inconsistent Access Control in `updateQuotaRevenue` Function
## Vulnerability detail
The `updateQuotaRevenue` function in the PoolV3 contract has inconsistent and potentially incorrect access control:

- The function is currently using the `creditManagerOnly` modifier.
- There's a commented-out `poolQuotaKeeperOnly` modifier.
- The function comment suggests it should be `poolQuotaKeeperOnly`.
The `setQuotaRevenue` function (which is similar in nature) uses the `poolQuotaKeeperOnly` modifier, further suggesting that `updateQuotaRevenue` should also use this modifier
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L691-L708
```
function updateQuotaRevenue(
    int256 quotaRevenueDelta
)
    external
    override
    nonReentrant // U:[LP-2B]
    //poolQuotaKeeperOnly // U:[LP-2C]
    creditManagerOnly
{
    _setQuotaRevenue(uint256(quotaRevenue().toInt256() + quotaRevenueDelta)); // U:[LP-19]
}
```
This inconsistency could lead to:

- Unauthorized access to critical functionality
- Potential manipulation of the quota revenue
- Violation of the principle of least privilege
- Confusion for developers and auditors reviewing the code
## Recommendation
- Clarify the intended access control for the `updateQuotaRevenue` function.
- Update the modifier to match the intended access control. If it should indeed be only callable by the pool quota keeper, change it to `poolQuotaKeeperOnly`

# [QA-02] Inconsistent Error Handling for Unsupported Tokens
## Vulnerability detail
The Flashlender contract handles unsupported tokens inconsistently across different functions. Specifically, the `maxFlashLoan` function silently returns 0 for unsupported tokens, while `flashFee` and `flashLoan` functions revert with specific errors.
This inconsistency could lead to confusion for users of the contract and potentially unexpected behavior in integrations.
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/Flashlender.sol#L64-L69
## Recommendation
- Make maxFlashLoan revert for unsupported tokens

# [QA-03] Incomplete ERC4626 Compliance
## Vulnerability detail
The `StakingLPEth` contract, while implementing the ERC4626 interface, does not fully comply with the standard when the cooldown feature is enabled. Specifically:

- When cooldownDuration > 0, the standard `withdraw` and `redeem` functions are disabled 
- The contract introduces non-standard functions `cooldownAssets` and `cooldownShares` for initiating withdrawals
- Users must call an additional unstake function to complete the withdrawal process: 
- This behavior violates the ERC4626 standard, which requires that users should be able to withdraw or redeem their shares at any time using the standard functions.

The incomplete ERC4626 compliance can lead to reduced Interoperability: Other protocols or contracts designed to work with ERC4626 vaults may not function correctly with this contract when cooldown is enabled.
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L104-L127
## Recommendation
Consider implementing the cooldown functionality in a separate, non-ERC4626 contract. This would allow for a clear separation between the standard-compliant functionality and the custom cooldown feature

# [QA-04] Inconsistent Error Handling in Vault Removal Process
## Vulnerability detail
The VaultRegistry contract has inconsistent error handling in its vault removal process. Specifically:

- The `removeVault` function checks if the vault is registered before attempting removal
- However, the `_removeVaultFromList` function, which is called by removeVault, does not handle the case where the vault is not found in the vaultList

This inconsistency could lead to a situation where a vault is marked as registered in the `registeredVaults` mapping but is not present in the `vaultList array`. If such a vault is attempted to be removed, the removeVault function would proceed (as the vault is in `registeredVaults`), but `_removeVaultFromList` would silently fail to remove anything from the list.
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/VaultRegistry.sol#L49-L56
## Recommendation
- Add a new custom error for this case

# [QA-05] No Mechanism to Remove Quota Tokens
## Vulnerability Detail
The `GaugeV3` contract includes a function to add quota tokens (`addQuotaToken`), but there is no corresponding function to remove quota tokens. This could be problematic if a token needs to be deprecated or removed from the system for any reason. Without a mechanism to remove quota tokens, the system may continue to operate with outdated or unwanted tokens, potentially leading to security risks or operational inefficiencies.
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/quotas/GaugeV3.sol#L232-L233
## Recommendation
Implement a function to remove quota tokens from the `GaugeV3` contract. This function should ensure that the token is actually added before attempting to remove it and should also handle the removal of the token from the quota keeper if necessary. Here is an example implementation:


# [QA-06] Improper Address Validation in `setVaultConfig` Function
## Vulnerability Detail
The `setVaultConfig` function in the `AuraVault` contract does not properly validate the `lockerRewards` address. The function currently checks if the `lockerRewards` address is `address(0x0)`, which is functionally correct but does not follow Solidity conventions. The conventional way to check for the zero address in Solidity is to use `address(0)`. This discrepancy can lead to confusion and potential issues in code readability and maintenance.
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L159-L160
## Recommendation 
To adhere to Solidity conventions and improve code readability, the check for the zero address should be updated from `address(0x0)` to `address(0)`. This change ensures that the code follows common practices and reduces the likelihood of confusion for developers who are familiar with Solidity conventions.
