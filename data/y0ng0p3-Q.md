# L-01. Missing event emission for AuraVault configuration

There is no event emission when a new configuration is made for the AuraVault.

```solidity
File: src/vendor/AuraVault.sol#L152-L168

    function setVaultConfig(
        uint32 _claimerIncentive,
        uint32 _lockerIncentive,
        address _lockerRewards
    ) public onlyRole(VAULT_ADMIN_ROLE) returns (bool) {
        if (_claimerIncentive > maxClaimerIncentive) revert AuraVault__setVaultConfig_invalidClaimerIncentive();
        if (_lockerIncentive > maxLockerIncentive) revert AuraVault__setVaultConfig_invalidLockerIncentive();
        if (_lockerRewards == address(0x0)) revert AuraVault__setVaultConfig_invalidLockerRewards();

        vaultConfig = VaultConfig({
            claimerIncentive: _claimerIncentive,
            lockerIncentive: _lockerIncentive,
            lockerRewards: _lockerRewards
        });

        return true;
    }
```
https://github.com/code-423n4/2024-07-loopfi/blob/4f508781a49ffa53511e7e5ed6cda0ff0eb5bdc5/src/vendor/AuraVault.sol#L152-L168

# L-02. Define state variable constants rather than using magic strings

Use readable constants instead of literals.

```solidity
File: src/vendor/AuraVault.sol#L146-L147

        if (parameter == "feed") feed = address(uint160(data));
        else if (parameter == "auraPriceOracle") auraPriceOracle = address(uint160(data));
```
https://github.com/code-423n4/2024-07-loopfi/blob/4f508781a49ffa53511e7e5ed6cda0ff0eb5bdc5/src/vendor/AuraVault.sol#L1416-L147
