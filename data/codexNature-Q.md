## L-2: Deprecated OpenZeppelin functions should not be used

Openzeppelin has deprecated several functions and replaced them with newer versions. Please consult: https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3488  and  https://docs.openzeppelin.com/contracts/5.x/api/access#AccessControl for setUpRole and https://docs.openzeppelin.com/contracts/3.x/api/token/erc20#SafeERC20 for safeApprove


****5 Found Instances****


- Found in src/VaultRegistry.sol [Line: 33](src/VaultRegistry.sol#L33)

	```solidity
               -      _setupRole(DEFAULT_ADMIN_ROLE, msg.sender);
               +      _grantRole(DEFAULT_ADMIN_ROLE, msg.sender);
	```

- Found in src/VaultRegistry.sol [Line: 34](src/VaultRegistry.sol#L34)

	```solidity
               -      _setupRole(VAULT_MANAGER_ROLE, msg.sender);
               +      _grantRole(VAULT_MANAGER_ROLE, msg.sender);
	```

- Found in src/vendor/AuraVault.sol [Line: 204](src/vendor/AuraVault.sol#L204)

	```solidity
            -     IERC20(asset()).safeApprove(rewardPool, assets);
            +     IERC20(asset()).safeIncreaseAllowance(rewardPool, assets);
	```

- Found in src/vendor/AuraVault.sol [Line: 221](src/vendor/AuraVault.sol#L221)

	```solidity
            -      IERC20(asset()).safeApprove(rewardPool, assets);
            +      IERC20(asset()).safeIncreaseAllowance(rewardPool, assets);
	```

- Found in src/vendor/AuraVault.sol [Line: 293](src/vendor/AuraVault.sol#L293)

	```solidity
            -      IERC20(asset()).safeApprove(rewardPool, amountIn);
            +      IERC20(asset()).safeDecreaseAllowance(rewardPool, amountIn)
	```
