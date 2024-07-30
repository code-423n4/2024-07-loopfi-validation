### Contracts are vulnerable to fee-on-transfer accounting-related issues
The function(s) below transfer funds from the caller to the receiver via `transferFrom()`, but do not ensure that the actual number of tokens received is the same as the input amount to the transfer. If the token is a fee-on-transfer token, the balance after the transfer will be smaller than expected, leading to accounting issues. Even if there are checks later, related to a secondary transfer, an attacker may be able to use latent funds (e.g. mistakenly sent by another user) in order to get a free credit. One way to address this problem is to measure the balance before and after the transfer, and use the difference as the amount, rather than the stated amount.

```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

219:        buy.safeTransferFrom(msg.sender, address(this), amountIn);	// @audit-issue
```
[219](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L219-L219), 


#### Recommendation

To mitigate potential vulnerabilities and ensure accurate accounting with fee-on-transfer tokens, modify your contract's token transfer logic to measure the recipient's balance before and after the transfer. Use this observed difference as the actual transferred amount for any further logic or calculations. For example:
```solidity
function transferTokenAndPerformAction(address token, address from, address to, uint256 amount) public {
    uint256 balanceBefore = IERC20(token).balanceOf(to);

    // Perform the token transfer
    IERC20(token).transferFrom(from, to, amount);

    uint256 balanceAfter = IERC20(token).balanceOf(to);
    uint256 actualReceived = balanceAfter - balanceBefore;

    // Proceed with logic using actualReceived instead of the initial amount
    require(actualReceived >= minimumRequiredAmount, "Received amount is less than required");

    // Further logic here
}
```

### Consider implementing two-step procedure for updating protocol addresses
Implementing a two-step procedure for updating protocol addresses adds an extra layer of security. In such a system, the first step initiates the change, and the second step, after a predefined delay, confirms and finalizes it. This delay allows stakeholders or monitoring tools to observe and react to unintended or malicious changes. If an unauthorized change is detected, corrective actions can be taken before the change is finalized. To achieve this, introduce a "proposed address" state variable and a "delay period". Upon an update request, set the "proposed address". After the delay, if not contested, the main protocol address can be updated.

```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

52:        feeRecipient = feeRecipient_;	// @audit-issue
```
[52](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L52-L52), 


```solidity
Path: ./contracts/registry/VersionRegistry.sol

53:        deployments[versionHash] = deployer;	// @audit-issue
```
[53](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L53-L53), 


```solidity
Path: ./contracts/mixins/ComponentRegistry.sol

38:        rToken = val;	// @audit-issue

46:        stRSR = val;	// @audit-issue

54:        assetRegistry = val;	// @audit-issue

62:        basketHandler = val;	// @audit-issue

70:        backingManager = val;	// @audit-issue

78:        distributor = val;	// @audit-issue

86:        rsrTrader = val;	// @audit-issue

94:        rTokenTrader = val;	// @audit-issue

102:        furnace = val;	// @audit-issue

110:        broker = val;	// @audit-issue
```
[38](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L38-L38), [46](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L46-L46), [54](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L54-L54), [62](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L62-L62), [70](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L70-L70), [78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L78-L78), [86](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L86-L86), [94](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L94-L94), [102](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L102-L102), [110](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L110-L110), 


```solidity
Path: ./contracts/p1/Main.sol

44:        rsr = rsr_;	// @audit-issue

65:        versionRegistry = VersionRegistry(versionRegistry_);	// @audit-issue

74:        assetPluginRegistry = AssetPluginRegistry(registry_);	// @audit-issue

83:        daoFeeRegistry = DAOFeeRegistry(feeRegistry_);	// @audit-issue
```
[44](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L44-L44), [65](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L65-L65), [74](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L74-L74), [83](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L83-L83), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

36:        tokenToBuy = tokenToBuy_;	// @audit-issue
```
[36](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L36-L36), 


```solidity
Path: ./contracts/p1/Furnace.sol

35:        rToken = main_.rToken();	// @audit-issue
```
[35](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L35-L35), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

44:        basketHandler = main_.basketHandler();	// @audit-issue

45:        backingManager = main_.backingManager();	// @audit-issue

240:        assets[erc20] = asset;	// @audit-issue
```
[44](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L44-L44), [45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L45-L45), [240](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L240-L240), 


```solidity
Path: ./contracts/p1/StRSR.sol

190:        assetRegistry = main_.assetRegistry();	// @audit-issue

191:        backingManager = main_.backingManager();	// @audit-issue

192:        basketHandler = main_.basketHandler();	// @audit-issue

196:        rsrRewardsAtLastPayout = main_.rsr().balanceOf(address(this));	// @audit-issue
```
[190](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L190-L190), [191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L191-L191), [192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L192-L192), [196](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L196-L196), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

116:        assetRegistry = main_.assetRegistry();	// @audit-issue

117:        backingManager = main_.backingManager();	// @audit-issue

118:        rsr = main_.rsr();	// @audit-issue

119:        rToken = main_.rToken();	// @audit-issue

120:        stRSR = main_.stRSR();	// @audit-issue
```
[116](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L116-L116), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L117-L117), [118](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L118-L118), [119](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L119-L119), [120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L120-L120), 


```solidity
Path: ./contracts/p1/RToken.sol

76:        assetRegistry = main_.assetRegistry();	// @audit-issue

77:        basketHandler = main_.basketHandler();	// @audit-issue

78:        backingManager = main_.backingManager();	// @audit-issue

79:        furnace = main_.furnace();	// @audit-issue
```
[76](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L76-L76), [77](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L77-L77), [78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L78-L78), [79](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L79-L79), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

223:        _delegates[delegator] = delegatee;	// @audit-issue
```
[223](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L223-L223), 


```solidity
Path: ./contracts/p1/Broker.sol

95:        batchTradeImplementation = batchTradeImplementation_;	// @audit-issue

96:        dutchTradeImplementation = dutchTradeImplementation_;	// @audit-issue

182:        gnosis = newGnosis;	// @audit-issue

193:        batchTradeImplementation = newTradeImplementation;	// @audit-issue

215:        dutchTradeImplementation = newTradeImplementation;	// @audit-issue
```
[95](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L95-L95), [96](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L96-L96), [182](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L182-L182), [193](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L193-L193), [215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L215-L215), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

53:        broker = main_.broker();	// @audit-issue
```
[53](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L53-L53), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

36:        main = main_;	// @audit-issue
```
[36](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L36-L36), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

103:        broker = broker_;	// @audit-issue

104:        origin = origin_;	// @audit-issue

105:        gnosis = gnosis_;	// @audit-issue
```
[103](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L103-L103), [104](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L104-L104), [105](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L105-L105), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

172:        origin = origin_;	// @audit-issue

173:        sell = sell_.erc20();	// @audit-issue

174:        buy = buy_.erc20();	// @audit-issue
```
[172](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L172-L172), [173](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L173-L173), [174](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L174-L174), 


#### Recommendation

Introduce two state variables in your contract: one to hold the proposed new address (`address public proposedAddress`) and another to timestamp the proposal (`uint public addressChangeInitiated`). Implement two functions: one to propose a new address, recording the current timestamp, and another to finalize the address change after the delay period has elapsed.

### Centralization risk for privileged functions
Contracts with privileged functions need owner/admin to be trusted not to perform malicious updates or drain funds. This may also cause a single point failure.


```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

44:    function setFeeRecipient(address feeRecipient_) external onlyOwner {	// @audit-issue

56:    function setDefaultFeeNumerator(uint256 feeNumerator_) external onlyOwner {	// @audit-issue

66:    function setRTokenFeeNumerator(address rToken, uint256 feeNumerator_) external onlyOwner {	// @audit-issue

76:    function resetRTokenFee(address rToken) external onlyOwner {	// @audit-issue
```
[44](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L44-L44), [56](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L56-L56), [66](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L66-L66), [76](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L76-L76), 


```solidity
Path: ./contracts/mixins/Auth.sol

68:    function __Auth_init(uint48 shortFreeze_, uint48 longFreeze_) internal onlyInitializing {	// @audit-issue

87:    function grantRole(bytes32 role, address account)
88:        public
89:        override(AccessControlUpgradeable, IAccessControlUpgradeable)
90:        onlyRole(getRoleAdmin(role))	// @audit-issue

124:    function freezeShort() external onlyRole(SHORT_FREEZER) {	// @audit-issue

139:    function freezeLong() external onlyRole(LONG_FREEZER) {	// @audit-issue

152:    function freezeForever() external onlyRole(OWNER) {	// @audit-issue

161:    function unfreeze() external onlyRole(OWNER) {	// @audit-issue

169:    function pauseTrading() external onlyRole(PAUSER) {	// @audit-issue

176:    function unpauseTrading() external onlyRole(PAUSER) {	// @audit-issue

183:    function pauseIssuance() external onlyRole(PAUSER) {	// @audit-issue

190:    function unpauseIssuance() external onlyRole(PAUSER) {	// @audit-issue

198:    function setShortFreeze(uint48 shortFreeze_) public onlyRole(OWNER) {	// @audit-issue

205:    function setLongFreeze(uint48 longFreeze_) public onlyRole(OWNER) {	// @audit-issue
```
[68](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L68-L68), [90](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L87-L90), [124](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L124-L124), [139](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L139-L139), [152](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L152-L152), [161](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L161-L161), [169](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L169-L169), [176](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L176-L176), [183](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L183-L183), [190](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L190-L190), [198](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L198-L198), [205](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L205-L205), 


```solidity
Path: ./contracts/mixins/ComponentRegistry.sol

18:    function __ComponentRegistry_init(Components memory components_) internal onlyInitializing {	// @audit-issue
```
[18](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L18-L18), 


```solidity
Path: ./contracts/p1/Main.sol

61:    function setVersionRegistry(VersionRegistry versionRegistry_) external onlyRole(OWNER) {	// @audit-issue

70:    function setAssetPluginRegistry(AssetPluginRegistry registry_) external onlyRole(OWNER) {	// @audit-issue

79:    function setDAOFeeRegistry(DAOFeeRegistry feeRegistry_) external onlyRole(OWNER) {	// @audit-issue

99:    function upgradeMainTo(bytes32 versionHash) external onlyRole(OWNER) {	// @audit-issue

111:    function upgradeRTokenTo(
112:        bytes32 versionHash,
113:        bool preValidation,
114:        bool postValidation
115:    ) external onlyRole(OWNER) {	// @audit-issue
```
[61](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L61-L61), [70](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L70-L70), [79](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L79-L79), [99](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L99-L99), [115](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L111-L115), 


```solidity
Path: ./contracts/p1/Broker.sol

186:    function setBatchTradeImplementation(ITrade newTradeImplementation) public onlyMain {	// @audit-issue

208:    function setDutchTradeImplementation(ITrade newTradeImplementation) public onlyMain {	// @audit-issue
```
[186](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L186-L186), [208](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L208-L208), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

48:    function __Trading_init(
49:        IMain main_,
50:        uint192 maxTradeSlippage_,
51:        uint192 minTradeVolume_
52:    ) internal onlyInitializing {	// @audit-issue
```
[52](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L48-L52), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

33:    function __Component_init(IMain main_) internal onlyInitializing {	// @audit-issue

67:    function _authorizeUpgrade(address newImplementation) internal view override onlyMain {}	// @audit-issue
```
[33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L33-L33), [67](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L67-L67), 


#### Recommendation

To mitigate centralization risks associated with privileged functions, consider implementing a multi-signature or decentralized governance mechanism. Instead of relying solely on a single owner/administrator, distribute control and decision-making authority among multiple parties or stakeholders. This approach enhances security, reduces the risk of malicious actions by a single entity, and prevents single points of failure. Explore governance solutions and smart contract frameworks that support decentralized control and decision-making to enhance the trustworthiness and resilience of your contract.

### Missing checks for `address(0)` in constructor/initializers
In Solidity, the Ethereum address `0x0000000000000000000000000000000000000000` is known as the "zero address". This address has significance because it's the default value for uninitialized address variables and is often used to represent an invalid or non-existent address. The "
Missing zero address control" issue arises when a Solidity smart contract does not properly check or prevent interactions with the zero address, leading to unintended behavior.
For instance, a contract might allow tokens to be sent to the zero address without any checks, which essentially burns those tokens as they become irretrievable. While sometimes this is intentional, without proper control or checks, accidental transfers could occur.    
        

```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

41:        feeRecipient = _feeRecipient;	// @audit-issue
```
[41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L41-L41), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

27:        versionRegistry = VersionRegistry(_versionRegistry);	// @audit-issue
```
[27](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L27-L27), 


```solidity
Path: ./contracts/p1/Deployer.sol

71:        _implementations = implementations_;	// @audit-issue
```
[71](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L71-L71), 


#### Recommendation

It is strongly recommended to implement checks to prevent the zero address from being set during the initialization of contracts. This can be achieved by adding require statements that ensure address parameters are not the zero address. 

### Missing checks for `address(0)`  when updating state variables
In Solidity, the Ethereum address `0x0000000000000000000000000000000000000000` is known as the "zero address". This address has significance because it's the default value for uninitialized address variables and is often used to represent an invalid or non-existent address. The "
Missing zero address control" issue arises when a Solidity smart contract does not properly check or prevent interactions with the zero address, leading to unintended behavior.
For instance, a contract might allow tokens to be sent to the zero address without any checks, which essentially burns those tokens as they become irretrievable. While sometimes this is intentional, without proper control or checks, accidental transfers could occur.    
        

```solidity
Path: ./contracts/p1/RevenueTrader.sol

55:        trade = super.settleTrade(sell); // nonReentrant	// @audit-issue
```
[55](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L55-L55), 


```solidity
Path: ./contracts/p1/Furnace.sol

35:        rToken = main_.rToken();	// @audit-issue
```
[35](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L35-L35), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

44:        basketHandler = main_.basketHandler();	// @audit-issue

45:        backingManager = main_.backingManager();	// @audit-issue

79:        return _register(asset);	// @audit-issue

99:        swapped = _registerIgnoringCollisions(asset);	// @audit-issue
```
[44](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L44-L44), [45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L45-L45), [79](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L79-L79), [99](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L99-L99), 


```solidity
Path: ./contracts/p1/BackingManager.sol

70:        require(assetRegistry.isRegistered(erc20), "erc20 unregistered");	// @audit-issue

87:        trade = super.settleTrade(sell); // nonReentrant	// @audit-issue
```
[70](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L70-L70), [87](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L87-L87), 


```solidity
Path: ./contracts/p1/StRSR.sol

190:        assetRegistry = main_.assetRegistry();	// @audit-issue

191:        backingManager = main_.backingManager();	// @audit-issue

192:        basketHandler = main_.basketHandler();	// @audit-issue

196:        rsrRewardsAtLastPayout = main_.rsr().balanceOf(address(this));	// @audit-issue
```
[190](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L190-L190), [191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L191-L191), [192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L192-L192), [196](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L196-L196), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

116:        assetRegistry = main_.assetRegistry();	// @audit-issue

117:        backingManager = main_.backingManager();	// @audit-issue

118:        rsr = main_.rsr();	// @audit-issue

119:        rToken = main_.rToken();	// @audit-issue

120:        stRSR = main_.stRSR();	// @audit-issue
```
[116](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L116-L116), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L117-L117), [118](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L118-L118), [119](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L119-L119), [120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L120-L120), 


```solidity
Path: ./contracts/p1/RToken.sol

76:        assetRegistry = main_.assetRegistry();	// @audit-issue

77:        basketHandler = main_.basketHandler();	// @audit-issue

78:        backingManager = main_.backingManager();	// @audit-issue

79:        furnace = main_.furnace();	// @audit-issue

419:        require(assetRegistry.isRegistered(erc20), "erc20 unregistered");	// @audit-issue
```
[76](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L76-L76), [77](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L77-L77), [78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L78-L78), [79](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L79-L79), [419](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L419-L419), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

223:        _delegates[delegator] = delegatee;	// @audit-issue
```
[223](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L223-L223), 


```solidity
Path: ./contracts/p1/Broker.sol

84:        setGnosis(gnosis_);	// @audit-issue
```
[84](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L84-L84), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

53:        broker = main_.broker();	// @audit-issue

76:        RewardableLibP1.claimRewardsSingle(main.assetRegistry().toAsset(erc20));	// @audit-issue
```
[53](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L53-L53), [76](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L76-L76), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

103:        broker = broker_;	// @audit-issue

104:        origin = origin_;	// @audit-issue

105:        gnosis = gnosis_;	// @audit-issue
```
[103](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L103-L103), [104](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L104-L104), [105](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L105-L105), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

172:        origin = origin_;	// @audit-issue

173:        sell = sell_.erc20();	// @audit-issue

174:        buy = buy_.erc20();	// @audit-issue
```
[172](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L172-L172), [173](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L173-L173), [174](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L174-L174), 


#### Recommendation

It is strongly recommended to implement checks to prevent the zero address from being set during the initialization of contracts. This can be achieved by adding require statements that ensure address parameters are not the zero address. 

### The `constructor`/`initialize` function lacks parameter validation.
Constructors and initialization functions play a critical role in contracts by setting important initial states when the contract is first deployed before the system starts. The parameters passed to the constructor and initialization functions directly affect the behavior of the contract / protocol. If incorrect parameters are provided, the system may fail to run, behave abnormally, be unstable, or lack security. Therefore, it's crucial to carefully check each parameter in the constructor and initialization functions. If an exception is found, the transaction should be rolled back.

```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

40:        roleRegistry = _roleRegistry;	// @audit-issue
```
[40](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L40-L40), 


```solidity
Path: ./contracts/registry/VersionRegistry.sol

32:        roleRegistry = _roleRegistry;	// @audit-issue
```
[32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L32-L32), 


```solidity
Path: ./contracts/p1/Deployer.sol

69:        gnosis = gnosis_;	// @audit-issue

70:        rsrAsset = rsrAsset_;	// @audit-issue
```
[69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L69-L69), [70](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L70-L70), 


#### Recommendation

Incorporate comprehensive parameter validation in your contract's constructor and initialization functions. This should include checks for value ranges, address validity, and any other condition that defines a parameter as valid. Use `require` statements for validation, providing clear error messages for each condition. If any validation fails, the `require` statement will revert the transaction, preventing the contract from being deployed or initialized with invalid parameters.
Example:
```solidity
contract MyContract {
    address public owner;
    uint256 public someValue;

    constructor(address _owner, uint256 _someValue) {
        require(_owner != address(0), "Owner cannot be the zero address");
        require(_someValue > 0 && _someValue < 100, "SomeValue must be between 1 and 99");

        owner = _owner;
        someValue = _someValue;
    }
}

// For contracts using the proxy pattern and requiring initialization functions:
function initialize(address _owner, uint256 _someValue) public {
    require(_owner != address(0), "Owner cannot be the zero address");
    require(_someValue > 0 && _someValue < 100, "SomeValue must be between 1 and 99");

    owner = _owner;
    someValue = _someValue;
}
```


### Division before multiplication
When dealing with arithmetic operations, it's crucial to prioritize multiplication before division. This practice helps to reduce the risk of rounding errors and increases the precision of the calculations. By rearranging your mathematical operations to perform multiplications first, you can enhance the accuracy and efficiency of your contract's computational processes.

```solidity
Path: ./contracts/libraries/Fixed.sol

324:            if (y & 1 == 1) result = (result * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;	// @audit-issue

327:            x = (x * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;	// @audit-issue
```
[324](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L324-L324), [327](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L327-L327), 


#### Recommendation

Review your smart contract code to identify instances where division precedes multiplication. Refactor these calculations by rearranging the order, ensuring multiplication is performed first. This adjustment can significantly improve the precision of your results, especially in environments where every decimal point matters, like financial or token-related contracts.

### Gas griefing/theft is possible on an unsafe external call
A low-level call will copy any amount of bytes to local memory. When bytes are copied from returndata to memory, the memory expansion cost is paid.

This means that when using a standard solidity call, the callee can 'returnbomb' the caller, imposing an arbitrary gas cost.

Because this gas is paid by the caller and in the caller's context, it can cause the caller to run out of gas and halt execution.

Consider replacing all unsafe `call` with `excessivelySafeCall` from this [repository](https://github.com/nomad-xyz/ExcessivelySafeCall).


```solidity
Path: ./contracts/p1/Main.sol

158:        (bool success, ) = proxy.call(	// @audit-issue
```
[158](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L158-L158), 


#### Recommendation

Mitigate the risk of gas griefing by replacing direct low-level calls (`call`, `delegatecall`, etc.) with `excessivelySafeCall`, a utility that limits the amount of gas used for copying return data, thereby preventing return bombing. The `excessivelySafeCall` function, available in certain security-focused repositories such as [nomad-xyz/ExcessivelySafeCall](https://github.com/nomad-xyz/ExcessivelySafeCall), implements additional checks to ensure that the callee cannot impose arbitrary gas costs on the caller. To incorporate this into your contract:

1. Review your contract for external calls to untrusted contracts.
2. Import or implement an `excessivelySafeCall` utility in your contract.
3. Replace unsafe external calls with `excessivelySafeCall`, specifying limits on return data size as appropriate for your application.

Example usage:
```solidity
// Assuming excessivelySafeCall is available in your contract
(bool success, ) = target.excessivelySafeCall(gasLimit, callData, maxReturnSize);
require(success, "External call failed");

This approach not only protects against gas griefing attacks but also enhances the overall security posture of your contract by ensuring that external interactions do not lead to unexpected execution halts or excessive gas consumption. Always perform thorough testing after integrating `excessivelySafeCall` to ensure compatibility and intended functionality.

### Unsafe downcast may overflow
When a type is downcast to a smaller type, the higher order bits are discarded, resulting in the application of a modulo operation to the original value.

If the downcasted value is large enough, this may result in an overflow that will not revert.


```solidity
Path: ./contracts/libraries/Fixed.sol

245:        return _safeWrap(uint256(x) - uint256(y * FIX_SCALE));	// @audit-issue: Variable `y` is type `uint48` and it is downcasted to `uint256`

264:        return _safeWrap(_divrnd(uint256(x) * uint256(y), FIX_SCALE, rounding));	// @audit-issue: Variable `y` is type `uint48` and it is downcasted to `uint256`

425:        return mulDiv256(uint256(x), uint256(y), FIX_SCALE_SQ);	// @audit-issue: Variable `y` is type `uint48` and it is downcasted to `uint256`

436:        return mulDiv256(uint256(x), uint256(y), FIX_SCALE_SQ, rounding);	// @audit-issue: Variable `y` is type `uint48` and it is downcasted to `uint256`

537:            return uint192(shiftDelta / FIX_ONE); // {D18} = {D36} / {D18}	// @audit-issue: Variable `shiftDelta` is type `uint256` and it is downcasted to `uint192`

554:        return uint192(raw); // don't need _safeWrap	// @audit-issue: Variable `raw` is type `uint256` and it is downcasted to `uint192`

605:        return uint192(result_256);	// @audit-issue: Variable `result_256` is type `uint256` and it is downcasted to `uint192`
```
[245](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L245-L245), [264](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L264-L264), [425](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L425-L425), [436](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L436-L436), [537](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L537-L537), [554](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L554-L554), [605](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L605-L605), 


```solidity
Path: ./contracts/p1/StRSR.sol

448:            stakeRate = uint192((FIX_ONE_256 * totalStakes + (stakeRSR - 1)) / stakeRSR);	// @audit-issue: Variable `totalStakes` is type `uint256` and it is downcasted to `uint192`

448:            stakeRate = uint192((FIX_ONE_256 * totalStakes + (stakeRSR - 1)) / stakeRSR);	// @audit-issue: Variable `stakeRSR` is type `uint256` and it is downcasted to `uint192`

463:            draftRate = uint192((FIX_ONE_256 * totalDrafts + (draftRSR - 1)) / draftRSR);	// @audit-issue: Variable `totalDrafts` is type `uint256` and it is downcasted to `uint192`

463:            draftRate = uint192((FIX_ONE_256 * totalDrafts + (draftRSR - 1)) / draftRSR);	// @audit-issue: Variable `draftRSR` is type `uint256` and it is downcasted to `uint192`

623:            : uint192((totalStakes * FIX_ONE_256 + (stakeRSR - 1)) / stakeRSR);	// @audit-issue: Variable `totalStakes` is type `uint256` and it is downcasted to `uint192`

623:            : uint192((totalStakes * FIX_ONE_256 + (stakeRSR - 1)) / stakeRSR);	// @audit-issue: Variable `stakeRSR` is type `uint256` and it is downcasted to `uint192`

663:        queue.push(CumulativeDraft(uint176(oldDrafts + draftAmount), availableAt));	// @audit-issue: Variable `oldDrafts` is type `uint192` and it is downcasted to `uint176`

663:        queue.push(CumulativeDraft(uint176(oldDrafts + draftAmount), availableAt));	// @audit-issue: Variable `draftAmount` is type `uint192` and it is downcasted to `uint176`

663:        queue.push(CumulativeDraft(uint176(oldDrafts + draftAmount), availableAt));	// @audit-issue: Variable `draftAmount` is type `uint256` and it is downcasted to `uint176`
```
[448](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L448-L448), [448](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L448-L448), [463](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L463-L463), [463](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L463-L463), [623](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L623-L623), [623](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L623-L623), [663](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L663-L663), [663](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L663-L663), [663](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L663-L663), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

456:        low = low256 >= FIX_MAX ? FIX_MAX : uint192(low256);	// @audit-issue: Variable `low256` is type `uint256` and it is downcasted to `uint192`

457:        high = high256 >= FIX_MAX ? FIX_MAX : uint192(high256);	// @audit-issue: Variable `high256` is type `uint256` and it is downcasted to `uint192`
```
[456](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L456-L456), [457](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L457-L457), 


#### Recommendation

When downcasting numbers to `addresses` in Solidity, be cautious of potential collisions. Downcasting truncates the upper bytes of the number, which can lead to multiple values mapping to the same `address`. To avoid collisions, consider using a more robust mapping strategy or additional checks to ensure unique mappings.

### `safeApprove()` is deprecated
The usage of deprecated library functions should be discouraged, as safeApprove is also potentially [dangerous](https://github.com/OpenZeppelin/openzeppelin-contracts/issues/2219).

```solidity
Path: ./contracts/p1/RevenueTrader.sol

191:        tokenToBuy.safeApprove(address(distributor), 0);	// @audit-issue

192:        tokenToBuy.safeApprove(address(distributor), bal);	// @audit-issue
```
[191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L191-L191), [192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L192-L192), 


```solidity
Path: ./contracts/p1/BackingManager.sol

72:        IERC20(address(erc20)).safeApprove(address(rToken), 0);	// @audit-issue

73:        IERC20(address(erc20)).safeApprove(address(rToken), type(uint256).max);	// @audit-issue
```
[72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L72-L72), [73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L73-L73), 


#### Recommendation

Given its deprecation and associated risks, replace `safeApprove()` with the standard `approve()` method, incorporating best practices to manage allowances safely:
1. Before increasing an allowance, reduce it to zero to mitigate the risk of double-spending in transactions that are mined in quick succession.
2. Use events or transaction return values to verify the success of an `approve()` call.
3. Stay informed about ERC20 token standards and security practices to ensure your contracts interact with tokens safely and efficiently.


### `decimals()` is not a part of the `ERC-20` standard
The `decimals()` function is not a part of the [ERC-20](https://eips.ethereum.org/EIPS/eip-20) standard, and was added later as an [optional extension](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/IERC20Metadata.sol). As such, some valid `ERC20` tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function.


```solidity
Path: ./contracts/p1/BasketHandler.sol

508:                int8(IERC20Metadata(address(basket.erc20s[i])).decimals()),	// @audit-issue
```
[508](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L508-L508), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

96:        sellAmount = shiftl_toFix(req.sellAmount, -int8(sell.decimals()), FLOOR); // {sellTok}	// @audit-issue

127:            DEFAULT_MIN_BID.shiftl_toUint(int8(buy.decimals()))	// @audit-issue
```
[96](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L96-L96), [127](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L127-L127), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

133:        return sellAmount.shiftl_toUint(int8(sell.decimals()));	// @audit-issue

177:        sellAmount = shiftl_toFix(sellAmount_, -int8(sell.decimals()), FLOOR); // {sellTok}	// @audit-issue

367:        return sellAmount.mul(price, CEIL).shiftl_toUint(int8(buy.decimals()), CEIL);	// @audit-issue
```
[133](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L133-L133), [177](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L177-L177), [367](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L367-L367), 


#### Recommendation

When working with ERC-20 tokens in your Solidity code, be aware that the `decimals()` function is not a part of the ERC-20 standard and is considered an optional extension. Not all valid ERC-20 tokens implement this interface, so avoid blindly casting all tokens to this interface and calling the `decimals()` function. Instead, check the token's documentation or contract to determine whether it supports this extension before using it.

### Int casting `block.timestamp` can reduce the lifespan of a contract
In Solidity, `block.timestamp` returns the Unix timestamp of the current block, which is a continuously increasing value. Casting `block.timestamp` to a smaller integer type, like `uint32`, can limit the maximum value it can hold. As time progresses and the Unix timestamp exceeds this maximum value, the casted value will overflow, leading to incorrect and potentially harmful behavior in the contract. This issue can significantly reduce the functional lifespan of a contract, as it becomes unreliable and potentially insecure once the timestamp exceeds the capacity of the casted integer type.

```solidity
Path: ./contracts/mixins/Auth.sol

127:        freezeUntil(uint48(block.timestamp) + shortFreeze);	// @audit-issue

144:        freezeUntil(uint48(block.timestamp) + longFreeze);	// @audit-issue

162:        emit UnfreezeAtSet(unfreezeAt, uint48(block.timestamp));	// @audit-issue

163:        unfreezeAt = uint48(block.timestamp);	// @audit-issue
```
[127](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L127-L127), [144](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L144-L144), [162](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L162-L162), [163](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L163-L163), 


```solidity
Path: ./contracts/libraries/Throttle.sol

53:            throttle.lastTimestamp = uint48(block.timestamp);	// @audit-issue

74:        uint48 delta = uint48(block.timestamp) - throttle.lastTimestamp; // {seconds}	// @audit-issue
```
[53](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L53-L53), [74](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L74-L74), 


```solidity
Path: ./contracts/p1/Furnace.sol

37:        lastPayout = uint48(block.timestamp);	// @audit-issue

66:        if (uint48(block.timestamp) < uint64(lastPayout + 1)) return;	// @audit-issue
```
[37](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L37-L37), [66](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L66-L66), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

68:        lastRefresh = uint48(block.timestamp); // safer to do this at end than start, actually	// @audit-issue
```
[68](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L68-L68), 


```solidity
Path: ./contracts/p1/StRSR.sol

195:        payoutLastPaid = uint48(block.timestamp);	// @audit-issue

595:        uint48 numPeriods = uint48(block.timestamp) - payoutLastPaid;	// @audit-issue

658:        availableAt = uint64(block.timestamp) + unstakingDelay;	// @audit-issue

713:            lastWithdrawRefresh = uint48(block.timestamp);	// @audit-issue
```
[195](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L195-L195), [595](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L595-L595), [658](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L658-L658), [713](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L713-L713), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

128:        lastStatusTimestamp = uint48(block.timestamp);	// @audit-issue

183:            lastStatusTimestamp = uint48(block.timestamp);	// @audit-issue

669:            timestamp = uint48(block.timestamp);	// @audit-issue
```
[128](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L128-L128), [183](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L183-L183), [669](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L669-L669), 


```solidity
Path: ./contracts/p1/RToken.sol

85:        issuanceThrottle.lastTimestamp = uint48(block.timestamp);	// @audit-issue

86:        redemptionThrottle.lastTimestamp = uint48(block.timestamp);	// @audit-issue
```
[85](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L85-L85), [86](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L86-L86), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

106:        endTime = uint48(block.timestamp) + batchAuctionLength;	// @audit-issue
```
[106](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L106-L106), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

180:        uint48 _startTime = uint48(block.timestamp) + 1; // cannot fulfill in current block	// @audit-issue

204:        uint192 price = _price(uint48(block.timestamp)); // enforces auction ongoing	// @audit-issue

240:        uint192 price = _price(uint48(block.timestamp)); // enforces auction ongoing	// @audit-issue
```
[180](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L180-L180), [204](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L204-L204), [240](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L240-L240), 


#### Recommendation

Avoid casting `block.timestamp` to smaller integer types in your Solidity contracts. Use `uint256` to store timestamp values to accommodate the increasing nature of Unix timestamps and ensure the contract remains functional in the long term. Regularly review and update your contracts to remove any unnecessary casting of `block.timestamp` that could lead to overflows and reduce the contract's lifespan. By doing so, you maintain the contract's reliability and integrity well into the future.

### Unsafe `uint` to `int` conversion
The `int` type in Solidity uses the [two's complement system](https://en.wikipedia.org/wiki/Two%27s_complement), so it is possible to accidentally overflow a very large `uint` to an `int`, even if they share the same number of bytes (e.g. a `uint256 number > type(uint128).max` will overflow a `int256` cast).

Consider using the [SafeCast](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol) library to prevent any overflows.


```solidity
Path: ./contracts/p1/RToken.sol

121:        issuanceThrottle.useAvailable(supply, int256(amount)); // reverts on over-issuance	// @audit-issue: Variable `amount` is type `uint256` and it is casted to `int256`

122:        redemptionThrottle.useAvailable(supply, -int256(amount)); // shouldn't revert	// @audit-issue: Variable `amount` is type `uint256` and it is casted to `int256`

199:        issuanceThrottle.useAvailable(supply, -int256(amount));	// @audit-issue: Variable `amount` is type `uint256` and it is casted to `int256`

200:        redemptionThrottle.useAvailable(supply, int256(amount)); // reverts on over-redemption	// @audit-issue: Variable `amount` is type `uint256` and it is casted to `int256`

277:        issuanceThrottle.useAvailable(supply, -int256(amount));	// @audit-issue: Variable `amount` is type `uint256` and it is casted to `int256`

278:        redemptionThrottle.useAvailable(supply, int256(amount)); // reverts on over-redemption	// @audit-issue: Variable `amount` is type `uint256` and it is casted to `int256`
```
[121](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L121-L121), [122](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L122-L122), [199](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L199-L199), [200](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L200-L200), [277](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L277-L277), [278](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L278-L278), 


#### Recommendation

To prevent potential overflow issues when converting from `uint` to `int` in Solidity, consider using the SafeCast library. This library helps ensure safe type conversions and avoids unexpected behavior, especially when dealing with very large `uint` values that could overflow when cast to `int`.

### Functions calling contracts/addresses with transfer hooks are missing reentrancy guards
Even if the function follows the best practice of check-effects-interaction, not using a reentrancy guard when there may be transfer hooks will open the users of this protocol up to [read-only reentrancies](https://chainsecurity.com/curve-lp-oracle-manipulation-post-mortem/) with no way to protect against it, except by block-listing the whole protocol.

```solidity
Path: ./contracts/p1/Distributor.sol

170:            IERC20Upgradeable(address(erc20)).safeTransferFrom(	// @audit-issue

183:                    IERC20Upgradeable(address(erc20)).safeTransferFrom(	// @audit-issue
```
[170](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L170-L170), [183](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L183-L183), 


```solidity
Path: ./contracts/p1/StRSR.sol

237:        IERC20Upgradeable(address(rsr)).safeTransferFrom(caller, address(this), rsrAmount);	// @audit-issue

337:        IERC20Upgradeable(address(rsr)).safeTransfer(account, rsrAmount);	// @audit-issue

477:        IERC20Upgradeable(address(rsr)).safeTransfer(caller, seizedRSR);	// @audit-issue
```
[237](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L237-L237), [337](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L337-L337), [477](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L477-L477), 


```solidity
Path: ./contracts/p1/RToken.sol

149:            IERC20Upgradeable(erc20s[i]).safeTransferFrom(	// @audit-issue

219:            IERC20Upgradeable(erc20s[i]).safeTransferFrom(	// @audit-issue

327:                IERC20Upgradeable(erc20s[i]).safeTransferFrom(	// @audit-issue

420:        IERC20Upgradeable(address(erc20)).safeTransfer(	// @audit-issue
```
[149](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L149-L149), [219](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L219-L219), [327](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L327-L327), [420](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L420-L420), 


```solidity
Path: ./contracts/p1/Broker.sol

259:        IERC20Upgradeable(address(req.sell.erc20())).safeTransferFrom(	// @audit-issue

287:        IERC20Upgradeable(address(req.sell.erc20())).safeTransferFrom(	// @audit-issue
```
[259](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L259-L259), [287](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L287-L287), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

205:        if (sellBal != 0) IERC20Upgradeable(address(sell)).safeTransfer(origin, sellBal);	// @audit-issue

206:        if (boughtAmt != 0) IERC20Upgradeable(address(buy)).safeTransfer(origin, boughtAmt);	// @audit-issue

227:        IERC20Upgradeable(address(erc20)).safeTransfer(origin, erc20.balanceOf(address(this)));	// @audit-issue
```
[205](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L205-L205), [206](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L206-L206), [227](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L227-L227), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

219:        buy.safeTransferFrom(msg.sender, address(this), amountIn);	// @audit-issue

255:        sell.safeTransfer(bidder, lot()); // {qSellTok}	// @audit-issue

286:            sell.safeTransfer(bidder, soldAmt); // {qSellTok}	// @audit-issue

291:        buy.safeTransfer(address(origin), boughtAmt); // {qBuyTok}	// @audit-issue

292:        sell.safeTransfer(address(origin), sell.balanceOf(address(this))); // {qSellTok}	// @audit-issue

300:        erc20.safeTransfer(address(origin), erc20.balanceOf(address(this)));	// @audit-issue
```
[219](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L219-L219), [255](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L255-L255), [286](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L286-L286), [291](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L291-L291), [292](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L292-L292), [300](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L300-L300), 


#### Recommendation

To ensure the security of your protocol and protect users from potential vulnerabilities, consider implementing reentrancy guards in functions that call contracts or addresses with transfer hooks. Even if your function follows the check-effects-interaction pattern, using reentrancy guards is essential to prevent read-only reentrancy attacks, as demonstrated in incidents like the [Curve LP Oracle Manipulation](https://chainsecurity.com/curve-lp-oracle-manipulation-post-mortem/). Implementing reentrancy guards adds an extra layer of protection and safeguards your protocol against such attacks.

### Consider using OpenZeppelin’s `SafeCast` library to prevent unexpected overflows when casting from various type int/uint values
In Solidity, when casting from `int` to `uint` or vice versa, there is a risk of unexpected overflow or underflow, especially when dealing with large values. To mitigate this risk and ensure safe type conversions, you can consider using OpenZeppelin’s `SafeCast` library. This library provides functions that check for overflow/underflow conditions before performing the cast, helping you prevent potential issues related to type conversion in your smart contracts.

```solidity
Path: ./contracts/mixins/Auth.sol

127:        freezeUntil(uint48(block.timestamp) + shortFreeze);	// @audit-issue

144:        freezeUntil(uint48(block.timestamp) + longFreeze);	// @audit-issue

162:        emit UnfreezeAtSet(unfreezeAt, uint48(block.timestamp));	// @audit-issue

163:        unfreezeAt = uint48(block.timestamp);	// @audit-issue
```
[127](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L127-L127), [144](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L144-L144), [162](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L162-L162), [163](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L163-L163), 


```solidity
Path: ./contracts/libraries/Array.sol

24:            if (uint160(address(arr[i])) <= uint160(address(arr[i - 1]))) return false;	// @audit-issue
```
[24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L24-L24), 


```solidity
Path: ./contracts/libraries/Throttle.sol

53:            throttle.lastTimestamp = uint48(block.timestamp);	// @audit-issue

58:            require(uint256(amount) <= available, "supply change throttled");	// @audit-issue

59:            available -= uint256(amount);	// @audit-issue

62:            available += uint256(-amount);	// @audit-issue

74:        uint48 delta = uint48(block.timestamp) - throttle.lastTimestamp; // {seconds}	// @audit-issue
```
[53](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L53-L53), [58](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L58-L58), [59](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L59-L59), [62](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L62-L62), [74](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L74-L74), 


```solidity
Path: ./contracts/libraries/Fixed.sol

191:        return uint136(_divrnd(uint256(x), FIX_SCALE, rounding));	// @audit-issue

216:        uint256 coeff = uint256(10**abs(decimals));	// @audit-issue

245:        return _safeWrap(uint256(x) - uint256(y * FIX_SCALE));	// @audit-issue

264:        return _safeWrap(_divrnd(uint256(x) * uint256(y), FIX_SCALE, rounding));	// @audit-issue

290:        return _safeWrap(_divrnd(uint256(x) * FIX_SCALE, y, rounding));	// @audit-issue

321:        uint256 x = uint256(x_) * FIX_SCALE; // x is D36	// @audit-issue

399:        uint256 coeff = uint256(10**abs(decimals));	// @audit-issue

400:        return decimals >= 0 ? uint256(x * coeff) : uint256(_divrnd(x, coeff, rounding));	// @audit-issue

407:        return mulDiv256(uint256(x), y, FIX_SCALE);	// @audit-issue

418:        return mulDiv256(uint256(x), y, FIX_SCALE, rounding);	// @audit-issue

425:        return mulDiv256(uint256(x), uint256(y), FIX_SCALE_SQ);	// @audit-issue

436:        return mulDiv256(uint256(x), uint256(y), FIX_SCALE_SQ, rounding);	// @audit-issue

509:            uint256 rawDelta = uint256(b) * a; // {D36} = {D18} * {D18}	// @audit-issue

537:            return uint192(shiftDelta / FIX_ONE); // {D18} = {D36} / {D18}	// @audit-issue

552:        uint256 raw = _divrnd(FIX_ONE_256 * a, uint256(b), rounding);	// @audit-issue

554:        return uint192(raw); // don't need _safeWrap	// @audit-issue

579:            uint256 c_256 = uint256(c);	// @audit-issue

605:        return uint192(result_256);	// @audit-issue
```
[191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L191-L191), [216](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L216-L216), [245](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L245-L245), [264](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L264-L264), [290](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L290-L290), [321](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L321-L321), [399](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L399-L399), [400](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L400-L400), [407](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L407-L407), [418](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L418-L418), [425](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L425-L425), [436](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L436-L436), [509](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L509-L509), [537](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L537-L537), [552](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L552-L552), [554](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L554-L554), [579](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L579-L579), [605](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L605-L605), 


```solidity
Path: ./contracts/libraries/String.sol

16:            if ((uint8(bStr[i]) >= 65) && (uint8(bStr[i]) <= 90)) {	// @audit-issue

18:                bLower[i] = bytes1(uint8(bStr[i]) + 32);	// @audit-issue
```
[16](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/String.sol#L16-L16), [18](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/String.sol#L18-L18), 


```solidity
Path: ./contracts/p1/Distributor.sol

221:                revTotals.rsrTotal += uint24(	// @audit-issue

222:                    (feeNumerator * uint256(revTotals.rTokenTotal + revTotals.rsrTotal)) /	// @audit-issue

266:        require(uint256(rTokenTotal) + uint256(rsrTotal) >= MAX_DISTRIBUTION, "totals too low");	// @audit-issue
```
[221](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L221-L221), [222](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L222-L222), [266](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L266-L266), 


```solidity
Path: ./contracts/p1/Furnace.sol

37:        lastPayout = uint48(block.timestamp);	// @audit-issue

66:        if (uint48(block.timestamp) < uint64(lastPayout + 1)) return;	// @audit-issue

69:        uint48 numPeriods = uint48((block.timestamp) - lastPayout);	// @audit-issue
```
[37](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L37-L37), [66](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L66-L66), [69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L69-L69), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

68:        lastRefresh = uint48(block.timestamp); // safer to do this at end than start, actually	// @audit-issue
```
[68](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L68-L68), 


```solidity
Path: ./contracts/p1/BackingManager.sol

246:                uint256 delta = bal.minus(req).shiftl_toUint(int8(asset.erc20Decimals()));	// @audit-issue
```
[246](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L246-L246), 


```solidity
Path: ./contracts/p1/StRSR.sol

195:        payoutLastPaid = uint48(block.timestamp);	// @audit-issue

448:            stakeRate = uint192((FIX_ONE_256 * totalStakes + (stakeRSR - 1)) / stakeRSR);	// @audit-issue

463:            draftRate = uint192((FIX_ONE_256 * totalDrafts + (draftRSR - 1)) / draftRSR);	// @audit-issue

595:        uint48 numPeriods = uint48(block.timestamp) - payoutLastPaid;	// @audit-issue

623:            : uint192((totalStakes * FIX_ONE_256 + (stakeRSR - 1)) / stakeRSR);	// @audit-issue

658:        availableAt = uint64(block.timestamp) + unstakingDelay;	// @audit-issue

663:        queue.push(CumulativeDraft(uint176(oldDrafts + draftAmount), availableAt));	// @audit-issue

713:            lastWithdrawRefresh = uint48(block.timestamp);	// @audit-issue
```
[195](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L195-L195), [448](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L448-L448), [463](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L463-L463), [595](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L595-L595), [623](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L623-L623), [658](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L658-L658), [663](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L663-L663), [713](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L713-L713), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

128:        lastStatusTimestamp = uint48(block.timestamp);	// @audit-issue

183:            lastStatusTimestamp = uint48(block.timestamp);	// @audit-issue

456:        low = low256 >= FIX_MAX ? FIX_MAX : uint192(low256);	// @audit-issue

457:        high = high256 >= FIX_MAX ? FIX_MAX : uint192(high256);	// @audit-issue

508:                int8(IERC20Metadata(address(basket.erc20s[i])).decimals()),	// @audit-issue

599:            .shiftl_toUint(int8(collsAll[i].erc20Decimals()), FLOOR);	// @audit-issue

669:            timestamp = uint48(block.timestamp);	// @audit-issue

723:                .shiftl_toUint(int8(asset.erc20Decimals()), FLOOR);	// @audit-issue
```
[128](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L128-L128), [183](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L183-L183), [456](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L456-L456), [457](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L457-L457), [508](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L508-L508), [599](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L599-L599), [669](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L669-L669), [723](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L723-L723), 


```solidity
Path: ./contracts/p1/RToken.sol

85:        issuanceThrottle.lastTimestamp = uint48(block.timestamp);	// @audit-issue

86:        redemptionThrottle.lastTimestamp = uint48(block.timestamp);	// @audit-issue

121:        issuanceThrottle.useAvailable(supply, int256(amount)); // reverts on over-issuance	// @audit-issue

122:        redemptionThrottle.useAvailable(supply, -int256(amount)); // shouldn't revert	// @audit-issue

199:        issuanceThrottle.useAvailable(supply, -int256(amount));	// @audit-issue

200:        redemptionThrottle.useAvailable(supply, int256(amount)); // reverts on over-redemption	// @audit-issue

277:        issuanceThrottle.useAvailable(supply, -int256(amount));	// @audit-issue

278:        redemptionThrottle.useAvailable(supply, int256(amount)); // reverts on over-redemption	// @audit-issue
```
[85](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L85-L85), [86](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L86-L86), [121](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L121-L121), [122](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L122-L122), [199](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L199-L199), [200](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L200-L200), [277](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L277-L277), [278](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L278-L278), 


```solidity
Path: ./contracts/p1/mixins/TradeLib.sol

83:        req.sellAmount = s.shiftl_toUint(int8(trade.sell.erc20Decimals()), FLOOR);	// @audit-issue

84:        req.minBuyAmount = b.shiftl_toUint(int8(trade.buy.erc20Decimals()), CEIL);	// @audit-issue

166:            amt.shiftl_toUint(int8(asset.erc20Decimals())) > 1;	// @audit-issue
```
[83](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L83-L83), [84](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L84-L84), [166](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L166-L166), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

167:                    deltaTop -= int256(	// @audit-issue

168:                        uint256(low.mulDiv(anchor - ctx.bals[i], buPriceHigh, FLOOR))	// @audit-issue

175:                    deltaTop += int256(	// @audit-issue

176:                        uint256(high.safeMulDiv(ctx.bals[i] - anchor, buPriceLow, CEIL))	// @audit-issue

206:            range.top = ctx.basketsHeld.top - _safeWrap(uint256(-deltaTop));	// @audit-issue

210:            if (uint256(deltaTop) + ctx.basketsHeld.top > FIX_MAX) range.top = FIX_MAX;	// @audit-issue

211:            else range.top = ctx.basketsHeld.top + _safeWrap(uint256(deltaTop));	// @audit-issue
```
[167](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L167-L167), [168](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L168-L168), [175](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L175-L175), [176](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L176-L176), [206](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L206-L206), [210](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L210-L210), [211](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L211-L211), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

96:        sellAmount = shiftl_toFix(req.sellAmount, -int8(sell.decimals()), FLOOR); // {sellTok}	// @audit-issue

106:        endTime = uint48(block.timestamp) + batchAuctionLength;	// @audit-issue

114:        uint96 _sellAmount = uint96(	// @audit-issue

123:        uint96 minBuyAmount = uint96(Math.max(1, req.minBuyAmount)); // Safe downcast; require'd	// @audit-issue

127:            DEFAULT_MIN_BID.shiftl_toUint(int8(buy.decimals()))	// @audit-issue
```
[96](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L96-L96), [106](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L106-L106), [114](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L114-L114), [123](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L123-L123), [127](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L127-L127), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

133:        return sellAmount.shiftl_toUint(int8(sell.decimals()));	// @audit-issue

177:        sellAmount = shiftl_toFix(sellAmount_, -int8(sell.decimals()), FLOOR); // {sellTok}	// @audit-issue

180:        uint48 _startTime = uint48(block.timestamp) + 1; // cannot fulfill in current block	// @audit-issue

204:        uint192 price = _price(uint48(block.timestamp)); // enforces auction ongoing	// @audit-issue

240:        uint192 price = _price(uint48(block.timestamp)); // enforces auction ongoing	// @audit-issue

335:            return bestPrice.mulDiv(ONE_POINT_FIVE, BASE.powu(uint48(exp.toUint(ROUND))), CEIL);	// @audit-issue

367:        return sellAmount.mul(price, CEIL).shiftl_toUint(int8(buy.decimals()), CEIL);	// @audit-issue
```
[133](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L133-L133), [177](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L177-L177), [180](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L180-L180), [204](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L204-L204), [240](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L240-L240), [335](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L335-L335), [367](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L367-L367), 


#### Recommendation

To enhance the safety and reliability of your Solidity smart contracts, it is advisable to utilize OpenZeppelin’s `SafeCast` library when casting between `int` and `uint` types. Incorporating this library into your codebase will help prevent unexpected overflows and underflows during type conversion, reducing the risk of vulnerabilities and ensuring secure contract execution.

### Revert on transfer to the zero address
It's good practice to revert a token transfer transaction if the recipient's address is the zero address. This can prevent unintentional transfers to the zero address due to accidental operations or programming errors. Many token contracts implement such a safeguard, such as [OpenZeppelin - ERC20](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/9e3f4d60c581010c4a3979480e07cc7752f124cc/contracts/token/ERC20/ERC20.sol#L232), [OpenZeppelin - ERC721](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/9e3f4d60c581010c4a3979480e07cc7752f124cc/contracts/token/ERC721/ERC721.sol#L142).

```solidity
Path: ./contracts/p1/Distributor.sol

172:                transfers[i].addrTo,	// @audit-issue
```
[172](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L172-L172), 


```solidity
Path: ./contracts/p1/BackingManager.sol

215:            IERC20(address(rsr)).safeTransfer(address(stRSR), rsr.balanceOf(address(this)));	// @audit-issue
```
[215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L215-L215), 


```solidity
Path: ./contracts/p1/StRSR.sol

337:        IERC20Upgradeable(address(rsr)).safeTransfer(account, rsrAmount);	// @audit-issue
```
[337](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L337-L337), 


```solidity
Path: ./contracts/p1/RToken.sol

151:                address(backingManager),	// @audit-issue

221:                recipient,	// @audit-issue

329:                    recipient,	// @audit-issue

421:            address(backingManager),	// @audit-issue
```
[151](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L151-L151), [221](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L221-L221), [329](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L329-L329), [421](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L421-L421), 


```solidity
Path: ./contracts/p1/Broker.sol

261:            address(trade),	// @audit-issue

289:            address(trade),	// @audit-issue
```
[261](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L261-L261), [289](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L289-L289), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

227:        IERC20Upgradeable(address(erc20)).safeTransfer(origin, erc20.balanceOf(address(this)));	// @audit-issue
```
[227](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L227-L227), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

300:        erc20.safeTransfer(address(origin), erc20.balanceOf(address(this)));	// @audit-issue
```
[300](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L300-L300), 


#### Recommendation

To enhance the security and reliability of your token contracts, it's advisable to implement a safeguard that reverts token transfer transactions if the recipient's address is the zero address. This practice helps prevent unintentional transfers to the zero address, reducing the risk of fund loss due to accidental operations or programming errors. Many token contracts, including [OpenZeppelin's ERC20](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/9e3f4d60c581010c4a3979480e07cc7752f124cc/contracts/token/ERC20/ERC20.sol#L232) and [ERC721](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/9e3f4d60c581010c4a3979480e07cc7752f124cc/contracts/token/ERC721/ERC721.sol#L142), incorporate this safeguard for added security.

### State variables not limited to reasonable values
Consider adding appropriate minimum/maximum value checks to ensure that the following state variables can never be used to excessively harm users, including via griefing.

```solidity
Path: ./contracts/p1/StRSR.sol

883:        _allowances[era][owner][spender] = amount;	// @audit-issue
```
[883](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L883-L883), 


```solidity
Path: ./contracts/p1/RToken.sol

400:        basketsNeeded = basketsNeeded_;	// @audit-issue
```
[400](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L400-L400), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

106:        endTime = uint48(block.timestamp) + batchAuctionLength;	// @audit-issue
```
[106](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L106-L106), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

182:        endTime = _startTime + auctionLength;	// @audit-issue
```
[182](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L182-L182), 


#### Recommendation


Implement validation checks for state variables to enforce minimum and maximum value limits. This can be achieved by adding modifiers or require statements in Solidity functions that modify these state variables. Ensure that these limits are reasonable and reflect the intended use of the contract. Additionally, consider implementing a mechanism to update these limits through a governance process or a trusted role, if applicable, to maintain flexibility and adaptability of the contract over time.

### Missing contract existence checks before low-level calls
Low-level calls return success if there is no code present at the specified address. In addition to the zero-address checks, add a check to verify that `<address>.code.length > 0`.

```solidity
Path: ./contracts/p1/Main.sol

158:        (bool success, ) = proxy.call(	// @audit-issue
```
[158](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L158-L158), 


#### Recommendation

To enhance the security and reliability of your Solidity smart contracts, always include contract existence checks before making low-level calls. In addition to verifying that the address is not the zero address, also confirm that `<address>.code.length > 0`. These checks help ensure that the target address corresponds to a valid and functioning contract, reducing the risk of unexpected behavior and vulnerabilities in your contract interactions.

### External calls in an unbounded loop can result in a DoS
Consider limiting the number of iterations in loops that make external calls, as just a single one of them failing will result in a revert.

```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

41:            if (address(versionRegistry.deployments(versionHash)) == address(0)) {	// @audit-issue

69:            if (address(versionRegistry.deployments(versionHash)) == address(0)) {	// @audit-issue
```
[41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L41-L41), [69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L69-L69), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

90:            require(assetRegistry.isRegistered(erc20s[i]), "unregistered erc20");	// @audit-issue

91:            erc20s[i].safeTransfer(address(backingManager), erc20s[i].balanceOf(address(this)));	// @audit-issue

143:                assetRegistry.toAsset(erc20s[i]).refresh();	// @audit-issue

158:            require(erc20.balanceOf(address(this)) != 0, "0 balance");	// @audit-issue

160:            IAsset assetToSell = assetRegistry.toAsset(erc20);	// @audit-issue

161:            (uint192 sellLow, uint192 sellHigh) = assetToSell.price(); // {UoA/tok}	// @audit-issue

166:                sellAmount: assetToSell.bal(address(this)),	// @audit-issue

173:            (, TradeRequest memory req) = TradeLib.prepareTradeSell(	// @audit-issue

178:            require(req.sellAmount > 1, "sell amount too low");	// @audit-issue

181:            tryTrade(kinds[i], req, trade.prices);	// @audit-issue
```
[90](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L90-L90), [91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L91-L91), [143](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L143-L143), [158](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L158-L158), [160](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L160-L160), [161](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L161-L161), [166](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L166-L166), [173](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L173-L173), [178](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L178-L178), [181](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L181-L181), 


```solidity
Path: ./contracts/p1/Distributor.sol

146:            address addrTo = destinations.at(i);	// @audit-issue

145:        for (uint256 i = 0; i < destinations.length(); ++i) {	// @audit-issue

170:            IERC20Upgradeable(address(erc20)).safeTransferFrom(	// @audit-issue

172:                transfers[i].addrTo,	// @audit-issue

173:                transfers[i].amount	// @audit-issue

207:            RevenueShare storage share = distribution[destinations.at(i)];	// @audit-issue
```
[146](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L146-L146), [145](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L145-L145), [170](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L170-L170), [172](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L172-L172), [173](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L173-L173), [207](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L207-L207), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

64:            assets[IERC20(_erc20s.at(i))].refresh();	// @audit-issue

152:            erc20s_[i] = IERC20(_erc20s.at(i));	// @audit-issue

164:            reg.erc20s[i] = IERC20(_erc20s.at(i));	// @audit-issue

165:            reg.assets[i] = assets[IERC20(_erc20s.at(i))];	// @audit-issue

180:                    assetPluginRegistry.isValidAsset(	// @audit-issue

181:                        keccak256(abi.encodePacked(this.version())),	// @audit-issue
```
[64](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L64-L64), [152](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L152-L152), [164](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L164-L164), [165](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L165-L165), [180](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L180-L180), [181](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L181-L181), 


```solidity
Path: ./contracts/p1/BackingManager.sol

237:            IAsset asset = assetRegistry.toAsset(erc20s[i]);	// @audit-issue

241:            uint192 req = needed.mul(basketHandler.quantity(erc20s[i]), CEIL);	// @audit-issue

242:            uint192 bal = asset.bal(address(this));	// @audit-issue

244:            if (bal.gt(req)) {	// @audit-issue

246:                uint256 delta = bal.minus(req).shiftl_toUint(int8(asset.erc20Decimals()));	// @audit-issue

254:                    erc20s[i].safeTransfer(address(rsrTrader), tokensPerShare * totals.rsrTotal);	// @audit-issue

257:                    erc20s[i].safeTransfer(	// @audit-issue

259:                        tokensPerShare * totals.rTokenTotal	// @audit-issue

290:            ctx.quantities[i] = basketHandler.quantityUnsafe(reg.erc20s[i], reg.assets[i]);	// @audit-issue

295:            ctx.bals[i] = reg.assets[i].bal(address(this)) + tokensOut[reg.erc20s[i]];	// @audit-issue

298:            if (reg.erc20s[i] == rsr) ctx.bals[i] += reg.assets[i].bal(address(stRSR));	// @audit-issue
```
[237](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L237-L237), [241](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L241-L241), [242](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L242-L242), [244](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L244-L244), [246](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L246-L246), [254](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L254-L254), [257](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L257-L257), [259](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L259-L259), [290](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L290-L290), [295](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L295-L295), [298](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L298-L298), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

263:            require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "erc20 is not collateral");	// @audit-issue

266:            config.erc20s.push(erc20s[i]);	// @audit-issue

268:            names[i] = assetRegistry.toColl(erc20s[i]).targetName();	// @audit-issue

299:            assetRegistry.toColl(erc20s[i]); // reverts if not collateral	// @audit-issue

300:            conf.erc20s.push(erc20s[i]);	// @audit-issue

322:            CollateralStatus s = assetRegistry.toColl(basket.erc20s[i]).status();	// @audit-issue

323:            if (s.worseThan(status_)) {	// @audit-issue

425:            try assetRegistry.toColl(basket.erc20s[i]) returns (ICollateral coll) {	// @audit-issue

426:                uint192 qty = _quantity(basket.erc20s[i], coll, CEIL);	// @audit-issue

429:                (uint192 lowP, uint192 highP) = coll.price();	// @audit-issue

431:                low256 += qty.safeMul(lowP, FLOOR);	// @audit-issue

443:                        if (premium > FIX_ONE) qty = qty.safeMul(premium, CEIL);	// @audit-issue

446:                    high256 += qty.safeMul(highP, CEIL);	// @audit-issue

492:            erc20s[i] = address(basket.erc20s[i]);	// @audit-issue

493:            ICollateral coll = assetRegistry.toColl(IERC20(erc20s[i]));	// @audit-issue

496:            uint192 q = _quantity(basket.erc20s[i], coll, rounding).safeMul(amount, rounding);	// @audit-issue

503:                if (premium > FIX_ONE) q = q.safeMul(premium, rounding);	// @audit-issue

507:            quantities[i] = q.shiftl_toUint(	// @audit-issue

508:                int8(IERC20Metadata(address(basket.erc20s[i])).decimals()),	// @audit-issue

550:                if (address(b.erc20s[j]) == address(0)) continue;	// @audit-issue

562:                uint192 amt = portions[i].mul(b.refAmts[b.erc20s[j]], FLOOR);	// @audit-issue

566:                    try assetRegistry.toAsset(b.erc20s[j]) returns (IAsset asset) {	// @audit-issue

567:                        if (!asset.isCollateral()) continue; // skip token if not collateral	// @audit-issue

597:            quantities[i] = amount	// @audit-issue
598:            .safeMulDiv(refAmtsAll[i], collsAll[i].refPerTok(), FLOOR)
599:            .shiftl_toUint(int8(collsAll[i].erc20Decimals()), FLOOR);

597:            quantities[i] = amount	// @audit-issue
598:            .safeMulDiv(refAmtsAll[i], collsAll[i].refPerTok(), FLOOR)

598:            .safeMulDiv(refAmtsAll[i], collsAll[i].refPerTok(), FLOOR)	// @audit-issue

599:            .shiftl_toUint(int8(collsAll[i].erc20Decimals()), FLOOR);	// @audit-issue

616:            ICollateral coll = assetRegistry.toColl(basket.erc20s[i]);	// @audit-issue

617:            if (coll.status() == CollateralStatus.DISABLED) return BasketRange(FIX_ZERO, FIX_MAX);	// @audit-issue

620:            uint192 q = _quantity(basket.erc20s[i], coll, CEIL);	// @audit-issue

624:            uint192 inBUs = coll.bal(account).div(q);	// @audit-issue

625:            baskets.bottom = fixMin(baskets.bottom, inBUs);	// @audit-issue

626:            baskets.top = fixMax(baskets.top, inBUs);	// @audit-issue

716:            try assetRegistry.toAsset(IERC20(erc20s[i])) returns (IAsset asset) {	// @audit-issue

717:                if (!asset.isCollateral()) continue; // skip token if no longer registered	// @audit-issue

720:                quantities[i] = b	// @audit-issue
721:                .refAmts[erc20s[i]]
722:                .safeDiv(ICollateral(address(asset)).refPerTok(), FLOOR)
723:                .shiftl_toUint(int8(asset.erc20Decimals()), FLOOR);

720:                quantities[i] = b	// @audit-issue
721:                .refAmts[erc20s[i]]
722:                .safeDiv(ICollateral(address(asset)).refPerTok(), FLOOR)

720:                quantities[i] = b	// @audit-issue
721:                .refAmts[erc20s[i]]

722:                .safeDiv(ICollateral(address(asset)).refPerTok(), FLOOR)	// @audit-issue

723:                .shiftl_toUint(int8(asset.erc20Decimals()), FLOOR);	// @audit-issue
```
[263](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L263-L263), [266](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L266-L266), [268](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L268-L268), [299](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L299-L299), [300](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L300-L300), [322](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L322-L322), [323](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L323-L323), [425](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L425-L425), [426](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L426-L426), [429](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L429-L429), [431](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L431-L431), [443](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L443-L443), [446](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L446-L446), [492](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L492-L492), [493](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L493-L493), [496](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L496-L496), [503](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L503-L503), [507](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L507-L507), [508](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L508-L508), [550](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L550-L550), [562](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L562-L562), [566](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L566-L566), [567](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L567-L567), [597](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L597-L599), [597](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L597-L598), [598](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L598-L598), [599](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L599-L599), [616](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L616-L616), [617](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L617-L617), [620](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L620-L620), [624](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L624-L624), [625](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L625-L625), [626](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L626-L626), [716](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L716-L716), [717](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L717-L717), [720](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L720-L723), [720](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L720-L722), [720](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L720-L721), [722](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L722-L722), [723](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L723-L723), 


```solidity
Path: ./contracts/p1/RToken.sol

149:            IERC20Upgradeable(erc20s[i]).safeTransferFrom(	// @audit-issue

219:            IERC20Upgradeable(erc20s[i]).safeTransferFrom(	// @audit-issue

300:                IERC20(erc20s[i]).balanceOf(address(backingManager)),	// @audit-issue

312:            pastBals[i] = IERC20(expectedERC20sOut[i]).balanceOf(recipient);	// @audit-issue

327:                IERC20Upgradeable(erc20s[i]).safeTransferFrom(	// @audit-issue

340:            uint256 bal = IERC20(expectedERC20sOut[i]).balanceOf(recipient);	// @audit-issue
```
[149](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L149-L149), [219](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L219-L219), [300](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L300-L300), [312](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L312-L312), [327](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L327-L327), [340](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L340-L340), 


```solidity
Path: ./contracts/p1/mixins/RewardableLib.sol

29:            address(registry.assets[i]).functionDelegateCall(	// @audit-issue

30:                abi.encodeWithSignature("claimRewards()"),	// @audit-issue
```
[29](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L29-L29), [30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L30-L30), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

141:            if (reg.erc20s[i] == IERC20(address(ctx.rToken))) continue;	// @audit-issue

143:            (uint192 low, uint192 high) = reg.assets[i].price(); // {UoA/tok}	// @audit-issue

149:                !TradeLib.isEnoughToSell(reg.assets[i], ctx.bals[i], low, ctx.minTradeVolume)	// @audit-issue

161:                uint192 anchor = ctx.quantities[i].mul(ctx.basketsHeld.top, CEIL);	// @audit-issue

168:                        uint256(low.mulDiv(anchor - ctx.bals[i], buPriceHigh, FLOOR))	// @audit-issue

176:                        uint256(high.safeMulDiv(ctx.bals[i] - anchor, buPriceLow, CEIL))	// @audit-issue

185:                uint192 anchor = ctx.quantities[i].mul(ctx.basketsHeld.bottom, FLOOR);	// @audit-issue

189:                uint192 val = low.mul(ctx.bals[i] - anchor, FLOOR);	// @audit-issue

289:            if (address(reg.erc20s[i]) == address(ctx.rToken)) continue;	// @audit-issue

297:            uint192 needed = range.top.mul(ctx.quantities[i], CEIL); // {tok}	// @audit-issue

299:            if (ctx.bals[i].gt(needed)) {	// @audit-issue

300:                (uint192 low, uint192 high) = reg.assets[i].price(); // {UoA/sellTok}	// @audit-issue

305:                uint192 delta = ctx.bals[i].minus(needed).mul(low, FLOOR);	// @audit-issue

309:                if (reg.assets[i].isCollateral()) {	// @audit-issue

310:                    status = ICollateral(address(reg.assets[i])).status();	// @audit-issue

317:                    TradeLib.isEnoughToSell(	// @audit-issue

318:                        reg.assets[i],	// @audit-issue

319:                        ctx.bals[i].minus(needed),	// @audit-issue

321:                        ctx.minTradeVolume	// @audit-issue

325:                    trade.sellAmount = ctx.bals[i].minus(needed);	// @audit-issue

334:                needed = range.bottom.mul(ctx.quantities[i], CEIL); // {buyTok};	// @audit-issue

336:                if (ctx.bals[i].lt(needed)) {	// @audit-issue

337:                    uint192 amtShort = needed.minus(ctx.bals[i]); // {buyTok}	// @audit-issue

338:                    (uint192 low, uint192 high) = reg.assets[i].price(); // {UoA/buyTok}	// @audit-issue

341:                    uint192 delta = amtShort.mul(high, CEIL);	// @audit-issue

344:                    if (delta.gt(maxes.deficit)) {	// @audit-issue
```
[141](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L141-L141), [143](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L143-L143), [149](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L149-L149), [161](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L161-L161), [168](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L168-L168), [176](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L176-L176), [185](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L185-L185), [189](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L189-L189), [289](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L289-L289), [297](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L297-L297), [299](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L299-L299), [300](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L300-L300), [305](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L305-L305), [309](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L309-L309), [310](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L310-L310), [317](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L317-L317), [318](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L318-L318), [319](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L319-L319), [321](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L321-L321), [325](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L325-L325), [334](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L334-L334), [336](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L336-L336), [337](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L337-L337), [338](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L338-L338), [341](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L341-L341), [344](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L344-L344), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

82:            self.erc20s.push(_erc20);	// @audit-issue

181:            targetNames.add(config.targetNames[config.erc20s[i]]);	// @audit-issue

203:                if (targetNames.at(targetIndex) == config.targetNames[_erc20]) break;	// @audit-issue

211:            totalWeights[targetIndex] = totalWeights[targetIndex].plus(targetWeight);	// @audit-issue

214:                goodCollateral(config.targetNames[_erc20], _erc20, assetRegistry) &&	// @audit-issue

215:                targetWeight.gt(FIX_ZERO)	// @audit-issue

217:                goodWeights[targetIndex] = goodWeights[targetIndex].plus(targetWeight);	// @audit-issue

220:                    targetWeight.div(	// @audit-issue

222:                        assetRegistry.toColl(_erc20).targetPerRef(),	// @audit-issue

242:            if (totalWeights[i].lte(goodWeights[i])) continue; // Don't need any backup weight	// @audit-issue

243:            bytes32 _targetName = targetNames.at(i);	// @audit-issue

253:                if (goodCollateral(_targetName, backup.erc20s[j], assetRegistry)) size++;	// @audit-issue

264:                if (goodCollateral(_targetName, backup.erc20s[j], assetRegistry)) {	// @audit-issue

265:                    uint192 backupWeight = totalWeights[i].minus(goodWeights[i]).div(	// @audit-issue

267:                        assetRegistry.toColl(backup.erc20s[j]).targetPerRef().mulu(size),	// @audit-issue

273:                    BasketLibP1.add(newBasket, backup.erc20s[j], backupWeight);	// @audit-issue

328:            (bool contains, uint256 amt) = _targetAmts.tryGet(targetName);	// @audit-issue

329:            _targetAmts.set(	// @audit-issue

331:                contains ? amt + config.targetAmts[erc20] : config.targetAmts[erc20]	// @audit-issue

338:            bytes32 targetName = assetRegistry.toColl(erc20s[i]).targetName();	// @audit-issue

339:            (bool contains, uint256 amt) = _targetAmts.tryGet(targetName);	// @audit-issue

341:            if (amt > targetAmts[i]) _targetAmts.set(targetName, amt - targetAmts[i]);	// @audit-issue

342:            else _targetAmts.remove(targetName);	// @audit-issue

364:            ICollateral coll = assetRegistry.toColl(erc20s[i]); // reverts if unregistered	// @audit-issue

365:            require(coll.status() == CollateralStatus.SOUND, "unsound new collateral");	// @audit-issue

367:            (uint192 low, uint192 high) = coll.price(); // {UoA/tok}	// @audit-issue

371:            newPrice += targetAmts[i].mulDiv(	// @audit-issue

373:                coll.targetPerRef().mul(coll.refPerTok(), CEIL),	// @audit-issue

382:            newTargetAmts[i] = targetAmts[i].mulDiv(price, newPrice, CEIL);	// @audit-issue
```
[82](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L82-L82), [181](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L181-L181), [203](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L203-L203), [211](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L211-L211), [214](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L214-L214), [215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L215-L215), [217](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L217-L217), [220](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L220-L220), [222](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L222-L222), [242](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L242-L242), [243](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L243-L243), [253](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L253-L253), [264](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L264-L264), [265](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L265-L265), [267](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L267-L267), [273](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L273-L273), [328](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L328-L328), [329](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L329-L329), [331](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L331-L331), [338](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L338-L338), [339](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L339-L339), [341](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L341-L341), [342](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L342-L342), [364](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L364-L364), [365](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L365-L365), [367](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L367-L367), [371](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L371-L371), [373](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L373-L373), [382](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L382-L382), 


#### Recommendation

To mitigate the risk of Denial-of-Service (DoS) attacks in your Solidity code, it's important to limit the number of iterations in loops that involve external calls. A single failed external call in an unbounded loop can lead to a revert, causing disruptions in contract execution. Consider implementing safeguards, such as setting a maximum loop iteration count or employing strategies like batch processing, to reduce the impact of potential external call failures.

### Solidity version 0.8.20 might not work on all chains due to `PUSH0`
The Solidity version 0.8.20 employs the recently introduced PUSH0 opcode in the Shanghai EVM. This opcode might not be universally supported across all blockchain networks and Layer 2 solutions. Thus, as a result, it might be not possible to deploy solution with version 0.8.20 >= on some blockchains.

```solidity
Path: ./contracts/libraries/Fixed.sol

4:pragma solidity ^0.8.19;	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L4-L4), 


#### Recommendation

The Solidity version 0.8.20 employs the recently introduced PUSH0 opcode in the Shanghai EVM. This opcode might not be universally supported across all blockchain networks and Layer 2 solutions. Thus, as a result, it might be not possible to deploy solution with version 0.8.20 >= on some blockchains.

It is recommended to verify whether solution can be deployed on particular blockchain with the Solidity version 0.8.20 >=. Whenever such deployment is not possible due to lack of PUSH0 opcode support and lowering the Solidity version is a must, it is strongly advised to review all feature changes and bugfixes in [Solidity releases](https://soliditylang.org/blog/category/releases/). Some changes may have impact on current implementation and may impose a necessity of maintaining another version of solution.

### Use `increaseAllowance()`/`decreaseAllowance()` instead of `approve()`/`safeApprove()`
Changing an allowance with `approve()` brings the risk that someone may use both the old and the new allowance by unfortunate transaction ordering. [Refer to ERC20 API: An Attack Vector on the Approve/TransferFrom Methods](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit#heading=h.m9fhqynw2xvt). It is recommended to use the `increaseAllowance()`/`decreaseAllowance()` to avoid ths problem.

```solidity
Path: ./contracts/libraries/Allowance.sol

25:        token.approve(spender, 0);	// @audit-issue

34:        try token.approve(spender, value) {	// @audit-issue

41:            token.approve(spender, type(uint256).max);	// @audit-issue
```
[25](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L25-L25), [34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L34-L34), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L41-L41), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

191:        tokenToBuy.safeApprove(address(distributor), 0);	// @audit-issue

192:        tokenToBuy.safeApprove(address(distributor), bal);	// @audit-issue
```
[191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L191-L191), [192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L192-L192), 


```solidity
Path: ./contracts/p1/BackingManager.sol

72:        IERC20(address(erc20)).safeApprove(address(rToken), 0);	// @audit-issue

73:        IERC20(address(erc20)).safeApprove(address(rToken), type(uint256).max);	// @audit-issue
```
[72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L72-L72), [73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L73-L73), 


#### Recommendation

To enhance the security of your Solidity smart contracts and avoid potential issues related to transaction ordering, it is advisable to use the `increaseAllowance()` and `decreaseAllowance()` functions instead of `approve()` or `safeApprove()`. These functions provide a safer and more atomic way to modify allowances and mitigate the risk associated with potential attack vectors like those described in the [ERC20 API: An Attack Vector on the Approve/TransferFrom Methods](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit#heading=h.m9fhqynw2xvt) document.

### `unchecked` blocks with subtractions may underflow
There aren't any checks to avoid an underflow which can happen inside an `unchecked` block, so the following subtractions may underflow silently.

```solidity
Path: ./contracts/libraries/Fixed.sol

676:        uint256 mm = mulmod(x, y, uint256(0) - uint256(1));	// @audit-issue

678:        hi = mm - lo;	// @audit-issue
```
[676](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L676-L676), [678](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L678-L678), 


#### Recommendation

Exercise caution when using `unchecked` blocks with subtractions in Solidity, as there are no built-in checks to prevent underflows. It's essential to implement your own checks and validations to ensure that subtractions within `unchecked` blocks do not result in silent underflows. Proper input validation and error handling can help prevent unexpected issues related to underflows.

### `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`
Use `abi.encode()` instead which will pad items to 32 bytes, which will [prevent hash collisions](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html#non-standard-packed-mode) (e.g. `abi.encodePacked(0x123,0x456)` => `0x123456` => `abi.encodePacked(0x1,0x23456)`, but `abi.encode(0x123,0x456)` => `0x0...1230...456`). "Unless there is a compelling reason, `abi.encode` should be preferred". If there is only one argument to `abi.encodePacked()` it can often be cast to `bytes()` or `bytes32()` [instead](https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739).
If all arguments are strings and or bytes, `bytes.concat()` should be used instead

```solidity
Path: ./contracts/registry/VersionRegistry.sol

47:        bytes32 versionHash = keccak256(abi.encodePacked(version));	// @audit-issue
```
[47](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L47-L47), 


```solidity
Path: ./contracts/p1/Main.sol

108:        require(keccak256(abi.encodePacked(this.version())) == versionHash, "upgrade mismatch");	// @audit-issue

117:        require(keccak256(abi.encodePacked(this.version())) == versionHash, "upgrade main first");	// @audit-issue
```
[108](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L108-L108), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L117-L117), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

181:                        keccak256(abi.encodePacked(this.version())),	// @audit-issue

225:                    keccak256(abi.encodePacked(this.version())),	// @audit-issue
```
[181](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L181-L181), [225](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L225-L225), 


#### Recommendation

To ensure the integrity of hash functions like `keccak256()` in Solidity, prefer using `abi.encode()` instead of `abi.encodePacked()` when dealing with dynamic types. `abi.encode()` pads items to 32 bytes, preventing hash collisions and enhancing security. Only use `abi.encodePacked()` when there is a compelling reason. If there's only one argument to `abi.encodePacked()`, consider casting it to `bytes()` or `bytes32()`. If all arguments are strings or bytes, use `bytes.concat()` for better clarity and reliability.

### Non Disabled Implementation Contract
The upgradeable contracts do not disable initializers in the constructor, as recommended by the imported Initializable contract. This means that anyone can call the initializer on the implementation contract to set the contract variables and assign the roles. To reduce the potential attack surface, `_disableInitializers` in the constructor needs to be called.

```solidity
Path: ./contracts/p1/Main.sol

30:    constructor() initializer {}	// @audit-issue
```
[30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L30-L30), 


```solidity
Path: ./contracts/p1/StRSR.sol

34:abstract contract StRSRP1 is Initializable, ComponentP1, IStRSR, EIP712Upgradeable {	// @audit-issue
```
[34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L34-L34), 


```solidity
Path: ./contracts/p1/RToken.sol

17:contract RTokenP1 is ComponentP1, ERC20PermitUpgradeable, IRToken {	// @audit-issue
```
[17](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L17-L17), 


#### Recommendation


Build a constructor function in the upgradeable contracts that calls the disableInitializers() function.

### Some tokens do not consider `type(uint256).max` as an infinite approval
Some tokens such as [COMP](https://github.com/compound-finance/compound-protocol/blob/a3214f67b73310d547e00fc578e8355911c9d376/contracts/Governance/Comp.sol#L89-L91) downcast such approvals to uint96 and use that as a raw value rather than interpreting it as an infinite approval. Eventually these approvals will reach zero, at which point the calling contract will no longer function properly.

```solidity
Path: ./contracts/libraries/Allowance.sol

41:            token.approve(spender, type(uint256).max);	// @audit-issue
```
[41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L41-L41), 


```solidity
Path: ./contracts/p1/BackingManager.sol

73:        IERC20(address(erc20)).safeApprove(address(rToken), type(uint256).max);	// @audit-issue
```
[73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L73-L73), 


#### Recommendation

Ensure token contracts correctly interpret `type(uint256).max` as representing infinite approval. Avoid downcasting this value to smaller types, which could unintentionally impose a finite limit on approvals. If implementing a token contract, either respect `type(uint256).max` as infinite or clearly document and communicate any deviations from this norm. For contracts interacting with tokens, be aware of how different tokens handle this value and plan for potential edge cases where infinite approvals are treated as finite.

### Contracts use infinite approvals with no means to revoke
Infinite approvals on external contracts can be dangerous if the target becomes compromised. See [here](https://revoke.cash/exploits) for a list of approval exploits. The following contracts are vulnerable to such attacks since they have no functionality to revoke the approval (call `approve` with amount `0`). Consider enabling the contract to revoke approval in emergency situations.

```solidity
Path: ./contracts/libraries/Allowance.sol

41:            token.approve(spender, type(uint256).max);	// @audit-issue
```
[41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L41-L41), 


```solidity
Path: ./contracts/p1/BackingManager.sol

73:        IERC20(address(erc20)).safeApprove(address(rToken), type(uint256).max);	// @audit-issue
```
[73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L73-L73), 


#### Recommendation

Implement functionalities in your Solidity contracts to revoke infinite approvals. Allow users or designated entities (like contract administrators) to reset approval amounts to zero, providing a safeguard against compromised external contracts. This can be a dedicated function or part of a broader emergency response mechanism. Clearly document and communicate this feature to users for transparency and ease of use. Additionally, consider monitoring and auditing approved contracts regularly to identify and respond to potential vulnerabilities promptly.

### Excessive Gas Consumption Due to Nested Loops in Solidity
In the Solidity smart contract, there are instances where for-loops are nested within other for-loops. Nested loops in Solidity can lead to significantly increased gas consumption, especially when operating on large data sets. This is because the complexity of nested loops is multiplicative; a double nested loop results in \(O(n^2)\) complexity, and a triple nested loop leads to \(O(n^3)\) complexity. In the context of Ethereum, where each operation consumes a certain amount of gas, such complexity can make transactions prohibitively expensive and may even hit the block gas limit, causing transactions to fail.

```solidity
Path: ./contracts/libraries/Array.sol

12:            for (uint256 j = 0; j < i; ++j) {	// @audit-issue: This for is under another for loop on line 11
```
[12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L12-L12), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

547:            for (uint256 j = 0; j < b.erc20s.length; ++j) {	// @audit-issue: This for is under another for loop on line 535

554:                for (uint256 k = 0; k < len; ++k) {	// @audit-issue: This for is under another for loop on line 535

554:                for (uint256 k = 0; k < len; ++k) {	// @audit-issue: This for is under another for loop on line 547
```
[547](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L547-L547), [554](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L554-L554), [554](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L554-L554), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

202:            for (targetIndex = 0; targetIndex < targetsLength; ++targetIndex) {	// @audit-issue: This for is under another for loop on line 198

252:            for (uint256 j = 0; j < backup.erc20s.length && size < backup.max; ++j) {	// @audit-issue: This for is under another for loop on line 241

263:            for (uint256 j = 0; j < backup.erc20s.length && assigned < size; ++j) {	// @audit-issue: This for is under another for loop on line 241
```
[202](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L202-L202), [252](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L252-L252), [263](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L263-L263), 


#### Recommendation


1. **Optimize Data Structures**: Where possible, use more efficient data structures that can reduce the need for nested iterations. This might involve restructuring how data is stored and accessed.
2. **Limit Loop Operations**: Implement checks or mechanisms to limit the number of iterations in each loop. For instance, setting a hard cap on array sizes or breaking out of loops early when a certain condition is met.


### Critical functions should have a timelock
Critical functions, especially those affecting protocol parameters or user funds, are potential points of failure or exploitation. To mitigate risks, incorporating a timelock on such functions can be beneficial. A timelock requires a waiting period between the time an action is initiated and when it's executed, giving stakeholders time to react, potentially vetoing malicious or erroneous changes. To implement, integrate a smart contract like OpenZeppelin's `TimelockController` or build a custom mechanism. This ensures governance decisions or administrative changes are transparent and allows for community or multi-signature interventions, enhancing protocol security and trustworthiness.

```solidity
Path: ./contracts/p1/Main.sol

61:    function setVersionRegistry(VersionRegistry versionRegistry_) external onlyRole(OWNER) {
62:        require(address(versionRegistry_) != address(0), "invalid registry address");
63:        require(address(versionRegistry) == address(0), "already set");
64:
65:        versionRegistry = VersionRegistry(versionRegistry_);	// @audit-issue

70:    function setAssetPluginRegistry(AssetPluginRegistry registry_) external onlyRole(OWNER) {
71:        require(address(registry_) != address(0), "invalid registry address");
72:        require(address(assetPluginRegistry) == address(0), "already set");
73:
74:        assetPluginRegistry = AssetPluginRegistry(registry_);	// @audit-issue

79:    function setDAOFeeRegistry(DAOFeeRegistry feeRegistry_) external onlyRole(OWNER) {
80:        require(address(feeRegistry_) != address(0), "invalid registry address");
81:        require(address(daoFeeRegistry) == address(0), "already set");
82:
83:        daoFeeRegistry = DAOFeeRegistry(feeRegistry_);	// @audit-issue
```
[65](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L61-L65), [74](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L70-L74), [83](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L79-L83), 


```solidity
Path: ./contracts/p1/Broker.sol

186:    function setBatchTradeImplementation(ITrade newTradeImplementation) public onlyMain {
187:        require(
188:            address(newTradeImplementation) != address(0),
189:            "invalid batchTradeImplementation address"
190:        );
191:
192:        emit BatchTradeImplementationSet(batchTradeImplementation, newTradeImplementation);
193:        batchTradeImplementation = newTradeImplementation;	// @audit-issue

208:    function setDutchTradeImplementation(ITrade newTradeImplementation) public onlyMain {
209:        require(
210:            address(newTradeImplementation) != address(0),
211:            "invalid dutchTradeImplementation address"
212:        );
213:
214:        emit DutchTradeImplementationSet(dutchTradeImplementation, newTradeImplementation);
215:        dutchTradeImplementation = newTradeImplementation;	// @audit-issue
```
[193](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L186-L193), [215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L208-L215), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

48:    function __Trading_init(
49:        IMain main_,
50:        uint192 maxTradeSlippage_,
51:        uint192 minTradeVolume_
52:    ) internal onlyInitializing {
53:        broker = main_.broker();	// @audit-issue
```
[53](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L48-L53), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

33:    function __Component_init(IMain main_) internal onlyInitializing {
34:        require(address(main_) != address(0), "main is zero address");
35:        __UUPSUpgradeable_init();
36:        main = main_;	// @audit-issue
```
[36](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L33-L36), 


#### Recommendation

Integrate a timelock mechanism into your Solidity contracts for critical functions, especially those controlling protocol parameters or managing user funds. Consider using established solutions like OpenZeppelin's `TimelockController` for robustness and reliability. Alternatively, develop a custom timelock mechanism tailored to your specific requirements. Ensure that the timelock duration is appropriate for your contract's use case and stakeholder needs, providing sufficient time for review and intervention. Clearly document and communicate the presence and workings of the timelock mechanism to stakeholders to maintain transparency and trust in your contract's operations.

### Consider bounding input array length
If the number of for loop iterations is unbounded, then it may lead to the transaction to run out of gas. While the function will revert if it eventually runs out of gas, it may be a nicer user experience to require() that the length of the array is below some reasonable maximum, so that the user doesn't have to use up a full transaction's gas only to see that the transaction reverts.

```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

39:        for (uint256 i = 0; i < validForVersions.length; ++i) {	// @audit-issue
```
[39](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L39-L39), 


```solidity
Path: ./contracts/libraries/String.sol

14:        for (uint256 i = 0; i < bStr.length; i++) {	// @audit-issue
```
[14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/String.sol#L14-L14), 


```solidity
Path: ./contracts/p1/BackingManager.sol

289:        for (uint256 i = 0; i < reg.erc20s.length; ++i) {	// @audit-issue

294:        for (uint256 i = 0; i < reg.erc20s.length; ++i) {	// @audit-issue
```
[289](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L289-L289), [294](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L294-L294), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

547:            for (uint256 j = 0; j < b.erc20s.length; ++j) {	// @audit-issue

684:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

713:        for (uint256 i = 0; i < b.erc20s.length; ++i) {	// @audit-issue

751:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

769:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue
```
[547](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L547-L547), [684](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L684-L684), [713](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L713-L713), [751](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L751-L751), [769](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L769-L769), 


```solidity
Path: ./contracts/p1/RToken.sol

148:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

214:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

269:        for (uint256 i = 0; i < portions.length; ++i) {	// @audit-issue

297:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

311:        for (uint256 i = 0; i < expectedERC20sOut.length; ++i) {	// @audit-issue

321:            for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

339:        for (uint256 i = 0; i < expectedERC20sOut.length; ++i) {	// @audit-issue
```
[148](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L148-L148), [214](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L214-L214), [269](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L269-L269), [297](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L297-L297), [311](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L311-L311), [321](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L321-L321), [339](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L339-L339), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

139:        for (uint256 i = 0; i < reg.erc20s.length; ++i) {	// @audit-issue

288:        for (uint256 i = 0; i < reg.erc20s.length; ++i) {	// @audit-issue
```
[139](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L139-L139), [288](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L288-L288), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

180:        for (uint256 i = 0; i < config.erc20s.length; ++i) {	// @audit-issue

198:        for (uint256 i = 0; i < config.erc20s.length; ++i) {	// @audit-issue
```
[180](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L180-L180), [198](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L198-L198), 


#### Recommendation

Implement a check at the beginning of your Solidity functions to enforce a maximum length on input arrays. Use a `require()` statement to validate that the length of any input array does not exceed a predetermined limit, which should be chosen based on the function's complexity and typical gas usage. This ensures that the function will not attempt to process more data than it can handle within reasonable gas limits, thereby preventing out-of-gas errors and improving the overall user experience. Clearly document this behavior and the rationale behind the chosen array size limit to inform users and developers interacting with your contract.

### Return values of `approve` not checked.
Not all `IERC20` implementations `revert` when there's a failure in `approve`. The function signature has a boolean return value and they indicate errors that way instead.

By not checking the return value, operations that should have marked as failed, may potentially go through without actually approving anything. 


```solidity
Path: ./contracts/libraries/Allowance.sol

25:        token.approve(spender, 0);	// @audit-issue

34:        try token.approve(spender, value) {	// @audit-issue

41:            token.approve(spender, type(uint256).max);	// @audit-issue
```
[25](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L25-L25), [34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L34-L34), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L41-L41), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

191:        tokenToBuy.safeApprove(address(distributor), 0);	// @audit-issue

192:        tokenToBuy.safeApprove(address(distributor), bal);	// @audit-issue
```
[191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L191-L191), [192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L192-L192), 


```solidity
Path: ./contracts/p1/BackingManager.sol

72:        IERC20(address(erc20)).safeApprove(address(rToken), 0);	// @audit-issue

73:        IERC20(address(erc20)).safeApprove(address(rToken), type(uint256).max);	// @audit-issue
```
[72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L72-L72), [73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L73-L73), 


#### Recommendation

To ensure the accuracy of token approvals and prevent potential issues, it's important to check the return values when calling the `approve` function of an `IERC20` contract. While some implementations use a boolean return value to indicate errors, not checking this return value may allow operations to proceed without the intended approval. Always verify the return value to confirm the success of the approval operation.

### Large transfers may not work with some `ERC20` tokens
Not all IERC20 implementations are totally compliant, and some (e.g UNI, COMP) may fail if the valued passed is larger than uint96. [Source](https://github.com/d-xo/weird-erc20#revert-on-large-approvals--transfers)


```solidity
Path: ./contracts/p1/Distributor.sol

170:            IERC20Upgradeable(address(erc20)).safeTransferFrom(	// @audit-issue

183:                    IERC20Upgradeable(address(erc20)).safeTransferFrom(	// @audit-issue
```
[170](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L170-L170), [183](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L183-L183), 


```solidity
Path: ./contracts/p1/BackingManager.sol

215:            IERC20(address(rsr)).safeTransfer(address(stRSR), rsr.balanceOf(address(this)));	// @audit-issue
```
[215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L215-L215), 


```solidity
Path: ./contracts/p1/StRSR.sol

237:        IERC20Upgradeable(address(rsr)).safeTransferFrom(caller, address(this), rsrAmount);	// @audit-issue

337:        IERC20Upgradeable(address(rsr)).safeTransfer(account, rsrAmount);	// @audit-issue

477:        IERC20Upgradeable(address(rsr)).safeTransfer(caller, seizedRSR);	// @audit-issue
```
[237](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L237-L237), [337](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L337-L337), [477](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L477-L477), 


```solidity
Path: ./contracts/p1/RToken.sol

149:            IERC20Upgradeable(erc20s[i]).safeTransferFrom(	// @audit-issue

219:            IERC20Upgradeable(erc20s[i]).safeTransferFrom(	// @audit-issue

327:                IERC20Upgradeable(erc20s[i]).safeTransferFrom(	// @audit-issue

420:        IERC20Upgradeable(address(erc20)).safeTransfer(	// @audit-issue
```
[149](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L149-L149), [219](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L219-L219), [327](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L327-L327), [420](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L420-L420), 


```solidity
Path: ./contracts/p1/Broker.sol

259:        IERC20Upgradeable(address(req.sell.erc20())).safeTransferFrom(	// @audit-issue

287:        IERC20Upgradeable(address(req.sell.erc20())).safeTransferFrom(	// @audit-issue
```
[259](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L259-L259), [287](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L287-L287), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

205:        if (sellBal != 0) IERC20Upgradeable(address(sell)).safeTransfer(origin, sellBal);	// @audit-issue

206:        if (boughtAmt != 0) IERC20Upgradeable(address(buy)).safeTransfer(origin, boughtAmt);	// @audit-issue

227:        IERC20Upgradeable(address(erc20)).safeTransfer(origin, erc20.balanceOf(address(this)));	// @audit-issue
```
[205](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L205-L205), [206](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L206-L206), [227](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L227-L227), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

219:        buy.safeTransferFrom(msg.sender, address(this), amountIn);	// @audit-issue

255:        sell.safeTransfer(bidder, lot()); // {qSellTok}	// @audit-issue

286:            sell.safeTransfer(bidder, soldAmt); // {qSellTok}	// @audit-issue

291:        buy.safeTransfer(address(origin), boughtAmt); // {qBuyTok}	// @audit-issue

292:        sell.safeTransfer(address(origin), sell.balanceOf(address(this))); // {qSellTok}	// @audit-issue

300:        erc20.safeTransfer(address(origin), erc20.balanceOf(address(this)));	// @audit-issue
```
[219](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L219-L219), [255](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L255-L255), [286](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L286-L286), [291](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L291-L291), [292](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L292-L292), [300](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L300-L300), 


#### Recommendation

When transferring tokens using ERC-20 contracts, such as UNI or COMP, it's important to be aware that not all IERC20 implementations are fully compliant, and they may not support large transfer values exceeding uint96. To avoid potential issues, it's essential to check the documentation or source code of the specific token you're using and ensure that your transfers adhere to the token's limitations.

### Division in comparison
To ensure accuracy in comparisons within programming, especially when dealing with integers, it's often more efficient to use multiplication rather than division. This approach stems from the fact that division operations are generally slower and more complex than multiplication. And in the context of solidity they can cause precision errors.

Suppose you want to compare if a/b is greater than c/d (where a, b, c, and d are integers). Instead of performing division, which is prone to precision errors, you can cross-multiply to avoid division. The comparison a/b > c/d is equivalent to ad > bc. This way, you only use multiplication, which is faster and avoids potential inaccuracies or complexities associated with division.

```solidity
Path: ./contracts/libraries/Fixed.sol

511:            if (rawDelta / b != a) return FIX_MAX;	// @audit-issue

534:            if (shiftDelta / FIX_ONE > FIX_MAX) return FIX_MAX;	// @audit-issue
```
[511](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L511-L511), [534](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L534-L534), 


#### Recommendation

When comparing ratios or fractions in Solidity, avoid direct division operations within the comparison. Instead, cross-multiply to maintain precision and reduce computational complexity. For example, to compare if `a/b` is greater than `c/d`, where `a`, `b`, `c`, and `d` are integers, reframe the comparison as `a * d > b * c`. This approach avoids the division entirely, eliminating the risk of precision loss due to integer division truncation. Additionally, this method is more gas-efficient, as multiplication operations are cheaper than division. Apply this strategy in all relevant comparisons to enhance the accuracy and performance of your smart contracts. Always test thoroughly to ensure that the logic remains correct after the transformation.

### Large approvals may not work with some `ERC20` tokens
Not all IERC20 implementations are totally compliant, and some (e.g UNI, COMP) may fail if the valued passed is larger than uint96. [Source](https://github.com/d-xo/weird-erc20#revert-on-large-approvals--transfers)

```solidity
Path: ./contracts/libraries/Allowance.sol

25:        token.approve(spender, 0);	// @audit-issue

34:        try token.approve(spender, value) {	// @audit-issue

41:            token.approve(spender, type(uint256).max);	// @audit-issue
```
[25](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L25-L25), [34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L34-L34), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L41-L41), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

191:        tokenToBuy.safeApprove(address(distributor), 0);	// @audit-issue

192:        tokenToBuy.safeApprove(address(distributor), bal);	// @audit-issue
```
[191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L191-L191), [192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L192-L192), 


```solidity
Path: ./contracts/p1/BackingManager.sol

72:        IERC20(address(erc20)).safeApprove(address(rToken), 0);	// @audit-issue

73:        IERC20(address(erc20)).safeApprove(address(rToken), type(uint256).max);	// @audit-issue
```
[72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L72-L72), [73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L73-L73), 


#### Recommendation

When working with ERC-20 tokens, particularly tokens like UNI or COMP, be aware that not all IERC20 implementations are fully compliant, and they may not support large approval values exceeding uint96. To avoid potential issues, it's essential to check the documentation or source code of the specific token you're interacting with and ensure that your approvals conform to the token's limitations.

### Unsafe ERC20 operation `approve()`
Approve call do not handle non-standard erc20 behavior. Use `safeApprove` instead of `approve`.
- Some token contracts do not return any value.
- Some token contracts revert the transaction when the allowance is not zero.

```solidity
Path: ./contracts/libraries/Allowance.sol

25:        token.approve(spender, 0);	// @audit-issue

34:        try token.approve(spender, value) {	// @audit-issue

41:            token.approve(spender, type(uint256).max);	// @audit-issue
```
[25](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L25-L25), [34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L34-L34), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L41-L41), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

191:        tokenToBuy.safeApprove(address(distributor), 0);	// @audit-issue

192:        tokenToBuy.safeApprove(address(distributor), bal);	// @audit-issue
```
[191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L191-L191), [192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L192-L192), 


```solidity
Path: ./contracts/p1/BackingManager.sol

72:        IERC20(address(erc20)).safeApprove(address(rToken), 0);	// @audit-issue

73:        IERC20(address(erc20)).safeApprove(address(rToken), type(uint256).max);	// @audit-issue
```
[72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L72-L72), [73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L73-L73), 


#### Recommendation

Use `safeApprove` instead of `approve`

### File allows a version of solidity that is susceptible to `.selector`-related optimizer bug
In solidity versions prior to 0.8.21, there is a legacy code generation [bug](https://soliditylang.org/blog/2023/07/19/missing-side-effects-on-selector-access-bug/) where if `foo().selector` is called, `foo()` doesn't actually get evaluated. It is listed as low-severity, because projects usually use the contract name rather than a function call to look up the selector. I've flagged all files using `.selector` where the version is vulnerable.

```solidity
Path: ./contracts/p1/Main.sol

159:            abi.encodeWithSelector(UUPSUpgradeable.upgradeTo.selector, implementation)	// @audit-issue
```
[159](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L159-L159), 


#### Recommendation

Review your Solidity contracts for usage of the `.selector` property in combination with function calls, particularly if your project is compiled with a Solidity version prior to 0.8.21. To mitigate the risk associated with this optimizer bug.

### Possible loss of precision
Division by large numbers may result in precision loss due to rounding down, or even the result being erroneously equal to zero. Consider adding checks on the numerator to ensure precision loss is handled appropriately.


```solidity
Path: ./contracts/libraries/Throttle.sol

75:        available = throttle.lastAvailable + (limit * delta) / ONE_HOUR;	// @audit-issue

88:        limit = (supply * params.pctRate) / FIX_ONE_256; // {qRTok}	// @audit-issue
```
[75](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L75-L75), [88](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L88-L88), 


```solidity
Path: ./contracts/libraries/Fixed.sol

324:            if (y & 1 == 1) result = (result * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;	// @audit-issue

327:            x = (x * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;	// @audit-issue

329:        return _safeWrap(result / FIX_SCALE);	// @audit-issue

473:        return mulDiv(x, y, z, FLOOR);	// @audit-issue

511:            if (rawDelta / b != a) return FIX_MAX;	// @audit-issue

534:            if (shiftDelta / FIX_ONE > FIX_MAX) return FIX_MAX;	// @audit-issue

537:            return uint192(shiftDelta / FIX_ONE); // {D18} = {D36} / {D18}	// @audit-issue

582:            c_256 /= pow2;	// @audit-issue

583:            lo /= pow2;	// @audit-issue

584:            lo += hi * ((0 - pow2) / pow2 + 1);	// @audit-issue
```
[324](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L324-L324), [327](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L327-L327), [329](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L329-L329), [473](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L473-L473), [511](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L511-L511), [534](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L534-L534), [537](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L537-L537), [582](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L582-L582), [583](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L583-L583), [584](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L584-L584), 


```solidity
Path: ./contracts/p1/Distributor.sol

133:            if (totalShares != 0) tokensPerShare = amount / totalShares;	// @audit-issue

222:                    (feeNumerator * uint256(revTotals.rTokenTotal + revTotals.rsrTotal)) /	// @audit-issue
223:                        (feeDenominator - feeNumerator)
```
[133](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L133-L133), [222](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L222-L223), 


```solidity
Path: ./contracts/p1/BackingManager.sol

247:                uint256 tokensPerShare = delta / (totals.rTokenTotal + totals.rsrTotal);	// @audit-issue
```
[247](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L247-L247), 


```solidity
Path: ./contracts/p1/StRSR.sol

274:        uint256 newStakeRSR = (FIX_ONE_256 * totalStakes + (stakeRate - 1)) / stakeRate;	// @audit-issue

326:        uint256 newDraftRSR = (newTotalDrafts * FIX_ONE_256 + (draftRate - 1)) / draftRate;	// @audit-issue

373:        uint256 newDraftRSR = (newTotalDrafts * FIX_ONE_256 + (draftRate - 1)) / draftRate;	// @audit-issue

441:        uint256 stakeRSRToTake = (stakeRSR * rsrAmount + (rsrBalance - 1)) / rsrBalance;	// @audit-issue

448:            stakeRate = uint192((FIX_ONE_256 * totalStakes + (stakeRSR - 1)) / stakeRSR);	// @audit-issue

456:        uint256 draftRSRToTake = (draftRSR * rsrAmount + (rsrBalance - 1)) / rsrBalance;	// @audit-issue

463:            draftRate = uint192((FIX_ONE_256 * totalDrafts + (draftRSR - 1)) / draftRSR);	// @audit-issue

472:        seizedRSR += (rewards * rsrAmount + (rsrBalance - 1)) / rsrBalance;	// @audit-issue

504:        return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method	// @audit-issue

609:            payout = (payoutRatio * rsrRewardsAtLastPayout) / FIX_ONE;	// @audit-issue

623:            : uint192((totalStakes * FIX_ONE_256 + (stakeRSR - 1)) / stakeRSR);	// @audit-issue

648:        uint256 newTotalDrafts = (draftRate * draftRSR) / FIX_ONE;	// @audit-issue

705:        uint192 withdrawal = _safeWrap((rsrWithdrawal * FIX_ONE + totalRSR - 1) / totalRSR); // {1}	// @audit-issue

729:        uint256 newTotalStakes = (stakeRate * newStakeRSR) / FIX_ONE;	// @audit-issue
```
[274](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L274-L274), [326](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L326-L326), [373](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L373-L373), [441](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L441-L441), [448](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L448-L448), [456](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L456-L456), [463](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L463-L463), [472](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L472-L472), [504](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L504-L504), [609](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L609-L609), [623](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L623-L623), [648](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L648-L648), [705](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L705-L705), [729](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L729-L729), 


```solidity
Path: ./contracts/p1/RToken.sol

409:        uint256 low = (FIX_ONE_256 * basketsNeeded_) / supply; // D18{BU/rTok}	// @audit-issue

410:        uint256 high = (FIX_ONE_256 * basketsNeeded_ + (supply - 1)) / supply; // D18{BU/rTok}	// @audit-issue
```
[409](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L409-L409), [410](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L410-L410), 


```solidity
Path: ./contracts/p1/mixins/TradeLib.sol

137:        uint192 exactSellAmount = trade.buyAmount.mulDiv(	// @audit-issue
```
[137](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L137-L137), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

168:                        uint256(low.mulDiv(anchor - ctx.bals[i], buPriceHigh, FLOOR))	// @audit-issue

220:            uoaBottom.mulDiv(FIX_ONE.minus(ctx.maxTradeSlippage), buPriceHigh, FLOOR);	// @audit-issue
```
[168](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L168-L168), [220](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L220-L220), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

371:            newPrice += targetAmts[i].mulDiv(	// @audit-issue

382:            newTargetAmts[i] = targetAmts[i].mulDiv(price, newPrice, CEIL);	// @audit-issue
```
[371](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L371-L371), [382](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L382-L382), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

126:            minBuyAmount / MAX_ORDERS,	// @audit-issue
```
[126](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L126-L126), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

185:        uint192 _worstPrice = prices.sellLow.mulDiv(	// @audit-issue

330:            uint192 exp = MAX_EXP.mulDiv(TWENTY_PERCENT - progression, TWENTY_PERCENT, ROUND);	// @audit-issue

335:            return bestPrice.mulDiv(ONE_POINT_FIVE, BASE.powu(uint48(exp.toUint(ROUND))), CEIL);	// @audit-issue

346:                (highPrice - _bestPrice).mulDiv(progression - TWENTY_PERCENT, TWENTY_FIVE_PERCENT);	// @audit-issue

355:                (_bestPrice - worstPrice).mulDiv(progression - FORTY_FIVE_PERCENT, FIFTY_PERCENT);	// @audit-issue
```
[185](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L185-L185), [330](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L330-L330), [335](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L335-L335), [346](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L346-L346), [355](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L355-L355), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

82:        return (asMicroPercent * pastSupply + (ONE_HUNDRED_PERCENT - 1)) / ONE_HUNDRED_PERCENT;	// @audit-issue
```
[82](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L82-L82), 


#### Recommendation

Incorporate strategies in your Solidity contracts to mitigate precision loss in division operations. This can include:
1. Performing checks on the numerator and denominator to ensure they are within a reasonable range to avoid significant rounding errors.
2. Considering the use of fixed-point arithmetic libraries or scaling factors to handle divisions with higher precision.
3. Clearly documenting any inherent limitations of your division logic and providing guidelines for inputs to minimize unexpected behavior.
Always thoroughly test division operations under various scenarios to ensure that the outcomes are consistent with your contract's intended logic and accuracy requirements.

### Library function isn't `internal` or `private`
In a library, using an external or public visibility means that we won't be going through the library with a DELEGATECALL but with a CALL. This changes the context and should be done carefully.

```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

33:    function prepareRecollateralizationTrade(TradingContext memory ctx, Registry memory reg)	// @audit-issue
```
[33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L33-L33), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

164:    function nextBasket(	// @audit-issue

316:    function requireConstantConfigTargets(	// @audit-issue

351:    function normalizeByPrice(	// @audit-issue
```
[164](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L164-L164), [316](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L316-L316), [351](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L351-L351), 


#### Recommendation

Carefully assess the intended use of functions within your libraries and opt for `internal` or `private` visibility unless there's a specific reason to expose them via `external` or `public`. This practice encourages the use of `DELEGATECALL` where appropriate, maintaining the execution context of the calling contract and ensuring consistent access to storage variables.
### `approve` can revert if the current approval is not zero
Some tokens like USDT check for the current approval, and they revert if it's not zero. While Tether is known to do this, it applies to other tokens as well, which are trying to protect against [this](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit) attack vector.

```solidity
Path: ./contracts/libraries/Allowance.sol

25:        token.approve(spender, 0);	// @audit-issue

34:        try token.approve(spender, value) {	// @audit-issue

41:            token.approve(spender, type(uint256).max);	// @audit-issue
```
[25](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L25-L25), [34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L34-L34), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L41-L41), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

191:        tokenToBuy.safeApprove(address(distributor), 0);	// @audit-issue

192:        tokenToBuy.safeApprove(address(distributor), bal);	// @audit-issue
```
[191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L191-L191), [192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L192-L192), 


```solidity
Path: ./contracts/p1/BackingManager.sol

72:        IERC20(address(erc20)).safeApprove(address(rToken), 0);	// @audit-issue

73:        IERC20(address(erc20)).safeApprove(address(rToken), type(uint256).max);	// @audit-issue
```
[72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L72-L72), [73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L73-L73), 


#### Recommendation

When working with tokens that may revert when calling the `approve` function, be cautious and ensure that the current approval is set to zero before attempting to set a new approval. Tokens like USDT and others may follow this behavior to protect against potential attack vectors. Always check the token's documentation and handle approvals accordingly to avoid unexpected reverts.

### `approve` will always revert as the `IERC20` interface mismatch
Some tokens, such as [USDT](https://etherscan.io/token/0xdac17f958d2ee523a2206206994597c13d831ec7#code#L199), have a different implementation for the approve function: when the address is cast to a compliant `IERC20` interface and the approve function is used, it will always revert due to the interface mismatch.

```solidity
Path: ./contracts/libraries/Allowance.sol

25:        token.approve(spender, 0);	// @audit-issue

34:        try token.approve(spender, value) {	// @audit-issue

41:            token.approve(spender, type(uint256).max);	// @audit-issue
```
[25](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L25-L25), [34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L34-L34), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L41-L41), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

191:        tokenToBuy.safeApprove(address(distributor), 0);	// @audit-issue

192:        tokenToBuy.safeApprove(address(distributor), bal);	// @audit-issue
```
[191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L191-L191), [192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L192-L192), 


```solidity
Path: ./contracts/p1/BackingManager.sol

72:        IERC20(address(erc20)).safeApprove(address(rToken), 0);	// @audit-issue

73:        IERC20(address(erc20)).safeApprove(address(rToken), type(uint256).max);	// @audit-issue
```
[72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L72-L72), [73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L73-L73), 


#### Recommendation

When interacting with tokens like USDT that have a different implementation for the `approve` function, ensure that you use the correct interface and method to avoid reverts due to interface mismatches. Instead of casting to `IERC20`, use the specific interface that matches the token's implementation. Always review the token's documentation or contract to determine the correct interface and method to use when working with such tokens.

### Non-compliant `IERC20` tokens may revert with `transfer`
Some `IERC20` tokens (e.g. `BNB`, `OMG`, `USDT`) do not implement the standard properly, but they are still accepted by most code that accepts `ERC20` tokens.

For example, `USDT` transfer functions on L1 do not return booleans: when casted to `IERC20`, their function signatures do not match, and therefore the calls made will revert.

```solidity
Path: ./contracts/p1/Distributor.sol

170:            IERC20Upgradeable(address(erc20)).safeTransferFrom(	// @audit-issue

183:                    IERC20Upgradeable(address(erc20)).safeTransferFrom(	// @audit-issue
```
[170](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L170-L170), [183](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L183-L183), 


```solidity
Path: ./contracts/p1/BackingManager.sol

215:            IERC20(address(rsr)).safeTransfer(address(stRSR), rsr.balanceOf(address(this)));	// @audit-issue
```
[215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L215-L215), 


```solidity
Path: ./contracts/p1/StRSR.sol

237:        IERC20Upgradeable(address(rsr)).safeTransferFrom(caller, address(this), rsrAmount);	// @audit-issue

337:        IERC20Upgradeable(address(rsr)).safeTransfer(account, rsrAmount);	// @audit-issue

477:        IERC20Upgradeable(address(rsr)).safeTransfer(caller, seizedRSR);	// @audit-issue
```
[237](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L237-L237), [337](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L337-L337), [477](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L477-L477), 


```solidity
Path: ./contracts/p1/RToken.sol

149:            IERC20Upgradeable(erc20s[i]).safeTransferFrom(	// @audit-issue

219:            IERC20Upgradeable(erc20s[i]).safeTransferFrom(	// @audit-issue

327:                IERC20Upgradeable(erc20s[i]).safeTransferFrom(	// @audit-issue

420:        IERC20Upgradeable(address(erc20)).safeTransfer(	// @audit-issue
```
[149](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L149-L149), [219](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L219-L219), [327](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L327-L327), [420](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L420-L420), 


```solidity
Path: ./contracts/p1/Broker.sol

259:        IERC20Upgradeable(address(req.sell.erc20())).safeTransferFrom(	// @audit-issue

287:        IERC20Upgradeable(address(req.sell.erc20())).safeTransferFrom(	// @audit-issue
```
[259](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L259-L259), [287](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L287-L287), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

205:        if (sellBal != 0) IERC20Upgradeable(address(sell)).safeTransfer(origin, sellBal);	// @audit-issue

206:        if (boughtAmt != 0) IERC20Upgradeable(address(buy)).safeTransfer(origin, boughtAmt);	// @audit-issue

227:        IERC20Upgradeable(address(erc20)).safeTransfer(origin, erc20.balanceOf(address(this)));	// @audit-issue
```
[205](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L205-L205), [206](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L206-L206), [227](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L227-L227), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

219:        buy.safeTransferFrom(msg.sender, address(this), amountIn);	// @audit-issue

255:        sell.safeTransfer(bidder, lot()); // {qSellTok}	// @audit-issue

286:            sell.safeTransfer(bidder, soldAmt); // {qSellTok}	// @audit-issue

291:        buy.safeTransfer(address(origin), boughtAmt); // {qBuyTok}	// @audit-issue

292:        sell.safeTransfer(address(origin), sell.balanceOf(address(this))); // {qSellTok}	// @audit-issue

300:        erc20.safeTransfer(address(origin), erc20.balanceOf(address(this)));	// @audit-issue
```
[219](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L219-L219), [255](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L255-L255), [286](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L286-L286), [291](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L291-L291), [292](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L292-L292), [300](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L300-L300), 


#### Recommendation

When dealing with tokens that may not fully comply with the `IERC20` standard, it's important to exercise caution and carefully verify the behavior of the specific token in question. In cases where tokens do not return booleans for transfer functions, consider implementing additional error-handling mechanisms to account for potential failures. This may include checking the token balance before and after the transfer to detect any discrepancies or using try-catch blocks to handle potential exceptions. Ensuring robust error handling can help your smart contract interact more gracefully with non-compliant tokens while maintaining security and reliability.

### Floating Pragma
A "floating pragma" in Solidity refers to the practice of using a pragma statement that does not specify a fixed compiler version but instead allows the contract to be compiled with any compatible compiler version. This issue arises when pragma statements like `pragma solidity ^0.8.0;` are used without a specific version number, allowing the contract to be compiled with the latest available compiler version. This can lead to various compatibility and stability issues.


```solidity
Path: ./contracts/libraries/Fixed.sol

4:pragma solidity ^0.8.19;	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L4-L4), 


#### Recommendation

Consider locking the pragma version whenever possible and avoid using a floating pragma in the final deployment. [Consider known bugs](https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

### Unneeded initializations of bool variable to `false`.
In Solidity, it is common practice to initialize variables with default values when declaring them. However, initializing `bool`variables to `false` when they are not subsequently used in the code can lead to unnecessary gas consumption and code clutter. This issue points out instances where such initializations are present but serve no functional purpose.

```solidity
Path: ./contracts/p1/Distributor.sol

142:        bool accountRewards = false;	// @audit-issue
```
[142](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L142-L142), 


#### Recommendation


It is recommended not to initialize boolean variables to `false` to save some Gas.


### Unneeded initializations of integer variable to `0`.
In Solidity, it is common practice to initialize variables with default values when declaring them. However, initializing integer variables to `0` when they are not subsequently used in the code can lead to unnecessary gas consumption and code clutter. This issue points out instances where such initializations are present but serve no functional purpose.

```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

39:        for (uint256 i = 0; i < validForVersions.length; ++i) {	// @audit-issue

67:        for (uint256 i = 0; i < _versionHashes.length; ++i) {	// @audit-issue

95:        for (uint256 i = 0; i < _assets.length; ++i) {	// @audit-issue
```
[39](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L39-L39), [67](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L67-L67), [95](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L95-L95), 


```solidity
Path: ./contracts/libraries/Array.sol

12:            for (uint256 j = 0; j < i; ++j) {	// @audit-issue
```
[12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L12-L12), 


```solidity
Path: ./contracts/libraries/String.sol

14:        for (uint256 i = 0; i < bStr.length; i++) {	// @audit-issue
```
[14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/String.sol#L14-L14), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

89:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

127:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

142:            for (uint256 i = 0; i < len; ++i) {	// @audit-issue

153:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue
```
[89](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L89-L89), [127](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L127-L127), [142](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L142-L142), [153](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L153-L153), 


```solidity
Path: ./contracts/p1/Distributor.sol

92:        for (uint256 i = 0; i < dests.length; ++i) {	// @audit-issue

145:        for (uint256 i = 0; i < destinations.length(); ++i) {	// @audit-issue

169:        for (uint256 i = 0; i < numTransfers; ++i) {	// @audit-issue

206:        for (uint256 i = 0; i < length; ++i) {	// @audit-issue
```
[92](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L92-L92), [145](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L145-L145), [169](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L169-L169), [206](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L206-L206), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

48:        for (uint256 i = 0; i < length; ++i) {	// @audit-issue

63:        for (uint256 i = 0; i < length; ++i) {	// @audit-issue

151:        for (uint256 i = 0; i < length; ++i) {	// @audit-issue

163:        for (uint256 i = 0; i < length; ++i) {	// @audit-issue

176:            for (uint256 i = 0; i < assetLen; ++i) {	// @audit-issue
```
[48](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L48-L48), [63](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L63-L63), [151](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L151-L151), [163](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L163-L163), [176](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L176-L176), 


```solidity
Path: ./contracts/p1/BackingManager.sol

236:        for (uint256 i = 0; i < length; ++i) {	// @audit-issue

289:        for (uint256 i = 0; i < reg.erc20s.length; ++i) {	// @audit-issue

294:        for (uint256 i = 0; i < reg.erc20s.length; ++i) {	// @audit-issue
```
[236](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L236-L236), [289](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L289-L289), [294](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L294-L294), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

142:        for (uint256 i = 0; i < len; ++i) refAmts[i] = basket.refAmts[basket.erc20s[i]];	// @audit-issue

251:        for (uint256 i = 0; i < config.erc20s.length; ++i) {	// @audit-issue

260:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

296:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

321:        for (uint256 i = 0; i < size; ++i) {	// @audit-issue

424:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

491:        for (uint256 i = 0; i < length; ++i) {	// @audit-issue

554:                for (uint256 k = 0; k < len; ++k) {	// @audit-issue

547:            for (uint256 j = 0; j < b.erc20s.length; ++j) {	// @audit-issue

535:        for (uint48 i = 0; i < basketNonces.length; ++i) {	// @audit-issue

593:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

615:        for (uint256 i = 0; i < length; ++i) {	// @audit-issue

676:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

684:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

713:        for (uint256 i = 0; i < b.erc20s.length; ++i) {	// @audit-issue

751:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

769:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue
```
[142](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L142-L142), [251](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L251-L251), [260](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L260-L260), [296](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L296-L296), [321](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L321-L321), [424](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L424-L424), [491](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L491-L491), [554](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L554-L554), [547](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L547-L547), [535](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L535-L535), [593](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L593-L593), [615](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L615-L615), [676](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L676-L676), [684](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L684-L684), [713](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L713-L713), [751](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L751-L751), [769](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L769-L769), 


```solidity
Path: ./contracts/p1/RToken.sol

148:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

214:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

269:        for (uint256 i = 0; i < portions.length; ++i) {	// @audit-issue

297:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

311:        for (uint256 i = 0; i < expectedERC20sOut.length; ++i) {	// @audit-issue

321:            for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

339:        for (uint256 i = 0; i < expectedERC20sOut.length; ++i) {	// @audit-issue
```
[148](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L148-L148), [214](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L214-L214), [269](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L269-L269), [297](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L297-L297), [311](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L311-L311), [321](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L321-L321), [339](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L339-L339), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

136:        uint256 low = 0;	// @audit-issue
```
[136](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L136-L136), 


```solidity
Path: ./contracts/p1/mixins/RewardableLib.sol

27:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue
```
[27](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L27-L27), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

139:        for (uint256 i = 0; i < reg.erc20s.length; ++i) {	// @audit-issue

288:        for (uint256 i = 0; i < reg.erc20s.length; ++i) {	// @audit-issue
```
[139](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L139-L139), [288](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L288-L288), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

72:        for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;	// @audit-issue

80:        for (uint256 i = 0; i < length; ++i) {	// @audit-issue

180:        for (uint256 i = 0; i < config.erc20s.length; ++i) {	// @audit-issue

198:        for (uint256 i = 0; i < config.erc20s.length; ++i) {	// @audit-issue

248:            uint256 size = 0; // backup basket size	// @audit-issue

252:            for (uint256 j = 0; j < backup.erc20s.length && size < backup.max; ++j) {	// @audit-issue

260:            uint256 assigned = 0;	// @audit-issue

263:            for (uint256 j = 0; j < backup.erc20s.length && assigned < size; ++j) {	// @audit-issue

241:        for (uint256 i = 0; i < targetsLength; ++i) {	// @audit-issue

325:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

337:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

363:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

380:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue
```
[72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L72-L72), [80](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L80-L80), [180](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L180-L180), [198](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L198-L198), [248](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L248-L248), [252](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L252-L252), [260](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L260-L260), [263](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L263-L263), [241](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L241-L241), [325](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L325-L325), [337](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L337-L337), [363](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L363-L363), [380](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L380-L380), 


#### Recommendation


It is recommended not to initialize integer variables to `0` to save some Gas.


### Use transfer libraries instead of low level calls
Consider using `SafeTransferLib.safeTransferETH` or `Address.sendValue` for clearer semantic meaning instead of using a low level call.

```solidity
Path: ./contracts/p1/Main.sol

158:        (bool success, ) = proxy.call(	// @audit-issue
```
[158](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L158-L158), 


#### Recommendation

To improve code readability and safety, consider using higher-level transfer libraries like `SafeTransferLib.safeTransferETH` or `Address.sendValue` instead of low-level calls for handling Ether transfers. These libraries provide clearer semantic meaning and help prevent common pitfalls associated with low-level calls.

### Unused arguments should be removed or implemented
In Solidity, functions often have arguments that are intended for specific operations or logic within the function. However, sometimes these arguments remain unused in the function body, either due to changes during development or oversight. Unused arguments in functions can lead to confusion, making the code less readable and potentially misleading for other developers who might use or audit the contract. Moreover, they can create a false impression of the function's purpose and behavior. It's crucial to either implement these arguments in the function's logic as originally intended or remove them to ensure clarity and efficiency of the code.

```solidity
Path: ./contracts/p1/Main.sol

153:    function _authorizeUpgrade(address) internal view override {	// @audit-issue: None
```
[153](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L153-L153), 


```solidity
Path: ./contracts/p1/StRSR.sol

906:        uint256	// @audit-issue: None
```
[906](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L906-L906), 


```solidity
Path: ./contracts/p1/RToken.sol

533:        uint256	// @audit-issue: None
```
[533](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L533-L533), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

67:    function _authorizeUpgrade(address newImplementation) internal view override onlyMain {}	// @audit-issue: newImplementation
```
[67](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L67-L67), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

169:        bytes memory /*params*/	// @audit-issue: None
```
[169](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L169-L169), 


#### Recommendation

Eliminate unused arguments in Solidity function definitions to enhance code clarity and efficiency. If an argument is currently not utilized in the function's logic, assess its potential future utility. If it serves no purpose, removing it simplifies the function signature and aligns the code more closely with its actual operation, contributing to a cleaner and more understandable contract structure.

### `else`-block not required
One level of nesting can be removed by not having an `else` block when the `if`-block returns, and `if (foo) { return 1; } else { return 2; }` becomes `if (foo) { return 1; } return 2;`


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

389:        } else if (curr.surplusStatus == CollateralStatus.SOUND) {
390:            return
391:                other == CollateralStatus.DISABLED ||
392:                (other == CollateralStatus.SOUND && surplusAmt.gt(curr.surplus));
393:        } else {	// @audit-issue
394:            // curr is IFFY
395:            return other != CollateralStatus.IFFY || surplusAmt.gt(curr.surplus);
396:        }
```
[393](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L389-L396), 


#### Recommendation

Consider simplifying code by removing unnecessary `else` blocks when the `if` block returns. You can achieve cleaner and more concise code by directly returning a value after the `if` block instead of using an `else` block for a subsequent return statement.

### Unused import
The identifier is imported but never used within the file.

```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

4:import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";	// @audit-issue: Ownable not used in the contract.
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L4-L4), 


#### Recommendation

Regularly review your Solidity code to remove unused imports. This practice declutters the codebase, making it easier to understand and maintain. In addition, consider using tools or IDE features that can automatically detect and highlight unused imports for cleanup. Keeping imports limited to only what is necessary helps maintain a clear understanding of the contract's dependencies and reduces potential confusion for developers working on or reviewing the code.

### Excessive Authorization in Contract Functions
A contract where a high percentage of functions require authorization (e.g., restricted to the contract owner or specific roles) may indicate over-centralization or excessive control. This could limit the contract's flexibility, reduce trust among users, and potentially create bottleneck points that could be exploited or become failure points. While some level of control is necessary for administrative purposes, overly restrictive access can detract from the decentralized nature of blockchain applications and concentrate too much power in the hands of a few.

```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

9:contract DAOFeeRegistry {	// @audit-issue: %100.0 amount of external/public and non-view/non-pure functions are required authorization to call.
	List of total functions: `setFeeRecipient`, `resetRTokenFee`, `setDefaultFeeNumerator`, `setRTokenFeeNumerator`
	List of functions that require authorization: `setFeeRecipient`, `resetRTokenFee`, `setDefaultFeeNumerator`, `setRTokenFeeNumerator`
	List of functions that doesn't require authorization: None
```
[9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L9-L9), 


```solidity
Path: ./contracts/mixins/Auth.sol

16:abstract contract Auth is AccessControlUpgradeable, IAuth {	// @audit-issue: %100.0 amount of external/public and non-view/non-pure functions are required authorization to call.
	List of total functions: `freezeShort`, `setLongFreeze`, `unpauseIssuance`, `freezeForever`, `freezeLong`, `unfreeze`, `unpauseTrading`, `setShortFreeze`, `pauseIssuance`, `grantRole`, `pauseTrading`
	List of functions that require authorization: `freezeShort`, `setLongFreeze`, `unpauseIssuance`, `freezeForever`, `freezeLong`, `unfreeze`, `unpauseTrading`, `setShortFreeze`, `pauseIssuance`, `grantRole`, `pauseTrading`
	List of functions that doesn't require authorization: None
```
[16](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L16-L16), 


```solidity
Path: ./contracts/p1/Main.sol

22:contract MainP1 is Versioned, Initializable, Auth, ComponentRegistry, UUPSUpgradeable, IMain {	// @audit-issue: %71.42857142857143 amount of external/public and non-view/non-pure functions are required authorization to call.
	List of total functions: `setVersionRegistry`, `setDAOFeeRegistry`, `setAssetPluginRegistry`, `init`, `upgradeMainTo`, `poke`, `upgradeRTokenTo`
	List of functions that require authorization: `setVersionRegistry`, `setDAOFeeRegistry`, `setAssetPluginRegistry`, `upgradeMainTo`, `upgradeRTokenTo`
	List of functions that doesn't require authorization: `init`, `poke`
```
[22](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L22-L22), 


#### Recommendation

Make contract more decentralized.

### `public` functions not called by the contract should be declared `external` instead
In Solidity, function visibility is an important aspect that determines how and where a function can be called from. Two commonly used visibilities are `public` and `external`. A `public` function can be called both from other functions inside the same contract and from outside transactions, while an `external` function can only be called from outside the contract.
A potential pitfall in smart contract development is the misuse of the `public` keyword for functions that are only meant to be accessed externally. When a function is not used internally within a contract and is only intended for external calls, it should be labeled as `external` rather than `public`. Using `public` unnecessarily can introduce potential vulnerabilities and also make the contract consume more gas than required. This is because `public` functions have to add additional code to handle both internal and external calls, while `external` functions can be more optimized since they only handle external calls.


```solidity
Path: ./contracts/registry/RoleRegistry.sol

18:    function isOwner(address account) public view returns (bool) {	// @audit-issue

22:    function isEmergencyCouncil(address account) public view returns (bool) {	// @audit-issue

26:    function isOwnerOrEmergencyCouncil(address account) public view returns (bool) {	// @audit-issue
```
[18](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/RoleRegistry.sol#L18-L18), [22](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/RoleRegistry.sol#L22-L22), [26](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/RoleRegistry.sol#L26-L26), 


```solidity
Path: ./contracts/mixins/Auth.sol

99:    function frozen() public view returns (bool) {	// @audit-issue

105:    function tradingPausedOrFrozen() public view returns (bool) {	// @audit-issue

111:    function issuancePausedOrFrozen() public view returns (bool) {	// @audit-issue
```
[99](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L99-L99), [105](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L105-L105), [111](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L111-L111), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

94:    function settleTrade(IERC20 sell) public virtual nonReentrant returns (ITrade trade) {	// @audit-issue
```
[94](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L94-L94), 


```solidity
Path: ./contracts/p1/StRSR.sol

754:    function totalSupply() public view returns (uint256) {	// @audit-issue

772:    function transfer(address to, uint256 amount) public returns (bool) {	// @audit-issue

781:    function approve(address spender, uint256 amount) public returns (bool) {	// @audit-issue

790:    function transferFrom(	// @audit-issue

800:    function increaseAllowance(address spender, uint256 addedValue) public returns (bool) {	// @audit-issue

806:    function decreaseAllowance(address spender, uint256 subtractedValue) public returns (bool) {	// @audit-issue

922:    function permit(	// @audit-issue

942:    function nonces(address owner) public view returns (uint256) {	// @audit-issue

946:    function delegationNonces(address owner) public view returns (uint256) {	// @audit-issue
```
[754](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L754-L754), [772](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L772-L772), [781](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L781-L781), [790](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L790-L790), [800](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L800-L800), [806](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L806-L806), [922](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L922-L922), [942](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L942-L942), [946](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L946-L946), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

343:    function quantity(IERC20 erc20) public view returns (uint192) {	// @audit-issue

359:    function quantityUnsafe(IERC20 erc20, IAsset asset) public view returns (uint192) {	// @audit-issue

419:    function price(bool applyIssuancePremium) public view returns (uint192 low, uint192 high) {	// @audit-issue

482:    function quote(	// @audit-issue

641:    function setIssuancePremiumEnabled(bool val) public {	// @audit-issue
```
[343](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L343-L343), [359](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L359-L359), [419](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L419-L419), [482](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L482-L482), [641](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L641-L641), 


```solidity
Path: ./contracts/p1/RToken.sol

94:    function issue(uint256 amount) public {	// @audit-issue
```
[94](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L94-L94), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

66:    function CLOCK_MODE() public pure returns (string memory) {	// @audit-issue

74:    function checkpoints(address account, uint48 pos) public view returns (Checkpoint memory) {	// @audit-issue

78:    function numCheckpoints(address account) public view returns (uint48) {	// @audit-issue

86:    function getVotes(address account) public view returns (uint256) {	// @audit-issue

91:    function getPastVotes(address account, uint256 timepoint) public view returns (uint256) {	// @audit-issue

98:    function getPastTotalSupply(uint256 timepoint) public view returns (uint256) {	// @audit-issue

105:    function getPastEra(uint256 timepoint) public view returns (uint256) {	// @audit-issue

162:    function delegate(address delegatee) public {	// @audit-issue

166:    function delegateBySig(	// @audit-issue
```
[66](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L66-L66), [74](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L74-L74), [78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L78-L78), [86](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L86-L86), [91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L91-L91), [98](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L98-L98), [105](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L105-L105), [162](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L162-L162), [166](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L166-L166), 


```solidity
Path: ./contracts/p1/Broker.sol

186:    function setBatchTradeImplementation(ITrade newTradeImplementation) public onlyMain {	// @audit-issue

208:    function setDutchTradeImplementation(ITrade newTradeImplementation) public onlyMain {	// @audit-issue
```
[186](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L186-L186), [208](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L208-L208), 


#### Recommendation

To optimize gas usage and improve code clarity, declare functions that are not called internally within the contract and are intended for external access as `external` rather than `public`. This ensures that these functions are only callable externally, reducing unnecessary gas consumption and potential security risks.

### `Internal` Functions Not Called by The Contract Should Be Removed
In Solidity, functions labeled as `internal` are meant to be used only within the contract in which they're defined or in derived contracts. They cannot be accessed from external transactions. While `internal` functions offer modularity and can be leveraged to break down complex logic, it's essential to monitor their relevance during the contract's lifecycle.

A common oversight during smart contract development is the presence of `internal` functions that remain within the contract code but are never called by any other function or part of the contract. These redundant functions occupy space, make the contract's bytecode longer, and, as a consequence, increase the contract deployment cost. Moreover, they can add unnecessary complexity to the code, making it harder to read, maintain, and audit.


```solidity
Path: ./contracts/libraries/Array.sol

21:    function sortedAndAllUnique(IERC20[] memory arr) internal pure returns (bool) {	// @audit-issue
```
[21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L21-L21), 


```solidity
Path: ./contracts/libraries/Throttle.sol

37:    function useAvailable(	// @audit-issue
```
[37](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L37-L37), 


```solidity
Path: ./contracts/libraries/Fixed.sol

183:    function toUint(uint192 x) internal pure returns (uint136) {	// @audit-issue

190:    function toUint(uint192 x, RoundingMode rounding) internal pure returns (uint136) {	// @audit-issue

198:    function shiftl(uint192 x, int8 decimals) internal pure returns (uint192) {	// @audit-issue

206:    function shiftl(	// @audit-issue

223:    function plus(uint192 x, uint192 y) internal pure returns (uint192) {	// @audit-issue

230:    function plusu(uint192 x, uint256 y) internal pure returns (uint192) {	// @audit-issue

237:    function minus(uint192 x, uint192 y) internal pure returns (uint192) {	// @audit-issue

244:    function minusu(uint192 x, uint256 y) internal pure returns (uint192) {	// @audit-issue

252:    function mul(uint192 x, uint192 y) internal pure returns (uint192) {	// @audit-issue

259:    function mul(	// @audit-issue

270:    function mulu(uint192 x, uint256 y) internal pure returns (uint192) {	// @audit-issue

277:    function div(uint192 x, uint192 y) internal pure returns (uint192) {	// @audit-issue

284:    function div(	// @audit-issue

296:    function divu(uint192 x, uint256 y) internal pure returns (uint192) {	// @audit-issue

303:    function divu(	// @audit-issue

332:    function sqrt(uint192 x) internal pure returns (uint192) {	// @audit-issue

337:    function lt(uint192 x, uint192 y) internal pure returns (bool) {	// @audit-issue

341:    function lte(uint192 x, uint192 y) internal pure returns (bool) {	// @audit-issue

345:    function gt(uint192 x, uint192 y) internal pure returns (bool) {	// @audit-issue

349:    function gte(uint192 x, uint192 y) internal pure returns (bool) {	// @audit-issue

353:    function eq(uint192 x, uint192 y) internal pure returns (bool) {	// @audit-issue

357:    function neq(uint192 x, uint192 y) internal pure returns (bool) {	// @audit-issue

364:    function near(	// @audit-issue

380:    function shiftl_toUint(uint192 x, int8 decimals) internal pure returns (uint256) {	// @audit-issue

387:    function shiftl_toUint(	// @audit-issue

406:    function mulu_toUint(uint192 x, uint256 y) internal pure returns (uint256) {	// @audit-issue

413:    function mulu_toUint(	// @audit-issue

424:    function mul_toUint(uint192 x, uint192 y) internal pure returns (uint256) {	// @audit-issue

431:    function mul_toUint(	// @audit-issue

443:    function muluDivu(	// @audit-issue

455:    function muluDivu(	// @audit-issue

468:    function mulDiv(	// @audit-issue

480:    function mulDiv(	// @audit-issue

494:    function safeMul(	// @audit-issue

544:    function safeDiv(	// @audit-issue

561:    function safeMulDiv(	// @audit-issue
```
[183](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L183-L183), [190](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L190-L190), [198](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L198-L198), [206](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L206-L206), [223](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L223-L223), [230](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L230-L230), [237](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L237-L237), [244](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L244-L244), [252](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L252-L252), [259](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L259-L259), [270](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L270-L270), [277](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L277-L277), [284](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L284-L284), [296](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L296-L296), [303](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L303-L303), [332](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L332-L332), [337](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L337-L337), [341](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L341-L341), [345](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L345-L345), [349](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L349-L349), [353](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L353-L353), [357](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L357-L357), [364](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L364-L364), [380](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L380-L380), [387](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L387-L387), [406](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L406-L406), [413](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L413-L413), [424](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L424-L424), [431](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L431-L431), [443](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L443-L443), [455](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L455-L455), [468](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L468-L468), [480](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L480-L480), [494](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L494-L494), [544](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L544-L544), [561](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L561-L561), 


```solidity
Path: ./contracts/p1/mixins/TradeLib.sol

118:    function prepareTradeToCoverDeficit(	// @audit-issue
```
[118](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L118-L118), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

77:    function setFrom(Basket storage self, Basket storage other) internal {	// @audit-issue
```
[77](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L77-L77), 


#### Recommendation

To maintain clean and efficient code, review the contract's `internal` functions to identify and remove any that are no longer in use or serve no purpose. Removing unused `internal` functions can improve code readability, reduce deployment costs, and enhance the overall quality of the smart contract.

### Functions contain the same code
The functions below have the same implementation as is seen in other files. The functions should be refactored into functions of a common base contract.

```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

121:    modifier stateTransition(TradeStatus begin, TradeStatus end) {	// @audit-issue: Seen on line 58 of ./contracts/plugins/trading/GnosisTrade.sol
```
[121](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L121-L121), 


#### Recommendation

Identify and consolidate duplicated code blocks in Solidity contracts into reusable modifiers or functions. This approach streamlines the contract by eliminating redundancy, thereby improving clarity, maintainability, and potentially reducing gas costs. For common checks, consider using modifiers for concise and consistent enforcement of conditions. For reusable logic, encapsulate it in functions to avoid code duplication and simplify future updates or bug fixes.

### `if`-statement can be converted to a ternary
The code can be made more compact while also increasing readability by converting the following `if`-statements to ternaries (e.g. `foo += (x > y) ? a : b`)

```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

116:        if (!isDeprecated[_asset]) {	// @audit-issue
117:            return _isValidAsset[_versionHash][_asset];
118:        }
```
[116](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L116-L118), 


```solidity
Path: ./contracts/libraries/Throttle.sol

52:        if (available != throttle.lastAvailable || available == limit) {	// @audit-issue
53:            throttle.lastTimestamp = uint48(block.timestamp);
54:        }
```
[52](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L52-L54), 


```solidity
Path: ./contracts/libraries/Permit.sol

17:        if (AddressUpgradeable.isContract(owner)) {	// @audit-issue
18:            require(
19:                IERC1271Upgradeable(owner).isValidSignature(hash, abi.encodePacked(r, s, v)) ==
20:                    0x1626ba7e,
21:                "ERC1271: Unauthorized"
22:            );
23:        } else {
24:            require(
25:                SignatureCheckerUpgradeable.isValidSignatureNow(
26:                    owner,
27:                    hash,
28:                    abi.encodePacked(r, s, v)
29:                ),
30:                "ERC20Permit: invalid signature"
31:            );
32:        }
```
[17](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Permit.sol#L17-L32), 


```solidity
Path: ./contracts/libraries/String.sol

16:            if ((uint8(bStr[i]) >= 65) && (uint8(bStr[i]) <= 90)) {	// @audit-issue
17:                // So we add 32 to make it lowercase
18:                bLower[i] = bytes1(uint8(bStr[i]) + 32);
19:            } else {
20:                bLower[i] = bStr[i];
21:            }
```
[16](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/String.sol#L16-L21), 


```solidity
Path: ./contracts/p1/Main.sol

123:        if (preValidation) {	// @audit-issue
124:            // Validate before the upgrade.
125:            assetRegistry.validateCurrentAssets();
126:        }

139:        if (postValidation) {	// @audit-issue
140:            // ...then validate after the upgrade.
141:            assetRegistry.validateCurrentAssets();
142:        }
```
[123](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L123-L126), [139](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L139-L142), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

80:        } else if (address(tokenToBuy) == address(rToken)) {	// @audit-issue
81:            require(revTotals.rTokenTotal == 0, "rTokenTotal > 0");
82:        } else {
83:            // untestable: tokenToBuy is always the RSR or RToken
84:            revert("invalid tokenToBuy");
85:        }
```
[80](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L80-L85), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

214:        if (asset.isCollateral()) {	// @audit-issue
215:            require(
216:                ICollateral(address(asset)).status() == CollateralStatus.SOUND,
217:                "collateral not sound"
218:            );
219:        }

222:        if (address(assetPluginRegistry) != address(0)) {	// @audit-issue
223:            require(
224:                main.assetPluginRegistry().isValidAsset(
225:                    keccak256(abi.encodePacked(this.version())),
226:                    address(asset)
227:                ),
228:                "unsupported asset"
229:            );
230:        }

246:        if (!main.frozen()) {	// @audit-issue
247:            backingManager.grantRTokenAllowance(erc20);
248:        }
```
[214](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L214-L219), [222](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L222-L230), [246](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L246-L248), 


```solidity
Path: ./contracts/p1/BackingManager.sol

222:        if (baskets > rToken.basketsNeeded()) {	// @audit-issue
223:            rToken.mint(baskets - rToken.basketsNeeded());
224:        }
```
[222](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L222-L224), 


```solidity
Path: ./contracts/p1/StRSR.sol

446:        if (stakeRSR != 0) {	// @audit-issue
447:            // Downcast is safe: totalStakes is 1e38 at most so expression maximum value is 1e56
448:            stakeRate = uint192((FIX_ONE_256 * totalStakes + (stakeRSR - 1)) / stakeRSR);
449:        }

461:        if (draftRSR != 0) {	// @audit-issue
462:            // Downcast is safe: totalDrafts is 1e38 at most so expression maximum value is 1e56
463:            draftRate = uint192((FIX_ONE_256 * totalDrafts + (draftRSR - 1)) / draftRSR);
464:        }

659:        if (lastAvailableAt > availableAt) {	// @audit-issue
660:            availableAt = lastAvailableAt;
661:        }
```
[446](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L446-L449), [461](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L461-L464), [659](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L659-L661), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

236:        if (	// @audit-issue
237:            (!reweightable || (reweightable && !disableTargetAmountCheck)) &&
238:            config.erc20s.length != 0
239:        ) {
240:            // Require targets remain constant
241:            BasketLibP1.requireConstantConfigTargets(
242:                assetRegistry,
243:                config,
244:                _targetAmts,
245:                erc20s,
246:                targetAmts
247:            );
248:        }
```
[236](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L236-L248), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

150:            if (ckpts[mid].fromTimepoint > timepoint) {	// @audit-issue
151:                high = mid;
152:            } else {
153:                low = mid + 1;
154:            }

195:        } else if (delegatee != address(0) && currentDelegate != delegatee) {	// @audit-issue
196:            // Delegate to delegatee if provided and different than current delegate
197:            _delegate(caller, delegatee);
198:        }

270:            if (pos != 0 && oldCkpt.fromTimepoint == clock()) {	// @audit-issue
271:                ckpts[pos - 1].val = SafeCastUpgradeable.toUint224(newWeight);
272:            } else {
273:                ckpts.push(
274:                    Checkpoint({
275:                        fromTimepoint: clock(),
276:                        val: SafeCastUpgradeable.toUint224(newWeight)
277:                    })
278:                );
279:            }
```
[150](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L150-L154), [195](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L195-L198), [270](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L270-L279), 


```solidity
Path: ./contracts/p1/Broker.sol

140:        if (kind == TradeKind.BATCH_AUCTION) {	// @audit-issue
141:            return newBatchAuction(req, caller);
142:        }
```
[140](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L140-L142), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

50:        if (address(trade.sell) == address(0) || address(trade.buy) == address(0)) {	// @audit-issue
51:            return (false, req, prices);
52:        }

57:        if (	// @audit-issue
58:            trade.prices.sellLow == 0 ||
59:            (trade.sell.isCollateral() &&
60:                ICollateral(address(trade.sell)).status() != CollateralStatus.SOUND)
61:        ) {
62:            // Emergency case
63:            // Set minBuyAmount as a function of sellAmount
64:            (doTrade, req) = trade.prepareTradeSell(ctx.minTradeVolume, ctx.maxTradeSlippage);
65:        } else {
66:            // Normal case
67:            // Set sellAmount as a function of minBuyAmount
68:            (doTrade, req) = trade.prepareTradeToCoverDeficit(
69:                ctx.minTradeVolume,
70:                ctx.maxTradeSlippage
71:            );
72:        }

122:        if (ctx.basketsHeld.top > basketsNeeded) {	// @audit-issue
123:            ctx.basketsHeld.top = basketsNeeded;
124:        }

389:        } else if (curr.surplusStatus == CollateralStatus.SOUND) {	// @audit-issue
390:            return
391:                other == CollateralStatus.DISABLED ||
392:                (other == CollateralStatus.SOUND && surplusAmt.gt(curr.surplus));
393:        } else {
394:            // curr is IFFY
395:            return other != CollateralStatus.IFFY || surplusAmt.gt(curr.surplus);
396:        }
```
[50](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L50-L52), [57](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L57-L72), [122](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L122-L124), [389](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L389-L396), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

214:        if (price > bestPrice.mul(ONE_POINT_FIVE, CEIL)) {	// @audit-issue
215:            broker.reportViolation();
216:        }

250:        if (price > bestPrice.mul(ONE_POINT_FIVE, CEIL)) {	// @audit-issue
251:            broker.reportViolation();
252:        }
```
[214](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L214-L216), [250](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L250-L252), 


#### Recommendation

Consider using single line if statements as ternary.

### Missing Revert Messages in `require`/`revert` Statements
In Solidity, the `require` function is commonly used to enforce certain conditions or invariants in the code. If the condition inside `require` evaluates to `false`, the function will throw an exception and revert all changes made during the transaction. Along with this condition check, `require` can also accept a second argument — a string that provides a descriptive error message to convey the reason for the transaction's failure.

When `require` is used without this descriptive error message, as in `require(someBoolean)`, it results in less informative feedback to the users or interacting contracts. This lack of clarity can pose challenges in debugging failed transactions or understanding why an operation was not allowed.

On the other hand, a well-structured error message, like `require(someBoolean, "this is an error msg")`, offers insight into the failure's cause, making it much easier for developers, users, and auditors to identify and address issues.


```solidity
Path: ./contracts/libraries/Fixed.sol

318:        require(x_ <= FIX_ONE);	// @audit-issue
```
[318](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L318-L318), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

62:            if (errData.length == 0) revert(); // solhint-disable-line reason-string	// @audit-issue
```
[62](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L62-L62), 


```solidity
Path: ./contracts/p1/BackingManager.sol

97:                if (errData.length == 0) revert(); // solhint-disable-line reason-string	// @audit-issue
```
[97](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L97-L97), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

450:                if (errData.length == 0) revert(); // solhint-disable-line reason-string	// @audit-issue

579:                        if (errData.length == 0) revert(); // solhint-disable-line reason-string	// @audit-issue

728:                if (errData.length == 0) revert(); // solhint-disable-line reason-string	// @audit-issue
```
[450](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L450-L450), [579](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L579-L579), [728](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L728-L728), 


#### Recommendation

Consider adding an error message to require statements.

### Contract uses both `require()`/`revert()` as well as custom errors
Consider using just one method in a single file

```solidity
Path: ./contracts/libraries/Fixed.sol

177:library FixLib {	// @audit-issue
```
[177](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L177-L177), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

14:contract RevenueTraderP1 is TradingP1, IRevenueTrader {	// @audit-issue
```
[14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L14-L14), 


```solidity
Path: ./contracts/p1/BackingManager.sol

20:contract BackingManagerP1 is TradingP1, IBackingManager {	// @audit-issue
```
[20](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L20-L20), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

24:contract BasketHandlerP1 is ComponentP1, IBasketHandler {	// @audit-issue
```
[24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L24-L24), 


```solidity
Path: ./contracts/p1/RToken.sol

17:contract RTokenP1 is ComponentP1, ERC20PermitUpgradeable, IRToken {	// @audit-issue
```
[17](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L17-L17), 


```solidity
Path: ./contracts/p1/Broker.sol

19:contract BrokerP1 is ComponentP1, IBroker {	// @audit-issue
```
[19](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L19-L19), 


#### Recommendation

Consistently use either `require()`/`revert()` or custom errors for error handling in your contract to maintain code consistency and readability. Using both methods in the same contract can lead to confusion and make the code harder to understand.

### Custom error has no error details
Consider adding parameters to the error to indicate which user or values caused the failure

```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

18:    error DAOFeeRegistry__FeeRecipientAlreadySet();	// @audit-issue

19:    error DAOFeeRegistry__InvalidFeeRecipient();	// @audit-issue

20:    error DAOFeeRegistry__InvalidFeeNumerator();	// @audit-issue

21:    error DAOFeeRegistry__InvalidRoleRegistry();	// @audit-issue

22:    error DAOFeeRegistry__InvalidCaller();	// @audit-issue
```
[18](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L18-L18), [19](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L19-L19), [20](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L20-L20), [21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L21-L21), [22](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L22-L22), 


```solidity
Path: ./contracts/registry/VersionRegistry.sol

18:    error VersionRegistry__ZeroAddress();	// @audit-issue

19:    error VersionRegistry__InvalidRegistration();	// @audit-issue

20:    error VersionRegistry__AlreadyDeprecated();	// @audit-issue

21:    error VersionRegistry__InvalidRoleRegistry();	// @audit-issue

22:    error VersionRegistry__InvalidCaller();	// @audit-issue
```
[18](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L18-L18), [19](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L19-L19), [20](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L20-L20), [21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L21-L21), [22](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L22-L22), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

19:    error AssetPluginRegistry__InvalidAsset();	// @audit-issue

20:    error AssetPluginRegistry__InvalidCaller();	// @audit-issue

21:    error AssetPluginRegistry__InvalidVersion();	// @audit-issue

22:    error AssetPluginRegistry__LengthMismatch();	// @audit-issue
```
[19](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L19-L19), [20](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L20-L20), [21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L21-L21), [22](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L22-L22), 


```solidity
Path: ./contracts/libraries/Fixed.sol

33:error UIntOutOfBounds();	// @audit-issue
```
[33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L33-L33), 


#### Recommendation

When defining custom errors, consider adding parameters or error details that provide information about the specific conditions or inputs that caused the error. Including error details can make debugging and troubleshooting easier by providing context on the cause of the failure.

### Use a `modifier` instead of `require` to check for `msg.sender`
If some functions are only allowed to be called by some specific users, consider using a `modifier` instead of checking with a `require` statement, especially if this check is done in multiple functions.

```solidity
Path: ./contracts/p1/Main.sol

154:        require(msg.sender == address(this), "not self");	// @audit-issue
```
[154](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L154-L154), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

180:        require(msg.sender == origin, "only origin can settle");	// @audit-issue
```
[180](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L180-L180), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

279:        require(msg.sender == address(origin), "only origin can settle");	// @audit-issue
```
[279](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L279-L279), 


#### Recommendation

To improve code readability and maintainability, consider using a `modifier` to check for `msg.sender` in functions that should only be called by specific users. This not only simplifies the code but also makes it more consistent and easier to audit.

### Consider moving `msg.sender` checks to `modifier`s
If some functions are only allowed to be called by some specific users, consider using a modifier instead of checking with a require statement, especially if this check is done in multiple functions.

```solidity
Path: ./contracts/registry/VersionRegistry.sol

38:        if (!roleRegistry.isOwner(msg.sender)) {
39:            revert VersionRegistry__InvalidCaller();	// @audit-issue

60:        if (!roleRegistry.isOwnerOrEmergencyCouncil(msg.sender)) {
61:            revert VersionRegistry__InvalidCaller();	// @audit-issue
```
[39](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L38-L39), [61](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L60-L61), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

32:        if (!roleRegistry.isOwner(msg.sender)) {
33:            revert AssetPluginRegistry__InvalidCaller();	// @audit-issue

56:        if (!roleRegistry.isOwner(msg.sender)) {
57:            revert AssetPluginRegistry__InvalidCaller();	// @audit-issue

84:        if (!roleRegistry.isOwner(msg.sender)) {
85:            revert AssetPluginRegistry__InvalidCaller();	// @audit-issue

108:        if (!roleRegistry.isOwnerOrEmergencyCouncil(msg.sender)) {
109:            revert AssetPluginRegistry__InvalidCaller();	// @audit-issue
```
[33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L32-L33), [57](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L56-L57), [85](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L84-L85), [109](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L108-L109), 


```solidity
Path: ./contracts/p1/Main.sol

154:        require(msg.sender == address(this), "not self");	// @audit-issue
```
[154](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L154-L154), 


```solidity
Path: ./contracts/p1/BackingManager.sol

115:        require(	// @audit-issue
116:            _msgSender() == address(this) || tradeEnd[kind] < block.timestamp,
117:            "already rebalancing"
118:        );
```
[115](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L115-L118), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

138:        require(_msgSender() == address(assetRegistry), "asset registry only");	// @audit-issue

161:        require(	// @audit-issue
162:            main.hasRole(OWNER, _msgSender()) ||
163:                (lastStatus == CollateralStatus.DISABLED && !main.tradingPausedOrFrozen()),
164:            "basket unrefreshable"
165:        );
```
[138](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L138-L138), [161](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L161-L165), 


```solidity
Path: ./contracts/p1/RToken.sol

267:        require(amount <= balanceOf(_msgSender()), "insufficient balance");	// @audit-issue

357:        require(_msgSender() == address(backingManager), "not backing manager");	// @audit-issue

398:        require(_msgSender() == address(backingManager), "not backing manager");	// @audit-issue
```
[267](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L267-L267), [357](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L357-L357), [398](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L398-L398), 


```solidity
Path: ./contracts/p1/Broker.sol

151:        require(trades[_msgSender()], "unrecognized trade contract");	// @audit-issue
```
[151](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L151-L151), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

180:        require(msg.sender == origin, "only origin can settle");	// @audit-issue
```
[180](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L180-L180), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

279:        require(msg.sender == address(origin), "only origin can settle");	// @audit-issue
```
[279](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L279-L279), 


#### Recommendation

Consider refactoring your code by moving `msg.sender` checks to modifiers when certain functions are only allowed to be called by specific users. This approach can enhance code readability, reduce redundancy, and make it easier to maintain access control logic.

### Constants in comparisons should appear on the left side
Doing so will prevent [typo bugs](https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html)

```solidity
Path: ./contracts/mixins/Auth.sol

143:        if (longFreezes[_msgSender()] == 0) _revokeRole(LONG_FREEZER, _msgSender());	// @audit-issue

199:        require(shortFreeze_ != 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");	// @audit-issue

206:        require(longFreeze_ != 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");	// @audit-issue
```
[143](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L143-L143), [199](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L199-L199), [206](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L206-L206), 


```solidity
Path: ./contracts/libraries/Throttle.sol

43:        if (throttle.params.amtRate == 0 && throttle.params.pctRate == 0) return;	// @audit-issue
```
[43](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L43-L43), 


```solidity
Path: ./contracts/libraries/Fixed.sol

212:        if (x == 0) return 0;	// @audit-issue

319:        if (y == 1) return x_;	// @audit-issue

320:        if (x_ == FIX_ONE || y == 0) return FIX_ONE;	// @audit-issue

324:            if (y & 1 == 1) result = (result * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;	// @audit-issue

393:        if (x == 0) return 0; // always computable, no matter what decimals is	// @audit-issue

501:        if (a == 0 || b == 0) return 0;	// @audit-issue

549:        if (a == 0) return 0;	// @audit-issue

550:        if (b == 0) return FIX_MAX;	// @audit-issue

567:        if (a == 0 || b == 0) return 0;	// @audit-issue

568:        if (a == FIX_MAX || b == FIX_MAX || c == 0) return FIX_MAX;	// @audit-issue

598:                if (mm != 0) result_256 += 1;	// @audit-issue
```
[212](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L212-L212), [319](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L319-L319), [320](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L320-L320), [324](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L324-L324), [393](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L393-L393), [501](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L501-L501), [549](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L549-L549), [550](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L550-L550), [567](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L567-L567), [568](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L568-L568), [598](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L598-L598), 


```solidity
Path: ./contracts/libraries/Allowance.sol

28:        require(token.allowance(address(this), spender) == 0, "allowance not 0");	// @audit-issue

30:        if (value == 0) return;	// @audit-issue
```
[28](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L28-L28), [30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L30-L30), 


```solidity
Path: ./contracts/libraries/Permit.sol

19:                IERC1271Upgradeable(owner).isValidSignature(hash, abi.encodePacked(r, s, v)) ==	// @audit-issue
```
[19](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Permit.sol#L19-L19), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

62:            if (errData.length == 0) revert(); // solhint-disable-line reason-string	// @audit-issue

79:            require(revTotals.rsrTotal == 0, "rsrTotal > 0");	// @audit-issue

81:            require(revTotals.rTokenTotal == 0, "rTokenTotal > 0");	// @audit-issue

115:        require(len != 0, "empty erc20s list");	// @audit-issue

119:            (tokenToBuy == rsr && revTotals.rsrTotal != 0) ||	// @audit-issue

120:                (address(tokenToBuy) == address(rToken) && revTotals.rTokenTotal != 0),	// @audit-issue

131:                if (len == 1) return; // return early if tokenToBuy is only entry	// @audit-issue

150:        require(buyHigh != 0 && buyHigh != FIX_MAX, "buy asset price unknown");	// @audit-issue

158:            require(erc20.balanceOf(address(this)) != 0, "0 balance");	// @audit-issue
```
[62](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L62-L62), [79](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L79-L79), [81](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L81-L81), [115](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L115-L115), [119](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L119-L119), [120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L120-L120), [131](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L131-L131), [150](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L150-L150), [158](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L158-L158), 


```solidity
Path: ./contracts/p1/Distributor.sol

133:            if (totalShares != 0) tokensPerShare = amount / totalShares;	// @audit-issue

134:            require(tokensPerShare != 0, "nothing to distribute");	// @audit-issue

151:            if (numberOfShares == 0) continue;	// @audit-issue

157:                if (transferAmt != 0) accountRewards = true;	// @audit-issue

160:                if (transferAmt != 0) accountRewards = true;	// @audit-issue

220:            if (feeRecipient != address(0) && feeNumerator != 0) {	// @audit-issue

247:        if (dest == FURNACE) require(share.rsrDist == 0, "Furnace must get 0% of RSR");	// @audit-issue

248:        if (dest == ST_RSR) require(share.rTokenDist == 0, "StRSR must get 0% of RToken");	// @audit-issue

252:        if (share.rsrDist == 0 && share.rTokenDist == 0) {	// @audit-issue
```
[133](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L133-L133), [134](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L134-L134), [151](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L151-L151), [157](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L157-L157), [160](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L160-L160), [220](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L220-L220), [247](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L247-L247), [248](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L248-L248), [252](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L252-L252), 


```solidity
Path: ./contracts/p1/Furnace.sol

78:        if (amount != 0) rToken.melt(amount);	// @audit-issue
```
[78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L78-L78), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

94:            if (quantity != 0) basketHandler.disableBasket(); // not an interaction	// @audit-issue

113:            if (quantity != 0) basketHandler.disableBasket(); // not an interaction	// @audit-issue
```
[94](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L94-L94), [113](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L113-L113), 


```solidity
Path: ./contracts/p1/BackingManager.sol

97:                if (errData.length == 0) revert(); // solhint-disable-line reason-string	// @audit-issue

120:        require(tradesOpen == 0, "trade open");	// @audit-issue

187:        require(tradesOpen == 0, "trade open");	// @audit-issue

213:        if (rsr.balanceOf(address(this)) != 0) {	// @audit-issue

248:                if (tokensPerShare == 0) continue;	// @audit-issue

253:                if (totals.rsrTotal != 0) {	// @audit-issue

256:                if (totals.rTokenTotal != 0) {	// @audit-issue

308:        assert(tradesOpen == 0);	// @audit-issue
```
[97](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L97-L97), [120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L120-L120), [187](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L187-L187), [213](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L213-L213), [248](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L248-L248), [253](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L253-L253), [256](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L256-L256), [308](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L308-L308), 


```solidity
Path: ./contracts/p1/StRSR.sol

183:        assert(bytes(name_).length != 0);	// @audit-issue

184:        assert(bytes(symbol_).length != 0);	// @audit-issue

309:        if (endId == 0 || firstId >= endId) return;	// @audit-issue

317:        uint192 oldDrafts = firstId != 0 ? queue[firstId - 1].drafts : 0;	// @audit-issue

329:        if (rsrAmount == 0) return;	// @audit-issue

355:        if (endId == 0 || firstId >= endId) return;	// @audit-issue

364:        uint192 oldDrafts = firstId != 0 ? queue[firstId - 1].drafts : 0;	// @audit-issue

376:        if (rsrAmount == 0) return;	// @audit-issue

446:        if (stakeRSR != 0) {	// @audit-issue

450:        if (stakeRSR == 0 || stakeRate > MAX_STAKE_RATE) {	// @audit-issue

461:        if (draftRSR != 0) {	// @audit-issue

466:        if (draftRSR == 0 || draftRate > MAX_DRAFT_RATE) {	// @audit-issue

621:        stakeRate = (stakeRSR == 0 || totalStakes == 0)	// @audit-issue

656:        uint192 oldDrafts = index != 0 ? queue[index - 1].drafts : 0;	// @audit-issue

657:        uint64 lastAvailableAt = index != 0 ? queue[index - 1].availableAt : 0;	// @audit-issue

916:        require(val != 0, "zero amount");	// @audit-issue
```
[183](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L183-L183), [184](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L184-L184), [309](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L309-L309), [317](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L317-L317), [329](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L329-L329), [355](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L355-L355), [364](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L364-L364), [376](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L376-L376), [446](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L446-L446), [450](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L450-L450), [461](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L461-L461), [466](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L466-L466), [621](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L621-L621), [656](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L656-L656), [657](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L657-L657), [916](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L916-L916), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

233:        require(erc20s.length != 0 && erc20s.length == targetAmts.length, "invalid lengths");	// @audit-issue

238:            config.erc20s.length != 0	// @audit-issue

319:        if (disabled || size == 0) return CollateralStatus.DISABLED;	// @audit-issue

372:            if (pegPrice == 0) return FIX_ONE;	// @audit-issue

397:        if (refPerTok == 0) return FIX_MAX;	// @audit-issue

427:                if (qty == 0) continue;	// @audit-issue

450:                if (errData.length == 0) revert(); // solhint-disable-line reason-string	// @audit-issue

579:                        if (errData.length == 0) revert(); // solhint-disable-line reason-string	// @audit-issue

612:        if (length == 0 || disabled) return BasketRange(FIX_ZERO, FIX_MAX);	// @audit-issue

728:                if (errData.length == 0) revert(); // solhint-disable-line reason-string	// @audit-issue
```
[233](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L233-L233), [238](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L238-L238), [319](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L319-L319), [372](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L372-L372), [397](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L397-L397), [427](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L427-L427), [450](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L450-L450), [579](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L579-L579), [612](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L612-L612), [728](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L728-L728), 


```solidity
Path: ./contracts/p1/RToken.sol

69:        require(bytes(name_).length != 0, "name empty");	// @audit-issue

70:        require(bytes(symbol_).length != 0, "symbol empty");	// @audit-issue

71:        require(bytes(mandate_).length != 0, "mandate empty");	// @audit-issue

106:        require(amount != 0, "Cannot issue zero");	// @audit-issue

133:        uint192 amtBaskets = supply != 0	// @audit-issue

191:        require(amount != 0, "Cannot redeem zero");	// @audit-issue

215:            if (amounts[i] == 0) continue;	// @audit-issue

266:        require(amount != 0, "Cannot redeem zero");	// @audit-issue

322:                if (amounts[i] == 0) continue; // unregistered ERC20s will have 0 amount	// @audit-issue

404:        require(supply != 0, "0 supply");	// @audit-issue

489:        uint256 amtRToken = totalSupply != 0	// @audit-issue
```
[69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L69-L69), [70](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L70-L70), [71](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L71-L71), [106](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L106-L106), [133](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L133-L133), [191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L191-L191), [215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L215-L215), [266](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L266-L266), [322](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L322-L322), [404](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L404-L404), [489](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L489-L489), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

88:        return pos == 0 ? 0 : _checkpoints[era][account][pos - 1].val;	// @audit-issue

158:            return high == 0 ? 0 : ckpts[high - 1].val;	// @audit-issue

235:        if (src != dst && amount != 0) {	// @audit-issue

265:            Checkpoint memory oldCkpt = pos == 0 ? Checkpoint(0, 0) : ckpts[pos - 1];	// @audit-issue

270:            if (pos != 0 && oldCkpt.fromTimepoint == clock()) {	// @audit-issue
```
[88](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L88-L88), [158](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L158-L158), [235](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L235-L235), [265](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L265-L265), [270](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L270-L270), 


```solidity
Path: ./contracts/p1/Broker.sol

199:            newAuctionLength == 0 ||	// @audit-issue

221:            newAuctionLength == 0 ||	// @audit-issue

245:        require(batchAuctionLength != 0, "batch auctions not enabled");	// @audit-issue

277:        require(dutchAuctionLength != 0, "dutch auctions not enabled");	// @audit-issue
```
[199](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L199-L199), [221](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L221-L221), [245](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L245-L245), [277](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L277-L277), 


```solidity
Path: ./contracts/p1/mixins/TradeLib.sol

49:            trade.prices.buyHigh != 0 &&	// @audit-issue

71:            require(trade.prices.sellLow == 0, "trade pricing error");	// @audit-issue

124:            trade.prices.sellLow != 0 &&	// @audit-issue

126:                trade.prices.buyHigh != 0 &&	// @audit-issue

176:        uint192 size = price == 0 ? FIX_MAX : minTradeVolume.div(price, CEIL);	// @audit-issue

177:        return size != 0 ? size : 1;	// @audit-issue

189:        return size != 0 ? size : 1;	// @audit-issue
```
[49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L49-L49), [71](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L71-L71), [124](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L124-L124), [126](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L126-L126), [176](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L176-L176), [177](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L177-L177), [189](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L189-L189), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

58:            trade.prices.sellLow == 0 ||	// @audit-issue

117:        require(buPriceLow != 0 && buPriceHigh != FIX_MAX, "BUs unpriced");	// @audit-issue

148:                ctx.quantities[i] == 0 &&	// @audit-issue

302:                if (high == 0) continue; // skip over worthless assets	// @audit-issue

362:                high != 0 &&	// @audit-issue
```
[58](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L58-L58), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L117-L117), [148](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L148-L148), [302](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L302-L302), [362](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L362-L362), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

171:        while (targetNames.length() != 0) {	// @audit-issue

257:            if (size == 0) return false;	// @audit-issue

282:        return newBasket.erc20s.length != 0;	// @audit-issue

305:                coll.refPerTok() != 0 &&	// @audit-issue

306:                coll.targetPerRef() != 0;	// @audit-issue

344:        require(_targetAmts.length() == 0, "missing target weights");	// @audit-issue

368:            require(low != 0 && high != FIX_MAX, "invalid price");	// @audit-issue
```
[171](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L171-L171), [257](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L257-L257), [282](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L282-L282), [305](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L305-L305), [306](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L306-L306), [344](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L344-L344), [368](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L368-L368), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

133:        if (minBuyAmtPerOrder == 0) minBuyAmtPerOrder = 1;	// @audit-issue

205:        if (sellBal != 0) IERC20Upgradeable(address(sell)).safeTransfer(origin, sellBal);	// @audit-issue

206:        if (boughtAmt != 0) IERC20Upgradeable(address(buy)).safeTransfer(origin, boughtAmt);	// @audit-issue
```
[133](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L133-L133), [205](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L205-L205), [206](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L206-L206), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

168:        require(prices.sellLow != 0 && prices.sellHigh < FIX_MAX / 1000, "bad sell pricing");	// @audit-issue

169:        require(prices.buyLow != 0 && prices.buyHigh < FIX_MAX / 1000, "bad buy pricing");	// @audit-issue
```
[168](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L168-L168), [169](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L169-L169), 


#### Recommendation

To prevent typo bugs and improve code readability, it's advisable to place constants on the left side of comparisons. This coding practice helps catch accidental assignment (=) instead of comparison (==) and enhances code quality.

### Convert duplicated codes to modifier/functions
Duplicated code in Solidity contracts, especially when repeated across multiple functions, can lead to inefficiencies and increased maintenance challenges. It not only bloats the contract but also makes it harder to implement changes consistently across the codebase. By identifying common patterns or checks that are repeated and abstracting them into modifiers or separate functions, the code can be made more concise, readable, and maintainable. This practice not only reduces the overall bytecode size, potentially lowering deployment and execution costs, but also enhances the contract's reliability by ensuring consistency in how these common checks or operations are executed.

```solidity
Path: ./contracts/libraries/Fixed.sol

212:        if (x == 0) return 0;	// @audit-issue: Same if statement in line(s): ['393']

501:        if (a == 0 || b == 0) return 0;	// @audit-issue: Same if statement in line(s): ['567']
```
[212](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L212-L212), [501](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L501-L501), 


```solidity
Path: ./contracts/p1/Distributor.sol

157:                if (transferAmt != 0) accountRewards = true;	// @audit-issue: Same if statement in line(s): ['160']
```
[157](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L157-L157), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

94:            if (quantity != 0) basketHandler.disableBasket(); // not an interaction	// @audit-issue: Same if statement in line(s): ['113']
```
[94](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L94-L94), 


```solidity
Path: ./contracts/p1/BackingManager.sol

187:        require(tradesOpen == 0, "trade open");	// @audit-issue: Exactly same copy pasted functionality between lines: `{'120->122'}`
188:        require(basketHandler.isReady(), "basket not ready");
189:        require(block.timestamp >= basketHandler.timestamp() + tradingDelay, "trading delayed");
```
[187](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L187-L189), 


```solidity
Path: ./contracts/p1/StRSR.sol

364:        uint192 oldDrafts = firstId != 0 ? queue[firstId - 1].drafts : 0;	// @audit-issue: Exactly same copy pasted functionality between lines: `{'317->329'}`
365:        uint192 draftAmount = queue[endId - 1].drafts - oldDrafts;
366:
367:        // advance queue past withdrawal
368:        firstRemainingDraft[draftEra][account] = endId;
369:
370:        // ==== Compute RSR amount
371:        uint256 newTotalDrafts = totalDrafts - draftAmount;
372:        // newDraftRSR: {qRSR} = {qDrafts} * D18 / D18{qDrafts/qRSR}
373:        uint256 newDraftRSR = (newTotalDrafts * FIX_ONE_256 + (draftRate - 1)) / draftRate;
374:        uint256 rsrAmount = draftRSR - newDraftRSR;
375:
376:        if (rsrAmount == 0) return;

309:        if (endId == 0 || firstId >= endId) return;	// @audit-issue: Same if statement in line(s): ['355']

329:        if (rsrAmount == 0) return;	// @audit-issue: Same if statement in line(s): ['376']
```
[364](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L364-L376), [309](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L309-L309), [329](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L329-L329), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

450:                if (errData.length == 0) revert(); // solhint-disable-line reason-string	// @audit-issue: Same if statement in line(s): ['579', '728']

567:                        if (!asset.isCollateral()) continue; // skip token if not collateral	// @audit-issue: Same if statement in line(s): ['717']
```
[450](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L450-L450), [567](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L567-L567), 


```solidity
Path: ./contracts/p1/RToken.sol

274:        uint256 supply = totalSupply();	// @audit-issue: Exactly same copy pasted functionality between lines: `{'196->200'}`
275:
276:        // Revert if redemption exceeds either supply throttle
277:        issuanceThrottle.useAvailable(supply, -int256(amount));
278:        redemptionThrottle.useAvailable(supply, int256(amount)); // reverts on over-redemption

215:            if (amounts[i] == 0) continue;	// @audit-issue: Same if statement in line(s): ['322']
```
[274](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L274-L278), [215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L215-L215), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

141:            if (ckpts[mid].fromTimepoint > timepoint) {	// @audit-issue: Same if statement in line(s): ['150']
```
[141](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L141-L141), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

236:        require(bidder == address(0), "bid already received");	// @audit-issue: Exactly same copy pasted functionality between lines: `{'200->210'}`
237:        assert(status == TradeStatus.OPEN);
238:
239:        // {buyTok/sellTok}
240:        uint192 price = _price(uint48(block.timestamp)); // enforces auction ongoing
241:
242:        // {qBuyTok}
243:        amountIn = _bidAmount(price);
244:
245:        // Mark bidder
246:        bidder = msg.sender;

214:        if (price > bestPrice.mul(ONE_POINT_FIVE, CEIL)) {	// @audit-issue: Same if statement in line(s): ['250']
```
[236](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L236-L246), [214](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L214-L214), 


#### Recommendation

Identify and consolidate duplicated code blocks in Solidity contracts into reusable modifiers or functions. This approach streamlines the contract by eliminating redundancy, thereby improving clarity, maintainability, and potentially reducing gas costs. For common checks, consider using modifiers for concise and consistent enforcement of conditions. For reusable logic, encapsulate it in functions to avoid code duplication and simplify future updates or bug fixes.

### Style guide: Function ordering does not follow the Solidity style guide
According to the Solidity [style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions), functions should be laid out in the following order :`constructor()`, `receive()`, `fallback()`, `external`, `public`, `internal`, `private`, but the cases below do not follow this pattern

```solidity
Path: ./contracts/mixins/Auth.sol

87:    function grantRole(bytes32 role, address account)	// @audit-issue
```
[87](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L87-L87), 


```solidity
Path: ./contracts/p1/Main.sol

51:    function poke() external {	// @audit-issue
```
[51](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L51-L51), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

70:    function distributeTokenToBuy() external notTradingPausedOrFrozen {	// @audit-issue
```
[70](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L70-L70), 


```solidity
Path: ./contracts/p1/Distributor.sol

270:    function cacheComponents() public {	// @audit-issue: cacheComponents should come before than _setDistribution
```
[270](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L270-L270), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

78:    function register(IAsset asset) external governance returns (bool) {	// @audit-issue
```
[78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L78-L78), 


```solidity
Path: ./contracts/p1/BackingManager.sol

107:    function rebalance(TradeKind kind) external nonReentrant {	// @audit-issue
```
[107](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L107-L107), 


```solidity
Path: ./contracts/p1/StRSR.sol

259:    function unstake(uint256 stakeAmount) external {	// @audit-issue
```
[259](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L259-L259), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

197:    function setPrimeBasket(IERC20[] calldata erc20s, uint192[] calldata targetAmts) external {	// @audit-issue
```
[197](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L197-L197), 


```solidity
Path: ./contracts/p1/RToken.sol

160:    function redeem(uint256 amount) external {	// @audit-issue
```
[160](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L160-L160), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

58:    function clock() public view returns (uint48) {	// @audit-issue
```
[58](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L58-L58), 


```solidity
Path: ./contracts/p1/Broker.sol

125:    function openTrade(	// @audit-issue
```
[125](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L125-L125), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

65:    function claimRewards() external {	// @audit-issue
```
[65](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L65-L65), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

164:    function nextBasket(	// @audit-issue
```
[164](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L164-L164), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

139:    function bidAmount(uint48 timestamp) external view returns (uint256) {	// @audit-issue
```
[139](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L139-L139), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

174:    function supportsInterface(bytes4 interfaceId)	// @audit-issue: supportsInterface should come before than _execute
```
[174](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L174-L174), 


#### Recommendation

Follow the official [Solidity guidelines](https://docs.soliditylang.org/en/latest/style-guide.html).

### Style guide: Contract does not follow the Solidity style guide's suggested layout ordering
The [style guide](https://docs.soliditylang.org/en/latest/style-guide.html#order-of-layout) says that, within a contract, the ordering should be 1) Type declarations, 2) State variables, 3) Events, 4) Errors, 5) Modifiers, and 6) Functions, but the contract(s) below do not follow this ordering


```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

9:contract DAOFeeRegistry {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> roleRegistry
	2 -> feeRecipient
	3 -> defaultFeeNumerator
	4 -> rTokenFeeNumerator
	5 -> rTokenFeeSet
	6 -> FeeRecipientSet
	7 -> DefaultFeeNumeratorSet
	8 -> RTokenFeeNumeratorSet
	9 -> DAOFeeRegistry__FeeRecipientAlreadySet
	10 -> DAOFeeRegistry__InvalidFeeRecipient
	11 -> DAOFeeRegistry__InvalidFeeNumerator
	12 -> DAOFeeRegistry__InvalidRoleRegistry
	13 -> DAOFeeRegistry__InvalidCaller
	14 -> onlyOwner
	15 -> constructor
	16 -> setFeeRecipient
	17 -> setDefaultFeeNumerator
	18 -> setRTokenFeeNumerator
	19 -> resetRTokenFee
	20 -> getFeeDetails

```
[9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L9-L9), 


```solidity
Path: ./contracts/registry/VersionRegistry.sol

12:contract VersionRegistry {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> deployments
	2 -> isDeprecated
	3 -> latestVersion
	4 -> roleRegistry
	5 -> VersionRegistered
	6 -> VersionDeprecated
	7 -> VersionRegistry__ZeroAddress
	8 -> VersionRegistry__InvalidRegistration
	9 -> VersionRegistry__AlreadyDeprecated
	10 -> VersionRegistry__InvalidRoleRegistry
	11 -> VersionRegistry__InvalidCaller
	12 -> constructor
	13 -> registerVersion
	14 -> deprecateVersion
	15 -> getLatestVersion
	16 -> getImplementationForVersion

```
[12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L12-L12), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

12:contract AssetPluginRegistry {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> versionRegistry
	2 -> roleRegistry
	3 -> _isValidAsset
	4 -> isDeprecated
	5 -> AssetPluginRegistryUpdated
	6 -> AssetPluginRegistry__InvalidAsset
	7 -> AssetPluginRegistry__InvalidCaller
	8 -> AssetPluginRegistry__InvalidVersion
	9 -> AssetPluginRegistry__LengthMismatch
	10 -> constructor
	11 -> registerAsset
	12 -> updateVersionsByAsset
	13 -> updateAssetsByVersion
	14 -> deprecateAsset
	15 -> isValidAsset

```
[12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L12-L12), 


```solidity
Path: ./contracts/mixins/Auth.sol

16:abstract contract Auth is AccessControlUpgradeable, IAuth {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> OWNER_ROLE
	2 -> SHORT_FREEZER_ROLE
	3 -> LONG_FREEZER_ROLE
	4 -> PAUSER_ROLE
	5 -> longFreezes
	6 -> unfreezeAt
	7 -> shortFreeze
	8 -> longFreeze
	9 -> tradingPaused
	10 -> issuancePaused
	11 -> __gap
	12 -> __Auth_init
	13 -> grantRole
	14 -> frozen
	15 -> tradingPausedOrFrozen
	16 -> issuancePausedOrFrozen
	17 -> freezeShort
	18 -> freezeLong
	19 -> freezeForever
	20 -> unfreeze
	21 -> pauseTrading
	22 -> unpauseTrading
	23 -> pauseIssuance
	24 -> unpauseIssuance
	25 -> setShortFreeze
	26 -> setLongFreeze
	27 -> freezeUntil

```
[16](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L16-L16), 


```solidity
Path: ./contracts/mixins/ComponentRegistry.sol

12:abstract contract ComponentRegistry is Initializable, Auth, IComponentRegistry {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> rToken
	2 -> stRSR
	3 -> assetRegistry
	4 -> basketHandler
	5 -> backingManager
	6 -> distributor
	7 -> rsrTrader
	8 -> rTokenTrader
	9 -> furnace
	10 -> broker
	11 -> __gap
	12 -> __ComponentRegistry_init
	13 -> _setRToken
	14 -> _setStRSR
	15 -> _setAssetRegistry
	16 -> _setBasketHandler
	17 -> _setBackingManager
	18 -> _setDistributor
	19 -> _setRSRTrader
	20 -> _setRTokenTrader
	21 -> _setFurnace
	22 -> _setBroker

```
[12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L12-L12), 


```solidity
Path: ./contracts/libraries/Fixed.sol

177:library FixLib {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> FIX_HALF
	2 -> toUint
	3 -> toUint
	4 -> shiftl
	5 -> shiftl
	6 -> plus
	7 -> plusu
	8 -> minus
	9 -> minusu
	10 -> mul
	11 -> mul
	12 -> mulu
	13 -> div
	14 -> div
	15 -> divu
	16 -> divu
	17 -> powu
	18 -> sqrt
	19 -> lt
	20 -> lte
	21 -> gt
	22 -> gte
	23 -> eq
	24 -> neq
	25 -> near
	26 -> shiftl_toUint
	27 -> shiftl_toUint
	28 -> mulu_toUint
	29 -> mulu_toUint
	30 -> mul_toUint
	31 -> mul_toUint
	32 -> muluDivu
	33 -> muluDivu
	34 -> mulDiv
	35 -> mulDiv
	36 -> safeMul
	37 -> safeDiv
	38 -> safeMulDiv

```
[177](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L177-L177), 


```solidity
Path: ./contracts/p1/Main.sol

22:contract MainP1 is Versioned, Initializable, Auth, ComponentRegistry, UUPSUpgradeable, IMain {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> rsr
	2 -> versionRegistry
	3 -> assetPluginRegistry
	4 -> daoFeeRegistry
	5 -> __gap
	6 -> constructor
	7 -> init
	8 -> poke
	9 -> setVersionRegistry
	10 -> setAssetPluginRegistry
	11 -> setDAOFeeRegistry
	12 -> hasRole
	13 -> upgradeMainTo
	14 -> upgradeRTokenTo
	15 -> _authorizeUpgrade
	16 -> _upgradeProxy

```
[22](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L22-L22), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

14:contract RevenueTraderP1 is TradingP1, IRevenueTrader {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> using FixLib
	2 -> using SafeERC20
	3 -> tokenToBuy
	4 -> assetRegistry
	5 -> distributor
	6 -> backingManager
	7 -> furnace
	8 -> rToken
	9 -> rsr
	10 -> __gap
	11 -> init
	12 -> cacheComponents
	13 -> settleTrade
	14 -> distributeTokenToBuy
	15 -> returnTokens
	16 -> manageTokens
	17 -> _distributeTokenToBuy

```
[14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L14-L14), 


```solidity
Path: ./contracts/p1/Distributor.sol

12:contract DistributorP1 is ComponentP1, IDistributor {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> using SafeERC20Upgradeable
	2 -> using FixLib
	3 -> using EnumerableSet
	4 -> destinations
	5 -> distribution
	6 -> FURNACE
	7 -> ST_RSR
	8 -> MAX_DESTINATIONS_ALLOWED
	9 -> rsr
	10 -> rToken
	11 -> furnace
	12 -> stRSR
	13 -> rTokenTrader
	14 -> rsrTrader
	15 -> __gap
	16 -> init
	17 -> setDistribution
	18 -> setDistributions
	19 -> distribute
	20 -> totals
	21 -> _setDistribution
	22 -> _ensureSufficientTotal
	23 -> cacheComponents

```
[12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L12-L12), 


```solidity
Path: ./contracts/p1/Furnace.sol

12:contract FurnaceP1 is ComponentP1, IFurnace {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> using FixLib
	2 -> MAX_RATIO
	3 -> rToken
	4 -> ratio
	5 -> lastPayout
	6 -> lastPayoutBal
	7 -> __gap
	8 -> init
	9 -> melt
	10 -> setRatio

```
[12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L12-L12), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

12:contract AssetRegistryP1 is ComponentP1, IAssetRegistry {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> using EnumerableSet
	2 -> GAS_FOR_BH_QTY
	3 -> GAS_FOR_DISABLE_BASKET
	4 -> basketHandler
	5 -> backingManager
	6 -> _erc20s
	7 -> assets
	8 -> lastRefresh
	9 -> __gap
	10 -> init
	11 -> refresh
	12 -> register
	13 -> swapRegistered
	14 -> unregister
	15 -> toAsset
	16 -> toColl
	17 -> isRegistered
	18 -> erc20s
	19 -> getRegistry
	20 -> validateCurrentAssets
	21 -> size
	22 -> _register
	23 -> _registerIgnoringCollisions
	24 -> _reserveGas

```
[12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L12-L12), 


```solidity
Path: ./contracts/p1/BackingManager.sol

20:contract BackingManagerP1 is TradingP1, IBackingManager {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> using FixLib
	2 -> using SafeERC20
	3 -> assetRegistry
	4 -> basketHandler
	5 -> distributor
	6 -> rToken
	7 -> rsr
	8 -> stRSR
	9 -> rsrTrader
	10 -> rTokenTrader
	11 -> MAX_TRADING_DELAY
	12 -> MAX_BACKING_BUFFER
	13 -> tradingDelay
	14 -> backingBuffer
	15 -> furnace
	16 -> tradeEnd
	17 -> tokensOut
	18 -> __gap
	19 -> init
	20 -> grantRTokenAllowance
	21 -> settleTrade
	22 -> rebalance
	23 -> forwardRevenue
	24 -> tradingContext
	25 -> compromiseBasketsNeeded
	26 -> setTradingDelay
	27 -> setBackingBuffer
	28 -> cacheComponents

```
[20](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L20-L20), 


```solidity
Path: ./contracts/p1/StRSR.sol

34:abstract contract StRSRP1 is Initializable, ComponentP1, IStRSR, EIP712Upgradeable {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> using CountersUpgradeable
	2 -> using SafeERC20Upgradeable
	3 -> MIN_UNSTAKING_DELAY
	4 -> MAX_UNSTAKING_DELAY
	5 -> MAX_REWARD_RATIO
	6 -> name
	7 -> symbol
	8 -> decimals
	9 -> assetRegistry
	10 -> backingManager
	11 -> basketHandler
	12 -> rsr
	13 -> era
	14 -> stakes
	15 -> totalStakes
	16 -> stakeRSR
	17 -> stakeRate
	18 -> MAX_STAKE_RATE
	19 -> _allowances
	20 -> draftEra
	21 -> draftQueues
	22 -> firstRemainingDraft
	23 -> totalDrafts
	24 -> draftRSR
	25 -> draftRate
	26 -> MAX_DRAFT_RATE
	27 -> _nonces
	28 -> _delegationNonces
	29 -> _PERMIT_TYPEHASH
	30 -> unstakingDelay
	31 -> rewardRatio
	32 -> payoutLastPaid
	33 -> rsrRewardsAtLastPayout
	34 -> MAX_WITHDRAWAL_LEAK
	35 -> leaked
	36 -> lastWithdrawRefresh
	37 -> withdrawalLeak
	38 -> MAX_SAFE_STAKE_RATE
	39 -> MIN_SAFE_STAKE_RATE
	40 -> __gap
	41 -> init
	42 -> payoutRewards
	43 -> stake
	44 -> unstake
	45 -> withdraw
	46 -> cancelUnstake
	47 -> seizeRSR
	48 -> resetStakes
	49 -> exchangeRate
	50 -> endIdForWithdraw
	51 -> draftQueueLen
	52 -> getDraftRSR
	53 -> getStakeRSR
	54 -> getTotalDrafts
	55 -> getDraftEra
	56 -> _payoutRewards
	57 -> pushDraft
	58 -> beginEra
	59 -> beginDraftEra
	60 -> rsrRewards
	61 -> leakyRefresh
	62 -> mintStakes
	63 -> _requireNotTradingPausedOrFrozen
	64 -> _requireNotFrozen
	65 -> _requireGovernanceOnly
	66 -> totalSupply
	67 -> balanceOf
	68 -> allowance
	69 -> transfer
	70 -> approve
	71 -> transferFrom
	72 -> increaseAllowance
	73 -> decreaseAllowance
	74 -> _transfer
	75 -> _mint
	76 -> _burn
	77 -> _approve
	78 -> _spendAllowance
	79 -> _afterTokenTransfer
	80 -> _notZero
	81 -> _notZero
	82 -> permit
	83 -> nonces
	84 -> delegationNonces
	85 -> DOMAIN_SEPARATOR
	86 -> _useNonce
	87 -> _useDelegationNonce
	88 -> setUnstakingDelay
	89 -> setRewardRatio
	90 -> setWithdrawalLeak

```
[34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L34-L34), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

24:contract BasketHandlerP1 is ComponentP1, IBasketHandler {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> using BasketLibP1
	2 -> using CollateralStatusComparator
	3 -> using EnumerableMap
	4 -> using EnumerableSet
	5 -> using FixLib
	6 -> MAX_TARGET_AMT
	7 -> MIN_WARMUP_PERIOD
	8 -> MAX_WARMUP_PERIOD
	9 -> MAX_BACKUP_ERC20S
	10 -> assetRegistry
	11 -> backingManager
	12 -> rsr
	13 -> rToken
	14 -> stRSR
	15 -> config
	16 -> basket
	17 -> nonce
	18 -> timestamp
	19 -> disabled
	20 -> _targetNames
	21 -> _newBasket
	22 -> warmupPeriod
	23 -> lastStatusTimestamp
	24 -> lastStatus
	25 -> basketHistory
	26 -> _targetAmts
	27 -> reweightable
	28 -> lastCollateralized
	29 -> enableIssuancePremium
	30 -> __gap
	31 -> init
	32 -> disableBasket
	33 -> refreshBasket
	34 -> trackStatus
	35 -> setPrimeBasket
	36 -> forceSetPrimeBasket
	37 -> _setPrimeBasket
	38 -> setBackupConfig
	39 -> fullyCollateralized
	40 -> status
	41 -> isReady
	42 -> quantity
	43 -> quantityUnsafe
	44 -> issuancePremium
	45 -> _quantity
	46 -> price
	47 -> price
	48 -> quote
	49 -> quote
	50 -> quoteCustomRedemption
	51 -> basketsHeldBy
	52 -> setWarmupPeriod
	53 -> setIssuancePremiumEnabled
	54 -> requireGovernanceOnly
	55 -> _switchBasket
	56 -> requireValidCollArray
	57 -> getHistoricalBasket
	58 -> getPrimeBasket
	59 -> getBackupConfig

```
[24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L24-L24), 


```solidity
Path: ./contracts/p1/RToken.sol

17:contract RTokenP1 is ComponentP1, ERC20PermitUpgradeable, IRToken {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> using FixLib
	2 -> using ThrottleLib
	3 -> using SafeERC20Upgradeable
	4 -> MIN_THROTTLE_RATE_AMT
	5 -> MAX_THROTTLE_RATE_AMT
	6 -> MAX_THROTTLE_PCT_AMT
	7 -> MIN_EXCHANGE_RATE
	8 -> MAX_EXCHANGE_RATE
	9 -> mandate
	10 -> assetRegistry
	11 -> basketHandler
	12 -> backingManager
	13 -> furnace
	14 -> basketsNeeded
	15 -> issuanceThrottle
	16 -> redemptionThrottle
	17 -> __gap
	18 -> init
	19 -> issue
	20 -> issueTo
	21 -> redeem
	22 -> redeemTo
	23 -> redeemCustom
	24 -> mint
	25 -> melt
	26 -> dissolve
	27 -> setBasketsNeeded
	28 -> monetizeDonations
	29 -> issuanceAvailable
	30 -> redemptionAvailable
	31 -> issuanceThrottleParams
	32 -> redemptionThrottleParams
	33 -> setIssuanceThrottleParams
	34 -> setRedemptionThrottleParams
	35 -> _scaleUp
	36 -> _scaleDown
	37 -> _beforeTokenTransfer

```
[17](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L17-L17), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

15:contract StRSRP1Votes is StRSRP1, IERC5805Upgradeable, IStRSRVotes {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> _DELEGATE_TYPEHASH
	2 -> _delegates
	3 -> _eras
	4 -> _checkpoints
	5 -> _totalSupplyCheckpoints
	6 -> __gap
	7 -> beginEra
	8 -> clock
	9 -> CLOCK_MODE
	10 -> currentEra
	11 -> checkpoints
	12 -> numCheckpoints
	13 -> delegates
	14 -> getVotes
	15 -> getPastVotes
	16 -> getPastTotalSupply
	17 -> getPastEra
	18 -> _checkpointsLookup
	19 -> delegate
	20 -> delegateBySig
	21 -> stakeAndDelegate
	22 -> _mint
	23 -> _burn
	24 -> _afterTokenTransfer
	25 -> _delegate
	26 -> _moveVotingPower
	27 -> _writeCheckpoint
	28 -> _add
	29 -> _subtract
	30 -> _requireValidTimepoint

```
[15](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L15-L15), 


```solidity
Path: ./contracts/p1/Broker.sol

19:contract BrokerP1 is ComponentP1, IBroker {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> using EnumerableSet
	2 -> using FixLib
	3 -> using SafeERC20Upgradeable
	4 -> using Clones
	5 -> MAX_AUCTION_LENGTH
	6 -> MIN_AUCTION_LENGTH
	7 -> backingManager
	8 -> rsrTrader
	9 -> rTokenTrader
	10 -> batchTradeImplementation
	11 -> gnosis
	12 -> batchAuctionLength
	13 -> batchTradeDisabled
	14 -> trades
	15 -> dutchTradeImplementation
	16 -> dutchAuctionLength
	17 -> dutchTradeDisabled
	18 -> rToken
	19 -> __gap
	20 -> init
	21 -> cacheComponents
	22 -> openTrade
	23 -> reportViolation
	24 -> setGnosis
	25 -> setBatchTradeImplementation
	26 -> setBatchAuctionLength
	27 -> setDutchTradeImplementation
	28 -> setDutchAuctionLength
	29 -> enableBatchTrade
	30 -> enableDutchTrade
	31 -> newBatchAuction
	32 -> newDutchAuction
	33 -> pricedAtTimestamp

```
[19](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L19-L19), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

19:abstract contract TradingP1 is Multicall, ComponentP1, ReentrancyGuardUpgradeable, ITrading {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> using FixLib
	2 -> MAX_TRADE_VOLUME
	3 -> MAX_TRADE_SLIPPAGE
	4 -> broker
	5 -> trades
	6 -> tradesOpen
	7 -> maxTradeSlippage
	8 -> minTradeVolume
	9 -> tradesNonce
	10 -> __gap
	11 -> __Trading_init
	12 -> requireNotTradingPausedOrFrozen
	13 -> claimRewards
	14 -> claimRewardsSingle
	15 -> settleTrade
	16 -> tryTrade
	17 -> setMaxTradeSlippage
	18 -> setMinTradeVolume

```
[19](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L19-L19), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

14:abstract contract ComponentP1 is	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> main
	2 -> __gap
	3 -> notTradingPausedOrFrozen
	4 -> notIssuancePausedOrFrozen
	5 -> notFrozen
	6 -> governance
	7 -> onlyMain
	8 -> constructor
	9 -> __Component_init
	10 -> _authorizeUpgrade

```
[14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L14-L14), 


#### Recommendation

Follow the official [Solidity guidelines](https://docs.soliditylang.org/en/v0.8.17/style-guide.html).

### Duplicated `require()`/`revert()` checks should be refactored to a modifier or function
In Solidity contracts, it's common to encounter duplicated `require()` or `revert()` statements across multiple functions. These statements are crucial for validating conditions and ensuring contract integrity. However, repeated checks can lead to code redundancy, making the contract larger and more difficult to maintain. This redundancy can be streamlined by encapsulating the repeated logic in a modifier or a dedicated function. By doing so, the code becomes more concise, easier to audit, and more gas-efficient. Furthermore, centralizing the validation logic in a single location makes the codebase more adaptable to changes and reduces the risk of inconsistencies or errors in future updates.

```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

57:        if (feeNumerator_ > MAX_FEE_NUMERATOR) {	// @audit-issue: Same if statement in line(s): ['67']
```
[57](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L57-L57), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

32:        if (!roleRegistry.isOwner(msg.sender)) {	// @audit-issue: Same if statement in line(s): ['56', '84']

35:        if (_asset == address(0)) {	// @audit-issue: Same if statement in line(s): ['63']

41:            if (address(versionRegistry.deployments(versionHash)) == address(0)) {	// @audit-issue: Same if statement in line(s): ['69']
```
[32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L32-L32), [35](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L35-L35), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L41-L41), 


```solidity
Path: ./contracts/p1/Main.sol

100:        require(address(versionRegistry) != address(0), "no registry");	// @audit-issue: Same require statement in line(s): ['116']
```
[100](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L100-L100), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

127:        require(_erc20s.contains(address(erc20)), "erc20 unregistered");	// @audit-issue: Same require statement in line(s): ['135']
```
[127](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L127-L127), 


```solidity
Path: ./contracts/p1/BackingManager.sol

120:        require(tradesOpen == 0, "trade open");	// @audit-issue: Same require statement in line(s): ['187']

121:        require(basketHandler.isReady(), "basket not ready");	// @audit-issue: Same require statement in line(s): ['188']

122:        require(block.timestamp >= basketHandler.timestamp() + tradingDelay, "trading delayed");	// @audit-issue: Same require statement in line(s): ['189']
```
[120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L120-L120), [121](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L121-L121), [122](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L122-L122), 


```solidity
Path: ./contracts/p1/StRSR.sol

312:        require(endId <= queue.length, "index out-of-bounds");	// @audit-issue: Same require statement in line(s): ['357']
```
[312](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L312-L312), 


```solidity
Path: ./contracts/p1/RToken.sol

191:        require(amount != 0, "Cannot redeem zero");	// @audit-issue: Same require statement in line(s): ['266']

357:        require(_msgSender() == address(backingManager), "not backing manager");	// @audit-issue: Same require statement in line(s): ['398']
```
[191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L191-L191), [357](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L357-L357), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

200:        require(bidder == address(0), "bid already received");	// @audit-issue: Same require statement in line(s): ['236']
```
[200](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L200-L200), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

121:        require(startedInSameEra(proposalId), "new era");	// @audit-issue: Same require statement in line(s): ['144']
```
[121](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L121-L121), 


#### Recommendation

Consolidate repeated `require()` or `revert()` checks in Solidity by creating a modifier or a separate function. Apply this modifier to all functions requiring the same validation, or call the function at the beginning of each relevant function. This refactoring enhances contract efficiency, maintainability, and consistency, while potentially reducing gas costs associated with deploying and executing redundant code.

### Duplicate import statements
Contracts often depend on libraries and other contracts to modularize code and reuse functionalities. However, redundant imports occur when a contract imports a library or another contract that is already imported by one of its dependencies. This redundancy does not impact the compiled bytecode but can clutter the codebase, making it harder to understand the direct dependencies of each contract. Simplifying imports by removing these redundancies enhances code readability and maintainability.

```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

6:import { RoleRegistry } from "./RoleRegistry.sol";	// @audit-issue: Same library is also imported on: `['VersionRegistry']`, at lines: `[5]`
```
[6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L6-L6), 


```solidity
Path: ./contracts/p1/Main.sol

4:import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";	// @audit-issue: Same library is also imported on: `['ComponentRegistry']`, at lines: `[4]`

5:import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";	// @audit-issue: Same library is also imported on: `['ComponentRegistry']`, at lines: `[5]`

7:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue: Same library is also imported on: `['ComponentRegistry', 'IMain', 'IAuth', 'IComponentRegistry', 'TestIMain']`, at lines: `[6, 5, 5, 5, 5]`

8:import "../interfaces/IMain.sol";	// @audit-issue: Same library is also imported on: `['Auth']`, at lines: `[5]`

12:import "../registry/VersionRegistry.sol";	// @audit-issue: Same library is also imported on: `['IMain', 'IAuth', 'IComponentRegistry', 'TestIMain']`, at lines: `[7, 7, 7, 7]`

13:import "../registry/AssetPluginRegistry.sol";	// @audit-issue: Same library is also imported on: `['IMain', 'IAuth', 'IComponentRegistry', 'TestIMain']`, at lines: `[6, 6, 6, 6]`

14:import "../registry/DAOFeeRegistry.sol";	// @audit-issue: Same library is also imported on: `['IMain', 'IAuth', 'IComponentRegistry', 'TestIMain']`, at lines: `[8, 8, 8, 8]`
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L5-L5), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L7-L7), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L8-L8), [12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L12-L12), [13](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L13-L13), [14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L14-L14), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

4:import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";	// @audit-issue: Same library is also imported on: `['TradingP0']`, at lines: `[4]`

5:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue: Same library is also imported on: `['TradingP1', 'IAuth', 'IComponentRegistry', 'IMain', 'TestIMain', 'IAssetRegistry', 'TradingP0', 'TradeLib']`, at lines: `[5, 5, 5, 5, 5, 4, 5, 4]`
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L5-L5), 


```solidity
Path: ./contracts/p1/Distributor.sol

5:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue: Same library is also imported on: `['IDistributor', 'TestIDistributor', 'IAuth', 'IComponentRegistry', 'IMain', 'TestIMain']`, at lines: `[4, 4, 5, 5, 5, 5]`
```
[5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L5-L5), 


```solidity
Path: ./contracts/p1/Furnace.sol

4:import "../libraries/Fixed.sol";	// @audit-issue: Same library is also imported on: `['IFurnace', 'TestIFurnace']`, at lines: `[4, 4]`
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L4-L4), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

4:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue: Same library is also imported on: `['IAssetRegistry', 'IAuth', 'IComponentRegistry', 'IMain', 'TestIMain']`, at lines: `[4, 5, 5, 5, 5]`
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L4-L4), 


```solidity
Path: ./contracts/p1/BackingManager.sol

4:import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";	// @audit-issue: Same library is also imported on: `['TradingP0']`, at lines: `[4]`

5:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue: Same library is also imported on: `['TradingP1', 'IBackingManager', 'TestIBackingManager', 'IAuth', 'IComponentRegistry', 'IMain', 'TestIMain', 'ArrayLib', 'TradingP0', 'RecollateralizationLibP1']`, at lines: `[5, 4, 4, 5, 5, 5, 5, 4, 5, 4]`

10:import "../libraries/Fixed.sol";	// @audit-issue: Same library is also imported on: `['IAsset', 'TestIAsset', 'CollateralStatusComparator', 'ICollateral', 'TestICollateral']`, at lines: `[6, 6, 6, 6, 6]`
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L5-L5), [10](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L10-L10), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

5:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue: Same library is also imported on: `['IBasketHandler', 'IAssetRegistry', 'TestIBasketHandler', 'IAuth', 'IComponentRegistry', 'IMain', 'TestIMain', 'ArrayLib', 'BasketLibP1']`, at lines: `[4, 4, 4, 5, 5, 5, 5, 4, 4]`

6:import "@openzeppelin/contracts/utils/structs/EnumerableMap.sol";	// @audit-issue: Same library is also imported on: `['BasketLibP1']`, at lines: `[6]`

7:import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";	// @audit-issue: Same library is also imported on: `['BasketLibP1']`, at lines: `[5]`

12:import "../libraries/Fixed.sol";	// @audit-issue: Same library is also imported on: `['IBasketHandler', 'TestIBasketHandler']`, at lines: `[5, 5]`
```
[5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L7-L7), [12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L12-L12), 


```solidity
Path: ./contracts/p1/Deployer.sol

6:import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";	// @audit-issue: Same library is also imported on: `['IDeployer', 'IAsset', 'TestIAsset', 'CollateralStatusComparator', 'ICollateral', 'TestICollateral', 'IBroker', 'IExtendedBroker', 'TestIBroker', 'TestIDeployer', 'Asset']`, at lines: `[4, 5, 5, 5, 5, 5, 4, 4, 4, 4, 5]`

11:import "../interfaces/IBroker.sol";	// @audit-issue: Same library is also imported on: `['MainP1']`, at lines: `[15]`

18:import "../mixins/Versioned.sol";	// @audit-issue: Same library is also imported on: `['MainP1', 'MainP0']`, at lines: `[11, 9]`
```
[6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L6-L6), [11](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L11-L11), [18](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L18-L18), 


```solidity
Path: ./contracts/p1/RToken.sol

8:import "../libraries/Fixed.sol";	// @audit-issue: Same library is also imported on: `['IRToken', 'TestIRToken']`, at lines: `[8, 8]`

9:import "../libraries/Throttle.sol";	// @audit-issue: Same library is also imported on: `['IRToken', 'TestIRToken']`, at lines: `[9, 9]`
```
[8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L8-L8), [9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L9-L9), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

6:import "@openzeppelin/contracts-upgradeable/interfaces/IERC5805Upgradeable.sol";	// @audit-issue: Same library is also imported on: `['IStRSRVotes']`, at lines: `[5]`
```
[6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L6-L6), 


```solidity
Path: ./contracts/p1/Broker.sol

4:import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";	// @audit-issue: Same library is also imported on: `['GnosisTrade']`, at lines: `[6]`
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L4-L4), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

4:import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";	// @audit-issue: Same library is also imported on: `['ITrade']`, at lines: `[4]`
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L4-L4), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

4:import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";	// @audit-issue: Same library is also imported on: `['ITrade']`, at lines: `[4]`
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L4-L4), 


#### Recommendation

Review your Solidity contracts and eliminate duplicate import statements. Ensure each file is imported only once where it's needed. If you find the same import statement across multiple files, consider whether you can restructure your code to centralize common logic or dependencies, potentially through base contracts or libraries. Regularly conduct code audits or utilize linters and other static analysis tools to identify and resolve duplicate imports, thereby enhancing the clarity, structure, and security of your Solidity codebase.

### Adding a return statement when the function defines a named return variable, is redundant
Once the return variable has been assigned (or has its default value), there is no need to explicitly return it at the end of the function, since it's returned automatically.

```solidity
Path: ./contracts/libraries/Fixed.sol

561:    function safeMulDiv(
562:        uint192 a,
563:        uint192 b,
564:        uint192 c,
565:        RoundingMode rounding
566:    ) internal pure returns (uint192 result) {
567:        if (a == 0 || b == 0) return 0;	// @audit-issue
568:        if (a == FIX_MAX || b == FIX_MAX || c == 0) return FIX_MAX;
569:
570:        uint256 result_256;
571:        unchecked {
572:            (uint256 hi, uint256 lo) = fullMul(a, b);
573:            if (hi >= c) return FIX_MAX;
574:            uint256 mm = mulmod(a, b, c);
575:            if (mm > lo) hi -= 1;
576:            lo -= mm;
577:            uint256 pow2 = c & (0 - c);
578:
579:            uint256 c_256 = uint256(c);
580:            // Warning: Should not access c below this line
581:
582:            c_256 /= pow2;
583:            lo /= pow2;
584:            lo += hi * ((0 - pow2) / pow2 + 1);
585:            uint256 r = 1;
586:            r *= 2 - c_256 * r;
587:            r *= 2 - c_256 * r;
588:            r *= 2 - c_256 * r;
589:            r *= 2 - c_256 * r;
590:            r *= 2 - c_256 * r;
591:            r *= 2 - c_256 * r;
592:            r *= 2 - c_256 * r;
593:            r *= 2 - c_256 * r;
594:            result_256 = lo * r;
595:
596:            // Apply rounding
597:            if (rounding == CEIL) {
598:                if (mm != 0) result_256 += 1;
599:            } else if (rounding == ROUND) {
600:                if (mm > ((c_256 - 1) / 2)) result_256 += 1;
601:            }
602:        }
603:
604:        if (result_256 >= FIX_MAX) return FIX_MAX;
605:        return uint192(result_256);
606:    }

561:    function safeMulDiv(
562:        uint192 a,
563:        uint192 b,
564:        uint192 c,
565:        RoundingMode rounding
566:    ) internal pure returns (uint192 result) {
567:        if (a == 0 || b == 0) return 0;
568:        if (a == FIX_MAX || b == FIX_MAX || c == 0) return FIX_MAX;	// @audit-issue
569:
570:        uint256 result_256;
571:        unchecked {
572:            (uint256 hi, uint256 lo) = fullMul(a, b);
573:            if (hi >= c) return FIX_MAX;
574:            uint256 mm = mulmod(a, b, c);
575:            if (mm > lo) hi -= 1;
576:            lo -= mm;
577:            uint256 pow2 = c & (0 - c);
578:
579:            uint256 c_256 = uint256(c);
580:            // Warning: Should not access c below this line
581:
582:            c_256 /= pow2;
583:            lo /= pow2;
584:            lo += hi * ((0 - pow2) / pow2 + 1);
585:            uint256 r = 1;
586:            r *= 2 - c_256 * r;
587:            r *= 2 - c_256 * r;
588:            r *= 2 - c_256 * r;
589:            r *= 2 - c_256 * r;
590:            r *= 2 - c_256 * r;
591:            r *= 2 - c_256 * r;
592:            r *= 2 - c_256 * r;
593:            r *= 2 - c_256 * r;
594:            result_256 = lo * r;
595:
596:            // Apply rounding
597:            if (rounding == CEIL) {
598:                if (mm != 0) result_256 += 1;
599:            } else if (rounding == ROUND) {
600:                if (mm > ((c_256 - 1) / 2)) result_256 += 1;
601:            }
602:        }
603:
604:        if (result_256 >= FIX_MAX) return FIX_MAX;
605:        return uint192(result_256);
606:    }

561:    function safeMulDiv(
562:        uint192 a,
563:        uint192 b,
564:        uint192 c,
565:        RoundingMode rounding
566:    ) internal pure returns (uint192 result) {
567:        if (a == 0 || b == 0) return 0;
568:        if (a == FIX_MAX || b == FIX_MAX || c == 0) return FIX_MAX;
569:
570:        uint256 result_256;
571:        unchecked {
572:            (uint256 hi, uint256 lo) = fullMul(a, b);
573:            if (hi >= c) return FIX_MAX;	// @audit-issue
574:            uint256 mm = mulmod(a, b, c);
575:            if (mm > lo) hi -= 1;
576:            lo -= mm;
577:            uint256 pow2 = c & (0 - c);
578:
579:            uint256 c_256 = uint256(c);
580:            // Warning: Should not access c below this line
581:
582:            c_256 /= pow2;
583:            lo /= pow2;
584:            lo += hi * ((0 - pow2) / pow2 + 1);
585:            uint256 r = 1;
586:            r *= 2 - c_256 * r;
587:            r *= 2 - c_256 * r;
588:            r *= 2 - c_256 * r;
589:            r *= 2 - c_256 * r;
590:            r *= 2 - c_256 * r;
591:            r *= 2 - c_256 * r;
592:            r *= 2 - c_256 * r;
593:            r *= 2 - c_256 * r;
594:            result_256 = lo * r;
595:
596:            // Apply rounding
597:            if (rounding == CEIL) {
598:                if (mm != 0) result_256 += 1;
599:            } else if (rounding == ROUND) {
600:                if (mm > ((c_256 - 1) / 2)) result_256 += 1;
601:            }
602:        }
603:
604:        if (result_256 >= FIX_MAX) return FIX_MAX;
605:        return uint192(result_256);
606:    }

561:    function safeMulDiv(
562:        uint192 a,
563:        uint192 b,
564:        uint192 c,
565:        RoundingMode rounding
566:    ) internal pure returns (uint192 result) {
567:        if (a == 0 || b == 0) return 0;
568:        if (a == FIX_MAX || b == FIX_MAX || c == 0) return FIX_MAX;
569:
570:        uint256 result_256;
571:        unchecked {
572:            (uint256 hi, uint256 lo) = fullMul(a, b);
573:            if (hi >= c) return FIX_MAX;
574:            uint256 mm = mulmod(a, b, c);
575:            if (mm > lo) hi -= 1;
576:            lo -= mm;
577:            uint256 pow2 = c & (0 - c);
578:
579:            uint256 c_256 = uint256(c);
580:            // Warning: Should not access c below this line
581:
582:            c_256 /= pow2;
583:            lo /= pow2;
584:            lo += hi * ((0 - pow2) / pow2 + 1);
585:            uint256 r = 1;
586:            r *= 2 - c_256 * r;
587:            r *= 2 - c_256 * r;
588:            r *= 2 - c_256 * r;
589:            r *= 2 - c_256 * r;
590:            r *= 2 - c_256 * r;
591:            r *= 2 - c_256 * r;
592:            r *= 2 - c_256 * r;
593:            r *= 2 - c_256 * r;
594:            result_256 = lo * r;
595:
596:            // Apply rounding
597:            if (rounding == CEIL) {
598:                if (mm != 0) result_256 += 1;
599:            } else if (rounding == ROUND) {
600:                if (mm > ((c_256 - 1) / 2)) result_256 += 1;
601:            }
602:        }
603:
604:        if (result_256 >= FIX_MAX) return FIX_MAX;	// @audit-issue
605:        return uint192(result_256);
606:    }

561:    function safeMulDiv(
562:        uint192 a,
563:        uint192 b,
564:        uint192 c,
565:        RoundingMode rounding
566:    ) internal pure returns (uint192 result) {
567:        if (a == 0 || b == 0) return 0;
568:        if (a == FIX_MAX || b == FIX_MAX || c == 0) return FIX_MAX;
569:
570:        uint256 result_256;
571:        unchecked {
572:            (uint256 hi, uint256 lo) = fullMul(a, b);
573:            if (hi >= c) return FIX_MAX;
574:            uint256 mm = mulmod(a, b, c);
575:            if (mm > lo) hi -= 1;
576:            lo -= mm;
577:            uint256 pow2 = c & (0 - c);
578:
579:            uint256 c_256 = uint256(c);
580:            // Warning: Should not access c below this line
581:
582:            c_256 /= pow2;
583:            lo /= pow2;
584:            lo += hi * ((0 - pow2) / pow2 + 1);
585:            uint256 r = 1;
586:            r *= 2 - c_256 * r;
587:            r *= 2 - c_256 * r;
588:            r *= 2 - c_256 * r;
589:            r *= 2 - c_256 * r;
590:            r *= 2 - c_256 * r;
591:            r *= 2 - c_256 * r;
592:            r *= 2 - c_256 * r;
593:            r *= 2 - c_256 * r;
594:            result_256 = lo * r;
595:
596:            // Apply rounding
597:            if (rounding == CEIL) {
598:                if (mm != 0) result_256 += 1;
599:            } else if (rounding == ROUND) {
600:                if (mm > ((c_256 - 1) / 2)) result_256 += 1;
601:            }
602:        }
603:
604:        if (result_256 >= FIX_MAX) return FIX_MAX;
605:        return uint192(result_256);	// @audit-issue
606:    }
```
[567](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L561-L606), [568](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L561-L606), [573](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L561-L606), [604](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L561-L606), [605](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L561-L606), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

213:    function _registerIgnoringCollisions(IAsset asset) private returns (bool swapped) {
214:        if (asset.isCollateral()) {
215:            require(
216:                ICollateral(address(asset)).status() == CollateralStatus.SOUND,
217:                "collateral not sound"
218:            );
219:        }
220:
221:        AssetPluginRegistry assetPluginRegistry = main.assetPluginRegistry();
222:        if (address(assetPluginRegistry) != address(0)) {
223:            require(
224:                main.assetPluginRegistry().isValidAsset(
225:                    keccak256(abi.encodePacked(this.version())),
226:                    address(asset)
227:                ),
228:                "unsupported asset"
229:            );
230:        }
231:
232:        IERC20Metadata erc20 = asset.erc20();
233:        if (_erc20s.contains(address(erc20))) {
234:            if (assets[erc20] == asset) return false;	// @audit-issue
235:            else emit AssetUnregistered(erc20, assets[erc20]);
236:        } else {
237:            _erc20s.add(address(erc20));
238:        }
239:
240:        assets[erc20] = asset;
241:        emit AssetRegistered(erc20, asset);
242:
243:        // Refresh to ensure it does not revert, and to save a recent lastPrice
244:        asset.refresh();
245:
246:        if (!main.frozen()) {
247:            backingManager.grantRTokenAllowance(erc20);
248:        }
249:
250:        return true;
251:    }

213:    function _registerIgnoringCollisions(IAsset asset) private returns (bool swapped) {
214:        if (asset.isCollateral()) {
215:            require(
216:                ICollateral(address(asset)).status() == CollateralStatus.SOUND,
217:                "collateral not sound"
218:            );
219:        }
220:
221:        AssetPluginRegistry assetPluginRegistry = main.assetPluginRegistry();
222:        if (address(assetPluginRegistry) != address(0)) {
223:            require(
224:                main.assetPluginRegistry().isValidAsset(
225:                    keccak256(abi.encodePacked(this.version())),
226:                    address(asset)
227:                ),
228:                "unsupported asset"
229:            );
230:        }
231:
232:        IERC20Metadata erc20 = asset.erc20();
233:        if (_erc20s.contains(address(erc20))) {
234:            if (assets[erc20] == asset) return false;
235:            else emit AssetUnregistered(erc20, assets[erc20]);
236:        } else {
237:            _erc20s.add(address(erc20));
238:        }
239:
240:        assets[erc20] = asset;
241:        emit AssetRegistered(erc20, asset);
242:
243:        // Refresh to ensure it does not revert, and to save a recent lastPrice
244:        asset.refresh();
245:
246:        if (!main.frozen()) {
247:            backingManager.grantRTokenAllowance(erc20);
248:        }
249:
250:        return true;	// @audit-issue
251:    }
```
[234](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L213-L251), [250](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L213-L251), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

314:    function status() public view returns (CollateralStatus status_) {
315:        uint256 size = basket.erc20s.length;
316:
317:        // untestable:
318:        //      disabled is only set in _switchBasket, and only if size > 0.
319:        if (disabled || size == 0) return CollateralStatus.DISABLED;	// @audit-issue
320:
321:        for (uint256 i = 0; i < size; ++i) {
322:            CollateralStatus s = assetRegistry.toColl(basket.erc20s[i]).status();
323:            if (s.worseThan(status_)) {
324:                if (s == CollateralStatus.DISABLED) return CollateralStatus.DISABLED;
325:                status_ = s;
326:            }
327:        }
328:    }

314:    function status() public view returns (CollateralStatus status_) {
315:        uint256 size = basket.erc20s.length;
316:
317:        // untestable:
318:        //      disabled is only set in _switchBasket, and only if size > 0.
319:        if (disabled || size == 0) return CollateralStatus.DISABLED;
320:
321:        for (uint256 i = 0; i < size; ++i) {
322:            CollateralStatus s = assetRegistry.toColl(basket.erc20s[i]).status();
323:            if (s.worseThan(status_)) {
324:                if (s == CollateralStatus.DISABLED) return CollateralStatus.DISABLED;	// @audit-issue
325:                status_ = s;
326:            }
327:        }
328:    }

409:    function price() external view returns (uint192 low, uint192 high) {
410:        return price(true);	// @audit-issue
411:    }

467:    function quote(uint192 amount, RoundingMode rounding)
468:        external
469:        view
470:        returns (address[] memory erc20s, uint256[] memory quantities)
471:    {
472:        return quote(amount, rounding == CEIL, rounding);	// @audit-issue
473:    }

610:    function basketsHeldBy(address account) public view returns (BasketRange memory baskets) {
611:        uint256 length = basket.erc20s.length;
612:        if (length == 0 || disabled) return BasketRange(FIX_ZERO, FIX_MAX);	// @audit-issue
613:        baskets.bottom = FIX_MAX;
614:
615:        for (uint256 i = 0; i < length; ++i) {
616:            ICollateral coll = assetRegistry.toColl(basket.erc20s[i]);
617:            if (coll.status() == CollateralStatus.DISABLED) return BasketRange(FIX_ZERO, FIX_MAX);
618:
619:            // {tok/BU}
620:            uint192 q = _quantity(basket.erc20s[i], coll, CEIL);
621:            if (q == FIX_MAX) return BasketRange(FIX_ZERO, FIX_MAX);
622:
623:            // {BU} = {tok} / {tok/BU}
624:            uint192 inBUs = coll.bal(account).div(q);
625:            baskets.bottom = fixMin(baskets.bottom, inBUs);
626:            baskets.top = fixMax(baskets.top, inBUs);
627:        }
628:    }

610:    function basketsHeldBy(address account) public view returns (BasketRange memory baskets) {
611:        uint256 length = basket.erc20s.length;
612:        if (length == 0 || disabled) return BasketRange(FIX_ZERO, FIX_MAX);
613:        baskets.bottom = FIX_MAX;
614:
615:        for (uint256 i = 0; i < length; ++i) {
616:            ICollateral coll = assetRegistry.toColl(basket.erc20s[i]);
617:            if (coll.status() == CollateralStatus.DISABLED) return BasketRange(FIX_ZERO, FIX_MAX);	// @audit-issue
618:
619:            // {tok/BU}
620:            uint192 q = _quantity(basket.erc20s[i], coll, CEIL);
621:            if (q == FIX_MAX) return BasketRange(FIX_ZERO, FIX_MAX);
622:
623:            // {BU} = {tok} / {tok/BU}
624:            uint192 inBUs = coll.bal(account).div(q);
625:            baskets.bottom = fixMin(baskets.bottom, inBUs);
626:            baskets.top = fixMax(baskets.top, inBUs);
627:        }
628:    }
```
[319](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L314-L328), [324](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L314-L328), [410](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L409-L411), [472](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L467-L473), [612](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L610-L628), [617](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L610-L628), 


```solidity
Path: ./contracts/p1/mixins/TradeLib.sol

42:    function prepareTradeSell(
43:        TradeInfo memory trade,
44:        uint192 minTradeVolume,
45:        uint192 maxTradeSlippage
46:    ) internal view returns (bool notDust, TradeRequest memory req) {
47:        // checked for in RevenueTrader / CollateralizatlionLib
48:        assert(
49:            trade.prices.buyHigh != 0 &&
50:                trade.prices.buyHigh != FIX_MAX &&
51:                trade.prices.sellLow != FIX_MAX
52:        );
53:
54:        notDust = isEnoughToSell(
55:            trade.sell,
56:            trade.sellAmount,
57:            trade.prices.sellLow,
58:            minTradeVolume
59:        );
60:
61:        // Cap sell amount using the high price
62:        // Under price decay trade.prices.sellHigh can become up to 3x the savedHighPrice before
63:        // becoming FIX_MAX after the full price timeout
64:        uint192 s = trade.sellAmount;
65:        if (trade.prices.sellHigh != FIX_MAX) {
66:            // {sellTok}
67:            uint192 maxSell = maxTradeSize(trade.sell, trade.buy, trade.prices.sellHigh);
68:            require(maxSell > 1, "trade sizing error");
69:            if (s > maxSell) s = maxSell;
70:        } else {
71:            require(trade.prices.sellLow == 0, "trade pricing error");
72:        }
73:
74:        // Calculate equivalent buyAmount within [0, FIX_MAX]
75:        // {buyTok} = {sellTok} * {1} * {UoA/sellTok} / {UoA/buyTok}
76:        uint192 b = s.mul(FIX_ONE.minus(maxTradeSlippage)).safeMulDiv(
77:            trade.prices.sellLow,
78:            trade.prices.buyHigh,
79:            CEIL
80:        );
81:
82:        // {*tok} => {q*Tok}
83:        req.sellAmount = s.shiftl_toUint(int8(trade.sell.erc20Decimals()), FLOOR);
84:        req.minBuyAmount = b.shiftl_toUint(int8(trade.buy.erc20Decimals()), CEIL);
85:        req.sell = trade.sell;
86:        req.buy = trade.buy;
87:
88:        return (notDust, req);	// @audit-issue
89:    }

118:    function prepareTradeToCoverDeficit(
119:        TradeInfo memory trade,
120:        uint192 minTradeVolume,
121:        uint192 maxTradeSlippage
122:    ) internal view returns (bool notDust, TradeRequest memory req) {
123:        assert(
124:            trade.prices.sellLow != 0 &&
125:                trade.prices.sellLow != FIX_MAX &&
126:                trade.prices.buyHigh != 0 &&
127:                trade.prices.buyHigh != FIX_MAX
128:        );
129:
130:        // Don't buy dust.
131:        trade.buyAmount = fixMax(
132:            trade.buyAmount,
133:            minTradeSize(minTradeVolume, trade.prices.buyHigh)
134:        );
135:
136:        // {sellTok} = {buyTok} * {UoA/buyTok} / {UoA/sellTok}
137:        uint192 exactSellAmount = trade.buyAmount.mulDiv(
138:            trade.prices.buyHigh,
139:            trade.prices.sellLow,
140:            CEIL
141:        );
142:        // exactSellAmount: Amount to sell to buy `deficitAmount` if there's no slippage
143:
144:        // slippedSellAmount: Amount needed to sell to buy `deficitAmount`, counting slippage
145:        uint192 slippedSellAmount = exactSellAmount.div(FIX_ONE.minus(maxTradeSlippage), CEIL);
146:
147:        trade.sellAmount = fixMin(slippedSellAmount, trade.sellAmount); // {sellTok}
148:        return prepareTradeSell(trade, minTradeVolume, maxTradeSlippage);	// @audit-issue
149:    }
```
[88](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L42-L89), [148](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L118-L149), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

33:    function prepareRecollateralizationTrade(TradingContext memory ctx, Registry memory reg)
34:        external
35:        view
36:        returns (
37:            bool doTrade,
38:            TradeRequest memory req,
39:            TradePrices memory prices
40:        )
41:    {
42:        // Compute a target basket range for trading -  {BU}
43:        // The basket range is the full range of projected outcomes for the rebalancing process
44:        BasketRange memory range = basketRange(ctx, reg);
45:
46:        // Select a pair to trade next, if one exists
47:        TradeInfo memory trade = nextTradePair(ctx, reg, range);
48:
49:        // Don't trade if no pair is selected
50:        if (address(trade.sell) == address(0) || address(trade.buy) == address(0)) {
51:            return (false, req, prices);	// @audit-issue
52:        }
53:
54:        // If we are selling a fully unpriced asset or UNSOUND collateral, do not cover deficit
55:        // untestable:
56:        //     sellLow will not be zero, those assets are skipped in nextTradePair
57:        if (
58:            trade.prices.sellLow == 0 ||
59:            (trade.sell.isCollateral() &&
60:                ICollateral(address(trade.sell)).status() != CollateralStatus.SOUND)
61:        ) {
62:            // Emergency case
63:            // Set minBuyAmount as a function of sellAmount
64:            (doTrade, req) = trade.prepareTradeSell(ctx.minTradeVolume, ctx.maxTradeSlippage);
65:        } else {
66:            // Normal case
67:            // Set sellAmount as a function of minBuyAmount
68:            (doTrade, req) = trade.prepareTradeToCoverDeficit(
69:                ctx.minTradeVolume,
70:                ctx.maxTradeSlippage
71:            );
72:        }
73:
74:        // At this point doTrade _must_ be true, otherwise nextTradePair assumptions are broken
75:        assert(doTrade);
76:
77:        return (doTrade, req, trade.prices);
78:    }

33:    function prepareRecollateralizationTrade(TradingContext memory ctx, Registry memory reg)
34:        external
35:        view
36:        returns (
37:            bool doTrade,
38:            TradeRequest memory req,
39:            TradePrices memory prices
40:        )
41:    {
42:        // Compute a target basket range for trading -  {BU}
43:        // The basket range is the full range of projected outcomes for the rebalancing process
44:        BasketRange memory range = basketRange(ctx, reg);
45:
46:        // Select a pair to trade next, if one exists
47:        TradeInfo memory trade = nextTradePair(ctx, reg, range);
48:
49:        // Don't trade if no pair is selected
50:        if (address(trade.sell) == address(0) || address(trade.buy) == address(0)) {
51:            return (false, req, prices);
52:        }
53:
54:        // If we are selling a fully unpriced asset or UNSOUND collateral, do not cover deficit
55:        // untestable:
56:        //     sellLow will not be zero, those assets are skipped in nextTradePair
57:        if (
58:            trade.prices.sellLow == 0 ||
59:            (trade.sell.isCollateral() &&
60:                ICollateral(address(trade.sell)).status() != CollateralStatus.SOUND)
61:        ) {
62:            // Emergency case
63:            // Set minBuyAmount as a function of sellAmount
64:            (doTrade, req) = trade.prepareTradeSell(ctx.minTradeVolume, ctx.maxTradeSlippage);
65:        } else {
66:            // Normal case
67:            // Set sellAmount as a function of minBuyAmount
68:            (doTrade, req) = trade.prepareTradeToCoverDeficit(
69:                ctx.minTradeVolume,
70:                ctx.maxTradeSlippage
71:            );
72:        }
73:
74:        // At this point doTrade _must_ be true, otherwise nextTradePair assumptions are broken
75:        assert(doTrade);
76:
77:        return (doTrade, req, trade.prices);	// @audit-issue
78:    }
```
[51](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L33-L78), [77](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L33-L78), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

104:    function propose(
105:        address[] memory targets,
106:        uint256[] memory values,
107:        bytes[] memory calldatas,
108:        string memory description
109:    ) public override(Governor, IGovernor) returns (uint256 proposalId) {
110:        // The super call checks that getVotes() >= proposalThreshold()
111:        return super.propose(targets, values, calldatas, description);	// @audit-issue
112:    }
```
[111](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L104-L112), 


#### Recommendation

When a function defines a named return variable and assigns a value to it, there is no need to add an explicit return statement at the end of the function. The named return variable will be automatically returned with its assigned value. Removing the redundant return statement can lead to cleaner and more concise code, improving readability and reducing the risk of introducing unnecessary errors.

### Consider using `delete` rather than assigning values to `false`
The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic.


```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

78:        rTokenFeeSet[rToken] = false;	// @audit-issue
```
[78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L78-L78), 


```solidity
Path: ./contracts/mixins/Auth.sol

178:        tradingPaused = false;	// @audit-issue

192:        issuancePaused = false;	// @audit-issue
```
[178](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L178-L178), [192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L192-L192), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

670:            disabled = false;	// @audit-issue
```
[670](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L670-L670), 


```solidity
Path: ./contracts/p1/RToken.sol

323:                if (allZero) allZero = false;	// @audit-issue
```
[323](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L323-L323), 


```solidity
Path: ./contracts/p1/Broker.sol

232:        batchTradeDisabled = false;	// @audit-issue

238:        dutchTradeDisabled[erc20] = false;	// @audit-issue
```
[232](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L232-L232), [238](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L238-L238), 


#### Recommendation

Prefer using the `delete` keyword to reset state variables to their initial values instead of assigning `false` or other values, as it makes the intent clearer and helps with auditing.

### Consider using `delete` rather than assigning zero to clear values
The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic.

```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

77:        rTokenFeeNumerator[rToken] = 0;	// @audit-issue
```
[77](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L77-L77), 


```solidity
Path: ./contracts/p1/StRSR.sol

672:        stakeRSR = 0;	// @audit-issue

673:        totalStakes = 0;	// @audit-issue

684:        draftRSR = 0;	// @audit-issue

685:        totalDrafts = 0;	// @audit-issue

712:            leaked = 0;	// @audit-issue
```
[672](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L672-L672), [673](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L673-L673), [684](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L684-L684), [685](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L685-L685), [712](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L712-L712), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

202:            for (targetIndex = 0; targetIndex < targetsLength; ++targetIndex) {	// @audit-issue
```
[202](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L202-L202), 


#### Recommendation

When you need to clear or reset values in storage variables, consider using the `delete` keyword instead of manually assigning zero or other values. Using `delete` provides a more explicit and efficient way to clear storage variables. For example, instead of `myVariable = 0;`, you can use `delete myVariable;` to clear the value.

### Array is `push()`ed but not `pop()`ed
Array entries are added but are never removed. Consider whether this should be the case, or whether there should be a maximum, or whether old entries should be removed. Cases where there are specific potential problems will be flagged separately under a different issue.

```solidity
Path: ./contracts/p1/StRSR.sol

663:        queue.push(CumulativeDraft(uint176(oldDrafts + draftAmount), availableAt));	// @audit-issue
```
[663](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L663-L663), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

273:                ckpts.push(	// @audit-issue
```
[273](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L273-L273), 


#### Recommendation

When working with arrays in your Solidity code, carefully evaluate whether the addition of array entries using `push()` should be balanced with corresponding removal using methods like `pop()`, `splice()`, or other appropriate array manipulation techniques. This evaluation is crucial to manage the array's size and prevent unnecessary storage growth, which can impact gas costs and contract efficiency.

### Defined named returns not used within function
Such instances can be replaced with unnamed returns

```solidity
Path: ./contracts/libraries/Fixed.sol

566:    ) internal pure returns (uint192 result) {	// @audit-issue: Return param `result` is defined but not used in function.
```
[566](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L566-L566), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

213:    function _registerIgnoringCollisions(IAsset asset) private returns (bool swapped) {	// @audit-issue: Return param `swapped` is defined but not used in function.
```
[213](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L213-L213), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

409:    function price() external view returns (uint192 low, uint192 high) {	// @audit-issue: Return param `low` is defined but not used in function.

409:    function price() external view returns (uint192 low, uint192 high) {	// @audit-issue: Return param `high` is defined but not used in function.

470:        returns (address[] memory erc20s, uint256[] memory quantities)	// @audit-issue: Return param `erc20s` is defined but not used in function.

470:        returns (address[] memory erc20s, uint256[] memory quantities)	// @audit-issue: Return param `quantities` is defined but not used in function.
```
[409](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L409-L409), [409](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L409-L409), [470](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L470-L470), [470](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L470-L470), 


```solidity
Path: ./contracts/p1/mixins/TradeLib.sol

122:    ) internal view returns (bool notDust, TradeRequest memory req) {	// @audit-issue: Return param `notDust` is defined but not used in function.

122:    ) internal view returns (bool notDust, TradeRequest memory req) {	// @audit-issue: Return param `req` is defined but not used in function.
```
[122](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L122-L122), [122](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L122-L122), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

109:    ) public override(Governor, IGovernor) returns (uint256 proposalId) {	// @audit-issue: Return param `proposalId` is defined but not used in function.
```
[109](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L109-L109), 


#### Recommendation

Inspect your Solidity functions for unused named return variables. If a named return is not actively used in the function, switch to using unnamed return types. This change simplifies the function signature and avoids potential confusion over the function's return behavior. Ensure that the function's documentation and comments clearly describe the return type and purpose, maintaining clarity even with unnamed return variables. This approach contributes to cleaner, more concise function declarations in your Solidity codebase.

### Too long functions should be refactored
Functions with too many lines are difficult to understand. It is recommended to refactor complex functions into multiple shorter and easier to understand functions.


```solidity
Path: ./contracts/libraries/Fixed.sol

694:function sqrt256(uint256 x) pure returns (uint256 result) {	// @audit-issue 77 lines
```
[694](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L694-L694), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

109:    function manageTokens(IERC20[] calldata erc20s, TradeKind[] calldata kinds)	// @audit-issue 74 lines
```
[109](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L109-L109), 


```solidity
Path: ./contracts/p1/Distributor.sol

120:    function distribute(IERC20 erc20, uint256 amount) external {	// @audit-issue 80 lines
```
[120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L120-L120), 


```solidity
Path: ./contracts/p1/BackingManager.sol

107:    function rebalance(TradeKind kind) external nonReentrant {	// @audit-issue 65 lines

179:    function forwardRevenue(IERC20[] calldata erc20s) external nonReentrant {	// @audit-issue 86 lines
```
[107](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L107-L107), [179](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L179-L179), 


```solidity
Path: ./contracts/p1/StRSR.sol

424:    function seizeRSR(uint256 rsrAmount) external {	// @audit-issue 54 lines
```
[424](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L424-L424), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

521:    function quoteCustomRedemption(	// @audit-issue 80 lines
```
[521](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L521-L521), 


```solidity
Path: ./contracts/p1/Deployer.sol

107:    function deploy(	// @audit-issue 157 lines
```
[107](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L107-L107), 


```solidity
Path: ./contracts/p1/RToken.sol

105:    function issueTo(address recipient, uint256 amount) public notIssuancePausedOrFrozen {	// @audit-issue 50 lines

253:    function redeemCustom(	// @audit-issue 91 lines
```
[105](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L105-L105), [253](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L253-L253), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

108:    function basketRange(TradingContext memory ctx, Registry memory reg)	// @audit-issue 119 lines

274:    function nextTradePair(	// @audit-issue 102 lines
```
[108](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L108-L108), [274](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L274-L274), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

164:    function nextBasket(	// @audit-issue 119 lines
```
[164](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L164-L164), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

84:    function init(	// @audit-issue 73 lines
```
[84](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L84-L84), 


#### Recommendation

To address this issue, refactor long and complex functions into multiple shorter and more manageable functions. This will improve code readability and maintainability, making it easier to understand and maintain your smart contract.

### Expressions for `constant` values should use `immutable` rather than `constant`
While it does not save gas for some simple binary expressions because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constant`s should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the `constructor`.

```solidity
Path: ./contracts/registry/RoleRegistry.sol

12:    bytes32 public constant EMERGENCY_COUNCIL = keccak256("EMERGENCY_COUNCIL");	// @audit-issue
```
[12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/RoleRegistry.sol#L12-L12), 


```solidity
Path: ./contracts/p1/Distributor.sol

32:    address public constant FURNACE = address(1);	// @audit-issue

33:    address public constant ST_RSR = address(2);	// @audit-issue
```
[32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L32-L32), [33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L33-L33), 


```solidity
Path: ./contracts/p1/StRSR.sol

131:    bytes32 private constant _PERMIT_TYPEHASH =	// @audit-issue
132:        keccak256(
133:            "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
134:        );

165:    uint192 private constant MIN_SAFE_STAKE_RATE = uint192(1e12); // 1e-6  D18{qStRSR/qRSR}	// @audit-issue
```
[131](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L131-L134), [165](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L165-L165), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

30:    bytes32 private constant _DELEGATE_TYPEHASH =	// @audit-issue
31:        keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");
```
[30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L30-L31), 


```solidity
Path: ./contracts/libraries/Fixed.sol

34:bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));	// @audit-issue
```
[34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L34-L34), 


#### Recommendation

To improve code readability and adhere to best practices, consider using `immutable` variables instead of `constant` for expressions or values calculated in, or passed into the constructor. While the compiler may handle this issue for simple binary expressions, it's essential to use the right tool for the task at hand. `constant` should be reserved for literal values written directly into the code, while `immutable` is better suited for dynamic values and expressions.

### Missing call to inherited contract (super call)
Custom implementation of <`_beforeTokenTransfer`, etc.> function should call `super.function` function to keep the system consistent.

```solidity
Path: ./contracts/p1/StRSR.sol

903:    function _afterTokenTransfer(	// @audit-issue
```
[903](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L903-L903), 


```solidity
Path: ./contracts/p1/RToken.sol

530:    function _beforeTokenTransfer(	// @audit-issue
```
[530](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L530-L530), 


#### Recommendation

Call the `super.function`

### Event is not properly `indexed`
Index event fields make the field more quickly accessible [to off-chain tools](https://ethereum.stackexchange.com/questions/40396/can-somebody-please-explain-the-concept-of-event-indexing) that parse events. This is especially useful when it comes to filtering based on an address. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Where applicable, each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three applicable fields, all of the applicable fields should be `indexed`.

```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

24:    event AssetPluginRegistryUpdated(bytes32 versionHash, address asset, bool validity);	// @audit-issue
```
[24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L24-L24), 


#### Recommendation

Enhance smart contract efficiency post-deployment by utilizing indexed events. This approach aids in efficiently tracking contract activities, significantly contributing to the reduction of gas costs.

### Dependence on external protocols
External protocols should be monitored as such dependencies may introduce vulnerabilities if a vulnerability is found /introduced in the external protocol

```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

4:import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L4-L4), 


```solidity
Path: ./contracts/registry/RoleRegistry.sol

5:import { AccessControlEnumerable } from "@openzeppelin/contracts/access/AccessControlEnumerable.sol";	// @audit-issue
```
[5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/RoleRegistry.sol#L5-L5), 


```solidity
Path: ./contracts/mixins/Auth.sol

4:import "@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L4-L4), 


```solidity
Path: ./contracts/mixins/ComponentRegistry.sol

4:import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";	// @audit-issue

5:import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";	// @audit-issue

6:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L6-L6), 


```solidity
Path: ./contracts/libraries/Array.sol

4:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L4-L4), 


```solidity
Path: ./contracts/libraries/Permit.sol

4:import "@openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol";	// @audit-issue

5:import "@openzeppelin/contracts-upgradeable/utils/cryptography/SignatureCheckerUpgradeable.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Permit.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Permit.sol#L5-L5), 


```solidity
Path: ./contracts/p1/Main.sol

4:import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";	// @audit-issue

5:import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";	// @audit-issue

6:import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";	// @audit-issue

7:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L7-L7), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

4:import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";	// @audit-issue

5:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L5-L5), 


```solidity
Path: ./contracts/p1/Distributor.sol

4:import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";	// @audit-issue

5:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue

6:import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L6-L6), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

4:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue

5:import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L5-L5), 


```solidity
Path: ./contracts/p1/BackingManager.sol

4:import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";	// @audit-issue

5:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L5-L5), 


```solidity
Path: ./contracts/p1/StRSR.sol

4:import "@openzeppelin/contracts-upgradeable/interfaces/IERC1271Upgradeable.sol";	// @audit-issue

5:import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";	// @audit-issue

6:import "@openzeppelin/contracts-upgradeable/utils/cryptography/SignatureCheckerUpgradeable.sol";	// @audit-issue

7:import "@openzeppelin/contracts-upgradeable/utils/cryptography/draft-EIP712Upgradeable.sol";	// @audit-issue

8:import "@openzeppelin/contracts-upgradeable/utils/CountersUpgradeable.sol";	// @audit-issue

9:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L7-L7), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L8-L8), [9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L9-L9), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

4:import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";	// @audit-issue

5:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue

6:import "@openzeppelin/contracts/utils/structs/EnumerableMap.sol";	// @audit-issue

7:import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L7-L7), 


```solidity
Path: ./contracts/p1/Deployer.sol

4:import "@openzeppelin/contracts/proxy/Clones.sol";	// @audit-issue

5:import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";	// @audit-issue

6:import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L6-L6), 


```solidity
Path: ./contracts/p1/RToken.sol

5:import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";	// @audit-issue
```
[5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L5-L5), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

4:import "@openzeppelin/contracts-upgradeable/utils/math/SafeCastUpgradeable.sol";	// @audit-issue

5:import "@openzeppelin/contracts-upgradeable/utils/math/MathUpgradeable.sol";	// @audit-issue

6:import "@openzeppelin/contracts-upgradeable/interfaces/IERC5805Upgradeable.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L6-L6), 


```solidity
Path: ./contracts/p1/Broker.sol

4:import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";	// @audit-issue

5:import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";	// @audit-issue

6:import "@openzeppelin/contracts/proxy/Clones.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L6-L6), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

4:import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";	// @audit-issue

5:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L5-L5), 


```solidity
Path: ./contracts/p1/mixins/TradeLib.sol

4:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L4-L4), 


```solidity
Path: ./contracts/p1/mixins/RewardableLib.sol

4:import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";	// @audit-issue

5:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue

6:import "@openzeppelin/contracts/utils/Address.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L6-L6), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

4:import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";	// @audit-issue

5:import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";	// @audit-issue

6:import "@openzeppelin/contracts-upgradeable/utils/ContextUpgradeable.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L6-L6), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

4:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L4-L4), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

4:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue

5:import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";	// @audit-issue

6:import "@openzeppelin/contracts/utils/structs/EnumerableMap.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L6-L6), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

4:import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";	// @audit-issue

5:import "@openzeppelin/contracts/utils/math/Math.sol";	// @audit-issue

6:import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L6-L6), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

4:import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";	// @audit-issue

5:import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L5-L5), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

4:import "@openzeppelin/contracts/governance/Governor.sol";	// @audit-issue

5:import "@openzeppelin/contracts/governance/extensions/GovernorCountingSimple.sol";	// @audit-issue

6:import "@openzeppelin/contracts/governance/extensions/GovernorSettings.sol";	// @audit-issue

7:import "@openzeppelin/contracts/governance/extensions/GovernorTimelockControl.sol";	// @audit-issue

8:import "@openzeppelin/contracts/governance/extensions/GovernorVotes.sol";	// @audit-issue

9:import "@openzeppelin/contracts/governance/extensions/GovernorVotesQuorumFraction.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L7-L7), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L8-L8), [9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L9-L9), 


#### Recommendation

Regularly monitor and review the external protocols your Solidity contracts depend on for any updates or identified vulnerabilities. Consider implementing fallback mechanisms or contingency plans in your contracts to handle potential failures or compromises in these external protocols. Additionally, thoroughly audit and test the integration points with external protocols to ensure they adhere to your contract's security standards. Where possible, design your contract architecture to minimize reliance on external protocols or allow for upgradability in response to changes in these dependencies. Stay informed about developments in the protocols you depend on and actively participate in their community for early awareness of potential issues.

### Incorrect withdraw declaration
In Solidity, it's essential for clarity and interoperability to correctly specify return types in function declarations. If the `withdraw` function is expected to return a `bool` to indicate success or failure, its omission could lead to ambiguity or unexpected behavior when interacting with or calling this function from other contracts or off-chain systems. Missing return values can mislead developers and potentially lead to contract integrations built on incorrect assumptions. To resolve this, the function declaration for `withdraw` should be modified to explicitly include the `bool` return type, ensuring clarity and correctness in contract interactions.

```solidity
Path: ./contracts/p1/StRSR.sol

304:    function withdraw(address account, uint256 endId) external {	// @audit-issue
```
[304](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L304-L304), 


#### Recommendation

Review the function declarations in your Solidity contracts, particularly for critical operations like `withdraw`, to ensure that they correctly specify return types. If a function is intended to indicate success or failure, it should explicitly declare a `bool` return type in its signature. For example, modify the `withdraw` function declaration from:
```solidity
function withdraw(uint256 amount) public {
    // Implementation
}

// to

function withdraw(uint256 amount) public returns (bool) {
    // Implementation
    return true; // Indicate success
}
```


### Subtraction may underflow if result of multiplication is too large
The following expressions may underflow, as the subtrahend could be greater than the minuend in case the former is multiplied by a large number.

```solidity
Path: ./contracts/libraries/Fixed.sol

245:        return _safeWrap(uint256(x) - uint256(y * FIX_SCALE));	// @audit-issue

586:            r *= 2 - c_256 * r;	// @audit-issue

587:            r *= 2 - c_256 * r;	// @audit-issue

588:            r *= 2 - c_256 * r;	// @audit-issue

589:            r *= 2 - c_256 * r;	// @audit-issue

590:            r *= 2 - c_256 * r;	// @audit-issue

591:            r *= 2 - c_256 * r;	// @audit-issue

592:            r *= 2 - c_256 * r;	// @audit-issue

593:            r *= 2 - c_256 * r;	// @audit-issue
```
[245](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L245-L245), [586](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L586-L586), [587](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L587-L587), [588](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L588-L588), [589](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L589-L589), [590](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L590-L590), [591](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L591-L591), [592](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L592-L592), [593](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L593-L593), 


```solidity
Path: ./contracts/p1/StRSR.sol

705:        uint192 withdrawal = _safeWrap((rsrWithdrawal * FIX_ONE + totalRSR - 1) / totalRSR); // {1}	// @audit-issue
```
[705](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L705-L705), 


#### Recommendation

Implement checks to guard against underflow in subtraction operations that follow multiplications in your Solidity contracts. Use SafeMath library or Solidity 0.8.x or higher, which has built-in overflow/underflow checks, to safely perform these arithmetic operations. Before performing the subtraction, ensure that the minuend is greater than or equal to the subtrahend. If using a version of Solidity that does not include automatic checks, consider adding explicit require statements to validate this condition, or use a library like OpenZeppelin's SafeMath to provide these safeguards. Careful handling of arithmetic operations, especially in critical functions involving financial calculations, is crucial for the security and correctness of your contract.

### Bug in Deduplication of Verbatim Blocks
The current Solidity version has the [Bug in Deduplication of Verbatim Blocks](https://soliditylang.org/blog/2023/11/08/verbatim-invalid-deduplication-bug/) issue.

```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L2-L2), 


```solidity
Path: ./contracts/registry/VersionRegistry.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L2-L2), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L2-L2), 


```solidity
Path: ./contracts/registry/RoleRegistry.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/RoleRegistry.sol#L2-L2), 


```solidity
Path: ./contracts/mixins/Versioned.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Versioned.sol#L2-L2), 


```solidity
Path: ./contracts/mixins/Auth.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L2-L2), 


```solidity
Path: ./contracts/mixins/ComponentRegistry.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L2-L2), 


```solidity
Path: ./contracts/libraries/Array.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L2-L2), 


```solidity
Path: ./contracts/libraries/Throttle.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L2-L2), 


```solidity
Path: ./contracts/libraries/Fixed.sol

4:pragma solidity ^0.8.19;	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L4-L4), 


```solidity
Path: ./contracts/libraries/Allowance.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L2-L2), 


```solidity
Path: ./contracts/libraries/Permit.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Permit.sol#L2-L2), 


```solidity
Path: ./contracts/libraries/String.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/String.sol#L2-L2), 


```solidity
Path: ./contracts/p1/Main.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L2-L2), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L2-L2), 


```solidity
Path: ./contracts/p1/Distributor.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L2-L2), 


```solidity
Path: ./contracts/p1/Furnace.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L2-L2), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L2-L2), 


```solidity
Path: ./contracts/p1/BackingManager.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L2-L2), 


```solidity
Path: ./contracts/p1/StRSR.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L2-L2), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L2-L2), 


```solidity
Path: ./contracts/p1/Deployer.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L2-L2), 


```solidity
Path: ./contracts/p1/RToken.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L2-L2), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L2-L2), 


```solidity
Path: ./contracts/p1/Broker.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L2-L2), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L2-L2), 


```solidity
Path: ./contracts/p1/mixins/TradeLib.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L2-L2), 


```solidity
Path: ./contracts/p1/mixins/RewardableLib.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L2-L2), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L2-L2), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L2-L2), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L2-L2), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L2-L2), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L2-L2), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

2:pragma solidity 0.8.19;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L2-L2), 


#### Recommendation

It is recommended to follow the fix provided in the [link](https://soliditylang.org/blog/2023/11/08/verbatim-invalid-deduplication-bug/).

### Use `bytes.concat()` on `bytes` instead of `abi.encodePacked()` for clearer semantic meaning
Starting with version 0.8.4, Solidity has the `bytes.concat()` function, which allows one to concatenate a list of bytes/strings, without extra padding. Using this function rather than `abi.encodePacked()` makes the intended operation more clear, leading to less reviewer confusion.

```solidity
Path: ./contracts/registry/VersionRegistry.sol

47:        bytes32 versionHash = keccak256(abi.encodePacked(version));	// @audit-issue
```
[47](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L47-L47), 


```solidity
Path: ./contracts/libraries/Permit.sol

19:                IERC1271Upgradeable(owner).isValidSignature(hash, abi.encodePacked(r, s, v)) ==	// @audit-issue

28:                    abi.encodePacked(r, s, v)	// @audit-issue
```
[19](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Permit.sol#L19-L19), [28](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Permit.sol#L28-L28), 


```solidity
Path: ./contracts/p1/Main.sol

108:        require(keccak256(abi.encodePacked(this.version())) == versionHash, "upgrade mismatch");	// @audit-issue

117:        require(keccak256(abi.encodePacked(this.version())) == versionHash, "upgrade main first");	// @audit-issue
```
[108](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L108-L108), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L117-L117), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

181:                        keccak256(abi.encodePacked(this.version())),	// @audit-issue

225:                    keccak256(abi.encodePacked(this.version())),	// @audit-issue
```
[181](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L181-L181), [225](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L225-L225), 


```solidity
Path: ./contracts/p1/Deployer.sol

228:            string memory stRSRSymbol = string(abi.encodePacked(StringLib.toLower(symbol), "RSR"));	// @audit-issue

229:            string memory stRSRName = string(abi.encodePacked(stRSRSymbol, " Token"));	// @audit-issue
```
[228](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L228-L228), [229](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L229-L229), 


#### Recommendation

Consider using `bytes.concat()` instead of `abi.encodePacked()` for concatenating `bytes` for clearer semantic meaning, especially in Solidity versions 0.8.4 and later.

### Use scientific notation (e.g. `1e18`) rather than exponentiation (e.g. `10**18`)
While the compiler knows to optimize away the exponentiation, it's still better coding practice to use idioms that do not require compiler optimization, if they exist


```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

7:uint256 constant FEE_DENOMINATOR = 100_00;	// @audit-issue: This number `100_00` can be written as `1e4`
```
[7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L7-L7), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

15:    uint256 public constant GAS_FOR_BH_QTY = 100_000; // enough to call bh.quantity	// @audit-issue: This number `100_000` can be written as `1e5`
```
[15](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L15-L15), 


#### Recommendation

Consider using scientific notation (e.g., `1e18`) instead of exponentiation (e.g., `10**18`) for better coding practice. While the compiler can optimize exponentiation, using scientific notation makes the code more readable and relies on standard idioms.

### Hardcoded string that is repeatedly used can be replaced with a constant
For better maintainability, please consider creating and using a constant for those strings instead of hardcoding them.

```solidity
Path: ./contracts/p1/Main.sol

62:        require(address(versionRegistry_) != address(0), "invalid registry address");	// @audit-issue: This string `invalid registry address` is used also on line(s): `[71, 80]`

63:        require(address(versionRegistry) == address(0), "already set");	// @audit-issue: This string `already set` is used also on line(s): `[72, 81]`

100:        require(address(versionRegistry) != address(0), "no registry");	// @audit-issue: This string `no registry` is used also on line(s): `[116]`
```
[62](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L62-L62), [63](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L63-L63), [100](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L100-L100), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

127:        require(_erc20s.contains(address(erc20)), "erc20 unregistered");	// @audit-issue: This string `erc20 unregistered` is used also on line(s): `[135]`

184:                    "unsupported asset"	// @audit-issue: This string `unsupported asset` is used also on line(s): `[228]`
```
[127](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L127-L127), [184](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L184-L184), 


```solidity
Path: ./contracts/p1/BackingManager.sol

120:        require(tradesOpen == 0, "trade open");	// @audit-issue: This string `trade open` is used also on line(s): `[187]`

121:        require(basketHandler.isReady(), "basket not ready");	// @audit-issue: This string `basket not ready` is used also on line(s): `[188]`

122:        require(block.timestamp >= basketHandler.timestamp() + tradingDelay, "trading delayed");	// @audit-issue: This string `trading delayed` is used also on line(s): `[189]`
```
[120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L120-L120), [121](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L121-L121), [122](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L122-L122), 


```solidity
Path: ./contracts/p1/StRSR.sol

264:        require(stakes[era][account] >= stakeAmount, "insufficient balance");	// @audit-issue: This string `insufficient balance` is used also on line(s): `[829]`

312:        require(endId <= queue.length, "index out-of-bounds");	// @audit-issue: This string `index out-of-bounds` is used also on line(s): `[357]`
```
[264](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L264-L264), [312](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L312-L312), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

233:        require(erc20s.length != 0 && erc20s.length == targetAmts.length, "invalid lengths");	// @audit-issue: This string `invalid lengths` is used also on line(s): `[526]`
```
[233](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L233-L233), 


```solidity
Path: ./contracts/p1/RToken.sol

191:        require(amount != 0, "Cannot redeem zero");	// @audit-issue: This string `Cannot redeem zero` is used also on line(s): `[266]`

192:        require(amount <= balanceOf(caller), "insufficient balance");	// @audit-issue: This string `insufficient balance` is used also on line(s): `[267]`

357:        require(_msgSender() == address(backingManager), "not backing manager");	// @audit-issue: This string `not backing manager` is used also on line(s): `[389, 398]`
```
[191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L191-L191), [192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L192-L192), [357](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L357-L357), 


```solidity
Path: ./contracts/p1/Broker.sol

88:            "invalid batchTradeImplementation address"	// @audit-issue: This string `invalid batchTradeImplementation address` is used also on line(s): `[189]`

92:            "invalid dutchTradeImplementation address"	// @audit-issue: This string `invalid dutchTradeImplementation address` is used also on line(s): `[211]`
```
[88](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L88-L88), [92](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L92-L92), 


```solidity
Path: ./contracts/p1/mixins/RewardableLib.sol

30:                abi.encodeWithSignature("claimRewards()"),	// @audit-issue: This string `claimRewards()` is used also on line(s): `[42]`

31:                "rewards claim failed"	// @audit-issue: This string `rewards claim failed` is used also on line(s): `[43]`
```
[30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L30-L30), [31](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L31-L31), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

200:        require(bidder == address(0), "bid already received");	// @audit-issue: This string `bid already received` is used also on line(s): `[236]`
```
[200](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L200-L200), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

121:        require(startedInSameEra(proposalId), "new era");	// @audit-issue: This string `new era` is used also on line(s): `[144]`
```
[121](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L121-L121), 


#### Recommendation

Identify hardcoded strings that are used repeatedly in your Solidity contract and replace them with constants. Define these constants at the beginning of your contract or in a separate dedicated contract or library if they are shared across multiple contracts. This practice centralizes the management of these values, making your code more maintainable and reducing the risk of inconsistencies or errors when changes are required. Refactoring to use constants not only simplifies updates but also enhances the readability and clarity of your code.

### Style Guide: Surround top level declarations in Solidity source with two blank lines.
1- Surround top level declarations in Solidity source with two blank lines.
2- Within a contract surround function declarations with a single blank line.


```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

63:    }
64:
65:    /// @dev A fee below 1% not recommended due to poor precision in the Distributor	// @audit-issue: There should be single blank line between function declarations.
66:    function setRTokenFeeNumerator(address rToken, uint256 feeNumerator_) external onlyOwner {
```
[65](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L63-L66), 


```solidity
Path: ./contracts/registry/VersionRegistry.sol

33:    }
34:
35:    /// Register a deployer address, keyed by version.
36:    /// @param deployer The deployer contract address for the version to be added.	// @audit-issue: There should be single blank line between function declarations.
37:    function registerVersion(IDeployer deployer) external {
```
[36](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L33-L37), 


```solidity
Path: ./contracts/mixins/Auth.sol

46:    bool public issuancePaused;
47:
48:    /* ==== Invariants ====
49:       0 <= longFreeze[a] <= LONG_FREEZE_CHARGES for all addrs a
50:       set{a has LONG_FREEZER} == set{a : longFreeze[a] == 0}
51:    */
52:
53:    // checks:
54:    // - __Auth_init has not previously been called
55:    // - 0 < shortFreeze_ <= MAX_SHORT_FREEZE
56:    // - 0 < longFreeze_ <= MAX_LONG_FREEZE
57:    // effects:
58:    // - caller has only the OWNER role
59:    // - OWNER is the admin role for all roles
60:    // - shortFreeze' == shortFreeze_
61:    // - longFreeze' == longFreeze_
62:    // questions: (what do I know about the values of paused and unfreezeAt?)
63:    // untestable:
64:    //      `else` branch of `onlyInitializing` (ie. revert) is currently untestable.
65:    //      This function is only called inside other `init` functions, each of which is wrapped
66:    //      in an `initializer` modifier, which would fail first.
67:    // solhint-disable-next-line func-name-mixedcase	// @audit-issue: There should be single blank line between function declarations.
68:    function __Auth_init(uint48 shortFreeze_, uint48 longFreeze_) internal onlyInitializing {

81:    }
82:
83:    // checks: caller is an admin for role, account is not 0
84:    // effects:
85:    // - account has the `role` role
86:    // - if role is LONG_FREEZER, then longFreezes'[account] == LONG_FREEZE_CHARGES	// @audit-issue: There should be single blank line between function declarations.
87:    function grantRole(bytes32 role, address account)

95:    }
96:
97:    // ==== System-wide views ====
98:    // returns: bool(main is frozen) == now < unfreezeAt	// @audit-issue: There should be single blank line between function declarations.
99:    function frozen() public view returns (bool) {

101:    }
102:
103:    /// @dev This -or- condition is a performance optimization for the consuming Component
104:    // returns: bool(main is frozen or tradingPaused) == tradingPaused || (now < unfreezeAt)	// @audit-issue: There should be single blank line between function declarations.
105:    function tradingPausedOrFrozen() public view returns (bool) {

107:    }
108:
109:    /// @dev This -or- condition is a performance optimization for the consuming Component
110:    // returns: bool(main is frozen or issuancePaused) == issuancePaused || (now < unfreezeAt)	// @audit-issue: There should be single blank line between function declarations.
111:    function issuancePausedOrFrozen() public view returns (bool) {

113:    }
114:
115:    // === Freezing ===
116:
117:    /// Enter a freeze for the `shortFreeze` duration
118:    // checks:
119:    // - caller has the SHORT_FREEZER role
120:    // - now + shortFreeze >= unfreezeAt (that is, this call should increase unfreezeAt)
121:    // effects:
122:    // - unfreezeAt' = now + shortFreeze
123:    // - after, caller does not have the SHORT_FREEZER role	// @audit-issue: There should be single blank line between function declarations.
124:    function freezeShort() external onlyRole(SHORT_FREEZER) {

128:    }
129:
130:    /// Enter a freeze by the `longFreeze` duration
131:    // checks:
132:    // - caller has the LONG_FREEZER role
133:    // - longFreezes[caller] > 0
134:    // - now + longFreeze >= unfreezeAt (that is, this call should increase unfreezeAt)
135:    // effects:
136:    // - unfreezeAt' = now + longFreeze
137:    // - longFreezes'[caller] = longFreezes[caller] - 1
138:    // - if longFreezes'[caller] == 0 then caller loses the LONG_FREEZER role	// @audit-issue: There should be single blank line between function declarations.
139:    function freezeLong() external onlyRole(LONG_FREEZER) {

145:    }
146:
147:    /// Enter a permanent freeze
148:    // checks:
149:    // - caller has the OWNER role
150:    // - unfreezeAt != type(uint48).max
151:    // effects: unfreezeAt' = type(uint48).max	// @audit-issue: There should be single blank line between function declarations.
152:    function freezeForever() external onlyRole(OWNER) {

154:    }
155:
156:    /// End all freezes
157:    // checks:
158:    // - unfreezeAt > now  (i.e, frozen() == true before the call)
159:    // - caller has the OWNER role
160:    // effects: unfreezeAt' = now  (i.e, frozen() == false after the call)	// @audit-issue: There should be single blank line between function declarations.
161:    function unfreeze() external onlyRole(OWNER) {

164:    }
165:
166:    // === Pausing ===
167:    // checks: caller has PAUSER
168:    // effects: tradingPaused' = true	// @audit-issue: There should be single blank line between function declarations.
169:    function pauseTrading() external onlyRole(PAUSER) {

172:    }
173:
174:    // checks: caller has PAUSER
175:    // effects: tradingPaused' = false	// @audit-issue: There should be single blank line between function declarations.
176:    function unpauseTrading() external onlyRole(PAUSER) {

179:    }
180:
181:    // checks: caller has PAUSER
182:    // effects: issuancePaused' = true	// @audit-issue: There should be single blank line between function declarations.
183:    function pauseIssuance() external onlyRole(PAUSER) {

186:    }
187:
188:    // checks: caller has PAUSER
189:    // effects: issuancePaused' = false	// @audit-issue: There should be single blank line between function declarations.
190:    function unpauseIssuance() external onlyRole(PAUSER) {

193:    }
194:
195:    // === Gov params ===
196:
197:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
198:    function setShortFreeze(uint48 shortFreeze_) public onlyRole(OWNER) {

202:    }
203:
204:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
205:    function setLongFreeze(uint48 longFreeze_) public onlyRole(OWNER) {

209:    }
210:
211:    // === Private Helper ===
212:    // checks: newUnfreezeAt > unfreezeAt
213:    // effects: unfreezeAt' = newUnfreezeAt	// @audit-issue: There should be single blank line between function declarations.
214:    function freezeUntil(uint48 newUnfreezeAt) private {
```
[67](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L46-L68), [86](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L81-L87), [98](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L95-L99), [104](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L101-L105), [110](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L107-L111), [123](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L113-L124), [138](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L128-L139), [151](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L145-L152), [160](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L154-L161), [168](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L164-L169), [175](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L172-L176), [182](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L179-L183), [189](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L186-L190), [197](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L193-L198), [204](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L202-L205), [213](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L209-L214), 


```solidity
Path: ./contracts/libraries/Array.sol

17:    }
18:
19:    /// O(n) -- must already be in sorted ascending order!
20:    /// @return If the array contains all unique addresses, in ascending order	// @audit-issue: There should be single blank line between function declarations.
21:    function sortedAndAllUnique(IERC20[] memory arr) internal pure returns (bool) {
```
[20](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L17-L21), 


```solidity
Path: ./contracts/libraries/Throttle.sol

31:    }
32:
33:    /// Reverts if usage amount exceeds available amount
34:    /// @param supply {qRTok} Total RToken supply beforehand
35:    /// @param amount {qRTok} Amount of RToken to use. Should be negative for the issuance
36:    ///   throttle during redemption and for the redemption throttle during issuance.	// @audit-issue: There should be single blank line between function declarations.
37:    function useAvailable(

65:    }
66:
67:    /// @param limit {qRTok/hour} The hourly limit
68:    /// @return available {qRTok} Amount currently available for consumption	// @audit-issue: There should be single blank line between function declarations.
69:    function currentlyAvailable(Throttle storage throttle, uint256 limit)

77:    }
78:
79:    /// @return limit {qRTok} The hourly limit	// @audit-issue: There should be single blank line between function declarations.
80:    function hourlyLimit(Throttle storage throttle, uint256 supply)
```
[36](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L31-L37), [68](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L65-L69), [79](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L77-L80), 


```solidity
Path: ./contracts/libraries/Fixed.sol

185:    }
186:
187:    /// Convert this uint192 to a uint
188:    /// @return x
189:    // as-ints: x / 1e18 with rounding	// @audit-issue: There should be single blank line between function declarations.
190:    function toUint(uint192 x, RoundingMode rounding) internal pure returns (uint136) {

192:    }
193:
194:    /// Return the uint192 shifted to the left by `decimal` digits
195:    /// (Similar to a bitshift but in base 10)
196:    /// @return x * 10**decimals
197:    // as-ints: x * 10**decimals	// @audit-issue: There should be single blank line between function declarations.
198:    function shiftl(uint192 x, int8 decimals) internal pure returns (uint192) {

200:    }
201:
202:    /// Return the uint192 shifted to the left by `decimal` digits
203:    /// (Similar to a bitshift but in base 10)
204:    /// @return x * 10**decimals
205:    // as-ints: x * 10**decimals	// @audit-issue: There should be single blank line between function declarations.
206:    function shiftl(

218:    }
219:
220:    /// Add a uint192 to this uint192
221:    /// @return x + y
222:    // as-ints: x + y	// @audit-issue: There should be single blank line between function declarations.
223:    function plus(uint192 x, uint192 y) internal pure returns (uint192) {

225:    }
226:
227:    /// Add a uint to this uint192
228:    /// @return x + y
229:    // as-ints: x + y*1e18	// @audit-issue: There should be single blank line between function declarations.
230:    function plusu(uint192 x, uint256 y) internal pure returns (uint192) {

232:    }
233:
234:    /// Subtract a uint192 from this uint192
235:    /// @return x - y
236:    // as-ints: x - y	// @audit-issue: There should be single blank line between function declarations.
237:    function minus(uint192 x, uint192 y) internal pure returns (uint192) {

239:    }
240:
241:    /// Subtract a uint from this uint192
242:    /// @return x - y
243:    // as-ints: x - y*1e18	// @audit-issue: There should be single blank line between function declarations.
244:    function minusu(uint192 x, uint256 y) internal pure returns (uint192) {

246:    }
247:
248:    /// Multiply this uint192 by a uint192
249:    /// Round truncated values to the nearest available value. 5e-19 rounds away from zero.
250:    /// @return x * y
251:    // as-ints: x * y/1e18  [division using ROUND, not FLOOR]	// @audit-issue: There should be single blank line between function declarations.
252:    function mul(uint192 x, uint192 y) internal pure returns (uint192) {

254:    }
255:
256:    /// Multiply this uint192 by a uint192
257:    /// @return x * y
258:    // as-ints: x * y/1e18	// @audit-issue: There should be single blank line between function declarations.
259:    function mul(

265:    }
266:
267:    /// Multiply this uint192 by a uint
268:    /// @return x * y
269:    // as-ints: x * y	// @audit-issue: There should be single blank line between function declarations.
270:    function mulu(uint192 x, uint256 y) internal pure returns (uint192) {

272:    }
273:
274:    /// Divide this uint192 by a uint192
275:    /// @return x / y
276:    // as-ints: x * 1e18 / y	// @audit-issue: There should be single blank line between function declarations.
277:    function div(uint192 x, uint192 y) internal pure returns (uint192) {

279:    }
280:
281:    /// Divide this uint192 by a uint192
282:    /// @return x / y
283:    // as-ints: x * 1e18 / y	// @audit-issue: There should be single blank line between function declarations.
284:    function div(

291:    }
292:
293:    /// Divide this uint192 by a uint
294:    /// @return x / y
295:    // as-ints: x / y	// @audit-issue: There should be single blank line between function declarations.
296:    function divu(uint192 x, uint256 y) internal pure returns (uint192) {

298:    }
299:
300:    /// Divide this uint192 by a uint
301:    /// @return x / y
302:    // as-ints: x / y	// @audit-issue: There should be single blank line between function declarations.
303:    function divu(

311:    uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;
312:
313:    /// Raise this uint192 to a nonnegative integer power. Requires that x_ <= FIX_ONE
314:    /// Gas cost is O(lg(y)), precision is +- 1e-18.
315:    /// @return x_ ** y
316:    // as-ints: x_ ** y / 1e18**(y-1)    <- technically correct for y = 0. :D	// @audit-issue: There should be single blank line between function declarations.
317:    function powu(uint192 x_, uint48 y) internal pure returns (uint192) {

334:    }
335:
336:    /// Comparison operators...	// @audit-issue: There should be single blank line between function declarations.
337:    function lt(uint192 x, uint192 y) internal pure returns (bool) {

359:    }
360:
361:    /// Return whether or not this uint192 is less than epsilon away from y.
362:    /// @return |x - y| < epsilon
363:    // as-ints: |x - y| < epsilon	// @audit-issue: There should be single blank line between function declarations.
364:    function near(

371:    }
372:
373:    // ================ Chained Operations ================
374:    // The operation foo_bar() always means:
375:    //   Do foo() followed by bar(), and overflow only if the _end_ result doesn't fit in an uint192
376:
377:    /// Shift this uint192 left by `decimals` digits, and convert to a uint
378:    /// @return x * 10**decimals
379:    // as-ints: x * 10**(decimals - 18)	// @audit-issue: There should be single blank line between function declarations.
380:    function shiftl_toUint(uint192 x, int8 decimals) internal pure returns (uint256) {

382:    }
383:
384:    /// Shift this uint192 left by `decimals` digits, and convert to a uint.
385:    /// @return x * 10**decimals
386:    // as-ints: x * 10**(decimals - 18)	// @audit-issue: There should be single blank line between function declarations.
387:    function shiftl_toUint(

401:    }
402:
403:    /// Multiply this uint192 by a uint, and output the result as a uint
404:    /// @return x * y
405:    // as-ints: x * y / 1e18	// @audit-issue: There should be single blank line between function declarations.
406:    function mulu_toUint(uint192 x, uint256 y) internal pure returns (uint256) {

408:    }
409:
410:    /// Multiply this uint192 by a uint, and output the result as a uint
411:    /// @return x * y
412:    // as-ints: x * y / 1e18	// @audit-issue: There should be single blank line between function declarations.
413:    function mulu_toUint(

419:    }
420:
421:    /// Multiply this uint192 by a uint192 and output the result as a uint
422:    /// @return x * y
423:    // as-ints: x * y / 1e36	// @audit-issue: There should be single blank line between function declarations.
424:    function mul_toUint(uint192 x, uint192 y) internal pure returns (uint256) {

426:    }
427:
428:    /// Multiply this uint192 by a uint192 and output the result as a uint
429:    /// @return x * y
430:    // as-ints: x * y / 1e36	// @audit-issue: There should be single blank line between function declarations.
431:    function mul_toUint(

437:    }
438:
439:    /// Compute x * y / z avoiding intermediate overflow
440:    /// @dev Only use if you need to avoid overflow; costlier than x * y / z
441:    /// @return x * y / z
442:    // as-ints: x * y / z	// @audit-issue: There should be single blank line between function declarations.
443:    function muluDivu(

449:    }
450:
451:    /// Compute x * y / z, avoiding intermediate overflow
452:    /// @dev Only use if you need to avoid overflow; costlier than x * y / z
453:    /// @return x * y / z
454:    // as-ints: x * y / z	// @audit-issue: There should be single blank line between function declarations.
455:    function muluDivu(

462:    }
463:
464:    /// Compute x * y / z on Fixes, avoiding intermediate overflow
465:    /// @dev Only use if you need to avoid overflow; costlier than x * y / z
466:    /// @return x * y / z
467:    // as-ints: x * y / z	// @audit-issue: There should be single blank line between function declarations.
468:    function mulDiv(

474:    }
475:
476:    /// Compute x * y / z on Fixes, avoiding intermediate overflow
477:    /// @dev Only use if you need to avoid overflow; costlier than x * y / z
478:    /// @return x * y / z
479:    // as-ints: x * y / z	// @audit-issue: There should be single blank line between function declarations.
480:    function mulDiv(

487:    }
488:
489:    // === safe*() ===
490:
491:    /// Multiply two fixes, rounding up to FIX_MAX and down to 0
492:    /// @param a First param to multiply
493:    /// @param b Second param to multiply	// @audit-issue: There should be single blank line between function declarations.
494:    function safeMul(

539:    }
540:
541:    /// Divide two fixes, rounding up to FIX_MAX and down to 0
542:    /// @param a Numerator
543:    /// @param b Denominator	// @audit-issue: There should be single blank line between function declarations.
544:    function safeDiv(

555:    }
556:
557:    /// Multiplies two fixes and divide by a third
558:    /// @param a First to multiply
559:    /// @param b Second to multiply
560:    /// @param c Denominator	// @audit-issue: There should be single blank line between function declarations.
561:    function safeMulDiv(
```
[189](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L185-L190), [197](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L192-L198), [205](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L200-L206), [222](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L218-L223), [229](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L225-L230), [236](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L232-L237), [243](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L239-L244), [251](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L246-L252), [258](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L254-L259), [269](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L265-L270), [276](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L272-L277), [283](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L279-L284), [295](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L291-L296), [302](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L298-L303), [316](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L311-L317), [336](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L334-L337), [363](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L359-L364), [379](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L371-L380), [386](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L382-L387), [405](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L401-L406), [412](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L408-L413), [423](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L419-L424), [430](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L426-L431), [442](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L437-L443), [454](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L449-L455), [467](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L462-L468), [479](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L474-L480), [493](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L487-L494), [543](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L539-L544), [560](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L555-L561), 


```solidity
Path: ./contracts/libraries/Allowance.sol

8:}
9:	// @audit-issue: There should be at least two blank lines between top level declarations.
10:library AllowanceLib {
```
[9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L8-L10), 


```solidity
Path: ./contracts/p1/Main.sol

26:    DAOFeeRegistry public daoFeeRegistry;
27:
28:    /// @custom:oz-upgrades-unsafe-allow constructor
29:    // solhint-disable-next-line no-empty-blocks	// @audit-issue: There should be single blank line between function declarations.
30:    constructor() initializer {}

30:    constructor() initializer {}
31:
32:    /// Initializer	// @audit-issue: There should be single blank line between function declarations.
33:    function init(

46:    }
47:
48:    /// @custom:refresher
49:    /// @custom:interaction CEI
50:    /// @dev Not intended to be used in production, only for equivalence with P0	// @audit-issue: There should be single blank line between function declarations.
51:    function poke() external {

57:    }
58:
59:    /// Set Version Registry
60:    /// @dev Can only be called once.	// @audit-issue: There should be single blank line between function declarations.
61:    function setVersionRegistry(VersionRegistry versionRegistry_) external onlyRole(OWNER) {

66:    }
67:
68:    /// Set Asset Plugin Registry
69:    /// @dev Can only be called once.	// @audit-issue: There should be single blank line between function declarations.
70:    function setAssetPluginRegistry(AssetPluginRegistry registry_) external onlyRole(OWNER) {

75:    }
76:
77:    /// Set DAO Fee Registry
78:    /// @dev Can only be called once.	// @audit-issue: There should be single blank line between function declarations.
79:    function setDAOFeeRegistry(DAOFeeRegistry feeRegistry_) external onlyRole(OWNER) {

93:    }
94:
95:    /**
96:     * @dev When upgrading from a prior version to 4.0.0,
97:     *      this must happen in the Governance proposal.
98:     */	// @audit-issue: There should be single blank line between function declarations.
99:    function upgradeMainTo(bytes32 versionHash) external onlyRole(OWNER) {

150:    }
151:
152:    // === Upgradeability ===	// @audit-issue: There should be single blank line between function declarations.
153:    function _authorizeUpgrade(address) internal view override {
```
[29](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L26-L30), [32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L30-L33), [50](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L46-L51), [60](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L57-L61), [69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L66-L70), [78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L75-L79), [98](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L93-L99), [152](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L150-L153), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

38:    }
39:
40:    /// Call after upgrade to >= 3.0.0	// @audit-issue: There should be single blank line between function declarations.
41:    function cacheComponents() public {

48:    }
49:
50:    /// Settle a single trade + distribute revenue
51:    /// @param sell The sell token in the trade
52:    /// @return trade The ITrade contract settled
53:    /// @custom:interaction	// @audit-issue: There should be single blank line between function declarations.
54:    function settleTrade(IERC20 sell) public override(ITrading, TradingP1) returns (ITrade trade) {

65:    }
66:
67:    /// Distribute tokenToBuy to its destinations
68:    /// @dev Special-case of manageTokens([tokenToBuy], *)
69:    /// @custom:interaction	// @audit-issue: There should be single blank line between function declarations.
70:    function distributeTokenToBuy() external notTradingPausedOrFrozen {

72:    }
73:
74:    /// Return registered ERC20s to the BackingManager if distribution for tokenToBuy is 0
75:    /// @custom:interaction	// @audit-issue: There should be single blank line between function declarations.
76:    function returnTokens(IERC20[] memory erc20s) external notTradingPausedOrFrozen {

93:    }
94:
95:    /// Process some number of tokens
96:    /// If the tokenToBuy is included in erc20s, RevenueTrader will distribute it at end of the tx
97:    /// @param erc20s The ERC20s to manage; can be tokenToBuy or anything registered
98:    /// @param kinds The kinds of auctions to launch: DUTCH_AUCTION | BATCH_AUCTION
99:    /// @custom:interaction not strictly RCEI; nonReentrant
100:    // let bal = this contract's balance of erc20
101:    // checks: !paused (trading), !frozen
102:    // does nothing if erc20 == addr(0) or bal == 0
103:    //
104:    // For each ERC20:
105:    //   if erc20 is tokenToBuy: distribute it
106:    //   else: sell erc20 for tokenToBuy
107:    // untested:
108:    //      OZ nonReentrant line is assumed to be working. cost/benefit of direct testing is high	// @audit-issue: There should be single blank line between function declarations.
109:    function manageTokens(IERC20[] calldata erc20s, TradeKind[] calldata kinds)

183:    }
184:
185:    // === Internal ===
186:
187:    /// Distribute tokenToBuy to its destinations
188:    /// @dev Assumes notTradingPausedOrFrozen has already been checked!	// @audit-issue: There should be single blank line between function declarations.
189:    function _distributeTokenToBuy() internal {
```
[40](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L38-L41), [53](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L48-L54), [69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L65-L70), [75](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L72-L76), [108](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L93-L109), [188](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L183-L189), 


```solidity
Path: ./contracts/p1/Distributor.sol

51:    }
52:
53:    /// Set the RevenueShare for destination `dest`. Destinations `FURNACE` and `ST_RSR` refer to
54:    /// main.furnace() and main.stRSR().
55:    /// Consider calling `BackingManager.forwardRevenue()` before to ensure fair past distribution
56:    /// @custom:governance
57:    // checks: invariants hold in post-state
58:    // effects:
59:    //   destinations' = destinations.add(dest)
60:    //   distribution' = distribution.set(dest, share)	// @audit-issue: There should be single blank line between function declarations.
61:    function setDistribution(address dest, RevenueShare calldata share) external governance {

71:    }
72:
73:    /// Set RevenueShares for destinations. Destinations `FURNACE` and `ST_RSR` refer to
74:    /// main.furnace() and main.stRSR().
75:    /// Consider calling `BackingManager.forwardRevenue()` before to ensure fair past distribution
76:    /// @custom:governance
77:    // checks: invariants hold in post-state
78:    // effects:
79:    //   destinations' = destinations.add(dests[i]) for i < dests.length
80:    //   distribution' = distribution.set(dests[i], shares[i]) for i < dests.length	// @audit-issue: There should be single blank line between function declarations.
81:    function setDistributions(address[] calldata dests, RevenueShare[] calldata shares)

103:    }
104:
105:    /// Distribute revenue, in rsr or rtoken, per the distribution table.
106:    /// Requires that this contract has an allowance of at least
107:    /// `amount` tokens, from `from`, of the token at `erc20`.
108:    /// Only callable by RevenueTraders
109:    /// @custom:protected CEI
110:    // let:
111:    //   w = the map such that w[dest] = distribution[dest].{erc20}Shares
112:    //   tokensPerShare = floor(amount / sum(values(w)))
113:    //   addrOf(dest) = 1 -> furnace | 2 -> stRSR | x -> x
114:    // checks:
115:    //   erc20 is in {rsr, rToken}
116:    //   sum(values(w)) > 0
117:    // actions:
118:    //   for dest where w[dest] != 0:
119:    //     erc20.transferFrom(from, addrOf(dest), tokensPerShare * w[dest])	// @audit-issue: There should be single blank line between function declarations.
120:    function distribute(IERC20 erc20, uint256 amount) external {

200:    }
201:
202:    /// The rsr and rToken shareTotals
203:    /// @return revTotals equals sum(distribution[d] for d in distribution)	// @audit-issue: There should be single blank line between function declarations.
204:    function totals() public view returns (RevenueTotals memory revTotals) {

227:    }
228:
229:    // ==== Internal ====
230:
231:    /// Set a distribution pair
232:    // checks:
233:    //   distribution'[FURNACE].rsrDist == 0
234:    //   distribution'[ST_RSR].rTokenDist == 0
235:    //   share.rsrDist <= MAX_DISTRIBUTION
236:    //   size(destinations') <= MAX_DESTINATIONS_ALLOWED
237:    // effects:
238:    //   destinations' = destinations.add(dest)
239:    //   distribution' = distribution.set(dest, share)	// @audit-issue: There should be single blank line between function declarations.
240:    function _setDistribution(address dest, RevenueShare memory share) internal {

261:    }
262:
263:    /// Ensures distribution values are large enough
264:    // checks: sum exceeds MAX_DISTRIBUTION	// @audit-issue: There should be single blank line between function declarations.
265:    function _ensureSufficientTotal(uint24 rTokenTotal, uint24 rsrTotal) internal pure {

267:    }
268:
269:    /// Call after upgrade to >= 3.0.0	// @audit-issue: There should be single blank line between function declarations.
270:    function cacheComponents() public {
```
[60](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L51-L61), [80](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L71-L81), [119](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L103-L120), [203](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L200-L204), [239](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L227-L240), [264](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L261-L265), [269](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L267-L270), 


```solidity
Path: ./contracts/p1/Furnace.sol

24:    uint256 public lastPayoutBal; // {qRTok} The balance of RToken at the last payout
25:
26:    // ==== Invariants ====
27:    // ratio <= MAX_RATIO = 1e18
28:    // lastPayout was the timestamp of the end of the last period we paid out
29:    //   (or, if no periods have been paid out, the timestamp init() was called)
30:    // lastPayoutBal was rtoken.balanceOf(this) after the last period we paid out
31:    //   (or, if no periods have been paid out, that balance when init() was called)
32:	// @audit-issue: There should be single blank line between function declarations.
33:    function init(IMain main_, uint192 ratio_) external initializer {

39:    }
40:
41:    // [furnace-payout-formula]:
42:    //   The process we're modelling is:
43:    //     N = number of whole periods since lastPayout
44:    //     bal_0 = rToken.balanceOf(this)
45:    //     payout_{i+1} = bal_i * ratio
46:    //     bal_{i+1} = bal_i - payout_{i+1}
47:    //     payoutAmount = sum{payout_i for i in [1...N]}
48:    //   thus:
49:    //     bal_N = bal_0 - payout
50:    //     bal_{i+1} = bal_i - bal_i * ratio = bal_i * (1-ratio)
51:    //     bal_N = bal_0 * (1-ratio)**N
52:    //   and so:
53:    //     payoutAmount = bal_N - bal_0 = bal_0 * (1 - (1-ratio)**N)
54:
55:    /// Performs any melting that has vested since last call.
56:    /// @custom:refresher
57:    // let numPeriods = number of whole periods that have passed since `lastPayout`
58:    //     payoutAmount = RToken.balanceOf(this) * (1 - (1-ratio)**N) from [furnace-payout-formula]
59:    // effects:
60:    //   lastPayout' = lastPayout + numPeriods (end of last pay period)
61:    //   lastPayoutBal' = rToken.balanceOf'(this) (balance now == at end of pay leriod)
62:    // actions:
63:    //   rToken.melt(payoutAmount), paying payoutAmount to RToken holders
64:	// @audit-issue: There should be single blank line between function declarations.
65:    function melt() public {

79:    }
80:
81:    /// Ratio setting
82:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
83:    function setRatio(uint192 ratio_) public governance {
```
[32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L24-L33), [64](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L39-L65), [82](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L79-L83), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

30:    uint48 public lastRefresh; // {s}
31:
32:    /* ==== Contract Invariants ====
33:       The contract state is just the mapping assets; _erc20s is ignored in properties.
34:
35:       invariant: _erc20s == keys(assets)
36:       invariant: addr == assets[addr].erc20()
37:           where: addr in assets
38:     */
39:
40:    /// Initialize the AssetRegistry with assets
41:    // effects: assets' = {a.erc20(): a for a in assets_}	// @audit-issue: There should be single blank line between function declarations.
42:    function init(IMain main_, IAsset[] calldata assets_) external initializer {

51:    }
52:
53:    /// Update the state of all assets
54:    /// @custom:refresher
55:    // actions:
56:    //   calls refresh(c) for c in keys(assets) when c.isCollateral()
57:    //   tracks basket status on basketHandler	// @audit-issue: There should be single blank line between function declarations.
58:    function refresh() public {

69:    }
70:
71:    /// Register `asset`
72:    /// If either the erc20 address or the asset was already registered, fail
73:    /// @return true if the erc20 address was not already registered.
74:    /// @custom:governance
75:    // checks: asset.erc20() not in keys(assets) or assets[asset.erc20] == asset
76:    // effects: assets' = assets.set(asset.erc20(), asset)
77:    // returns: (asset.erc20 not in keys(assets))	// @audit-issue: There should be single blank line between function declarations.
78:    function register(IAsset asset) external governance returns (bool) {

80:    }
81:
82:    /// Register `asset` if and only if its erc20 address is already registered.
83:    /// If the erc20 address was not registered, revert.
84:    /// @return swapped If the asset was swapped for a previously-registered asset
85:    /// @custom:governance
86:    // contract
87:    // checks: asset.erc20() in assets
88:    // effects: assets' = assets + {asset.erc20(): asset}
89:    // actions: if asset.erc20() is in basketHandler's basket then basketHandler.disableBasket()	// @audit-issue: There should be single blank line between function declarations.
90:    function swapRegistered(IAsset asset) external governance returns (bool swapped) {

100:    }
101:
102:    /// Unregister an asset, requiring that it is already registered
103:    /// Rewards are NOT claimed by default when unregistering due to security concerns.
104:    /// If the collateral is secure, governance should claim rewards before unregistering.
105:    /// @custom:governance
106:    // checks: assets[asset.erc20()] == asset
107:    // effects: assets' = assets - {asset.erc20():_} + {asset.erc20(), asset}	// @audit-issue: There should be single blank line between function declarations.
108:    function unregister(IAsset asset) external governance {

121:    }
122:
123:    /// Return the Asset registered for erc20; revert if erc20 is not registered.
124:    // checks: erc20 in assets
125:    // returns: assets[erc20]	// @audit-issue: There should be single blank line between function declarations.
126:    function toAsset(IERC20 erc20) external view returns (IAsset) {

129:    }
130:
131:    /// Return the Collateral registered for erc20; revert if erc20 is not registered as Collateral
132:    // checks: erc20 in assets, assets[erc20].isCollateral()
133:    // returns: assets[erc20]	// @audit-issue: There should be single blank line between function declarations.
134:    function toColl(IERC20 erc20) external view returns (ICollateral) {

138:    }
139:
140:    /// Returns true if erc20 is registered.
141:    // returns: (erc20 in assets)	// @audit-issue: There should be single blank line between function declarations.
142:    function isRegistered(IERC20 erc20) external view returns (bool) {

144:    }
145:
146:    /// Returns keys(assets) as a (duplicate-free) list.
147:    // returns: [keys(assets)] without duplicates.	// @audit-issue: There should be single blank line between function declarations.
148:    function erc20s() external view returns (IERC20[] memory erc20s_) {

154:    }
155:
156:    /// Returns keys(assets), values(assets) as (duplicate-free) lists.
157:    // returns: [keys(assets)], [values(assets)] without duplicates.
158:    /// @return reg The list of registered ERC20s and Assets, in the same order	// @audit-issue: There should be single blank line between function declarations.
159:    function getRegistry() public view returns (Registry memory reg) {

167:    }
168:
169:    /// @inheritdoc IAssetRegistry	// @audit-issue: There should be single blank line between function declarations.
170:    function validateCurrentAssets() external view {

188:    }
189:
190:    /// @return The number of registered ERC20s	// @audit-issue: There should be single blank line between function declarations.
191:    function size() external view returns (uint256) {

193:    }
194:
195:    /// Register an asset
196:    /// Forbids registering a different asset for an ERC20 that is already registered
197:    /// @return registered If the asset was moved from unregistered to registered
198:    // checks: (asset.erc20() not in assets) or (assets[asset.erc20()] == asset)
199:    // effects: assets' = assets.set(asset.erc20(), asset)
200:    // returns: assets.erc20() not in assets	// @audit-issue: There should be single blank line between function declarations.
201:    function _register(IAsset asset) internal returns (bool registered) {

208:    }
209:
210:    /// Register an asset, unregistering any previous asset with the same ERC20.
211:    // effects: assets' = assets.set(asset.erc20(), asset)
212:    // returns: assets[asset.erc20()] != asset	// @audit-issue: There should be single blank line between function declarations.
213:    function _registerIgnoringCollisions(IAsset asset) private returns (bool swapped) {
```
[41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L30-L42), [57](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L51-L58), [77](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L69-L78), [89](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L80-L90), [107](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L100-L108), [125](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L121-L126), [133](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L129-L134), [141](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L138-L142), [147](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L144-L148), [158](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L154-L159), [169](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L167-L170), [190](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L188-L191), [200](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L193-L201), [212](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L208-L213), 


```solidity
Path: ./contracts/p1/BackingManager.sol

44:    mapping(IERC20 => uint192) private tokensOut; // {tok} token balances out in ITrades
45:
46:    // ==== Invariants ====
47:    // tradingDelay <= MAX_TRADING_DELAY and backingBuffer <= MAX_BACKING_BUFFER
48:	// @audit-issue: There should be single blank line between function declarations.
49:    function init(

62:    }
63:
64:    /// Give RToken max allowance over the registered token `erc20`
65:    /// @custom:interaction CEI
66:    // checks: erc20 in assetRegistry
67:    // action: set allowance on erc20 for rToken to UINT_MAX
68:    // Using two safeApprove calls instead of safeIncreaseAllowance to support USDT	// @audit-issue: There should be single blank line between function declarations.
69:    function grantRTokenAllowance(IERC20 erc20) external notFrozen {

74:    }
75:
76:    /// Settle a single trade. If the caller is the trade, try chaining into rebalance()
77:    /// While this function is not nonReentrant, its two subsets each individually are
78:    /// If the caller is a trade contract, initiate the next trade.
79:    /// This is done in order to better align incentives,
80:    /// and have the last bidder be the one to start the next auction.
81:    /// This behaviour currently only happens for Dutch Trade.
82:    /// @param sell The sell token in the trade
83:    /// @return trade The ITrade contract settled
84:    /// @custom:interaction	// @audit-issue: There should be single blank line between function declarations.
85:    function settleTrade(IERC20 sell) public override(ITrading, TradingP1) returns (ITrade trade) {

100:    }
101:
102:    /// Apply the overall backing policy using the specified TradeKind, taking a haircut if unable
103:    /// @param kind TradeKind.DUTCH_AUCTION or TradeKind.BATCH_AUCTION
104:    /// @custom:interaction not RCEI; nonReentrant
105:    // untested:
106:    //      OZ nonReentrant line is assumed to be working. cost/benefit of direct testing is high	// @audit-issue: There should be single blank line between function declarations.
107:    function rebalance(TradeKind kind) external nonReentrant {

172:    }
173:
174:    /// Forward revenue to RevenueTraders; reverts if not fully collateralized
175:    /// @param erc20s The tokens to forward
176:    /// @custom:interaction not RCEI; nonReentrant
177:    // untested:
178:    //      OZ nonReentrant line is assumed to be working. cost/benefit of direct testing is high	// @audit-issue: There should be single blank line between function declarations.
179:    function forwardRevenue(IERC20[] calldata erc20s) external nonReentrant {

265:    }
266:
267:    // === View ===
268:
269:    /// Structs for trading
270:    /// @param basketsHeld The number of baskets held by the BackingManager
271:    /// @return ctx The TradingContext
272:    /// @return reg Contents of AssetRegistry.getRegistry()	// @audit-issue: There should be single blank line between function declarations.
273:    function tradingContext(BasketRange memory basketsHeld)

300:    }
301:
302:    // === Private ===
303:
304:    /// Compromise on how many baskets are needed in order to recollateralize-by-accounting
305:    /// @param basketsHeldBottom {BU} The number of full basket units held by the BackingManager	// @audit-issue: There should be single blank line between function declarations.
306:    function compromiseBasketsNeeded(uint192 basketsHeldBottom) private {

310:    }
311:
312:    // === Governance Setters ===
313:
314:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
315:    function setTradingDelay(uint48 val) public governance {

319:    }
320:
321:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
322:    function setBackingBuffer(uint192 val) public governance {

326:    }
327:
328:    /// Call after upgrade to >= 3.0.0	// @audit-issue: There should be single blank line between function declarations.
329:    function cacheComponents() public {
```
[48](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L44-L49), [68](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L62-L69), [84](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L74-L85), [106](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L100-L107), [178](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L172-L179), [272](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L265-L273), [305](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L300-L306), [314](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L310-L315), [321](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L319-L322), [328](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L326-L329), 


```solidity
Path: ./contracts/p1/StRSR.sol

165:    uint192 private constant MIN_SAFE_STAKE_RATE = uint192(1e12); // 1e-6  D18{qStRSR/qRSR}
166:
167:    // ======================
168:
169:    // init() can only be called once (initializer)
170:    // ==== Financial State:
171:    // effects:
172:    //   draft' = {}, bal' = {}, all totals zero, all rates FIX_ONE.
173:    //   payoutLastPaid' = now
174:    //   rsrRewardsAtLastPayout' = current RSR balance ( == rsrRewards() given the above )	// @audit-issue: There should be single blank line between function declarations.
175:    function init(

203:    }
204:
205:    /// Assign reward payouts to the staker pool
206:    /// @custom:refresher	// @audit-issue: There should be single blank line between function declarations.
207:    function payoutRewards() external {

209:    }
210:
211:    /// Stakes an RSR `amount` on the corresponding RToken to earn yield and over-collateralize
212:    /// the system
213:    /// @param rsrAmount {qRSR}
214:    /// @dev Staking continues while paused/frozen, with reward handouts
215:    /// @custom:interaction CEI
216:    // checks:
217:    //   0 < rsrAmount
218:    //
219:    // effects:
220:    //   stakeRSR' = stakeRSR + rsrAmount
221:    //   totalStakes' = stakeRSR' * stakeRate / 1e18   (as required by invariant)
222:    //   bal'[caller] = bal[caller] + (totalStakes' - totalStakes)
223:    //   stakeRate' = stakeRate     (this could go without saying, but it's important!)
224:    //
225:    // actions:
226:    //   rsr.transferFrom(account, this, rsrAmount)	// @audit-issue: There should be single blank line between function declarations.
227:    function stake(uint256 rsrAmount) public {

238:    }
239:
240:    /// Begins a delayed unstaking for `amount` StRSR
241:    /// @param stakeAmount {qStRSR}
242:    /// @custom:interaction
243:    // checks:
244:    //   not paused (trading) or frozen
245:    //   0 < stakeAmount <= bal[caller]
246:    //
247:    // effects:
248:    //   totalStakes' = totalStakes - stakeAmount
249:    //   bal'[caller] = bal[caller] - stakeAmount
250:    //   stakeRSR' = ceil(totalStakes' * 1e18 / stakeRate)
251:    //   stakeRate' = stakeRate (no change)
252:    //
253:    //   draftRSR' + stakeRSR' = draftRSR + stakeRSR
254:    //   draftRate' = draftRate (no change)
255:    //   totalDrafts' = floor(draftRSR' + draftRate' / 1e18)
256:    //
257:    //   A draft for (totalDrafts' - totalDrafts) drafts
258:    //   is freshly appended to the caller's draft record.	// @audit-issue: There should be single blank line between function declarations.
259:    function unstake(uint256 stakeAmount) external {

281:    }
282:
283:    /// Complete an account's unstaking; callable by anyone
284:    /// @custom:interaction CEIC - Warning: violates CEI; has checks at the end
285:    // Let:
286:    //   r = draft[account]
287:    //   draftAmount = r.queue[endId - 1].drafts - r.queue[r.left-1].drafts
288:    //
289:    // checks:
290:    //   RToken is fully collateralized and the basket is sound.
291:    //   The system is not paused (trading) or frozen.
292:    //   endId <= r.right
293:    //   r.queue[endId - 1].availableAt <= now
294:    //
295:    // effects:
296:    //   r'.left = max(endId, r.left)
297:    //   draftSum'(account) = draftSum(account) + draftAmount)
298:    //   r'.right = r.right
299:    //   totalDrafts' = totalDrafts - draftAmount
300:    //   draftRSR' = ceil(totalDrafts' * 1e18 / draftRate)
301:    //
302:    // actions:
303:    //   rsr.transfer(account, rsrOut)	// @audit-issue: There should be single blank line between function declarations.
304:    function withdraw(address account, uint256 endId) external {

342:    }
343:
344:    /// Cancel an ongoing unstaking; resume staking
345:    /// @custom:interaction CEI	// @audit-issue: There should be single blank line between function declarations.
346:    function cancelUnstake(uint256 endId) external {

388:    }
389:
390:    /// @param rsrAmount {qRSR}
391:    /// Must seize at least `rsrAmount`, or revert
392:    /// @custom:protected
393:    // let:
394:    //   keepRatio = 1 - (rsrAmount / rsr.balanceOf(this))
395:    //
396:    // checks:
397:    //   0 < rsrAmount <= rsr.balanceOf(this)
398:    //   not paused (trading) or frozen
399:    //   caller is backingManager
400:    //
401:    // effects, in two phases. Phase 1: (from x to x')
402:    //   stakeRSR' = floor(stakeRSR * keepRatio)
403:    //   totalStakes' = totalStakes
404:    //   stakeRate' = ceil(totalStakes' * 1e18 / stakeRSR')
405:    //
406:    //   draftRSR' = floor(draftRSR * keepRatio)
407:    //   totalDrafts' = totalDrafts
408:    //   draftRate' = ceil(totalDrafts' * 1e18 / draftRSR')
409:    //
410:    //   let fromRewards = floor(rsrRewards() * (1 - keepRatio))
411:    //
412:    // effects phase 2: (from x' to x'')
413:    //   draftRSR'' = (draftRSR' <= MAX_DRAFT_RATE) ? draftRSR' : 0
414:    //   if draftRSR'' = 0, then totalDrafts'' = 0 and draftRate'' = FIX_ONE
415:    //   stakeRSR'' = (stakeRSR' <= MAX_STAKE_RATE) ? stakeRSR' : 0
416:    //   if stakeRSR'' = 0, then totalStakes'' = 0 and stakeRate'' = FIX_ONE
417:    //
418:    // actions:
419:    //   as (this), rsr.transfer(backingManager, seized)
420:    //   where seized = draftRSR - draftRSR'' + stakeRSR - stakeRSR'' + fromRewards
421:    //
422:    // other properties:
423:    //   seized >= rsrAmount, which should be a logical consequence of the above effects	// @audit-issue: There should be single blank line between function declarations.
424:    function seizeRSR(uint256 rsrAmount) external {

478:    }
479:
480:    /// @custom:governance
481:    /// Reset all stakes and advance era
482:    /// @notice This function is only callable when the stake rate is unsafe.
483:    ///     The stake rate is unsafe when it is either too high or too low.
484:    ///     There is the possibility of the rate reaching the borderline of being unsafe,
485:    ///     where users won't stake in fear that a reset might be executed.
486:    ///     A user may also grief this situation by staking enough RSR to vote against any reset.
487:    ///     This standoff will continue until enough RSR is staked and a reset is executed.
488:    ///     There is currently no good and easy way to mitigate the possibility of this situation,
489:    ///     and the risk of it occurring is low enough that it is not worth the effort to mitigate.	// @audit-issue: There should be single blank line between function declarations.
490:    function resetStakes() external {

499:    }
500:
501:    /// @return D18{qRSR/qStRSR} The exchange rate between RSR and StRSR	// @audit-issue: There should be single blank line between function declarations.
502:    function exchangeRate() public view returns (uint192) {

505:    }
506:
507:    /// Return the maximum value of endId such that withdraw(endId) can immediately work
508:    // let r = draft[account]
509:    // returns:
510:    //   if r.left == r.right: r.right (i.e, withdraw 0 drafts)
511:    //   else: the least id such that r.left <= id <= r.right and r.queue[id].availableAt > now	// @audit-issue: There should be single blank line between function declarations.
512:    function endIdForWithdraw(address account) external view returns (uint256) {

535:    }
536:
537:    /// Used by FacadeP1
538:    /// @return The length of the draft queue for an account in an era	// @audit-issue: There should be single blank line between function declarations.
539:    function draftQueueLen(uint256 era_, address account) external view returns (uint256) {

541:    }
542:
543:    /// @return {qDrafts} The amount of RSR currently being withdrawn	// @audit-issue: There should be single blank line between function declarations.
544:    function getDraftRSR() external view returns (uint256) {

546:    }
547:
548:    /// @return {qRSR} The amount of RSR currently being staked and earning rewards	// @audit-issue: There should be single blank line between function declarations.
549:    function getStakeRSR() external view returns (uint256) {

551:    }
552:
553:    /// @return {qDrafts} The amount of StRSR currently being withdrawn	// @audit-issue: There should be single blank line between function declarations.
554:    function getTotalDrafts() external view returns (uint256) {

556:    }
557:
558:    /// @return {draftEra} The current era for drafts (withdrawals)	// @audit-issue: There should be single blank line between function declarations.
559:    function getDraftEra() external view returns (uint256) {

561:    }
562:
563:    // ==== Internal Functions ====
564:
565:    /// Assign reward payouts to the staker pool
566:    /// @dev do this by effecting stakeRSR and payoutLastPaid as appropriate, given the current
567:    /// value of rsrRewards()
568:    /// @dev perhaps astonishingly, this _isn't_ a refresher
569:
570:    // let
571:    //   N = numPeriods; the number of whole rewardPeriods since the last payout
572:    //   payout = rsrRewards() * (1 - (1 - rewardRatio)^N)  (see [strsr-payout-formula])
573:    //
574:    // effects:
575:    //   stakeRSR' = stakeRSR + payout
576:    //   rsrRewards'() = rsrRewards() - payout   (implicit in the code, but true)
577:    //   stakeRate' = ceil(totalStakes' * 1e18 / stakeRSR')  (because [stake-rate])
578:    //     unless totalStakes == 0 or stakeRSR == 0, in which case stakeRate' = FIX_ONE
579:    //   totalStakes' = totalStakes
580:    //
581:    // [strsr-payout-formula]:
582:    //   The process we're modelling is:
583:    //     N = number of whole rewardPeriods since last _payoutRewards() call
584:    //     rewards_0 = rsrRewards()
585:    //     payout_{i+1} = rewards_i * payoutRatio
586:    //     rewards_{i+1} = rewards_i - payout_{i+1}
587:    //     payout = sum{payout_i for i in [1...N]}
588:    //   thus:
589:    //     rewards_N = rewards_0 - payout
590:    //     rewards_{i+1} = rewards_i - rewards_i * payoutRatio = rewards_i * (1-payoutRatio)
591:    //     rewards_N = rewards_0 * (1-payoutRatio) ^ N
592:    //     payout = rewards_N - rewards_0 = rewards_0 * (1 - (1-payoutRatio)^N)	// @audit-issue: There should be single blank line between function declarations.
593:    function _payoutRewards() internal {

627:    }
628:
629:    /// @param rsrAmount {qRSR}
630:    /// @return index The index of the draft
631:    /// @return availableAt {s} The timestamp the cumulative draft vests
632:    // effects:
633:    //   draftRSR' = draftRSR + rsrAmount
634:    //   draftRate' = draftRate    (ie, unchanged)
635:    //   totalDrafts' = floor(draftRSR' * draftRate' / 1e18)
636:    //   r'.left = r.left
637:    //   r'.right = r.right + 1
638:    //   r'.queue is r.queue with a new entry appeneded for (totalDrafts' - totalDraft) drafts
639:    //   where r = draft[account] and r' = draft'[account]	// @audit-issue: There should be single blank line between function declarations.
640:    function pushDraft(address account, uint256 rsrAmount)

664:    }
665:
666:    /// Zero all stakes and withdrawals
667:    /// Overriden in StRSRVotes to handle rebases
668:    // effects:
669:    //   stakeRSR' = totalStakes' = 0
670:    //   stakeRate' = FIX_ONE	// @audit-issue: There should be single blank line between function declarations.
671:    function beginEra() internal virtual {

678:    }
679:
680:    // effects:
681:    //  draftRSR' = totalDrafts' = 0
682:    //  draftRate' = FIX_ONE	// @audit-issue: There should be single blank line between function declarations.
683:    function beginDraftEra() internal virtual {

690:    }
691:
692:    /// @return {qRSR} The balance of RSR that this contract owns dedicated to future RSR rewards.	// @audit-issue: There should be single blank line between function declarations.
693:    function rsrRewards() internal view returns (uint256) {

695:    }
696:
697:    /// Refresh if too much RSR has exited since the last refresh occurred
698:    /// @param rsrWithdrawal {qRSR} How much RSR is being withdrawn
699:    /// Effects-Refresh	// @audit-issue: There should be single blank line between function declarations.
700:    function leakyRefresh(uint256 rsrWithdrawal) private {

718:    }
719:
720:    /// Mint stakes corresponding to rsrAmount to an account
721:    /// @param rsrAmount {qRSR} The RSR amount being staked	// @audit-issue: There should be single blank line between function declarations.
722:    function mintStakes(address account, uint256 rsrAmount) private {

736:    }
737:
738:    // contract-size-saver
739:    // solhint-disable-next-line no-empty-blocks	// @audit-issue: There should be single blank line between function declarations.
740:    function _requireNotTradingPausedOrFrozen() private notTradingPausedOrFrozen {}

740:    function _requireNotTradingPausedOrFrozen() private notTradingPausedOrFrozen {}
741:
742:    // contract-size-saver
743:    // solhint-disable-next-line no-empty-blocks	// @audit-issue: There should be single blank line between function declarations.
744:    function _requireNotFrozen() private notFrozen {}

744:    function _requireNotFrozen() private notFrozen {}
745:
746:    // contract-size-saver
747:    // solhint-disable-next-line no-empty-blocks	// @audit-issue: There should be single blank line between function declarations.
748:    function _requireGovernanceOnly() private governance {}

748:    function _requireGovernanceOnly() private governance {}
749:
750:    // ==== ERC20 ====
751:    // This section extracted from ERC20; adjusted to work with stakes/eras
752:    // name(), symbol(), and decimals() are all auto-generated
753:	// @audit-issue: There should be single blank line between function declarations.
754:    function totalSupply() public view returns (uint256) {

775:    }
776:
777:    /**
778:     * NOTE: If `amount` is the maximum `uint256`, the allowance is not updated on
779:     * `transferFrom`. This is semantically equivalent to an infinite approval.
780:     */	// @audit-issue: There should be single blank line between function declarations.
781:    function approve(address spender, uint256 amount) public returns (bool) {

784:    }
785:
786:    /**
787:     * NOTE: Does not update the allowance if the current allowance
788:     * is the maximum `uint256`.
789:     */	// @audit-issue: There should be single blank line between function declarations.
790:    function transferFrom(

815:    }
816:
817:    // checks: from != 0, to != 0,
818:    // effects: bal[from] -= amount; bal[to] += amount;	// @audit-issue: There should be single blank line between function declarations.
819:    function _transfer(

837:    }
838:
839:    // checks: account != 0; totalStakes' < 2^224 - 1  (for StRSRVotes)
840:    // effects: bal[account] += amount; totalStakes += amount
841:    // this must only be called from a function that will fixup stakeRSR/Rate	// @audit-issue: There should be single blank line between function declarations.
842:    function _mint(address account, uint256 amount) internal virtual {

851:    }
852:
853:    // checks: account != 0; bal[account] >= amount
854:    // effects: bal[account] -= amount; totalStakes -= amount;
855:    // this must only be called from a function that will fixup stakeRSR/Rate	// @audit-issue: There should be single blank line between function declarations.
856:    function _burn(address account, uint256 amount) internal virtual {

899:    }
900:
901:    /// Used by StRSRVotes to track voting
902:    // solhint-disable no-empty-blocks	// @audit-issue: There should be single blank line between function declarations.
903:    function _afterTokenTransfer(

917:    }
918:
919:    // === ERC20Permit ===
920:    // This section extracted from OZ:ERC20PermitUpgradeable
921:	// @audit-issue: There should be single blank line between function declarations.
922:    function permit(

948:    }
949:
950:    // solhint-disable-next-line func-name-mixedcase	// @audit-issue: There should be single blank line between function declarations.
951:    function DOMAIN_SEPARATOR() external view returns (bytes32) {

965:    }
966:
967:    // ==== Gov Param Setters ====
968:
969:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
970:    function setUnstakingDelay(uint48 val) public {

975:    }
976:
977:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
978:    function setRewardRatio(uint192 val) public {

984:    }
985:
986:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
987:    function setWithdrawalLeak(uint192 val) public {
```
[174](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L165-L175), [206](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L203-L207), [226](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L209-L227), [258](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L238-L259), [303](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L281-L304), [345](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L342-L346), [423](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L388-L424), [489](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L478-L490), [501](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L499-L502), [511](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L505-L512), [538](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L535-L539), [543](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L541-L544), [548](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L546-L549), [553](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L551-L554), [558](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L556-L559), [592](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L561-L593), [639](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L627-L640), [670](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L664-L671), [682](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L678-L683), [692](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L690-L693), [699](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L695-L700), [721](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L718-L722), [739](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L736-L740), [743](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L740-L744), [747](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L744-L748), [753](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L748-L754), [780](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L775-L781), [789](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L784-L790), [818](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L815-L819), [841](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L837-L842), [855](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L851-L856), [902](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L899-L903), [921](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L917-L922), [950](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L948-L951), [969](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L965-L970), [977](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L975-L978), [986](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L984-L987), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

96:    bool public enableIssuancePremium;
97:
98:    // ==== Invariants ====
99:    // basket is a valid Basket:
100:    //   basket.erc20s is a valid collateral array and basket.erc20s == keys(basket.refAmts)
101:    // config is a valid BasketConfig:
102:    //   erc20s == keys(targetAmts) == keys(targetNames)
103:    //   erc20s is a valid collateral array
104:    //   for b in vals(backups), b.erc20s is a valid collateral array.
105:    // if basket.erc20s is empty then disabled == true
106:
107:    // BasketHandler.init() just leaves the BasketHandler state zeroed	// @audit-issue: There should be single blank line between function declarations.
108:    function init(

131:    }
132:
133:    /// Disable the basket in order to schedule a basket refresh
134:    /// @custom:protected
135:    // checks: caller is assetRegistry
136:    // effects: disabled' = true	// @audit-issue: There should be single blank line between function declarations.
137:    function disableBasket() external {

147:    }
148:
149:    /// Switch the basket, only callable directly by governance or after a default
150:    /// @custom:interaction OR @custom:governance
151:    // checks: either caller has OWNER,
152:    //         or (basket is disabled after refresh and we're unpaused and unfrozen)
153:    // actions: calls assetRegistry.refresh(), then _switchBasket()
154:    // effects:
155:    //   Either: (basket' is a valid nonempty basket, without DISABLED collateral,
156:    //            that satisfies basketConfig) and disabled' = false
157:    //   Or no such basket exists and disabled' = true	// @audit-issue: There should be single blank line between function declarations.
158:    function refreshBasket() external {

169:    }
170:
171:    /// Track basket status and collateralization changes
172:    // effects: lastStatus' = status(), and lastStatusTimestamp' = current timestamp
173:    /// @dev Does NOT interact with collateral plugins or tokens when basket is disabled
174:    /// @custom:refresher	// @audit-issue: There should be single blank line between function declarations.
175:    function trackStatus() public {

191:    }
192:
193:    /// Set the prime basket, checking target amounts are constant
194:    /// @param erc20s The collateral for the new prime basket
195:    /// @param targetAmts The target amounts (in) {target/BU} for the new prime basket
196:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
197:    function setPrimeBasket(IERC20[] calldata erc20s, uint192[] calldata targetAmts) external {

199:    }
200:
201:    /// Set the prime basket, skipping any constant target amount checks if RToken is reweightable
202:    /// Warning: Reweightable RTokens SHOULD use a spell to execute this function to avoid
203:    ///          accidentally changing the UoA value of the RToken.
204:    /// @param erc20s The collateral for the new prime basket
205:    /// @param targetAmts The target amounts (in) {target/BU} for the new prime basket
206:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
207:    function forceSetPrimeBasket(IERC20[] calldata erc20s, uint192[] calldata targetAmts) external {

209:    }
210:
211:    /// Set the prime basket in the basket configuration, in terms of erc20s and target amounts
212:    /// @param erc20s The collateral for the new prime basket
213:    /// @param targetAmts The target amounts (in) {target/BU} for the new prime basket
214:    /// @param disableTargetAmountCheck If true, skips the `requireConstantConfigTargets()` check
215:    /// @custom:governance
216:    // checks:
217:    //   caller is OWNER
218:    //   len(erc20s) == len(targetAmts)
219:    //   erc20s is a valid collateral array
220:    //   for all i, erc20[i] is in AssetRegistry as collateral
221:    //   for all i, 0 < targetAmts[i] <= MAX_TARGET_AMT == 1000
222:    //
223:    // effects:
224:    //   config'.erc20s = erc20s
225:    //   config'.targetAmts[erc20s[i]] = targetAmts[i], for i from 0 to erc20s.length-1
226:    //   config'.targetNames[e] = assetRegistry.toColl(e).targetName, for e in erc20s	// @audit-issue: There should be single blank line between function declarations.
227:    function _setPrimeBasket(

273:    }
274:
275:    /// Set the backup configuration for some target name
276:    /// @custom:governance
277:    // checks:
278:    //   caller is OWNER
279:    //   erc20s is a valid collateral array
280:    //   for all i, erc20[i] is in AssetRegistry as collateral
281:    //
282:    // effects:
283:    //   config'.backups[targetName] = {max: max, erc20s: erc20s}	// @audit-issue: There should be single blank line between function declarations.
284:    function setBackupConfig(

303:    }
304:
305:    /// @return Whether this contract owns enough collateral to cover rToken.basketsNeeded() BUs
306:    /// ie, whether the protocol is currently fully collateralized	// @audit-issue: There should be single blank line between function declarations.
307:    function fullyCollateralized() public view returns (bool) {

310:    }
311:
312:    /// @return status_ The status of the basket
313:    // returns DISABLED if disabled == true, and worst(status(coll)) otherwise	// @audit-issue: There should be single blank line between function declarations.
314:    function status() public view returns (CollateralStatus status_) {

328:    }
329:
330:    /// @return Whether the basket is ready to issue and trade	// @audit-issue: There should be single blank line between function declarations.
331:    function isReady() external view returns (bool) {

335:    }
336:
337:    /// Basket quantity rounded up, without any issuance premium
338:    /// @param erc20 The token contract to check for quantity for
339:    /// @return {tok/BU} The token-quantity of an ERC20 token in the basket.
340:    // Returns 0 if erc20 is not registered or not in the basket
341:    // Returns FIX_MAX (in lieu of +infinity) if Collateral.refPerTok() is 0.
342:    // Otherwise returns (token's basket.refAmts / token's Collateral.refPerTok())	// @audit-issue: There should be single blank line between function declarations.
343:    function quantity(IERC20 erc20) public view returns (uint192) {

349:    }
350:
351:    /// Basket quantity rounded up, without any issuance premium
352:    /// Like quantity(), but unsafe because it DOES NOT CONFIRM THAT THE ASSET IS CORRECT
353:    /// @param erc20 The ERC20 token contract for the asset
354:    /// @param asset The registered asset plugin contract for the erc20
355:    /// @return {tok/BU} The token-quantity of an ERC20 token in the basket.
356:    // Returns 0 if erc20 is not registered or not in the basket
357:    // Returns FIX_MAX (in lieu of +infinity) if Collateral.refPerTok() is 0.
358:    // Otherwise returns (token's basket.refAmts / token's Collateral.refPerTok())	// @audit-issue: There should be single blank line between function declarations.
359:    function quantityUnsafe(IERC20 erc20, IAsset asset) public view returns (uint192) {

362:    }
363:
364:    /// @param coll A collateral that has had refresh() called on it this timestamp
365:    /// @return {1} The multiplier to charge on issuance quantities for a collateral	// @audit-issue: There should be single blank line between function declarations.
366:    function issuancePremium(ICollateral coll) public view returns (uint192) {

382:    }
383:
384:    /// Returns the quantity of collateral token in a BU
385:    /// @param erc20 The token contract
386:    /// @param coll The registered collateral plugin contract
387:    /// @return {tok/BU} The token-quantity of an ERC20 token in the basket
388:    // Returns 0 if coll is not in the basket
389:    // Returns FIX_MAX (in lieu of +infinity) if Collateral.refPerTok() is 0.
390:    // Otherwise returns (token's basket.refAmts / token's Collateral.refPerTok())	// @audit-issue: There should be single blank line between function declarations.
391:    function _quantity(

401:    }
402:
403:    /// Returns the price of a BU (including issuance premium)
404:    /// Included for backwards compatibility with <4.0.0
405:    /// Should not revert
406:    /// @return low {UoA/BU} The lower end of the price estimate
407:    /// @return high {UoA/BU} The upper end of the price estimate
408:    // returns sum(quantity(erc20) * price(erc20) for erc20 in basket.erc20s)	// @audit-issue: There should be single blank line between function declarations.
409:    function price() external view returns (uint192 low, uint192 high) {

411:    }
412:
413:    /// Returns the price of a BU
414:    /// Should not revert
415:    /// @param applyIssuancePremium Whether to apply the issuance premium to the high price
416:    /// @return low {UoA/BU} The lower end of the price estimate
417:    /// @return high {UoA/BU} The upper end of the price estimate
418:    // returns sum(quantity(erc20) * price(erc20) for erc20 in basket.erc20s)	// @audit-issue: There should be single blank line between function declarations.
419:    function price(bool applyIssuancePremium) public view returns (uint192 low, uint192 high) {

458:    }
459:
460:    /// Return the current issuance/redemption quantities for `amount` BUs
461:    /// Included for backwards compatibility with <4.0.0
462:    /// @param rounding If CEIL, apply issuance premium
463:    /// @param amount {BU}
464:    /// @return erc20s The backing collateral erc20s
465:    /// @return quantities {qTok} ERC20 token quantities equal to `amount` BUs
466:    // Returns (erc20s, [quantity(e) * amount {as qTok} for e in erc20s])	// @audit-issue: There should be single blank line between function declarations.
467:    function quote(uint192 amount, RoundingMode rounding)

473:    }
474:
475:    /// Return the current issuance/redemption quantities for `amount` BUs
476:    /// @dev Subset of logic of quoteCustomRedemption; more gas efficient for current nonce
477:    /// @param amount {BU}
478:    /// @param applyIssuancePremium Whether to apply the issuance premium
479:    /// @return erc20s The backing collateral erc20s
480:    /// @return quantities {qTok} ERC20 token quantities equal to `amount` BUs
481:    // Returns (erc20s, [quantity(e) * amount {as qTok} for e in erc20s])	// @audit-issue: There should be single blank line between function declarations.
482:    function quote(

512:    }
513:
514:    /// Return the redemption value of `amount` BUs for a linear combination of historical baskets
515:    /// @param basketNonces An array of basket nonces to do redemption from
516:    /// @param portions {1} An array of Fix quantities
517:    /// @param amount {BU}
518:    /// @return erc20s The backing collateral erc20s
519:    /// @return quantities {qTok} ERC20 token quantities equal to `amount` BUs
520:    // Returns (erc20s, [quantity(e) * amount {as qTok} for e in erc20s])	// @audit-issue: There should be single blank line between function declarations.
521:    function quoteCustomRedemption(

601:    }
602:
603:    /// @return baskets {BU}
604:    ///          .top The number of partial basket units: e.g max(coll.map((c) => c.balAsBUs())
605:    ///          .bottom The number of whole basket units held by the account
606:    /// @dev Returns (FIX_ZERO, FIX_MAX) for an empty or DISABLED basket
607:    // Returns:
608:    //    (0, 0), if (basket.erc20s is empty) or (disabled is true) or (status() is DISABLED)
609:    //    min(e.balanceOf(account) / quantity(e) for e in basket.erc20s if quantity(e) > 0),	// @audit-issue: There should be single blank line between function declarations.
610:    function basketsHeldBy(address account) public view returns (BasketRange memory baskets) {

628:    }
629:
630:    // === Governance Setters ===
631:
632:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
633:    function setWarmupPeriod(uint48 val) public {

638:    }
639:
640:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
641:    function setIssuancePremiumEnabled(bool val) public {

645:    }
646:
647:    // === Private ===
648:
649:    // contract-size-saver
650:    // solhint-disable-next-line no-empty-blocks	// @audit-issue: There should be single blank line between function declarations.
651:    function requireGovernanceOnly() private governance {}

651:    function requireGovernanceOnly() private governance {}
652:
653:    /// Select and save the next basket, based on the BasketConfig and Collateral statuses	// @audit-issue: There should be single blank line between function declarations.
654:    function _switchBasket() private {

680:    }
681:
682:    /// Require that erc20s is a valid collateral array	// @audit-issue: There should be single blank line between function declarations.
683:    function requireValidCollArray(IERC20[] calldata erc20s) private view {

695:    }
696:
697:    // ==== ReadFacet views ====
698:    // Not used in-protocol; helpful for reconstructing state
699:
700:    /// Get a reference basket in today's collateral tokens, by nonce
701:    /// @param basketNonce {basketNonce}
702:    /// @return erc20s The erc20s in the reference basket
703:    /// @return quantities {qTok/BU} The quantity of whole tokens per whole basket unit	// @audit-issue: There should be single blank line between function declarations.
704:    function getHistoricalBasket(uint48 basketNonce)

731:    }
732:
733:    /// Getter part1 for `config` struct variable
734:    /// @dev Indices are shared across return values
735:    /// @return erc20s The erc20s in the prime basket
736:    /// @return targetNames The bytes32 name identifier of the target unit, per ERC20
737:    /// @return targetAmts {target/BU} The amount of the target unit in the basket, per ERC20	// @audit-issue: There should be single blank line between function declarations.
738:    function getPrimeBasket()

756:    }
757:
758:    /// Getter part2 for `config` struct variable
759:    /// @param targetName The name of the target unit to lookup the backup for
760:    /// @return erc20s The backup erc20s for the target unit, in order of most to least desirable
761:    /// @return max The maximum number of tokens from the array to use at a single time	// @audit-issue: There should be single blank line between function declarations.
762:    function getBackupConfig(bytes32 targetName)
```
[107](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L96-L108), [136](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L131-L137), [157](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L147-L158), [174](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L169-L175), [196](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L191-L197), [206](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L199-L207), [226](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L209-L227), [283](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L273-L284), [306](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L303-L307), [313](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L310-L314), [330](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L328-L331), [342](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L335-L343), [358](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L349-L359), [365](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L362-L366), [390](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L382-L391), [408](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L401-L409), [418](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L411-L419), [466](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L458-L467), [481](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L473-L482), [520](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L512-L521), [609](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L601-L610), [632](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L628-L633), [640](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L638-L641), [650](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L645-L651), [653](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L651-L654), [682](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L680-L683), [703](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L695-L704), [737](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L731-L738), [761](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L756-L762), 


```solidity
Path: ./contracts/p1/Deployer.sol

38:    Implementations private _implementations;
39:
40:    // checks: every address in the input is nonzero
41:    // effects: post, all contract-state values are set	// @audit-issue: There should be single blank line between function declarations.
42:    constructor(

76:    }
77:
78:    /// Deploys an instance of the entire system, oriented around some mandate.
79:    ///
80:    /// The mandate describes what goals its governors should try to achieve. By succinctly
81:    /// explaining the RToken’s purpose and what the RToken is intended to do, it provides common
82:    /// ground for the governors to decide upon priorities and how to weigh tradeoffs.
83:    ///
84:    /// Example Mandates:
85:    ///
86:    /// - Capital preservation first. Spending power preservation second. Permissionless
87:    ///     access third.
88:    /// - Capital preservation above all else. All revenues fund the over-collateralization pool.
89:    /// - Risk-neutral pursuit of profit for token holders.
90:    ///     Maximize (gross revenue - payments for over-collateralization and governance).
91:    /// - This RToken holds only FooCoin, to provide a trade for hedging against its
92:    ///     possible collapse.
93:    ///
94:    /// The mandate may also be a URI to a longer body of text
95:    /// @param name The name of the RToken to deploy
96:    /// @param symbol The symbol of the RToken to deploy
97:    /// @param mandate An IPFS link or direct string; describes what the RToken _should be_
98:    /// @param owner The address that should own the entire system, hopefully a governance contract
99:    /// @param params Deployment params
100:    /// @return The address of the newly deployed RToken.
101:
102:    // effects:
103:    //   Deploy a proxy for Main and every component of Main
104:    //   Call init() on Main and every component of Main, using `params` for needed parameters
105:    //     While doing this, init assetRegistry with this.rsrAsset and a new rTokenAsset
106:    //   Set up Auth so that `owner` holds the OWNER role and no one else has any	// @audit-issue: There should be single blank line between function declarations.
107:    function deploy(

264:    }
265:
266:    /// Deploys a new RTokenAsset instance. Not needed during normal deployment flow
267:    /// @param maxTradeVolume {UoA} The maximum trade volume for the RTokenAsset
268:    /// @return rTokenAsset The address of the newly deployed RTokenAsset	// @audit-issue: There should be single blank line between function declarations.
269:    function deployRTokenAsset(IRToken rToken, uint192 maxTradeVolume)
```
[41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L38-L42), [106](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L76-L107), [268](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L264-L269), 


```solidity
Path: ./contracts/p1/RToken.sol

87:    }
88:
89:    /// Issue an RToken on the current basket
90:    /// Do no use inifite approvals.  Instead, use BasketHandler.quote() to determine the amount
91:    ///     of backing tokens to approve.
92:    /// @param amount {qTok} The quantity of RToken to issue
93:    /// @custom:interaction nearly CEI, but see comments around handling of refunds	// @audit-issue: There should be single blank line between function declarations.
94:    function issue(uint256 amount) public {

96:    }
97:
98:    /// Issue an RToken on the current basket, to a particular recipient
99:    /// Do no use inifite approvals.  Instead, use BasketHandler.quote() to determine the amount
100:    ///     of backing tokens to approve.
101:    /// @param recipient The address to receive the issued RTokens
102:    /// @param amount {qRTok} The quantity of RToken to issue
103:    /// @custom:interaction RCEI
104:    // BU exchange rate cannot decrease, and it can only increase when < FIX_ONE.	// @audit-issue: There should be single blank line between function declarations.
105:    function issueTo(address recipient, uint256 amount) public notIssuancePausedOrFrozen {

155:    }
156:
157:    /// Redeem RToken for basket collateral
158:    /// @param amount {qTok} The quantity {qRToken} of RToken to redeem
159:    /// @custom:interaction CEI	// @audit-issue: There should be single blank line between function declarations.
160:    function redeem(uint256 amount) external {

162:    }
163:
164:    /// Redeem RToken for basket collateral to a particular recipient
165:    // checks:
166:    //   amount > 0
167:    //   amount <= balanceOf(caller)
168:    //
169:    // effects:
170:    //   (so totalSupply -= amount and balanceOf(caller) -= amount)
171:    //   basketsNeeded' / totalSupply' >== basketsNeeded / totalSupply
172:    //   burn(caller, amount)
173:    //
174:    // actions:
175:    //   let erc20s = basketHandler.erc20s()
176:    //   for each token in erc20s:
177:    //     let tokenAmt = (amount * basketsNeeded / totalSupply) current baskets
178:    //     do token.transferFrom(backingManager, caller, tokenAmt)
179:    // BU exchange rate cannot decrease, and it can only increase when < FIX_ONE.
180:    /// @param recipient The address to receive the backing collateral tokens
181:    /// @param amount {qRTok} The quantity {qRToken} of RToken to redeem
182:    /// @custom:interaction RCEI	// @audit-issue: There should be single blank line between function declarations.
183:    function redeemTo(address recipient, uint256 amount) public notFrozen {

225:    }
226:
227:    /// Redeem RToken for a linear combination of historical baskets, to a particular recipient
228:    // checks:
229:    //   amount > 0
230:    //   amount <= balanceOf(caller)
231:    //   sum(portions) == FIX_ONE
232:    //   nonce >= basketHandler.primeNonce() for nonce in basketNonces
233:    //
234:    // effects:
235:    //   (so totalSupply -= amount and balanceOf(caller) -= amount)
236:    //   basketsNeeded' / totalSupply' >== basketsNeeded / totalSupply
237:    //   burn(caller, amount)
238:    //
239:    // actions:
240:    //   for each token in erc20s:
241:    //     let tokenAmt = (amount * basketsNeeded / totalSupply) custom baskets
242:    //     let prorataAmt = (amount / totalSupply) * token.balanceOf(backingManager)
243:    //     do token.transferFrom(backingManager, caller, min(tokenAmt, prorataAmt))
244:    // BU exchange rate cannot decrease, and it can only increase when < FIX_ONE.
245:    /// @dev Allows partial redemptions up to the minAmounts
246:    /// @param recipient The address to receive the backing collateral tokens
247:    /// @param amount {qRTok} The quantity {qRToken} of RToken to redeem
248:    /// @param basketNonces An array of basket nonces to do redemption from
249:    /// @param portions {1} An array of Fix quantities that must add up to FIX_ONE
250:    /// @param expectedERC20sOut An array of ERC20s expected out
251:    /// @param minAmounts {qTok} The minimum ERC20 quantities the caller should receive
252:    /// @custom:interaction RCEI	// @audit-issue: There should be single blank line between function declarations.
253:    function redeemCustom(

344:    }
345:
346:    /// Mint an amount of RToken equivalent to baskets BUs, scaling basketsNeeded up
347:    /// Callable only by BackingManager
348:    /// @param baskets {BU} The number of baskets to mint RToken for
349:    /// @custom:protected
350:    // checks: caller is backingManager
351:    // effects:
352:    //   bal'[recipient] = bal[recipient] + amtRToken
353:    //   totalSupply' = totalSupply + amtRToken
354:    //   basketsNeeded' = basketsNeeded + baskets
355:    // BU exchange rate cannot decrease, and it can only increase when < FIX_ONE.	// @audit-issue: There should be single blank line between function declarations.
356:    function mint(uint192 baskets) external {

359:    }
360:
361:    /// Melt a quantity of RToken from the caller's account, increasing the basket rate
362:    /// @param amtRToken {qRTok} The amtRToken to be melted
363:    /// @custom:protected
364:    // checks: caller is furnace
365:    // effects:
366:    //   bal'[caller] = bal[caller] - amtRToken
367:    //   totalSupply' = totalSupply - amtRToken
368:    // BU exchange rate cannot decrease
369:    // BU exchange rate CAN increase, but we already trust furnace to do this slowly	// @audit-issue: There should be single blank line between function declarations.
370:    function melt(uint256 amtRToken) external {

375:    }
376:
377:    /// Burn an amount of RToken from caller's account and scale basketsNeeded down
378:    /// Callable only by backingManager
379:    /// @param amount {qRTok}
380:    /// @custom:protected
381:    // checks: caller is backingManager
382:    // effects:
383:    //   bal'[recipient] = bal[recipient] - amtRToken
384:    //   totalSupply' = totalSupply - amtRToken
385:    //   basketsNeeded' = basketsNeeded - baskets
386:    // BU exchange rate cannot decrease, and it can only increase when < FIX_ONE.	// @audit-issue: There should be single blank line between function declarations.
387:    function dissolve(uint256 amount) external {

391:    }
392:
393:    /// An affordance of last resort for Main in order to ensure re-capitalization
394:    /// @custom:protected
395:    // checks: caller is backingManager
396:    // effects: basketsNeeded' = basketsNeeded_	// @audit-issue: There should be single blank line between function declarations.
397:    function setBasketsNeeded(uint192 basketsNeeded_) external notTradingPausedOrFrozen {

414:    }
415:
416:    /// Sends all token balance of erc20 (if it is registered) to the BackingManager
417:    /// @custom:interaction	// @audit-issue: There should be single blank line between function declarations.
418:    function monetizeDonations(IERC20 erc20) external notTradingPausedOrFrozen {

424:    }
425:
426:    // ==== Throttle setters/getters ====
427:
428:    /// @return {qRTok} The maximum issuance that can be performed in the current block	// @audit-issue: There should be single blank line between function declarations.
429:    function issuanceAvailable() external view returns (uint256) {

431:    }
432:
433:    /// @return available {qRTok} The maximum redemption that can be performed in the current block	// @audit-issue: There should be single blank line between function declarations.
434:    function redemptionAvailable() external view returns (uint256 available) {

438:    }
439:
440:    /// @return The issuance throttle parametrization	// @audit-issue: There should be single blank line between function declarations.
441:    function issuanceThrottleParams() external view returns (ThrottleLib.Params memory) {

443:    }
444:
445:    /// @return The redemption throttle parametrization	// @audit-issue: There should be single blank line between function declarations.
446:    function redemptionThrottleParams() external view returns (ThrottleLib.Params memory) {

448:    }
449:
450:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
451:    function setIssuanceThrottleParams(ThrottleLib.Params calldata params) public governance {

459:    }
460:
461:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
462:    function setRedemptionThrottleParams(ThrottleLib.Params calldata params) public governance {

470:    }
471:
472:    // ==== Private ====
473:
474:    /// Mint an amount of RToken equivalent to amtBaskets and scale basketsNeeded up
475:    /// @param recipient The address to receive the RTokens
476:    /// @param amtBaskets {BU} The number of amtBaskets to mint RToken for
477:    /// @param totalSupply {qRTok} The current totalSupply
478:    // effects:
479:    //   bal'[recipient] = bal[recipient] + amtRToken
480:    //   totalSupply' = totalSupply + amtRToken
481:    //   basketsNeeded' = basketsNeeded + amtBaskets
482:    // BU exchange rate cannot decrease, and it can only increase when < FIX_ONE.	// @audit-issue: There should be single blank line between function declarations.
483:    function _scaleUp(

497:    }
498:
499:    /// Burn an amount of RToken and scale basketsNeeded down
500:    /// @param account The address to dissolve RTokens from
501:    /// @param amtRToken {qRTok} The amount of RToken to be dissolved
502:    /// @return amtBaskets {BU} The equivalent number of baskets dissolved
503:    // effects:
504:    //   bal'[recipient] = bal[recipient] - amtRToken
505:    //   totalSupply' = totalSupply - amtRToken
506:    //   basketsNeeded' = basketsNeeded - amtBaskets
507:    // BU exchange rate cannot decrease, and it can only increase when < FIX_ONE.	// @audit-issue: There should be single blank line between function declarations.
508:    function _scaleDown(address account, uint256 amtRToken) private returns (uint192 amtBaskets) {

516:    }
517:
518:    /**
519:     * @dev Hook that is called before any transfer of tokens. This includes
520:     * minting and burning.
521:     *
522:     * Calling conditions:
523:     *
524:     * - when `from` and `to` are both non-zero, `amount` of ``from``'s tokens
525:     * will be transferred to `to`.
526:     * - when `from` is zero, `amount` tokens will be minted for `to`.
527:     * - when `to` is zero, `amount` of ``from``'s tokens will be burned.
528:     * - `from` and `to` are never both zero.
529:     */	// @audit-issue: There should be single blank line between function declarations.
530:    function _beforeTokenTransfer(
```
[93](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L87-L94), [104](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L96-L105), [159](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L155-L160), [182](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L162-L183), [252](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L225-L253), [355](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L344-L356), [369](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L359-L370), [386](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L375-L387), [396](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L391-L397), [417](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L414-L418), [428](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L424-L429), [433](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L431-L434), [440](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L438-L441), [445](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L443-L446), [450](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L448-L451), [461](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L459-L462), [482](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L470-L483), [507](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L497-L508), [529](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L516-L530), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

43:    mapping(uint256 => Checkpoint[]) private _totalSupplyCheckpoints; // {qStRSR}
44:
45:    // When RSR is seized, stakeholders are divested not only of their economic position,
46:    // but also of their governance position.
47:
48:    // ===
49:
50:    /// Rebase hook
51:    /// No need to override beginDraftEra: we are only concerned with raw balances (stakes)	// @audit-issue: There should be single blank line between function declarations.
52:    function beginEra() internal override {

60:    }
61:
62:    /**
63:     * @dev Description of the clock
64:     */
65:    // solhint-disable-next-line func-name-mixedcase	// @audit-issue: There should be single blank line between function declarations.
66:    function CLOCK_MODE() public pure returns (string memory) {

109:    }
110:
111:    /// Return the value from history `ckpts` that was current for timepoint `timepoint`	// @audit-issue: There should be single blank line between function declarations.
112:    function _checkpointsLookup(Checkpoint[] storage ckpts, uint256 timepoint)

183:    }
184:
185:    /// Stakes an RSR `amount` on the corresponding RToken and allows to delegate
186:    /// votes from the sender to `delegatee` or self	// @audit-issue: There should be single blank line between function declarations.
187:    function stakeAndDelegate(uint256 rsrAmount, address delegatee) external {

254:    }
255:
256:    // Set this timepoint's value in the history `ckpts`	// @audit-issue: There should be single blank line between function declarations.
257:    function _writeCheckpoint(
```
[51](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L43-L52), [65](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L60-L66), [111](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L109-L112), [186](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L183-L187), [256](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L254-L257), 


```solidity
Path: ./contracts/p1/Broker.sol

67:    IRToken private rToken;
68:
69:    // ==== Invariant ====
70:    // (trades[addr] == true) iff this contract has created an ITrade clone at addr
71:
72:    // effects: initial parameters are set	// @audit-issue: There should be single blank line between function declarations.
73:    function init(

103:    }
104:
105:    /// Call after upgrade to >= 3.1.0	// @audit-issue: There should be single blank line between function declarations.
106:    function cacheComponents() public {

111:    }
112:
113:    /// Handle a trade request by deploying a customized disposable trading contract
114:    /// @param kind TradeKind.DUTCH_AUCTION or TradeKind.BATCH_AUCTION
115:    /// @dev Requires setting an allowance in advance
116:    /// @custom:protected and @custom:interaction CEI
117:    // checks:
118:    //   caller is a system Trader
119:    // effects:
120:    //   Deploys a new trade clone, `trade`
121:    //   trades'[trade] = true
122:    // actions:
123:    //   Transfers req.sellAmount of req.sell.erc20 from caller to `trade`
124:    //   Calls trade.init() with appropriate parameters	// @audit-issue: There should be single blank line between function declarations.
125:    function openTrade(

144:    }
145:
146:    /// Disable the broker until re-enabled by governance
147:    /// @custom:protected
148:    // checks: caller is a Trade this contract cloned
149:    // effects: disabled' = true	// @audit-issue: There should be single blank line between function declarations.
150:    function reportViolation() external {

173:    }
174:
175:    // === Setters ===
176:
177:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
178:    function setGnosis(IGnosis newGnosis) public governance {

183:    }
184:
185:    /// @custom:main	// @audit-issue: There should be single blank line between function declarations.
186:    function setBatchTradeImplementation(ITrade newTradeImplementation) public onlyMain {

194:    }
195:
196:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
197:    function setBatchAuctionLength(uint48 newAuctionLength) public governance {

205:    }
206:
207:    /// @custom:main	// @audit-issue: There should be single blank line between function declarations.
208:    function setDutchTradeImplementation(ITrade newTradeImplementation) public onlyMain {

216:    }
217:
218:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
219:    function setDutchAuctionLength(uint48 newAuctionLength) public governance {

227:    }
228:
229:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
230:    function enableBatchTrade() external governance {

233:    }
234:
235:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
236:    function enableDutchTrade(IERC20Metadata erc20) external governance {

239:    }
240:
241:    // === Private ===
242:	// @audit-issue: There should be single blank line between function declarations.
243:    function newBatchAuction(TradeRequest memory req, address caller) private returns (ITrade) {

295:    }
296:
297:    /// @return true iff the asset has been priced at this timestamp, or it's the RTokenAsset	// @audit-issue: There should be single blank line between function declarations.
298:    function pricedAtTimestamp(IAsset asset) private view returns (bool) {
```
[72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L67-L73), [105](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L103-L106), [124](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L111-L125), [149](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L144-L150), [177](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L173-L178), [185](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L183-L186), [196](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L194-L197), [207](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L205-L208), [218](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L216-L219), [229](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L227-L230), [235](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L233-L236), [242](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L239-L243), [297](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L295-L298), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

37:    uint256 public tradesNonce; // to keep track of how many trades have been opened in total
38:
39:    // ==== Invariants ====
40:    // tradesOpen = len(values(trades))
41:    // trades[sell] != 0 iff trade[sell] has been opened and not yet settled
42:
43:    // untestable:
44:    //      `else` branch of `onlyInitializing` (ie. revert) is currently untestable.
45:    //      This function is only called inside other `init` functions, each of which is wrapped
46:    //      in an `initializer` modifier, which would fail first.
47:    // solhint-disable-next-line func-name-mixedcase	// @audit-issue: There should be single blank line between function declarations.
48:    function __Trading_init(

56:    }
57:
58:    /// Contract-size helper
59:    // solhint-disable-next-line no-empty-blocks	// @audit-issue: There should be single blank line between function declarations.
60:    function requireNotTradingPausedOrFrozen() internal view notTradingPausedOrFrozen {}

60:    function requireNotTradingPausedOrFrozen() internal view notTradingPausedOrFrozen {}
61:
62:    /// Claim all rewards
63:    /// Collective Action
64:    /// @custom:interaction CEI	// @audit-issue: There should be single blank line between function declarations.
65:    function claimRewards() external {

68:    }
69:
70:    /// Claim rewards for a single asset
71:    /// Collective Action
72:    /// @param erc20 The ERC20 to claimRewards on
73:    /// @custom:interaction CEI	// @audit-issue: There should be single blank line between function declarations.
74:    function claimRewardsSingle(IERC20 erc20) external {

77:    }
78:
79:    /// Settle a single trade, expected to be used with multicall for efficient mass settlement
80:    /// @param sell The sell token in the trade
81:    /// @return trade The ITrade contract settled
82:    /// @custom:interaction (only reads or writes trades, and is marked `nonReentrant`)
83:    // checks:
84:    //   !paused (trading), !frozen
85:    //   trade[sell].canSettle()
86:    //   (see override)
87:    // actions:
88:    //   trade[sell].settle()
89:    // effects:
90:    //   trades.set(sell, 0)
91:    //   tradesOpen' = tradesOpen - 1
92:    // untested:
93:    //      OZ nonReentrant line is assumed to be working. cost/benefit of direct testing is high	// @audit-issue: There should be single blank line between function declarations.
94:    function settleTrade(IERC20 sell) public virtual nonReentrant returns (ITrade trade) {

105:    }
106:
107:    /// Try to initiate a trade with a trading partner provided by the broker
108:    /// @param kind TradeKind.DUTCH_AUCTION or TradeKind.BATCH_AUCTION
109:    /// @return trade The trade contract created
110:    /// @custom:interaction Assumption: Caller is nonReentrant
111:    // checks:
112:    //   (not external, so we don't need auth or pause checks)
113:    //   trades[req.sell] == 0
114:    // actions:
115:    //   req.sell.increaseAllowance(broker, req.sellAmount) - two safeApprove calls to support USDT
116:    //   tradeID = broker.openTrade(req)
117:    // effects:
118:    //   trades' = trades.set(req.sell, tradeID)
119:    //   tradesOpen' = tradesOpen + 1	// @audit-issue: There should be single blank line between function declarations.
120:    function tryTrade(

141:    }
142:
143:    // === Setters ===
144:
145:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
146:    function setMaxTradeSlippage(uint192 val) public governance {

150:    }
151:
152:    /// @custom:governance	// @audit-issue: There should be single blank line between function declarations.
153:    function setMinTradeVolume(uint192 val) public governance {
```
[47](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L37-L48), [59](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L56-L60), [64](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L60-L65), [73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L68-L74), [93](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L77-L94), [119](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L105-L120), [145](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L141-L146), [152](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L150-L153), 


```solidity
Path: ./contracts/p1/mixins/TradeLib.sol

24:    using FixLib for uint192;
25:
26:    /// Prepare a trade to sell `trade.sellAmount` that guarantees a reasonable closing price,
27:    /// without explicitly aiming at a particular buy amount.
28:    /// @param trade:
29:    ///   sell != 0, sellAmount >= 0 {sellTok}, prices.sellLow >= 0 {UoA/sellTok}
30:    ///   buy != 0, buyAmount (unused) {buyTok}, prices.buyHigh > 0 {UoA/buyTok}
31:    /// @return notDust True when the trade is larger than the dust amount
32:    /// @return req The prepared trade request to send to the Broker
33:    //
34:    // If notDust is true, then the returned trade request satisfies:
35:    //   req.sell == trade.sell and req.buy == trade.buy,
36:    //   req.minBuyAmount * trade.prices.buyHigh ~=
37:    //        trade.sellAmount * trade.prices.sellLow * (1-maxTradeSlippage),
38:    //   req.sellAmount == min(trade.sell.maxTradeSize(), trade.sellAmount)
39:    //   1 < req.sellAmount
40:    //
41:    // If notDust is false, no trade exists that satisfies those constraints.	// @audit-issue: There should be single blank line between function declarations.
42:    function prepareTradeSell(

89:    }
90:
91:    /// Assuming we have `trade.sellAmount` sell tokens available, prepare a trade to cover as
92:    /// much of our deficit of `trade.buyAmount` buy tokens as possible, given expected trade
93:    /// slippage and maxTradeVolume().
94:    /// @param trade:
95:    ///   sell != 0
96:    ///   buy != 0
97:    ///   sellAmount (unused) {sellTok}
98:    ///   buyAmount >= 0 {buyTok}
99:    ///   prices.sellLow > 0 {UoA/sellTok}
100:    ///   prices.buyHigh > 0 {UoA/buyTok}
101:    /// @return notDust Whether the prepared trade is large enough to be worth trading
102:    /// @return req The prepared trade request to send to the Broker
103:    //
104:    // Returns prepareTradeSell(trade, rules), where
105:    //   req.sellAmount = min(trade.sellAmount,
106:    //                trade.buyAmount * (buyHigh / sellLow) / (1-maxTradeSlippage))
107:    //   i.e, the minimum of trade.sellAmount and (a sale amount that, at current prices and
108:    //   maximum slippage, will yield at least the requested trade.buyAmount)
109:    //
110:    // Which means we should get that, if notDust is true, then:
111:    //   req.sell = sell and req.buy = buy
112:    //
113:    //   1 <= req.minBuyAmount <= max(trade.buyAmount, buy.minTradeSize()))
114:    //   1 < req.sellAmount <= min(trade.sellAmount, sell.maxTradeSize())
115:    //   req.minBuyAmount ~= trade.sellAmount * sellLow / buyHigh * (1-maxTradeSlippage)
116:    //
117:    //   req.sellAmount (and req.minBuyAmount) are maximal satisfying all these conditions	// @audit-issue: There should be single blank line between function declarations.
118:    function prepareTradeToCoverDeficit(

149:    }
150:
151:    /// @param asset The asset in consideration
152:    /// @param amt {tok} The number of whole tokens we plan to sell
153:    /// @param price {UoA/tok} The price to use for sizing
154:    /// @param minTradeVolume {UoA} The min trade volume, passed in for gas optimization
155:    /// @return If amt is sufficiently large to be worth selling into our trading platforms	// @audit-issue: There should be single blank line between function declarations.
156:    function isEnoughToSell(

167:    }
168:
169:    // === Private ===
170:
171:    /// Calculates the minTradeSize for an asset based on the given minTradeVolume and price
172:    /// @param minTradeVolume {UoA} The min trade volume, passed in for gas optimization
173:    /// @return {tok} The min trade size for the asset in whole tokens	// @audit-issue: There should be single blank line between function declarations.
174:    function minTradeSize(uint192 minTradeVolume, uint192 price) private pure returns (uint192) {

178:    }
179:
180:    /// Calculates the maximum trade size for a trade pair of tokens
181:    /// @return {tok} The max trade size for the trade overall	// @audit-issue: There should be single blank line between function declarations.
182:    function maxTradeSize(
```
[41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L24-L42), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L89-L118), [155](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L149-L156), [173](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L167-L174), [181](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L178-L182), 


```solidity
Path: ./contracts/p1/mixins/RewardableLib.sol

17:    using SafeERC20 for IERC20;
18:
19:    // === Used by Traders + RToken ===
20:
21:    /// Claim all rewards
22:    // actions:
23:    //   do asset.delegatecall(abi.encodeWithSignature("claimRewards()")) for asset in assets	// @audit-issue: There should be single blank line between function declarations.
24:    function claimRewards(IAssetRegistry reg) internal {

34:    }
35:
36:    /// Claim rewards for a single ERC20
37:    // actions:
38:    //   do asset.delegatecall(abi.encodeWithSignature("claimRewards()"))	// @audit-issue: There should be single blank line between function declarations.
39:    function claimRewardsSingle(IAsset asset) internal {
```
[23](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L17-L24), [38](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L34-L39), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

21:    IMain public main;
22:
23:    /// @custom:oz-upgrades-unsafe-allow constructor
24:    // solhint-disable-next-line no-empty-blocks	// @audit-issue: There should be single blank line between function declarations.
25:    constructor() initializer {}

25:    constructor() initializer {}
26:
27:    // Sets main for the component - Can only be called during initialization
28:    // untestable:
29:    //      `else` branch of `onlyInitializing` (ie. revert) is currently untestable.
30:    //      This function is only called inside other `init` functions, each of which is wrapped
31:    //      in an `initializer` modifier, which would fail first.
32:    // solhint-disable-next-line func-name-mixedcase	// @audit-issue: There should be single blank line between function declarations.
33:    function __Component_init(IMain main_) internal onlyInitializing {

37:    }
38:
39:    // === See docs/pause-freeze-states.md ===
40:	// @audit-issue: There should be single blank line between function declarations.
41:    modifier notTradingPausedOrFrozen() {

64:    }
65:
66:    // solhint-disable-next-line no-empty-blocks	// @audit-issue: There should be single blank line between function declarations.
67:    function _authorizeUpgrade(address newImplementation) internal view override onlyMain {}
```
[24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L21-L25), [32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L25-L33), [40](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L37-L41), [66](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L64-L67), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

22:    using TradeLib for IBackingManager;
23:
24:    /// Select and prepare a trade that moves us closer to capitalization, using the
25:    /// basket range to avoid overeager/duplicate trading.
26:    /// The basket range is the full range of projected outcomes for the rebalancing process.
27:    // This is the "main loop" for recollateralization trading:
28:    // actions:
29:    //   let range = basketRange(...)
30:    //   let trade = nextTradePair(...)
31:    //   if trade.sell is not a defaulted collateral, prepareTradeToCoverDeficit(...)
32:    //   otherwise, prepareTradeSell(...) taking the minBuyAmount as the dependent variable	// @audit-issue: There should be single blank line between function declarations.
33:    function prepareRecollateralizationTrade(TradingContext memory ctx, Registry memory reg)

78:    }
79:
80:    // Compute the target basket range
81:    // Algorithm intuition: Trade conservatively. Quantify uncertainty based on the proportion of
82:    // token balances requiring trading vs not requiring trading. Seek to decrease uncertainty
83:    // the largest amount possible with each trade.
84:    //
85:    // Algorithm Invariant: every increase of basketsHeld.bottom causes basketsRange().low to
86:    //  reach a new maximum. Note that basketRange().low may decrease slightly along the way.
87:    // Assumptions: constant oracle prices; monotonically increasing refPerTok; no supply changes
88:    //
89:    // Preconditions:
90:    // - ctx is correctly populated, with current basketsHeld.bottom + basketsHeld.top
91:    // - reg contains erc20 + asset + quantities arrays in same order and without duplicates
92:    // Trading Strategy:
93:    // - We will not aim to hold more than rToken.basketsNeeded() BUs
94:    // - No double trades: capital converted from token A to token B should not go to token C
95:    //       unless the clearing price was outside the expected price range
96:    // - The best price we might get for a trade is at the high sell price and low buy price
97:    // - The worst price we might get for a trade is at the low sell price and
98:    //     the high buy price, multiplied by ( 1 - maxTradeSlippage )
99:    // - In the worst-case an additional dust balance can be lost, up to minTradeVolume
100:    // - Given all that, we're aiming to hold as many BUs as possible using the assets we own.
101:    //
102:    // More concretely:
103:    // - range.top = min(rToken.basketsNeeded, basketsHeld.top - least baskets missing
104:    //                                                                   + most baskets surplus)
105:    // - range.bottom = min(rToken.basketsNeeded, basketsHeld.bottom + least baskets purchaseable)
106:    //   where "least baskets purchaseable" involves trading at the worst price,
107:    //   incurring the full maxTradeSlippage, and taking up to a minTradeVolume loss due to dust.	// @audit-issue: There should be single blank line between function declarations.
108:    function basketRange(TradingContext memory ctx, Registry memory reg)

238:    }
239:
240:    // Choose next sell/buy pair to trade, with reference to the basket range
241:    // Skip over trading surplus dust amounts
242:    /// @return trade
243:    ///   sell: Surplus collateral OR address(0)
244:    ///   deficit Deficit collateral OR address(0)
245:    ///   sellAmount {sellTok} Surplus amount (whole tokens)
246:    ///   buyAmount {buyTok} Deficit amount (whole tokens)
247:    ///   prices.sellLow {UoA/sellTok} The worst-case price of the sell token on secondary markets
248:    ///   prices.sellHigh {UoA/sellTok} The best-case price of the sell token on secondary markets
249:    ///   prices.buyLow {UoA/buyTok} The best-case price of the buy token on secondary markets
250:    ///   prices.buyHigh {UoA/buyTok} The worst-case price of the buy token on secondary markets
251:    ///
252:    // For each asset e:
253:    //   If bal(e) > (quantity(e) * range.top), then e is in surplus by the difference
254:    //   If bal(e) < (quantity(e) * range.bottom), then e is in deficit by the difference
255:    //
256:    // First, ignoring RSR:
257:    //   `trade.sell` is the token from erc20s with the greatest surplus value (in UoA),
258:    //   and sellAmount is the quantity of that token that it's in surplus (in qTok).
259:    //   if `trade.sell` == 0, then no token is in surplus by at least minTradeSize,
260:    //        and `trade.sellAmount` and `trade.sellLow` / `trade.sellHigh are unset.
261:    //
262:    //   `trade.buy` is the token from erc20s with the greatest deficit value (in UoA),
263:    //   and buyAmount is the quantity of that token that it's in deficit (in qTok).
264:    //   if `trade.buy` == 0, then no token is in deficit at all,
265:    //        and `trade.buyAmount` and `trade.buyLow` / `trade.buyHigh` are unset.
266:    //
267:    // Then, just if we have a buy asset and no sell asset, consider selling available RSR.
268:    //
269:    // Prefer selling assets in this order: DISABLED -> SOUND -> IFFY.
270:    // Sell IFFY last because it may recover value in the future.
271:    // All collateral in the basket have already been guaranteed to be SOUND by upstream checks.
272:    // Warning: If the trading algorithm is changed to trade unpriced (0, FIX_MAX) assets it can
273:    //          result in losses in GnosisTrade. Unpriced assets should not be sold in rebalancing.	// @audit-issue: There should be single blank line between function declarations.
274:    function nextTradePair(

376:    }
377:
378:    /// @param curr The current MaxSurplusDeficit containing the best surplus so far
379:    /// @param other The collateral status of the asset in consideration
380:    /// @param surplusAmt {UoA} The amount by which the asset in consideration is in surplus	// @audit-issue: There should be single blank line between function declarations.
381:    function isBetterSurplus(
```
[32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L22-L33), [107](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L78-L108), [273](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L238-L274), [380](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L376-L381), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

63:    using FixLib for uint192;
64:
65:    // === Basket Algebra ===
66:
67:    /// Set self to a fresh, empty basket
68:    // self'.erc20s = [] (empty list)
69:    // self'.refAmts = {} (empty map)	// @audit-issue: There should be single blank line between function declarations.
70:    function empty(Basket storage self) internal {

74:    }
75:
76:    /// Set `self` equal to `other`	// @audit-issue: There should be single blank line between function declarations.
77:    function setFrom(Basket storage self, Basket storage other) internal {

85:    }
86:
87:    /// Add `weight` to the refAmount of collateral token `tok` in the basket `self`
88:    // self'.refAmts[tok] = self.refAmts[tok] + weight
89:    // self'.erc20s is keys(self'.refAmts)	// @audit-issue: There should be single blank line between function declarations.
90:    function add(

106:    }
107:
108:    // === Basket Selection ===
109:
110:    /* nextBasket() computes basket' from three inputs:
111:       - the basket configuration (config: BasketConfig)
112:       - the function (isGood: erc20 -> bool), implemented here by goodCollateral()
113:       - the function (targetPerRef: erc20 -> Fix) implemented by the Collateral plugin
114:
115:       ==== Definitions ====
116:
117:       We use e:IERC20 to mean any erc20 token address, and tgt:bytes32 to mean any target name
118:
119:       // targetWeight(b, e) is the target-unit weight of token e in basket b
120:       Let targetWeight(b, e) = b.refAmt[e] * targetPerRef(e)
121:
122:       // backups(tgt) is the list of sound backup tokens we plan to use for target `tgt`.
123:       Let backups(tgt) = config.backups[tgt].erc20s
124:                          .filter(isGood)
125:                          .takeUpTo(config.backups[tgt].max)
126:
127:       Let primeWt(e) = if e in config.erc20s and isGood(e)
128:                        then config.targetAmts[e]
129:                        else 0
130:       Let backupWt(e) = if e in backups(tgt)
131:                         then unsoundPrimeWt(tgt) / len(Backups(tgt))
132:                         else 0
133:       Let unsoundPrimeWt(tgt) = sum(config.targetAmts[e]
134:                                     for e in config.erc20s
135:                                     where config.targetNames[e] == tgt and !isGood(e))
136:
137:       ==== The correctness condition ====
138:
139:       If unsoundPrimeWt(tgt) > 0 and len(backups(tgt)) == 0 for some tgt, then return false.
140:       Else, return true and targetWeight(basket', e) == primeWt(e) + backupWt(e) for all e.
141:
142:       ==== Higher-level desideratum ====
143:
144:       The resulting total target weights should equal the configured target weight. Formally:
145:
146:       let configTargetWeight(tgt) = sum(config.targetAmts[e]
147:                                         for e in config.erc20s
148:                                         where targetNames[e] == tgt)
149:
150:       let targetWeightSum(b, tgt) = sum(targetWeight(b, e)
151:                                         for e in config.erc20s
152:                                         where targetNames[e] == tgt)
153:
154:       Given all that, if nextBasket() returns true, then for all tgt,
155:           targetWeightSum(basket', tgt) == configTargetWeight(tgt)
156:    */
157:
158:    /// Select next reference basket from basket config
159:    /// Works in-place on `newBasket`
160:    /// @param targetNames Scratch space for computation; initial value unused
161:    /// @param newBasket Scratch space for computation; initial value unused
162:    /// @param config The current basket configuration
163:    /// @return success result; i.e newBasket can be expected to contain a valid reference basket	// @audit-issue: There should be single blank line between function declarations.
164:    function nextBasket(

283:    }
284:
285:    // === Private ===
286:
287:    /// Good collateral is registered, collateral, SOUND, has the expected targetName,
288:    /// has nonzero targetPerRef() and refPerTok(), and is not a system token or 0 addr	// @audit-issue: There should be single blank line between function declarations.
289:    function goodCollateral(

310:    }
311:
312:    // === Contract-size savers ===
313:
314:    /// Require that erc20s and targetAmts preserve the current config targets
315:    /// @param _targetAmts Scratch space for computation; assumed to be empty	// @audit-issue: There should be single blank line between function declarations.
316:    function requireConstantConfigTargets(

345:    }
346:
347:    /// Normalize the target amounts to maintain constant UoA value with the current config
348:    /// @dev Unused; left in for future use in reweightable RToken forceSetPrimeBasket() spell
349:    /// @param price {UoA/BU} Price of the reference basket (point estimate)
350:    /// @return newTargetAmts {target/BU} The new target amounts for the normalized basket	// @audit-issue: There should be single blank line between function declarations.
351:    function normalizeByPrice(
```
[69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L63-L70), [76](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L74-L77), [89](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L85-L90), [163](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L106-L164), [288](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L283-L289), [315](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L310-L316), [350](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L345-L351), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

52:    uint192 public worstCasePrice; // D27{qBuyTok/qSellTok}, the worst price we expect to get
53:    // We expect Gnosis Auction either to meet or beat worstCasePrice, or to return the `sell`
54:    // tokens. If we actually *get* a worse clearing that worstCasePrice, we consider it an error in
55:    // our trading scheme and call broker.reportViolation()
56:
57:    // This modifier both enforces the state-machine pattern and guards against reentrancy.	// @audit-issue: There should be single blank line between function declarations.
58:    modifier stateTransition(TradeStatus begin, TradeStatus end) {

68:    }
69:
70:    /// Constructor function, can only be called once
71:    /// @dev Expects sell tokens to already be present
72:    /// @custom:interaction reentrancy-safe b/c state-locking
73:    // checks:
74:    //   state is NOT_STARTED
75:    //   req.sellAmount <= our balance of sell tokens < 2**96
76:    //   req.minBuyAmount < 2**96
77:    // effects:
78:    //   state' is OPEN
79:    //   correctly sets all Metadata and Economic parameters of this contract
80:    //
81:    // actions:
82:    //   increases the `req.sell` allowance for `gnosis` by the amount needed to fund the auction
83:    //   calls gnosis.initiateAuction(...) to launch the requested auction.	// @audit-issue: There should be single blank line between function declarations.
84:    function init(

157:    }
158:
159:    /// Settle trade, transfer tokens to trader, and report bad trade if needed
160:    /// @dev boughtAmt can be manipulated upwards; soldAmt upwards
161:    /// @custom:interaction reentrancy-safe b/c state-locking
162:    // checks:
163:    //   state is OPEN
164:    //   caller is `origin`
165:    //   now >= endTime
166:    // actions:
167:    //   (if not already called) call gnosis.settleAuction(auctionID), which:
168:    //     settles the Gnosis Auction
169:    //     transfers the resulting tokens back to this address
170:    //   if the auction's clearing price was below what we assert it should be,
171:    //     then broker.reportViolation()
172:    //   transfer all balancess of `buy` and `sell` at this address to `origin`
173:    // effects:
174:    //   state' is CLOSED	// @audit-issue: There should be single blank line between function declarations.
175:    function settle()

220:    }
221:
222:    /// Anyone can transfer any ERC20 back to the origin after the trade has been closed
223:    /// @dev Escape hatch in case trading partner freezes up, or other unexpected events
224:    /// @custom:interaction CEI (and respects the state lock)	// @audit-issue: There should be single blank line between function declarations.
225:    function transferToOriginAfterTradeComplete(IERC20 erc20) external {

228:    }
229:
230:    /// @return True if the trade can be settled.
231:    // Guaranteed to be true some time after init(), until settle() is called	// @audit-issue: There should be single blank line between function declarations.
232:    function canSettle() external view returns (bool) {

234:    }
235:
236:    // === Private ===
237:	// @audit-issue: There should be single blank line between function declarations.
238:    function isAuctionCleared() private view returns (bool) {
```
[57](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L52-L58), [83](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L68-L84), [174](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L157-L175), [224](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L220-L225), [231](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L228-L232), [237](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L234-L238), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

117:    address public bidder;
118:    // the bid amount is just whatever token balance is in the contract at settlement time
119:
120:    // This modifier both enforces the state-machine pattern and guards against reentrancy.	// @audit-issue: There should be single blank line between function declarations.
121:    modifier stateTransition(TradeStatus begin, TradeStatus end) {

127:    }
128:
129:    // === Auction Sizing Views ===
130:
131:    /// @return {qSellTok} The size of the lot being sold, in token quanta	// @audit-issue: There should be single blank line between function declarations.
132:    function lot() public view returns (uint256) {

134:    }
135:
136:    /// Calculates how much buy token is needed to purchase the lot at a particular time
137:    /// @param timestamp {s} The timestamp of the bid
138:    /// @return {qBuyTok} The amount of buy tokens required to purchase the lot	// @audit-issue: There should be single blank line between function declarations.
139:    function bidAmount(uint48 timestamp) external view returns (uint256) {

141:    }
142:
143:    // ==== Constructor ===
144:	// @audit-issue: There should be single blank line between function declarations.
145:    constructor() {

147:    }
148:
149:    // === External ===
150:
151:    /// @param origin_ The Trader that originated the trade
152:    /// @param sell_ The asset being sold by the protocol
153:    /// @param buy_ The asset being bought by the protocol
154:    /// @param sellAmount_ {qSellTok} The amount to sell in the auction, in token quanta
155:    /// @param auctionLength {s} How many seconds the dutch auction should run for	// @audit-issue: There should be single blank line between function declarations.
156:    function init(

194:    }
195:
196:    /// Bid for the auction lot at the current price; settle trade in protocol
197:    /// @dev Caller must have provided approval
198:    /// @return amountIn {qBuyTok} The quantity of tokens the bidder paid	// @audit-issue: There should be single blank line between function declarations.
199:    function bid() external returns (uint256 amountIn) {

226:    }
227:
228:    /// Bid with callback for the auction lot at the current price; settle trade in protocol
229:    ///  Sold funds are sent back to the callee first via callee.dutchTradeCallback(...)
230:    ///  Balance of buy token must increase by bidAmount(block.timestamp) after callback
231:    ///
232:    /// @dev Caller must implement IDutchTradeCallee
233:    /// @param data {bytes} The data to pass to the callback
234:    /// @return amountIn {qBuyTok} The quantity of tokens the bidder paid	// @audit-issue: There should be single blank line between function declarations.
235:    function bidWithCallback(bytes calldata data) external returns (uint256 amountIn) {

269:    }
270:
271:    /// Settle the auction, emptying the contract of balances
272:    /// @return soldAmt {qSellTok} Token quantity sold by the protocol
273:    /// @return boughtAmt {qBuyTok} Token quantity purchased by the protocol	// @audit-issue: There should be single blank line between function declarations.
274:    function settle()

293:    }
294:
295:    /// Anyone can transfer any ERC20 back to the origin after the trade has been closed
296:    /// @dev Escape hatch in case of accidentally transferred tokens after auction end
297:    /// @custom:interaction CEI (and respects the state lock)	// @audit-issue: There should be single blank line between function declarations.
298:    function transferToOriginAfterTradeComplete(IERC20Metadata erc20) external {

301:    }
302:
303:    /// @return true iff the trade can be settled.
304:    // Guaranteed to be true some time after init(), until settle() is called	// @audit-issue: There should be single blank line between function declarations.
305:    function canSettle() external view returns (bool) {

307:    }
308:
309:    // === Private ===
310:
311:    /// Return the price of the auction at a particular timestamp
312:    /// @param timestamp {s} The timestamp to get price for
313:    /// @return {buyTok/sellTok}	// @audit-issue: There should be single blank line between function declarations.
314:    function _price(uint48 timestamp) private view returns (uint192) {

360:    }
361:
362:    /// Calculates how much buy token is needed to purchase the lot at a particular price
363:    /// @param price {buyTok/sellTok}
364:    /// @return {qBuyTok} The amount of buy tokens required to purchase the lot	// @audit-issue: There should be single blank line between function declarations.
365:    function _bidAmount(uint192 price) public view returns (uint256) {
```
[120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L117-L121), [131](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L127-L132), [138](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L134-L139), [144](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L141-L145), [155](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L147-L156), [198](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L194-L199), [234](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L226-L235), [273](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L269-L274), [297](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L293-L298), [304](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L301-L305), [313](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L307-L314), [364](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L360-L365), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

66:    }
67:
68:    /// @return {qStRSR} The number of votes required in order for a voter to become a proposer	// @audit-issue: There should be single blank line between function declarations.
69:    function proposalThreshold()

163:    }
164:
165:    /// @return {qStRSR} The voting weight the account had at a previous timepoint	// @audit-issue: There should be single blank line between function declarations.
166:    function _getVotes(

181:    }
182:
183:    // === Private ===
184:	// @audit-issue: There should be single blank line between function declarations.
185:    function startedInSameEra(uint256 proposalId) private view returns (bool) {

198:    }
199:
200:    // solhint-disable-next-line func-name-mixedcase	// @audit-issue: There should be single blank line between function declarations.
201:    function CLOCK_MODE() public pure override(GovernorVotes, IGovernor) returns (string memory) {
```
[68](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L66-L69), [165](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L163-L166), [184](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L181-L185), [200](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L198-L201), 


#### Recommendation

Follow the official [Solidity guidelines](https://docs.soliditylang.org/en/latest/style-guide.html#blank-lines).

### `constants` should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

```solidity
Path: ./contracts/libraries/Fixed.sol

213:        if (decimals <= -59) return (rounding == CEIL ? 1 : 0); // 59, because 1e58 > 2**192	// @audit-issue

214:        if (58 <= decimals) revert UIntOutOfBounds(); // 58, because x * 1e58 > 2 ** 192 if x != 0	// @audit-issue

324:            if (y & 1 == 1) result = (result * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;	// @audit-issue

327:            x = (x * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;	// @audit-issue

394:        if (decimals <= -42) return (rounding == CEIL ? 1 : 0);	// @audit-issue

395:        if (96 <= decimals) revert UIntOutOfBounds();	// @audit-issue

397:        decimals -= 18; // shift so that toUint happens at the same time.	// @audit-issue

515:            if (rounding == RoundingMode.ROUND) shiftDelta += (FIX_ONE / 2);	// @audit-issue

586:            r *= 2 - c_256 * r;	// @audit-issue

587:            r *= 2 - c_256 * r;	// @audit-issue

588:            r *= 2 - c_256 * r;	// @audit-issue

589:            r *= 2 - c_256 * r;	// @audit-issue

590:            r *= 2 - c_256 * r;	// @audit-issue

591:            r *= 2 - c_256 * r;	// @audit-issue

592:            r *= 2 - c_256 * r;	// @audit-issue

593:            r *= 2 - c_256 * r;	// @audit-issue

600:                if (mm > ((c_256 - 1) / 2)) result_256 += 1;	// @audit-issue
```
[213](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L213-L213), [214](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L214-L214), [324](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L324-L324), [327](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L327-L327), [394](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L394-L394), [395](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L395-L395), [397](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L397-L397), [515](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L515-L515), [586](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L586-L586), [587](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L587-L587), [588](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L588-L588), [589](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L589-L589), [590](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L590-L590), [591](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L591-L591), [592](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L592-L592), [593](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L593-L593), [600](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L600-L600), 


```solidity
Path: ./contracts/libraries/Permit.sol

20:                    0x1626ba7e,	// @audit-issue
```
[20](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Permit.sol#L20-L20), 


```solidity
Path: ./contracts/libraries/String.sol

16:            if ((uint8(bStr[i]) >= 65) && (uint8(bStr[i]) <= 90)) {	// @audit-issue

18:                bLower[i] = bytes1(uint8(bStr[i]) + 32);	// @audit-issue
```
[16](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/String.sol#L16-L16), [18](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/String.sol#L18-L18), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

260:            gas > (64 * GAS_FOR_BH_QTY) / 63 + GAS_FOR_DISABLE_BASKET,	// @audit-issue
```
[260](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L260-L260), 


```solidity
Path: ./contracts/p1/StRSR.sol

504:        return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method	// @audit-issue

530:            test = (left + right) / 2; // left < test < right because left < right - 1	// @audit-issue
```
[504](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L504-L504), [530](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L530-L530), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

139:        if (length > 5) {	// @audit-issue
```
[139](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L139-L139), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

372:                (low + high) / 2,	// @audit-issue
```
[372](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L372-L372), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

165:        assert(address(sell_) != address(0) && address(buy_) != address(0) && auctionLength >= 60);	// @audit-issue

168:        require(prices.sellLow != 0 && prices.sellHigh < FIX_MAX / 1000, "bad sell pricing");	// @audit-issue

169:        require(prices.buyLow != 0 && prices.buyHigh < FIX_MAX / 1000, "bad buy pricing");	// @audit-issue
```
[165](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L165-L165), [168](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L168-L168), [169](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L169-L169), 


#### Recommendation

Consider defining constants with meaningful names for magic numbers and hexadecimal literals to improve code readability and maintainability.

### Constant redefined elsewhere
Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A [cheap way](https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678) to store constants in a single location is to create an `internal constant` in a `library`. If the variable is a local cache of another contract's value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don't get out of sync.

```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

95:    TradeKind public constant KIND = TradeKind.DUTCH_AUCTION;	// @audit-issue seen in: ./contracts/plugins/trading/GnosisTrade.sol
```
[95](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L95-L95), 


#### Recommendation

To avoid constant values being redefined in multiple locations, consider defining constants in a single contract, such as a library, using internal constants. This approach ensures that values remain consistent and reduces the risk of synchronization issues. If a variable serves as a local cache of another contract's value, make the cache variable internal or private, requiring external users to query the contract with the source of truth to prevent synchronization problems.

### Lack of index element validation in function
There's no validation to check whether the index element provided as an argument actually exists in the call. This omission could lead to unintended behavior if an element that does not exist in the call is passed to the function. The function should validate that the provided index element exists in the call before proceeding.

```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

40:            bytes32 versionHash = validForVersions[i];	// @audit-issue

75:            emit AssetPluginRegistryUpdated(versionHash, _asset, _validities[i]);	// @audit-issue

103:            emit AssetPluginRegistryUpdated(_versionHash, asset, _validities[i]);	// @audit-issue
```
[40](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L40-L40), [75](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L75-L75), [103](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L103-L103), 


```solidity
Path: ./contracts/libraries/Array.sol

13:                if (arr[i] == arr[j]) return false;	// @audit-issue

24:            if (uint160(address(arr[i])) <= uint160(address(arr[i - 1]))) return false;	// @audit-issue
```
[13](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L13-L13), [24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L24-L24), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

91:            erc20s[i].safeTransfer(address(backingManager), erc20s[i].balanceOf(address(this)));	// @audit-issue

181:            tryTrade(kinds[i], req, trade.prices);	// @audit-issue
```
[91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L91-L91), [181](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L181-L181), 


```solidity
Path: ./contracts/p1/Distributor.sol

93:            _setDistribution(dests[i], shares[i]);	// @audit-issue
```
[93](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L93-L93), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

49:            _register(assets_[i]);	// @audit-issue
```
[49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L49-L49), 


```solidity
Path: ./contracts/p1/BackingManager.sol

257:                    erc20s[i].safeTransfer(	// @audit-issue
```
[257](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L257-L257), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

269:            config.targetNames[erc20s[i]] = names[i];	// @audit-issue

300:            conf.erc20s.push(erc20s[i]);	// @audit-issue

562:                uint192 amt = portions[i].mul(b.refAmts[b.erc20s[j]], FLOOR);	// @audit-issue

689:                    erc20s[i] != IERC20(address(0)),	// @audit-issue
```
[269](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L269-L269), [300](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L300-L300), [562](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L562-L562), [689](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L689-L689), 


```solidity
Path: ./contracts/p1/RToken.sol

342:            require(bal - pastBals[i] >= minAmounts[i], "redemption below minimum");	// @audit-issue
```
[342](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L342-L342), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

150:            if (ckpts[mid].fromTimepoint > timepoint) {	// @audit-issue

158:            return high == 0 ? 0 : ckpts[high - 1].val;	// @audit-issue

271:                ckpts[pos - 1].val = SafeCastUpgradeable.toUint224(newWeight);	// @audit-issue
```
[150](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L150-L150), [158](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L158-L158), [271](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L271-L271), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

341:            if (amt > targetAmts[i]) _targetAmts.set(targetName, amt - targetAmts[i]);	// @audit-issue

382:            newTargetAmts[i] = targetAmts[i].mulDiv(price, newPrice, CEIL);	// @audit-issue
```
[341](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L341-L341), [382](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L382-L382), 


#### Recommendation

Integrate explicit index validation checks at the beginning of functions that operate based on index elements. Use conditional statements to verify that the provided index falls within the valid range of existing elements. For array operations, ensure the index is less than the array's length. For mappings, consider additional logic to confirm the presence of a key. For example, in an array-based function:
```solidity
function getElementByIndex(uint256 index) public view returns (ElementType) {
    require(index < array.length, "Index out of bounds");
    return array[index];
}
```


### Contract should expose an `interface`
All `external`/`public` functions should extend an `interface`. This is useful to make sure that the whole API is extracted.


```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

9:contract DAOFeeRegistry {	// @audit-issue
```
[9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L9-L9), 


```solidity
Path: ./contracts/registry/VersionRegistry.sol

12:contract VersionRegistry {	// @audit-issue
```
[12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L12-L12), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

12:contract AssetPluginRegistry {	// @audit-issue
```
[12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L12-L12), 


```solidity
Path: ./contracts/registry/RoleRegistry.sol

11:contract RoleRegistry is AccessControlEnumerable {	// @audit-issue
```
[11](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/RoleRegistry.sol#L11-L11), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

24:contract Governance is	// @audit-issue
25:    Governor,
26:    GovernorSettings,
27:    GovernorCountingSimple,
28:    GovernorVotes,
29:    GovernorVotesQuorumFraction,
30:    GovernorTimelockControl
```
[24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L24-L30), 


#### Recommendation

Consider defining an `interface` that includes all `external`/`public` functions of the contract. Exposing a well-defined interface helps ensure that the entire API is extracted and provides a clear and standardized way for other contracts or users to interact with your contract.

### Polymorphic functions make security audits more time-consuming and error-prone
The instances below point to one of two functions with the same name. Consider naming each function differently, in order to make code navigation and analysis easier.

```solidity
Path: ./contracts/libraries/Fixed.sol

190:    function toUint(uint192 x, RoundingMode rounding) internal pure returns (uint136) {	// @audit-issue same function also on line(s): 183

206:    function shiftl(	// @audit-issue same function also on line(s): 198

259:    function mul(	// @audit-issue same function also on line(s): 252

284:    function div(	// @audit-issue same function also on line(s): 277

303:    function divu(	// @audit-issue same function also on line(s): 296

387:    function shiftl_toUint(	// @audit-issue same function also on line(s): 380

413:    function mulu_toUint(	// @audit-issue same function also on line(s): 406

431:    function mul_toUint(	// @audit-issue same function also on line(s): 424

455:    function muluDivu(	// @audit-issue same function also on line(s): 443

480:    function mulDiv(	// @audit-issue same function also on line(s): 468
```
[190](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L190-L190), [206](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L206-L206), [259](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L259-L259), [284](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L284-L284), [303](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L303-L303), [387](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L387-L387), [413](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L413-L413), [431](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L431-L431), [455](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L455-L455), [480](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L480-L480), 


```solidity
Path: ./contracts/p1/StRSR.sol

915:    function _notZero(uint256 val) internal pure {	// @audit-issue same function also on line(s): 911
```
[915](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L915-L915), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

419:    function price(bool applyIssuancePremium) public view returns (uint192 low, uint192 high) {	// @audit-issue same function also on line(s): 409

482:    function quote(	// @audit-issue same function also on line(s): 467
```
[419](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L419-L419), [482](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L482-L482), 


#### Recommendation

Rename one or both of the polymorphic functions to have distinct names, improving code readability and making security audits more efficient and less error-prone. Clear and unique function names help prevent confusion and ensure that the intended function is called.

### Consider using named returns
Using named returns makes the code more self-documenting, makes it easier to fill out NatSpec, and in some cases can save gas. The cases below are where there currently is at most one return statement, which is ideal for named returns.

```solidity
Path: ./contracts/registry/VersionRegistry.sol

88:    function getImplementationForVersion(bytes32 versionHash)
89:        external
90:        view
91:        returns (Implementations memory)	// @audit-issue
```
[91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L88-L91), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

115:    function isValidAsset(bytes32 _versionHash, address _asset) external view returns (bool) {	// @audit-issue
```
[115](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L115-L115), 


```solidity
Path: ./contracts/registry/RoleRegistry.sol

18:    function isOwner(address account) public view returns (bool) {	// @audit-issue

22:    function isEmergencyCouncil(address account) public view returns (bool) {	// @audit-issue

26:    function isOwnerOrEmergencyCouncil(address account) public view returns (bool) {	// @audit-issue
```
[18](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/RoleRegistry.sol#L18-L18), [22](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/RoleRegistry.sol#L22-L22), [26](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/RoleRegistry.sol#L26-L26), 


```solidity
Path: ./contracts/mixins/Versioned.sol

14:    function version() public pure virtual override returns (string memory) {	// @audit-issue
```
[14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Versioned.sol#L14-L14), 


```solidity
Path: ./contracts/mixins/Auth.sol

99:    function frozen() public view returns (bool) {	// @audit-issue

105:    function tradingPausedOrFrozen() public view returns (bool) {	// @audit-issue

111:    function issuancePausedOrFrozen() public view returns (bool) {	// @audit-issue
```
[99](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L99-L99), [105](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L105-L105), [111](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L111-L111), 


```solidity
Path: ./contracts/libraries/Array.sol

9:    function allUnique(IERC20[] memory arr) internal pure returns (bool) {	// @audit-issue

21:    function sortedAndAllUnique(IERC20[] memory arr) internal pure returns (bool) {	// @audit-issue
```
[9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L9-L9), [21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L21-L21), 


```solidity
Path: ./contracts/libraries/Fixed.sol

183:    function toUint(uint192 x) internal pure returns (uint136) {	// @audit-issue

190:    function toUint(uint192 x, RoundingMode rounding) internal pure returns (uint136) {	// @audit-issue

198:    function shiftl(uint192 x, int8 decimals) internal pure returns (uint192) {	// @audit-issue

206:    function shiftl(
207:        uint192 x,
208:        int8 decimals,
209:        RoundingMode rounding
210:    ) internal pure returns (uint192) {	// @audit-issue

223:    function plus(uint192 x, uint192 y) internal pure returns (uint192) {	// @audit-issue

230:    function plusu(uint192 x, uint256 y) internal pure returns (uint192) {	// @audit-issue

237:    function minus(uint192 x, uint192 y) internal pure returns (uint192) {	// @audit-issue

244:    function minusu(uint192 x, uint256 y) internal pure returns (uint192) {	// @audit-issue

252:    function mul(uint192 x, uint192 y) internal pure returns (uint192) {	// @audit-issue

259:    function mul(
260:        uint192 x,
261:        uint192 y,
262:        RoundingMode rounding
263:    ) internal pure returns (uint192) {	// @audit-issue

270:    function mulu(uint192 x, uint256 y) internal pure returns (uint192) {	// @audit-issue

277:    function div(uint192 x, uint192 y) internal pure returns (uint192) {	// @audit-issue

284:    function div(
285:        uint192 x,
286:        uint192 y,
287:        RoundingMode rounding
288:    ) internal pure returns (uint192) {	// @audit-issue

296:    function divu(uint192 x, uint256 y) internal pure returns (uint192) {	// @audit-issue

303:    function divu(
304:        uint192 x,
305:        uint256 y,
306:        RoundingMode rounding
307:    ) internal pure returns (uint192) {	// @audit-issue

317:    function powu(uint192 x_, uint48 y) internal pure returns (uint192) {	// @audit-issue

332:    function sqrt(uint192 x) internal pure returns (uint192) {	// @audit-issue

337:    function lt(uint192 x, uint192 y) internal pure returns (bool) {	// @audit-issue

341:    function lte(uint192 x, uint192 y) internal pure returns (bool) {	// @audit-issue

345:    function gt(uint192 x, uint192 y) internal pure returns (bool) {	// @audit-issue

349:    function gte(uint192 x, uint192 y) internal pure returns (bool) {	// @audit-issue

353:    function eq(uint192 x, uint192 y) internal pure returns (bool) {	// @audit-issue

357:    function neq(uint192 x, uint192 y) internal pure returns (bool) {	// @audit-issue

364:    function near(
365:        uint192 x,
366:        uint192 y,
367:        uint192 epsilon
368:    ) internal pure returns (bool) {	// @audit-issue

380:    function shiftl_toUint(uint192 x, int8 decimals) internal pure returns (uint256) {	// @audit-issue

387:    function shiftl_toUint(
388:        uint192 x,
389:        int8 decimals,
390:        RoundingMode rounding
391:    ) internal pure returns (uint256) {	// @audit-issue

406:    function mulu_toUint(uint192 x, uint256 y) internal pure returns (uint256) {	// @audit-issue

413:    function mulu_toUint(
414:        uint192 x,
415:        uint256 y,
416:        RoundingMode rounding
417:    ) internal pure returns (uint256) {	// @audit-issue

424:    function mul_toUint(uint192 x, uint192 y) internal pure returns (uint256) {	// @audit-issue

431:    function mul_toUint(
432:        uint192 x,
433:        uint192 y,
434:        RoundingMode rounding
435:    ) internal pure returns (uint256) {	// @audit-issue

443:    function muluDivu(
444:        uint192 x,
445:        uint256 y,
446:        uint256 z
447:    ) internal pure returns (uint192) {	// @audit-issue

455:    function muluDivu(
456:        uint192 x,
457:        uint256 y,
458:        uint256 z,
459:        RoundingMode rounding
460:    ) internal pure returns (uint192) {	// @audit-issue

468:    function mulDiv(
469:        uint192 x,
470:        uint192 y,
471:        uint192 z
472:    ) internal pure returns (uint192) {	// @audit-issue

480:    function mulDiv(
481:        uint192 x,
482:        uint192 y,
483:        uint192 z,
484:        RoundingMode rounding
485:    ) internal pure returns (uint192) {	// @audit-issue

494:    function safeMul(
495:        uint192 a,
496:        uint192 b,
497:        RoundingMode rounding
498:    ) internal pure returns (uint192) {	// @audit-issue

544:    function safeDiv(
545:        uint192 a,
546:        uint192 b,
547:        RoundingMode rounding
548:    ) internal pure returns (uint192) {	// @audit-issue
```
[183](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L183-L183), [190](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L190-L190), [198](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L198-L198), [210](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L206-L210), [223](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L223-L223), [230](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L230-L230), [237](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L237-L237), [244](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L244-L244), [252](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L252-L252), [263](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L259-L263), [270](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L270-L270), [277](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L277-L277), [288](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L284-L288), [296](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L296-L296), [307](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L303-L307), [317](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L317-L317), [332](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L332-L332), [337](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L337-L337), [341](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L341-L341), [345](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L345-L345), [349](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L349-L349), [353](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L353-L353), [357](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L357-L357), [368](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L364-L368), [380](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L380-L380), [391](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L387-L391), [406](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L406-L406), [417](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L413-L417), [424](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L424-L424), [435](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L431-L435), [447](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L443-L447), [460](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L455-L460), [472](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L468-L472), [485](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L480-L485), [498](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L494-L498), [548](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L544-L548), 


```solidity
Path: ./contracts/libraries/Allowance.sol

7:    function allowance(address owner, address spender) external view returns (uint256);	// @audit-issue
```
[7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L7-L7), 


```solidity
Path: ./contracts/libraries/String.sol

11:    function toLower(string memory str) internal pure returns (string memory) {	// @audit-issue
```
[11](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/String.sol#L11-L11), 


```solidity
Path: ./contracts/p1/Main.sol

86:    function hasRole(bytes32 role, address account)
87:        public
88:        view
89:        override(IAccessControlUpgradeable, AccessControlUpgradeable)
90:        returns (bool)	// @audit-issue
```
[90](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L86-L90), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

78:    function register(IAsset asset) external governance returns (bool) {	// @audit-issue

126:    function toAsset(IERC20 erc20) external view returns (IAsset) {	// @audit-issue

134:    function toColl(IERC20 erc20) external view returns (ICollateral) {	// @audit-issue

142:    function isRegistered(IERC20 erc20) external view returns (bool) {	// @audit-issue

191:    function size() external view returns (uint256) {	// @audit-issue

253:    function _reserveGas() private view returns (uint256) {	// @audit-issue
```
[78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L78-L78), [126](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L126-L126), [134](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L134-L134), [142](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L142-L142), [191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L191-L191), [253](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L253-L253), 


```solidity
Path: ./contracts/p1/StRSR.sol

502:    function exchangeRate() public view returns (uint192) {	// @audit-issue

512:    function endIdForWithdraw(address account) external view returns (uint256) {	// @audit-issue

539:    function draftQueueLen(uint256 era_, address account) external view returns (uint256) {	// @audit-issue

544:    function getDraftRSR() external view returns (uint256) {	// @audit-issue

549:    function getStakeRSR() external view returns (uint256) {	// @audit-issue

554:    function getTotalDrafts() external view returns (uint256) {	// @audit-issue

559:    function getDraftEra() external view returns (uint256) {	// @audit-issue

693:    function rsrRewards() internal view returns (uint256) {	// @audit-issue

754:    function totalSupply() public view returns (uint256) {	// @audit-issue

758:    function balanceOf(address account) public view returns (uint256) {	// @audit-issue

762:    function allowance(address owner, address spender)
763:        public
764:        view
765:        virtual
766:        override
767:        returns (uint256)	// @audit-issue

772:    function transfer(address to, uint256 amount) public returns (bool) {	// @audit-issue

781:    function approve(address spender, uint256 amount) public returns (bool) {	// @audit-issue

790:    function transferFrom(
791:        address from,
792:        address to,
793:        uint256 amount
794:    ) public returns (bool) {	// @audit-issue

800:    function increaseAllowance(address spender, uint256 addedValue) public returns (bool) {	// @audit-issue

806:    function decreaseAllowance(address spender, uint256 subtractedValue) public returns (bool) {	// @audit-issue

942:    function nonces(address owner) public view returns (uint256) {	// @audit-issue

946:    function delegationNonces(address owner) public view returns (uint256) {	// @audit-issue

951:    function DOMAIN_SEPARATOR() external view returns (bytes32) {	// @audit-issue
```
[502](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L502-L502), [512](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L512-L512), [539](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L539-L539), [544](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L544-L544), [549](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L549-L549), [554](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L554-L554), [559](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L559-L559), [693](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L693-L693), [754](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L754-L754), [758](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L758-L758), [767](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L762-L767), [772](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L772-L772), [781](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L781-L781), [794](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L790-L794), [800](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L800-L800), [806](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L806-L806), [942](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L942-L942), [946](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L946-L946), [951](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L951-L951), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

307:    function fullyCollateralized() public view returns (bool) {	// @audit-issue

331:    function isReady() external view returns (bool) {	// @audit-issue

343:    function quantity(IERC20 erc20) public view returns (uint192) {	// @audit-issue

359:    function quantityUnsafe(IERC20 erc20, IAsset asset) public view returns (uint192) {	// @audit-issue

366:    function issuancePremium(ICollateral coll) public view returns (uint192) {	// @audit-issue

391:    function _quantity(
392:        IERC20 erc20,
393:        ICollateral coll,
394:        RoundingMode rounding
395:    ) internal view returns (uint192) {	// @audit-issue
```
[307](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L307-L307), [331](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L331-L331), [343](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L343-L343), [359](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L359-L359), [366](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L366-L366), [395](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L391-L395), 


```solidity
Path: ./contracts/p1/Deployer.sol

74:    function implementations() external view override returns (Implementations memory) {	// @audit-issue

107:    function deploy(
108:        string memory name,
109:        string memory symbol,
110:        string calldata mandate,
111:        address owner,
112:        DeploymentParams memory params
113:    ) external returns (address) {	// @audit-issue
```
[74](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L74-L74), [113](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L107-L113), 


```solidity
Path: ./contracts/p1/RToken.sol

429:    function issuanceAvailable() external view returns (uint256) {	// @audit-issue

441:    function issuanceThrottleParams() external view returns (ThrottleLib.Params memory) {	// @audit-issue

446:    function redemptionThrottleParams() external view returns (ThrottleLib.Params memory) {	// @audit-issue
```
[429](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L429-L429), [441](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L441-L441), [446](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L446-L446), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

58:    function clock() public view returns (uint48) {	// @audit-issue

66:    function CLOCK_MODE() public pure returns (string memory) {	// @audit-issue

70:    function currentEra() external view returns (uint256) {	// @audit-issue

74:    function checkpoints(address account, uint48 pos) public view returns (Checkpoint memory) {	// @audit-issue

78:    function numCheckpoints(address account) public view returns (uint48) {	// @audit-issue

82:    function delegates(address account) public view returns (address) {	// @audit-issue

86:    function getVotes(address account) public view returns (uint256) {	// @audit-issue

91:    function getPastVotes(address account, uint256 timepoint) public view returns (uint256) {	// @audit-issue

98:    function getPastTotalSupply(uint256 timepoint) public view returns (uint256) {	// @audit-issue

105:    function getPastEra(uint256 timepoint) public view returns (uint256) {	// @audit-issue

112:    function _checkpointsLookup(Checkpoint[] storage ckpts, uint256 timepoint)
113:        private
114:        view
115:        returns (uint256)	// @audit-issue

283:    function _add(uint256 a, uint256 b) private pure returns (uint256) {	// @audit-issue

287:    function _subtract(uint256 a, uint256 b) private pure returns (uint256) {	// @audit-issue
```
[58](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L58-L58), [66](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L66-L66), [70](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L70-L70), [74](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L74-L74), [78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L78-L78), [82](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L82-L82), [86](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L86-L86), [91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L91-L91), [98](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L98-L98), [105](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L105-L105), [115](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L112-L115), [283](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L283-L283), [287](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L287-L287), 


```solidity
Path: ./contracts/p1/Broker.sol

125:    function openTrade(
126:        TradeKind kind,
127:        TradeRequest memory req,
128:        TradePrices memory prices
129:    ) external returns (ITrade) {	// @audit-issue

243:    function newBatchAuction(TradeRequest memory req, address caller) private returns (ITrade) {	// @audit-issue

268:    function newDutchAuction(
269:        TradeRequest memory req,
270:        TradePrices memory prices,
271:        ITrading caller
272:    ) private returns (ITrade) {	// @audit-issue

298:    function pricedAtTimestamp(IAsset asset) private view returns (bool) {	// @audit-issue
```
[129](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L125-L129), [243](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L243-L243), [272](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L268-L272), [298](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L298-L298), 


```solidity
Path: ./contracts/p1/mixins/TradeLib.sol

156:    function isEnoughToSell(
157:        IAsset asset,
158:        uint192 amt,
159:        uint192 price,
160:        uint192 minTradeVolume
161:    ) internal view returns (bool) {	// @audit-issue

174:    function minTradeSize(uint192 minTradeVolume, uint192 price) private pure returns (uint192) {	// @audit-issue

182:    function maxTradeSize(
183:        IAsset sell,
184:        IAsset buy,
185:        uint192 price
186:    ) private view returns (uint192) {	// @audit-issue
```
[161](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L156-L161), [174](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L174-L174), [186](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L182-L186), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

381:    function isBetterSurplus(
382:        MaxSurplusDeficit memory curr,
383:        CollateralStatus other,
384:        uint192 surplusAmt
385:    ) private pure returns (bool) {	// @audit-issue
```
[385](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L381-L385), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

164:    function nextBasket(
165:        Basket storage newBasket,
166:        EnumerableSet.Bytes32Set storage targetNames,
167:        BasketConfig storage config,
168:        IAssetRegistry assetRegistry
169:    ) external returns (bool) {	// @audit-issue

289:    function goodCollateral(
290:        bytes32 targetName,
291:        IERC20 erc20,
292:        IAssetRegistry assetRegistry
293:    ) private view returns (bool) {	// @audit-issue
```
[169](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L164-L169), [293](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L289-L293), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

232:    function canSettle() external view returns (bool) {	// @audit-issue

238:    function isAuctionCleared() private view returns (bool) {	// @audit-issue
```
[232](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L232-L232), [238](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L238-L238), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

132:    function lot() public view returns (uint256) {	// @audit-issue

139:    function bidAmount(uint48 timestamp) external view returns (uint256) {	// @audit-issue

305:    function canSettle() external view returns (bool) {	// @audit-issue

314:    function _price(uint48 timestamp) private view returns (uint192) {	// @audit-issue

365:    function _bidAmount(uint192 price) public view returns (uint256) {	// @audit-issue
```
[132](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L132-L132), [139](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L139-L139), [305](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L305-L305), [314](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L314-L314), [365](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L365-L365), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

55:    function votingDelay() public view override(IGovernor, GovernorSettings) returns (uint256) {	// @audit-issue

59:    function votingPeriod() public view override(IGovernor, GovernorSettings) returns (uint256) {	// @audit-issue

69:    function proposalThreshold()
70:        public
71:        view
72:        override(Governor, GovernorSettings)
73:        returns (uint256)	// @audit-issue

85:    function quorum(uint256 timepoint)
86:        public
87:        view
88:        virtual
89:        override(IGovernor, GovernorVotesQuorumFraction)
90:        returns (uint256)	// @audit-issue

95:    function state(uint256 proposalId)
96:        public
97:        view
98:        override(Governor, GovernorTimelockControl)
99:        returns (ProposalState)	// @audit-issue

124:    function cancel(
125:        address[] memory targets,
126:        uint256[] memory values,
127:        bytes[] memory calldatas,
128:        bytes32 descriptionHash
129:    ) public override(Governor, IGovernor) returns (uint256) {	// @audit-issue

147:    function _cancel(
148:        address[] memory targets,
149:        uint256[] memory values,
150:        bytes[] memory calldatas,
151:        bytes32 descriptionHash
152:    ) internal override(Governor, GovernorTimelockControl) returns (uint256) {	// @audit-issue

156:    function _executor()
157:        internal
158:        view
159:        override(Governor, GovernorTimelockControl)
160:        returns (address)	// @audit-issue

166:    function _getVotes(
167:        address account,
168:        uint256 timepoint,
169:        bytes memory /*params*/
170:    ) internal view override(Governor, GovernorVotes) returns (uint256) {	// @audit-issue

174:    function supportsInterface(bytes4 interfaceId)
175:        public
176:        view
177:        override(Governor, GovernorTimelockControl)
178:        returns (bool)	// @audit-issue

185:    function startedInSameEra(uint256 proposalId) private view returns (bool) {	// @audit-issue

196:    function clock() public view override(GovernorVotes, IGovernor) returns (uint48) {	// @audit-issue

201:    function CLOCK_MODE() public pure override(GovernorVotes, IGovernor) returns (string memory) {	// @audit-issue
```
[55](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L55-L55), [59](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L59-L59), [73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L69-L73), [90](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L85-L90), [99](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L95-L99), [129](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L124-L129), [152](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L147-L152), [160](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L156-L160), [170](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L166-L170), [178](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L174-L178), [185](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L185-L185), [196](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L196-L196), [201](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L201-L201), 


#### Recommendation

Adopt named returns in your Solidity functions, especially in cases where functions contain a single return statement. This approach enhances code readability and documentation by making the return values clear and explicit. When defining your function, specify the return types with names, and manipulate these named variables directly within your function logic. Additionally, leverage named returns to streamline your NatSpec documentation, providing clear descriptions for each return variable. Evaluate your current contracts for opportunities to refactor functions to use named returns, prioritizing those with simple return patterns for immediate benefits in gas efficiency and code clarity.

### Use `abi.encodeCall()` instead of `abi.encodeWithSignature()`/`abi.encodeWithSelector()`
Starting with version 0.8.11, abi.encodeCall() has compiler [type safety](https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3693), whereas the other two functions do not.

```solidity
Path: ./contracts/p1/Main.sol

159:            abi.encodeWithSelector(UUPSUpgradeable.upgradeTo.selector, implementation)	// @audit-issue
```
[159](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L159-L159), 


```solidity
Path: ./contracts/p1/mixins/RewardableLib.sol

30:                abi.encodeWithSignature("claimRewards()"),	// @audit-issue

42:            abi.encodeWithSignature("claimRewards()"),	// @audit-issue
```
[30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L30-L30), [42](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L42-L42), 


#### Recommendation

In Solidity version 0.8.11 and later, it's recommended to use `abi.encodeCall()` for creating function call data, as it provides better type safety compared to `abi.encodeWithSignature()` or `abi.encodeWithSelector()`. This helps prevent type-related errors and ensures more reliable contract interactions.

### Missing events in initializers/deploys
As a best practice, consider emitting an event when the contract is initialized. In this way, it's easy for the user to track the exact point in time when the contract was initialized, by filtering the emitted events.

```solidity
Path: ./contracts/p1/RevenueTrader.sol

27:    function init(	// @audit-issue
```
[27](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L27-L27), 


```solidity
Path: ./contracts/p1/Distributor.sol

44:    function init(IMain main_, RevenueShare calldata dist) external initializer {	// @audit-issue
```
[44](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L44-L44), 


```solidity
Path: ./contracts/p1/Furnace.sol

33:    function init(IMain main_, uint192 ratio_) external initializer {	// @audit-issue
```
[33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L33-L33), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

42:    function init(IMain main_, IAsset[] calldata assets_) external initializer {	// @audit-issue
```
[42](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L42-L42), 


```solidity
Path: ./contracts/p1/BackingManager.sol

49:    function init(	// @audit-issue
```
[49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L49-L49), 


```solidity
Path: ./contracts/p1/StRSR.sol

175:    function init(	// @audit-issue
```
[175](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L175-L175), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

108:    function init(	// @audit-issue
```
[108](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L108-L108), 


```solidity
Path: ./contracts/p1/RToken.sol

61:    function init(	// @audit-issue
```
[61](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L61-L61), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

84:    function init(	// @audit-issue
```
[84](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L84-L84), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

156:    function init(	// @audit-issue
```
[156](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L156-L156), 


#### Recommendation

To provide transparency and enable users to track the initialization of the contract, consider emitting an event within the contract's initializer function. Emitting an event during initialization can help users pinpoint the exact moment the contract was initialized by filtering and monitoring the emitted events.

### Inefficient Array Usage
Use mappings instead of arrays for managing lists of data in order to conserve gas. Mappings are less expensive and more efficient for accessing any value without having to iterate through an array.

```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

31:    function registerAsset(address _asset, bytes32[] calldata validForVersions) external {	// @audit-issue

53:        bytes32[] calldata _versionHashes,	// @audit-issue

54:        bool[] calldata _validities	// @audit-issue

81:        address[] calldata _assets,	// @audit-issue

82:        bool[] calldata _validities	// @audit-issue
```
[31](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L31-L31), [53](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L53-L53), [54](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L54-L54), [81](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L81-L81), [82](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L82-L82), 


```solidity
Path: ./contracts/libraries/Array.sol

9:    function allUnique(IERC20[] memory arr) internal pure returns (bool) {	// @audit-issue

21:    function sortedAndAllUnique(IERC20[] memory arr) internal pure returns (bool) {	// @audit-issue
```
[9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L9-L9), [21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L21-L21), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

76:    function returnTokens(IERC20[] memory erc20s) external notTradingPausedOrFrozen {	// @audit-issue

109:    function manageTokens(IERC20[] calldata erc20s, TradeKind[] calldata kinds)	// @audit-issue
```
[76](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L76-L76), [109](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L109-L109), 


```solidity
Path: ./contracts/p1/Distributor.sol

81:    function setDistributions(address[] calldata dests, RevenueShare[] calldata shares)	// @audit-issue

139:        Transfer[] memory transfers = new Transfer[](destinations.length());	// @audit-issue
```
[81](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L81-L81), [139](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L139-L139), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

42:    function init(IMain main_, IAsset[] calldata assets_) external initializer {	// @audit-issue
```
[42](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L42-L42), 


```solidity
Path: ./contracts/p1/BackingManager.sol

179:    function forwardRevenue(IERC20[] calldata erc20s) external nonReentrant {	// @audit-issue
```
[179](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L179-L179), 


```solidity
Path: ./contracts/p1/StRSR.sol

84:    mapping(uint256 => mapping(address => CumulativeDraft[])) public draftQueues; // {qDrafts}	// @audit-issue

308:        CumulativeDraft[] storage queue = draftQueues[draftEra][account];	// @audit-issue

354:        CumulativeDraft[] storage queue = draftQueues[draftEra][account];	// @audit-issue

514:        CumulativeDraft[] storage queue = draftQueues[draftEra][account];	// @audit-issue

653:        CumulativeDraft[] storage queue = draftQueues[draftEra][account];	// @audit-issue
```
[84](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L84-L84), [308](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L308-L308), [354](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L354-L354), [514](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L514-L514), [653](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L653-L653), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

141:        uint192[] memory refAmts = new uint192[](len);	// @audit-issue

197:    function setPrimeBasket(IERC20[] calldata erc20s, uint192[] calldata targetAmts) external {	// @audit-issue

207:    function forceSetPrimeBasket(IERC20[] calldata erc20s, uint192[] calldata targetAmts) external {	// @audit-issue

228:        IERC20[] calldata erc20s,	// @audit-issue

229:        uint192[] memory targetAmts,	// @audit-issue

258:        bytes32[] memory names = new bytes32[](erc20s.length);	// @audit-issue

287:        IERC20[] calldata erc20s	// @audit-issue

522:        uint48[] memory basketNonces,	// @audit-issue

523:        uint192[] memory portions,	// @audit-issue

528:        IERC20[] memory erc20sAll = new IERC20[](assetRegistry.size());	// @audit-issue

529:        ICollateral[] memory collsAll = new ICollateral[](erc20sAll.length);	// @audit-issue

530:        uint192[] memory refAmtsAll = new uint192[](erc20sAll.length);	// @audit-issue

675:        uint192[] memory refAmts = new uint192[](len);	// @audit-issue

683:    function requireValidCollArray(IERC20[] calldata erc20s) private view {	// @audit-issue
```
[141](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L141-L141), [197](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L197-L197), [207](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L207-L207), [228](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L228-L228), [229](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L229-L229), [258](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L258-L258), [287](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L287-L287), [522](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L522-L522), [523](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L523-L523), [528](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L528-L528), [529](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L529-L529), [530](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L530-L530), [675](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L675-L675), [683](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L683-L683), 


```solidity
Path: ./contracts/p1/Deployer.sol

251:        IAsset[] memory assets = new IAsset[](2);	// @audit-issue
```
[251](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L251-L251), 


```solidity
Path: ./contracts/p1/RToken.sol

139:        (address[] memory erc20s, uint256[] memory deposits) = basketHandler.quote(	// @audit-issue

206:        (address[] memory erc20s, uint256[] memory amounts) = basketHandler.quote(	// @audit-issue

256:        uint48[] memory basketNonces,	// @audit-issue

257:        uint192[] memory portions,	// @audit-issue

258:        address[] memory expectedERC20sOut,	// @audit-issue

259:        uint256[] memory minAmounts	// @audit-issue

286:        (address[] memory erc20s, uint256[] memory amounts) = basketHandler.quoteCustomRedemption(	// @audit-issue

310:        uint256[] memory pastBals = new uint256[](expectedERC20sOut.length);	// @audit-issue
```
[139](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L139-L139), [206](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L206-L206), [256](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L256-L256), [257](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L257-L257), [258](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L258-L258), [259](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L259-L259), [286](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L286-L286), [310](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L310-L310), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

37:    Checkpoint[] private _eras; // {era}	// @audit-issue

41:    mapping(uint256 => mapping(address => Checkpoint[])) private _checkpoints; // {qStRSR}	// @audit-issue

43:    mapping(uint256 => Checkpoint[]) private _totalSupplyCheckpoints; // {qStRSR}	// @audit-issue

112:    function _checkpointsLookup(Checkpoint[] storage ckpts, uint256 timepoint)	// @audit-issue

258:        Checkpoint[] storage ckpts,	// @audit-issue
```
[37](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L37-L37), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L41-L41), [43](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L43-L43), [112](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L112-L112), [258](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L258-L258), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

191:        uint192[] memory goodWeights = new uint192[](targetsLength);	// @audit-issue

195:        uint192[] memory totalWeights = new uint192[](targetsLength);	// @audit-issue

320:        IERC20[] calldata erc20s,	// @audit-issue

321:        uint192[] calldata targetAmts	// @audit-issue

353:        IERC20[] calldata erc20s,	// @audit-issue

354:        uint192[] calldata targetAmts,	// @audit-issue

16:    IERC20[] erc20s; // Ordered list of backup collateral ERC20s	// @audit-issue

34:    IERC20[] erc20s;	// @audit-issue

49:    IERC20[] erc20s; // enumerated keys for refAmts	// @audit-issue
```
[191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L191-L191), [195](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L195-L195), [320](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L320-L320), [321](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L321-L321), [353](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L353-L353), [354](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L354-L354), [16](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L16-L16), [34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L34-L34), [49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L49-L49), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

105:        address[] memory targets,	// @audit-issue

106:        uint256[] memory values,	// @audit-issue

107:        bytes[] memory calldatas,	// @audit-issue

115:        address[] memory targets,	// @audit-issue

116:        uint256[] memory values,	// @audit-issue

117:        bytes[] memory calldatas,	// @audit-issue

125:        address[] memory targets,	// @audit-issue

126:        uint256[] memory values,	// @audit-issue

127:        bytes[] memory calldatas,	// @audit-issue

138:        address[] memory targets,	// @audit-issue

139:        uint256[] memory values,	// @audit-issue

140:        bytes[] memory calldatas,	// @audit-issue

148:        address[] memory targets,	// @audit-issue

149:        uint256[] memory values,	// @audit-issue

150:        bytes[] memory calldatas,	// @audit-issue
```
[105](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L105-L105), [106](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L106-L106), [107](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L107-L107), [115](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L115-L115), [116](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L116-L116), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L117-L117), [125](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L125-L125), [126](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L126-L126), [127](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L127-L127), [138](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L138-L138), [139](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L139-L139), [140](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L140-L140), [148](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L148-L148), [149](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L149-L149), [150](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L150-L150), 


#### Recommendation

In scenarios where data access efficiency is critical, prefer using mappings over arrays in Solidity contracts. Mappings offer more efficient and gas-effective data retrieval and updates, especially when dealing with large or frequently accessed datasets. Ensure to structure your data and choose keys thoughtfully to maximize the efficiency gains offered by mappings. While arrays might be suitable for ordered data or when the entire dataset needs to be iterated, for most other use cases, mappings are likely to be the more gas-efficient choice.

### Enum values should be used in place of constant array indexes
Consider using an `enum` instead of hardcoding an index access to make the code easier to understand.

```solidity
Path: ./contracts/p1/Deployer.sol

252:        assets[0] = new RTokenAsset(components.rToken, params.rTokenMaxTradeVolume);	// @audit-issue

253:        assets[1] = rsrAsset;	// @audit-issue
```
[252](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L252-L252), [253](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L253-L253), 


#### Recommendation

To improve code readability and maintainability, replace hardcoded array indexes with corresponding enum values. Enum values provide descriptive names for array elements, making your code more self-explanatory and reducing the risk of errors when working with arrays. This enhances the overall clarity and robustness of your smart contract code.

### Complex math should be split into multiple steps
Consider splitting long arithmetic calculations (more than 4 operations) into multiple steps to improve the code readability.

```solidity
Path: ./contracts/libraries/Fixed.sol

584:            lo += hi * ((0 - pow2) / pow2 + 1);	// @audit-issue
```
[584](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L584-L584), 


```solidity
Path: ./contracts/p1/Distributor.sol

221:                revTotals.rsrTotal += uint24(	// @audit-issue
222:                    (feeNumerator * uint256(revTotals.rTokenTotal + revTotals.rsrTotal)) /
223:                        (feeDenominator - feeNumerator)
224:                );
```
[221](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L221-L224), 


```solidity
Path: ./contracts/p1/StRSR.sol

472:        seizedRSR += (rewards * rsrAmount + (rsrBalance - 1)) / rsrBalance;	// @audit-issue
```
[472](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L472-L472), 


```solidity
Path: ./contracts/p1/Deployer.sol

49:            address(rsr_) != address(0) &&	// @audit-issue
50:                address(gnosis_) != address(0) &&
51:                address(rsrAsset_) != address(0) &&
52:                address(implementations_.main) != address(0) &&
53:                address(implementations_.trading.gnosisTrade) != address(0) &&
54:                address(implementations_.trading.dutchTrade) != address(0) &&
55:                address(implementations_.components.assetRegistry) != address(0) &&
56:                address(implementations_.components.backingManager) != address(0) &&
57:                address(implementations_.components.basketHandler) != address(0) &&
58:                address(implementations_.components.broker) != address(0) &&
59:                address(implementations_.components.distributor) != address(0) &&
60:                address(implementations_.components.furnace) != address(0) &&
61:                address(implementations_.components.rsrTrader) != address(0) &&
62:                address(implementations_.components.rTokenTrader) != address(0) &&
63:                address(implementations_.components.rToken) != address(0) &&
64:                address(implementations_.components.stRSR) != address(0),

49:            address(rsr_) != address(0) &&	// @audit-issue
50:                address(gnosis_) != address(0) &&
51:                address(rsrAsset_) != address(0) &&
52:                address(implementations_.main) != address(0) &&
53:                address(implementations_.trading.gnosisTrade) != address(0) &&
54:                address(implementations_.trading.dutchTrade) != address(0) &&
55:                address(implementations_.components.assetRegistry) != address(0) &&
56:                address(implementations_.components.backingManager) != address(0) &&
57:                address(implementations_.components.basketHandler) != address(0) &&
58:                address(implementations_.components.broker) != address(0) &&
59:                address(implementations_.components.distributor) != address(0) &&
60:                address(implementations_.components.furnace) != address(0) &&
61:                address(implementations_.components.rsrTrader) != address(0) &&
62:                address(implementations_.components.rTokenTrader) != address(0) &&
63:                address(implementations_.components.rToken) != address(0) &&

49:            address(rsr_) != address(0) &&	// @audit-issue
50:                address(gnosis_) != address(0) &&
51:                address(rsrAsset_) != address(0) &&
52:                address(implementations_.main) != address(0) &&
53:                address(implementations_.trading.gnosisTrade) != address(0) &&
54:                address(implementations_.trading.dutchTrade) != address(0) &&
55:                address(implementations_.components.assetRegistry) != address(0) &&
56:                address(implementations_.components.backingManager) != address(0) &&
57:                address(implementations_.components.basketHandler) != address(0) &&
58:                address(implementations_.components.broker) != address(0) &&
59:                address(implementations_.components.distributor) != address(0) &&
60:                address(implementations_.components.furnace) != address(0) &&
61:                address(implementations_.components.rsrTrader) != address(0) &&
62:                address(implementations_.components.rTokenTrader) != address(0) &&

49:            address(rsr_) != address(0) &&	// @audit-issue
50:                address(gnosis_) != address(0) &&
51:                address(rsrAsset_) != address(0) &&
52:                address(implementations_.main) != address(0) &&
53:                address(implementations_.trading.gnosisTrade) != address(0) &&
54:                address(implementations_.trading.dutchTrade) != address(0) &&
55:                address(implementations_.components.assetRegistry) != address(0) &&
56:                address(implementations_.components.backingManager) != address(0) &&
57:                address(implementations_.components.basketHandler) != address(0) &&
58:                address(implementations_.components.broker) != address(0) &&
59:                address(implementations_.components.distributor) != address(0) &&
60:                address(implementations_.components.furnace) != address(0) &&
61:                address(implementations_.components.rsrTrader) != address(0) &&

49:            address(rsr_) != address(0) &&	// @audit-issue
50:                address(gnosis_) != address(0) &&
51:                address(rsrAsset_) != address(0) &&
52:                address(implementations_.main) != address(0) &&
53:                address(implementations_.trading.gnosisTrade) != address(0) &&
54:                address(implementations_.trading.dutchTrade) != address(0) &&
55:                address(implementations_.components.assetRegistry) != address(0) &&
56:                address(implementations_.components.backingManager) != address(0) &&
57:                address(implementations_.components.basketHandler) != address(0) &&
58:                address(implementations_.components.broker) != address(0) &&
59:                address(implementations_.components.distributor) != address(0) &&
60:                address(implementations_.components.furnace) != address(0) &&

49:            address(rsr_) != address(0) &&	// @audit-issue
50:                address(gnosis_) != address(0) &&
51:                address(rsrAsset_) != address(0) &&
52:                address(implementations_.main) != address(0) &&
53:                address(implementations_.trading.gnosisTrade) != address(0) &&
54:                address(implementations_.trading.dutchTrade) != address(0) &&
55:                address(implementations_.components.assetRegistry) != address(0) &&
56:                address(implementations_.components.backingManager) != address(0) &&
57:                address(implementations_.components.basketHandler) != address(0) &&
58:                address(implementations_.components.broker) != address(0) &&
59:                address(implementations_.components.distributor) != address(0) &&

49:            address(rsr_) != address(0) &&	// @audit-issue
50:                address(gnosis_) != address(0) &&
51:                address(rsrAsset_) != address(0) &&
52:                address(implementations_.main) != address(0) &&
53:                address(implementations_.trading.gnosisTrade) != address(0) &&
54:                address(implementations_.trading.dutchTrade) != address(0) &&
55:                address(implementations_.components.assetRegistry) != address(0) &&
56:                address(implementations_.components.backingManager) != address(0) &&
57:                address(implementations_.components.basketHandler) != address(0) &&
58:                address(implementations_.components.broker) != address(0) &&

49:            address(rsr_) != address(0) &&	// @audit-issue
50:                address(gnosis_) != address(0) &&
51:                address(rsrAsset_) != address(0) &&
52:                address(implementations_.main) != address(0) &&
53:                address(implementations_.trading.gnosisTrade) != address(0) &&
54:                address(implementations_.trading.dutchTrade) != address(0) &&
55:                address(implementations_.components.assetRegistry) != address(0) &&
56:                address(implementations_.components.backingManager) != address(0) &&
57:                address(implementations_.components.basketHandler) != address(0) &&

49:            address(rsr_) != address(0) &&	// @audit-issue
50:                address(gnosis_) != address(0) &&
51:                address(rsrAsset_) != address(0) &&
52:                address(implementations_.main) != address(0) &&
53:                address(implementations_.trading.gnosisTrade) != address(0) &&
54:                address(implementations_.trading.dutchTrade) != address(0) &&
55:                address(implementations_.components.assetRegistry) != address(0) &&
56:                address(implementations_.components.backingManager) != address(0) &&

49:            address(rsr_) != address(0) &&	// @audit-issue
50:                address(gnosis_) != address(0) &&
51:                address(rsrAsset_) != address(0) &&
52:                address(implementations_.main) != address(0) &&
53:                address(implementations_.trading.gnosisTrade) != address(0) &&
54:                address(implementations_.trading.dutchTrade) != address(0) &&
55:                address(implementations_.components.assetRegistry) != address(0) &&

49:            address(rsr_) != address(0) &&	// @audit-issue
50:                address(gnosis_) != address(0) &&
51:                address(rsrAsset_) != address(0) &&
52:                address(implementations_.main) != address(0) &&
53:                address(implementations_.trading.gnosisTrade) != address(0) &&
54:                address(implementations_.trading.dutchTrade) != address(0) &&

49:            address(rsr_) != address(0) &&	// @audit-issue
50:                address(gnosis_) != address(0) &&
51:                address(rsrAsset_) != address(0) &&
52:                address(implementations_.main) != address(0) &&
53:                address(implementations_.trading.gnosisTrade) != address(0) &&
```
[49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L49-L64), [49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L49-L63), [49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L49-L62), [49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L49-L61), [49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L49-L60), [49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L49-L59), [49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L49-L58), [49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L49-L57), [49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L49-L56), [49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L49-L55), [49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L49-L54), [49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L49-L53), 


#### Recommendation

To enhance code readability and maintainability, it's advisable to break down complex arithmetic calculations into multiple steps. This not only makes the code more understandable but also helps in debugging and verifying the correctness of calculations.

### Lack of specific import identifier
It is better to use `import {<identifier>} from "<file.sol>"` instead of `import "<file.sol>"` to improve readability and speed up the compilation time.

```solidity
Path: ./contracts/mixins/Versioned.sol

4:import "../interfaces/IVersioned.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Versioned.sol#L4-L4), 


```solidity
Path: ./contracts/mixins/Auth.sol

4:import "@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol";	// @audit-issue

5:import "../interfaces/IMain.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L5-L5), 


```solidity
Path: ./contracts/mixins/ComponentRegistry.sol

4:import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";	// @audit-issue

5:import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";	// @audit-issue

6:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue

7:import "./Auth.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L7-L7), 


```solidity
Path: ./contracts/libraries/Array.sol

4:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L4-L4), 


```solidity
Path: ./contracts/libraries/Throttle.sol

4:import "./Fixed.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L4-L4), 


```solidity
Path: ./contracts/libraries/Permit.sol

4:import "@openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol";	// @audit-issue

5:import "@openzeppelin/contracts-upgradeable/utils/cryptography/SignatureCheckerUpgradeable.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Permit.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Permit.sol#L5-L5), 


```solidity
Path: ./contracts/p1/Main.sol

4:import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";	// @audit-issue

5:import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";	// @audit-issue

6:import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";	// @audit-issue

7:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue

8:import "../interfaces/IMain.sol";	// @audit-issue

9:import "../mixins/ComponentRegistry.sol";	// @audit-issue

10:import "../mixins/Auth.sol";	// @audit-issue

11:import "../mixins/Versioned.sol";	// @audit-issue

12:import "../registry/VersionRegistry.sol";	// @audit-issue

13:import "../registry/AssetPluginRegistry.sol";	// @audit-issue

14:import "../registry/DAOFeeRegistry.sol";	// @audit-issue

15:import "../interfaces/IBroker.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L7-L7), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L8-L8), [9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L9-L9), [10](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L10-L10), [11](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L11-L11), [12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L12-L12), [13](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L13-L13), [14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L14-L14), [15](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L15-L15), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

4:import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";	// @audit-issue

5:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue

6:import "../interfaces/IMain.sol";	// @audit-issue

7:import "../interfaces/IAssetRegistry.sol";	// @audit-issue

8:import "./mixins/Trading.sol";	// @audit-issue

9:import "./mixins/TradeLib.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L7-L7), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L8-L8), [9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L9-L9), 


```solidity
Path: ./contracts/p1/Distributor.sol

4:import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";	// @audit-issue

5:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue

6:import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";	// @audit-issue

7:import "../interfaces/IDistributor.sol";	// @audit-issue

8:import "../interfaces/IMain.sol";	// @audit-issue

9:import "../libraries/Fixed.sol";	// @audit-issue

10:import "./mixins/Component.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L7-L7), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L8-L8), [9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L9-L9), [10](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L10-L10), 


```solidity
Path: ./contracts/p1/Furnace.sol

4:import "../libraries/Fixed.sol";	// @audit-issue

5:import "../interfaces/IFurnace.sol";	// @audit-issue

6:import "./mixins/Component.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L6-L6), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

4:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue

5:import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";	// @audit-issue

6:import "../interfaces/IAssetRegistry.sol";	// @audit-issue

7:import "../interfaces/IMain.sol";	// @audit-issue

8:import "./mixins/Component.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L7-L7), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L8-L8), 


```solidity
Path: ./contracts/p1/BackingManager.sol

4:import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";	// @audit-issue

5:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue

6:import "../interfaces/IAsset.sol";	// @audit-issue

7:import "../interfaces/IBackingManager.sol";	// @audit-issue

8:import "../interfaces/IMain.sol";	// @audit-issue

9:import "../libraries/Array.sol";	// @audit-issue

10:import "../libraries/Fixed.sol";	// @audit-issue

11:import "./mixins/Trading.sol";	// @audit-issue

12:import "./mixins/RecollateralizationLib.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L7-L7), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L8-L8), [9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L9-L9), [10](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L10-L10), [11](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L11-L11), [12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L12-L12), 


```solidity
Path: ./contracts/p1/StRSR.sol

4:import "@openzeppelin/contracts-upgradeable/interfaces/IERC1271Upgradeable.sol";	// @audit-issue

5:import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";	// @audit-issue

6:import "@openzeppelin/contracts-upgradeable/utils/cryptography/SignatureCheckerUpgradeable.sol";	// @audit-issue

7:import "@openzeppelin/contracts-upgradeable/utils/cryptography/draft-EIP712Upgradeable.sol";	// @audit-issue

8:import "@openzeppelin/contracts-upgradeable/utils/CountersUpgradeable.sol";	// @audit-issue

9:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue

11:import "../interfaces/IStRSR.sol";	// @audit-issue

12:import "../interfaces/IMain.sol";	// @audit-issue

13:import "../libraries/Fixed.sol";	// @audit-issue

14:import "../libraries/Permit.sol";	// @audit-issue

15:import "./mixins/Component.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L7-L7), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L8-L8), [9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L9-L9), [11](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L11-L11), [12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L12-L12), [13](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L13-L13), [14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L14-L14), [15](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L15-L15), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

4:import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";	// @audit-issue

5:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue

6:import "@openzeppelin/contracts/utils/structs/EnumerableMap.sol";	// @audit-issue

7:import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";	// @audit-issue

8:import "../interfaces/IAssetRegistry.sol";	// @audit-issue

9:import "../interfaces/IBasketHandler.sol";	// @audit-issue

10:import "../interfaces/IMain.sol";	// @audit-issue

11:import "../libraries/Array.sol";	// @audit-issue

12:import "../libraries/Fixed.sol";	// @audit-issue

13:import "./mixins/BasketLib.sol";	// @audit-issue

14:import "./mixins/Component.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L7-L7), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L8-L8), [9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L9-L9), [10](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L10-L10), [11](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L11-L11), [12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L12-L12), [13](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L13-L13), [14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L14-L14), 


```solidity
Path: ./contracts/p1/Deployer.sol

4:import "@openzeppelin/contracts/proxy/Clones.sol";	// @audit-issue

5:import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";	// @audit-issue

6:import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";	// @audit-issue

7:import "../interfaces/IAsset.sol";	// @audit-issue

8:import "../interfaces/IAssetRegistry.sol";	// @audit-issue

9:import "../interfaces/IBackingManager.sol";	// @audit-issue

10:import "../interfaces/IBasketHandler.sol";	// @audit-issue

11:import "../interfaces/IBroker.sol";	// @audit-issue

12:import "../interfaces/IDeployer.sol";	// @audit-issue

13:import "../interfaces/IDistributor.sol";	// @audit-issue

14:import "../interfaces/IFurnace.sol";	// @audit-issue

15:import "../interfaces/IRevenueTrader.sol";	// @audit-issue

16:import "../interfaces/IRToken.sol";	// @audit-issue

17:import "../interfaces/IStRSR.sol";	// @audit-issue

18:import "../mixins/Versioned.sol";	// @audit-issue

19:import "../plugins/assets/Asset.sol";	// @audit-issue

20:import "../plugins/assets/RTokenAsset.sol";	// @audit-issue

21:import "./Main.sol";	// @audit-issue

22:import "../libraries/String.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L7-L7), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L8-L8), [9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L9-L9), [10](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L10-L10), [11](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L11-L11), [12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L12-L12), [13](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L13-L13), [14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L14-L14), [15](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L15-L15), [16](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L16-L16), [17](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L17-L17), [18](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L18-L18), [19](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L19-L19), [20](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L20-L20), [21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L21-L21), [22](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L22-L22), 


```solidity
Path: ./contracts/p1/RToken.sol

5:import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";	// @audit-issue

6:import "../interfaces/IMain.sol";	// @audit-issue

7:import "../interfaces/IRToken.sol";	// @audit-issue

8:import "../libraries/Fixed.sol";	// @audit-issue

9:import "../libraries/Throttle.sol";	// @audit-issue

10:import "../vendor/ERC20PermitUpgradeable.sol";	// @audit-issue

11:import "./mixins/Component.sol";	// @audit-issue
```
[5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L7-L7), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L8-L8), [9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L9-L9), [10](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L10-L10), [11](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L11-L11), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

4:import "@openzeppelin/contracts-upgradeable/utils/math/SafeCastUpgradeable.sol";	// @audit-issue

5:import "@openzeppelin/contracts-upgradeable/utils/math/MathUpgradeable.sol";	// @audit-issue

6:import "@openzeppelin/contracts-upgradeable/interfaces/IERC5805Upgradeable.sol";	// @audit-issue

7:import "../interfaces/IStRSRVotes.sol";	// @audit-issue

8:import "./StRSR.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L7-L7), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L8-L8), 


```solidity
Path: ./contracts/p1/Broker.sol

4:import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";	// @audit-issue

5:import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";	// @audit-issue

6:import "@openzeppelin/contracts/proxy/Clones.sol";	// @audit-issue

7:import "../interfaces/IBroker.sol";	// @audit-issue

8:import "../interfaces/IMain.sol";	// @audit-issue

9:import "../interfaces/ITrade.sol";	// @audit-issue

10:import "../libraries/Fixed.sol";	// @audit-issue

11:import "./mixins/Component.sol";	// @audit-issue

12:import "../plugins/trading/DutchTrade.sol";	// @audit-issue

13:import "../plugins/trading/GnosisTrade.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L7-L7), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L8-L8), [9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L9-L9), [10](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L10-L10), [11](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L11-L11), [12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L12-L12), [13](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L13-L13), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

4:import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";	// @audit-issue

5:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue

6:import "../../interfaces/ITrade.sol";	// @audit-issue

7:import "../../interfaces/ITrading.sol";	// @audit-issue

8:import "../../libraries/Allowance.sol";	// @audit-issue

9:import "../../libraries/Fixed.sol";	// @audit-issue

10:import "../../vendor/oz/Multicall.sol";	// @audit-issue

11:import "./Component.sol";	// @audit-issue

12:import "./RewardableLib.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L7-L7), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L8-L8), [9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L9-L9), [10](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L10-L10), [11](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L11-L11), [12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L12-L12), 


```solidity
Path: ./contracts/p1/mixins/TradeLib.sol

4:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue

5:import "../../interfaces/IAsset.sol";	// @audit-issue

6:import "../../libraries/Fixed.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L6-L6), 


```solidity
Path: ./contracts/p1/mixins/RewardableLib.sol

4:import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";	// @audit-issue

5:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue

6:import "@openzeppelin/contracts/utils/Address.sol";	// @audit-issue

7:import "../../interfaces/IAssetRegistry.sol";	// @audit-issue

8:import "../../interfaces/IBackingManager.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L7-L7), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L8-L8), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

4:import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";	// @audit-issue

5:import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";	// @audit-issue

6:import "@openzeppelin/contracts-upgradeable/utils/ContextUpgradeable.sol";	// @audit-issue

7:import "../../interfaces/IComponent.sol";	// @audit-issue

8:import "../../interfaces/IMain.sol";	// @audit-issue

9:import "../../mixins/Versioned.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L7-L7), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L8-L8), [9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L9-L9), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

4:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue

5:import "../../libraries/Fixed.sol";	// @audit-issue

6:import "./TradeLib.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L6-L6), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

4:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue

5:import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";	// @audit-issue

6:import "@openzeppelin/contracts/utils/structs/EnumerableMap.sol";	// @audit-issue

8:import "../../interfaces/IAssetRegistry.sol";	// @audit-issue

9:import "../../libraries/Fixed.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L6-L6), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L8-L8), [9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L9-L9), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

4:import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";	// @audit-issue

5:import "@openzeppelin/contracts/utils/math/Math.sol";	// @audit-issue

6:import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";	// @audit-issue

7:import "../../libraries/Allowance.sol";	// @audit-issue

8:import "../../libraries/Fixed.sol";	// @audit-issue

9:import "../../interfaces/IBroker.sol";	// @audit-issue

10:import "../../interfaces/IGnosis.sol";	// @audit-issue

11:import "../../interfaces/ITrade.sol";	// @audit-issue

12:import "../../mixins/Versioned.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L7-L7), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L8-L8), [9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L9-L9), [10](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L10-L10), [11](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L11-L11), [12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L12-L12), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

4:import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";	// @audit-issue

5:import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";	// @audit-issue

6:import "../../libraries/Fixed.sol";	// @audit-issue

7:import "../../interfaces/IAsset.sol";	// @audit-issue

8:import "../../interfaces/IBroker.sol";	// @audit-issue

9:import "../../interfaces/ITrade.sol";	// @audit-issue

10:import "../../mixins/Versioned.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L7-L7), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L8-L8), [9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L9-L9), [10](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L10-L10), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

4:import "@openzeppelin/contracts/governance/Governor.sol";	// @audit-issue

5:import "@openzeppelin/contracts/governance/extensions/GovernorCountingSimple.sol";	// @audit-issue

6:import "@openzeppelin/contracts/governance/extensions/GovernorSettings.sol";	// @audit-issue

7:import "@openzeppelin/contracts/governance/extensions/GovernorTimelockControl.sol";	// @audit-issue

8:import "@openzeppelin/contracts/governance/extensions/GovernorVotes.sol";	// @audit-issue

9:import "@openzeppelin/contracts/governance/extensions/GovernorVotesQuorumFraction.sol";	// @audit-issue

10:import "../../interfaces/IStRSRVotes.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L7-L7), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L8-L8), [9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L9-L9), [10](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L10-L10), 


#### Recommendation

To improve code clarity and avoid naming conflicts, it's recommended to use specific import identifiers when importing from other contracts or libraries. Instead of using `import "<file.sol>";`, specify the desired identifiers using `import { <identifier1>, <identifier2> } from "<file.sol>";`. This not only enhances readability but also can speed up compilation times by only importing the necessary symbols.

### Imports should be organized more systematically
The contract's interface should be imported first, followed by each of the interfaces it uses, followed by all other files. The examples below do not follow this layout.

```solidity
Path: ./contracts/p1/Main.sol

22:contract MainP1 is Versioned, Initializable, Auth, ComponentRegistry, UUPSUpgradeable, IMain {	// @audit-issue: `Versioned` came before `Initializable`.
```
[22](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L22-L22), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

15:contract StRSRP1Votes is StRSRP1, IERC5805Upgradeable, IStRSRVotes {	// @audit-issue: `StRSRP1` came before `IERC5805Upgradeable`.
```
[15](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L15-L15), 


#### Recommendation

Follow the official [Solidity guidelines](https://docs.soliditylang.org/en/v0.8.17/style-guide.html).

### Returning a struct instead of returning many variables is better
If a function returns [too many variables](https://docs.soliditylang.org/en/latest/contracts.html#returning-multiple-values), replacing them with a struct can improve code readability, maintainability and reusability.

```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

83:    function getFeeDetails(address rToken)
84:        external
85:        view
86:        returns (
87:            address recipient,
88:            uint256 feeNumerator,
89:            uint256 feeDenominator
90:        )	// @audit-issue
```
[90](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L83-L90), 


```solidity
Path: ./contracts/registry/VersionRegistry.sol

72:    function getLatestVersion()
73:        external
74:        view
75:        returns (
76:            bytes32 versionHash,
77:            string memory version,
78:            IDeployer deployer,
79:            bool deprecated
80:        )	// @audit-issue
```
[80](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L72-L80), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

738:    function getPrimeBasket()
739:        external
740:        view
741:        returns (
742:            IERC20[] memory erc20s,
743:            bytes32[] memory targetNames,
744:            uint192[] memory targetAmts
745:        )	// @audit-issue
```
[745](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L738-L745), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

33:    function prepareRecollateralizationTrade(TradingContext memory ctx, Registry memory reg)
34:        external
35:        view
36:        returns (
37:            bool doTrade,
38:            TradeRequest memory req,
39:            TradePrices memory prices
40:        )	// @audit-issue
```
[40](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L33-L40), 


#### Recommendation

Consider returning a struct instead of multiple variables when a function returns many variables. This can enhance code readability, maintainability, and reusability, as well as make the contract interface more user-friendly.

### Consider using a `struct` rather than having many function input parameters
In Solidity, functions with a large number of input parameters can become cumbersome to manage and call. This can lead to readability issues and increased likelihood of errors, especially if the order of parameters is complex or not intuitive. To streamline this, consider consolidating multiple parameters into a single `struct`. This approach not only simplifies function signatures but also enhances code clarity. Structs allow for grouping related data together, making it easier to understand the relationship between parameters and manage them as a single entity.

```solidity
Path: ./contracts/libraries/Permit.sol

10:    function requireSignature(
11:        address owner,
12:        bytes32 hash,
13:        uint8 v,
14:        bytes32 r,
15:        bytes32 s
16:    ) internal view {
```
[33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Permit.sol#L10-L16), 


```solidity
Path: ./contracts/p1/BackingManager.sol

49:    function init(
50:        IMain main_,
51:        uint48 tradingDelay_,
52:        uint192 backingBuffer_,
53:        uint192 maxTradeSlippage_,
54:        uint192 minTradeVolume_
55:    ) external initializer {
```
[62](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L49-L55), 


```solidity
Path: ./contracts/p1/StRSR.sol

175:    function init(
176:        IMain main_,
177:        string calldata name_,
178:        string calldata symbol_,
179:        uint48 unstakingDelay_,
180:        uint192 rewardRatio_,
181:        uint192 withdrawalLeak_
182:    ) external initializer {

922:    function permit(
923:        address owner,
924:        address spender,
925:        uint256 value,
926:        uint256 deadline,
927:        uint8 v,
928:        bytes32 r,
929:        bytes32 s
930:    ) public {
```
[203](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L175-L182), [940](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L922-L930), 


```solidity
Path: ./contracts/p1/Deployer.sol

107:    function deploy(
108:        string memory name,
109:        string memory symbol,
110:        string calldata mandate,
111:        address owner,
112:        DeploymentParams memory params
113:    ) external returns (address) {
```
[264](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L107-L113), 


```solidity
Path: ./contracts/p1/RToken.sol

61:    function init(
62:        IMain main_,
63:        string calldata name_,
64:        string calldata symbol_,
65:        string calldata mandate_,
66:        ThrottleLib.Params calldata issuanceThrottleParams_,
67:        ThrottleLib.Params calldata redemptionThrottleParams_
68:    ) external initializer {

253:    function redeemCustom(
254:        address recipient,
255:        uint256 amount,
256:        uint48[] memory basketNonces,
257:        uint192[] memory portions,
258:        address[] memory expectedERC20sOut,
259:        uint256[] memory minAmounts
260:    ) external notFrozen {
```
[87](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L61-L68), [344](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L253-L260), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

166:    function delegateBySig(
167:        address delegatee,
168:        uint256 nonce,
169:        uint256 expiry,
170:        uint8 v,
171:        bytes32 r,
172:        bytes32 s
173:    ) public {
```
[183](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L166-L173), 


```solidity
Path: ./contracts/p1/Broker.sol

73:    function init(
74:        IMain main_,
75:        IGnosis gnosis_,
76:        ITrade batchTradeImplementation_,
77:        uint48 batchAuctionLength_,
78:        ITrade dutchTradeImplementation_,
79:        uint48 dutchAuctionLength_
80:    ) external initializer {
```
[103](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L73-L80), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

316:    function requireConstantConfigTargets(
317:        IAssetRegistry assetRegistry,
318:        BasketConfig storage config,
319:        EnumerableMap.Bytes32ToUintMap storage _targetAmts,
320:        IERC20[] calldata erc20s,
321:        uint192[] calldata targetAmts
322:    ) external {
```
[345](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L316-L322), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

84:    function init(
85:        IBroker broker_,
86:        address origin_,
87:        IGnosis gnosis_,
88:        uint48 batchAuctionLength,
89:        TradeRequest calldata req
90:    ) external stateTransition(TradeStatus.NOT_STARTED, TradeStatus.OPEN) {
```
[157](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L84-L90), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

156:    function init(
157:        ITrading origin_,
158:        IAsset sell_,
159:        IAsset buy_,
160:        uint256 sellAmount_,
161:        uint48 auctionLength,
162:        TradePrices memory prices
163:    ) external stateTransition(TradeStatus.NOT_STARTED, TradeStatus.OPEN) {
```
[194](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L156-L163), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

38:    constructor(
39:        IStRSRVotes token_,
40:        TimelockController timelock_,
41:        uint256 votingDelay_, // {s}
42:        uint256 votingPeriod_, // {s}
43:        uint256 proposalThresholdAsMicroPercent_, // e.g. 1e4 for 0.01%
44:        uint256 quorumPercent // e.g 4 for 4%
45:    )

136:    function _execute(
137:        uint256 proposalId,
138:        address[] memory targets,
139:        uint256[] memory values,
140:        bytes[] memory calldatas,
141:        bytes32 descriptionHash
142:    ) internal override(Governor, GovernorTimelockControl) {
```
[53](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L38-L45), [145](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L136-L142), 


#### Recommendation

When faced with functions having numerous input parameters, refactor them to accept a `struct` instead. Define a `struct` that encapsulates all these parameters, thereby simplifying the function signature and improving code readability and maintainability. This method is particularly effective in complex functions or those with parameters that are logically related, making the code more intuitive and less error-prone.

### Some variables have a implicit default visibility
Consider always adding an explicit visibility modifier for variables, as the default is `internal`.

```solidity
Path: ./contracts/libraries/Fixed.sol

311:    uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;	// @audit-issue
```
[311](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L311-L311), 


#### Recommendation

Always add an explicit visibility modifier for variables to enhance code clarity and avoid potential issues. The default visibility for variables is `internal`, but specifying it explicitly makes your intentions clear.

### Unnecessary Use of override Keyword
In Solidity version 0.8.8 and later, the use of the override keyword becomes superfluous when a function is overriding solely from an interface and the function isn't present in multiple base contracts. Previously, the override keyword was required as an explicit indication to the compiler. However, this is no longer the case, and the extraneous use of the keyword can make the code less clean and more verbose.
Solidity documentation on [Function Overriding](https://docs.soliditylang.org/en/v0.8.20/contracts.html#function-overriding).


```solidity
Path: ./contracts/mixins/Versioned.sol

14:    function version() public pure virtual override returns (string memory) {	// @audit-issue
```
[14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Versioned.sol#L14-L14), 


```solidity
Path: ./contracts/mixins/Auth.sol

87:    function grantRole(bytes32 role, address account)	// @audit-issue
88:        public
89:        override(AccessControlUpgradeable, IAccessControlUpgradeable)
90:        onlyRole(getRoleAdmin(role))
91:    {
```
[87](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L87-L91), 


```solidity
Path: ./contracts/p1/Main.sol

86:    function hasRole(bytes32 role, address account)	// @audit-issue
87:        public
88:        view
89:        override(IAccessControlUpgradeable, AccessControlUpgradeable)
90:        returns (bool)
91:    {

153:    function _authorizeUpgrade(address) internal view override {	// @audit-issue
```
[86](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L86-L91), [153](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L153-L153), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

54:    function settleTrade(IERC20 sell) public override(ITrading, TradingP1) returns (ITrade trade) {	// @audit-issue
```
[54](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L54-L54), 


```solidity
Path: ./contracts/p1/BackingManager.sol

85:    function settleTrade(IERC20 sell) public override(ITrading, TradingP1) returns (ITrade trade) {	// @audit-issue
```
[85](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L85-L85), 


```solidity
Path: ./contracts/p1/StRSR.sol

762:    function allowance(address owner, address spender)	// @audit-issue
763:        public
764:        view
765:        virtual
766:        override
767:        returns (uint256)
768:    {
```
[762](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L762-L768), 


```solidity
Path: ./contracts/p1/Deployer.sol

74:    function implementations() external view override returns (Implementations memory) {	// @audit-issue
```
[74](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L74-L74), 


```solidity
Path: ./contracts/p1/RToken.sol

530:    function _beforeTokenTransfer(
531:        address,
532:        address to,
533:        uint256
534:    ) internal virtual override {	// @audit-issue
```
[534](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L530-L534), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

52:    function beginEra() internal override {	// @audit-issue

201:    function _mint(address account, uint256 amount) internal override {	// @audit-issue

206:    function _burn(address account, uint256 amount) internal override {	// @audit-issue

211:    function _afterTokenTransfer(
212:        address from,
213:        address to,
214:        uint256 amount
215:    ) internal override {	// @audit-issue
```
[52](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L52-L52), [201](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L201-L201), [206](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L206-L206), [215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L211-L215), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

67:    function _authorizeUpgrade(address newImplementation) internal view override onlyMain {}	// @audit-issue
```
[67](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L67-L67), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

55:    function votingDelay() public view override(IGovernor, GovernorSettings) returns (uint256) {	// @audit-issue

59:    function votingPeriod() public view override(IGovernor, GovernorSettings) returns (uint256) {	// @audit-issue

63:    function setVotingDelay(uint256 newVotingDelay) public override {	// @audit-issue

69:    function proposalThreshold()	// @audit-issue
70:        public
71:        view
72:        override(Governor, GovernorSettings)
73:        returns (uint256)
74:    {

85:    function quorum(uint256 timepoint)	// @audit-issue
86:        public
87:        view
88:        virtual
89:        override(IGovernor, GovernorVotesQuorumFraction)
90:        returns (uint256)
91:    {

95:    function state(uint256 proposalId)	// @audit-issue
96:        public
97:        view
98:        override(Governor, GovernorTimelockControl)
99:        returns (ProposalState)
100:    {

104:    function propose(
105:        address[] memory targets,
106:        uint256[] memory values,
107:        bytes[] memory calldatas,
108:        string memory description
109:    ) public override(Governor, IGovernor) returns (uint256 proposalId) {	// @audit-issue

114:    function queue(
115:        address[] memory targets,
116:        uint256[] memory values,
117:        bytes[] memory calldatas,
118:        bytes32 descriptionHash
119:    ) public override returns (uint256 proposalId) {	// @audit-issue

124:    function cancel(
125:        address[] memory targets,
126:        uint256[] memory values,
127:        bytes[] memory calldatas,
128:        bytes32 descriptionHash
129:    ) public override(Governor, IGovernor) returns (uint256) {	// @audit-issue

136:    function _execute(
137:        uint256 proposalId,
138:        address[] memory targets,
139:        uint256[] memory values,
140:        bytes[] memory calldatas,
141:        bytes32 descriptionHash
142:    ) internal override(Governor, GovernorTimelockControl) {	// @audit-issue

147:    function _cancel(
148:        address[] memory targets,
149:        uint256[] memory values,
150:        bytes[] memory calldatas,
151:        bytes32 descriptionHash
152:    ) internal override(Governor, GovernorTimelockControl) returns (uint256) {	// @audit-issue

156:    function _executor()	// @audit-issue
157:        internal
158:        view
159:        override(Governor, GovernorTimelockControl)
160:        returns (address)
161:    {

166:    function _getVotes(
167:        address account,
168:        uint256 timepoint,
169:        bytes memory /*params*/
170:    ) internal view override(Governor, GovernorVotes) returns (uint256) {	// @audit-issue

174:    function supportsInterface(bytes4 interfaceId)	// @audit-issue
175:        public
176:        view
177:        override(Governor, GovernorTimelockControl)
178:        returns (bool)
179:    {

196:    function clock() public view override(GovernorVotes, IGovernor) returns (uint48) {	// @audit-issue

201:    function CLOCK_MODE() public pure override(GovernorVotes, IGovernor) returns (string memory) {	// @audit-issue
```
[55](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L55-L55), [59](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L59-L59), [63](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L63-L63), [69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L69-L74), [85](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L85-L91), [95](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L95-L100), [109](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L104-L109), [119](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L114-L119), [129](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L124-L129), [142](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L136-L142), [152](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L147-L152), [156](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L156-L161), [170](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L166-L170), [174](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L174-L179), [196](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L196-L196), [201](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L201-L201), 


#### Recommendation

In Solidity versions 0.8.8 and later, the `override` keyword is no longer required for functions that are solely overriding from an interface and not present in multiple base contracts. Removing the unnecessary `override` keyword can make the code cleaner and less verbose.

### Use a single file for all system-wide constants
System-wide constants should be declared in a single file for better maintainability and readability. This contract seems to contain constants which could potentially be system-wide and could be better managed if they were centralized in a single location.

```solidity
Path: ./contracts/registry/RoleRegistry.sol

12:    bytes32 public constant EMERGENCY_COUNCIL = keccak256("EMERGENCY_COUNCIL");	// @audit-issue
```
[12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/RoleRegistry.sol#L12-L12), 


```solidity
Path: ./contracts/mixins/Auth.sol

29:    bytes32 public constant OWNER_ROLE = OWNER;	// @audit-issue

30:    bytes32 public constant SHORT_FREEZER_ROLE = SHORT_FREEZER;	// @audit-issue

31:    bytes32 public constant LONG_FREEZER_ROLE = LONG_FREEZER;	// @audit-issue

32:    bytes32 public constant PAUSER_ROLE = PAUSER;	// @audit-issue
```
[29](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L29-L29), [30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L30-L30), [31](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L31-L31), [32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L32-L32), 


```solidity
Path: ./contracts/libraries/Fixed.sol

311:    uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;	// @audit-issue
```
[311](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L311-L311), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

22:    uint192 public constant MAX_TRADE_VOLUME = 1e29; // {UoA}	// @audit-issue

23:    uint192 public constant MAX_TRADE_SLIPPAGE = 1e18; // {%}	// @audit-issue
```
[22](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L22-L22), [23](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L23-L23), 


```solidity
Path: ./contracts/p1/Distributor.sol

32:    address public constant FURNACE = address(1);	// @audit-issue

33:    address public constant ST_RSR = address(2);	// @audit-issue

35:    uint8 public constant MAX_DESTINATIONS_ALLOWED = MAX_DESTINATIONS; // 100	// @audit-issue
```
[32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L32-L32), [33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L33-L33), [35](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L35-L35), 


```solidity
Path: ./contracts/p1/Furnace.sol

15:    uint192 public constant MAX_RATIO = 1e14; // {1} 0.01%	// @audit-issue
```
[15](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L15-L15), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

15:    uint256 public constant GAS_FOR_BH_QTY = 100_000; // enough to call bh.quantity	// @audit-issue

16:    uint256 public constant GAS_FOR_DISABLE_BASKET = 900_000; // enough to disable basket on n=128	// @audit-issue
```
[15](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L15-L15), [16](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L16-L16), 


```solidity
Path: ./contracts/p1/BackingManager.sol

33:    uint48 public constant MAX_TRADING_DELAY = 60 * 60 * 24 * 365; // {s} 1 year	// @audit-issue

34:    uint192 public constant MAX_BACKING_BUFFER = FIX_ONE; // {1} 100%	// @audit-issue
```
[33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L33-L33), [34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L34-L34), 


```solidity
Path: ./contracts/p1/StRSR.sol

40:    uint48 private constant MIN_UNSTAKING_DELAY = 60 * 2; // {s} 2 minutes	// @audit-issue

41:    uint48 private constant MAX_UNSTAKING_DELAY = 60 * 60 * 24 * 365; // {s} 1 year	// @audit-issue

42:    uint192 private constant MAX_REWARD_RATIO = 1e14; // {1} 0.01%	// @audit-issue

48:    uint8 public constant decimals = 18;	// @audit-issue

68:    uint192 private constant MAX_STAKE_RATE = 1e9 * FIX_ONE; // 1e9 D18{qStRSR/qRSR}	// @audit-issue

90:    uint192 private constant MAX_DRAFT_RATE = 1e9 * FIX_ONE; // 1e9 D18{qDrafts/qRSR}	// @audit-issue

131:    bytes32 private constant _PERMIT_TYPEHASH =	// @audit-issue

157:    uint192 private constant MAX_WITHDRAWAL_LEAK = 3e17; // {1} 30%	// @audit-issue

164:    uint192 private constant MAX_SAFE_STAKE_RATE = 1e6 * FIX_ONE; // 1e6   D18{qStRSR/qRSR}	// @audit-issue

165:    uint192 private constant MIN_SAFE_STAKE_RATE = uint192(1e12); // 1e-6  D18{qStRSR/qRSR}	// @audit-issue
```
[40](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L40-L40), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L41-L41), [42](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L42-L42), [48](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L48-L48), [68](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L68-L68), [90](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L90-L90), [131](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L131-L131), [157](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L157-L157), [164](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L164-L164), [165](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L165-L165), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

31:    uint192 public constant MAX_TARGET_AMT = 1e3 * FIX_ONE; // {target/BU} max basket weight	// @audit-issue

32:    uint48 public constant MIN_WARMUP_PERIOD = 60; // {s} 1 minute	// @audit-issue

33:    uint48 public constant MAX_WARMUP_PERIOD = 60 * 60 * 24 * 365; // {s} 1 year	// @audit-issue

34:    uint256 internal constant MAX_BACKUP_ERC20S = 64;	// @audit-issue
```
[31](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L31-L31), [32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L32-L32), [33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L33-L33), [34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L34-L34), 


```solidity
Path: ./contracts/p1/Deployer.sol

31:    string public constant ENS = "reserveprotocol.eth";	// @audit-issue
```
[31](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L31-L31), 


```solidity
Path: ./contracts/p1/RToken.sol

22:    uint256 public constant MIN_THROTTLE_RATE_AMT = 1e18; // {qRTok}	// @audit-issue

23:    uint256 public constant MAX_THROTTLE_RATE_AMT = 1e48; // {qRTok}	// @audit-issue

24:    uint192 public constant MAX_THROTTLE_PCT_AMT = 1e18; // {qRTok}	// @audit-issue

25:    uint192 public constant MIN_EXCHANGE_RATE = 1e9; // D18{BU/rTok}	// @audit-issue

26:    uint192 public constant MAX_EXCHANGE_RATE = 1e27; // D18{BU/rTok}	// @audit-issue
```
[22](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L22-L22), [23](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L23-L23), [24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L24-L24), [25](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L25-L25), [26](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L26-L26), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

30:    bytes32 private constant _DELEGATE_TYPEHASH =	// @audit-issue
```
[30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L30-L30), 


```solidity
Path: ./contracts/p1/Broker.sol

25:    uint48 public constant MAX_AUCTION_LENGTH = 60 * 60 * 24 * 7; // {s} max valid duration, 1 week	// @audit-issue

28:    uint48 public constant MIN_AUCTION_LENGTH = 60; // {s} 60 seconds auction min duration	// @audit-issue
```
[25](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L25-L25), [28](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L28-L28), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

24:    TradeKind public constant KIND = TradeKind.BATCH_AUCTION;	// @audit-issue

25:    uint256 public constant FEE_DENOMINATOR = 1000;	// @audit-issue

29:    uint96 public constant MAX_ORDERS = 5000; // bounded to avoid going beyond block gas limit	// @audit-issue

32:    uint192 public constant DEFAULT_MIN_BID = FIX_ONE / 100; // {tok}	// @audit-issue
```
[24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L24-L24), [25](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L25-L25), [29](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L29-L29), [32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L32-L32), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

95:    TradeKind public constant KIND = TradeKind.DUTCH_AUCTION;	// @audit-issue
```
[95](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L95-L95), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

33:    uint256 public constant ONE_HUNDRED_PERCENT = 1e8; // {micro %}	// @audit-issue

36:    uint256 public constant MIN_VOTING_DELAY = 86400; // {s} ONE_DAY	// @audit-issue
```
[33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L33-L33), [36](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L36-L36), 


#### Recommendation

Consider centralizing system-wide constants in a single file for better maintainability and readability. This practice makes it easier to manage and update constants across the contract and promotes consistency in your codebase.

### Missing events in sensitive functions
Events should be emitted when sensitive changes are made to the contracts, but some functions lack them.

```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

107:    function deprecateAsset(address _asset) external {	// @audit-issue
```
[107](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L107-L107), 


```solidity
Path: ./contracts/mixins/Auth.sol

87:    function grantRole(bytes32 role, address account)	// @audit-issue
```
[87](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L87-L87), 


```solidity
Path: ./contracts/p1/Main.sol

61:    function setVersionRegistry(VersionRegistry versionRegistry_) external onlyRole(OWNER) {	// @audit-issue

70:    function setAssetPluginRegistry(AssetPluginRegistry registry_) external onlyRole(OWNER) {	// @audit-issue

79:    function setDAOFeeRegistry(DAOFeeRegistry feeRegistry_) external onlyRole(OWNER) {	// @audit-issue
```
[61](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L61-L61), [70](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L70-L70), [79](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L79-L79), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

41:    function cacheComponents() public {	// @audit-issue
```
[41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L41-L41), 


```solidity
Path: ./contracts/p1/Distributor.sol

270:    function cacheComponents() public {	// @audit-issue
```
[270](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L270-L270), 


```solidity
Path: ./contracts/p1/Furnace.sol

65:    function melt() public {	// @audit-issue
```
[65](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L65-L65), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

58:    function refresh() public {	// @audit-issue
```
[58](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L58-L58), 


```solidity
Path: ./contracts/p1/BackingManager.sol

107:    function rebalance(TradeKind kind) external nonReentrant {	// @audit-issue

329:    function cacheComponents() public {	// @audit-issue
```
[107](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L107-L107), [329](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L329-L329), 


```solidity
Path: ./contracts/p1/Broker.sol

106:    function cacheComponents() public {	// @audit-issue
```
[106](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L106-L106), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

199:    function bid() external returns (uint256 amountIn) {	// @audit-issue

235:    function bidWithCallback(bytes calldata data) external returns (uint256 amountIn) {	// @audit-issue
```
[199](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L199-L199), [235](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L235-L235), 


#### Recommendation

To enhance transparency and auditability, ensure that events are emitted when sensitive changes are made to the contracts. Review and update functions that lack event emissions, especially in cases where sensitive operations or state changes occur, to provide a clear record of such actions.

### Include sender information in events
When an action is triggered based on a user's action, not being able to filter based on who triggered the action makes event processing a lot more cumbersome. Including the `msg.sender` the events of these types of action will make events much more useful to end users, especially when `msg.sender` is not `tx.origin`.

```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

53:        emit FeeRecipientSet(feeRecipient_);	// @audit-issue

62:        emit DefaultFeeNumeratorSet(defaultFeeNumerator);	// @audit-issue

73:        emit RTokenFeeNumeratorSet(rToken, feeNumerator_, true);	// @audit-issue

80:        emit RTokenFeeNumeratorSet(rToken, 0, false);	// @audit-issue
```
[53](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L53-L53), [62](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L62-L62), [73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L73-L73), [80](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L80-L80), 


```solidity
Path: ./contracts/registry/VersionRegistry.sol

56:        emit VersionRegistered(versionHash, deployer);	// @audit-issue

69:        emit VersionDeprecated(versionHash);	// @audit-issue
```
[56](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L56-L56), [69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L69-L69), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

47:            emit AssetPluginRegistryUpdated(versionHash, _asset, true);	// @audit-issue

75:            emit AssetPluginRegistryUpdated(versionHash, _asset, _validities[i]);	// @audit-issue

103:            emit AssetPluginRegistryUpdated(_versionHash, asset, _validities[i]);	// @audit-issue
```
[47](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L47-L47), [75](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L75-L75), [103](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L103-L103), 


```solidity
Path: ./contracts/mixins/Auth.sol

162:        emit UnfreezeAtSet(unfreezeAt, uint48(block.timestamp));	// @audit-issue

170:        emit TradingPausedSet(tradingPaused, true);	// @audit-issue

177:        emit TradingPausedSet(tradingPaused, false);	// @audit-issue

184:        emit IssuancePausedSet(issuancePaused, true);	// @audit-issue

191:        emit IssuancePausedSet(issuancePaused, false);	// @audit-issue

200:        emit ShortFreezeDurationSet(shortFreeze, shortFreeze_);	// @audit-issue

207:        emit LongFreezeDurationSet(longFreeze, longFreeze_);	// @audit-issue

216:        emit UnfreezeAtSet(unfreezeAt, newUnfreezeAt);	// @audit-issue
```
[162](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L162-L162), [170](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L170-L170), [177](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L177-L177), [184](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L184-L184), [191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L191-L191), [200](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L200-L200), [207](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L207-L207), [216](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L216-L216), 


```solidity
Path: ./contracts/mixins/ComponentRegistry.sol

37:        emit RTokenSet(rToken, val);	// @audit-issue

45:        emit StRSRSet(stRSR, val);	// @audit-issue

53:        emit AssetRegistrySet(assetRegistry, val);	// @audit-issue

61:        emit BasketHandlerSet(basketHandler, val);	// @audit-issue

69:        emit BackingManagerSet(backingManager, val);	// @audit-issue

77:        emit DistributorSet(distributor, val);	// @audit-issue

85:        emit RSRTraderSet(rsrTrader, val);	// @audit-issue

93:        emit RTokenTraderSet(rTokenTrader, val);	// @audit-issue

101:        emit FurnaceSet(furnace, val);	// @audit-issue

109:        emit BrokerSet(broker, val);	// @audit-issue
```
[37](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L37-L37), [45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L45-L45), [53](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L53-L53), [61](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L61-L61), [69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L69-L69), [77](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L77-L77), [85](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L85-L85), [93](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L93-L93), [101](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L101-L101), [109](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L109-L109), 


```solidity
Path: ./contracts/p1/Main.sol

45:        emit MainInitialized();	// @audit-issue
```
[45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L45-L45), 


```solidity
Path: ./contracts/p1/Distributor.sol

166:        emit RevenueDistributed(erc20, caller, amount);	// @audit-issue

260:        emit DistributionSet(dest, share.rTokenDist, share.rsrDist);	// @audit-issue
```
[166](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L166-L166), [260](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L260-L260), 


```solidity
Path: ./contracts/p1/Furnace.sol

88:        emit RatioSet(ratio, ratio_);	// @audit-issue
```
[88](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L88-L88), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

120:        emit AssetUnregistered(asset.erc20(), asset);	// @audit-issue

235:            else emit AssetUnregistered(erc20, assets[erc20]);	// @audit-issue

241:        emit AssetRegistered(erc20, asset);	// @audit-issue
```
[120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L120-L120), [235](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L235-L235), [241](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L241-L241), 


```solidity
Path: ./contracts/p1/BackingManager.sol

317:        emit TradingDelaySet(tradingDelay, val);	// @audit-issue

324:        emit BackingBufferSet(backingBuffer, val);	// @audit-issue
```
[317](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L317-L317), [324](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L324-L324), 


```solidity
Path: ./contracts/p1/StRSR.sol

280:        emit UnstakingStarted(index, draftEra, account, rsrAmount, stakeAmount, availableAt);	// @audit-issue

338:        emit UnstakingCompleted(firstId, endId, draftEra, account, rsrAmount);	// @audit-issue

384:        emit UnstakingCancelled(firstId, endId, draftEra, account, rsrAmount);	// @audit-issue

476:        emit ExchangeRateSet(initRate, exchangeRate());	// @audit-issue

625:        emit RewardsPaid(payout);	// @audit-issue

626:        emit ExchangeRateSet(initRate, exchangeRate());	// @audit-issue

677:        emit AllBalancesReset(era);	// @audit-issue

689:        emit AllUnstakingReset(draftEra);	// @audit-issue

735:        emit Staked(era, account, rsrAmount, stakeAmount);	// @audit-issue

835:        emit Transfer(from, to, amount);	// @audit-issue

849:        emit Transfer(address(0), account, amount);	// @audit-issue

871:        emit Transfer(account, address(0), amount);	// @audit-issue

884:        emit Approval(owner, spender, amount);	// @audit-issue

973:        emit UnstakingDelaySet(unstakingDelay, val);	// @audit-issue

982:        emit RewardRatioSet(rewardRatio, val);	// @audit-issue

990:        emit WithdrawalLeakSet(withdrawalLeak, val);	// @audit-issue
```
[280](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L280-L280), [338](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L338-L338), [384](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L384-L384), [476](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L476-L476), [625](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L625-L625), [626](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L626-L626), [677](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L677-L677), [689](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L689-L689), [735](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L735-L735), [835](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L835-L835), [849](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L849-L849), [871](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L871-L871), [884](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L884-L884), [973](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L973-L973), [982](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L982-L982), [990](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L990-L990), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

143:        emit BasketSet(nonce, basket.erc20s, refAmts, true);	// @audit-issue

181:            emit BasketStatusChanged(lastStatus, currentStatus);	// @audit-issue

188:            emit LastCollateralizedChanged(lastCollateralized, nonce);	// @audit-issue

272:        emit PrimeBasketSet(erc20s, targetAmts, names);	// @audit-issue

302:        emit BackupConfigSet(targetName, max, erc20s);	// @audit-issue

636:        emit WarmupPeriodSet(warmupPeriod, val);	// @audit-issue

643:        emit EnableIssuancePremiumSet(enableIssuancePremium, val);	// @audit-issue

679:        emit BasketSet(nonce, basket.erc20s, refAmts, disabled);	// @audit-issue
```
[143](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L143-L143), [181](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L181-L181), [188](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L188-L188), [272](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L272-L272), [302](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L302-L302), [636](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L636-L636), [643](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L643-L643), [679](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L679-L679), 


```solidity
Path: ./contracts/p1/Deployer.sol

262:        emit RTokenCreated(main, components.rToken, components.stRSR, owner, version());	// @audit-issue

274:        emit RTokenAssetCreated(rToken, rTokenAsset);	// @audit-issue
```
[262](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L262-L262), [274](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L274-L274), 


```solidity
Path: ./contracts/p1/RToken.sol

136:        emit Issuance(issuer, recipient, amount, amtBaskets);	// @audit-issue

204:        emit Redemption(caller, recipient, amount, baskets);	// @audit-issue

374:        emit Melted(amtRToken);	// @audit-issue

399:        emit BasketsNeededChanged(basketsNeeded, basketsNeeded_);	// @audit-issue

457:        emit IssuanceThrottleSet(issuanceThrottle.params, params);	// @audit-issue

468:        emit RedemptionThrottleSet(redemptionThrottle.params, params);	// @audit-issue

492:        emit BasketsNeededChanged(basketsNeeded, basketsNeeded + amtBaskets);	// @audit-issue

511:        emit BasketsNeededChanged(basketsNeeded, basketsNeeded - amtBaskets);	// @audit-issue
```
[136](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L136-L136), [204](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L204-L204), [374](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L374-L374), [399](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L399-L399), [457](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L457-L457), [468](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L468-L468), [492](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L492-L492), [511](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L511-L511), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

225:        emit DelegateChanged(delegator, currentDelegate, delegatee);	// @audit-issue

242:                emit DelegateVotesChanged(src, oldWeight, newWeight);	// @audit-issue

251:                emit DelegateVotesChanged(dst, oldWeight, newWeight);	// @audit-issue
```
[225](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L225-L225), [242](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L242-L242), [251](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L251-L251), 


```solidity
Path: ./contracts/p1/Broker.sol

98:        emit BatchTradeImplementationSet(ITrade(address(0)), batchTradeImplementation_);	// @audit-issue

99:        emit DutchTradeImplementationSet(ITrade(address(0)), dutchTradeImplementation_);	// @audit-issue

156:            emit BatchTradeDisabledSet(batchTradeDisabled, true);	// @audit-issue

162:                emit DutchTradeDisabledSet(sell, dutchTradeDisabled[sell], true);	// @audit-issue

166:                emit DutchTradeDisabledSet(buy, dutchTradeDisabled[buy], true);	// @audit-issue

181:        emit GnosisSet(gnosis, newGnosis);	// @audit-issue

192:        emit BatchTradeImplementationSet(batchTradeImplementation, newTradeImplementation);	// @audit-issue

203:        emit BatchAuctionLengthSet(batchAuctionLength, newAuctionLength);	// @audit-issue

214:        emit DutchTradeImplementationSet(dutchTradeImplementation, newTradeImplementation);	// @audit-issue

225:        emit DutchAuctionLengthSet(dutchAuctionLength, newAuctionLength);	// @audit-issue

231:        emit BatchTradeDisabledSet(batchTradeDisabled, false);	// @audit-issue

237:        emit DutchTradeDisabledSet(erc20, dutchTradeDisabled[erc20], false);	// @audit-issue
```
[98](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L98-L98), [99](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L99-L99), [156](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L156-L156), [162](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L162-L162), [166](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L166-L166), [181](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L181-L181), [192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L192-L192), [203](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L203-L203), [214](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L214-L214), [225](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L225-L225), [231](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L231-L231), [237](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L237-L237), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

104:        emit TradeSettled(trade, sell, trade.buy(), soldAmt, boughtAmt);	// @audit-issue

140:        emit TradeStarted(trade, sell, req.buy.erc20(), req.sellAmount, req.minBuyAmount);	// @audit-issue

148:        emit MaxTradeSlippageSet(maxTradeSlippage, val);	// @audit-issue

155:        emit MinTradeVolumeSet(minTradeVolume, val);	// @audit-issue
```
[104](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L104-L104), [140](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L140-L140), [148](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L148-L148), [155](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L155-L155), 


#### Recommendation

To improve the usability and analysis of your smart contract events, consider including the `msg.sender` address as part of the event data. This enables easier filtering and identification of the sender's actions within your contract, providing valuable insights for users and external tools.

### Don't use `_msgSender()` if not supporting EIP-2771
Use `msg.sender` if the code does not implement [EIP-2771 trusted forwarder](https://eips.ethereum.org/EIPS/eip-2771) support.

```solidity
Path: ./contracts/mixins/Auth.sol

77:        _grantRole(OWNER, _msgSender());	// @audit-issue

126:        _revokeRole(SHORT_FREEZER, _msgSender());	// @audit-issue

140:        longFreezes[_msgSender()] -= 1; // reverts on underflow	// @audit-issue

143:        if (longFreezes[_msgSender()] == 0) _revokeRole(LONG_FREEZER, _msgSender());	// @audit-issue
```
[77](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L77-L77), [126](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L126-L126), [140](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L140-L140), [143](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L143-L143), 


```solidity
Path: ./contracts/p1/Distributor.sol

123:        address caller = _msgSender();	// @audit-issue
```
[123](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L123-L123), 


```solidity
Path: ./contracts/p1/BackingManager.sol

90:        if (_msgSender() == address(trade)) {	// @audit-issue

116:            _msgSender() == address(this) || tradeEnd[kind] < block.timestamp,	// @audit-issue
```
[90](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L90-L90), [116](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L116-L116), 


```solidity
Path: ./contracts/p1/StRSR.sol

233:        address caller = _msgSender();	// @audit-issue

263:        address account = _msgSender();	// @audit-issue

348:        address account = _msgSender();	// @audit-issue

428:        address caller = _msgSender();	// @audit-issue

773:        _transfer(_msgSender(), to, amount);	// @audit-issue

782:        _approve(_msgSender(), spender, amount);	// @audit-issue

795:        _spendAllowance(from, _msgSender(), amount);	// @audit-issue

801:        address owner = _msgSender();	// @audit-issue

807:        address owner = _msgSender();	// @audit-issue
```
[233](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L233-L233), [263](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L263-L263), [348](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L348-L348), [428](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L428-L428), [773](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L773-L773), [782](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L782-L782), [795](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L795-L795), [801](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L801-L801), [807](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L807-L807), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

138:        require(_msgSender() == address(assetRegistry), "asset registry only");	// @audit-issue

162:            main.hasRole(OWNER, _msgSender()) ||	// @audit-issue
```
[138](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L138-L138), [162](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L162-L162), 


```solidity
Path: ./contracts/p1/RToken.sol

95:        issueTo(_msgSender(), amount);	// @audit-issue

114:        address issuer = _msgSender(); // OK to save: it can't be changed in reentrant runs	// @audit-issue

161:        redeemTo(_msgSender(), amount);	// @audit-issue

189:        address caller = _msgSender();	// @audit-issue

267:        require(amount <= balanceOf(_msgSender()), "insufficient balance");	// @audit-issue

281:        uint192 baskets = _scaleDown(_msgSender(), amount);	// @audit-issue

282:        emit Redemption(_msgSender(), recipient, amount, baskets);	// @audit-issue

357:        require(_msgSender() == address(backingManager), "not backing manager");	// @audit-issue

371:        address caller = _msgSender();	// @audit-issue

388:        address caller = _msgSender();	// @audit-issue

398:        require(_msgSender() == address(backingManager), "not backing manager");	// @audit-issue
```
[95](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L95-L95), [114](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L114-L114), [161](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L161-L161), [189](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L189-L189), [267](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L267-L267), [281](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L281-L281), [282](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L282-L282), [357](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L357-L357), [371](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L371-L371), [388](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L388-L388), [398](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L398-L398), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

163:        _delegate(_msgSender(), delegatee);	// @audit-issue

189:        address caller = _msgSender();	// @audit-issue
```
[163](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L163-L163), [189](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L189-L189), 


```solidity
Path: ./contracts/p1/Broker.sol

130:        address caller = _msgSender();	// @audit-issue

151:        require(trades[_msgSender()], "unrecognized trade contract");	// @audit-issue

152:        ITrade trade = ITrade(_msgSender());	// @audit-issue
```
[130](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L130-L130), [151](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L151-L151), [152](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L152-L152), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

57:        require(main.hasRole(OWNER, _msgSender()), "governance only");	// @audit-issue

62:        require(_msgSender() == address(main), "main only");	// @audit-issue
```
[57](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L57-L57), [62](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L62-L62), 


#### Recommendation

Consider refactoring your code by moving `msg.sender` checks to modifiers when certain functions are only allowed to be called by specific users. This approach can enhance code readability, reduce redundancy, and make it easier to maintain access control logic.

### Avoid external calls in modifiers
It is unusual to have external calls in modifiers, and doing so will make reviewers more likely to miss important external interactions. Consider moving the external call to an internal function, and calling that function from the modifier.

```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

29:        if (!roleRegistry.isOwner(msg.sender)) {	// @audit-issue
```
[29](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L29-L29), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

42:        require(!main.tradingPausedOrFrozen(), "frozen or trading paused");	// @audit-issue

47:        require(!main.issuancePausedOrFrozen(), "frozen or issuance paused");	// @audit-issue

52:        require(!main.frozen(), "frozen");	// @audit-issue

57:        require(main.hasRole(OWNER, _msgSender()), "governance only");	// @audit-issue
```
[42](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L42-L42), [47](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L47-L47), [52](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L52-L52), [57](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L57-L57), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

60:        status = TradeStatus.PENDING;	// @audit-issue

62:        assert(status == TradeStatus.PENDING);	// @audit-issue
```
[60](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L60-L60), [62](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L62-L62), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

123:        status = TradeStatus.PENDING;	// @audit-issue

125:        assert(status == TradeStatus.PENDING);	// @audit-issue
```
[123](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L123-L123), [125](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L125-L125), 


#### Recommendation

Refrain from incorporating external calls directly within modifiers. Instead, encapsulate the external call within an internal function and invoke this function from within the body of the functions that use the modifier. This approach enhances code readability and security, making it easier for reviewers and auditors to track external interactions. Additionally, it centralizes external calls, simplifying the management and review of these potentially risky operations. Always ensure external calls are handled with care, implementing checks, balances, and reentrancy guards as necessary to protect your contract from malicious actors and unintended consequences.

### Interfaces should be defined in separate files from their usage
This issue arises when the interfaces are defined in the same files where they are used. They should be separated into different files for better readability and reusability.

In Solidity, interfaces are used to interact with contract abstractions. They define the functions and events that other contracts can call or listen to. While it is possible to define an interface within the same file where it is used, it is considered a good practice to separate the interface definition into its own file.

```solidity
Path: ./contracts/libraries/Allowance.sol

4:interface IERC20ApproveOnly {	// @audit-issue used in lines: [22]
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L4-L4), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

12:interface IDutchTradeCallee {	// @audit-issue used in lines: [258]
```
[12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L12-L12), 


#### Recommendation

To improve code organization and maintainability, it's recommended to define interfaces in separate files from their usage. This separation of concerns makes your codebase more modular and easier to understand. Create dedicated files for interface definitions, and then import and use these interfaces in the contracts that require them.

### Unnecessary Constant Variable in Function Parameters
Passing a constant variable as a function parameter is redundant because the function can access the constant directly.

```solidity
Path: ./contracts/mixins/Auth.sol

153:        freezeUntil(MAX_UNFREEZE_AT);	// @audit-issue
```
[153](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L153-L153), 


```solidity
Path: ./contracts/libraries/Fixed.sol

184:        return toUint(x, FLOOR);	// @audit-issue

199:        return shiftl(x, decimals, FLOOR);	// @audit-issue

253:        return mul(x, y, ROUND);	// @audit-issue

278:        return div(x, y, FLOOR);	// @audit-issue

297:        return divu(x, y, FLOOR);	// @audit-issue

381:        return shiftl_toUint(x, decimals, FLOOR);	// @audit-issue

448:        return muluDivu(x, y, z, FLOOR);	// @audit-issue

473:        return mulDiv(x, y, z, FLOOR);	// @audit-issue
```
[184](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L184-L184), [199](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L199-L199), [253](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L253-L253), [278](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L278-L278), [297](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L297-L297), [381](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L381-L381), [448](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L448-L448), [473](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L473-L473), 


```solidity
Path: ./contracts/p1/Distributor.sol

49:        _setDistribution(FURNACE, RevenueShare(dist.rTokenDist, 0));	// @audit-issue

50:        _setDistribution(ST_RSR, RevenueShare(0, dist.rsrDist));	// @audit-issue
```
[49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L49-L49), [50](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L50-L50), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

345:            return _quantity(erc20, coll, CEIL);	// @audit-issue

361:        return _quantity(erc20, ICollateral(address(asset)), CEIL);	// @audit-issue

426:                uint192 qty = _quantity(basket.erc20s[i], coll, CEIL);	// @audit-issue

620:            uint192 q = _quantity(basket.erc20s[i], coll, CEIL);	// @audit-issue
```
[345](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L345-L345), [361](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L361-L361), [426](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L426-L426), [620](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L620-L620), 


#### Recommendation


        Reference constant variables directly within the function body instead of passing them as parameters. This simplifies the function signature and conserves gas.


### Control structures do not follow the Solidity Style Guide
Refer to the [Solidity style guide - Control Structures](https://docs.soliditylang.org/en/v0.8.20/style-guide.html#control-structures).

```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

83:    function getFeeDetails(address rToken)
84:        external
85:        view
86:        returns (
87:            address recipient,
88:            uint256 feeNumerator,
89:            uint256 feeDenominator
90:        )	// @audit-issue
```
[90](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L83-L90), 


```solidity
Path: ./contracts/registry/VersionRegistry.sol

72:    function getLatestVersion()
73:        external
74:        view
75:        returns (
76:            bytes32 versionHash,
77:            string memory version,
78:            IDeployer deployer,
79:            bool deprecated
80:        )	// @audit-issue

88:    function getImplementationForVersion(bytes32 versionHash)
89:        external
90:        view
91:        returns (Implementations memory)	// @audit-issue
```
[80](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L72-L80), [91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L88-L91), 


```solidity
Path: ./contracts/mixins/Auth.sol

87:    function grantRole(bytes32 role, address account)	// @audit-issue
```
[87](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L87-L87), 


```solidity
Path: ./contracts/libraries/Throttle.sol

69:    function currentlyAvailable(Throttle storage throttle, uint256 limit)
70:        internal
71:        view
72:        returns (uint256 available)	// @audit-issue

80:    function hourlyLimit(Throttle storage throttle, uint256 supply)
81:        internal
82:        view
83:        returns (uint256 limit)	// @audit-issue
```
[72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L69-L72), [83](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L80-L83), 


```solidity
Path: ./contracts/p1/Main.sol

86:    function hasRole(bytes32 role, address account)
87:        public
88:        view
89:        override(IAccessControlUpgradeable, AccessControlUpgradeable)
90:        returns (bool)	// @audit-issue
```
[90](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L86-L90), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

109:    function manageTokens(IERC20[] calldata erc20s, TradeKind[] calldata kinds)	// @audit-issue
```
[109](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L109-L109), 


```solidity
Path: ./contracts/p1/Distributor.sol

81:    function setDistributions(address[] calldata dests, RevenueShare[] calldata shares)	// @audit-issue
```
[81](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L81-L81), 


```solidity
Path: ./contracts/p1/BackingManager.sol

273:    function tradingContext(BasketRange memory basketsHeld)
274:        public
275:        view
276:        returns (TradingContext memory ctx, Registry memory reg)	// @audit-issue
```
[276](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L273-L276), 


```solidity
Path: ./contracts/p1/StRSR.sol

640:    function pushDraft(address account, uint256 rsrAmount)
641:        internal
642:        returns (uint256 index, uint64 availableAt)	// @audit-issue

762:    function allowance(address owner, address spender)
763:        public
764:        view
765:        virtual
766:        override
767:        returns (uint256)	// @audit-issue
```
[642](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L640-L642), [767](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L762-L767), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

236:        if (
237:            (!reweightable || (reweightable && !disableTargetAmountCheck)) &&
238:            config.erc20s.length != 0	// @audit-issue

467:    function quote(uint192 amount, RoundingMode rounding)
468:        external
469:        view
470:        returns (address[] memory erc20s, uint256[] memory quantities)	// @audit-issue

704:    function getHistoricalBasket(uint48 basketNonce)
705:        external
706:        view
707:        returns (IERC20[] memory erc20s, uint256[] memory quantities)	// @audit-issue

738:    function getPrimeBasket()
739:        external
740:        view
741:        returns (
742:            IERC20[] memory erc20s,
743:            bytes32[] memory targetNames,
744:            uint192[] memory targetAmts
745:        )	// @audit-issue

762:    function getBackupConfig(bytes32 targetName)
763:        external
764:        view
765:        returns (IERC20[] memory erc20s, uint256 max)	// @audit-issue
```
[238](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L236-L238), [470](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L467-L470), [707](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L704-L707), [745](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L738-L745), [765](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L762-L765), 


```solidity
Path: ./contracts/p1/Deployer.sol

269:    function deployRTokenAsset(IRToken rToken, uint192 maxTradeVolume)
270:        external
271:        returns (IAsset rTokenAsset)	// @audit-issue
```
[271](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L269-L271), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

112:    function _checkpointsLookup(Checkpoint[] storage ckpts, uint256 timepoint)
113:        private
114:        view
115:        returns (uint256)	// @audit-issue
```
[115](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L112-L115), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

33:    function prepareRecollateralizationTrade(TradingContext memory ctx, Registry memory reg)
34:        external
35:        view
36:        returns (
37:            bool doTrade,
38:            TradeRequest memory req,
39:            TradePrices memory prices
40:        )	// @audit-issue

57:        if (
58:            trade.prices.sellLow == 0 ||
59:            (trade.sell.isCollateral() &&
60:                ICollateral(address(trade.sell)).status() != CollateralStatus.SOUND)	// @audit-issue

108:    function basketRange(TradingContext memory ctx, Registry memory reg)
109:        internal
110:        view
111:        returns (BasketRange memory range)	// @audit-issue

147:            if (
148:                ctx.quantities[i] == 0 &&
149:                !TradeLib.isEnoughToSell(reg.assets[i], ctx.bals[i], low, ctx.minTradeVolume)	// @audit-issue

315:                if (
316:                    isBetterSurplus(maxes, status, delta) &&
317:                    TradeLib.isEnoughToSell(
318:                        reg.assets[i],
319:                        ctx.bals[i].minus(needed),
320:                        low,
321:                        ctx.minTradeVolume
322:                    )	// @audit-issue

361:            if (
362:                high != 0 &&
363:                TradeLib.isEnoughToSell(
364:                    reg.assets[rsrIndex],
365:                    ctx.bals[rsrIndex],
366:                    low,
367:                    ctx.minTradeVolume
368:                )	// @audit-issue
```
[40](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L33-L40), [60](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L57-L60), [111](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L108-L111), [149](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L147-L149), [322](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L315-L322), [368](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L361-L368), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

213:            if (
214:                goodCollateral(config.targetNames[_erc20], _erc20, assetRegistry) &&
215:                targetWeight.gt(FIX_ZERO)	// @audit-issue
```
[215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L213-L215), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

175:    function settle()
176:        external
177:        stateTransition(TradeStatus.OPEN, TradeStatus.CLOSED)
178:        returns (uint256 soldAmt, uint256 boughtAmt)	// @audit-issue
```
[178](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L175-L178), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

274:    function settle()
275:        external
276:        stateTransition(TradeStatus.OPEN, TradeStatus.CLOSED)
277:        returns (uint256 soldAmt, uint256 boughtAmt)	// @audit-issue
```
[277](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L274-L277), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

38:    constructor(
39:        IStRSRVotes token_,
40:        TimelockController timelock_,
41:        uint256 votingDelay_, // {s}
42:        uint256 votingPeriod_, // {s}
43:        uint256 proposalThresholdAsMicroPercent_, // e.g. 1e4 for 0.01%
44:        uint256 quorumPercent // e.g 4 for 4%
45:    )	// @audit-issue

69:    function proposalThreshold()
70:        public
71:        view
72:        override(Governor, GovernorSettings)
73:        returns (uint256)	// @audit-issue

85:    function quorum(uint256 timepoint)
86:        public
87:        view
88:        virtual
89:        override(IGovernor, GovernorVotesQuorumFraction)
90:        returns (uint256)	// @audit-issue

95:    function state(uint256 proposalId)
96:        public
97:        view
98:        override(Governor, GovernorTimelockControl)
99:        returns (ProposalState)	// @audit-issue

156:    function _executor()
157:        internal
158:        view
159:        override(Governor, GovernorTimelockControl)
160:        returns (address)	// @audit-issue

174:    function supportsInterface(bytes4 interfaceId)
175:        public
176:        view
177:        override(Governor, GovernorTimelockControl)
178:        returns (bool)	// @audit-issue
```
[45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L38-L45), [73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L69-L73), [90](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L85-L90), [99](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L95-L99), [160](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L156-L160), [178](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L174-L178), 


#### Recommendation

Adhere to the Solidity style guide regarding control structures by avoiding the definition of multiple functions with identical names in a contract. Unique and descriptive function names improve code clarity and prevent potential confusion or errors. Consult [Solidity style guide - Control Structures](https://docs.soliditylang.org/en/v0.8.20/style-guide.html#control-structures) for best practices.

### Add inline comments for unnamed variables
In Solidity, it's not uncommon to encounter functions with unnamed parameters, especially when certain arguments are not used within the function body. While this is syntactically valid, it can lead to confusion and a lack of clarity about the function's intent and design. For better readability and maintainability, it's beneficial to include inline comments for these unnamed parameters. This practice provides context to other developers or auditors, clarifying the purpose or the reason for the exclusion of these parameters. For instance, transforming `function foo(address x, address)` to `function foo(address x, address /* unused */)`` or `function foo(address x, address /* y */)``. This small change can significantly enhance the understandability of the contract.

```solidity
Path: ./contracts/p1/Main.sol

153:    function _authorizeUpgrade(address) internal view override {	// @audit-issue: Need inline comments for unnamed parameter.
```
[153](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L153-L153), 


```solidity
Path: ./contracts/p1/StRSR.sol

903:    function _afterTokenTransfer(
904:        address,	// @audit-issue: Need inline comments for unnamed parameter.
905:        address to,
906:        uint256
907:    ) internal virtual {

903:    function _afterTokenTransfer(
904:        address,
905:        address to,
906:        uint256	// @audit-issue: Need inline comments for unnamed parameter.
907:    ) internal virtual {
```
[904](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L903-L907), [906](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L903-L907), 


```solidity
Path: ./contracts/p1/RToken.sol

530:    function _beforeTokenTransfer(
531:        address,	// @audit-issue: Need inline comments for unnamed parameter.
532:        address to,
533:        uint256
534:    ) internal virtual override {

530:    function _beforeTokenTransfer(
531:        address,
532:        address to,
533:        uint256	// @audit-issue: Need inline comments for unnamed parameter.
534:    ) internal virtual override {
```
[531](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L530-L534), [533](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L530-L534), 


#### Recommendation

Incorporate inline comments for unnamed parameters in Solidity function definitions. This enhances readability and provides clarity on the function's design. For example, change `function foo(address x, address)` to `function foo(address x, address /* unused */)` or `function foo(address x, address /* y */)` to clearly indicate the purpose or the intentional omission of these parameters.

