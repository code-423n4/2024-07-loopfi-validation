# L-1: Deprecated OpenZeppelin functions should not be used

Openzeppelin has deprecated several functions and replaced with newer versions. Please consult https://docs.openzeppelin.com/

- src/VaultRegistry.sol 
```33 _setupRole(DEFAULT_ADMIN_ROLE, msg.sender);```
```34 _setupRole(VAULT_MANAGER_ROLE, msg.sender);```
- src/vendor/AuraVault.sol
```204 IERC20(asset()).safeApprove(rewardPool, assets);```
```221 IERC20(asset()).safeApprove(rewardPool, assets);```
```293 IERC20(asset()).safeApprove(rewardPool, amountIn);```

# L-2: Unsafe ERC20 Operations should not be used
ERC20 functions may not behave as expected. For example: return values are not always meaningful. It is recommended to use OpenZeppelin's SafeERC20 library.

- src/Flashlender.sol 
```105 underlyingToken.transferFrom(address(receiver), address(pool), total);```
```133 underlyingToken.transferFrom(address(receiver), address(pool), total);```
- src/interfaces/IFlashlender.sol
```115 flashlender.underlyingToken().approve(address(flashlender), amount);```
- src/proxy/PoolAction.sol
```286 IPMarket(market).transferFrom(poolActionParams.recipient, market, netLpIn);```
```288 IPMarket(market).transfer(market, netLpIn);```
- src/proxy/SwapAction.sol
```358 IPMarket(market).transferFrom(recipient, market, netLpIn);```
```360 IPMarket(market).transfer(market, netLpIn);```

# L-3: Solidity pragma should be specific, not wide
Consider using a specific version of Solidity in your contracts instead of a wide version. For example, instead of `pragma solidity ^0.8.0;`, use `pragma solidity 0.8.0;`

# L-4: Missing checks for address(0) when assigning values to address state variables
Check for address(0) when assigning values to address state variables.

- src/CDPVault.sol
```209 if (parameter == "rewardController") rewardController = IChefIncentivesController(data);```
- src/PoolV3.sol
```179 interestRateModel = interestRateModel_; // U:[LP-1B]```
```754 interestRateModel = newInterestRateModel; // U:[LP-22B]```
- src/reward/ChefIncentivesController.sol
```273 bountyManager = _bountyManager;```
- src/vendor/AuraVault.sol
```131 feed = feed_;```
```132 auraPriceOracle = auraPriceOracle_;```
```146 if (parameter == "feed") feed = address(uint160(data));```
```147 else if (parameter == "auraPriceOracle") auraPriceOracle = address(uint160(data));```

# L-5: `public` functions not used internally could be marked `external`

Instead of marking a function as `public`, consider marking it as `external` if it is not used internally.

- ```    function decimals() public view override(ERC20, ERC4626, IERC20Metadata) returns (uint8) {```
- ```    function expectedLiquidityLU() public view override returns (uint256) {```
- ```     function totalAssets() public view override(ERC4626, IERC4626) returns (uint256 assets) {```
- ```    function withdraw(```

# L-6: Define and use `constant` variables instead of using literals

If the same constant literal value is used multiple times, create a constant state variable and reference it throughout the contract.

- MultiFeeDistribution.sol
```                uint256 reward = rewards[onBehalf][token] / 1e12;```
```         return (rewardData[rewardToken].rewardPerSecond * rewardsDuration) / 1e12;```
```            rptStored = rptStored + ((newReward * 1e18) / lockedSupplyWithMultiplier);```

# L-7: The `nonReentrant` `modifier` should occur before all other modifiers
Check `src/PoolV3.sol`

# L-8: Modifiers invoked only once can be shoe-horned into the function
Check `src/PoolV3.sol` and `src/Silo.sol`

- ```    modifier poolQuotaKeeperOnly() {```
- ```    modifier onlyStakingVault() {```

# L-9: Empty Block
Consider removing empty blocks.

- src/oracle/ChainlinkOracle.sol
```70 function _authorizeUpgrade(address /*implementation*/) internal virtual override onlyRole(MANAGER_ROLE) {}```
- src/reward/ChefIncentivesController.sol
```660 function handleActionBefore(address _user) external {}```
```667 function beforeLockUpdate(address _user) external {}```

# L-10: Contract still has TODOs
Check `src/oracle/BalancerOracle.sol` and `src/vendor/AuraVault.sol`

# L-11: Large literal values multiples of 10000 can be replaced with scientific notation

Use `e` notation, for example: `1e18`, instead of its full numeric value.

- src/reward/EligibilityDataProvider.sol
```20 uint256 public constant RATIO_DIVISOR = 10000;```
- src/reward/MultiFeeDistribution.sol
```39 uint256 public constant WHOLE = 100000; // 100%```
```43 uint256 public constant PERCENT_DIVISOR = 10000; //100%```
```47 uint256 public constant RATIO_DIVISOR = 10000;```
- src/vendor/AuraVault.sol
```47 uint256 private constant INCENTIVE_BASIS = 10000;```

# L-12: Inconsistency in declaring uint256/uint (or) int256/int variables within a contract. Use explicit size declarations (uint256 or int256).
Consider keeping the naming convention consistent in a given contract. Explicit size declarations are preferred (uint256, int256) over implicit ones (uint, int) to avoid confusion.

Check out `src/proxy/PositionAction.sol` 
- `        uint loanAmount = leverParams.primarySwap.amount;`

# L-13: Potentially unused `private` / `internal` state variables found.
State variable appears to be unused. No analysis has been performed to see if any inilne assembly references it. So if that's not the case, consider removing this unused variable.
- src/oracle/BalancerOracle.sol

```    uint256[47] private __gap;```
- src/oracle/ChainlinkOracle.sol

```    uint256[50] private __gap;```
- src/oracle/PendleLPOracle.sol

```    uint256[50] private __gap;```
