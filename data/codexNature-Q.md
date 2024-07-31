## L-4: Solidity pragma should be specific, not wide

Consider using a specific version of Solidity instead of a wide version in your contracts. For example, instead of `pragma solidity ^0.8.0;`, use `pragma solidity 0.8.0;` Only the below list of contracts did not use `pragma solidity 0.8.19;`

### 6 Instances Found

- Found in src/Silo.sol [Line: 2](src/Silo.sol#L2)

	```solidity
	pragma solidity ^0.8.0;
	```

- Found in src/interfaces/IPoolQuotaKeeperV3.sol [Line: 4](src/interfaces/IPoolQuotaKeeperV3.sol#L4)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/interfaces/IPoolV3.sol [Line: 4](src/interfaces/IPoolV3.sol#L4)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/quotas/GaugeV3.sol [Line: 4](src/quotas/GaugeV3.sol#L4)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/quotas/PoolQuotaKeeperV3.sol [Line: 4](src/quotas/PoolQuotaKeeperV3.sol#L4)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/quotas/QuotasLogic.sol [Line: 4](src/quotas/QuotasLogic.sol#L4)

	```solidity
	pragma solidity ^0.8.17;
	```





## Deprecated OpenZeppelin functions should not be used

Openzeppelin has deprecated several functions and replaced them with newer versions. Please consult: https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3488  and  https://docs.openzeppelin.com/contracts/5.x/api/access#AccessControl for `_setUpRole` and https://docs.openzeppelin.com/contracts/3.x/api/token/erc20#SafeERC20 for `safeApprove`


### 5 Found Instances


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



## Unsafe ERC20 Operations should not be used

ERC20 functions may not behave as expected. For example: return values are not always meaningful. It is recommended that OpenZeppelin's SafeERC20 library be used.

### 3 Instances Found


- Found in src/Flashlender.sol [Line: 105](src/Flashlender.sol#L105)

	```solidity
           -      underlyingToken.transferFrom(address(receiver), address(pool), total);
           +      underlyingToken.safeTransferFrom(address(receiver), address(pool), total);
	```

- Found in src/Flashlender.sol [Line: 133](src/Flashlender.sol#L133)

	```solidity
           -      underlyingToken.transferFrom(address(receiver), address(pool), total);
           +      underlyingToken.safeTransferFrom(address(receiver), address(pool), total);
	```

- Found in src/interfaces/IFlashlender.sol [Line: 115](src/interfaces/IFlashlender.sol#L115)

	```solidity
           -      flashlender.underlyingToken().approve(address(flashlender), amount);
           +      flashlender.underlyingToken().safeApprove(address(flashlender), amount);
	```
