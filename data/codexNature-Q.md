## Constants should be defined rather than using magic numbers

(https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/EligibilityDataProvider.sol#L274-L277)

## Recommendation

```diff
+    uint256 public constant DECIMALS = 10 ** 18;


    function _lockedUsdValue(uint256 lockedLP) internal view returns (uint256) {
        uint256 lpPrice = priceProvider.getLpTokenPriceUsd();
-       return (lockedLP * lpPrice) / 10 ** 18;
+       return (lockedLP * lpPrice) / DECIMALS;
    }
```



## Not emitting event for an important state change.

## Vulnerability Details
When changing state variables events are not emitted. EligibilityDataProvider (https://github.com/code-423n4/2024-07-loopfi/blob/main/src/reward/EligibilityDataProvider.sol)

## Tools Used
Manual Review

## Impact
The system doesn't record historical state changes.

## Recommendation
It is recommended that an event be included for `EligibilityDataProvider:refresh` function, so changes made to the blockchain are easily recorded and tracked.

```diff
+   event EligibilityRefreshed(address indexed user); 

    function refresh(address user) external returns (bool currentEligibility) {
        if (msg.sender != address(chef)) revert OnlyCIC();
        if (user == address(0)) revert AddressZero();

        updatePrice();
        currentEligibility = isEligibleForRewards(user);/
        if (currentEligibility && disqualifiedTime[user] != 0) {
            disqualifiedTime[user] = 0;
        }
        lastEligibleStatus[user] = currentEligibility;

+       emit EligibilityRefreshed(user);
    }



## Define and use `constant` variables instead of using literals

## Vulnerability Details
If the same constant literal value is used multiple times, create a constant state variable and reference it throughout the contract.

### 11 Instances Found


- Found in src/reward/MultiFeeDistribution.sol [Line: 733](src/reward/MultiFeeDistribution.sol#L733)

	```diff
	    -           uint256 reward = rewards[onBehalf][token] / 1e12;

        +           uint256 public constant ONE_SZABO = 1e12;
	    +           uint256 reward = rewards[onBehalf][token] / ONE_SZABO;
	```

- Found in src/reward/MultiFeeDistribution.sol [Line: 823](src/reward/MultiFeeDistribution.sol#L823)

	```diff
	   -     return (rewardData[rewardToken].rewardPerSecond * rewardsDuration) / 1e12;


        +           uint256 public constant ONE_SZABO = 1e12;
	    +    return (rewardData[rewardToken].rewardPerSecond * rewardsDuration) / ONE_SZABO;
	```

- Found in src/reward/MultiFeeDistribution.sol [Line: 1040](src/reward/MultiFeeDistribution.sol#L1040)

	```solidity
	    -        rptStored = rptStored + ((newReward * 1e18) / lockedSupplyWithMultiplier);

        +           uint256 public constant ONE_ETHER = 1e18;
	    +        rptStored = rptStored + ((newReward * ONE_ETHER) / lockedSupplyWithMultiplier);
	```

- Found in src/reward/MultiFeeDistribution.sol [Line: 1062](src/reward/MultiFeeDistribution.sol#L1062)

	```diff
	    -           rewardPerToken(rewardsData[i].token)) / 1e12;

        +           uint256 public constant ONE_SZABO = 1e12;
	    +           rewardPerToken(rewardsData[i].token)) / ONE_SZABO;
	```

- Found in src/reward/MultiFeeDistribution.sol [Line: 1200](src/reward/MultiFeeDistribution.sol#L1200)

	```diff
	    -       r.rewardPerSecond = (reward * 1e12) / rewardsDuration;

        +           uint256 public constant ONE_SZABO = 1e12;
	    +       r.rewardPerSecond = (reward * ONE_SZABO) / rewardsDuration;
	```

- Found in src/reward/MultiFeeDistribution.sol [Line: 1203](src/reward/MultiFeeDistribution.sol#L1203)

	```diff
	    -       uint256 leftover = (remaining * r.rewardPerSecond) / 1e12;

        +           uint256 public constant ONE_SZABO = 1e12;
	    +       uint256 leftover = (remaining * r.rewardPerSecond) / ONE_SZABO;
	```

- Found in src/reward/MultiFeeDistribution.sol [Line: 1204](src/reward/MultiFeeDistribution.sol#L1204)

	```diff
	    -        r.rewardPerSecond = ((reward + leftover) * 1e12) / rewardsDuration;

        +           uint256 public constant ONE_SZABO = 1e12;
	    -        r.rewardPerSecond = ((reward + leftover) * ONE_SZABO) / rewardsDuration;
	```

- Found in src/reward/MultiFeeDistribution.sol [Line: 1251](src/reward/MultiFeeDistribution.sol#L1251)

	```diff
	    -       uint256 reward = rewards[user][token] / 1e12;

        +           uint256 public constant ONE_SZABO = 1e12;
	    +       uint256 reward = rewards[user][token] / ONE_SZABO;
	```

- Found in src/reward/MultiFeeDistribution.sol [Line: 1447](src/reward/MultiFeeDistribution.sol#L1447)

	```diff
	    -    earnings = earnings + ((balance * realRPT) / 1e18);

        +           uint256 public constant ONE_SZABO = 1e12;
	    +    earnings = earnings + ((balance * realRPT) / ONE_SZABO);
	```

- Found in src/vendor/AuraVault.sol [Line: 183](src/vendor/AuraVault.sol#L183)

	```diff
	    -    return assets.mulDiv(totalSupply() + 10 ** _decimalsOffset(), totalAssets() + 1, rounding);

        +       uint256 public constant ONE = 1;
        +       uint256 public constant TEN = 10;
	    +    return assets.mulDiv(totalSupply() + TEN ** _decimalsOffset(), totalAssets() + ONE, rounding);
	```

- Found in src/vendor/AuraVault.sol [Line: 190](src/vendor/AuraVault.sol#L190)

	```diff
	        return shares.mulDiv(totalAssets() + 1, totalSupply() + 10 ** _decimalsOffset(), rounding);

        +       uint256 public constant ONE = 1;
        +       uint256 public constant TEN = 10;
	    +   return shares.mulDiv(totalAssets() + ONE, totalSupply() + TEN ** _decimalsOffset(), rounding);
	```







## Solidity pragma should be specific, not wide

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


