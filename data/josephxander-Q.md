# Set `Agent`s cannot verify their permission status via `Permission::hasPermission`

## SUMMARY

Permission status of an `Agent` cannot be viewed.

`Owner` can set `Caller` as `Agent` via `Permission::setPermissionAgent`, and as `Caller` via `Permission::modifyPermission`.

A `Caller` has the permission to perform an action on the `owner`'s behalf while an `Agent` has the permission to modify the permissions of the `owner`.

`Permission::hasPermission` checks if `owner` is `Caller`, or if `Caller` has the permission to perform an action on behalf of `owner`.

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/utils/Permission.sol#L66-L68

```
//src/utils/Permission.sol
//ln66-68
 function hasPermission(address owner, address caller) public view returns (bool) {
        return owner == caller || _permitted[owner][caller];  
    }

```

However, the permission status of `Agent`s recorded in mapping `Permission::_permittedAgents` via the `Permission::setPermissionAgent` is not checked in `Permission::hasPermission`. And there is no checker for such either.

## MITIGATION
* Create own unique `agentHasPermission` getter function
* Include `_permittedAgents[owner][caller];` in `Permission::hasPermission`.


# `PoolQuotaKeeperV3::setCreditManagers` and `PoolQuotaKeeperV3::setGauge` can be set to address(0)

There are no address(0) checks in `PoolQuotaKeeperV3::setCreditManagers` and `PoolQuotaKeeperV3::setGuage`

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/quotas/PoolQuotaKeeperV3.sol#L225-L247

```
//src/quotas/PoolQuotaKeeperV3.sol

    function setGauge(
        address _gauge
    )
        external
        override
        configuratorOnly // U:[PQK-2]
    {
        if (gauge != _gauge) {
            gauge = _gauge; // U:[PQK-8]
            emit SetGauge(_gauge); // U:[PQK-8]
        }
    }


    function setCreditManager(
        address token,
        address vault
    )
        external
        override
        configuratorOnly // U:[PQK-2]
    {
        creditManagers[token] = vault;
    }
```

## MITIGATION

Implement checks that `guage != address(0)` in  `PoolQuotaKeeperV3::setGuage`. And that `vault != address(0)` in `PoolQuotaKeeperV3::setCreditManagers`.

