consider using openzepplins SafeCast Library to prevent unexpected overflows when downcasting

In https://github.com/code-423n4/2024-07-loopfi/blob/4f508781a49ffa53511e7e5ed6cda0ff0eb5bdc5/src/PoolV3.sol#L177

scope error :

Downcasting from uint256/int256 in Solidity does not revert on overflow. This can result in undesired exploitation or bugs, since developers usually assume that overflows raise errors. 


recommended :
Using this library eliminates an entire class of bugs, so it's recommended to use it always. Some exceptions are acceptable like with the classic uint256(uint160(address(variable)))