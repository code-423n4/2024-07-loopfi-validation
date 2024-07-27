Title:
State Variables That Could Be Declared Immutable

Links to Affected Code:
https://github.com/code-423n4/2024-07-loopfi/blob/4f508781a49ffa53511e7e5ed6cda0ff0eb5bdc5/src/CDPVault.sol#L55
- [Token.sol](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/reward/Token.sol#L7)

## Impact
The `token` variable in CDPVault and `_customDecimals` in Token should be declared `immutable` since they are set only once in the constructor and not modified thereafter. Declaring these variables as `immutable` can save gas and make the code safer and clearer.

## Proof of Concept
In `CDPVault.sol`:

```solidity
IERC20 public token;

In Token.sol

uint8 private _customDecimals;


To reproduce the issue:

Review the contract deployment.
Ensure the token and _customDecimals variables are set only in the constructor and never modified.

Tools Used
Manual Code Review

Recommended Mitigation Steps
Declare these variables as immutable:

In CDPVault.sol:

solidity

IERC20 public immutable token;

In Token.sol:

uint8 private immutable _customDecimals;


