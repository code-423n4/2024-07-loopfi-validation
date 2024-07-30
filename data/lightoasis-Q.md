## Low Bugs.
### Unused return values are redundant and can be removed.
[PoolV3.sol#L223](https://github.com/code-423n4/2024-07-loopfi/blob/4f508781a49ffa53511e7e5ed6cda0ff0eb5bdc5/src/PoolV3.sol#L223)
```solidity
    function totalAssets() public view override(ERC4626, IERC4626) returns (uint256 assets) {
        return expectedLiquidity();
    }
```
## Recommendation
```solidity
+   function totalAssets() public view override(ERC4626, IERC4626) returns (uint256) {
        return expectedLiquidity();
    }
```
