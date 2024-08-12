# Non Critical Issues

## Spelling Errors

# Low Impact Vulnerabilities

## 1: Avoid making withdraw/unstake functions Pausable

Vulnerability details

## Context:

Making withdraw or unstake functions pausable can create a risk by potentially locking users' funds indefinitely, especially in scenarios where the contract is paused for a prolonged period. This design could undermine trust and may not align with the decentralization principles of blockchain. It's advisable to design these functions with user security and accessibility in mind, ensuring that pausing capabilities are used judiciously and transparently, with clear conditions for resuming normal operations.

## Proof of Concept

> ***Num of Instances: 1***
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L239
```
	function withdraw(address to, uint256 amount) external whenNotPaused returns (uint256 tokenAmount) {
```
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L292C1-L304C6
```
	function withdraw( // <= Found
    	uint256 assets,
    	address receiver,
    	address owner
	)
    	public
    	override(ERC4626, IERC4626)
    	whenNotPaused // U:[LP-2A] // <= Found
    	whenNotLocked
    	nonReentrant // U:[LP-2B]
    	nonZeroAddress(receiver) // U:[LP-5]
    	returns (uint256 shares)
	{
```
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/MultiFeeDistribution.sol#L1351C1-L1356C56
```
	function _withdrawExpiredLocksFor( // <= Found
    	address address_,
    	bool isRelockAction,
    	bool doTransfer,
    	uint256 limit
	) internal whenNotPaused returns (uint256 amount) { // <= Found
```

## Tools Used
Manual Analysis

### Recommended Mitigation Steps
Avoid making withdraw/unstake functions Pausable

## 2: Solidity version 0.8.23 won't work on all chains due to MCOPY

Vulnerability details

## Context:

Solidity version 0.8.23 introduces the MCOPY opcode, this may not be implemented on all chains and L2 thus reducing the portability and compatibility of the code. Consider using an earlier solidity version.

## Proof of Concept

All files in scope

### Tools Used

Manual Analysis

