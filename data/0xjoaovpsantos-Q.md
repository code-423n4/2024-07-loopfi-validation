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