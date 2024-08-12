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

## 3: Missing zero address check in constructor

Vulnerability details

## Context:

In Solidity, constructors often take address parameters to initialize important components of a contract, such as owner or linked contracts. However, without a check, there's a risk that an address parameter could be mistakenly set to the zero address (0x0). This could occur due to a mistake or oversight during contract deployment. A zero address in a crucial role can cause serious issues, as it cannot perform actions like a normal address, and any funds sent to it are irretrievable. Therefore, it's crucial to include a zero address check in constructors to prevent such potential problems. If a zero address is detected, the constructor should revert the transaction.

## Findings

> ***Num of Instances: 9***

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L54C1-L61C6
```
	constructor(
    	address _liquidityPool,
    	string memory _name,
    	string memory _symbol
	) ERC4626(IERC20(_liquidityPool)) ERC20(_name, _symbol) {
    	silo = new Silo(address(this), _liquidityPool);
    	cooldownDuration = MAX_COOLDOWN_DURATION;
	}
```
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/Silo.sol#L18C1-L21C6
```
	constructor(address _stakingVault, address _lpEth) {
    	STAKING_VAULT = _stakingVault;
    	lpETH = IERC20(_lpEth);
	}
```
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction4626.sol#L25C1-L30C81
```
	constructor(
    	address flashlender_,
    	address swapActions_,
    	address poolAction_,
    	address vaultRegistry_
	) PositionAction(flashlender_, swapActions_, poolAction_, vaultRegistry_) {}
```
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction20.sol#L24C1-L29C80
```
	constructor(
    	address flashlender_,
    	address swapAction_,
    	address poolAction_,
    	address vaultRegistry_
	) PositionAction(flashlender_, swapAction_, poolAction_, vaultRegistry_) {}
```

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/quotas/PoolQuotaKeeperV3.sol#L79C1-L84C6
```
	constructor(
    	address _pool
	) ACLNonReentrantTrait(IPoolV3(_pool).addressProvider()) ContractsRegisterTrait(IPoolV3(_pool).addressProvider()) {
    	pool = _pool; // U:[PQK-1]
    	underlying = IPoolV3(_pool).asset(); // U:[PQK-1]
	}
```
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PoolAction.sol#L61C1-L64C6
```
	constructor(address balancerVault_, address _pendleRouter) {
    	balancerVault = IVault(balancerVault_);
    	pendleRouter = IPActionAddRemoveLiqV3(_pendleRouter);
	}
```
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/quotas/GaugeV3.sol#L52C1-L64C6
```
	constructor(
    	address _pool,
    	address _voter
	)
    	ACLNonReentrantTrait(IPoolV3(_pool).addressProvider())
    	nonZeroAddress(_voter) // U:[GA-01]
	{
    	pool = _pool; // U:[GA-01]
    	voter = _voter; // U:[GA-01]
    	epochLastUpdate = IGearStakingV3(_voter).getCurrentEpoch(); // U:[GA-01]
    	epochFrozen = true; // U:[GA-01]
    	emit SetFrozenEpoch(true); // U:[GA-01]
	}
```

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/BalancerOracle.sol#L68C1-L89C6
```
	constructor(
    	address balancerVault_,
    	address chainlinkOracle_,
    	address pool_,
    	uint256 updateWaitWindow_,
    	uint256 stalePeriod_
	) initializer {
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
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L120C1-L135C6
```
	constructor(
    	address rewardPool_,
    	address asset_,
    	address feed_,
    	address auraPriceOracle_,
    	uint32 maxClaimerIncentive_,
    	uint32 maxLockerIncentive_,
    	string memory tokenName_,
    	string memory tokenSymbol_
	) ERC4626(IERC20(asset_)) ERC20(tokenName_, tokenSymbol_) {
    	rewardPool = rewardPool_;
    	feed = feed_;
    	auraPriceOracle = auraPriceOracle_;
    	maxClaimerIncentive = maxClaimerIncentive_;
    	maxLockerIncentive = maxLockerIncentive_;
	}
```


## Tools Used

Manual Analysis
