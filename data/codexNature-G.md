## `public` functions not used internally could be marked `external`

Instead of marking a function as `public`, consider marking it as `external` if it is not used internally, as functions marked `external` are more gas efficient than functions marked `public`

### 42 Instances Found 


- Found in src/PoolV3.sol [Line: 192](src/PoolV3.sol#L192)

	```diff
           -   function decimals() public view override(ERC20, ERC4626, IERC20Metadata) returns (uint8) {
           +   function decimals() external view override(ERC20, ERC4626, IERC20Metadata) returns (uint8) {
	```

- Found in src/PoolV3.sol [Line: 213](src/PoolV3.sol#L213)

	```diff
	    -   function expectedLiquidityLU() public view override returns (uint256) {
	    +   function expectedLiquidityLU() external view override returns (uint256) {
	```

- Found in src/PoolV3.sol [Line: 223](src/PoolV3.sol#L223)

	```diff
	    function totalAssets() public view override(ERC4626, IERC4626) returns (uint256 assets) {
	    function totalAssets() external view override(ERC4626, IERC4626) returns (uint256 assets) {
	```

- Found in src/PoolV3.sol [Line: 292](src/PoolV3.sol#L292)

	```diff
           -   function withdraw(uint256 assets,address receiver,address owner)
        public
        override(ERC4626, IERC4626)
           +   function withdraw(uint256 assets,address receiver,address owner)
        external
        override(ERC4626, IERC4626)
	```

- Found in src/PoolV3.sol [Line: 316](src/PoolV3.sol#L316)

	```diff
	    -  function redeem(uint256 shares,address receiver,address owner)
        public override(ERC4626, IERC4626)
	    +  function redeem(uint256 shares,address receiver,address owner)
        external override(ERC4626, IERC4626)
	```

- Found in src/PoolV3.sol [Line: 336](src/PoolV3.sol#L336)

	```diff
	    -  function previewDeposit(uint256 assets) public view override(ERC4626, IERC4626) returns (uint256 shares) {
	    +  function previewDeposit(uint256 assets) external view override(ERC4626, IERC4626) returns (uint256 shares) {
	```

- Found in src/PoolV3.sol [Line: 341](src/PoolV3.sol#L341)

	```diff
	    -  function previewMint(uint256 shares) public view override(ERC4626, IERC4626) returns (uint256) {
	    +  function previewMint(uint256 shares) external view override(ERC4626, IERC4626) returns (uint256) {
	```

- Found in src/PoolV3.sol [Line: 346](src/PoolV3.sol#L346)

	```diff
	    -  function previewWithdraw(uint256 assets) public view override(ERC4626, IERC4626) returns (uint256) {
	    +  function previewWithdraw(uint256 assets) external view override(ERC4626, IERC4626) returns (uint256) {
	```

- Found in src/PoolV3.sol [Line: 351](src/PoolV3.sol#L351)

	```diff
	    -  function previewRedeem(uint256 shares) public view override(ERC4626, IERC4626) returns (uint256) {
	    +  function previewRedeem(uint256 shares) external view override(ERC4626, IERC4626) returns (uint256) {
	```

- Found in src/PoolV3.sol [Line: 356](src/PoolV3.sol#L356)

	```diff
	    -  function maxDeposit(address) public view override(ERC4626, IERC4626) returns (uint256) {
	    +  function maxDeposit(address) external view override(ERC4626, IERC4626) returns (uint256) {
	```

- Found in src/PoolV3.sol [Line: 361](src/PoolV3.sol#L361)

	```diff
	    -  function maxMint(address) public view override(ERC4626, IERC4626) returns (uint256) {
	    +  function maxMint(address) external view override(ERC4626, IERC4626) returns (uint256) {
	```

- Found in src/PoolV3.sol [Line: 366](src/PoolV3.sol#L366)

	```diff
	    -  function maxWithdraw(address owner) public view override(ERC4626, IERC4626) returns (uint256) {
	    +  function maxWithdraw(address owner) external view override(ERC4626, IERC4626) returns (uint256) {
	```

- Found in src/PoolV3.sol [Line: 376](src/PoolV3.sol#L376)

	```diff
	    -  function maxRedeem(address owner) public view override(ERC4626, IERC4626) returns (uint256) {
	    +  function maxRedeem(address owner) external view override(ERC4626, IERC4626) returns (uint256) {
	```

- Found in src/PoolV3.sol [Line: 614](src/PoolV3.sol#L614)

	```diff
	    -  function baseInterestIndex() public view override returns (uint256) {
	    +  function baseInterestIndex() external view override returns (uint256) {
	```

- Found in src/StakingLPEth.sol [Line: 66](src/StakingLPEth.sol#L66)

	```diff
	    -  function withdraw(uint256 assets,address receiver,address _owner) public
	    +  function withdraw(uint256 assets,address receiver,address _owner) external
	```

- Found in src/StakingLPEth.sol [Line: 77](src/StakingLPEth.sol#L77)

	```diff
	    -  function redeem(uint256 shares,address receiver,address _owner)public
	    +  function redeem(uint256 shares,address receiver,address _owner)external
	```

- Found in src/oracle/BalancerOracle.sol [Line: 145](src/oracle/BalancerOracle.sol#L145)

	```diff
	    -  function getStatus(address /*token*/) public view virtual override returns (bool status) {
	    +  function getStatus(address /*token*/) external view virtual override returns (bool status) {
	```

- Found in src/oracle/ChainlinkOracle.sol [Line: 79](src/oracle/ChainlinkOracle.sol#L79)

	```diff
	    -  function getStatus(address token) public view virtual override returns (bool status) {
	    +  function getStatus(address token) external view virtual override returns (bool status) {
	```

- Found in src/proxy/PoolAction.sol [Line: 195](src/proxy/PoolAction.sol#L195)

	```diff
	    -  function exit(PoolActionParams memory poolActionParams) public returns (uint256 retAmount) {
	    +  function exit(PoolActionParams memory poolActionParams) external returns (uint256 retAmount) {
	```

- Found in src/proxy/SwapAction.sol [Line: 320](src/proxy/SwapAction.sol#L320)

	```diff
	    -  function getSwapToken(SwapParams calldata swapParams) public pure returns (address token) {
	    +  function getSwapToken(SwapParams calldata swapParams) external pure returns (address token) {
	```

- Found in src/quotas/PoolQuotaKeeperV3.sol [Line: 87](src/quotas/PoolQuotaKeeperV3.sol#L87)

	```diff
	    -  function cumulativeIndex(address token) public view override returns (uint192) {
	    +  function getSwapToken(SwapParams calldata swapParams) external pure returns (address token) {
	```

- Found in src/reward/ChefIncentivesController.sol [Line: 234](src/reward/ChefIncentivesController.sol#L234)

	```diff
	    -  function initialize(address rdntToken_,address lockZap_,address dao_,address priceProvider_,uint256 rewardsDuration_,uint256 rewardsLookback_,       uint256 lockDuration_, uint256 burnRatio_,uint256 vestDuration_) public
	    +  function initialize(address rdntToken_,address lockZap_,address dao_,address priceProvider_,uint256 rewardsDuration_,uint256 rewardsLookback_,       uint256 lockDuration_, uint256 burnRatio_,uint256 vestDuration_) external
	```

- Found in src/reward/ChefIncentivesController.sol [Line: 291](src/reward/ChefIncentivesController.sol#L291)

	```diff
	    -  function start() public onlyOwner {
	    +  function start() external onlyOwner {
	```

- Found in src/reward/ChefIncentivesController.sol [Line: 570](src/reward/ChefIncentivesController.sol#L570)

	```diff
	    -  function setEligibilityExempt(address _contract, bool _value) public {
	    +  function setEligibilityExempt(address _contract, bool _value) external {
	```

- Found in src/reward/ChefIncentivesController.sol [Line: 781](src/reward/ChefIncentivesController.sol#L781)

	```diff
	    -  function claimBounty(address _user, bool _execute) public returns (bool issueBaseBounty) {
	    +  function claimBounty(address _user, bool _execute) external returns (bool issueBaseBounty) {
	```

- Found in src/reward/ChefIncentivesController.sol [Line: 951](src/reward/ChefIncentivesController.sol#L951)

	```diff
	    -  function allPendingRewards(address _user) public view returns (uint256 pending) {
	    +  function allPendingRewards(address _user) external view returns (uint256 pending) {
	```

- Found in src/reward/EligibilityDataProvider.sol [Line: 95](src/reward/EligibilityDataProvider.sol#L95)

	```diff
	    -  function initialize(IVaultRegistry _vaultRegistry, IMultiFeeDistribution _multiFeeDistribution,IPriceProvider _priceProvider) public
	    +  function initialize(IVaultRegistry _vaultRegistry, IMultiFeeDistribution _multiFeeDistribution,IPriceProvider _priceProvider) external
	```

- Found in src/reward/EligibilityDataProvider.sol [Line: 208](src/reward/EligibilityDataProvider.sol#L208)

	```diff
	    -  function getDqTime(address _user) public view returns (uint256) {
	    +  function getDqTime(address _user) external view returns (uint256) {
	```

- Found in src/reward/EligibilityDataProvider.sol [Line: 220](src/reward/EligibilityDataProvider.sol#L220)

	```diff
	    -  function lastEligibleTime(address user) public view returns (uint256 lastEligibleTimestamp) {
	    +  function lastEligibleTime(address user) external view returns (uint256 lastEligibleTimestamp) {
	```

- Found in src/reward/MultiFeeDistribution.sol [Line: 221](src/reward/MultiFeeDistribution.sol#L221)

	```diff
	    -  function initialize(address rdntToken_,address lockZap_,address dao_,
        address priceProvider_,uint256 rewardsDuration_,uint256 rewardsLookback_,
        uint256 lockDuration_,uint256 burnRatio_,uint256 vestDuration_) public
	    +  function initialize(address rdntToken_,address lockZap_,address dao_,
        address priceProvider_,uint256 rewardsDuration_,uint256 rewardsLookback_,
        uint256 lockDuration_,uint256 burnRatio_,uint256 vestDuration_) external
	```

- Found in src/reward/MultiFeeDistribution.sol [Line: 843](src/reward/MultiFeeDistribution.sol#L843)

	```diff
	    -  function claimBounty(address user, bool execute) public whenNotPaused returns (bool issueBaseBounty) {
	    +  function claimBounty(address user, bool execute) external whenNotPaused returns (bool issueBaseBounty) {
	```

- Found in src/reward/MultiFeeDistribution.sol [Line: 873](src/reward/MultiFeeDistribution.sol#L873)

	```diff
	    -  function pause() public onlyOwner {
	    +  function pause() external onlyOwner {
	```

- Found in src/reward/MultiFeeDistribution.sol [Line: 880](src/reward/MultiFeeDistribution.sol#L880)

	```diff
	    -  function unpause() public onlyOwner {
	    +  function unpause() external onlyOwner {
	```

- Found in src/reward/MultiFeeDistribution.sol [Line: 940](src/reward/MultiFeeDistribution.sol#L940)

	```diff
	    -  function lockedBalance(address user) public view returns (uint256 locked) {
	    +  function lockedBalance(address user) external view returns (uint256 locked) {
	```

- Found in src/reward/MultiFeeDistribution.sol [Line: 961](src/reward/MultiFeeDistribution.sol#L961)

	```diff
	    -  function earnedBalances(address user) public
	    +  function earnedBalances(address user) external
	```

- Found in src/reward/MultiFeeDistribution.sol [Line: 1049](src/reward/MultiFeeDistribution.sol#L1049)

	```diff
	    -  function claimableRewards(address account) public view returns (IFeeDistribution.RewardData[] memory rewardsData) {
	    +  function claimableRewards(address account) external view returns (IFeeDistribution.RewardData[] memory rewardsData) {
	```

- Found in src/vendor/AuraVault.sol [Line: 152](src/vendor/AuraVault.sol#L152)

	```diff
	    -  function setVaultConfig(uint32 _claimerIncentive,uint32 _lockerIncentive,address _lockerRewards) public
	    +  function setVaultConfig(uint32 _claimerIncentive,uint32 _lockerIncentive,address _lockerRewards) external
	```

- Found in src/vendor/AuraVault.sol [Line: 199](src/vendor/AuraVault.sol#L199)

	```diff
	   -  function deposit(uint256 assets, address receiver) public virtual override(IERC4626, ERC4626) returns (uint256) {
	   +  function deposit(uint256 assets, address receiver) external virtual override(IERC4626, ERC4626) returns (uint256) {
	```

- Found in src/vendor/AuraVault.sol [Line: 216](src/vendor/AuraVault.sol#L216)

	```diff
	    -  function mint(uint256 shares, address receiver) public virtual override(IERC4626, ERC4626) returns (uint256) {
	    +  function mint(uint256 shares, address receiver) external virtual override(IERC4626, ERC4626) returns (uint256) {
	```

- Found in src/vendor/AuraVault.sol [Line: 233](src/vendor/AuraVault.sol#L233)

	```diff
	    -  function withdraw(uint256 assets,address receiver,address owner) public
	    +  function withdraw(uint256 assets,address receiver,address owner) external
	```

- Found in src/vendor/AuraVault.sol [Line: 256](src/vendor/AuraVault.sol#L256)

	```diff
	    -  function redeem(uint256 shares,address receiver,address owner) public
	    +  function redeem(uint256 shares,address receiver,address owner) external
	```

- Found in src/vendor/AuraVault.sol [Line: 315](src/vendor/AuraVault.sol#L315)

	```diff
	    -  function previewReward() public view returns (uint256 amount) {
	    +  function previewReward() external view returns (uint256 amount) {
	```