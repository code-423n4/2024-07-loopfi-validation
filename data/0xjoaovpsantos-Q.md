## [L-01] Attacker can initialize the implementation in the `ChainlinkOracle`

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

## [L-02] Attacker can initialize the implementation in the `BalancerOracle`

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

## [L-03] The `Storage Gaps` or `Namespaced Storage Layout` for the upgradeable contracts are not followed wich can affect layout of future child contracts in the `MultiFeeDistribution` contract.

### Description
`Storage Gaps` and `Namespaced Storage Layout` are patterns to reserving slots in a base contract. This allows future versions of that contract to use up those slots without affecting the storage layout of child contracts.

### Impact
The missing of the pattern like `Storage Gaps` and `Namespaced Storage Layout` will affect the storage layout of future child contracts. This is due to an unfollowed convention for implementation of upgradeable contracts.


### Recommended Mitigation
Implement [`Storage Gaps` like in the OpenZeppelin Docs](https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable#storage-gaps) or [`Namespaced Storage Layout` like in the OpenZeppelin Docs](https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable#namespaced-storage-layout) in the `src/reward/MultiFeeDistribution.sol`.