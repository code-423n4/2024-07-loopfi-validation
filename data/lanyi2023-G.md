> https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L568

When the penalty is 0, calling the safeTransferFrom function will consume a significant amount of gas. 

case test_liquidate_partial_1:
[PASS] test_liquidate_partial_1() (gas: 5218581)

Change to the following code:

```
if(penalty > 0){
            poolUnderlying.safeTransferFrom(msg.sender, address(pool), penalty);
        }
```
[PASS] test_liquidate_partial_1() (gas: 5212128)

The difference is 6453 gas