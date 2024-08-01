## [L-01] Incorrect verification about `stalePeriod` in the `BalancerOracle::_getStatus`

### Description
The function `BalancerOracle::_getStatus` has the verification:

```solidity
    status = (safePrice != 0) && block.timestamp - lastUpdate < stalePeriod;
```

For exmaple: 

- block.timestamp = 200
- lastUpdate = 150
- stalePeriod = 50

- (200 - 150) < 50
- 50 < 50

In this case the difference of `block.timestamp` minus `lastUpdate` is equal to `stalePeriod` and it must be reverted but will not.

### Impact
If the difference of `block.timestamp` minus `lastUpdate` is equal to `stalePeriod` it should revert because the time is in the `stalePeriod` but will not, failing the verification.

### Recommended Mitigation
In the `BalancerOracle::_getStatus` fix the verification:

```diff
-       status = (safePrice != 0) && block.timestamp - lastUpdate < stalePeriod;
+       status = (safePrice != 0) && block.timestamp - lastUpdate <= stalePeriod;
```

## [L-02] Incorrect verification about `updateWaitWindow` in the `BalancerOracle::update`

### Description
The function `BalancerOracle::update` has the verification:

```solidity
    if (block.timestamp - lastUpdate < updateWaitWindow) revert BalancerOracle__update_InUpdateWaitWindow();
```

For exmaple: 

- block.timestamp = 200
- lastUpdate = 199
- updateWaitWindow = 1

- (200 - 199) < 1
- 1 < 1

In this case the difference of `block.timestamp` minus `lastUpdate` is equal to `updateWaitWindow` and it must be reverted but will not.

### Impact
If the difference of `block.timestamp` minus `lastUpdate` is equal to `updateWaitWindow` it should revert because the time is in the `updateWaitWindow` but will not, failing the verification.

### Recommended Mitigation
In the `BalancerOracle::update` fix the verification:

```diff
-       if (block.timestamp - lastUpdate < updateWaitWindow) revert BalancerOracle__update_InUpdateWaitWindow();
+       if (block.timestamp - lastUpdate <= updateWaitWindow) revert BalancerOracle__update_InUpdateWaitWindow();
```

## [L-03] Attacker can initialize the implementation in the `ChainlinkOracle`

### Description
The contract `ChainlinkOracle` is upgradable, inheriting from the `UUPSUpgradeable`. However, the current implementation is missing the `_disableInitializers()` function call in the constructor. Thus, an attacker can initialize the implementation. We can see more details in the [OpenZeppelin docs](https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable#initializing_the_implementation_contract)

### Impact
Usually, the initialized implementation has no direct impact on the proxy itself, however, it can be exploited in a phising attack. In rare cases, the implementation might be mutable and may have an impact on the proxy.

### Recommended Mitigation
It's recommended to call `_disableInitializers()` within the contract constructor to prevent the implementation from being initialized.

In the `src/oracle/ChainlinkOracle.sol` add this code:

```diff
+    /// @custom:oz-upgrades-unsafe-allow constructor
+    constructor() {
+        _disableInitializers();
+    }
```

## [L-04] Attacker can initialize the implementation in the `BalancerOracle`

### Description
The contract `BalancerOracle` is upgradable, inheriting from the `UUPSUpgradeable`. However, the current implementation is missing the `_disableInitializers()` function call in the constructor. Thus, an attacker can initialize the implementation. We can see more details in the [OpenZeppelin docs](https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable#initializing_the_implementation_contract)

### Impact
Usually, the initialized implementation has no direct impact on the proxy itself, however, it can be exploited in a phising attack. In rare cases, the implementation might be mutable and may have an impact on the proxy.

### Recommended Mitigation
It's recommended to call `_disableInitializers()` within the contract constructor to prevent the implementation from being initialized.

In the `src/oracle/BalancerOracle.sol` add this code:

```diff
    constructor(
        address balancerVault_,
        address chainlinkOracle_,
        address pool_,
        uint256 updateWaitWindow_,
        uint256 stalePeriod_
-    ) initializer {
+    ) {
+       _disableInitializers();

        balancerVault = IVault(balancerVault_);
        updateWaitWindow = updateWaitWindow_;
        stalePeriod = stalePeriod_;
        chainlinkOracle = IOracle(chainlinkOracle_);
        pool = pool_;
        poolId = IWeightedPool(pool).getPoolId();

        (address[] memory tokens, , ) = balancerVault.getPoolTokens(poolId);

        // store the tokens
        uint256 len = tokens.length;
        token0 = (len > 0) ? tokens[0] : address(0);
        token1 = (len > 1) ? tokens[1] : address(0);
        token2 = (len > 2) ? tokens[2] : address(0);
    }
```