# Deprecated OpenZeppelin functions should not be used

Openzeppelin has deprecated several functions and replaced with newer versions. Please consult https://docs.openzeppelin.com/

- src/VaultRegistry.sol 
```33 _setupRole(DEFAULT_ADMIN_ROLE, msg.sender);```
```34 _setupRole(VAULT_MANAGER_ROLE, msg.sender);```
- src/vendor/AuraVault.sol
```204 IERC20(asset()).safeApprove(rewardPool, assets);```
```221 IERC20(asset()).safeApprove(rewardPool, assets);```
```293 IERC20(asset()).safeApprove(rewardPool, amountIn);```