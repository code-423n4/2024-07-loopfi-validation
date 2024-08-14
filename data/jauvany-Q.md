# Non Critical Issues

## 1: Spelling Errors

## 2: Open TODO in comments

Vulnerability details

## Context:

Production code should not have open TODOs as this makes code appear incomplete at production deployment

## Findings

> ***Num of Instances: 6***

https://github.com/jauvany/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/BalancerOracle.sol#L44
```
	// todo: can be packed in a single struct
```

https://github.com/jauvany/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L119
```
	// TODO: check inputs
```
https://github.com/jauvany/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L197
```
 	* TODO: account for unclaimed rewards
```
https://github.com/jauvany/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L214
```
 	* TODO: account for unclaimed rewards
```
https://github.com/jauvany/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L231
```
 	* TODO: account for unclaimed rewards
```
https://github.com/jauvany/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L254
```
 	* TODO account for unclaimed rewards
```

### Tools Used

Manual Analysis


# Low Impact Vulnerabilities

## 1: Avoid making withdraw/unstake functions Pausable

Vulnerability details

## Context:

Making withdraw or unstake functions pausable can create a risk by potentially locking users' funds indefinitely, especially in scenarios where the contract is paused for a prolonged period. This design could undermine trust and may not align with the decentralization principles of blockchain. It's advisable to design these functions with user security and accessibility in mind, ensuring that pausing capabilities are used judiciously and transparently, with clear conditions for resuming normal operations.

## Proof of Concept

> ***Num of Instances: 3***

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

## 4: Return values not checked for approve()

Vulnerability details

## Context:

The ERC-20 token standard does not dictate that the approve function must return a value. The function signature in the ERC-20 standard is function approve(address spender, uint tokens) public returns (bool success);. However, a well-implemented ERC-20 token contract will typically have approve return a boolean value indicating whether or not the operation was successful.

It's crucial to note that not all token contracts follow this practice. Some might not return a value, or they might return a value in a non-standard way. This inconsistency among token contracts is one reason why it's important to handle token interactions carefully in your smart contracts and to check the return value of approve when possible.

## Findings

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/SwapAction.sol#L258
```
    	IERC20(assetIn).forceApprove(address(balancerVault), amountToApprove);
```
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/SwapAction.sol#L302
```
        	IERC20(assetIn).forceApprove(address(uniRouter), amount);
```
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/SwapAction.sol#L314

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/SwapAction.sol#L344

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction4626.sol#L47

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction4626.sol#L51

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction4626.sol#L122

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction4626.sol#L128

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction20.sol#L41

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction20.sol#L69

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction.sol#L424

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction.sol#L446

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction.sol#L489

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction.sol#L556

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction.sol#L583

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PoolAction.sol#L134

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PoolAction.sol#L169

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PoolAction.sol#L251

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L204

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L221

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L293 

## Tools Used

Manual Analysis

## 5: Incorrect withdraw declaration

Vulnerability details

## Context:

In Solidity, it's essential for clarity and interoperability to correctly specify return types in function declarations. If the `withdraw` function is expected to return a `bool` to indicate success or failure, its omission could lead to ambiguity or unexpected behavior when interacting with or calling this function from other contracts or off-chain systems. Missing return values can mislead developers and potentially lead to contract integrations built on incorrect assumptions. To resolve this, the function declaration for `withdraw` should be modified to explicitly include the `bool` return type, ensuring clarity and correctness in contract interactions.

## Findings

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L66C1-L72C6
```
	function withdraw(
    	uint256 assets,
    	address receiver,
    	address _owner
	) public virtual override ensureCooldownOff returns (uint256) {
    	return super.withdraw(assets, receiver, _owner);
	}
```

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L166C1-L175C6
```
	function _withdraw(
    	address caller,
    	address receiver,
    	address _owner,
    	uint256 assets,
    	uint256 shares
	) internal override nonReentrant {
    	super._withdraw(caller, receiver, _owner, assets, shares);
    	_checkMinShares();
	}
```
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/Silo.sol#L28C1-L30C6
```
	function withdraw(address to, uint256 amount) external onlyStakingVault {
    	lpETH.safeTransfer(to, amount);
	}
```
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L401C1-L416C26
```
	function _withdraw(
    	address receiver,
    	address owner,
    	uint256 assetsSent,
    	uint256 assetsReceived,
    	uint256 amountToUser,
    	uint256 shares
	) internal {
    	if (msg.sender != owner) _spendAllowance({owner: owner, spender: msg.sender, amount: shares}); // U:[LP-8,9]
    	_burn(owner, shares); // U:[LP-8,9]

    	_updateBaseInterest({
        	expectedLiquidityDelta: -assetsSent.toInt256(),
        	availableLiquidityDelta: -assetsSent.toInt256(),
        	checkOptimalBorrowing: false
    	}); // U:[LP-8,9]
```
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L292
```
	function withdraw(
    	uint256 assets,
    	address receiver,
    	address owner
	)
    	public
    	override(ERC4626, IERC4626)
    	whenNotPaused // U:[LP-2A]
    	whenNotLocked
    	nonReentrant // U:[LP-2B]
    	nonZeroAddress(receiver) // U:[LP-5]
    	returns (uint256 shares)
	{
    	uint256 assetsToUser = _amountWithFee(assets);
    	uint256 assetsSent = _amountWithWithdrawalFee(assetsToUser); // U:[LP-8]
    	shares = _convertToShares(assetsSent); // U:[LP-8]
    	_withdraw(receiver, owner, assetsSent, assets, assetsToUser, shares); // U:[LP-8]
	}
```
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/MultiFeeDistribution.sol#L537C1-L537C49
```
	function withdraw(uint256 amount) external {
```
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L239
```
	function withdraw(address to, uint256 amount) external whenNotPaused returns (uint256 tokenAmount) {
    	tokenAmount = wdiv(amount, tokenScale);
    	int256 deltaCollateral = -toInt256(tokenAmount);
    	modifyCollateralAndDebt({
        	owner: to,
        	collateralizer: msg.sender,
        	creditor: msg.sender,
        	deltaCollateral: deltaCollateral,
        	deltaDebt: 0
    	});
	}
```
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L233C1-L237C69
```
	function withdraw(
    	uint256 assets,
    	address receiver,
    	address owner
	) public virtual override(IERC4626, ERC4626) returns (uint256) {
```
 
### Tools Used

Manual Analysis

## 6: Missing zero address check in initializer

Vulnerability details

## Context:

Initializer functions in contracts often set important parameters or addresses. Failing to check for the zero address (0x0000000000000000000000000000000000000000) in initializers can lead to unintended behavior, as this address typically signifies an unset or default value. Transfers to or interactions with the zero address can result in permanent loss of assets or broken functionality. It's crucial to add checks using `require(targetAddress != address(0), "Address cannot be zero")` in initializers to prevent accidentally setting important state variables or parameters to this address, ensuring the system's integrity and user asset safety.

## Findings

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/BalancerOracle.sol#L98C1-L105C6
```
	function initialize(address admin, address manager) external initializer {
    	// init. Access Control
    	__AccessControl_init();
    	// Role Admin
    	_grantRole(DEFAULT_ADMIN_ROLE, admin);
    	// Credit Manager
    	_grantRole(MANAGER_ROLE, manager);
	}
```
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/ChainlinkOracle.sol#L58C1-L65C6
```
	function initialize(address admin, address manager) external initializer {
    	// init. Access Control
    	__AccessControl_init();
    	// Role Admin
    	_grantRole(DEFAULT_ADMIN_ROLE, admin);
    	// Credit Manager
    	_grantRole(MANAGER_ROLE, manager);
	}
```

### Tools Used

Manual Analysis

## 7: Missing contract-existence checks before low-level calls

Vulnerability details

## Context:

Low-level calls in Solidity, when made to addresses without contract code, don't fail but return a successful status. This behavior can be misleading, leading to unintended consequences in dApps. Ignoring this can potentially mean acting on false positive results. To address this, apart from the conventional zero-address check, developers should verify the existence of contract code at the target address by ensuring that the code length at the specified address (`<address>.code.length`) is greater than zero. By doing so, it provides a more robust validation before executing low-level calls, safeguarding against unintentional interactions with empty addresses.

## Findings

> ***Num of Instances: 1***

https://github.com/jauvany/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction.sol#L269C1-L286C6
```
	function multisend(
    	address[] calldata targets,
    	bytes[] calldata data,
    	bool[] calldata delegateCall
	) external onlyDelegatecall {
    	uint256 totalTargets = targets.length;
    	for (uint256 i; i < totalTargets; ) {
        	if (delegateCall[i]) {
            	_delegateCall(targets[i], data[i]);
        	} else {
            	(bool success, bytes memory response) = targets[i].call(data[i]);
            	if (!success) _revertBytes(response);
        	}
        	unchecked {
            	++i;
        	}
    	}
	}
```

### Tools Used

Manual Analysis

## 8: Inconsistent use of _msgSender() and msg.sender in contract

Vulnerability details

## Context:

For the sake of consistency, stick to using only one of these values throughout the contract. Not doing so in this case can be quite harmful as _msgSender and msg.sender do have some differences, one being that msgSender cannot be used to determine if an account is a EOA but msg.sender can. Differences like these can introduce vulnerabilities is they are not properly acknowledged by the dev team.

## Proof of Concept

https://github.com/jauvany/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L201
```
    	_deposit(_msgSender(), receiver, assets, shares);
```
https://github.com/jauvany/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L218
```
    	_deposit(_msgSender(), receiver, assets, shares);
```
https://github.com/jauvany/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L245
```
    	_withdraw(_msgSender(), receiver, owner, assets, shares);
```
https://github.com/jauvany/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L266
```
    	_withdraw(_msgSender(), receiver, owner, assets, shares);
```

### Tools Used
Manual Analysis

### Recommended Mitigation steps
Use msg.Sender all along
