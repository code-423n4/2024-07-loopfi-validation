### Prevent re-setting a state variable with the same value
Not only is wasteful in terms of gas, but this is especially problematic when an event is emitted and the old and new values set are the same, as listeners might not expect this kind of scenario.

```solidity
Path: ./contracts/registry/VersionRegistry.sol

53:        deployments[versionHash] = deployer;	// @audit-issue
```
[53](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L53-L53), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

73:            _isValidAsset[versionHash][_asset] = _validities[i];	// @audit-issue

101:            _isValidAsset[_versionHash][asset] = _validities[i];	// @audit-issue
```
[73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L73-L73), [101](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L101-L101), 


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

65:        versionRegistry = VersionRegistry(versionRegistry_);	// @audit-issue

74:        assetPluginRegistry = AssetPluginRegistry(registry_);	// @audit-issue

83:        daoFeeRegistry = DAOFeeRegistry(feeRegistry_);	// @audit-issue
```
[65](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L65-L65), [74](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L74-L74), [83](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L83-L83), 


```solidity
Path: ./contracts/p1/Distributor.sol

259:        distribution[dest] = share;	// @audit-issue
```
[259](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L259-L259), 


```solidity
Path: ./contracts/p1/StRSR.sol

883:        _allowances[era][owner][spender] = amount;	// @audit-issue
```
[883](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L883-L883), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

267:            config.targetAmts[erc20s[i]] = targetAmts[i];	// @audit-issue

644:        enableIssuancePremium = val;	// @audit-issue
```
[267](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L267-L267), [644](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L644-L644), 


```solidity
Path: ./contracts/p1/RToken.sol

400:        basketsNeeded = basketsNeeded_;	// @audit-issue

458:        issuanceThrottle.params = params;	// @audit-issue

469:        redemptionThrottle.params = params;	// @audit-issue
```
[400](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L400-L400), [458](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L458-L458), [469](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L469-L469), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

223:        _delegates[delegator] = delegatee;	// @audit-issue
```
[223](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L223-L223), 


```solidity
Path: ./contracts/p1/Broker.sol

182:        gnosis = newGnosis;	// @audit-issue

193:        batchTradeImplementation = newTradeImplementation;	// @audit-issue

215:        dutchTradeImplementation = newTradeImplementation;	// @audit-issue
```
[182](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L182-L182), [193](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L193-L193), [215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L215-L215), 


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


#### Recommendation

Implement checks in your Solidity contracts to avoid re-setting state variables to their existing values. Prior to updating a state variable, compare the new value with the current value and proceed with the assignment only if they differ. Additionally, ensure that events related to state variable updates are emitted only when actual changes occur. This approach not only saves gas but also prevents confusion and unnecessary triggers in event listeners. Regularly reviewing and optimizing your contract for such redundancies can significantly enhance efficiency and clarity in contract operations.

### Unnecessary Assert
Unnecessary `assert` statements in Solidity contracts can have several detrimental impacts on code readability, gas efficiency, and overall contract functionality. `assert` statements are typically used to check for conditions that should never occur under normal circumstances. While they can be valuable for catching unexpected errors and halting contract execution, their misuse can lead to unnecessary complexity and increased gas costs.


```solidity
Path: ./contracts/p1/BackingManager.sol

308:        assert(tradesOpen == 0);	// @audit-issue
```
[308](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L308-L308), 


```solidity
Path: ./contracts/p1/StRSR.sol

183:        assert(bytes(name_).length != 0);	// @audit-issue

184:        assert(bytes(symbol_).length != 0);	// @audit-issue

844:        assert(totalStakes + amount < type(uint224).max);	// @audit-issue
```
[183](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L183-L183), [184](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L184-L184), [844](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L844-L844), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

126:        assert(address(trades[sell]) == address(0)); // ensure calling class has checked this	// @audit-issue
```
[126](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L126-L126), 


```solidity
Path: ./contracts/p1/mixins/TradeLib.sol

48:        assert(	// @audit-issue
49:            trade.prices.buyHigh != 0 &&
50:                trade.prices.buyHigh != FIX_MAX &&
51:                trade.prices.sellLow != FIX_MAX
52:        );

123:        assert(	// @audit-issue
124:            trade.prices.sellLow != 0 &&
125:                trade.prices.sellLow != FIX_MAX &&
126:                trade.prices.buyHigh != 0 &&
127:                trade.prices.buyHigh != FIX_MAX
128:        );
```
[48](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L48-L52), [123](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L123-L128), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

75:        assert(doTrade);	// @audit-issue
```
[75](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L75-L75), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

205:            assert(targetIndex < targetsLength);	// @audit-issue
```
[205](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L205-L205), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

62:        assert(status == TradeStatus.PENDING);	// @audit-issue

101:        assert(origin_ != address(0));	// @audit-issue

188:            assert(isAuctionCleared());	// @audit-issue
```
[62](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L62-L62), [101](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L101-L101), [188](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L188-L188), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

125:        assert(status == TradeStatus.PENDING);	// @audit-issue

165:        assert(address(sell_) != address(0) && address(buy_) != address(0) && auctionLength >= 60);	// @audit-issue

191:        assert(_worstPrice <= _bestPrice);	// @audit-issue

201:        assert(status == TradeStatus.OPEN);	// @audit-issue

225:        assert(status == TradeStatus.CLOSED);	// @audit-issue

237:        assert(status == TradeStatus.OPEN);	// @audit-issue

268:        assert(status == TradeStatus.CLOSED);	// @audit-issue
```
[125](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L125-L125), [165](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L165-L165), [191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L191-L191), [201](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L201-L201), [225](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L225-L225), [237](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L237-L237), [268](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L268-L268), 


#### Recommendation

Use 'assert' statements in Solidity judiciously, reserving them for conditions that indicate critical and unforeseen errors. Avoid overuse to maintain code clarity and prevent unnecessary gas consumption due to these costly checks.

### Cache State Variable Array Length In For Loop
Failing to cache the array length when iterating through arrays in Solidity can have significant performance and gas cost implications. In Solidity, array lengths can change during execution due to external calls or storage modifications. When the array length is not cached before entering a loop, it is recomputed with each iteration, leading to unnecessary gas consumption and potentially making the contract vulnerable to reentrancy attacks.        


```solidity
Path: ./contracts/p1/BasketHandler.sol

251:        for (uint256 i = 0; i < config.erc20s.length; ++i) {	// @audit-issue
```
[251](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L251-L251), 


#### Recommendation

To enhance performance and reduce gas costs, cache the array length before entering a for loop in Solidity. This approach prevents repeated computation of the array length and mitigates the risk of reentrancy attacks due to array length changes during loop execution.

### Cache Local Variable Array Length In For Loop
If not cached, the solidity compiler will always read the length of the array during each iteration. If it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

39:        for (uint256 i = 0; i < validForVersions.length; ++i) {	// @audit-issue

67:        for (uint256 i = 0; i < _versionHashes.length; ++i) {	// @audit-issue

95:        for (uint256 i = 0; i < _assets.length; ++i) {	// @audit-issue
```
[39](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L39-L39), [67](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L67-L67), [95](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L95-L95), 


```solidity
Path: ./contracts/libraries/String.sol

14:        for (uint256 i = 0; i < bStr.length; i++) {	// @audit-issue
```
[14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/String.sol#L14-L14), 


```solidity
Path: ./contracts/p1/Distributor.sol

92:        for (uint256 i = 0; i < dests.length; ++i) {	// @audit-issue
```
[92](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L92-L92), 


```solidity
Path: ./contracts/p1/BackingManager.sol

289:        for (uint256 i = 0; i < reg.erc20s.length; ++i) {	// @audit-issue

294:        for (uint256 i = 0; i < reg.erc20s.length; ++i) {	// @audit-issue
```
[289](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L289-L289), [294](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L294-L294), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

260:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

296:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

535:        for (uint48 i = 0; i < basketNonces.length; ++i) {	// @audit-issue

547:            for (uint256 j = 0; j < b.erc20s.length; ++j) {	// @audit-issue

684:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

713:        for (uint256 i = 0; i < b.erc20s.length; ++i) {	// @audit-issue

751:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

769:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue
```
[260](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L260-L260), [296](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L296-L296), [535](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L535-L535), [547](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L547-L547), [684](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L684-L684), [713](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L713-L713), [751](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L751-L751), [769](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L769-L769), 


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

To optimize gas consumption, cache the length of memory arrays before for loop iterations in Solidity. This reduces redundant mload operations, saving 3 gas per iteration after the first and improving overall contract efficiency.

### State Variable Access Within a Loop
State variable reads and writes are more expensive than local variable reads and writes. Therefore, it is recommended to replace state variable reads and writes within loops with a local variable. Gas savings should be multiplied by the average loop length.

```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

41:            if (address(versionRegistry.deployments(versionHash)) == address(0)) {	// @audit-issue: `versionRegistry` used in loop.

45:            _isValidAsset[versionHash][_asset] = true;	// @audit-issue: `_isValidAsset` used in loop.

69:            if (address(versionRegistry.deployments(versionHash)) == address(0)) {	// @audit-issue: `versionRegistry` used in loop.

73:            _isValidAsset[versionHash][_asset] = _validities[i];	// @audit-issue: `_isValidAsset` used in loop.

101:            _isValidAsset[_versionHash][asset] = _validities[i];	// @audit-issue: `_isValidAsset` used in loop.
```
[41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L41-L41), [45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L45-L45), [69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L69-L69), [73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L73-L73), [101](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L101-L101), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

90:            require(assetRegistry.isRegistered(erc20s[i]), "unregistered erc20");	// @audit-issue: `assetRegistry` used in loop.

91:            erc20s[i].safeTransfer(address(backingManager), erc20s[i].balanceOf(address(this)));	// @audit-issue: `backingManager` used in loop.

128:            if (erc20s[i] == IERC20(address(rToken))) involvesRToken = true;	// @audit-issue: `rToken` used in loop.

129:            if (erc20s[i] == tokenToBuy) {	// @audit-issue: `tokenToBuy` used in loop.

143:                assetRegistry.toAsset(erc20s[i]).refresh();	// @audit-issue: `assetRegistry` used in loop.

155:            if (erc20 == tokenToBuy) continue;	// @audit-issue: `tokenToBuy` used in loop.

157:            require(address(trades[erc20]) == address(0), "trade open");	// @audit-issue: `trades` used in loop.

160:            IAsset assetToSell = assetRegistry.toAsset(erc20);	// @audit-issue: `assetRegistry` used in loop.

175:                minTradeVolume,	// @audit-issue: `minTradeVolume` used in loop.

176:                maxTradeSlippage	// @audit-issue: `maxTradeSlippage` used in loop.
```
[90](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L90-L90), [91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L91-L91), [128](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L128-L128), [129](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L129-L129), [143](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L143-L143), [155](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L155-L155), [157](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L157-L157), [160](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L160-L160), [175](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L175-L175), [176](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L176-L176), 


```solidity
Path: ./contracts/p1/Distributor.sol

146:            address addrTo = destinations.at(i);	// @audit-issue: `destinations` used in loop.

149:                ? distribution[addrTo].rsrDist	// @audit-issue: `distribution` used in loop.

150:                : distribution[addrTo].rTokenDist;	// @audit-issue: `distribution` used in loop.

156:                addrTo = address(furnace);	// @audit-issue: `furnace` used in loop.

159:                addrTo = address(stRSR);	// @audit-issue: `stRSR` used in loop.

145:        for (uint256 i = 0; i < destinations.length(); ++i) {	// @audit-issue: `destinations` used in loop.

207:            RevenueShare storage share = distribution[destinations.at(i)];	// @audit-issue: `distribution` used in loop.

207:            RevenueShare storage share = distribution[destinations.at(i)];	// @audit-issue: `destinations` used in loop.
```
[146](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L146-L146), [149](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L149-L149), [150](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L150-L150), [156](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L156-L156), [159](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L159-L159), [145](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L145-L145), [207](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L207-L207), [207](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L207-L207), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

64:            assets[IERC20(_erc20s.at(i))].refresh();	// @audit-issue: `assets` used in loop.

64:            assets[IERC20(_erc20s.at(i))].refresh();	// @audit-issue: `_erc20s` used in loop.

152:            erc20s_[i] = IERC20(_erc20s.at(i));	// @audit-issue: `_erc20s` used in loop.

164:            reg.erc20s[i] = IERC20(_erc20s.at(i));	// @audit-issue: `_erc20s` used in loop.

165:            reg.assets[i] = assets[IERC20(_erc20s.at(i))];	// @audit-issue: `assets` used in loop.

165:            reg.assets[i] = assets[IERC20(_erc20s.at(i))];	// @audit-issue: `_erc20s` used in loop.
```
[64](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L64-L64), [64](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L64-L64), [152](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L152-L152), [164](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L164-L164), [165](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L165-L165), [165](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L165-L165), 


```solidity
Path: ./contracts/p1/BackingManager.sol

237:            IAsset asset = assetRegistry.toAsset(erc20s[i]);	// @audit-issue: `assetRegistry` used in loop.

241:            uint192 req = needed.mul(basketHandler.quantity(erc20s[i]), CEIL);	// @audit-issue: `basketHandler` used in loop.

254:                    erc20s[i].safeTransfer(address(rsrTrader), tokensPerShare * totals.rsrTotal);	// @audit-issue: `rsrTrader` used in loop.

258:                        address(rTokenTrader),	// @audit-issue: `rTokenTrader` used in loop.

290:            ctx.quantities[i] = basketHandler.quantityUnsafe(reg.erc20s[i], reg.assets[i]);	// @audit-issue: `basketHandler` used in loop.

295:            ctx.bals[i] = reg.assets[i].bal(address(this)) + tokensOut[reg.erc20s[i]];	// @audit-issue: `tokensOut` used in loop.

298:            if (reg.erc20s[i] == rsr) ctx.bals[i] += reg.assets[i].bal(address(stRSR));	// @audit-issue: `rsr` used in loop.

298:            if (reg.erc20s[i] == rsr) ctx.bals[i] += reg.assets[i].bal(address(stRSR));	// @audit-issue: `stRSR` used in loop.
```
[237](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L237-L237), [241](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L241-L241), [254](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L254-L254), [258](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L258-L258), [290](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L290-L290), [295](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L295-L295), [298](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L298-L298), [298](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L298-L298), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

142:        for (uint256 i = 0; i < len; ++i) refAmts[i] = basket.refAmts[basket.erc20s[i]];	// @audit-issue: `basket` used in loop.

252:            delete config.targetAmts[config.erc20s[i]];	// @audit-issue: `config` used in loop.

253:            delete config.targetNames[config.erc20s[i]];	// @audit-issue: `config` used in loop.

251:        for (uint256 i = 0; i < config.erc20s.length; ++i) {	// @audit-issue: `config` used in loop.

263:            require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "erc20 is not collateral");	// @audit-issue: `assetRegistry` used in loop.

266:            config.erc20s.push(erc20s[i]);	// @audit-issue: `config` used in loop.

267:            config.targetAmts[erc20s[i]] = targetAmts[i];	// @audit-issue: `config` used in loop.

268:            names[i] = assetRegistry.toColl(erc20s[i]).targetName();	// @audit-issue: `assetRegistry` used in loop.

269:            config.targetNames[erc20s[i]] = names[i];	// @audit-issue: `config` used in loop.

299:            assetRegistry.toColl(erc20s[i]); // reverts if not collateral	// @audit-issue: `assetRegistry` used in loop.

322:            CollateralStatus s = assetRegistry.toColl(basket.erc20s[i]).status();	// @audit-issue: `assetRegistry` used in loop.

322:            CollateralStatus s = assetRegistry.toColl(basket.erc20s[i]).status();	// @audit-issue: `basket` used in loop.

425:            try assetRegistry.toColl(basket.erc20s[i]) returns (ICollateral coll) {	// @audit-issue: `assetRegistry` used in loop.

425:            try assetRegistry.toColl(basket.erc20s[i]) returns (ICollateral coll) {	// @audit-issue: `basket` used in loop.

426:                uint192 qty = _quantity(basket.erc20s[i], coll, CEIL);	// @audit-issue: `basket` used in loop.

492:            erc20s[i] = address(basket.erc20s[i]);	// @audit-issue: `basket` used in loop.

493:            ICollateral coll = assetRegistry.toColl(IERC20(erc20s[i]));	// @audit-issue: `assetRegistry` used in loop.

496:            uint192 q = _quantity(basket.erc20s[i], coll, rounding).safeMul(amount, rounding);	// @audit-issue: `basket` used in loop.

508:                int8(IERC20Metadata(address(basket.erc20s[i])).decimals()),	// @audit-issue: `basket` used in loop.

537:                basketNonces[i] >= lastCollateralized && basketNonces[i] <= nonce,	// @audit-issue: `lastCollateralized` used in loop.

537:                basketNonces[i] >= lastCollateralized && basketNonces[i] <= nonce,	// @audit-issue: `nonce` used in loop.

546:            Basket storage b = basketHistory[basketNonces[i]];	// @audit-issue: `basketHistory` used in loop.

566:                    try assetRegistry.toAsset(b.erc20s[j]) returns (IAsset asset) {	// @audit-issue: `assetRegistry` used in loop.

566:                    try assetRegistry.toAsset(b.erc20s[j]) returns (IAsset asset) {	// @audit-issue: `assetRegistry` used in loop.

616:            ICollateral coll = assetRegistry.toColl(basket.erc20s[i]);	// @audit-issue: `assetRegistry` used in loop.

616:            ICollateral coll = assetRegistry.toColl(basket.erc20s[i]);	// @audit-issue: `basket` used in loop.

620:            uint192 q = _quantity(basket.erc20s[i], coll, CEIL);	// @audit-issue: `basket` used in loop.

677:            refAmts[i] = basket.refAmts[basket.erc20s[i]];	// @audit-issue: `basket` used in loop.

686:                erc20s[i] != rsr &&	// @audit-issue: `rsr` used in loop.

687:                    erc20s[i] != IERC20(address(rToken)) &&	// @audit-issue: `rToken` used in loop.

688:                    erc20s[i] != IERC20(address(stRSR)) &&	// @audit-issue: `stRSR` used in loop.

716:            try assetRegistry.toAsset(IERC20(erc20s[i])) returns (IAsset asset) {	// @audit-issue: `assetRegistry` used in loop.

752:            erc20s[i] = config.erc20s[i];	// @audit-issue: `config` used in loop.

753:            targetNames[i] = config.targetNames[erc20s[i]];	// @audit-issue: `config` used in loop.

754:            targetAmts[i] = config.targetAmts[erc20s[i]];	// @audit-issue: `config` used in loop.
```
[142](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L142-L142), [252](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L252-L252), [253](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L253-L253), [251](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L251-L251), [263](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L263-L263), [266](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L266-L266), [267](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L267-L267), [268](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L268-L268), [269](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L269-L269), [299](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L299-L299), [322](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L322-L322), [322](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L322-L322), [425](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L425-L425), [425](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L425-L425), [426](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L426-L426), [492](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L492-L492), [493](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L493-L493), [496](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L496-L496), [508](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L508-L508), [537](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L537-L537), [537](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L537-L537), [546](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L546-L546), [566](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L566-L566), [566](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L566-L566), [616](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L616-L616), [616](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L616-L616), [620](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L620-L620), [677](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L677-L677), [686](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L686-L686), [687](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L687-L687), [688](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L688-L688), [716](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L716-L716), [752](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L752-L752), [753](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L753-L753), [754](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L754-L754), 


```solidity
Path: ./contracts/p1/RToken.sol

151:                address(backingManager),	// @audit-issue: `backingManager` used in loop.

220:                address(backingManager),	// @audit-issue: `backingManager` used in loop.

300:                IERC20(erc20s[i]).balanceOf(address(backingManager)),	// @audit-issue: `backingManager` used in loop.

328:                    address(backingManager),	// @audit-issue: `backingManager` used in loop.
```
[151](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L151-L151), [220](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L220-L220), [300](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L300-L300), [328](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L328-L328), 


#### Recommendation

Optimize gas usage in Solidity by replacing state variable reads and writes within loops with local variable operations. This reduces gas costs significantly, especially when multiplied by the average loop length.

### Another Constant With Same Value Is Already Defined
Defining multiple constants with the same value in a Solidity contract introduces unnecessary redundancy and can lead to confusion and potential errors in contract maintenance. Constants are used to define values that remain unchanged throughout the contract's lifecycle, enhancing readability and efficiency. However, when multiple constants representing the same value are defined, it not only clutters the code but also complicates the process of updating values and understanding the contract's logic. Consolidating these constants into a single definition improves code clarity and maintainability.

```solidity
Path: ./contracts/p1/RToken.sol

24:    uint192 public constant MAX_THROTTLE_PCT_AMT = 1e18; // {qRTok}	// @audit-issue: Same value `1e18` is already defined on line `22` as variable: `MIN_THROTTLE_RATE_AMT`
```
[24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L24-L24), 


#### Recommendation

Identify and consolidate duplicate constants in your Solidity contracts. Examine your contract's constants to find any with identical values and refactor your code to use a single constant definition for each unique value. This may involve choosing the most descriptive and appropriate name for the consolidated constant and updating all references accordingly. Such consolidation not only makes your contract more concise and easier to read but also simplifies future updates to constant values. Document the purpose and usage of each constant clearly, ensuring that the chosen names accurately reflect their role within the contract.

### Shortcircuit rules can be be used to optimize some gas usage
Some conditions may be reordered to save an SLOAD (2100 gas), as we avoid reading state variables when the first part of the condition fails (with `&&`), or succeeds (with `||`).

```solidity
Path: ./contracts/p1/BasketHandler.sol

187:        if (reweightable && nonce > lastCollateralized && fullyCollateralized()) {	// @audit-issue: Control with `lastCollateralized` can be done after all non state variable/function call controls.

319:        if (disabled || size == 0) return CollateralStatus.DISABLED;	// @audit-issue: Control with `disabled` can be done after all non state variable/function call controls.

368:        if (!enableIssuancePremium || coll.lastSave() != block.timestamp) return FIX_ONE;	// @audit-issue: Control with `enableIssuancePremium` can be done after all non state variable/function call controls.
```
[187](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L187-L187), [319](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L319-L319), [368](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L368-L368), 


#### Recommendation

Review your Solidity contracts for conditional statements that use `&&` and `||` operators. Prioritize conditions that are less gas-intensive or have a higher likelihood of determining the outcome of the entire expression. For instance, if a condition involves an `SLOAD` operation and another is a simple variable comparison, evaluate the simpler condition first:
Before optimization:
```solidity
if (contractStateVariable > value && isEligible(msg.sender)) {
    // Execution logic
}

After optimization:
```solidity
if (isEligible(msg.sender) && contractStateVariable > value) {
    // Execution logic
}

This rearrangement ensures that if `isEligible(msg.sender)` returns false, the contract avoids the gas cost associated with reading `contractStateVariable` from storage. Apply this principle across your contract's logic where applicable, especially in functions called frequently or in gas-sensitive contexts. Testing remains crucial; ensure that the reordering of conditions does not alter the intended logic or outcomes of your contract.

### Same cast is done multiple times
It's cheaper to do it once, and store the result to a variable.

```solidity
Path: ./contracts/mixins/Auth.sol

162:        emit UnfreezeAtSet(unfreezeAt, uint48(block.timestamp));	// @audit-issue: Same casting also done in line(s): `[163]`.
```
[162](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L162-L162), 


```solidity
Path: ./contracts/libraries/Throttle.sol

58:            require(uint256(amount) <= available, "supply change throttled");	// @audit-issue: Same casting also done in line(s): `[59]`.
```
[58](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L58-L58), 


```solidity
Path: ./contracts/libraries/String.sol

16:            if ((uint8(bStr[i]) >= 65) && (uint8(bStr[i]) <= 90)) {	// @audit-issue: Same casting also done in line(s): `[18]`.
```
[16](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/String.sol#L16-L16), 


```solidity
Path: ./contracts/p1/Main.sol

144:        IExtendedBroker(address(broker)).setBatchTradeImplementation(	// @audit-issue: Same casting also done in line(s): `[147, 135]`.
```
[144](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L144-L144), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

120:                (address(tokenToBuy) == address(rToken) && revTotals.rTokenTotal != 0),	// @audit-issue: Same casting also done in line(s): `[128, 126]`.

192:        tokenToBuy.safeApprove(address(distributor), bal);	// @audit-issue: Same casting also done in line(s): `[191]`.
```
[120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L120-L120), [192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L192-L192), 


```solidity
Path: ./contracts/p1/Distributor.sol

170:            IERC20Upgradeable(address(erc20)).safeTransferFrom(	// @audit-issue: Same casting also done in line(s): `[183]`.
```
[170](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L170-L170), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

109:        require(_erc20s.contains(address(asset.erc20())), "no asset to unregister");	// @audit-issue: Same casting also done in line(s): `[118]`.

216:                ICollateral(address(asset)).status() == CollateralStatus.SOUND,	// @audit-issue: Same casting also done in line(s): `[226]`.

233:        if (_erc20s.contains(address(erc20))) {	// @audit-issue: Same casting also done in line(s): `[237]`.
```
[109](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L109-L109), [216](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L216-L216), [233](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L233-L233), 


```solidity
Path: ./contracts/p1/BackingManager.sol

72:        IERC20(address(erc20)).safeApprove(address(rToken), 0);	// @audit-issue: Same casting also done in line(s): `[73]`.
```
[72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L72-L72), 


```solidity
Path: ./contracts/p1/RToken.sol

85:        issuanceThrottle.lastTimestamp = uint48(block.timestamp);	// @audit-issue: Same casting also done in line(s): `[86]`.

121:        issuanceThrottle.useAvailable(supply, int256(amount)); // reverts on over-issuance	// @audit-issue: Same casting also done in line(s): `[122]`.

200:        redemptionThrottle.useAvailable(supply, int256(amount)); // reverts on over-redemption	// @audit-issue: Same casting also done in line(s): `[199]`.

277:        issuanceThrottle.useAvailable(supply, -int256(amount));	// @audit-issue: Same casting also done in line(s): `[278]`.

328:                    address(backingManager),	// @audit-issue: Same casting also done in line(s): `[300]`.

357:        require(_msgSender() == address(backingManager), "not backing manager");	// @audit-issue: Same casting also done in line(s): `[358]`.
```
[85](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L85-L85), [121](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L121-L121), [200](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L200-L200), [277](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L277-L277), [328](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L328-L328), [357](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L357-L357), 


```solidity
Path: ./contracts/p1/Broker.sol

261:            address(trade),	// @audit-issue: Same casting also done in line(s): `[247]`.

289:            address(trade),	// @audit-issue: Same casting also done in line(s): `[284]`.
```
[261](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L261-L261), [289](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L289-L289), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

210:            if (uint256(deltaTop) + ctx.basketsHeld.top > FIX_MAX) range.top = FIX_MAX;	// @audit-issue: Same casting also done in line(s): `[211]`.
```
[210](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L210-L210), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

291:        buy.safeTransfer(address(origin), boughtAmt); // {qBuyTok}	// @audit-issue: Same casting also done in line(s): `[292, 279]`.
```
[291](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L291-L291), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

187:        uint256 pastEra = IStRSRVotes(address(token)).getPastEra(startTimepoint);	// @audit-issue: Same casting also done in line(s): `[188]`.
```
[187](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L187-L187), 


#### Recommendation

Identify instances in your Solidity contracts where the same value is cast to another type multiple times. Refactor these operations by performing the cast once and storing the result in a local variable. Use this variable for all subsequent operations requiring the cast value.

### `address(this)` should be cached
Cacheing saves gas when compared to repeating the calculation at each point it is used in the contract.

```solidity
Path: ./contracts/libraries/Allowance.sol

35:            success = token.allowance(address(this), spender) == value;	// @audit-issue: `adress(this)` also used on line(s): [28, 44]
```
[35](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L35-L35), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

158:            require(erc20.balanceOf(address(this)) != 0, "0 balance");	// @audit-issue: `adress(this)` also used on line(s): [166]
```
[158](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L158-L158), 


```solidity
Path: ./contracts/p1/BackingManager.sol

124:        BasketRange memory basketsHeld = basketHandler.basketsHeldBy(address(this));	// @audit-issue: `adress(this)` also used on line(s): [130, 160, 116]

242:            uint192 bal = asset.bal(address(this));	// @audit-issue: `adress(this)` also used on line(s): [213, 185, 215]
```
[124](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L124-L124), [242](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L242-L242), 


```solidity
Path: ./contracts/p1/Deployer.sol

114:        require(owner != address(0) && owner != address(this), "invalid owner");	// @audit-issue: `adress(this)` also used on line(s): [260]
```
[114](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L114-L114), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

203:        boughtAmt = buy.balanceOf(address(this));	// @audit-issue: `adress(this)` also used on line(s): [194]
```
[203](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L203-L203), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

257:        uint256 balanceBefore = buy.balanceOf(address(this)); // {qBuyTok}	// @audit-issue: `adress(this)` also used on line(s): [260]

290:        boughtAmt = buy.balanceOf(address(this)); // {qBuyTok}	// @audit-issue: `adress(this)` also used on line(s): [292]
```
[257](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L257-L257), [290](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L290-L290), 


#### Recommendation

To enhance gas efficiency, cache the contract's address by storing `address(this)` in a state variable at the point of contract deployment or initialization. Use this cached address throughout the contract instead of repeatedly calling `address(this)`. This practice reduces the gas cost associated with multiple computations of the contract's address, leading to more efficient contract execution, especially in scenarios with frequent usage of the contract's address.

### Superfluous event fields
`block.number` and `block.timestamp` are added to the event information by default, so adding them manually will waste additional gas.

```solidity
Path: ./contracts/mixins/Auth.sol

162:        emit UnfreezeAtSet(unfreezeAt, uint48(block.timestamp));	// @audit-issue
```
[162](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L162-L162), 


#### Recommendation

Review your smart contracts to identify and remove `block.number` and `block.timestamp` from the parameters of emitted events. Instead of manually adding these fields, rely on the Ethereum blockchain's inherent inclusion of this information within the block context. Simplify your event definitions to include only the essential data specific to the event's purpose, excluding universally available block metadata.

### It is a waste of GAS to emit variable literals
Emitting variable literals (true, false, address(0), 1 etc...) in events is inefficient, as it consumes extra gas without providing added value. These literals are fixed values that can be accessed or hardcoded elsewhere in the smart contract or application, making their inclusion in events redundant and an unnecessary drain on resources during transaction execution.

```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

80:        emit RTokenFeeNumeratorSet(rToken, 0, false);	// @audit-issue
```
[80](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L80-L80), 


```solidity
Path: ./contracts/p1/StRSR.sol

849:        emit Transfer(address(0), account, amount);	// @audit-issue

871:        emit Transfer(account, address(0), amount);	// @audit-issue
```
[849](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L849-L849), [871](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L871-L871), 


#### Recommendation

Refrain from including fixed variable literals as parameters in your Solidity contract events. Evaluate your event emissions and remove literals that do not provide dynamic information about contract state or actions. For instance, instead of emitting an event with a `true` literal to indicate success, consider naming the event in a way that inherently indicates success or failure, such as `ActionSuccessful()`:
```solidity
// Before optimization
event ActionTaken(bool success);

// After optimization
event ActionSuccessful();
event ActionFailed();
```


### Revert String Size Optimization
Shortening the revert strings to fit within 32 bytes will decrease deployment time and decrease runtime Gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional `mstore`, along with additional overhead to calculate memory offset, etc.



```solidity
Path: ./contracts/p1/Distributor.sol

242:        require(	// @audit-issue: String length is `48`
243:            dest != address(furnace) && dest != address(stRSR),
244:            "destination can not be furnace or strsr directly"
245:        );

246:        require(dest != address(main.daoFeeRegistry()), "destination cannot be daoFeeRegistry");	// @audit-issue: String length is `36`
```
[242](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L242-L245), [246](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L246-L246), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

259:        require(	// @audit-issue: String length is `35`
260:            gas > (64 * GAS_FOR_BH_QTY) / 63 + GAS_FOR_DISABLE_BASKET,
261:            "not enough gas to unregister safely"
262:        );
```
[259](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L259-L262), 


```solidity
Path: ./contracts/p1/RToken.sol

193:        require(basketHandler.fullyCollateralized(), "partial redemption; use redeemCustom");	// @audit-issue: String length is `36`

272:        require(portionsSum == FIX_ONE, "portions do not add up to FIX_ONE");	// @audit-issue: String length is `33`
```
[193](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L193-L193), [272](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L272-L272), 


```solidity
Path: ./contracts/p1/Broker.sol

86:        require(	// @audit-issue: String length is `40`
87:            address(batchTradeImplementation_) != address(0),
88:            "invalid batchTradeImplementation address"
89:        );

90:        require(	// @audit-issue: String length is `40`
91:            address(dutchTradeImplementation_) != address(0),
92:            "invalid dutchTradeImplementation address"
93:        );

187:        require(	// @audit-issue: String length is `40`
188:            address(newTradeImplementation) != address(0),
189:            "invalid batchTradeImplementation address"
190:        );

209:        require(	// @audit-issue: String length is `40`
210:            address(newTradeImplementation) != address(0),
211:            "invalid dutchTradeImplementation address"
212:        );

273:        require(	// @audit-issue: String length is `38`
274:            !dutchTradeDisabled[req.sell.erc20()] && !dutchTradeDisabled[req.buy.erc20()],
275:            "dutch auctions disabled for token pair"
276:        );

278:        require(	// @audit-issue: String length is `34`
279:            pricedAtTimestamp(req.sell) && pricedAtTimestamp(req.buy),
280:            "dutch auctions require live prices"
281:        );
```
[86](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L86-L89), [90](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L90-L93), [187](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L187-L190), [209](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L209-L212), [273](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L273-L276), [278](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L278-L281), 


#### Recommendation


To optimize Gas usage in your Solidity contract, it is recommended to keep revert strings as short as possible and to ensure that they fit within 32 bytes. It is possible to use abbreviations or simplified error messages to keep the string length short. Doing so can reduce the amount of Gas used during deployment and runtime when the revert condition is met.


### Custom Errors in Solidity for Gas Efficiency
Starting from Solidity version 0.8.4, the language introduced a feature known as "custom errors". These custom errors provide a way for developers to define more descriptive and semantically meaningful error conditions without relying on string messages. Prior to this version, developers often used the `require` statement with string error messages to handle specific conditions or validations. However, every unique string used as a revert reason consumes gas, making transactions more expensive.

Custom errors, on the other hand, are identified by their name and the types of their parameters only, and they do not have the overhead of string storage. This means that, when using custom errors instead of `require` statements with string messages, the gas consumption can be significantly reduced, leading to more gas-efficient contracts.

```solidity
Path: ./contracts/mixins/Auth.sol

92:        require(account != address(0), "cannot grant role to address 0");	// @audit-issue

199:        require(shortFreeze_ != 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");	// @audit-issue

206:        require(longFreeze_ != 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");	// @audit-issue

215:        require(newUnfreezeAt > unfreezeAt, "frozen");	// @audit-issue
```
[92](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L92-L92), [199](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L199-L199), [206](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L206-L206), [215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L215-L215), 


```solidity
Path: ./contracts/mixins/ComponentRegistry.sol

36:        require(address(val) != address(0), "invalid RToken address");	// @audit-issue

44:        require(address(val) != address(0), "invalid StRSR address");	// @audit-issue

52:        require(address(val) != address(0), "invalid AssetRegistry address");	// @audit-issue

60:        require(address(val) != address(0), "invalid BasketHandler address");	// @audit-issue

68:        require(address(val) != address(0), "invalid BackingManager address");	// @audit-issue

76:        require(address(val) != address(0), "invalid Distributor address");	// @audit-issue

84:        require(address(val) != address(0), "invalid RSRTrader address");	// @audit-issue

92:        require(address(val) != address(0), "invalid RTokenTrader address");	// @audit-issue

100:        require(address(val) != address(0), "invalid Furnace address");	// @audit-issue

108:        require(address(val) != address(0), "invalid Broker address");	// @audit-issue
```
[36](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L36-L36), [44](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L44-L44), [52](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L52-L52), [60](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L60-L60), [68](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L68-L68), [76](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L76-L76), [84](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L84-L84), [92](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L92-L92), [100](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L100-L100), [108](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L108-L108), 


```solidity
Path: ./contracts/libraries/Throttle.sol

58:            require(uint256(amount) <= available, "supply change throttled");
```
[57](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L58-L58), 


```solidity
Path: ./contracts/libraries/Fixed.sol

318:        require(x_ <= FIX_ONE);	// @audit-issue
```
[318](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L318-L318), 


```solidity
Path: ./contracts/libraries/Allowance.sol

28:        require(token.allowance(address(this), spender) == 0, "allowance not 0");	// @audit-issue

44:            require(token.allowance(address(this), spender) >= value, "allowance missing");
```
[28](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L28-L28), [40](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L44-L44), 


```solidity
Path: ./contracts/libraries/Permit.sol

18:            require(
19:                IERC1271Upgradeable(owner).isValidSignature(hash, abi.encodePacked(r, s, v)) ==
20:                    0x1626ba7e,
21:                "ERC1271: Unauthorized"
22:            );

24:            require(
25:                SignatureCheckerUpgradeable.isValidSignatureNow(
26:                    owner,
27:                    hash,
28:                    abi.encodePacked(r, s, v)
29:                ),
30:                "ERC20Permit: invalid signature"
31:            );
```
[17](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Permit.sol#L18-L22), [17](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Permit.sol#L24-L31), 


```solidity
Path: ./contracts/p1/Main.sol

39:        require(address(rsr_) != address(0), "invalid RSR address");	// @audit-issue

62:        require(address(versionRegistry_) != address(0), "invalid registry address");	// @audit-issue

63:        require(address(versionRegistry) == address(0), "already set");	// @audit-issue

71:        require(address(registry_) != address(0), "invalid registry address");	// @audit-issue

72:        require(address(assetPluginRegistry) == address(0), "already set");	// @audit-issue

80:        require(address(feeRegistry_) != address(0), "invalid registry address");	// @audit-issue

81:        require(address(daoFeeRegistry) == address(0), "already set");	// @audit-issue

100:        require(address(versionRegistry) != address(0), "no registry");	// @audit-issue

101:        require(!versionRegistry.isDeprecated(versionHash), "version deprecated");	// @audit-issue

108:        require(keccak256(abi.encodePacked(this.version())) == versionHash, "upgrade mismatch");	// @audit-issue

116:        require(address(versionRegistry) != address(0), "no registry");	// @audit-issue

117:        require(keccak256(abi.encodePacked(this.version())) == versionHash, "upgrade main first");	// @audit-issue

154:        require(msg.sender == address(this), "not self");	// @audit-issue

162:        require(success, "upgrade failed");	// @audit-issue
```
[39](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L39-L39), [62](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L62-L62), [63](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L63-L63), [71](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L71-L71), [72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L72-L72), [80](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L80-L80), [81](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L81-L81), [100](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L100-L100), [101](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L101-L101), [108](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L108-L108), [116](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L116-L116), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L117-L117), [154](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L154-L154), [162](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L162-L162), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

33:        require(address(tokenToBuy_) != address(0), "invalid token address");	// @audit-issue

62:            if (errData.length == 0) revert(); // solhint-disable-line reason-string

79:            require(revTotals.rsrTotal == 0, "rsrTotal > 0");

81:            require(revTotals.rTokenTotal == 0, "rTokenTotal > 0");

84:            revert("invalid tokenToBuy");

90:            require(assetRegistry.isRegistered(erc20s[i]), "unregistered erc20");

115:        require(len != 0, "empty erc20s list");	// @audit-issue

116:        require(len == kinds.length, "length mismatch");	// @audit-issue

118:        require(	// @audit-issue
119:            (tokenToBuy == rsr && revTotals.rsrTotal != 0) ||
120:                (address(tokenToBuy) == address(rToken) && revTotals.rTokenTotal != 0),
121:            "zero distribution"
122:        );

150:        require(buyHigh != 0 && buyHigh != FIX_MAX, "buy asset price unknown");	// @audit-issue

157:            require(address(trades[erc20]) == address(0), "trade open");

158:            require(erc20.balanceOf(address(this)) != 0, "0 balance");

178:            require(req.sellAmount > 1, "sell amount too low");
```
[33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L33-L33), [58](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L62-L62), [78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L79-L79), [78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L81-L81), [78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L84-L84), [89](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L90-L90), [115](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L115-L115), [116](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L116-L116), [118](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L118-L122), [150](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L150-L150), [153](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L157-L157), [153](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L158-L158), [153](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L178-L178), 


```solidity
Path: ./contracts/p1/Distributor.sol

85:        require(dests.length == shares.length, "array length mismatch");	// @audit-issue

124:        require(caller == rsrTrader || caller == rTokenTrader, "RevenueTraders only");	// @audit-issue

125:        require(erc20 == rsr || erc20 == rToken, "RSR or RToken");	// @audit-issue

134:            require(tokensPerShare != 0, "nothing to distribute");

241:        require(dest != address(0), "dest cannot be zero");	// @audit-issue

242:        require(	// @audit-issue
243:            dest != address(furnace) && dest != address(stRSR),
244:            "destination can not be furnace or strsr directly"
245:        );

246:        require(dest != address(main.daoFeeRegistry()), "destination cannot be daoFeeRegistry");	// @audit-issue

247:        if (dest == FURNACE) require(share.rsrDist == 0, "Furnace must get 0% of RSR");	// @audit-issue

248:        if (dest == ST_RSR) require(share.rTokenDist == 0, "StRSR must get 0% of RToken");	// @audit-issue

249:        require(share.rsrDist <= MAX_DISTRIBUTION, "RSR distribution too high");	// @audit-issue

250:        require(share.rTokenDist <= MAX_DISTRIBUTION, "RToken distribution too high");	// @audit-issue

256:            require(destinations.length() <= MAX_DESTINATIONS_ALLOWED, "Too many destinations");

266:        require(uint256(rTokenTotal) + uint256(rsrTotal) >= MAX_DISTRIBUTION, "totals too low");	// @audit-issue
```
[85](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L85-L85), [124](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L124-L124), [125](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L125-L125), [130](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L134-L134), [241](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L241-L241), [242](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L242-L245), [246](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L246-L246), [247](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L247-L247), [248](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L248-L248), [249](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L249-L249), [250](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L250-L250), [252](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L256-L256), [266](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L266-L266), 


```solidity
Path: ./contracts/p1/Furnace.sol

84:        require(ratio_ <= MAX_RATIO, "invalid ratio");	// @audit-issue
```
[84](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L84-L84), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

91:        require(_erc20s.contains(address(asset.erc20())), "no ERC20 collision");	// @audit-issue

109:        require(_erc20s.contains(address(asset.erc20())), "no asset to unregister");	// @audit-issue

110:        require(assets[asset.erc20()] == asset, "asset not found");	// @audit-issue

127:        require(_erc20s.contains(address(erc20)), "erc20 unregistered");	// @audit-issue

135:        require(_erc20s.contains(address(erc20)), "erc20 unregistered");	// @audit-issue

136:        require(assets[erc20].isCollateral(), "erc20 is not collateral");	// @audit-issue

179:                require(
180:                    assetPluginRegistry.isValidAsset(
181:                        keccak256(abi.encodePacked(this.version())),
182:                        address(asset)
183:                    ),
184:                    "unsupported asset"
185:                );

202:        require(	// @audit-issue
203:            !_erc20s.contains(address(asset.erc20())) || assets[asset.erc20()] == asset,
204:            "duplicate ERC20 detected"
205:        );

215:            require(
216:                ICollateral(address(asset)).status() == CollateralStatus.SOUND,
217:                "collateral not sound"
218:            );

223:            require(
224:                main.assetPluginRegistry().isValidAsset(
225:                    keccak256(abi.encodePacked(this.version())),
226:                    address(asset)
227:                ),
228:                "unsupported asset"
229:            );

259:        require(	// @audit-issue
260:            gas > (64 * GAS_FOR_BH_QTY) / 63 + GAS_FOR_DISABLE_BASKET,
261:            "not enough gas to unregister safely"
262:        );
```
[91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L91-L91), [109](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L109-L109), [110](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L110-L110), [127](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L127-L127), [135](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L135-L135), [136](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L136-L136), [174](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L179-L185), [202](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L202-L205), [214](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L215-L218), [222](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L223-L229), [259](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L259-L262), 


```solidity
Path: ./contracts/p1/BackingManager.sol

70:        require(assetRegistry.isRegistered(erc20), "erc20 unregistered");	// @audit-issue

97:                if (errData.length == 0) revert(); // solhint-disable-line reason-string

115:        require(	// @audit-issue
116:            _msgSender() == address(this) || tradeEnd[kind] < block.timestamp,
117:            "already rebalancing"
118:        );

120:        require(tradesOpen == 0, "trade open");	// @audit-issue

121:        require(basketHandler.isReady(), "basket not ready");	// @audit-issue

122:        require(block.timestamp >= basketHandler.timestamp() + tradingDelay, "trading delayed");	// @audit-issue

125:        require(basketsHeld.bottom < rToken.basketsNeeded(), "already collateralized");	// @audit-issue

181:        require(ArrayLib.allUnique(erc20s), "duplicate tokens");	// @audit-issue

187:        require(tradesOpen == 0, "trade open");	// @audit-issue

188:        require(basketHandler.isReady(), "basket not ready");	// @audit-issue

189:        require(block.timestamp >= basketHandler.timestamp() + tradingDelay, "trading delayed");	// @audit-issue

190:        require(basketsHeld.bottom >= rToken.basketsNeeded(), "undercollateralized");	// @audit-issue

308:        assert(tradesOpen == 0);	// @audit-issue

316:        require(val <= MAX_TRADING_DELAY, "invalid tradingDelay");	// @audit-issue

323:        require(val <= MAX_BACKING_BUFFER, "invalid backingBuffer");	// @audit-issue
```
[70](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L70-L70), [90](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L97-L97), [115](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L115-L118), [120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L120-L120), [121](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L121-L121), [122](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L122-L122), [125](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L125-L125), [181](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L181-L181), [187](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L187-L187), [188](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L188-L188), [189](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L189-L189), [190](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L190-L190), [308](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L308-L308), [316](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L316-L316), [323](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L323-L323), 


```solidity
Path: ./contracts/p1/StRSR.sol

183:        assert(bytes(name_).length != 0);	// @audit-issue

184:        assert(bytes(symbol_).length != 0);	// @audit-issue

264:        require(stakes[era][account] >= stakeAmount, "insufficient balance");	// @audit-issue

312:        require(endId <= queue.length, "index out-of-bounds");	// @audit-issue

313:        require(queue[endId - 1].availableAt <= block.timestamp, "withdrawal unavailable");	// @audit-issue

341:        require(basketHandler.isReady() && basketHandler.fullyCollateralized(), "RToken readying");	// @audit-issue

357:        require(endId <= queue.length, "index out-of-bounds");	// @audit-issue

429:        require(caller == address(backingManager), "!bm");	// @audit-issue

432:        require(rsrAmount <= rsrBalance, "seize exceeds balance");	// @audit-issue

492:        require(	// @audit-issue
493:            stakeRate <= MIN_SAFE_STAKE_RATE || stakeRate >= MAX_SAFE_STAKE_RATE,
494:            "rate still safe"
495:        );

809:        require(currentAllowance >= subtractedValue, "decrease allowance");	// @audit-issue

829:        require(fromBalance >= amount, "insufficient balance");	// @audit-issue

844:        assert(totalStakes + amount < type(uint224).max);	// @audit-issue

865:        require(accountBalance >= amount, "insufficient balances");	// @audit-issue

894:            require(currentAllowance >= amount, "insufficient allowance");

908:        require(to != address(this), "transfer to self");	// @audit-issue

912:        require(addr != address(0), "zero address");	// @audit-issue

916:        require(val != 0, "zero amount");	// @audit-issue

931:        require(block.timestamp <= deadline, "ERC20Permit: expired deadline");	// @audit-issue

972:        require(val > MIN_UNSTAKING_DELAY && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");	// @audit-issue

981:        require(val <= MAX_REWARD_RATIO, "invalid rewardRatio");	// @audit-issue

989:        require(val <= MAX_WITHDRAWAL_LEAK, "invalid withdrawalLeak");	// @audit-issue
```
[183](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L183-L183), [184](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L184-L184), [264](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L264-L264), [312](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L312-L312), [313](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L313-L313), [341](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L341-L341), [357](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L357-L357), [429](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L429-L429), [432](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L432-L432), [492](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L492-L495), [809](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L809-L809), [829](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L829-L829), [844](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L844-L844), [865](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L865-L865), [893](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L894-L894), [908](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L908-L908), [912](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L912-L912), [916](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L916-L916), [931](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L931-L931), [972](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L972-L972), [981](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L981-L981), [989](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L989-L989), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

138:        require(_msgSender() == address(assetRegistry), "asset registry only");	// @audit-issue

161:        require(	// @audit-issue
162:            main.hasRole(OWNER, _msgSender()) ||
163:                (lastStatus == CollateralStatus.DISABLED && !main.tradingPausedOrFrozen()),
164:            "basket unrefreshable"
165:        );

233:        require(erc20s.length != 0 && erc20s.length == targetAmts.length, "invalid lengths");	// @audit-issue

263:            require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "erc20 is not collateral");

264:            require(0 < targetAmts[i] && targetAmts[i] <= MAX_TARGET_AMT, "invalid target amount");

290:        require(max <= MAX_BACKUP_ERC20S && erc20s.length <= MAX_BACKUP_ERC20S, "too large");	// @audit-issue

450:                if (errData.length == 0) revert(); // solhint-disable-line reason-string

526:        require(basketNonces.length == portions.length, "invalid lengths");	// @audit-issue

536:            require(
537:                basketNonces[i] >= lastCollateralized && basketNonces[i] <= nonce,
538:                "invalid basketNonce"
539:            );

579:                        if (errData.length == 0) revert(); // solhint-disable-line reason-string

635:        require(val >= MIN_WARMUP_PERIOD && val <= MAX_WARMUP_PERIOD, "invalid warmupPeriod");	// @audit-issue

685:            require(
686:                erc20s[i] != rsr &&
687:                    erc20s[i] != IERC20(address(rToken)) &&
688:                    erc20s[i] != IERC20(address(stRSR)) &&
689:                    erc20s[i] != IERC20(address(0)),
690:                "invalid collateral"
691:            );

694:        require(ArrayLib.allUnique(erc20s), "contains duplicates");	// @audit-issue

728:                if (errData.length == 0) revert(); // solhint-disable-line reason-string
```
[138](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L138-L138), [161](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L161-L165), [233](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L233-L233), [260](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L263-L263), [260](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L264-L264), [290](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L290-L290), [424](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L450-L450), [526](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L526-L526), [535](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L536-L539), [535](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L579-L579), [635](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L635-L635), [684](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L685-L691), [694](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L694-L694), [713](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L728-L728), 


```solidity
Path: ./contracts/p1/Deployer.sol

48:        require(	// @audit-issue
49:            address(rsr_) != address(0) &&
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
65:            "invalid address"
66:        );

114:        require(owner != address(0) && owner != address(this), "invalid owner");	// @audit-issue
```
[48](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L48-L66), [114](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L114-L114), 


```solidity
Path: ./contracts/p1/RToken.sol

69:        require(bytes(name_).length != 0, "name empty");	// @audit-issue

70:        require(bytes(symbol_).length != 0, "symbol empty");	// @audit-issue

71:        require(bytes(mandate_).length != 0, "mandate empty");	// @audit-issue

106:        require(amount != 0, "Cannot issue zero");	// @audit-issue

117:        require(basketHandler.isReady(), "basket not ready");	// @audit-issue

191:        require(amount != 0, "Cannot redeem zero");	// @audit-issue

192:        require(amount <= balanceOf(caller), "insufficient balance");	// @audit-issue

193:        require(basketHandler.fullyCollateralized(), "partial redemption; use redeemCustom");	// @audit-issue

266:        require(amount != 0, "Cannot redeem zero");	// @audit-issue

267:        require(amount <= balanceOf(_msgSender()), "insufficient balance");	// @audit-issue

272:        require(portionsSum == FIX_ONE, "portions do not add up to FIX_ONE");	// @audit-issue

333:            if (allZero) revert("empty redemption");

342:            require(bal - pastBals[i] >= minAmounts[i], "redemption below minimum");

357:        require(_msgSender() == address(backingManager), "not backing manager");	// @audit-issue

372:        require(caller == address(furnace), "furnace only");	// @audit-issue

389:        require(caller == address(backingManager), "not backing manager");	// @audit-issue

398:        require(_msgSender() == address(backingManager), "not backing manager");	// @audit-issue

404:        require(supply != 0, "0 supply");	// @audit-issue

413:        require(low >= MIN_EXCHANGE_RATE && high <= MAX_EXCHANGE_RATE, "BU rate out of range");	// @audit-issue

419:        require(assetRegistry.isRegistered(erc20), "erc20 unregistered");	// @audit-issue

452:        require(params.amtRate >= MIN_THROTTLE_RATE_AMT, "issuance amtRate too small");	// @audit-issue

453:        require(params.amtRate <= MAX_THROTTLE_RATE_AMT, "issuance amtRate too big");	// @audit-issue

454:        require(params.pctRate <= MAX_THROTTLE_PCT_AMT, "issuance pctRate too big");	// @audit-issue

463:        require(params.amtRate >= MIN_THROTTLE_RATE_AMT, "redemption amtRate too small");	// @audit-issue

464:        require(params.amtRate <= MAX_THROTTLE_RATE_AMT, "redemption amtRate too big");	// @audit-issue

465:        require(params.pctRate <= MAX_THROTTLE_PCT_AMT, "redemption pctRate too big");	// @audit-issue

535:        require(to != address(this), "RToken transfer to self");	// @audit-issue
```
[69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L69-L69), [70](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L70-L70), [71](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L71-L71), [106](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L106-L106), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L117-L117), [191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L191-L191), [192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L192-L192), [193](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L193-L193), [266](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L266-L266), [267](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L267-L267), [272](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L272-L272), [319](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L333-L333), [339](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L342-L342), [357](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L357-L357), [372](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L372-L372), [389](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L389-L389), [398](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L398-L398), [404](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L404-L404), [413](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L413-L413), [419](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L419-L419), [452](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L452-L452), [453](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L453-L453), [454](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L454-L454), [463](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L463-L463), [464](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L464-L464), [465](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L465-L465), [535](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L535-L535), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

174:        require(block.timestamp <= expiry, "signature expired");	// @audit-issue

181:        require(nonce == _useDelegationNonce(signer), "invalid nonce");	// @audit-issue

292:        require(timepoint < block.timestamp, "future lookup");	// @audit-issue
```
[174](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L174-L174), [181](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L181-L181), [292](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L292-L292), 


```solidity
Path: ./contracts/p1/Broker.sol

86:        require(	// @audit-issue
87:            address(batchTradeImplementation_) != address(0),
88:            "invalid batchTradeImplementation address"
89:        );

90:        require(	// @audit-issue
91:            address(dutchTradeImplementation_) != address(0),
92:            "invalid dutchTradeImplementation address"
93:        );

132:        require(	// @audit-issue
133:            caller == address(backingManager) ||
134:                caller == address(rsrTrader) ||
135:                caller == address(rTokenTrader),
136:            "only traders"
137:        );

151:        require(trades[_msgSender()], "unrecognized trade contract");	// @audit-issue

171:            revert("unrecognized trade kind");

179:        require(address(newGnosis) != address(0), "invalid Gnosis address");	// @audit-issue

187:        require(	// @audit-issue
188:            address(newTradeImplementation) != address(0),
189:            "invalid batchTradeImplementation address"
190:        );

198:        require(	// @audit-issue
199:            newAuctionLength == 0 ||
200:                (newAuctionLength >= MIN_AUCTION_LENGTH && newAuctionLength <= MAX_AUCTION_LENGTH),
201:            "invalid batchAuctionLength"
202:        );

209:        require(	// @audit-issue
210:            address(newTradeImplementation) != address(0),
211:            "invalid dutchTradeImplementation address"
212:        );

220:        require(	// @audit-issue
221:            newAuctionLength == 0 ||
222:                (newAuctionLength >= MIN_AUCTION_LENGTH && newAuctionLength <= MAX_AUCTION_LENGTH),
223:            "invalid dutchAuctionLength"
224:        );

244:        require(!batchTradeDisabled, "batch auctions disabled");	// @audit-issue

245:        require(batchAuctionLength != 0, "batch auctions not enabled");	// @audit-issue

273:        require(	// @audit-issue
274:            !dutchTradeDisabled[req.sell.erc20()] && !dutchTradeDisabled[req.buy.erc20()],
275:            "dutch auctions disabled for token pair"
276:        );

277:        require(dutchAuctionLength != 0, "dutch auctions not enabled");	// @audit-issue

278:        require(	// @audit-issue
279:            pricedAtTimestamp(req.sell) && pricedAtTimestamp(req.buy),
280:            "dutch auctions require live prices"
281:        );
```
[86](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L86-L89), [90](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L90-L93), [132](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L132-L137), [151](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L151-L151), [155](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L171-L171), [179](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L179-L179), [187](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L187-L190), [198](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L198-L202), [209](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L209-L212), [220](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L220-L224), [244](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L244-L244), [245](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L245-L245), [273](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L273-L276), [277](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L277-L277), [278](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L278-L281), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

96:        require(address(trade) != address(0), "no trade open");	// @audit-issue

97:        require(trade.canSettle(), "cannot settle yet");	// @audit-issue

126:        assert(address(trades[sell]) == address(0)); // ensure calling class has checked this	// @audit-issue

147:        require(val < MAX_TRADE_SLIPPAGE, "invalid maxTradeSlippage");	// @audit-issue

154:        require(val <= MAX_TRADE_VOLUME, "invalid minTradeVolume");	// @audit-issue
```
[96](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L96-L96), [97](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L97-L97), [126](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L126-L126), [147](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L147-L147), [154](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L154-L154), 


```solidity
Path: ./contracts/p1/mixins/TradeLib.sol

48:        assert(	// @audit-issue
49:            trade.prices.buyHigh != 0 &&
50:                trade.prices.buyHigh != FIX_MAX &&
51:                trade.prices.sellLow != FIX_MAX
52:        );

68:            require(maxSell > 1, "trade sizing error");

71:            require(trade.prices.sellLow == 0, "trade pricing error");

123:        assert(	// @audit-issue
124:            trade.prices.sellLow != 0 &&
125:                trade.prices.sellLow != FIX_MAX &&
126:                trade.prices.buyHigh != 0 &&
127:                trade.prices.buyHigh != FIX_MAX
128:        );
```
[48](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L48-L52), [65](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L68-L68), [65](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L71-L71), [123](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L123-L128), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

34:        require(address(main_) != address(0), "main is zero address");	// @audit-issue

42:        require(!main.tradingPausedOrFrozen(), "frozen or trading paused");	// @audit-issue

47:        require(!main.issuancePausedOrFrozen(), "frozen or issuance paused");	// @audit-issue

52:        require(!main.frozen(), "frozen");	// @audit-issue

57:        require(main.hasRole(OWNER, _msgSender()), "governance only");	// @audit-issue

62:        require(_msgSender() == address(main), "main only");	// @audit-issue
```
[34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L34-L34), [42](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L42-L42), [47](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L47-L47), [52](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L52-L52), [57](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L57-L57), [62](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L62-L62), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

75:        assert(doTrade);	// @audit-issue

117:        require(buPriceLow != 0 && buPriceHigh != FIX_MAX, "BUs unpriced");	// @audit-issue
```
[75](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L75-L75), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L117-L117), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

205:            assert(targetIndex < targetsLength);

340:            require(contains && amt >= targetAmts[i], "new target weights");

344:        require(_targetAmts.length() == 0, "missing target weights");	// @audit-issue

365:            require(coll.status() == CollateralStatus.SOUND, "unsound new collateral");

368:            require(low != 0 && high != FIX_MAX, "invalid price");
```
[198](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L205-L205), [337](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L340-L340), [344](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L344-L344), [363](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L365-L365), [363](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L368-L368), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

59:        require(status == begin, "Invalid trade state");	// @audit-issue

62:        assert(status == TradeStatus.PENDING);	// @audit-issue

91:        require(req.sellAmount <= type(uint96).max, "sellAmount too large");	// @audit-issue

92:        require(req.minBuyAmount <= type(uint96).max, "minBuyAmount too large");	// @audit-issue

99:        require(initBal >= req.sellAmount, "unfunded trade");	// @audit-issue

101:        assert(origin_ != address(0));	// @audit-issue

180:        require(msg.sender == origin, "only origin can settle");	// @audit-issue

188:            assert(isAuctionCleared());

226:        require(status == TradeStatus.CLOSED, "only after trade is closed");	// @audit-issue
```
[59](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L59-L59), [62](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L62-L62), [91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L91-L91), [92](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L92-L92), [99](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L99-L99), [101](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L101-L101), [180](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L180-L180), [183](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L188-L188), [226](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L226-L226), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

122:        require(status == begin, "Invalid trade state");	// @audit-issue

125:        assert(status == TradeStatus.PENDING);	// @audit-issue

165:        assert(address(sell_) != address(0) && address(buy_) != address(0) && auctionLength >= 60);	// @audit-issue

168:        require(prices.sellLow != 0 && prices.sellHigh < FIX_MAX / 1000, "bad sell pricing");	// @audit-issue

169:        require(prices.buyLow != 0 && prices.buyHigh < FIX_MAX / 1000, "bad buy pricing");	// @audit-issue

176:        require(sellAmount_ <= sell.balanceOf(address(this)), "unfunded trade");	// @audit-issue

191:        assert(_worstPrice <= _bestPrice);	// @audit-issue

200:        require(bidder == address(0), "bid already received");	// @audit-issue

201:        assert(status == TradeStatus.OPEN);	// @audit-issue

225:        assert(status == TradeStatus.CLOSED);	// @audit-issue

236:        require(bidder == address(0), "bid already received");	// @audit-issue

237:        assert(status == TradeStatus.OPEN);	// @audit-issue

259:        require(	// @audit-issue
260:            amountIn <= buy.balanceOf(address(this)) - balanceBefore,
261:            "insufficient buy tokens"
262:        );

268:        assert(status == TradeStatus.CLOSED);	// @audit-issue

279:        require(msg.sender == address(origin), "only origin can settle");	// @audit-issue

280:        require(bidder != address(0) || block.timestamp > endTime, "auction not over");	// @audit-issue

299:        require(status == TradeStatus.CLOSED, "only after trade is closed");	// @audit-issue

317:        require(timestamp >= _startTime, "auction not started");	// @audit-issue

318:        require(timestamp <= _endTime, "auction over");	// @audit-issue
```
[122](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L122-L122), [125](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L125-L125), [165](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L165-L165), [168](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L168-L168), [169](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L169-L169), [176](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L176-L176), [191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L191-L191), [200](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L200-L200), [201](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L201-L201), [225](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L225-L225), [236](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L236-L236), [237](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L237-L237), [259](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L259-L262), [268](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L268-L268), [279](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L279-L279), [280](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L280-L280), [299](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L299-L299), [317](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L317-L317), [318](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L318-L318), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

121:        require(startedInSameEra(proposalId), "new era");	// @audit-issue

131:        require(!startedInSameEra(proposalId), "same era");	// @audit-issue

144:        require(startedInSameEra(proposalId), "new era");	// @audit-issue

193:        require(newVotingDelay >= MIN_VOTING_DELAY, "invalid votingDelay");	// @audit-issue
```
[121](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L121-L121), [131](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L131-L131), [144](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L144-L144), [193](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L193-L193), 


#### Recommendation


It is recommended to use custom errors instead of revert strings to reduce gas costs, especially during contract deployment. Custom errors can be defined using the error keyword and can include dynamic information.

### Avoid repeating computations
In Solidity development, repeating the same computations within a contract can lead to unnecessary gas consumption and reduce the contract's efficiency. This is particularly relevant in functions that are called frequently or involve complex calculations. Repeating computations not only wastes computational resources but also increases the cost of executing transactions. By identifying and eliminating redundant calculations, developers can optimize contract performance, reduce gas costs, and improve overall execution speed.

```solidity
Path: ./contracts/libraries/Fixed.sol

324:            if (y & 1 == 1) result = (result * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;	// @audit-issue: Same binary operation statement in line(s) between: ['327:327']

586:            r *= 2 - c_256 * r;	// @audit-issue: Same binary operation statement in line(s) between: ['587:587', '588:588', '589:589', '590:590', '591:591', '592:592', '593:593']
```
[324](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L324-L324), [586](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L586-L586), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

127:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue: Same binary operation statement in line(s) between: ['142:142', '153:153']
```
[127](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L127-L127), 


```solidity
Path: ./contracts/p1/Distributor.sol

125:        require(erc20 == rsr || erc20 == rToken, "RSR or RToken");	// @audit-issue: Same binary operation statement in line(s) between: ['126:126']

157:                if (transferAmt != 0) accountRewards = true;	// @audit-issue: Same binary operation statement in line(s) between: ['160:160']

247:        if (dest == FURNACE) require(share.rsrDist == 0, "Furnace must get 0% of RSR");	// @audit-issue: Same binary operation statement in line(s) between: ['252:252']

248:        if (dest == ST_RSR) require(share.rTokenDist == 0, "StRSR must get 0% of RToken");	// @audit-issue: Same binary operation statement in line(s) between: ['252:252']
```
[125](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L125-L125), [157](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L157-L157), [247](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L247-L247), [248](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L248-L248), 


```solidity
Path: ./contracts/p1/BackingManager.sol

221:        uint192 baskets = (basketsHeld.bottom.div(FIX_ONE + backingBuffer));	// @audit-issue: Same binary operation statement in line(s) between: ['226:226']

289:        for (uint256 i = 0; i < reg.erc20s.length; ++i) {	// @audit-issue: Same binary operation statement in line(s) between: ['294:294']
```
[221](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L221-L221), [289](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L289-L289), 


```solidity
Path: ./contracts/p1/StRSR.sol

313:        require(queue[endId - 1].availableAt <= block.timestamp, "withdrawal unavailable");	// @audit-issue: Same binary operation statement in line(s) between: ['318:318']

441:        uint256 stakeRSRToTake = (stakeRSR * rsrAmount + (rsrBalance - 1)) / rsrBalance;	// @audit-issue: Same binary operation statement in line(s) between: ['456:456', '472:472']
```
[313](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L313-L313), [441](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L441-L441), 


```solidity
Path: ./contracts/p1/RToken.sol

297:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue: Same binary operation statement in line(s) between: ['321:321']

311:        for (uint256 i = 0; i < expectedERC20sOut.length; ++i) {	// @audit-issue: Same binary operation statement in line(s) between: ['339:339']

409:        uint256 low = (FIX_ONE_256 * basketsNeeded_) / supply; // D18{BU/rTok}	// @audit-issue: Same binary operation statement in line(s) between: ['410:410']
```
[297](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L297-L297), [311](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L311-L311), [409](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L409-L409), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

141:            if (ckpts[mid].fromTimepoint > timepoint) {	// @audit-issue: Same binary operation statement in line(s) between: ['150:150']

144:                low = mid + 1;	// @audit-issue: Same binary operation statement in line(s) between: ['153:153']

265:            Checkpoint memory oldCkpt = pos == 0 ? Checkpoint(0, 0) : ckpts[pos - 1];	// @audit-issue: Same binary operation statement in line(s) between: ['271:271']
```
[141](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L141-L141), [144](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L144-L144), [265](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L265-L265), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

176:                        uint256(high.safeMulDiv(ctx.bals[i] - anchor, buPriceLow, CEIL))	// @audit-issue: Same binary operation statement in line(s) between: ['189:189']

388:            return other == CollateralStatus.DISABLED && surplusAmt.gt(curr.surplus);	// @audit-issue: Same binary operation statement in line(s) between: ['391:391']
```
[176](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L176-L176), [388](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L388-L388), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

180:        for (uint256 i = 0; i < config.erc20s.length; ++i) {	// @audit-issue: Same binary operation statement in line(s) between: ['198:198']

252:            for (uint256 j = 0; j < backup.erc20s.length && size < backup.max; ++j) {	// @audit-issue: Same binary operation statement in line(s) between: ['263:263']

363:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue: Same binary operation statement in line(s) between: ['380:380']
```
[180](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L180-L180), [252](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L252-L252), [363](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L363-L363), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

168:        require(prices.sellLow != 0 && prices.sellHigh < FIX_MAX / 1000, "bad sell pricing");	// @audit-issue: Same binary operation statement in line(s) between: ['169:169']
```
[168](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L168-L168), 


#### Recommendation

Review your Solidity contracts to identify any computations that are performed multiple times with the same inputs. Cache the results of these computations in local variables and reuse them within the function or across function calls if the state remains unchanged.

### Unused State Variables
There are state variables which are declared but not used in any function. This might increase the contract's gas usage unnecessarily.

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
Path: ./contracts/p1/StRSR.sol

48:    uint8 public constant decimals = 18;	// @audit-issue
```
[48](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L48-L48), 


```solidity
Path: ./contracts/p1/Deployer.sol

31:    string public constant ENS = "reserveprotocol.eth";	// @audit-issue
```
[31](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L31-L31), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

24:    TradeKind public constant KIND = TradeKind.BATCH_AUCTION;	// @audit-issue
```
[24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L24-L24), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

95:    TradeKind public constant KIND = TradeKind.DUTCH_AUCTION;	// @audit-issue
```
[95](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L95-L95), 


#### Recommendation

To optimize your contract's gas usage, remove any state variables that are declared but not used in any function. Unnecessary state variables can increase gas costs and should be eliminated during code review.

### Unused `struct`s
Note that there may be cases where a struct superficially appears to be used, but this is only because there are multiple definitions of the struct in different files. In such cases, the struct definition should be moved into a separate file. The instances below are the unused definitions.

```solidity
Path: ./contracts/p1/Distributor.sol

100:    struct Transfer {	// @audit-issue
101:        address addrTo;
102:        uint256 amount;
103:    }
```
[100](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L100-L103), 


#### Recommendation

Identify and remove any unused `struct` definitions from your Solidity contracts. If a `struct` is defined multiple times across different files, centralize its definition in a single file and import it wherever required. This approach reduces code redundancy, ensures consistency in data structures, and helps maintain a clean and efficient codebase. Regularly auditing your contracts for unused or duplicated code elements like `structs` is a good practice for optimal contract maintenance and development.

### Unused `error` definition
Note that there may be cases where an error superficially appears to be used, but this is only because there are multiple definitions of the error in different files. In such cases, the error definition should be moved into a separate file. The instances below are the unused definitions.


```solidity
Path: ./contracts/registry/VersionRegistry.sol

21:    error VersionRegistry__InvalidRoleRegistry();	// @audit-issue
```
[21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L21-L21), 


#### Recommendation

Unused `error` definitions should be removed from the contract, and if needed, consolidated into a separate file to avoid duplication.

### Using Prefix Operators Costs Less Gas Than Postfix Operators in Loops
Conditions can be optimized issues in Solidity refer to situations where smart contract developers write conditional statements that can be simplified or optimized for better gas efficiency, readability, and maintainability. Optimizing conditions can lead to more cost-effective and secure smart contracts.

```solidity
Path: ./contracts/libraries/String.sol

14:        for (uint256 i = 0; i < bStr.length; i++) {	// @audit-issue
```
[14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/String.sol#L14-L14), 


```solidity
Path: ./contracts/p1/StRSR.sol

675:        era++;	// @audit-issue

687:        draftEra++;	// @audit-issue
```
[675](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L675-L675), [687](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L687-L687), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

100:        tradesOpen--;	// @audit-issue
```
[100](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L100-L100), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

253:                if (goodCollateral(_targetName, backup.erc20s[j], assetRegistry)) size++;	// @audit-issue

274:                    assigned++;	// @audit-issue
```
[253](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L253-L253), [274](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L274-L274), 


#### Recommendation

To improve gas efficiency in your Solidity code, prefer using prefix operators (e.g., `++i` or `--i`) instead of postfix operators (e.g., `i++` or `i--`) within loops. Prefix operators typically result in lower gas costs and can contribute to more efficient contract execution.

### Optimization of Loop Control for Early Termination
The identified issue is a common inefficiency in programming related to loop control during search operations. In the current implementation, loops are designed to traverse through the entire dataset (like arrays or lists) even after the required condition has been met or the target element has been found. This lack of early termination results in unnecessary processing and can lead to increased execution times, particularly in large datasets. The issue is not about the correctness of the function but its efficiency, as continuing the loop after meeting the required condition is redundant and wastes computational resources.

```solidity
Path: ./contracts/p1/RToken.sol

297:        for (uint256 i = 0; i < erc20s.length; ++i) {
298:            // {qTok} = {qTok} * {qRTok} / {qRTok}
299:            uint256 prorata = mulDiv256(
300:                IERC20(erc20s[i]).balanceOf(address(backingManager)),
301:                amount,
302:                supply
303:            ); // FLOOR
304:
305:            if (prorata < amounts[i]) amounts[i] = prorata;	// @audit-issue
```
[305](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L297-L305), 


#### Recommendation

To optimize such functions, it is recommended to incorporate an early exit mechanism within the loop. This can be achieved by introducing a `break` statement (or an equivalent control structure) immediately after the condition is satisfied or the target element is located. This approach ensures that the loop terminates as soon as its objective is achieved, preventing further unnecessary iterations. Implementing this optimization will enhance the performance, especially in scenarios involving large data sets or resource-intensive operations, by minimizing the time and resources expended on redundant processing. This optimization strategy is applicable and beneficial across various programming languages and scenarios where loop-based search or check operations are utilized.

### Do not calculate constants
Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

```solidity
Path: ./contracts/libraries/Fixed.sol

311:    uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;	// @audit-issue
```
[311](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L311-L311), 


```solidity
Path: ./contracts/p1/BackingManager.sol

33:    uint48 public constant MAX_TRADING_DELAY = 60 * 60 * 24 * 365; // {s} 1 year	// @audit-issue
```
[33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L33-L33), 


```solidity
Path: ./contracts/p1/StRSR.sol

40:    uint48 private constant MIN_UNSTAKING_DELAY = 60 * 2; // {s} 2 minutes	// @audit-issue

41:    uint48 private constant MAX_UNSTAKING_DELAY = 60 * 60 * 24 * 365; // {s} 1 year	// @audit-issue

68:    uint192 private constant MAX_STAKE_RATE = 1e9 * FIX_ONE; // 1e9 D18{qStRSR/qRSR}	// @audit-issue

90:    uint192 private constant MAX_DRAFT_RATE = 1e9 * FIX_ONE; // 1e9 D18{qDrafts/qRSR}	// @audit-issue

164:    uint192 private constant MAX_SAFE_STAKE_RATE = 1e6 * FIX_ONE; // 1e6   D18{qStRSR/qRSR}	// @audit-issue
```
[40](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L40-L40), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L41-L41), [68](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L68-L68), [90](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L90-L90), [164](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L164-L164), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

31:    uint192 public constant MAX_TARGET_AMT = 1e3 * FIX_ONE; // {target/BU} max basket weight	// @audit-issue

33:    uint48 public constant MAX_WARMUP_PERIOD = 60 * 60 * 24 * 365; // {s} 1 year	// @audit-issue
```
[31](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L31-L31), [33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L33-L33), 


```solidity
Path: ./contracts/p1/Broker.sol

25:    uint48 public constant MAX_AUCTION_LENGTH = 60 * 60 * 24 * 7; // {s} max valid duration, 1 week	// @audit-issue
```
[25](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L25-L25), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

32:    uint192 public constant DEFAULT_MIN_BID = FIX_ONE / 100; // {tok}	// @audit-issue
```
[32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L32-L32), 


#### Recommendation

Precompute and assign literal values to constant variables in your Solidity contracts. Avoid defining constants with expressions or calculations. By providing direct values, you ensure that the compiler replaces these constants efficiently in the bytecode, maximizing gas savings and contract performance. Review your contracts to identify any constants defined with expressions and refactor them to use precomputed literal values instead.

### Multiple accesses of a mapping/array should use a local variable cache
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local `storage` or `calldata` variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves **~42 gas per access** due to not having to recalculate the key's keccak256 hash (Gkeccak256 - **30 gas**) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata

```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

75:            emit AssetPluginRegistryUpdated(versionHash, _asset, _validities[i]);	// @audit-issue: Same index access of variable `_validities` is used also on line(s): ['73'].

101:            _isValidAsset[_versionHash][asset] = _validities[i];	// @audit-issue: Same index access of variable `_validities` is used also on line(s): ['103'].
```
[75](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L75-L75), [101](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L101-L101), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

129:            if (erc20s[i] == tokenToBuy) {	// @audit-issue: Same index access of variable `erc20s` is used also on line(s): ['143', '128', '154'].
```
[129](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L129-L129), 


```solidity
Path: ./contracts/p1/Distributor.sol

149:                ? distribution[addrTo].rsrDist	// @audit-issue: Same index access of variable `distribution` is used also on line(s): ['150'].
```
[149](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L149-L149), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

137:        return ICollateral(address(assets[erc20]));	// @audit-issue: Same index access of variable `assets` is used also on line(s): ['136'].

234:            if (assets[erc20] == asset) return false;	// @audit-issue: Same index access of variable `assets` is used also on line(s): ['235'].
```
[137](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L137-L137), [234](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L234-L234), 


```solidity
Path: ./contracts/p1/BackingManager.sol

254:                    erc20s[i].safeTransfer(address(rsrTrader), tokensPerShare * totals.rsrTotal);	// @audit-issue: Same index access of variable `erc20s` is used also on line(s): ['257', '241', '237'].
```
[254](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L254-L254), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

266:            config.erc20s.push(erc20s[i]);	// @audit-issue: Same index access of variable `erc20s` is used also on line(s): ['268', '269', '263', '267'].

299:            assetRegistry.toColl(erc20s[i]); // reverts if not collateral	// @audit-issue: Same index access of variable `erc20s` is used also on line(s): ['300'].

426:                uint192 qty = _quantity(basket.erc20s[i], coll, CEIL);	// @audit-issue: Same index access of variable `basket` is used also on line(s): ['425'].

508:                int8(IERC20Metadata(address(basket.erc20s[i])).decimals()),	// @audit-issue: Same index access of variable `basket` is used also on line(s): ['496'].

620:            uint192 q = _quantity(basket.erc20s[i], coll, CEIL);	// @audit-issue: Same index access of variable `basket` is used also on line(s): ['616'].

686:                erc20s[i] != rsr &&	// @audit-issue: Same index access of variable `erc20s` is used also on line(s): ['688', '689', '687'].
```
[266](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L266-L266), [299](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L299-L299), [426](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L426-L426), [508](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L508-L508), [620](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L620-L620), [686](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L686-L686), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

88:        return pos == 0 ? 0 : _checkpoints[era][account][pos - 1].val;	// @audit-issue: Same index access of variable `_checkpoints` is used also on line(s): ['87'].

247:                    _checkpoints[era][dst],	// @audit-issue: Same index access of variable `_checkpoints` is used also on line(s): ['238'].
```
[88](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L88-L88), [247](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L247-L247), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

341:            if (amt > targetAmts[i]) _targetAmts.set(targetName, amt - targetAmts[i]);	// @audit-issue: Same index access of variable `targetAmts` is used also on line(s): ['341', '340'].

371:            newPrice += targetAmts[i].mulDiv(	// @audit-issue: Same index access of variable `targetAmts` is used also on line(s): ['382'].
```
[341](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L341-L341), [371](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L371-L371), 


#### Recommendation

When a mapping or array value is accessed multiple times within a function, cache it in a local `storage` or `calldata` variable. This approach minimizes the gas cost by reducing the number of hash computations for mappings and offset calculations for arrays. Carefully review your functions to identify opportunities for such optimizations, especially in functions with frequent or repeated accesses to the same mapping or array element, to enhance gas efficiency in your contracts.

### State Variables That Are Used Multiple Times In a Function Should Be Cached In Stack Variables
When performing multiple operations on a state variable in a function, it is recommended to cache it first. Either multiple reads or multiple writes to a state variable can save gas by caching it on the stack. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses. Saves 100 gas per instance.


```solidity
Path: ./contracts/mixins/Auth.sol

215:        require(newUnfreezeAt > unfreezeAt, "frozen");	// @audit-issue: State variable `unfreezeAt` is used also on line(s): ['216'].
```
[215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L215-L215), 


```solidity
Path: ./contracts/libraries/Throttle.sol

89:        if (params.amtRate > limit) limit = params.amtRate;	// @audit-issue: State variable `params` is used also on line(s): ['89'].
```
[89](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L89-L89), 


```solidity
Path: ./contracts/p1/Main.sol

135:        _upgradeProxy(address(broker), address(implementation.components.broker));	// @audit-issue: State variable `broker` is used also on line(s): ['147', '144'].
```
[135](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L135-L135), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

80:        } else if (address(tokenToBuy) == address(rToken)) {	// @audit-issue: State variable `tokenToBuy` is used also on line(s): ['78'].

120:                (address(tokenToBuy) == address(rToken) && revTotals.rTokenTotal != 0),	// @audit-issue: State variable `tokenToBuy` is used also on line(s): ['129', '119', '136', '126', '155'].

126:        bool involvesRToken = tokenToBuy == IERC20(address(rToken));	// @audit-issue: State variable `rToken` is used also on line(s): ['120', '128'].
```
[80](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L80-L80), [120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L120-L120), [126](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L126-L126), 


```solidity
Path: ./contracts/p1/Distributor.sol

126:        bool isRSR = erc20 == rsr; // if false: isRToken	// @audit-issue: State variable `rsr` is used also on line(s): ['125'].

180:                (address recipient, , ) = main.daoFeeRegistry().getFeeDetails(address(rToken));	// @audit-issue: State variable `rToken` is used also on line(s): ['125'].

149:                ? distribution[addrTo].rsrDist	// @audit-issue: State variable `distribution` is used also on line(s): ['150'].
```
[126](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L126-L126), [180](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L180-L180), [149](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L149-L149), 


```solidity
Path: ./contracts/p1/Furnace.sol

69:        uint48 numPeriods = uint48((block.timestamp) - lastPayout);	// @audit-issue: State variable `lastPayout` is used also on line(s): ['66'].
```
[69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L69-L69), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

137:        return ICollateral(address(assets[erc20]));	// @audit-issue: State variable `assets` is used also on line(s): ['136'].

234:            if (assets[erc20] == asset) return false;	// @audit-issue: State variable `assets` is used also on line(s): ['235'].
```
[137](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L137-L137), [234](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L234-L234), 


```solidity
Path: ./contracts/p1/BackingManager.sol

73:        IERC20(address(erc20)).safeApprove(address(rToken), type(uint256).max);	// @audit-issue: State variable `rToken` is used also on line(s): ['72'].

221:        uint192 baskets = (basketsHeld.bottom.div(FIX_ONE + backingBuffer));	// @audit-issue: State variable `backingBuffer` is used also on line(s): ['226'].

283:        ctx.stRSR = stRSR;	// @audit-issue: State variable `stRSR` is used also on line(s): ['298'].

284:        ctx.rsr = rsr;	// @audit-issue: State variable `rsr` is used also on line(s): ['298'].
```
[73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L73-L73), [221](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L221-L221), [283](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L283-L283), [284](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L284-L284), 


```solidity
Path: ./contracts/p1/StRSR.sol

274:        uint256 newStakeRSR = (FIX_ONE_256 * totalStakes + (stakeRate - 1)) / stakeRate;	// @audit-issue: State variable `stakeRate` is used also on line(s): ['274'].

321:        firstRemainingDraft[draftEra][account] = endId;	// @audit-issue: State variable `draftEra` is used also on line(s): ['338', '307', '308'].

326:        uint256 newDraftRSR = (newTotalDrafts * FIX_ONE_256 + (draftRate - 1)) / draftRate;	// @audit-issue: State variable `draftRate` is used also on line(s): ['326'].

318:        uint192 draftAmount = queue[endId - 1].drafts - oldDrafts;	// @audit-issue: State variable `queue` is used also on line(s): ['313'].

353:        uint256 firstId = firstRemainingDraft[draftEra][account];	// @audit-issue: State variable `draftEra` is used also on line(s): ['368', '354', '384'].

373:        uint256 newDraftRSR = (newTotalDrafts * FIX_ONE_256 + (draftRate - 1)) / draftRate;	// @audit-issue: State variable `draftRate` is used also on line(s): ['373'].

448:            stakeRate = uint192((FIX_ONE_256 * totalStakes + (stakeRSR - 1)) / stakeRSR);	// @audit-issue: State variable `stakeRSR` is used also on line(s): ['448', '446', '450', '441', '451'].

463:            draftRate = uint192((FIX_ONE_256 * totalDrafts + (draftRSR - 1)) / draftRSR);	// @audit-issue: State variable `draftRSR` is used also on line(s): ['466', '461', '467', '456', '463'].

493:            stakeRate <= MIN_SAFE_STAKE_RATE || stakeRate >= MAX_SAFE_STAKE_RATE,	// @audit-issue: State variable `stakeRate` is used also on line(s): ['493'].

504:        return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method	// @audit-issue: State variable `stakeRate` is used also on line(s): ['504'].

517:        (uint256 left, uint256 right) = (firstRemainingDraft[draftEra][account], queue.length);	// @audit-issue: State variable `draftEra` is used also on line(s): ['514'].

594:        if (block.timestamp < payoutLastPaid + 1) return;	// @audit-issue: State variable `payoutLastPaid` is used also on line(s): ['595'].

621:        stakeRate = (stakeRSR == 0 || totalStakes == 0)	// @audit-issue: State variable `totalStakes` is used also on line(s): ['623', '601'].

623:            : uint192((totalStakes * FIX_ONE_256 + (stakeRSR - 1)) / stakeRSR);	// @audit-issue: State variable `stakeRSR` is used also on line(s): ['621', '623'].

656:        uint192 oldDrafts = index != 0 ? queue[index - 1].drafts : 0;	// @audit-issue: State variable `queue` is used also on line(s): ['657'].

711:        if (leaked > withdrawalLeak) {	// @audit-issue: State variable `leaked` is used also on line(s): ['708'].
```
[274](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L274-L274), [321](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L321-L321), [326](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L326-L326), [318](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L318-L318), [353](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L353-L353), [373](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L373-L373), [448](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L448-L448), [463](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L463-L463), [493](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L493-L493), [504](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L504-L504), [517](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L517-L517), [594](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L594-L594), [621](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L621-L621), [623](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L623-L623), [656](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L656-L656), [711](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L711-L711), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

140:        uint256 len = basket.erc20s.length;	// @audit-issue: State variable `basket` is used also on line(s): ['142', '143'].

181:            emit BasketStatusChanged(lastStatus, currentStatus);	// @audit-issue: State variable `lastStatus` is used also on line(s): ['180'].

187:        if (reweightable && nonce > lastCollateralized && fullyCollateralized()) {	// @audit-issue: State variable `nonce` is used also on line(s): ['188', '189'].

188:            emit LastCollateralizedChanged(lastCollateralized, nonce);	// @audit-issue: State variable `lastCollateralized` is used also on line(s): ['187'].

237:            (!reweightable || (reweightable && !disableTargetAmountCheck)) &&	// @audit-issue: State variable `reweightable` is used also on line(s): ['237'].

253:            delete config.targetNames[config.erc20s[i]];	// @audit-issue: State variable `config` is used also on line(s): ['238', '252'].

423:        uint256 len = basket.erc20s.length;	// @audit-issue: State variable `basket` is used also on line(s): ['425', '426'].

426:                uint192 qty = _quantity(basket.erc20s[i], coll, CEIL);	// @audit-issue: State variable `basket` is used also on line(s): ['425'].

492:            erc20s[i] = address(basket.erc20s[i]);	// @audit-issue: State variable `basket` is used also on line(s): ['487'].

508:                int8(IERC20Metadata(address(basket.erc20s[i])).decimals()),	// @audit-issue: State variable `basket` is used also on line(s): ['496'].

550:                if (address(b.erc20s[j]) == address(0)) continue;	// @audit-issue: State variable `b` is used also on line(s): ['566', '555', '562', '569'].

620:            uint192 q = _quantity(basket.erc20s[i], coll, CEIL);	// @audit-issue: State variable `basket` is used also on line(s): ['611', '616'].

620:            uint192 q = _quantity(basket.erc20s[i], coll, CEIL);	// @audit-issue: State variable `basket` is used also on line(s): ['616'].

679:        emit BasketSet(nonce, basket.erc20s, refAmts, disabled);	// @audit-issue: State variable `nonce` is used also on line(s): ['668'].

679:        emit BasketSet(nonce, basket.erc20s, refAmts, disabled);	// @audit-issue: State variable `basket` is used also on line(s): ['674', '677'].

710:        erc20s = new IERC20[](b.erc20s.length);	// @audit-issue: State variable `b` is used also on line(s): ['714'].

752:            erc20s[i] = config.erc20s[i];	// @audit-issue: State variable `config` is used also on line(s): ['747'].

768:        erc20s = new IERC20[](backup.erc20s.length);	// @audit-issue: State variable `backup` is used also on line(s): ['770'].
```
[140](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L140-L140), [181](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L181-L181), [187](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L187-L187), [188](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L188-L188), [237](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L237-L237), [253](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L253-L253), [423](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L423-L423), [426](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L426-L426), [492](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L492-L492), [508](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L508-L508), [550](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L550-L550), [620](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L620-L620), [620](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L620-L620), [679](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L679-L679), [679](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L679-L679), [710](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L710-L710), [752](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L752-L752), [768](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L768-L768), 


```solidity
Path: ./contracts/p1/Deployer.sol

156:                    new ERC1967Proxy(address(_implementations.components.distributor), new bytes(0))	// @audit-issue: State variable `_implementations` is used also on line(s): ['167', '178', '141', '127', '174', '149', '133', '161', '123'].

222:            _implementations.trading.dutchTrade,	// @audit-issue: State variable `_implementations` is used also on line(s): ['220'].
```
[156](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L156-L156), [222](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L222-L222), 


```solidity
Path: ./contracts/p1/RToken.sol

328:                    address(backingManager),	// @audit-issue: State variable `backingManager` is used also on line(s): ['300'].

357:        require(_msgSender() == address(backingManager), "not backing manager");	// @audit-issue: State variable `backingManager` is used also on line(s): ['358'].

492:        emit BasketsNeededChanged(basketsNeeded, basketsNeeded + amtBaskets);	// @audit-issue: State variable `basketsNeeded` is used also on line(s): ['492', '490'].
```
[328](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L328-L328), [357](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L357-L357), [492](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L492-L492), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

88:        return pos == 0 ? 0 : _checkpoints[era][account][pos - 1].val;	// @audit-issue: State variable `era` is used also on line(s): ['87'].

88:        return pos == 0 ? 0 : _checkpoints[era][account][pos - 1].val;	// @audit-issue: State variable `_checkpoints` is used also on line(s): ['87'].

238:                    _checkpoints[era][src],	// @audit-issue: State variable `era` is used also on line(s): ['247'].

247:                    _checkpoints[era][dst],	// @audit-issue: State variable `_checkpoints` is used also on line(s): ['238'].
```
[88](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L88-L88), [88](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L88-L88), [238](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L238-L238), [247](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L247-L247), 


```solidity
Path: ./contracts/p1/Broker.sol

264:        trade.init(this, caller, gnosis, batchAuctionLength, req);	// @audit-issue: State variable `batchAuctionLength` is used also on line(s): ['245'].

293:        trade.init(caller, req.sell, req.buy, req.sellAmount, dutchAuctionLength, prices);	// @audit-issue: State variable `dutchAuctionLength` is used also on line(s): ['277'].
```
[264](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L264-L264), [293](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L293-L293), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

253:                if (goodCollateral(_targetName, backup.erc20s[j], assetRegistry)) size++;	// @audit-issue: State variable `backup` is used also on line(s): ['264', '273', '263', '252'].

263:            for (uint256 j = 0; j < backup.erc20s.length && assigned < size; ++j) {	// @audit-issue: State variable `backup` is used also on line(s): ['252'].

253:                if (goodCollateral(_targetName, backup.erc20s[j], assetRegistry)) size++;	// @audit-issue: State variable `backup` is used also on line(s): ['264', '273'].
```
[253](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L253-L253), [263](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L263-L263), [253](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L253-L253), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

59:        require(status == begin, "Invalid trade state");	// @audit-issue: State variable `status` is used also on line(s): ['62'].

147:            endTime,	// @audit-issue: State variable `endTime` is used also on line(s): ['148'].

206:        if (boughtAmt != 0) IERC20Upgradeable(address(buy)).safeTransfer(origin, boughtAmt);	// @audit-issue: State variable `origin` is used also on line(s): ['205', '180'].

209:        if (sellBal < initBal) {	// @audit-issue: State variable `initBal` is used also on line(s): ['210'].
```
[59](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L59-L59), [147](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L147-L147), [206](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L206-L206), [209](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L209-L209), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

125:        assert(status == TradeStatus.PENDING);	// @audit-issue: State variable `status` is used also on line(s): ['122'].

201:        assert(status == TradeStatus.OPEN);	// @audit-issue: State variable `status` is used also on line(s): ['225'].

255:        sell.safeTransfer(bidder, lot()); // {qSellTok}	// @audit-issue: State variable `bidder` is used also on line(s): ['258', '236'].

268:        assert(status == TradeStatus.CLOSED);	// @audit-issue: State variable `status` is used also on line(s): ['237'].

292:        sell.safeTransfer(address(origin), sell.balanceOf(address(this))); // {qSellTok}	// @audit-issue: State variable `origin` is used also on line(s): ['279', '291'].

280:        require(bidder != address(0) || block.timestamp > endTime, "auction not over");	// @audit-issue: State variable `bidder` is used also on line(s): ['286'].

282:        if (bidType == BidType.CALLBACK) {	// @audit-issue: State variable `bidType` is used also on line(s): ['284'].

359:        return worstPrice;	// @audit-issue: State variable `worstPrice` is used also on line(s): ['355'].
```
[125](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L125-L125), [201](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L201-L201), [255](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L255-L255), [268](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L268-L268), [292](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L292-L292), [280](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L280-L280), [282](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L282-L282), [359](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L359-L359), 


#### Recommendation

Cache state variables in stack or local memory variables within functions when they are used multiple times. This approach replaces costlier Gwarmaccess operations with cheaper stack reads, saving approximately 100 gas per instance and optimizing overall contract performance.

### Missing Constant Modifier For State Variables
State variables that are never re-assigned after their initial assignment should typically be declared with the `constant` modifier. This ensures that these variables remain unmodified and communicates their unchanging nature. Using the `constant` modifier also offers potential gas savings, as accessing these variables does not require any storage operations.

```solidity
Path: ./contracts/p1/BasketHandler.sol

49:    Basket private basket;	// @audit-issue

63:    Basket private _newBasket;	// @audit-issue

83:    EnumerableMap.Bytes32ToUintMap private _targetAmts; // targetName -> {target/BU}	// @audit-issue
```
[49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L49-L49), [63](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L63-L63), [83](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L83-L83), 


#### Recommendation

Declare state variables that are not reassigned after initialization with the 'constant' modifier in Solidity. This clarifies their unchanging nature, improves code readability, and saves gas by avoiding storage operations when accessing these variables.

### State Variables Only Set in The Constructor Should Be Declared `immutable`
Avoids a Gsset(** 20000 gas**) in the constructor, and replaces the first access in each transaction(Gcoldsload - ** 2100 gas **) and each access thereafter(Gwarmacces - ** 100 gas **) with a`PUSH32`(** 3 gas **).

While`string`s are not value types, and therefore cannot be`immutable` / `constant` if not hard - coded outside of the constructor, the same behavior can be achieved by making the current contract `abstract` with `virtual` functions for the`string` accessors, and having a child contract override the functions with the hard - coded implementation - specific values.

```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

10:    RoleRegistry public roleRegistry;	// @audit-issue
```
[10](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L10-L10), 


```solidity
Path: ./contracts/registry/VersionRegistry.sol

16:    RoleRegistry public roleRegistry;	// @audit-issue
```
[16](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L16-L16), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

13:    VersionRegistry public versionRegistry;	// @audit-issue

14:    RoleRegistry public roleRegistry;	// @audit-issue
```
[13](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L13-L13), [14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L14-L14), 


```solidity
Path: ./contracts/p1/Deployer.sol

38:    Implementations private _implementations;	// @audit-issue
```
[38](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L38-L38), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

35:    TradeStatus public status;	// @audit-issue
```
[35](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L35-L35), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

99:    TradeStatus public status; // reentrancy protection	// @audit-issue
```
[99](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L99-L99), 


#### Recommendation

Consider marking state variables as an immutable that never changes on the contract.

### Increments can be `unchecked` in for-loops
Newer versions of the Solidity compiler will check for integer overflows and underflows automatically. This provides safety but increases gas costs.
When an unsigned integer is guaranteed to never overflow, the unchecked feature of Solidity can be used to save gas costs.A common case for this is for-loops using a strictly-less-than comparision in their conditional statement.

```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

39:        for (uint256 i = 0; i < validForVersions.length; ++i) {	// @audit-issue

67:        for (uint256 i = 0; i < _versionHashes.length; ++i) {	// @audit-issue

95:        for (uint256 i = 0; i < _assets.length; ++i) {	// @audit-issue
```
[39](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L39-L39), [67](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L67-L67), [95](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L95-L95), 


```solidity
Path: ./contracts/libraries/Array.sol

11:        for (uint256 i = 1; i < arrLen; ++i) {	// @audit-issue

12:            for (uint256 j = 0; j < i; ++j) {	// @audit-issue

23:        for (uint256 i = 1; i < arrLen; ++i) {	// @audit-issue
```
[11](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L11-L11), [12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L12-L12), [23](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L23-L23), 


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

535:        for (uint48 i = 0; i < basketNonces.length; ++i) {	// @audit-issue

547:            for (uint256 j = 0; j < b.erc20s.length; ++j) {	// @audit-issue

554:                for (uint256 k = 0; k < len; ++k) {	// @audit-issue

593:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

615:        for (uint256 i = 0; i < length; ++i) {	// @audit-issue

676:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

684:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

713:        for (uint256 i = 0; i < b.erc20s.length; ++i) {	// @audit-issue

751:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

769:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue
```
[142](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L142-L142), [251](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L251-L251), [260](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L260-L260), [296](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L296-L296), [321](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L321-L321), [424](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L424-L424), [491](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L491-L491), [535](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L535-L535), [547](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L547-L547), [554](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L554-L554), [593](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L593-L593), [615](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L615-L615), [676](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L676-L676), [684](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L684-L684), [713](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L713-L713), [751](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L751-L751), [769](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L769-L769), 


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

202:            for (targetIndex = 0; targetIndex < targetsLength; ++targetIndex) {	// @audit-issue

241:        for (uint256 i = 0; i < targetsLength; ++i) {	// @audit-issue

252:            for (uint256 j = 0; j < backup.erc20s.length && size < backup.max; ++j) {	// @audit-issue

263:            for (uint256 j = 0; j < backup.erc20s.length && assigned < size; ++j) {	// @audit-issue

325:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

337:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

363:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

380:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue
```
[72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L72-L72), [80](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L80-L80), [180](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L180-L180), [198](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L198-L198), [202](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L202-L202), [241](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L241-L241), [252](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L252-L252), [263](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L263-L263), [325](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L325-L325), [337](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L337-L337), [363](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L363-L363), [380](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L380-L380), 


#### Recommendation

Use unchecked math to block overflow / underflow check to save Gas.

### Divisions can be unchecked to save gas
The expression type(int).min/(-1) is the only case where division causes an overflow. Therefore, uncheck can be used to save gas in scenarios where it is certain that such an overflow will not occur.

```solidity
Path: ./contracts/libraries/Throttle.sol

75:        available = throttle.lastAvailable + (limit * delta) / ONE_HOUR;	// @audit-issue

88:        limit = (supply * params.pctRate) / FIX_ONE_256; // {qRTok}	// @audit-issue
```
[75](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L75-L75), [88](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L88-L88), 


```solidity
Path: ./contracts/libraries/Fixed.sol

120:    if (x < uint256(type(uint256).max / FIX_SCALE_SQ)) {	// @audit-issue

121:        return _safeWrap(uint256(x * FIX_SCALE_SQ) / y);	// @audit-issue

160:    uint256 result = numerator / divisor;	// @audit-issue

165:        if (numerator % divisor > (divisor - 1) / 2) {	// @audit-issue

324:            if (y & 1 == 1) result = (result * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;	// @audit-issue

327:            x = (x * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;	// @audit-issue

329:        return _safeWrap(result / FIX_SCALE);	// @audit-issue

663:        if (mm > ((z - 1) / 2)) result += 1; // z should be z-1	// @audit-issue
```
[120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L120-L120), [121](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L121-L121), [160](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L160-L160), [165](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L165-L165), [324](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L324-L324), [327](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L327-L327), [329](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L329-L329), [663](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L663-L663), 


```solidity
Path: ./contracts/p1/Distributor.sol

133:            if (totalShares != 0) tokensPerShare = amount / totalShares;	// @audit-issue

222:                    (feeNumerator * uint256(revTotals.rTokenTotal + revTotals.rsrTotal)) /	// @audit-issue
```
[133](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L133-L133), [222](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L222-L222), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

260:            gas > (64 * GAS_FOR_BH_QTY) / 63 + GAS_FOR_DISABLE_BASKET,	// @audit-issue
```
[260](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L260-L260), 


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

530:            test = (left + right) / 2; // left < test < right because left < right - 1	// @audit-issue

609:            payout = (payoutRatio * rsrRewardsAtLastPayout) / FIX_ONE;	// @audit-issue

623:            : uint192((totalStakes * FIX_ONE_256 + (stakeRSR - 1)) / stakeRSR);	// @audit-issue

648:        uint256 newTotalDrafts = (draftRate * draftRSR) / FIX_ONE;	// @audit-issue

705:        uint192 withdrawal = _safeWrap((rsrWithdrawal * FIX_ONE + totalRSR - 1) / totalRSR); // {1}	// @audit-issue

729:        uint256 newTotalStakes = (stakeRate * newStakeRSR) / FIX_ONE;	// @audit-issue
```
[274](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L274-L274), [326](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L326-L326), [373](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L373-L373), [441](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L441-L441), [448](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L448-L448), [456](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L456-L456), [463](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L463-L463), [472](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L472-L472), [504](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L504-L504), [530](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L530-L530), [609](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L609-L609), [623](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L623-L623), [648](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L648-L648), [705](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L705-L705), [729](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L729-L729), 


```solidity
Path: ./contracts/p1/RToken.sol

409:        uint256 low = (FIX_ONE_256 * basketsNeeded_) / supply; // D18{BU/rTok}	// @audit-issue

410:        uint256 high = (FIX_ONE_256 * basketsNeeded_ + (supply - 1)) / supply; // D18{BU/rTok}	// @audit-issue
```
[409](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L409-L409), [410](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L410-L410), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

372:                (low + high) / 2,	// @audit-issue
```
[372](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L372-L372), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

126:            minBuyAmount / MAX_ORDERS,	// @audit-issue
```
[126](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L126-L126), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

168:        require(prices.sellLow != 0 && prices.sellHigh < FIX_MAX / 1000, "bad sell pricing");	// @audit-issue

169:        require(prices.buyLow != 0 && prices.buyHigh < FIX_MAX / 1000, "bad buy pricing");	// @audit-issue
```
[168](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L168-L168), [169](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L169-L169), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

82:        return (asMicroPercent * pastSupply + (ONE_HUNDRED_PERCENT - 1)) / ONE_HUNDRED_PERCENT;	// @audit-issue
```
[82](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L82-L82), 


#### Recommendation

Utilize 'unchecked' blocks in Solidity for divisions where overflow is impossible, such as when 'type(int).min/(-1)' is not a concern. This can save gas by bypassing overflow checks in these specific cases.

### Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
Unchecked keyword can be added to such scenerios: 
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

```solidity
Path: ./contracts/libraries/Throttle.sol

59:            available -= uint256(amount);	// @audit-issue
```
[59](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L59-L59), 


```solidity
Path: ./contracts/p1/Distributor.sol

186:                        tokensPerShare * (totalShares - paidOutShares)	// @audit-issue
```
[186](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L186-L186), 


```solidity
Path: ./contracts/p1/BackingManager.sol

223:            rToken.mint(baskets - rToken.basketsNeeded());	// @audit-issue
```
[223](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L223-L223), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

168:                        uint256(low.mulDiv(anchor - ctx.bals[i], buPriceHigh, FLOOR))	// @audit-issue

176:                        uint256(high.safeMulDiv(ctx.bals[i] - anchor, buPriceLow, CEIL))	// @audit-issue

189:                uint192 val = low.mul(ctx.bals[i] - anchor, FLOOR);	// @audit-issue
```
[168](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L168-L168), [176](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L176-L176), [189](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L189-L189), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

341:            if (amt > targetAmts[i]) _targetAmts.set(targetName, amt - targetAmts[i]);	// @audit-issue
```
[341](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L341-L341), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

210:            soldAmt = initBal - sellBal;	// @audit-issue
```
[210](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L210-L210), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

326:        uint192 progression = divuu(timestamp - _startTime, _endTime - _startTime); // {1}	// @audit-issue

330:            uint192 exp = MAX_EXP.mulDiv(TWENTY_PERCENT - progression, TWENTY_PERCENT, ROUND);	// @audit-issue

346:                (highPrice - _bestPrice).mulDiv(progression - TWENTY_PERCENT, TWENTY_FIVE_PERCENT);	// @audit-issue

355:                (_bestPrice - worstPrice).mulDiv(progression - FORTY_FIVE_PERCENT, FIFTY_PERCENT);	// @audit-issue
```
[326](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L326-L326), [330](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L330-L330), [346](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L346-L346), [355](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L355-L355), 


#### Recommendation

In scenarios where subtraction cannot result in underflow due to prior `require()` or `if`-statements, wrap these operations in an `unchecked` block to save gas. This optimization should only be applied when the safety of the operation is assured. Carefully analyze each case to confirm that underflow is impossible before implementing `unchecked` blocks, as incorrect usage can lead to vulnerabilities in the contract.

### Stack variable is only used once
If the variable is only accessed once, it's cheaper to use the assigned value directly that one time, and save the 3 gas the extra stack assignment would spend

```solidity
Path: ./contracts/registry/VersionRegistry.sol

46:        string memory version = deployer.version();	// @audit-issue: version used only on line: 47
```
[46](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L46-L46), 


```solidity
Path: ./contracts/libraries/Throttle.sol

74:        uint48 delta = uint48(block.timestamp) - throttle.lastTimestamp; // {seconds}	// @audit-issue: delta used only on line: 75
```
[74](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L74-L74), 


```solidity
Path: ./contracts/libraries/Fixed.sol

369:        uint192 diff = x <= y ? y - x : x - y;	// @audit-issue: diff used only on line: 370
```
[369](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L369-L369), 


```solidity
Path: ./contracts/p1/Main.sol

103:        Implementations memory implementation = versionRegistry.getImplementationForVersion(	// @audit-issue: implementation used only on line: 107
104:            versionHash
105:        );

158:        (bool success, ) = proxy.call(	// @audit-issue: success used only on line: 162
159:            abi.encodeWithSelector(UUPSUpgradeable.upgradeTo.selector, implementation)
160:        );
```
[103](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L103-L105), [158](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L158-L160), 


```solidity
Path: ./contracts/p1/Distributor.sol

177:        DAOFeeRegistry daoFeeRegistry = main.daoFeeRegistry();	// @audit-issue: daoFeeRegistry used only on line: 178

212:        DAOFeeRegistry daoFeeRegistry = main.daoFeeRegistry();	// @audit-issue: daoFeeRegistry used only on line: 213

215:            (address feeRecipient, uint256 feeNumerator, uint256 feeDenominator) = main	// @audit-issue: feeRecipient used only on line: 220
216:            .daoFeeRegistry()
217:            .getFeeDetails(address(rToken));

215:            (address feeRecipient, uint256 feeNumerator, uint256 feeDenominator) = main	// @audit-issue: feeDenominator used only on line: 223
216:            .daoFeeRegistry()
217:            .getFeeDetails(address(rToken));
```
[177](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L177-L177), [212](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L212-L212), [215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L215-L217), [215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L215-L217), 


```solidity
Path: ./contracts/p1/Furnace.sol

72:        uint192 payoutRatio = FIX_ONE.minus(FIX_ONE.minus(ratio).powu(numPeriods));	// @audit-issue: payoutRatio used only on line: 74
```
[72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L72-L72), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

221:        AssetPluginRegistry assetPluginRegistry = main.assetPluginRegistry();	// @audit-issue: assetPluginRegistry used only on line: 222

254:        uint256 gas = gasleft();	// @audit-issue: gas used only on line: 260
```
[221](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L221-L221), [254](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L254-L254), 


```solidity
Path: ./contracts/p1/BackingManager.sol

148:        (TradingContext memory ctx, Registry memory reg) = tradingContext(basketsHeld);	// @audit-issue: ctx used only on line: 153

148:        (TradingContext memory ctx, Registry memory reg) = tradingContext(basketsHeld);	// @audit-issue: reg used only on line: 153

149:        (	// @audit-issue: doTrade used only on line: 155
150:            bool doTrade,
151:            TradeRequest memory req,
152:            TradePrices memory prices
153:        ) = RecollateralizationLibP1.prepareRecollateralizationTrade(ctx, reg);

149:        (	// @audit-issue: prices used only on line: 165
150:            bool doTrade,
151:            TradeRequest memory req,
152:            TradePrices memory prices
153:        ) = RecollateralizationLibP1.prepareRecollateralizationTrade(ctx, reg);
```
[148](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L148-L148), [148](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L148-L148), [149](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L149-L153), [149](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L149-L153), 


```solidity
Path: ./contracts/p1/StRSR.sol

279:        (uint256 index, uint64 availableAt) = pushDraft(account, rsrAmount);	// @audit-issue: index used only on line: 280

279:        (uint256 index, uint64 availableAt) = pushDraft(account, rsrAmount);	// @audit-issue: availableAt used only on line: 280

317:        uint192 oldDrafts = firstId != 0 ? queue[firstId - 1].drafts : 0;	// @audit-issue: oldDrafts used only on line: 318

318:        uint192 draftAmount = queue[endId - 1].drafts - oldDrafts;	// @audit-issue: draftAmount used only on line: 324

364:        uint192 oldDrafts = firstId != 0 ? queue[firstId - 1].drafts : 0;	// @audit-issue: oldDrafts used only on line: 365

365:        uint192 draftAmount = queue[endId - 1].drafts - oldDrafts;	// @audit-issue: draftAmount used only on line: 371

437:        uint192 initRate = exchangeRate();	// @audit-issue: initRate used only on line: 476

438:        uint256 rewards = rsrRewards();	// @audit-issue: rewards used only on line: 472

597:        uint192 initRate = exchangeRate();	// @audit-issue: initRate used only on line: 626

606:            uint192 payoutRatio = FIX_ONE - FixLib.powu(FIX_ONE - rewardRatio, numPeriods);	// @audit-issue: payoutRatio used only on line: 609

649:        uint256 draftAmount = newTotalDrafts - totalDrafts;	// @audit-issue: draftAmount used only on line: 663

656:        uint192 oldDrafts = index != 0 ? queue[index - 1].drafts : 0;	// @audit-issue: oldDrafts used only on line: 663

727:        uint256 newStakeRSR = stakeRSR + rsrAmount;	// @audit-issue: newStakeRSR used only on line: 729

729:        uint256 newTotalStakes = (stakeRate * newStakeRSR) / FIX_ONE;	// @audit-issue: newTotalStakes used only on line: 730

933:        bytes32 structHash = keccak256(	// @audit-issue: structHash used only on line: 937
934:            abi.encode(_PERMIT_TYPEHASH, owner, spender, value, _useNonce(owner), deadline)
935:        );
```
[279](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L279-L279), [279](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L279-L279), [317](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L317-L317), [318](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L318-L318), [364](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L364-L364), [365](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L365-L365), [437](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L437-L437), [438](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L438-L438), [597](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L597-L597), [606](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L606-L606), [649](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L649-L649), [656](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L656-L656), [727](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L727-L727), [729](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L729-L729), [933](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L933-L935), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

308:        BasketRange memory held = basketsHeldBy(address(backingManager));	// @audit-issue: held used only on line: 309

658:        bool success = _newBasket.nextBasket(_targetNames, config, assetRegistry);	// @audit-issue: success used only on line: 661
```
[308](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L308-L308), [658](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L658-L658), 


```solidity
Path: ./contracts/p1/Deployer.sol

229:            string memory stRSRName = string(abi.encodePacked(stRSRSymbol, " Token"));	// @audit-issue: stRSRName used only on line: 232
```
[229](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L229-L229), 


```solidity
Path: ./contracts/p1/RToken.sol

409:        uint256 low = (FIX_ONE_256 * basketsNeeded_) / supply; // D18{BU/rTok}	// @audit-issue: low used only on line: 413

410:        uint256 high = (FIX_ONE_256 * basketsNeeded_ + (supply - 1)) / supply; // D18{BU/rTok}	// @audit-issue: high used only on line: 413

489:        uint256 amtRToken = totalSupply != 0	// @audit-issue: amtRToken used only on line: 496
490:            ? amtBaskets.muluDivu(totalSupply, basketsNeeded) // {rTok} = {BU} * {qRTok} * {qRTok}
491:            : amtBaskets; // {rTok}
```
[409](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L409-L409), [410](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L410-L410), [489](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L489-L491), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

94:        uint256 pastEra = _checkpointsLookup(_eras, timepoint);	// @audit-issue: pastEra used only on line: 95

101:        uint256 pastEra = _checkpointsLookup(_eras, timepoint);	// @audit-issue: pastEra used only on line: 102

222:        uint256 delegatorBalance = balanceOf(delegator);	// @audit-issue: delegatorBalance used only on line: 227
```
[94](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L94-L94), [101](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L101-L101), [222](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L222-L222), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

103:        (uint256 soldAmt, uint256 boughtAmt) = trade.settle();	// @audit-issue: soldAmt used only on line: 104

103:        (uint256 soldAmt, uint256 boughtAmt) = trade.settle();	// @audit-issue: boughtAmt used only on line: 104
```
[103](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L103-L103), [103](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L103-L103), 


```solidity
Path: ./contracts/p1/mixins/TradeLib.sol

76:        uint192 b = s.mul(FIX_ONE.minus(maxTradeSlippage)).safeMulDiv(	// @audit-issue: b used only on line: 84
77:            trade.prices.sellLow,
78:            trade.prices.buyHigh,
79:            CEIL
80:        );

137:        uint192 exactSellAmount = trade.buyAmount.mulDiv(	// @audit-issue: exactSellAmount used only on line: 145
138:            trade.prices.buyHigh,
139:            trade.prices.sellLow,
140:            CEIL
141:        );

145:        uint192 slippedSellAmount = exactSellAmount.div(FIX_ONE.minus(maxTradeSlippage), CEIL);	// @audit-issue: slippedSellAmount used only on line: 147
```
[76](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L76-L80), [137](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L137-L141), [145](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L145-L145), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

44:        BasketRange memory range = basketRange(ctx, reg);	// @audit-issue: range used only on line: 47
```
[44](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L44-L44), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

114:        uint96 _sellAmount = uint96(	// @audit-issue: _sellAmount used only on line: 149
115:            _divrnd(
116:                req.sellAmount * FEE_DENOMINATOR,
117:                FEE_DENOMINATOR + gnosis.feeNumerator(),
118:                FLOOR
119:            )
120:        );

213:            uint256 adjustedSoldAmt = Math.max(soldAmt, 1);	// @audit-issue: adjustedSoldAmt used only on line: 217

214:            uint256 adjustedBuyAmt = boughtAmt + 1;	// @audit-issue: adjustedBuyAmt used only on line: 217

217:            uint192 clearingPrice = shiftl_toFix(adjustedBuyAmt, 9).divu(adjustedSoldAmt, FLOOR);	// @audit-issue: clearingPrice used only on line: 218

239:        GnosisAuctionData memory data = gnosis.auctionData(auctionId);	// @audit-issue: data used only on line: 240
```
[114](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L114-L120), [213](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L213-L213), [214](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L214-L214), [217](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L217-L217), [239](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L239-L239), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

257:        uint256 balanceBefore = buy.balanceOf(address(this)); // {qBuyTok}	// @audit-issue: balanceBefore used only on line: 260

330:            uint192 exp = MAX_EXP.mulDiv(TWENTY_PERCENT - progression, TWENTY_PERCENT, ROUND);	// @audit-issue: exp used only on line: 335
```
[257](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L257-L257), [330](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L330-L330), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

75:        uint256 asMicroPercent = super.proposalThreshold(); // {micro %}	// @audit-issue: asMicroPercent used only on line: 82

78:        uint256 pastSupply = token.getPastTotalSupply(clock() - 1);	// @audit-issue: pastSupply used only on line: 82

186:        uint256 startTimepoint = proposalSnapshot(proposalId);	// @audit-issue: startTimepoint used only on line: 187

187:        uint256 pastEra = IStRSRVotes(address(token)).getPastEra(startTimepoint);	// @audit-issue: pastEra used only on line: 189

188:        uint256 currentEra = IStRSRVotes(address(token)).currentEra();	// @audit-issue: currentEra used only on line: 189
```
[75](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L75-L75), [78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L78-L78), [186](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L186-L186), [187](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L187-L187), [188](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L188-L188), 


#### Recommendation

Eliminate single-use stack variables in Solidity to optimize gas consumption. Directly use the assigned value in the place of the variable. This approach saves the 3 gas typically used for the extra stack assignment, streamlining the function's execution and enhancing overall gas efficiency.

### Avoid Using State Variables Directly in `emit` for Gas Efficiency
In Solidity, emitting events is a common way to log contract activity and changes, especially for off-chain monitoring and interfacing. However, using state variables directly in `emit` statements can lead to increased gas costs. Each access to a state variable incurs gas due to storage reading operations. When these variables are used directly in `emit` statements, especially within functions that perform multiple operations, the cumulative gas cost can become significant. Instead, caching state variables in memory and using these local copies in `emit` statements can optimize gas usage.


```solidity
Path: ./contracts/mixins/Auth.sol

216:        emit UnfreezeAtSet(unfreezeAt, newUnfreezeAt);	// @audit-issue: `unfreezeAt` is a state variable and used on line(s): ['215']
```
[216](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L216-L216), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

235:            else emit AssetUnregistered(erc20, assets[erc20]);	// @audit-issue: `assets` is a state variable and used on line(s): ['234']
```
[235](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L235-L235), 


```solidity
Path: ./contracts/p1/StRSR.sol

338:        emit UnstakingCompleted(firstId, endId, draftEra, account, rsrAmount);	// @audit-issue: `draftEra` is a state variable and used on line(s): ['321', '307', '308']

384:        emit UnstakingCancelled(firstId, endId, draftEra, account, rsrAmount);	// @audit-issue: `draftEra` is a state variable and used on line(s): ['353', '368', '354']
```
[338](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L338-L338), [384](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L384-L384), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

143:        emit BasketSet(nonce, basket.erc20s, refAmts, true);	// @audit-issue: `basket` is a state variable and used on line(s): ['140', '142', '142']

181:            emit BasketStatusChanged(lastStatus, currentStatus);	// @audit-issue: `lastStatus` is a state variable and used on line(s): ['180']

188:            emit LastCollateralizedChanged(lastCollateralized, nonce);	// @audit-issue: `lastCollateralized` is a state variable and used on line(s): ['187']

188:            emit LastCollateralizedChanged(lastCollateralized, nonce);	// @audit-issue: `nonce` is a state variable and used on line(s): ['187', '189']

679:        emit BasketSet(nonce, basket.erc20s, refAmts, disabled);	// @audit-issue: `nonce` is a state variable and used on line(s): ['668']

679:        emit BasketSet(nonce, basket.erc20s, refAmts, disabled);	// @audit-issue: `basket` is a state variable and used on line(s): ['677', '674', '667', '677']
```
[143](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L143-L143), [181](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L181-L181), [188](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L188-L188), [188](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L188-L188), [679](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L679-L679), [679](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L679-L679), 


```solidity
Path: ./contracts/p1/RToken.sol

457:        emit IssuanceThrottleSet(issuanceThrottle.params, params);	// @audit-issue: `issuanceThrottle` is a state variable and used on line(s): ['455']

468:        emit RedemptionThrottleSet(redemptionThrottle.params, params);	// @audit-issue: `redemptionThrottle` is a state variable and used on line(s): ['466']

492:        emit BasketsNeededChanged(basketsNeeded, basketsNeeded + amtBaskets);	// @audit-issue: `basketsNeeded` is a state variable and used on line(s): ['490']

511:        emit BasketsNeededChanged(basketsNeeded, basketsNeeded - amtBaskets);	// @audit-issue: `basketsNeeded` is a state variable and used on line(s): ['510']
```
[457](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L457-L457), [468](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L468-L468), [492](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L492-L492), [511](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L511-L511), 


```solidity
Path: ./contracts/p1/Broker.sol

162:                emit DutchTradeDisabledSet(sell, dutchTradeDisabled[sell], true);	// @audit-issue: `dutchTradeDisabled` is a state variable and used on line(s): ['166']

166:                emit DutchTradeDisabledSet(buy, dutchTradeDisabled[buy], true);	// @audit-issue: `dutchTradeDisabled` is a state variable and used on line(s): ['162']
```
[162](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L162-L162), [166](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L166-L166), 


#### Recommendation


To optimize gas efficiency, cache state variables in memory when they are used multiple times within a function, including in `emit` statements.

### Storage Layout Optimization
Storage Layout Optimization in Solidity involves arranging state variables to minimize gas costs. Since storage is expensive, combining variables into as few slots as possible and deleting unneeded variables can significantly reduce the gas needed for contract operations.

```solidity
Path: ./contracts/p1/AssetRegistry.sol

19:    IBasketHandler private basketHandler;	// @audit-issue: ['Current storage layout is like this: ', "However it can be optimized by 1 storage by storing variables like in order: \n\t`['assets', '__gap', 'basketHandler', 'backingManager', '_erc20s', 'lastRefresh']`"]
20:    IBackingManager private backingManager;
21:
22:    // Registered ERC20s
23:    EnumerableSet.AddressSet private _erc20s;
24:
25:    // Registered Assets
26:    mapping(IERC20 => IAsset) private assets;
27:
28:    // === 3.0.0 ===
29:
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
41:    // effects: assets' = {a.erc20(): a for a in assets_}
42:    function init(IMain main_, IAsset[] calldata assets_) external initializer {
43:        __Component_init(main_);
44:        basketHandler = main_.basketHandler();
45:        backingManager = main_.backingManager();
46:
47:        uint256 length = assets_.length;
48:        for (uint256 i = 0; i < length; ++i) {
49:            _register(assets_[i]);
50:        }
51:    }
52:
53:    /// Update the state of all assets
54:    /// @custom:refresher
55:    // actions:
56:    //   calls refresh(c) for c in keys(assets) when c.isCollateral()
57:    //   tracks basket status on basketHandler
58:    function refresh() public {
59:        // It's a waste of gas to require notPausedOrFrozen because assets can be updated directly
60:        // Assuming an RTokenAsset is registered, furnace.melt() will also be called
61:
62:        uint256 length = _erc20s.length();
63:        for (uint256 i = 0; i < length; ++i) {
64:            assets[IERC20(_erc20s.at(i))].refresh();
65:        }
66:
67:        basketHandler.trackStatus();
68:        lastRefresh = uint48(block.timestamp); // safer to do this at end than start, actually
69:    }
70:
71:    /// Register `asset`
72:    /// If either the erc20 address or the asset was already registered, fail
73:    /// @return true if the erc20 address was not already registered.
74:    /// @custom:governance
75:    // checks: asset.erc20() not in keys(assets) or assets[asset.erc20] == asset
76:    // effects: assets' = assets.set(asset.erc20(), asset)
77:    // returns: (asset.erc20 not in keys(assets))
78:    function register(IAsset asset) external governance returns (bool) {
79:        return _register(asset);
80:    }
81:
82:    /// Register `asset` if and only if its erc20 address is already registered.
83:    /// If the erc20 address was not registered, revert.
84:    /// @return swapped If the asset was swapped for a previously-registered asset
85:    /// @custom:governance
86:    // contract
87:    // checks: asset.erc20() in assets
88:    // effects: assets' = assets + {asset.erc20(): asset}
89:    // actions: if asset.erc20() is in basketHandler's basket then basketHandler.disableBasket()
90:    function swapRegistered(IAsset asset) external governance returns (bool swapped) {
91:        require(_erc20s.contains(address(asset.erc20())), "no ERC20 collision");
92:
93:        try basketHandler.quantity{ gas: _reserveGas() }(asset.erc20()) returns (uint192 quantity) {
94:            if (quantity != 0) basketHandler.disableBasket(); // not an interaction
95:        } catch {
96:            basketHandler.disableBasket();
97:        }
98:
99:        swapped = _registerIgnoringCollisions(asset);
100:    }
101:
102:    /// Unregister an asset, requiring that it is already registered
103:    /// Rewards are NOT claimed by default when unregistering due to security concerns.
104:    /// If the collateral is secure, governance should claim rewards before unregistering.
105:    /// @custom:governance
106:    // checks: assets[asset.erc20()] == asset
107:    // effects: assets' = assets - {asset.erc20():_} + {asset.erc20(), asset}
108:    function unregister(IAsset asset) external governance {
109:        require(_erc20s.contains(address(asset.erc20())), "no asset to unregister");
110:        require(assets[asset.erc20()] == asset, "asset not found");
111:
112:        try basketHandler.quantity{ gas: _reserveGas() }(asset.erc20()) returns (uint192 quantity) {
113:            if (quantity != 0) basketHandler.disableBasket(); // not an interaction
114:        } catch {
115:            basketHandler.disableBasket();
116:        }
117:
118:        _erc20s.remove(address(asset.erc20()));
119:        assets[asset.erc20()] = IAsset(address(0));
120:        emit AssetUnregistered(asset.erc20(), asset);
121:    }
122:
123:    /// Return the Asset registered for erc20; revert if erc20 is not registered.
124:    // checks: erc20 in assets
125:    // returns: assets[erc20]
126:    function toAsset(IERC20 erc20) external view returns (IAsset) {
127:        require(_erc20s.contains(address(erc20)), "erc20 unregistered");
128:        return assets[erc20];
129:    }
130:
131:    /// Return the Collateral registered for erc20; revert if erc20 is not registered as Collateral
132:    // checks: erc20 in assets, assets[erc20].isCollateral()
133:    // returns: assets[erc20]
134:    function toColl(IERC20 erc20) external view returns (ICollateral) {
135:        require(_erc20s.contains(address(erc20)), "erc20 unregistered");
136:        require(assets[erc20].isCollateral(), "erc20 is not collateral");
137:        return ICollateral(address(assets[erc20]));
138:    }
139:
140:    /// Returns true if erc20 is registered.
141:    // returns: (erc20 in assets)
142:    function isRegistered(IERC20 erc20) external view returns (bool) {
143:        return _erc20s.contains(address(erc20));
144:    }
145:
146:    /// Returns keys(assets) as a (duplicate-free) list.
147:    // returns: [keys(assets)] without duplicates.
148:    function erc20s() external view returns (IERC20[] memory erc20s_) {
149:        uint256 length = _erc20s.length();
150:        erc20s_ = new IERC20[](length);
151:        for (uint256 i = 0; i < length; ++i) {
152:            erc20s_[i] = IERC20(_erc20s.at(i));
153:        }
154:    }
155:
156:    /// Returns keys(assets), values(assets) as (duplicate-free) lists.
157:    // returns: [keys(assets)], [values(assets)] without duplicates.
158:    /// @return reg The list of registered ERC20s and Assets, in the same order
159:    function getRegistry() public view returns (Registry memory reg) {
160:        uint256 length = _erc20s.length();
161:        reg.erc20s = new IERC20[](length);
162:        reg.assets = new IAsset[](length);
163:        for (uint256 i = 0; i < length; ++i) {
164:            reg.erc20s[i] = IERC20(_erc20s.at(i));
165:            reg.assets[i] = assets[IERC20(_erc20s.at(i))];
166:        }
167:    }
168:
169:    /// @inheritdoc IAssetRegistry
170:    function validateCurrentAssets() external view {
171:        Registry memory registry = getRegistry();
172:        AssetPluginRegistry assetPluginRegistry = main.assetPluginRegistry();
173:
174:        if (address(assetPluginRegistry) != address(0)) {
175:            uint256 assetLen = registry.assets.length;
176:            for (uint256 i = 0; i < assetLen; ++i) {
177:                IAsset asset = registry.assets[i];
178:
179:                require(
180:                    assetPluginRegistry.isValidAsset(
181:                        keccak256(abi.encodePacked(this.version())),
182:                        address(asset)
183:                    ),
184:                    "unsupported asset"
185:                );
186:            }
187:        }
188:    }
189:
190:    /// @return The number of registered ERC20s
191:    function size() external view returns (uint256) {
192:        return _erc20s.length();
193:    }
194:
195:    /// Register an asset
196:    /// Forbids registering a different asset for an ERC20 that is already registered
197:    /// @return registered If the asset was moved from unregistered to registered
198:    // checks: (asset.erc20() not in assets) or (assets[asset.erc20()] == asset)
199:    // effects: assets' = assets.set(asset.erc20(), asset)
200:    // returns: assets.erc20() not in assets
201:    function _register(IAsset asset) internal returns (bool registered) {
202:        require(
203:            !_erc20s.contains(address(asset.erc20())) || assets[asset.erc20()] == asset,
204:            "duplicate ERC20 detected"
205:        );
206:
207:        registered = _registerIgnoringCollisions(asset);
208:    }
209:
210:    /// Register an asset, unregistering any previous asset with the same ERC20.
211:    // effects: assets' = assets.set(asset.erc20(), asset)
212:    // returns: assets[asset.erc20()] != asset
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
250:        return true;
251:    }
252:
253:    function _reserveGas() private view returns (uint256) {
254:        uint256 gas = gasleft();
255:        // Call to quantity() restricts gas that is passed along to 63 / 64 of gasleft().
256:        // Therefore gasleft() must be greater than 64 * GAS_FOR_BH_QTY / 63
257:        // GAS_FOR_DISABLE_BASKET is a buffer which can be considerably lower without
258:        // security implications.
259:        require(
260:            gas > (64 * GAS_FOR_BH_QTY) / 63 + GAS_FOR_DISABLE_BASKET,
261:            "not enough gas to unregister safely"
262:        );
263:        return GAS_FOR_BH_QTY;
264:    }
265:
266:    /**
267:     * @dev This empty reserved space is put in place to allow future versions to add new
268:     * variables without shifting down storage in the inheritance chain.
269:     * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
270:     *
271:     * AssetRegistry uses 52 slots, not 50.
272:     */
273:    uint256[46] private __gap;
```
[19](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L19-L273), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

37:    IAssetRegistry private assetRegistry;	// @audit-issue: ['Current storage layout is like this: ', "However it can be optimized by 1 storage by storing variables like in order: \n\t`['config', 'basket', '_newBasket', 'basketHistory', '__gap', 'assetRegistry', 'backingManager', 'rsr', 'rToken', 'stRSR', '_targetNames', '_targetAmts', 'nonce', 'timestamp', 'warmupPeriod', 'lastStatusTimestamp', 'lastCollateralized', 'disabled', 'lastStatus', 'reweightable', 'enableIssuancePremium']`"]
38:    IBackingManager private backingManager;
39:    IERC20 private rsr;
40:    IRToken private rToken;
41:    IStRSR private stRSR;
42:
43:    // config is the basket configuration, from which basket will be computed in a basket-switch
44:    // event. config is only modified by governance through setPrimeBasket and setBackupConfig
45:    BasketConfig private config;
46:
47:    // basket, disabled, nonce, and timestamp are only ever set by `_switchBasket()`
48:    // basket is the current basket.
49:    Basket private basket;
50:
51:    uint48 public nonce; // {basketNonce} A unique identifier for this basket instance
52:    uint48 public timestamp; // The timestamp when this basket was last set
53:
54:    // If disabled is true, status() is DISABLED, the basket is invalid,
55:    // and everything except redemption should be paused.
56:    bool private disabled;
57:
58:    // === Function-local transitory vars ===
59:
60:    // These are effectively local variables of _switchBasket.
61:    // Nothing should use their values from previous transactions.
62:    EnumerableSet.Bytes32Set private _targetNames;
63:    Basket private _newBasket;
64:
65:    // === Warmup Period ===
66:    // Added in 3.0.0
67:
68:    // Warmup Period
69:    uint48 public warmupPeriod; // {s} how long to wait until issuance/trading after regaining SOUND
70:
71:    // basket status changes, mainly set when `trackStatus()` is called
72:    // used to enforce warmup period, after regaining SOUND
73:    uint48 private lastStatusTimestamp;
74:    CollateralStatus private lastStatus;
75:
76:    // === Historical basket nonces ===
77:    // Added in 3.0.0
78:
79:    // A history of baskets by basket nonce; includes current basket
80:    mapping(uint48 => Basket) private basketHistory;
81:
82:    // Effectively local variable of `BasketLibP1.requireConstantConfigTargets()`
83:    EnumerableMap.Bytes32ToUintMap private _targetAmts; // targetName -> {target/BU}
84:
85:    // ===
86:    // Added in 3.2.0
87:
88:    // Whether the total weights of the target basket can be changed
89:    bool public reweightable; // immutable after init
90:
91:    uint48 public lastCollateralized; // {basketNonce} most recent full collateralization
92:
93:    // ===
94:    // Added in 4.0.0
95:
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
107:    // BasketHandler.init() just leaves the BasketHandler state zeroed
108:    function init(
109:        IMain main_,
110:        uint48 warmupPeriod_,
111:        bool reweightable_,
112:        bool enableIssuancePremium_
113:    ) external initializer {
114:        __Component_init(main_);
115:
116:        assetRegistry = main_.assetRegistry();
117:        backingManager = main_.backingManager();
118:        rsr = main_.rsr();
119:        rToken = main_.rToken();
120:        stRSR = main_.stRSR();
121:
122:        setWarmupPeriod(warmupPeriod_);
123:        reweightable = reweightable_; // immutable thereafter
124:        enableIssuancePremium = enableIssuancePremium_;
125:
126:        // Set last status to DISABLED (default)
127:        lastStatus = CollateralStatus.DISABLED;
128:        lastStatusTimestamp = uint48(block.timestamp);
129:
130:        disabled = true;
131:    }
132:
133:    /// Disable the basket in order to schedule a basket refresh
134:    /// @custom:protected
135:    // checks: caller is assetRegistry
136:    // effects: disabled' = true
137:    function disableBasket() external {
138:        require(_msgSender() == address(assetRegistry), "asset registry only");
139:
140:        uint256 len = basket.erc20s.length;
141:        uint192[] memory refAmts = new uint192[](len);
142:        for (uint256 i = 0; i < len; ++i) refAmts[i] = basket.refAmts[basket.erc20s[i]];
143:        emit BasketSet(nonce, basket.erc20s, refAmts, true);
144:        disabled = true;
145:
146:        trackStatus(); // does NOT interact with collateral plugins or tokens
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
157:    //   Or no such basket exists and disabled' = true
158:    function refreshBasket() external {
159:        assetRegistry.refresh();
160:
161:        require(
162:            main.hasRole(OWNER, _msgSender()) ||
163:                (lastStatus == CollateralStatus.DISABLED && !main.tradingPausedOrFrozen()),
164:            "basket unrefreshable"
165:        );
166:        _switchBasket();
167:
168:        trackStatus();
169:    }
170:
171:    /// Track basket status and collateralization changes
172:    // effects: lastStatus' = status(), and lastStatusTimestamp' = current timestamp
173:    /// @dev Does NOT interact with collateral plugins or tokens when basket is disabled
174:    /// @custom:refresher
175:    function trackStatus() public {
176:        // Historical context: This is not the ideal naming for this function but it allowed
177:        // reweightable RTokens introduced in 3.2.0 to be a minor update as opposed to major
178:
179:        CollateralStatus currentStatus = status();
180:        if (currentStatus != lastStatus) {
181:            emit BasketStatusChanged(lastStatus, currentStatus);
182:            lastStatus = currentStatus;
183:            lastStatusTimestamp = uint48(block.timestamp);
184:        }
185:
186:        // Invalidate old nonces if fully collateralized
187:        if (reweightable && nonce > lastCollateralized && fullyCollateralized()) {
188:            emit LastCollateralizedChanged(lastCollateralized, nonce);
189:            lastCollateralized = nonce;
190:        }
191:    }
192:
193:    /// Set the prime basket, checking target amounts are constant
194:    /// @param erc20s The collateral for the new prime basket
195:    /// @param targetAmts The target amounts (in) {target/BU} for the new prime basket
196:    /// @custom:governance
197:    function setPrimeBasket(IERC20[] calldata erc20s, uint192[] calldata targetAmts) external {
198:        _setPrimeBasket(erc20s, targetAmts, false);
199:    }
200:
201:    /// Set the prime basket, skipping any constant target amount checks if RToken is reweightable
202:    /// Warning: Reweightable RTokens SHOULD use a spell to execute this function to avoid
203:    ///          accidentally changing the UoA value of the RToken.
204:    /// @param erc20s The collateral for the new prime basket
205:    /// @param targetAmts The target amounts (in) {target/BU} for the new prime basket
206:    /// @custom:governance
207:    function forceSetPrimeBasket(IERC20[] calldata erc20s, uint192[] calldata targetAmts) external {
208:        _setPrimeBasket(erc20s, targetAmts, true);
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
226:    //   config'.targetNames[e] = assetRegistry.toColl(e).targetName, for e in erc20s
227:    function _setPrimeBasket(
228:        IERC20[] calldata erc20s,
229:        uint192[] memory targetAmts,
230:        bool disableTargetAmountCheck
231:    ) internal {
232:        requireGovernanceOnly();
233:        require(erc20s.length != 0 && erc20s.length == targetAmts.length, "invalid lengths");
234:        requireValidCollArray(erc20s);
235:
236:        if (
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
249:
250:        // Clean up previous basket config
251:        for (uint256 i = 0; i < config.erc20s.length; ++i) {
252:            delete config.targetAmts[config.erc20s[i]];
253:            delete config.targetNames[config.erc20s[i]];
254:        }
255:        delete config.erc20s;
256:
257:        // Set up new config basket
258:        bytes32[] memory names = new bytes32[](erc20s.length);
259:
260:        for (uint256 i = 0; i < erc20s.length; ++i) {
261:            // This is a nice catch to have, but in general it is possible for
262:            // an ERC20 in the prime basket to have its asset unregistered.
263:            require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "erc20 is not collateral");
264:            require(0 < targetAmts[i] && targetAmts[i] <= MAX_TARGET_AMT, "invalid target amount");
265:
266:            config.erc20s.push(erc20s[i]);
267:            config.targetAmts[erc20s[i]] = targetAmts[i];
268:            names[i] = assetRegistry.toColl(erc20s[i]).targetName();
269:            config.targetNames[erc20s[i]] = names[i];
270:        }
271:
272:        emit PrimeBasketSet(erc20s, targetAmts, names);
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
283:    //   config'.backups[targetName] = {max: max, erc20s: erc20s}
284:    function setBackupConfig(
285:        bytes32 targetName,
286:        uint256 max,
287:        IERC20[] calldata erc20s
288:    ) external {
289:        requireGovernanceOnly();
290:        require(max <= MAX_BACKUP_ERC20S && erc20s.length <= MAX_BACKUP_ERC20S, "too large");
291:        requireValidCollArray(erc20s);
292:        BackupConfig storage conf = config.backups[targetName];
293:        conf.max = max;
294:        delete conf.erc20s;
295:
296:        for (uint256 i = 0; i < erc20s.length; ++i) {
297:            // This is a nice catch to have, but in general it is possible for
298:            // an ERC20 in the backup config to have its asset altered.
299:            assetRegistry.toColl(erc20s[i]); // reverts if not collateral
300:            conf.erc20s.push(erc20s[i]);
301:        }
302:        emit BackupConfigSet(targetName, max, erc20s);
303:    }
304:
305:    /// @return Whether this contract owns enough collateral to cover rToken.basketsNeeded() BUs
306:    /// ie, whether the protocol is currently fully collateralized
307:    function fullyCollateralized() public view returns (bool) {
308:        BasketRange memory held = basketsHeldBy(address(backingManager));
309:        return held.bottom >= rToken.basketsNeeded();
310:    }
311:
312:    /// @return status_ The status of the basket
313:    // returns DISABLED if disabled == true, and worst(status(coll)) otherwise
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
324:                if (s == CollateralStatus.DISABLED) return CollateralStatus.DISABLED;
325:                status_ = s;
326:            }
327:        }
328:    }
329:
330:    /// @return Whether the basket is ready to issue and trade
331:    function isReady() external view returns (bool) {
332:        return
333:            status() == CollateralStatus.SOUND &&
334:            (block.timestamp >= lastStatusTimestamp + warmupPeriod);
335:    }
336:
337:    /// Basket quantity rounded up, without any issuance premium
338:    /// @param erc20 The token contract to check for quantity for
339:    /// @return {tok/BU} The token-quantity of an ERC20 token in the basket.
340:    // Returns 0 if erc20 is not registered or not in the basket
341:    // Returns FIX_MAX (in lieu of +infinity) if Collateral.refPerTok() is 0.
342:    // Otherwise returns (token's basket.refAmts / token's Collateral.refPerTok())
343:    function quantity(IERC20 erc20) public view returns (uint192) {
344:        try assetRegistry.toColl(erc20) returns (ICollateral coll) {
345:            return _quantity(erc20, coll, CEIL);
346:        } catch {
347:            return FIX_ZERO;
348:        }
349:    }
350:
351:    /// Basket quantity rounded up, without any issuance premium
352:    /// Like quantity(), but unsafe because it DOES NOT CONFIRM THAT THE ASSET IS CORRECT
353:    /// @param erc20 The ERC20 token contract for the asset
354:    /// @param asset The registered asset plugin contract for the erc20
355:    /// @return {tok/BU} The token-quantity of an ERC20 token in the basket.
356:    // Returns 0 if erc20 is not registered or not in the basket
357:    // Returns FIX_MAX (in lieu of +infinity) if Collateral.refPerTok() is 0.
358:    // Otherwise returns (token's basket.refAmts / token's Collateral.refPerTok())
359:    function quantityUnsafe(IERC20 erc20, IAsset asset) public view returns (uint192) {
360:        if (!asset.isCollateral()) return FIX_ZERO;
361:        return _quantity(erc20, ICollateral(address(asset)), CEIL);
362:    }
363:
364:    /// @param coll A collateral that has had refresh() called on it this timestamp
365:    /// @return {1} The multiplier to charge on issuance quantities for a collateral
366:    function issuancePremium(ICollateral coll) public view returns (uint192) {
367:        // `coll` does not need validation
368:        if (!enableIssuancePremium || coll.lastSave() != block.timestamp) return FIX_ONE;
369:
370:        // Use try-catch for safety since `savedPegPrice()` was only added in 4.0.0 to ICollateral
371:        try coll.savedPegPrice() returns (uint192 pegPrice) {
372:            if (pegPrice == 0) return FIX_ONE;
373:            uint192 targetPerRef = coll.targetPerRef(); // {target/ref}
374:            if (pegPrice >= targetPerRef) return FIX_ONE;
375:
376:            // {1} = {target/ref} / {target/ref}
377:            return targetPerRef.safeDiv(pegPrice, CEIL);
378:        } catch {
379:            // if savedPegPrice() does not exist on the collateral the error bytes are 0 len
380:            return FIX_ONE;
381:        }
382:    }
383:
384:    /// Returns the quantity of collateral token in a BU
385:    /// @param erc20 The token contract
386:    /// @param coll The registered collateral plugin contract
387:    /// @return {tok/BU} The token-quantity of an ERC20 token in the basket
388:    // Returns 0 if coll is not in the basket
389:    // Returns FIX_MAX (in lieu of +infinity) if Collateral.refPerTok() is 0.
390:    // Otherwise returns (token's basket.refAmts / token's Collateral.refPerTok())
391:    function _quantity(
392:        IERC20 erc20,
393:        ICollateral coll,
394:        RoundingMode rounding
395:    ) internal view returns (uint192) {
396:        uint192 refPerTok = coll.refPerTok();
397:        if (refPerTok == 0) return FIX_MAX;
398:
399:        // {tok/BU} = {ref/BU} / {ref/tok}
400:        return basket.refAmts[erc20].div(refPerTok, rounding);
401:    }
402:
403:    /// Returns the price of a BU (including issuance premium)
404:    /// Included for backwards compatibility with <4.0.0
405:    /// Should not revert
406:    /// @return low {UoA/BU} The lower end of the price estimate
407:    /// @return high {UoA/BU} The upper end of the price estimate
408:    // returns sum(quantity(erc20) * price(erc20) for erc20 in basket.erc20s)
409:    function price() external view returns (uint192 low, uint192 high) {
410:        return price(true);
411:    }
412:
413:    /// Returns the price of a BU
414:    /// Should not revert
415:    /// @param applyIssuancePremium Whether to apply the issuance premium to the high price
416:    /// @return low {UoA/BU} The lower end of the price estimate
417:    /// @return high {UoA/BU} The upper end of the price estimate
418:    // returns sum(quantity(erc20) * price(erc20) for erc20 in basket.erc20s)
419:    function price(bool applyIssuancePremium) public view returns (uint192 low, uint192 high) {
420:        uint256 low256;
421:        uint256 high256;
422:
423:        uint256 len = basket.erc20s.length;
424:        for (uint256 i = 0; i < len; ++i) {
425:            try assetRegistry.toColl(basket.erc20s[i]) returns (ICollateral coll) {
426:                uint192 qty = _quantity(basket.erc20s[i], coll, CEIL);
427:                if (qty == 0) continue;
428:
429:                (uint192 lowP, uint192 highP) = coll.price();
430:
431:                low256 += qty.safeMul(lowP, FLOOR);
432:
433:                if (high256 < FIX_MAX) {
434:                    if (highP == FIX_MAX) {
435:                        high256 = FIX_MAX;
436:                        continue;
437:                    }
438:
439:                    if (applyIssuancePremium) {
440:                        uint192 premium = issuancePremium(coll); // {1} always CEIL
441:
442:                        // {tok} = {tok} * {1}
443:                        if (premium > FIX_ONE) qty = qty.safeMul(premium, CEIL);
444:                    }
445:
446:                    high256 += qty.safeMul(highP, CEIL);
447:                }
448:            } catch (bytes memory errData) {
449:                // see: docs/solidity-style.md#Catching-Empty-Data
450:                if (errData.length == 0) revert(); // solhint-disable-line reason-string
451:                continue;
452:            }
453:        }
454:
455:        // safe downcast: FIX_MAX is type(uint192).max
456:        low = low256 >= FIX_MAX ? FIX_MAX : uint192(low256);
457:        high = high256 >= FIX_MAX ? FIX_MAX : uint192(high256);
458:    }
459:
460:    /// Return the current issuance/redemption quantities for `amount` BUs
461:    /// Included for backwards compatibility with <4.0.0
462:    /// @param rounding If CEIL, apply issuance premium
463:    /// @param amount {BU}
464:    /// @return erc20s The backing collateral erc20s
465:    /// @return quantities {qTok} ERC20 token quantities equal to `amount` BUs
466:    // Returns (erc20s, [quantity(e) * amount {as qTok} for e in erc20s])
467:    function quote(uint192 amount, RoundingMode rounding)
468:        external
469:        view
470:        returns (address[] memory erc20s, uint256[] memory quantities)
471:    {
472:        return quote(amount, rounding == CEIL, rounding);
473:    }
474:
475:    /// Return the current issuance/redemption quantities for `amount` BUs
476:    /// @dev Subset of logic of quoteCustomRedemption; more gas efficient for current nonce
477:    /// @param amount {BU}
478:    /// @param applyIssuancePremium Whether to apply the issuance premium
479:    /// @return erc20s The backing collateral erc20s
480:    /// @return quantities {qTok} ERC20 token quantities equal to `amount` BUs
481:    // Returns (erc20s, [quantity(e) * amount {as qTok} for e in erc20s])
482:    function quote(
483:        uint192 amount,
484:        bool applyIssuancePremium,
485:        RoundingMode rounding
486:    ) public view returns (address[] memory erc20s, uint256[] memory quantities) {
487:        uint256 length = basket.erc20s.length;
488:        erc20s = new address[](length);
489:        quantities = new uint256[](length);
490:
491:        for (uint256 i = 0; i < length; ++i) {
492:            erc20s[i] = address(basket.erc20s[i]);
493:            ICollateral coll = assetRegistry.toColl(IERC20(erc20s[i]));
494:
495:            // {tok} = {tok/BU} * {BU}
496:            uint192 q = _quantity(basket.erc20s[i], coll, rounding).safeMul(amount, rounding);
497:
498:            // Prevent toxic issuance by charging more when collateral is under peg
499:            if (applyIssuancePremium) {
500:                uint192 premium = issuancePremium(coll); // {1} always CEIL by definition
501:
502:                // {tok} = {tok} * {1}
503:                if (premium > FIX_ONE) q = q.safeMul(premium, rounding);
504:            }
505:
506:            // {qTok} = {tok} * {qTok/tok}
507:            quantities[i] = q.shiftl_toUint(
508:                int8(IERC20Metadata(address(basket.erc20s[i])).decimals()),
509:                rounding
510:            );
511:        }
512:    }
513:
514:    /// Return the redemption value of `amount` BUs for a linear combination of historical baskets
515:    /// @param basketNonces An array of basket nonces to do redemption from
516:    /// @param portions {1} An array of Fix quantities
517:    /// @param amount {BU}
518:    /// @return erc20s The backing collateral erc20s
519:    /// @return quantities {qTok} ERC20 token quantities equal to `amount` BUs
520:    // Returns (erc20s, [quantity(e) * amount {as qTok} for e in erc20s])
521:    function quoteCustomRedemption(
522:        uint48[] memory basketNonces,
523:        uint192[] memory portions,
524:        uint192 amount
525:    ) external view returns (address[] memory erc20s, uint256[] memory quantities) {
526:        require(basketNonces.length == portions.length, "invalid lengths");
527:
528:        IERC20[] memory erc20sAll = new IERC20[](assetRegistry.size());
529:        ICollateral[] memory collsAll = new ICollateral[](erc20sAll.length);
530:        uint192[] memory refAmtsAll = new uint192[](erc20sAll.length);
531:
532:        uint256 len; // length of return arrays
533:
534:        // Calculate the linear combination basket
535:        for (uint48 i = 0; i < basketNonces.length; ++i) {
536:            require(
537:                basketNonces[i] >= lastCollateralized && basketNonces[i] <= nonce,
538:                "invalid basketNonce"
539:            );
540:            // Known limitation: During an ongoing rebalance it may possible to redeem
541:            // on a previous basket nonce for _more_ UoA value than the current basket.
542:            // This can only occur for index RTokens, and the risk has been mitigated
543:            // by updating `lastCollateralized` on every assetRegistry.refresh().
544:
545:            // Add-in refAmts contribution from historical basket
546:            Basket storage b = basketHistory[basketNonces[i]];
547:            for (uint256 j = 0; j < b.erc20s.length; ++j) {
548:                // untestable:
549:                //     previous baskets erc20s do not contain the zero address
550:                if (address(b.erc20s[j]) == address(0)) continue;
551:
552:                // Search through erc20sAll
553:                uint256 erc20Index = type(uint256).max;
554:                for (uint256 k = 0; k < len; ++k) {
555:                    if (b.erc20s[j] == erc20sAll[k]) {
556:                        erc20Index = k;
557:                        break;
558:                    }
559:                }
560:
561:                // Add new ERC20 entry if not found
562:                uint192 amt = portions[i].mul(b.refAmts[b.erc20s[j]], FLOOR);
563:                if (erc20Index == type(uint256).max) {
564:                    // New entry found
565:
566:                    try assetRegistry.toAsset(b.erc20s[j]) returns (IAsset asset) {
567:                        if (!asset.isCollateral()) continue; // skip token if not collateral
568:
569:                        erc20sAll[len] = b.erc20s[j];
570:                        collsAll[len] = ICollateral(address(asset));
571:
572:                        // {ref} = {1} * {ref}
573:                        refAmtsAll[len] = amt;
574:                        ++len;
575:                    } catch (bytes memory errData) {
576:                        // untested:
577:                        //     OOG pattern tested in other contracts, cost to test here is high
578:                        // see: docs/solidity-style.md#Catching-Empty-Data
579:                        if (errData.length == 0) revert(); // solhint-disable-line reason-string
580:                        // skip token if no longer registered or other non-gas issue
581:                    }
582:                } else {
583:                    // {ref} = {1} * {ref}
584:                    refAmtsAll[erc20Index] += amt;
585:                }
586:            }
587:        }
588:
589:        erc20s = new address[](len);
590:        quantities = new uint256[](len);
591:
592:        // Calculate quantities
593:        for (uint256 i = 0; i < len; ++i) {
594:            erc20s[i] = address(erc20sAll[i]);
595:
596:            // {tok} = {BU} * {ref/BU} / {ref/tok}
597:            quantities[i] = amount
598:            .safeMulDiv(refAmtsAll[i], collsAll[i].refPerTok(), FLOOR)
599:            .shiftl_toUint(int8(collsAll[i].erc20Decimals()), FLOOR);
600:        }
601:    }
602:
603:    /// @return baskets {BU}
604:    ///          .top The number of partial basket units: e.g max(coll.map((c) => c.balAsBUs())
605:    ///          .bottom The number of whole basket units held by the account
606:    /// @dev Returns (FIX_ZERO, FIX_MAX) for an empty or DISABLED basket
607:    // Returns:
608:    //    (0, 0), if (basket.erc20s is empty) or (disabled is true) or (status() is DISABLED)
609:    //    min(e.balanceOf(account) / quantity(e) for e in basket.erc20s if quantity(e) > 0),
610:    function basketsHeldBy(address account) public view returns (BasketRange memory baskets) {
611:        uint256 length = basket.erc20s.length;
612:        if (length == 0 || disabled) return BasketRange(FIX_ZERO, FIX_MAX);
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
629:
630:    // === Governance Setters ===
631:
632:    /// @custom:governance
633:    function setWarmupPeriod(uint48 val) public {
634:        requireGovernanceOnly();
635:        require(val >= MIN_WARMUP_PERIOD && val <= MAX_WARMUP_PERIOD, "invalid warmupPeriod");
636:        emit WarmupPeriodSet(warmupPeriod, val);
637:        warmupPeriod = val;
638:    }
639:
640:    /// @custom:governance
641:    function setIssuancePremiumEnabled(bool val) public {
642:        requireGovernanceOnly();
643:        emit EnableIssuancePremiumSet(enableIssuancePremium, val);
644:        enableIssuancePremium = val;
645:    }
646:
647:    // === Private ===
648:
649:    // contract-size-saver
650:    // solhint-disable-next-line no-empty-blocks
651:    function requireGovernanceOnly() private governance {}
652:
653:    /// Select and save the next basket, based on the BasketConfig and Collateral statuses
654:    function _switchBasket() private {
655:        // Mark basket disabled. Pause most protocol functions unless there is a next basket
656:        disabled = true;
657:
658:        bool success = _newBasket.nextBasket(_targetNames, config, assetRegistry);
659:        // if success is true: _newBasket is the next basket
660:
661:        if (success) {
662:            // nonce' = nonce + 1
663:            // basket' = _newBasket
664:            // timestamp' = now
665:
666:            nonce += 1;
667:            basket.setFrom(_newBasket);
668:            basketHistory[nonce].setFrom(_newBasket);
669:            timestamp = uint48(block.timestamp);
670:            disabled = false;
671:        }
672:
673:        // Keep records, emit event
674:        uint256 len = basket.erc20s.length;
675:        uint192[] memory refAmts = new uint192[](len);
676:        for (uint256 i = 0; i < len; ++i) {
677:            refAmts[i] = basket.refAmts[basket.erc20s[i]];
678:        }
679:        emit BasketSet(nonce, basket.erc20s, refAmts, disabled);
680:    }
681:
682:    /// Require that erc20s is a valid collateral array
683:    function requireValidCollArray(IERC20[] calldata erc20s) private view {
684:        for (uint256 i = 0; i < erc20s.length; ++i) {
685:            require(
686:                erc20s[i] != rsr &&
687:                    erc20s[i] != IERC20(address(rToken)) &&
688:                    erc20s[i] != IERC20(address(stRSR)) &&
689:                    erc20s[i] != IERC20(address(0)),
690:                "invalid collateral"
691:            );
692:        }
693:
694:        require(ArrayLib.allUnique(erc20s), "contains duplicates");
695:    }
696:
697:    // ==== ReadFacet views ====
698:    // Not used in-protocol; helpful for reconstructing state
699:
700:    /// Get a reference basket in today's collateral tokens, by nonce
701:    /// @param basketNonce {basketNonce}
702:    /// @return erc20s The erc20s in the reference basket
703:    /// @return quantities {qTok/BU} The quantity of whole tokens per whole basket unit
704:    function getHistoricalBasket(uint48 basketNonce)
705:        external
706:        view
707:        returns (IERC20[] memory erc20s, uint256[] memory quantities)
708:    {
709:        Basket storage b = basketHistory[basketNonce];
710:        erc20s = new IERC20[](b.erc20s.length);
711:        quantities = new uint256[](erc20s.length);
712:
713:        for (uint256 i = 0; i < b.erc20s.length; ++i) {
714:            erc20s[i] = b.erc20s[i];
715:
716:            try assetRegistry.toAsset(IERC20(erc20s[i])) returns (IAsset asset) {
717:                if (!asset.isCollateral()) continue; // skip token if no longer registered
718:
719:                // {tok} = {BU} * {ref/BU} / {ref/tok}
720:                quantities[i] = b
721:                .refAmts[erc20s[i]]
722:                .safeDiv(ICollateral(address(asset)).refPerTok(), FLOOR)
723:                .shiftl_toUint(int8(asset.erc20Decimals()), FLOOR);
724:            } catch (bytes memory errData) {
725:                // untested:
726:                //     OOG pattern tested in other contracts, cost to test here is high
727:                // see: docs/solidity-style.md#Catching-Empty-Data
728:                if (errData.length == 0) revert(); // solhint-disable-line reason-string
729:            }
730:        }
731:    }
732:
733:    /// Getter part1 for `config` struct variable
734:    /// @dev Indices are shared across return values
735:    /// @return erc20s The erc20s in the prime basket
736:    /// @return targetNames The bytes32 name identifier of the target unit, per ERC20
737:    /// @return targetAmts {target/BU} The amount of the target unit in the basket, per ERC20
738:    function getPrimeBasket()
739:        external
740:        view
741:        returns (
742:            IERC20[] memory erc20s,
743:            bytes32[] memory targetNames,
744:            uint192[] memory targetAmts
745:        )
746:    {
747:        erc20s = new IERC20[](config.erc20s.length);
748:        targetNames = new bytes32[](erc20s.length);
749:        targetAmts = new uint192[](erc20s.length);
750:
751:        for (uint256 i = 0; i < erc20s.length; ++i) {
752:            erc20s[i] = config.erc20s[i];
753:            targetNames[i] = config.targetNames[erc20s[i]];
754:            targetAmts[i] = config.targetAmts[erc20s[i]];
755:        }
756:    }
757:
758:    /// Getter part2 for `config` struct variable
759:    /// @param targetName The name of the target unit to lookup the backup for
760:    /// @return erc20s The backup erc20s for the target unit, in order of most to least desirable
761:    /// @return max The maximum number of tokens from the array to use at a single time
762:    function getBackupConfig(bytes32 targetName)
763:        external
764:        view
765:        returns (IERC20[] memory erc20s, uint256 max)
766:    {
767:        BackupConfig storage backup = config.backups[targetName];
768:        erc20s = new IERC20[](backup.erc20s.length);
769:        for (uint256 i = 0; i < erc20s.length; ++i) {
770:            erc20s[i] = backup.erc20s[i];
771:        }
772:        max = backup.max;
773:    }
774:
775:    // ==== Storage Gap ====
776:
777:    /**
778:     * @dev This empty reserved space is put in place to allow future versions to add new
779:     * variables without shifting down storage in the inheritance chain.
780:     * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
781:     *
782:     * BasketHandler uses 58 slots, not 50.
783:     */
784:    uint256[36] private __gap;
```
[37](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L37-L784), 


#### Recommendation

To optimize storage and reduce gas costs, rearrange the storage variables in a way that makes the most of each 32-byte storage slot.

### Don't emit events inside a loop
Emitting an event has an overhead of 375 gas, which will be incurred on every iteration of the loop. It is cheaper to emit only once after the loop has finished.

```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

47:            emit AssetPluginRegistryUpdated(versionHash, _asset, true);	// @audit-issue

75:            emit AssetPluginRegistryUpdated(versionHash, _asset, _validities[i]);	// @audit-issue

103:            emit AssetPluginRegistryUpdated(_versionHash, asset, _validities[i]);	// @audit-issue
```
[47](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L47-L47), [75](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L75-L75), [103](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L103-L103), 


#### Recommendation

To optimize gas usage, avoid emitting events inside loops in Solidity. Instead, emit a single event after the loop completes, thereby saving the 375 gas overhead incurred on each iteration.

### `Internal` functions only called once can be inlined to save gas
If an internal function is only used once, there is no need to modularize it, unless the function calling it would otherwise be too long and complex. Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```solidity
Path: ./contracts/libraries/Throttle.sol

69:    function currentlyAvailable(Throttle storage throttle, uint256 limit)	// @audit-issue

80:    function hourlyLimit(Throttle storage throttle, uint256 supply)	// @audit-issue
```
[69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L69-L69), [80](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L80-L80), 


```solidity
Path: ./contracts/libraries/Fixed.sol

317:    function powu(uint192 x_, uint48 y) internal pure returns (uint192) {	// @audit-issue
```
[317](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L317-L317), 


```solidity
Path: ./contracts/p1/StRSR.sol

640:    function pushDraft(address account, uint256 rsrAmount)	// @audit-issue

842:    function _mint(address account, uint256 amount) internal virtual {	// @audit-issue

856:    function _burn(address account, uint256 amount) internal virtual {	// @audit-issue

887:    function _spendAllowance(	// @audit-issue

955:    function _useNonce(address owner) internal returns (uint256 current) {	// @audit-issue

961:    function _useDelegationNonce(address owner) internal returns (uint256 current) {	// @audit-issue
```
[640](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L640-L640), [842](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L842-L842), [856](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L856-L856), [887](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L887-L887), [955](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L955-L955), [961](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L961-L961), 


```solidity
Path: ./contracts/p1/mixins/RewardableLib.sol

24:    function claimRewards(IAssetRegistry reg) internal {	// @audit-issue

39:    function claimRewardsSingle(IAsset asset) internal {	// @audit-issue
```
[24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L24-L24), [39](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L39-L39), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

147:    function _cancel(	// @audit-issue
```
[147](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L147-L147), 


#### Recommendation

Inline 'internal' functions in Solidity that are called only once to save gas. This avoids the additional gas cost of 20 to 40 units associated with extra JUMP instructions and stack operations required for separate function calls, unless the calling function becomes too complex.

### `Private` functions only called once can be inlined to save gas
If a private function is only used once, there is no need to modularize it, unless the function calling it would otherwise be too long and complex. Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```solidity
Path: ./contracts/mixins/ComponentRegistry.sol

35:    function _setRToken(IRToken val) private {	// @audit-issue

43:    function _setStRSR(IStRSR val) private {	// @audit-issue

51:    function _setAssetRegistry(IAssetRegistry val) private {	// @audit-issue

59:    function _setBasketHandler(IBasketHandler val) private {	// @audit-issue

67:    function _setBackingManager(IBackingManager val) private {	// @audit-issue

75:    function _setDistributor(IDistributor val) private {	// @audit-issue

83:    function _setRSRTrader(IRevenueTrader val) private {	// @audit-issue

91:    function _setRTokenTrader(IRevenueTrader val) private {	// @audit-issue

99:    function _setFurnace(IFurnace val) private {	// @audit-issue

107:    function _setBroker(IBroker val) private {	// @audit-issue
```
[35](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L35-L35), [43](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L43-L43), [51](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L51-L51), [59](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L59-L59), [67](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L67-L67), [75](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L75-L75), [83](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L83-L83), [91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L91-L91), [99](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L99-L99), [107](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L107-L107), 


```solidity
Path: ./contracts/p1/BackingManager.sol

306:    function compromiseBasketsNeeded(uint192 basketsHeldBottom) private {	// @audit-issue
```
[306](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L306-L306), 


```solidity
Path: ./contracts/p1/StRSR.sol

700:    function leakyRefresh(uint256 rsrWithdrawal) private {	// @audit-issue

744:    function _requireNotFrozen() private notFrozen {}	// @audit-issue
```
[700](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L700-L700), [744](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L744-L744), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

654:    function _switchBasket() private {	// @audit-issue
```
[654](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L654-L654), 


```solidity
Path: ./contracts/p1/Broker.sol

243:    function newBatchAuction(TradeRequest memory req, address caller) private returns (ITrade) {	// @audit-issue

268:    function newDutchAuction(	// @audit-issue
```
[243](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L243-L243), [268](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L268-L268), 


```solidity
Path: ./contracts/p1/mixins/TradeLib.sol

182:    function maxTradeSize(	// @audit-issue
```
[182](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L182-L182), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

274:    function nextTradePair(	// @audit-issue

381:    function isBetterSurplus(	// @audit-issue
```
[274](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L274-L274), [381](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L381-L381), 


#### Recommendation

Inline 'private' functions in Solidity that are called only once to save gas. This avoids the additional gas cost of 20 to 40 units associated with extra JUMP instructions and stack operations required for separate function calls, unless the calling function becomes too complex.

### Inline modifiers used only once
In Solidity, modifiers provide a way to add reusable conditions or logic to functions. However, if a modifier is used only once, the modularization may not be beneficial in terms of gas efficiency. The overhead associated with a modifier – including two extra JUMP instructions and additional stack operations for the function call – results in an extra cost of about 20 to 40 gas. In cases where a modifier is unique to a single function and the function’s complexity does not necessitate breaking out logic, inlining the modifier's code directly within the function can be more gas-efficient. This approach avoids the overhead of a separate modifier call while maintaining code clarity.

```solidity
Path: ./contracts/p1/mixins/Component.sol

46:    modifier notIssuancePausedOrFrozen() {	// @audit-issue
```
[46](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L46-L46), 


#### Recommendation

Evaluate the use of modifiers in your Solidity contracts, particularly those applied to only one function. If a modifier is unique to a single function, consider inlining its logic within the function itself to save gas. This is especially advantageous for simpler functions where the additional clarity provided by a separate modifier does not outweigh the gas cost of its use. Ensure that the function remains clear and maintainable after inlining the modifier's logic, and document the rationale for inlining to maintain code readability and facilitate future maintenance.

### Optimizing Arithmetic with Shift Operators for Division and Multiplication by Powers of Two
In computational operations, especially within contexts where efficiency matters, certain arithmetic operations can be optimized. One such optimization is leveraging shift operators for division and multiplication by powers of two. This stems from the binary nature of numbers in computing, where shifting bits to the left (using `<<`) effectively multiplies a number by 2 for each position shifted, and shifting bits to the right (using `>>`) divides the number by 2 for each position shifted.

In Solidity, and many other programming languages, using shift operators can be more gas-efficient than traditional arithmetic operations for these specific cases. For instance, instead of performing `value * 4`, one can use `value << 2`, and instead of `value / 4`, one can use `value >> 2`. These optimizations can result in reduced gas costs and faster execution times.


```solidity
Path: ./contracts/libraries/Fixed.sol

165:        if (numerator % divisor > (divisor - 1) / 2) {	// @audit-issue

311:    uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;	// @audit-issue

324:            if (y & 1 == 1) result = (result * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;	// @audit-issue

327:            x = (x * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;	// @audit-issue

515:            if (rounding == RoundingMode.ROUND) shiftDelta += (FIX_ONE / 2);	// @audit-issue

600:                if (mm > ((c_256 - 1) / 2)) result_256 += 1;	// @audit-issue

663:        if (mm > ((z - 1) / 2)) result += 1; // z should be z-1	// @audit-issue
```
[165](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L165-L165), [311](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L311-L311), [324](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L324-L324), [327](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L327-L327), [515](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L515-L515), [600](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L600-L600), [663](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L663-L663), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

260:            gas > (64 * GAS_FOR_BH_QTY) / 63 + GAS_FOR_DISABLE_BASKET,	// @audit-issue
```
[260](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L260-L260), 


```solidity
Path: ./contracts/p1/StRSR.sol

40:    uint48 private constant MIN_UNSTAKING_DELAY = 60 * 2; // {s} 2 minutes	// @audit-issue

504:        return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method	// @audit-issue

530:            test = (left + right) / 2; // left < test < right because left < right - 1	// @audit-issue
```
[40](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L40-L40), [504](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L504-L504), [530](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L530-L530), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

372:                (low + high) / 2,	// @audit-issue
```
[372](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L372-L372), 


#### Recommendation

When performing division or multiplication by powers of two in Solidity, consider using shift operators (`<<` for multiplication and `>>` for division) for improved gas efficiency. This optimization takes advantage of the binary nature of computing and can lead to reduced gas costs and faster execution times.

### Redundant State Variable Getters in Solidity
In Solidity, state variables can have different visibility levels, including `public`. When a state variable is declared as `public`, the Solidity compiler automatically generates a getter function for it. This implicit getter has the same name as the state variable and allows external callers to query the variable's value.

A common oversight is the explicit creation of a function that returns the value of a public state variable. This function essentially duplicates the functionality already provided by the automatically generated getter. For instance, if there's a public state variable `uint256 public value;`, there's no need for a function like `function getValue() public view returns (uint256) { return value; }`, as the compiler already provides a `value()` function.



```solidity
Path: ./contracts/p1/AssetRegistry.sol

253:    function _reserveGas() private view returns (uint256) {	// @audit-issue
254:        uint256 gas = gasleft();
255:        // Call to quantity() restricts gas that is passed along to 63 / 64 of gasleft().
256:        // Therefore gasleft() must be greater than 64 * GAS_FOR_BH_QTY / 63
257:        // GAS_FOR_DISABLE_BASKET is a buffer which can be considerably lower without
258:        // security implications.
259:        require(
260:            gas > (64 * GAS_FOR_BH_QTY) / 63 + GAS_FOR_DISABLE_BASKET,
261:            "not enough gas to unregister safely"
262:        );
263:        return GAS_FOR_BH_QTY;
264:    }
```
[253](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L253-L264), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

314:    function _price(uint48 timestamp) private view returns (uint192) {	// @audit-issue
315:        uint48 _startTime = startTime; // {s} gas savings
316:        uint48 _endTime = endTime; // {s} gas savings
317:        require(timestamp >= _startTime, "auction not started");
318:        require(timestamp <= _endTime, "auction over");
319:
320:        /// Price Curve:
321:        ///   - first 20%: geometrically decrease the price from 1000x the bestPrice to 1.5x it
322:        ///   - next  25%: linearly decrease the price from 1.5x the bestPrice to 1x it
323:        ///   - next  50%: linearly decrease the price from bestPrice to worstPrice
324:        ///   - last   5%: constant at worstPrice
325:
326:        uint192 progression = divuu(timestamp - _startTime, _endTime - _startTime); // {1}
327:
328:        // Fast geometric decay -- 0%-20% of auction
329:        if (progression < TWENTY_PERCENT) {
330:            uint192 exp = MAX_EXP.mulDiv(TWENTY_PERCENT - progression, TWENTY_PERCENT, ROUND);
331:
332:            // bestPrice * ((1000000/999999) ^ exp) = bestPrice / ((999999/1000000) ^ exp)
333:            // safe uint48 downcast: exp is at-most 6502287
334:            // {buyTok/sellTok} = {buyTok/sellTok} / {1} ^ {1}
335:            return bestPrice.mulDiv(ONE_POINT_FIVE, BASE.powu(uint48(exp.toUint(ROUND))), CEIL);
336:            // this reverts for bestPrice >= 6.21654046e36 * FIX_ONE
337:        } else if (progression < FORTY_FIVE_PERCENT) {
338:            // First linear decay -- 20%-45% of auction
339:            // 1.5x -> 1x the bestPrice
340:
341:            uint192 _bestPrice = bestPrice; // gas savings
342:            // {buyTok/sellTok} = {buyTok/sellTok} * {1}
343:            uint192 highPrice = _bestPrice.mul(ONE_POINT_FIVE, CEIL);
344:            return
345:                highPrice -
346:                (highPrice - _bestPrice).mulDiv(progression - TWENTY_PERCENT, TWENTY_FIVE_PERCENT);
347:        } else if (progression < NINETY_FIVE_PERCENT) {
348:            // Second linear decay -- 45%-95% of auction
349:            // bestPrice -> worstPrice
350:
351:            uint192 _bestPrice = bestPrice; // gas savings
352:            // {buyTok/sellTok} = {buyTok/sellTok} * {1}
353:            return
354:                _bestPrice -
355:                (_bestPrice - worstPrice).mulDiv(progression - FORTY_FIVE_PERCENT, FIFTY_PERCENT);
356:        }
357:
358:        // Constant price -- 95%-100% of auction
359:        return worstPrice;
360:    }
```
[314](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L314-L360), 


#### Recommendation

Avoid creating explicit getter functions for 'public' state variables in Solidity. The compiler automatically generates getters for such variables, making additional functions redundant. This practice helps reduce contract size, lowers deployment costs, and simplifies maintenance and understanding of the contract.

### Calling External Function With this Keyword
Calling an external function internally, through the use of this wastes the gas overhead of calling an external function (100 gas). Instead, you can change the function from externalto `public` and remove the `this` keyword.


```solidity
Path: ./contracts/p1/RevenueTrader.sol

58:        try this.distributeTokenToBuy() {} catch (bytes memory errData) {	// @audit-issue
```
[58](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L58-L58), 


```solidity
Path: ./contracts/p1/BackingManager.sol

92:            try this.rebalance(trade.KIND()) {} catch (bytes memory errData) {	// @audit-issue
```
[92](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L92-L92), 


#### Recommendation

Avoid unnecessary gas overhead by calling functions directly without using 'this' keyword for internal calls. Change the function visibility from 'external' to 'public' to enable direct internal calls and reduce gas costs.

### Multiple Pragma Definition
In Solidity, pragma statements are used to specify the compiler version and settings for a smart contract. This issue occurs when multiple pragma statements are defined within the same contract or file. Each pragma statement should be unique within a contract or file, as it sets the compiler version and potentially other compiler-specific configurations.


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

Ensure each Solidity contract or file contains a single, unique pragma statement to clearly specify the intended compiler version and settings. Avoid multiple pragma definitions to prevent confusion and potential compilation issues.

### Use `private` Rather than `public` for Constants 
In Solidity, constants represent immutable values that cannot be changed after they are set at compile-time. By default, constants have internal visibility, meaning they can be accessed within the contract they are declared in and in derived contracts. If a constant is explicitly declared as `public`, Solidity automatically generates a getter function for it. While this might seem harmless, it actually incurs a gas overhead, especially when the contract is deployed, as the EVM needs to generate bytecode for that getter. Conversely, declaring constants as `private` ensures that no additional getter is generated, optimizing gas usage.

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

48:    uint8 public constant decimals = 18;	// @audit-issue
```
[48](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L48-L48), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

31:    uint192 public constant MAX_TARGET_AMT = 1e3 * FIX_ONE; // {target/BU} max basket weight	// @audit-issue

32:    uint48 public constant MIN_WARMUP_PERIOD = 60; // {s} 1 minute	// @audit-issue

33:    uint48 public constant MAX_WARMUP_PERIOD = 60 * 60 * 24 * 365; // {s} 1 year	// @audit-issue
```
[31](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L31-L31), [32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L32-L32), [33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L33-L33), 


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
Path: ./contracts/p1/Broker.sol

25:    uint48 public constant MAX_AUCTION_LENGTH = 60 * 60 * 24 * 7; // {s} max valid duration, 1 week	// @audit-issue

28:    uint48 public constant MIN_AUCTION_LENGTH = 60; // {s} 60 seconds auction min duration	// @audit-issue
```
[25](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L25-L25), [28](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L28-L28), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

22:    uint192 public constant MAX_TRADE_VOLUME = 1e29; // {UoA}	// @audit-issue

23:    uint192 public constant MAX_TRADE_SLIPPAGE = 1e18; // {%}	// @audit-issue
```
[22](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L22-L22), [23](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L23-L23), 


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

To optimize gas usage in your Solidity contracts, declare constants with `private` visibility rather than `public` when possible. Using `private` prevents the automatic generation of a getter function, reducing gas overhead, especially during contract deployment.

### Use `Array.unsafeAccess()` to avoid repeated array length checks
When using storage arrays, solidity adds an internal lookup of the array's length (a Gcoldsload **2100 gas**) to ensure you don't read past the array's end. You can avoid this lookup by using [`Array.unsafeAccess()`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/94697be8a3f0dfcd95dfb13ffbd39b5973f5c65d/contracts/utils/Arrays.sol#L57) in cases where the length has already been checked, as is the case with the instances below

```solidity
Path: ./contracts/p1/StRSR.sol

540:        return draftQueues[era_][account].length;	// ('@audit-issue: Length of `draftQueues` already checked. ',)
```
[540](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L540-L540), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

79:        return SafeCastUpgradeable.toUint48(_checkpoints[era][account].length);	// ('@audit-issue: Length of `_checkpoints` already checked. ',)

87:        uint256 pos = _checkpoints[era][account].length;	// ('@audit-issue: Length of `_checkpoints` already checked. ',)

88:        return pos == 0 ? 0 : _checkpoints[era][account][pos - 1].val;	// ('@audit-issue: Length of `_checkpoints` already checked. ',)
```
[79](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L79-L79), [87](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L87-L87), [88](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L88-L88), 


#### Recommendation

Leverage `Array.unsafeAccess()` from OpenZeppelin's `Arrays` library for storage array accesses where the index is already known to be within bounds. Ensure that you have thoroughly checked the array length prior to using this method to avoid any risk of out-of-bounds errors. This approach is particularly beneficial in performance-critical sections of your code, such as loops over storage arrays where the length check is redundant. Incorporating `Array.unsafeAccess()` judiciously can lead to significant gas savings, making your contract more efficient. Always balance the use of such optimizations with the need for code safety and clarity.

### Using `this` to access functions results in an external call, wasting gas
External calls have an overhead of **100 gas**, which can be avoided by not referencing the function using `this`. Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents' functions and change the visibility from `external` to `public`, so make this change if it's required in order to call the function internally.

```solidity
Path: ./contracts/p1/Main.sol

108:        require(keccak256(abi.encodePacked(this.version())) == versionHash, "upgrade mismatch");	// @audit-issue

117:        require(keccak256(abi.encodePacked(this.version())) == versionHash, "upgrade main first");	// @audit-issue
```
[108](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L108-L108), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L117-L117), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

58:        try this.distributeTokenToBuy() {} catch (bytes memory errData) {	// @audit-issue
```
[58](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L58-L58), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

181:                        keccak256(abi.encodePacked(this.version())),	// @audit-issue

225:                    keccak256(abi.encodePacked(this.version())),	// @audit-issue
```
[181](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L181-L181), [225](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L225-L225), 


```solidity
Path: ./contracts/p1/BackingManager.sol

92:            try this.rebalance(trade.KIND()) {} catch (bytes memory errData) {	// @audit-issue
```
[92](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L92-L92), 


#### Recommendation

Adopt a consistent timekeeping mechanism across L1 and L2 solutions when using `block.number` or similar timing mechanisms in your Solidity contracts. Consider using a standard clock or timestamp-based system, especially for functionalities like governance that require consistent timing across chains. For contracts on specific L2 solutions, ensure you're using the correct method to obtain block numbers consistent with the intended behavior. Keep abreast of standards and best practices, such as those suggested by OpenZeppelin, and implement appropriate solutions like EIP-6372 to provide a consistent and reliable timing mechanism across different blockchain environments.

### Consider pre-calculating the address of `address(this)` to save gas
Use `foundry`'s [`script.sol`](https://book.getfoundry.sh/reference/forge-std/compute-create-address) or `solady`'s [`LibRlp.sol`](https://github.com/Vectorized/solady/blob/main/src/utils/LibRLP.sol) to save the value in a constant, which will avoid having to spend gas to push the value on the stack every time it's used.

```solidity
Path: ./contracts/libraries/Allowance.sol

28:        require(token.allowance(address(this), spender) == 0, "allowance not 0");	// @audit-issue

35:            success = token.allowance(address(this), spender) == value;	// @audit-issue

44:            require(token.allowance(address(this), spender) >= value, "allowance missing");	// @audit-issue
```
[28](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L28-L28), [35](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L35-L35), [44](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L44-L44), 


```solidity
Path: ./contracts/p1/Main.sol

107:        _upgradeProxy(address(this), address(implementation.main));	// @audit-issue

154:        require(msg.sender == address(this), "not self");	// @audit-issue
```
[107](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L107-L107), [154](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L154-L154), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

91:            erc20s[i].safeTransfer(address(backingManager), erc20s[i].balanceOf(address(this)));	// @audit-issue

158:            require(erc20.balanceOf(address(this)) != 0, "0 balance");	// @audit-issue

166:                sellAmount: assetToSell.bal(address(this)),	// @audit-issue

190:        uint256 bal = tokenToBuy.balanceOf(address(this));	// @audit-issue
```
[91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L91-L91), [158](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L158-L158), [166](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L166-L166), [190](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L190-L190), 


```solidity
Path: ./contracts/p1/Furnace.sol

38:        lastPayoutBal = rToken.balanceOf(address(this));	// @audit-issue

77:        lastPayoutBal = rToken.balanceOf(address(this)) - amount;	// @audit-issue
```
[38](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L38-L38), [77](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L77-L77), 


```solidity
Path: ./contracts/p1/BackingManager.sol

116:            _msgSender() == address(this) || tradeEnd[kind] < block.timestamp,	// @audit-issue

124:        BasketRange memory basketsHeld = basketHandler.basketsHeldBy(address(this));	// @audit-issue

130:        uint256 balance = rToken.balanceOf(address(this));	// @audit-issue

160:                uint256 bal = sellERC20.balanceOf(address(this));	// @audit-issue

185:        BasketRange memory basketsHeld = basketHandler.basketsHeldBy(address(this));	// @audit-issue

213:        if (rsr.balanceOf(address(this)) != 0) {	// @audit-issue

215:            IERC20(address(rsr)).safeTransfer(address(stRSR), rsr.balanceOf(address(this)));	// @audit-issue

242:            uint192 bal = asset.bal(address(this));	// @audit-issue

295:            ctx.bals[i] = reg.assets[i].bal(address(this)) + tokensOut[reg.erc20s[i]];	// @audit-issue
```
[116](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L116-L116), [124](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L124-L124), [130](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L130-L130), [160](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L160-L160), [185](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L185-L185), [213](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L213-L213), [215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L215-L215), [242](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L242-L242), [295](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L295-L295), 


```solidity
Path: ./contracts/p1/StRSR.sol

196:        rsrRewardsAtLastPayout = main_.rsr().balanceOf(address(this));	// @audit-issue

237:        IERC20Upgradeable(address(rsr)).safeTransferFrom(caller, address(this), rsrAmount);	// @audit-issue

431:        uint256 rsrBalance = rsr.balanceOf(address(this));	// @audit-issue

694:        return rsr.balanceOf(address(this)) - stakeRSR - draftRSR;	// @audit-issue

908:        require(to != address(this), "transfer to self");	// @audit-issue
```
[196](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L196-L196), [237](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L237-L237), [431](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L431-L431), [694](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L694-L694), [908](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L908-L908), 


```solidity
Path: ./contracts/p1/Deployer.sol

114:        require(owner != address(0) && owner != address(this), "invalid owner");	// @audit-issue

260:        main.renounceRole(OWNER, address(this));	// @audit-issue
```
[114](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L114-L114), [260](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L260-L260), 


```solidity
Path: ./contracts/p1/RToken.sol

422:            erc20.balanceOf(address(this))	// @audit-issue

535:        require(to != address(this), "RToken transfer to self");	// @audit-issue
```
[422](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L422-L422), [535](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L535-L535), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

98:        initBal = sell.balanceOf(address(this)); // {qSellTok}	// @audit-issue

194:        uint256 sellBal = sell.balanceOf(address(this));	// @audit-issue

203:        boughtAmt = buy.balanceOf(address(this));	// @audit-issue

227:        IERC20Upgradeable(address(erc20)).safeTransfer(origin, erc20.balanceOf(address(this)));	// @audit-issue
```
[98](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L98-L98), [194](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L194-L194), [203](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L203-L203), [227](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L227-L227), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

176:        require(sellAmount_ <= sell.balanceOf(address(this)), "unfunded trade");	// @audit-issue

219:        buy.safeTransferFrom(msg.sender, address(this), amountIn);	// @audit-issue

257:        uint256 balanceBefore = buy.balanceOf(address(this)); // {qBuyTok}	// @audit-issue

260:            amountIn <= buy.balanceOf(address(this)) - balanceBefore,	// @audit-issue

290:        boughtAmt = buy.balanceOf(address(this)); // {qBuyTok}	// @audit-issue

292:        sell.safeTransfer(address(origin), sell.balanceOf(address(this))); // {qSellTok}	// @audit-issue

300:        erc20.safeTransfer(address(origin), erc20.balanceOf(address(this)));	// @audit-issue
```
[176](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L176-L176), [219](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L219-L219), [257](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L257-L257), [260](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L260-L260), [290](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L290-L290), [292](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L292-L292), [300](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L300-L300), 


#### Recommendation

To enhance gas efficiency, cache the contract's address by storing `address(this)` in a state variable at the point of contract deployment or initialization. Use this cached address throughout the contract instead of repeatedly calling `address(this)`. This practice reduces the gas cost associated with multiple computations of the contract's address, leading to more efficient contract execution, especially in scenarios with frequent usage of the contract's address.

### All variables can be packed into fewer storage slots by truncating timestamp bytes
By using a `uint32` rather than a larger type for variables that track timestamps, one can save gas by using fewer storage slots per struct, at the expense of the protocol breaking after the year 2106 (when `uint32` wraps). If this is an acceptable tradeoff, if variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (**20000 gas**). Reads of the variables can also be cheaper

```solidity
Path: ./contracts/p1/StRSR.sol

926:        uint256 deadline,	// @audit-issue
```
[926](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L926-L926), 


#### Recommendation

Evaluate the use of `uint32` for timestamp variables in your Solidity contracts, especially within structs that are frequently written to or read from storage. Ensure that the reduced size will not impact the functionality of your contract, particularly considering the 2106 overflow limitation. When implementing this optimization, aim to group such variables together in the same storage slot to maximize gas savings. This strategy is most effective when these variables are updated together, as in a constructor or specific functions, allowing for a single storage operation (`Gsset`) instead of multiple. Always balance the benefits of gas savings against the longevity and future-proofing of your contract.

### Counting down in for statements is more gas efficient
Looping downwards in Solidity is more gas efficient due to how the EVM compares variables. In a 'for' loop that counts down, the end condition is usually to compare with zero, which is cheaper than comparing with another number. As such, restructure your loops to count downwards where possible.

```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

39:        for (uint256 i = 0; i < validForVersions.length; ++i) {	// @audit-issue

67:        for (uint256 i = 0; i < _versionHashes.length; ++i) {	// @audit-issue

95:        for (uint256 i = 0; i < _assets.length; ++i) {	// @audit-issue
```
[39](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L39-L39), [67](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L67-L67), [95](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L95-L95), 


```solidity
Path: ./contracts/libraries/Array.sol

11:        for (uint256 i = 1; i < arrLen; ++i) {	// @audit-issue

12:            for (uint256 j = 0; j < i; ++j) {	// @audit-issue

23:        for (uint256 i = 1; i < arrLen; ++i) {	// @audit-issue
```
[11](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L11-L11), [12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L12-L12), [23](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L23-L23), 


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

535:        for (uint48 i = 0; i < basketNonces.length; ++i) {	// @audit-issue

547:            for (uint256 j = 0; j < b.erc20s.length; ++j) {	// @audit-issue

554:                for (uint256 k = 0; k < len; ++k) {	// @audit-issue

593:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

615:        for (uint256 i = 0; i < length; ++i) {	// @audit-issue

676:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

684:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

713:        for (uint256 i = 0; i < b.erc20s.length; ++i) {	// @audit-issue

751:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

769:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue
```
[142](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L142-L142), [251](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L251-L251), [260](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L260-L260), [296](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L296-L296), [321](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L321-L321), [424](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L424-L424), [491](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L491-L491), [535](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L535-L535), [547](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L547-L547), [554](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L554-L554), [593](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L593-L593), [615](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L615-L615), [676](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L676-L676), [684](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L684-L684), [713](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L713-L713), [751](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L751-L751), [769](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L769-L769), 


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

202:            for (targetIndex = 0; targetIndex < targetsLength; ++targetIndex) {	// @audit-issue

241:        for (uint256 i = 0; i < targetsLength; ++i) {	// @audit-issue

252:            for (uint256 j = 0; j < backup.erc20s.length && size < backup.max; ++j) {	// @audit-issue

263:            for (uint256 j = 0; j < backup.erc20s.length && assigned < size; ++j) {	// @audit-issue

325:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

337:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

363:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

380:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue
```
[72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L72-L72), [80](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L80-L80), [180](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L180-L180), [198](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L198-L198), [202](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L202-L202), [241](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L241-L241), [252](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L252-L252), [263](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L263-L263), [325](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L325-L325), [337](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L337-L337), [363](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L363-L363), [380](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L380-L380), 


#### Recommendation

Where feasible, refactor `for` loops in your Solidity contracts to count downwards. Adjust the loop initialization, condition, and iteration statements to decrement the loop variable and terminate the loop when it reaches zero. This approach can lead to gas savings, making your contract more efficient in terms of execution costs. Ensure that this refactoring aligns with the logic and requirements of your contract, and thoroughly test to confirm that the revised loop behavior matches the intended functionality.

### Use solady library where possible to save gas
The following OpenZeppelin imports have a Solady equivalent, as such they can be used to save GAS as Solady modules have been specifically designed to be as GAS efficient as possible

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

5:import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";	// @audit-issue

6:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L6-L6), 


```solidity
Path: ./contracts/libraries/Array.sol

4:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L4-L4), 


```solidity
Path: ./contracts/libraries/Permit.sol

4:import "@openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Permit.sol#L4-L4), 


```solidity
Path: ./contracts/p1/Main.sol

5:import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";	// @audit-issue

6:import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";	// @audit-issue

7:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L5-L5), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L6-L6), [7](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L7-L7), 


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
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L5-L5), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

4:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L4-L4), 


```solidity
Path: ./contracts/p1/BackingManager.sol

4:import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";	// @audit-issue

5:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L5-L5), 


```solidity
Path: ./contracts/p1/StRSR.sol

5:import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";	// @audit-issue

8:import "@openzeppelin/contracts-upgradeable/utils/CountersUpgradeable.sol";	// @audit-issue

9:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L5-L5), [8](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L8-L8), [9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L9-L9), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

4:import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";	// @audit-issue

5:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L5-L5), 


```solidity
Path: ./contracts/p1/Deployer.sol

6:import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";	// @audit-issue
```
[6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L6-L6), 


```solidity
Path: ./contracts/p1/RToken.sol

5:import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";	// @audit-issue
```
[5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L5-L5), 


```solidity
Path: ./contracts/p1/Broker.sol

4:import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L4-L4), 


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

5:import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";	// @audit-issue
```
[5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L5-L5), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

4:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L4-L4), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

4:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L4-L4), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

4:import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";	// @audit-issue

6:import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L4-L4), [6](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L6-L6), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

4:import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";	// @audit-issue

5:import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L4-L4), [5](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L5-L5), 


#### Recommendation

Evaluate and, where appropriate, integrate Solady modules in your Solidity contracts as alternatives to similar OpenZeppelin imports. Focus on areas where gas efficiency can be significantly improved. Ensure that any replacement with Solady's modules does not compromise the security or functionality of your contracts. Conduct thorough testing and code reviews when making such substitutions to confirm compatibility and maintain the integrity of your application. Stay informed about updates and community feedback on both libraries to make informed decisions about their use in your projects.

### Consider using solady's 'FixedPointMathLib'
Using Solady's "FixedPointMathLib" for multiplication or division operations in Solidity can lead to significant gas savings. This library is designed to optimize fixed-point arithmetic operations, which are common in financial calculations involving tokens or currencies. By implementing more efficient algorithms and assembly optimizations, "FixedPointMathLib" minimizes the computational resources required for these operations. For contracts that frequently perform such calculations, integrating this library can reduce transaction costs, thereby enhancing overall performance and cost-effectiveness. However, developers must ensure compatibility with their existing codebase and thoroughly test for accuracy and expected behavior to avoid any unintended consequences.

```solidity
Path: ./contracts/libraries/Throttle.sol

75:        available = throttle.lastAvailable + (limit * delta) / ONE_HOUR;	// @audit-issue

88:        limit = (supply * params.pctRate) / FIX_ONE_256; // {qRTok}	// @audit-issue
```
[75](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L75-L75), [88](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L88-L88), 


```solidity
Path: ./contracts/libraries/Fixed.sol

217:        return _safeWrap(decimals >= 0 ? x * coeff : _divrnd(x, coeff, rounding));	// @audit-issue

231:        return _safeWrap(x + y * FIX_SCALE);	// @audit-issue

245:        return _safeWrap(uint256(x) - uint256(y * FIX_SCALE));	// @audit-issue

264:        return _safeWrap(_divrnd(uint256(x) * uint256(y), FIX_SCALE, rounding));	// @audit-issue

271:        return _safeWrap(x * y);	// @audit-issue

290:        return _safeWrap(_divrnd(uint256(x) * FIX_SCALE, y, rounding));	// @audit-issue

321:        uint256 x = uint256(x_) * FIX_SCALE; // x is D36	// @audit-issue

324:            if (y & 1 == 1) result = (result * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;	// @audit-issue

327:            x = (x * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;	// @audit-issue

329:        return _safeWrap(result / FIX_SCALE);	// @audit-issue

333:        return _safeWrap(sqrt256(x * FIX_ONE_256)); // FLOOR	// @audit-issue

400:        return decimals >= 0 ? uint256(x * coeff) : uint256(_divrnd(x, coeff, rounding));	// @audit-issue

509:            uint256 rawDelta = uint256(b) * a; // {D36} = {D18} * {D18}	// @audit-issue

511:            if (rawDelta / b != a) return FIX_MAX;	// @audit-issue

515:            if (rounding == RoundingMode.ROUND) shiftDelta += (FIX_ONE / 2);	// @audit-issue

534:            if (shiftDelta / FIX_ONE > FIX_MAX) return FIX_MAX;	// @audit-issue

537:            return uint192(shiftDelta / FIX_ONE); // {D18} = {D36} / {D18}	// @audit-issue

552:        uint256 raw = _divrnd(FIX_ONE_256 * a, uint256(b), rounding);	// @audit-issue

582:            c_256 /= pow2;	// @audit-issue

583:            lo /= pow2;	// @audit-issue

584:            lo += hi * ((0 - pow2) / pow2 + 1);	// @audit-issue

586:            r *= 2 - c_256 * r;	// @audit-issue

587:            r *= 2 - c_256 * r;	// @audit-issue

588:            r *= 2 - c_256 * r;	// @audit-issue

589:            r *= 2 - c_256 * r;	// @audit-issue

590:            r *= 2 - c_256 * r;	// @audit-issue

591:            r *= 2 - c_256 * r;	// @audit-issue

592:            r *= 2 - c_256 * r;	// @audit-issue

593:            r *= 2 - c_256 * r;	// @audit-issue

594:            result_256 = lo * r;	// @audit-issue

600:                if (mm > ((c_256 - 1) / 2)) result_256 += 1;	// @audit-issue
```
[217](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L217-L217), [231](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L231-L231), [245](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L245-L245), [264](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L264-L264), [271](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L271-L271), [290](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L290-L290), [321](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L321-L321), [324](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L324-L324), [327](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L327-L327), [329](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L329-L329), [333](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L333-L333), [400](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L400-L400), [509](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L509-L509), [511](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L511-L511), [515](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L515-L515), [534](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L534-L534), [537](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L537-L537), [552](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L552-L552), [582](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L582-L582), [583](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L583-L583), [584](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L584-L584), [586](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L586-L586), [587](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L587-L587), [588](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L588-L588), [589](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L589-L589), [590](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L590-L590), [591](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L591-L591), [592](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L592-L592), [593](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L593-L593), [594](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L594-L594), [600](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L600-L600), 


```solidity
Path: ./contracts/p1/Distributor.sol

133:            if (totalShares != 0) tokensPerShare = amount / totalShares;	// @audit-issue

152:            uint256 transferAmt = tokensPerShare * numberOfShares;	// @audit-issue

186:                        tokensPerShare * (totalShares - paidOutShares)	// @audit-issue

222:                    (feeNumerator * uint256(revTotals.rTokenTotal + revTotals.rsrTotal)) /	// @audit-issue
```
[133](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L133-L133), [152](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L152-L152), [186](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L186-L186), [222](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L222-L222), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

260:            gas > (64 * GAS_FOR_BH_QTY) / 63 + GAS_FOR_DISABLE_BASKET,	// @audit-issue
```
[260](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L260-L260), 


```solidity
Path: ./contracts/p1/BackingManager.sol

131:        if (balance >= MAX_DISTRIBUTION * MAX_DESTINATIONS) rToken.dissolve(balance);	// @audit-issue

247:                uint256 tokensPerShare = delta / (totals.rTokenTotal + totals.rsrTotal);	// @audit-issue

254:                    erc20s[i].safeTransfer(address(rsrTrader), tokensPerShare * totals.rsrTotal);	// @audit-issue

259:                        tokensPerShare * totals.rTokenTotal	// @audit-issue
```
[131](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L131-L131), [247](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L247-L247), [254](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L254-L254), [259](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L259-L259), 


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

530:            test = (left + right) / 2; // left < test < right because left < right - 1	// @audit-issue

609:            payout = (payoutRatio * rsrRewardsAtLastPayout) / FIX_ONE;	// @audit-issue

623:            : uint192((totalStakes * FIX_ONE_256 + (stakeRSR - 1)) / stakeRSR);	// @audit-issue

648:        uint256 newTotalDrafts = (draftRate * draftRSR) / FIX_ONE;	// @audit-issue

705:        uint192 withdrawal = _safeWrap((rsrWithdrawal * FIX_ONE + totalRSR - 1) / totalRSR); // {1}	// @audit-issue

729:        uint256 newTotalStakes = (stakeRate * newStakeRSR) / FIX_ONE;	// @audit-issue
```
[274](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L274-L274), [326](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L326-L326), [373](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L373-L373), [441](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L441-L441), [448](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L448-L448), [456](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L456-L456), [463](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L463-L463), [472](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L472-L472), [504](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L504-L504), [530](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L530-L530), [609](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L609-L609), [623](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L623-L623), [648](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L648-L648), [705](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L705-L705), [729](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L729-L729), 


```solidity
Path: ./contracts/p1/RToken.sol

409:        uint256 low = (FIX_ONE_256 * basketsNeeded_) / supply; // D18{BU/rTok}	// @audit-issue

410:        uint256 high = (FIX_ONE_256 * basketsNeeded_ + (supply - 1)) / supply; // D18{BU/rTok}	// @audit-issue
```
[409](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L409-L409), [410](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L410-L410), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

372:                (low + high) / 2,	// @audit-issue
```
[372](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L372-L372), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

116:                req.sellAmount * FEE_DENOMINATOR,	// @audit-issue

126:            minBuyAmount / MAX_ORDERS,	// @audit-issue
```
[116](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L116-L116), [126](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L126-L126), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

168:        require(prices.sellLow != 0 && prices.sellHigh < FIX_MAX / 1000, "bad sell pricing");	// @audit-issue

169:        require(prices.buyLow != 0 && prices.buyHigh < FIX_MAX / 1000, "bad buy pricing");	// @audit-issue
```
[168](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L168-L168), [169](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L169-L169), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

82:        return (asMicroPercent * pastSupply + (ONE_HUNDRED_PERCENT - 1)) / ONE_HUNDRED_PERCENT;	// @audit-issue
```
[82](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L82-L82), 


#### Recommendation

Consider integrating Solady's 'FixedPointMathLib' into your Solidity contracts for optimized fixed-point arithmetic operations. This library can provide substantial gas savings and enhance the performance of your contract. Before integration, evaluate how 'FixedPointMathLib' aligns with your contract’s requirements. Ensure thorough testing for accuracy and compatibility with your existing contract logic. Carefully document any changes and keep track of how these optimizations affect your contract's operations to maintain transparency and reliability in your application. Adopting 'FixedPointMathLib' should be a considered decision, balancing the benefits of gas efficiency with the need for maintaining code clarity and functionality.

### Consider using OZ EnumerateSet in place of nested mappings
Nested mappings and multi-dimensional arrays in Solidity operate through a process of double hashing, wherein the original storage slot and the first key are concatenated and hashed, and then this hash is again concatenated with the second key and hashed. This process can be quite gas expensive due to the double-hashing operation and subsequent storage operation (sstore).

A possible optimization involves manually concatenating the keys followed by a single hash operation and an sstore. However, this technique introduces the risk of storage collision, especially when there are other nested hash maps in the contract that use the same key types. Because Solidity is unaware of the number and structure of nested hash maps in a contract, it follows a conservative approach in computing the storage slot to avoid possible collisions.

OpenZeppelin's EnumerableSet provides a potential solution to this problem. It creates a data structure that combines the benefits of set operations with the ability to enumerate stored elements, which is not natively available in Solidity. EnumerableSet handles the element uniqueness internally and can therefore provide a more gas-efficient and collision-resistant alternative to nested mappings or multi-dimensional arrays in certain scenarios.


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

16:    mapping(bytes32 => mapping(address => bool)) private _isValidAsset;	// @audit-issue
```
[16](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L16-L16), 


```solidity
Path: ./contracts/p1/StRSR.sol

63:    mapping(uint256 => mapping(address => uint256)) private stakes; // Stakes per account {qStRSR}	// @audit-issue

71:    mapping(uint256 => mapping(address => mapping(address => uint256))) private _allowances;	// @audit-issue

84:    mapping(uint256 => mapping(address => CumulativeDraft[])) public draftQueues; // {qDrafts}	// @audit-issue

85:    mapping(uint256 => mapping(address => uint256)) public firstRemainingDraft; // draft index	// @audit-issue
```
[63](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L63-L63), [71](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L71-L71), [84](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L84-L84), [85](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L85-L85), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

63:     * @dev Description of the clock	// @audit-issue

71:        return era;	// @audit-issue

84:    }	// @audit-issue

85:	// @audit-issue

41:    mapping(uint256 => mapping(address => Checkpoint[])) private _checkpoints; // {qStRSR}	// @audit-issue
```
[63](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L63-L63), [71](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L71-L71), [84](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L84-L84), [85](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L85-L85), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L41-L41), 


#### Recommendation

Consider using OpenZeppelin's EnumerableSet library as a more gas-efficient and collision-resistant alternative to nested mappings or multi-dimensional arrays, especially when managing unique elements. This library simplifies the handling of unique data sets and allows for the enumeration of elements, a feature not natively supported in Solidity. When refactoring your contract, replace nested mappings or arrays with EnumerableSet where appropriate, ensuring both gas efficiency and enhanced functionality. Be sure to understand the use cases and limitations of EnumerableSet to apply it effectively in your contract's design and implementation.

### Constant Keccak Variables Are Treated As Expressions, Not Constants
In Solidity, state variables or local variables declared with the `constant` keyword are expected to represent constant values that are determined at compile time. These constants typically do not incur any gas costs when accessed since their values are hard-coded into the contract bytecode.

However, this expected behavior deviates when using the `keccak256()` function. When a variable is initialized with a `keccak256()` hash computation, even if declared as `constant`, it isn't truly treated as a constant in the resulting bytecode. Instead, every time this "constant" is referenced, the EVM recalculates the hash. This behavior contrasts with other true constants, which are embedded directly into the bytecode and accessed without additional computations.


```solidity
Path: ./contracts/registry/RoleRegistry.sol

12:    bytes32 public constant EMERGENCY_COUNCIL = keccak256("EMERGENCY_COUNCIL");	// @audit-issue
```
[12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/RoleRegistry.sol#L12-L12), 


```solidity
Path: ./contracts/p1/StRSR.sol

131:    bytes32 private constant _PERMIT_TYPEHASH =	// @audit-issue
132:        keccak256(
133:            "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
134:        );
```
[131](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L131-L134), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

30:    bytes32 private constant _DELEGATE_TYPEHASH =	// @audit-issue
31:        keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");
```
[30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L30-L31), 


#### Recommendation

Avoid using 'keccak256()' to initialize 'constant' variables in Solidity. Instead, precompute the hash value outside of the contract and assign this precomputed value to the constant. This ensures the variable is a true constant, eliminating redundant hash computations and saving gas.

### Use bitmap to save gas
Bitmaps in Solidity are essentially a way of representing a set of boolean values within an integer type variable such as `uint256`. Each bit in the integer represents a true or false value (1 or 0), thus allowing efficient storage of multiple boolean values.

Bitmaps can save gas in the Ethereum network because they condense a lot of information into a small amount of storage. In Ethereum, storage is one of the most significant costs in terms of gas usage. By reducing the amount of storage space needed, you can potentially save on gas fees.

Here's a quick comparison:

If you were to represent 256 different boolean values in the traditional way, you would have to declare 256 different `bool` variables. Given that each `bool` occupies a storage slot and each storage slot costs 20,000 gas to initialize, you would end up paying a considerable amount of gas.

On the other hand, if you were to use a bitmap, you could store these 256 boolean values within a single `uint256` variable. In other words, you'd only pay for a single storage slot, resulting in significant gas savings.

However, it's important to note that while bitmaps can provide gas efficiencies, they do add complexity to the code, making it harder to read and maintain. Also, using bitmaps is efficient only when dealing with a large number of boolean variables that are frequently changed or accessed together. 

In contrast, the straightforward counterpart to bitmaps would be using arrays or mappings to store boolean values, with each `bool` value occupying its own storage slot. This approach is simpler and more readable but could potentially be more expensive in terms of gas usage.

```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

72:        rTokenFeeSet[rToken] = true;	// @audit-issue

78:        rTokenFeeSet[rToken] = false;	// @audit-issue
```
[72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L72-L72), [78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L78-L78), 


```solidity
Path: ./contracts/registry/VersionRegistry.sol

67:        isDeprecated[versionHash] = true;	// @audit-issue
```
[67](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L67-L67), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

45:            _isValidAsset[versionHash][_asset] = true;	// @audit-issue

112:        isDeprecated[_asset] = true;	// @audit-issue
```
[45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L45-L45), [112](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L112-L112), 


```solidity
Path: ./contracts/mixins/Auth.sol

171:        tradingPaused = true;	// @audit-issue

178:        tradingPaused = false;	// @audit-issue

185:        issuancePaused = true;	// @audit-issue

192:        issuancePaused = false;	// @audit-issue
```
[171](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L171-L171), [178](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L178-L178), [185](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L185-L185), [192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L192-L192), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

128:            if (erc20s[i] == IERC20(address(rToken))) involvesRToken = true;	// @audit-issue
```
[128](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L128-L128), 


```solidity
Path: ./contracts/p1/Distributor.sol

157:                if (transferAmt != 0) accountRewards = true;	// @audit-issue

160:                if (transferAmt != 0) accountRewards = true;	// @audit-issue
```
[157](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L157-L157), [160](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L160-L160), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

130:        disabled = true;	// @audit-issue

144:        disabled = true;	// @audit-issue

656:        disabled = true;	// @audit-issue

670:            disabled = false;	// @audit-issue
```
[130](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L130-L130), [144](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L144-L144), [656](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L656-L656), [670](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L670-L670), 


```solidity
Path: ./contracts/p1/RToken.sol

323:                if (allZero) allZero = false;	// @audit-issue
```
[323](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L323-L323), 


```solidity
Path: ./contracts/p1/Broker.sol

157:            batchTradeDisabled = true;	// @audit-issue

163:                dutchTradeDisabled[sell] = true;	// @audit-issue

167:                dutchTradeDisabled[buy] = true;	// @audit-issue

232:        batchTradeDisabled = false;	// @audit-issue

238:        dutchTradeDisabled[erc20] = false;	// @audit-issue

247:        trades[address(trade)] = true;	// @audit-issue

284:        trades[address(trade)] = true;	// @audit-issue
```
[157](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L157-L157), [163](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L163-L163), [167](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L167-L167), [232](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L232-L232), [238](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L238-L238), [247](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L247-L247), [284](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L284-L284), 


#### Recommendation

Consider using bitmaps in your Solidity contracts when you need to store and manipulate a large set of boolean values. This approach is particularly advantageous in terms of gas efficiency for scenarios involving frequent changes or accesses to these values. However, balance this efficiency with code readability and maintainability. Ensure that the use of bitmaps is well-documented, and consider the complexity it introduces into the code. Employ bitmaps judiciously, especially when their use results in significant gas savings, and the logic they represent is a core aspect of the contract's functionality.

### Using `bool`s for storage incurs overhead
[Booleans are more expensive than uint256](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
Use `uint256(0)` and `uint256(1)` for true/false to avoid a Gwarmaccess (**[100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)**) for the extra SLOAD.


```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

16:    mapping(address => bool) private rTokenFeeSet;	// @audit-issue

26:    event RTokenFeeNumeratorSet(address indexed rToken, uint256 feeNumerator, bool isActive);	// @audit-issue
```
[16](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L16-L16), [26](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L26-L26), 


```solidity
Path: ./contracts/registry/VersionRegistry.sol

14:    mapping(bytes32 => bool) public isDeprecated;	// @audit-issue
```
[14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L14-L14), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

16:    mapping(bytes32 => mapping(address => bool)) private _isValidAsset;	// @audit-issue

17:    mapping(address => bool) public isDeprecated;	// @audit-issue

24:    event AssetPluginRegistryUpdated(bytes32 versionHash, address asset, bool validity);	// @audit-issue
```
[16](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L16-L16), [17](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L17-L17), [24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L24-L24), 


```solidity
Path: ./contracts/mixins/Auth.sol

45:    bool public tradingPaused;	// @audit-issue

46:    bool public issuancePaused;	// @audit-issue
```
[45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L45-L45), [46](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L46-L46), 


```solidity
Path: ./contracts/p1/Distributor.sol

142:        bool accountRewards = false;	// @audit-issue
```
[142](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L142-L142), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

56:    bool private disabled;	// @audit-issue

89:    bool public reweightable; // immutable after init	// @audit-issue

96:    bool public enableIssuancePremium;	// @audit-issue
```
[56](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L56-L56), [89](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L89-L89), [96](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L96-L96), 


```solidity
Path: ./contracts/p1/RToken.sol

320:            bool allZero = true;	// @audit-issue
```
[320](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L320-L320), 


```solidity
Path: ./contracts/p1/Broker.sol

49:    bool public batchTradeDisabled;	// @audit-issue

52:    mapping(address => bool) private trades;	// @audit-issue

63:    mapping(IERC20Metadata => bool) public dutchTradeDisabled;	// @audit-issue
```
[49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L49-L49), [52](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L52-L52), [63](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L63-L63), 


#### Recommendation

To minimize gas overhead in your Solidity contracts, consider using `uint256(1)` and `uint256(2)` to represent `true` and `false`, respectively, instead of `bool` types for storage. This approach avoids additional `SLOAD` and `SSTORE` operations, resulting in more gas-efficient code.

### Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Each operation involving a `uint8` costs an extra [22-28](https://gist.github.com/IllIllI000/9388d20c70f9a4632eb3ca7836f54977) gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed


```solidity
Path: ./contracts/mixins/Auth.sol

38:    uint48 public unfreezeAt; // {s} uint48.max to pause indefinitely	// @audit-issue

39:    uint48 public shortFreeze; // {s} length of an initial freeze	// @audit-issue

40:    uint48 public longFreeze; // {s} length of a freeze extension	// @audit-issue

68:    function __Auth_init(uint48 shortFreeze_, uint48 longFreeze_) internal onlyInitializing {	// @audit-issue

198:    function setShortFreeze(uint48 shortFreeze_) public onlyRole(OWNER) {	// @audit-issue

205:    function setLongFreeze(uint48 longFreeze_) public onlyRole(OWNER) {	// @audit-issue

214:    function freezeUntil(uint48 newUnfreezeAt) private {	// @audit-issue
```
[38](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L38-L38), [39](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L39-L39), [40](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L40-L40), [68](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L68-L68), [198](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L198-L198), [205](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L205-L205), [214](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L214-L214), 


```solidity
Path: ./contracts/libraries/Throttle.sol

74:        uint48 delta = uint48(block.timestamp) - throttle.lastTimestamp; // {seconds}	// @audit-issue
```
[74](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L74-L74), 


```solidity
Path: ./contracts/libraries/Fixed.sol

311:    uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;	// @audit-issue

183:    function toUint(uint192 x) internal pure returns (uint136) {	// @audit-issue

190:    function toUint(uint192 x, RoundingMode rounding) internal pure returns (uint136) {	// @audit-issue

198:    function shiftl(uint192 x, int8 decimals) internal pure returns (uint192) {	// @audit-issue

207:        uint192 x,	// @audit-issue

208:        int8 decimals,	// @audit-issue

210:    ) internal pure returns (uint192) {	// @audit-issue

223:    function plus(uint192 x, uint192 y) internal pure returns (uint192) {	// @audit-issue

230:    function plusu(uint192 x, uint256 y) internal pure returns (uint192) {	// @audit-issue

237:    function minus(uint192 x, uint192 y) internal pure returns (uint192) {	// @audit-issue

244:    function minusu(uint192 x, uint256 y) internal pure returns (uint192) {	// @audit-issue

252:    function mul(uint192 x, uint192 y) internal pure returns (uint192) {	// @audit-issue

260:        uint192 x,	// @audit-issue

261:        uint192 y,	// @audit-issue

263:    ) internal pure returns (uint192) {	// @audit-issue

270:    function mulu(uint192 x, uint256 y) internal pure returns (uint192) {	// @audit-issue

277:    function div(uint192 x, uint192 y) internal pure returns (uint192) {	// @audit-issue

285:        uint192 x,	// @audit-issue

286:        uint192 y,	// @audit-issue

288:    ) internal pure returns (uint192) {	// @audit-issue

296:    function divu(uint192 x, uint256 y) internal pure returns (uint192) {	// @audit-issue

304:        uint192 x,	// @audit-issue

307:    ) internal pure returns (uint192) {	// @audit-issue

317:    function powu(uint192 x_, uint48 y) internal pure returns (uint192) {	// @audit-issue

332:    function sqrt(uint192 x) internal pure returns (uint192) {	// @audit-issue

337:    function lt(uint192 x, uint192 y) internal pure returns (bool) {	// @audit-issue

341:    function lte(uint192 x, uint192 y) internal pure returns (bool) {	// @audit-issue

345:    function gt(uint192 x, uint192 y) internal pure returns (bool) {	// @audit-issue

349:    function gte(uint192 x, uint192 y) internal pure returns (bool) {	// @audit-issue

353:    function eq(uint192 x, uint192 y) internal pure returns (bool) {	// @audit-issue

357:    function neq(uint192 x, uint192 y) internal pure returns (bool) {	// @audit-issue

365:        uint192 x,	// @audit-issue

366:        uint192 y,	// @audit-issue

367:        uint192 epsilon	// @audit-issue

369:        uint192 diff = x <= y ? y - x : x - y;	// @audit-issue

380:    function shiftl_toUint(uint192 x, int8 decimals) internal pure returns (uint256) {	// @audit-issue

388:        uint192 x,	// @audit-issue

389:        int8 decimals,	// @audit-issue

406:    function mulu_toUint(uint192 x, uint256 y) internal pure returns (uint256) {	// @audit-issue

414:        uint192 x,	// @audit-issue

424:    function mul_toUint(uint192 x, uint192 y) internal pure returns (uint256) {	// @audit-issue

432:        uint192 x,	// @audit-issue

433:        uint192 y,	// @audit-issue

444:        uint192 x,	// @audit-issue

447:    ) internal pure returns (uint192) {	// @audit-issue

456:        uint192 x,	// @audit-issue

460:    ) internal pure returns (uint192) {	// @audit-issue

469:        uint192 x,	// @audit-issue

470:        uint192 y,	// @audit-issue

471:        uint192 z	// @audit-issue

472:    ) internal pure returns (uint192) {	// @audit-issue

481:        uint192 x,	// @audit-issue

482:        uint192 y,	// @audit-issue

483:        uint192 z,	// @audit-issue

485:    ) internal pure returns (uint192) {	// @audit-issue

495:        uint192 a,	// @audit-issue

496:        uint192 b,	// @audit-issue

498:    ) internal pure returns (uint192) {	// @audit-issue

545:        uint192 a,	// @audit-issue

546:        uint192 b,	// @audit-issue

548:    ) internal pure returns (uint192) {	// @audit-issue

562:        uint192 a,	// @audit-issue

563:        uint192 b,	// @audit-issue

564:        uint192 c,	// @audit-issue

566:    ) internal pure returns (uint192 result) {	// @audit-issue
```
[311](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L311-L311), [183](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L183-L183), [190](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L190-L190), [198](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L198-L198), [207](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L207-L207), [208](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L208-L208), [210](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L210-L210), [223](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L223-L223), [230](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L230-L230), [237](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L237-L237), [244](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L244-L244), [252](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L252-L252), [260](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L260-L260), [261](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L261-L261), [263](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L263-L263), [270](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L270-L270), [277](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L277-L277), [285](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L285-L285), [286](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L286-L286), [288](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L288-L288), [296](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L296-L296), [304](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L304-L304), [307](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L307-L307), [317](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L317-L317), [332](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L332-L332), [337](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L337-L337), [341](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L341-L341), [345](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L345-L345), [349](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L349-L349), [353](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L353-L353), [357](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L357-L357), [365](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L365-L365), [366](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L366-L366), [367](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L367-L367), [369](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L369-L369), [380](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L380-L380), [388](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L388-L388), [389](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L389-L389), [406](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L406-L406), [414](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L414-L414), [424](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L424-L424), [432](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L432-L432), [433](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L433-L433), [444](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L444-L444), [447](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L447-L447), [456](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L456-L456), [460](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L460-L460), [469](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L469-L469), [470](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L470-L470), [471](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L471-L471), [472](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L472-L472), [481](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L481-L481), [482](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L482-L482), [483](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L483-L483), [485](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L485-L485), [495](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L495-L495), [496](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L496-L496), [498](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L498-L498), [545](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L545-L545), [546](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L546-L546), [548](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L548-L548), [562](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L562-L562), [563](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L563-L563), [564](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L564-L564), [566](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L566-L566), 


```solidity
Path: ./contracts/libraries/Permit.sol

13:        uint8 v,	// @audit-issue
```
[13](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Permit.sol#L13-L13), 


```solidity
Path: ./contracts/p1/Main.sol

36:        uint48 shortFreeze_,	// @audit-issue

37:        uint48 longFreeze_	// @audit-issue
```
[36](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L36-L36), [37](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L37-L37), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

30:        uint192 maxTradeSlippage_,	// @audit-issue

31:        uint192 minTradeVolume_	// @audit-issue

149:        (uint192 buyLow, uint192 buyHigh) = assetToBuy.price(); // {UoA/tok}	// @audit-issue

161:            (uint192 sellLow, uint192 sellHigh) = assetToSell.price(); // {UoA/tok}	// @audit-issue
```
[30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L30-L30), [31](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L31-L31), [149](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L149-L149), [161](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L161-L161), 


```solidity
Path: ./contracts/p1/Distributor.sol

35:    uint8 public constant MAX_DESTINATIONS_ALLOWED = MAX_DESTINATIONS; // 100	// @audit-issue

265:    function _ensureSufficientTotal(uint24 rTokenTotal, uint24 rsrTotal) internal pure {	// @audit-issue
```
[35](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L35-L35), [265](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L265-L265), 


```solidity
Path: ./contracts/p1/Furnace.sol

15:    uint192 public constant MAX_RATIO = 1e14; // {1} 0.01%	// @audit-issue

20:    uint192 public ratio; // {1} What fraction of balance to melt each period	// @audit-issue

23:    uint48 public lastPayout; // {seconds} The last time we did a payout	// @audit-issue

33:    function init(IMain main_, uint192 ratio_) external initializer {	// @audit-issue

69:        uint48 numPeriods = uint48((block.timestamp) - lastPayout);	// @audit-issue

72:        uint192 payoutRatio = FIX_ONE.minus(FIX_ONE.minus(ratio).powu(numPeriods));	// @audit-issue

83:    function setRatio(uint192 ratio_) public governance {	// @audit-issue
```
[15](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L15-L15), [20](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L20-L20), [23](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L23-L23), [33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L33-L33), [69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L69-L69), [72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L72-L72), [83](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L83-L83), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

30:    uint48 public lastRefresh; // {s}	// @audit-issue
```
[30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L30-L30), 


```solidity
Path: ./contracts/p1/BackingManager.sol

33:    uint48 public constant MAX_TRADING_DELAY = 60 * 60 * 24 * 365; // {s} 1 year	// @audit-issue

34:    uint192 public constant MAX_BACKING_BUFFER = FIX_ONE; // {1} 100%	// @audit-issue

36:    uint48 public tradingDelay; // {s} how long to wait until resuming trading after switching	// @audit-issue

37:    uint192 public backingBuffer; // {1} how much extra backing collateral to keep	// @audit-issue

41:    mapping(TradeKind => uint48) private tradeEnd; // {s} last endTime() of an auction per kind	// @audit-issue

44:    mapping(IERC20 => uint192) private tokensOut; // {tok} token balances out in ITrades	// @audit-issue

51:        uint48 tradingDelay_,	// @audit-issue

52:        uint192 backingBuffer_,	// @audit-issue

53:        uint192 maxTradeSlippage_,	// @audit-issue

54:        uint192 minTradeVolume_	// @audit-issue

221:        uint192 baskets = (basketsHeld.bottom.div(FIX_ONE + backingBuffer));	// @audit-issue

226:        uint192 needed = rToken.basketsNeeded().mul(FIX_ONE + backingBuffer); // {BU}	// @audit-issue

241:            uint192 req = needed.mul(basketHandler.quantity(erc20s[i]), CEIL);	// @audit-issue

242:            uint192 bal = asset.bal(address(this));	// @audit-issue

306:    function compromiseBasketsNeeded(uint192 basketsHeldBottom) private {	// @audit-issue

315:    function setTradingDelay(uint48 val) public governance {	// @audit-issue

322:    function setBackingBuffer(uint192 val) public governance {	// @audit-issue
```
[33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L33-L33), [34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L34-L34), [36](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L36-L36), [37](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L37-L37), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L41-L41), [44](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L44-L44), [51](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L51-L51), [52](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L52-L52), [53](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L53-L53), [54](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L54-L54), [221](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L221-L221), [226](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L226-L226), [241](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L241-L241), [242](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L242-L242), [306](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L306-L306), [315](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L315-L315), [322](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L322-L322), 


```solidity
Path: ./contracts/p1/StRSR.sol

40:    uint48 private constant MIN_UNSTAKING_DELAY = 60 * 2; // {s} 2 minutes	// @audit-issue

41:    uint48 private constant MAX_UNSTAKING_DELAY = 60 * 60 * 24 * 365; // {s} 1 year	// @audit-issue

42:    uint192 private constant MAX_REWARD_RATIO = 1e14; // {1} 0.01%	// @audit-issue

48:    uint8 public constant decimals = 18;	// @audit-issue

66:    uint192 private stakeRate; // The exchange rate between stakes and RSR. D18{qStRSR/qRSR}	// @audit-issue

68:    uint192 private constant MAX_STAKE_RATE = 1e9 * FIX_ONE; // 1e9 D18{qStRSR/qRSR}	// @audit-issue

88:    uint192 public draftRate; // The exchange rate between drafts and RSR. D18{qDrafts/qRSR}	// @audit-issue

90:    uint192 private constant MAX_DRAFT_RATE = 1e9 * FIX_ONE; // 1e9 D18{qDrafts/qRSR}	// @audit-issue

138:    uint48 public unstakingDelay; // {s} The minimum length of time spent in the draft queue	// @audit-issue

139:    uint192 public rewardRatio; // {1} The fraction of the revenue balance to handout per period	// @audit-issue

150:    uint48 private payoutLastPaid;	// @audit-issue

157:    uint192 private constant MAX_WITHDRAWAL_LEAK = 3e17; // {1} 30%	// @audit-issue

159:    uint192 private leaked; // {1} stake fraction that has withdrawn without a refresh	// @audit-issue

160:    uint48 private lastWithdrawRefresh; // {s} timestamp of last refresh() during withdraw()	// @audit-issue

161:    uint192 public withdrawalLeak; // {1} gov param -- % RSR that can be withdrawn without refresh	// @audit-issue

164:    uint192 private constant MAX_SAFE_STAKE_RATE = 1e6 * FIX_ONE; // 1e6   D18{qStRSR/qRSR}	// @audit-issue

165:    uint192 private constant MIN_SAFE_STAKE_RATE = uint192(1e12); // 1e-6  D18{qStRSR/qRSR}	// @audit-issue

179:        uint48 unstakingDelay_,	// @audit-issue

180:        uint192 rewardRatio_,	// @audit-issue

181:        uint192 withdrawalLeak_	// @audit-issue

279:        (uint256 index, uint64 availableAt) = pushDraft(account, rsrAmount);	// @audit-issue

317:        uint192 oldDrafts = firstId != 0 ? queue[firstId - 1].drafts : 0;	// @audit-issue

318:        uint192 draftAmount = queue[endId - 1].drafts - oldDrafts;	// @audit-issue

364:        uint192 oldDrafts = firstId != 0 ? queue[firstId - 1].drafts : 0;	// @audit-issue

365:        uint192 draftAmount = queue[endId - 1].drafts - oldDrafts;	// @audit-issue

437:        uint192 initRate = exchangeRate();	// @audit-issue

502:    function exchangeRate() public view returns (uint192) {	// @audit-issue

595:        uint48 numPeriods = uint48(block.timestamp) - payoutLastPaid;	// @audit-issue

597:        uint192 initRate = exchangeRate();	// @audit-issue

606:            uint192 payoutRatio = FIX_ONE - FixLib.powu(FIX_ONE - rewardRatio, numPeriods);	// @audit-issue

642:        returns (uint256 index, uint64 availableAt)	// @audit-issue

656:        uint192 oldDrafts = index != 0 ? queue[index - 1].drafts : 0;	// @audit-issue

657:        uint64 lastAvailableAt = index != 0 ? queue[index - 1].availableAt : 0;	// @audit-issue

701:        uint48 lastRefresh = assetRegistry.lastRefresh(); // {s}	// @audit-issue

705:        uint192 withdrawal = _safeWrap((rsrWithdrawal * FIX_ONE + totalRSR - 1) / totalRSR); // {1}	// @audit-issue

927:        uint8 v,	// @audit-issue

970:    function setUnstakingDelay(uint48 val) public {	// @audit-issue

978:    function setRewardRatio(uint192 val) public {	// @audit-issue

987:    function setWithdrawalLeak(uint192 val) public {	// @audit-issue
```
[40](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L40-L40), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L41-L41), [42](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L42-L42), [48](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L48-L48), [66](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L66-L66), [68](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L68-L68), [88](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L88-L88), [90](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L90-L90), [138](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L138-L138), [139](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L139-L139), [150](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L150-L150), [157](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L157-L157), [159](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L159-L159), [160](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L160-L160), [161](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L161-L161), [164](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L164-L164), [165](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L165-L165), [179](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L179-L179), [180](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L180-L180), [181](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L181-L181), [279](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L279-L279), [317](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L317-L317), [318](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L318-L318), [364](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L364-L364), [365](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L365-L365), [437](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L437-L437), [502](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L502-L502), [595](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L595-L595), [597](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L597-L597), [606](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L606-L606), [642](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L642-L642), [656](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L656-L656), [657](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L657-L657), [701](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L701-L701), [705](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L705-L705), [927](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L927-L927), [970](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L970-L970), [978](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L978-L978), [987](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L987-L987), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

31:    uint192 public constant MAX_TARGET_AMT = 1e3 * FIX_ONE; // {target/BU} max basket weight	// @audit-issue

32:    uint48 public constant MIN_WARMUP_PERIOD = 60; // {s} 1 minute	// @audit-issue

33:    uint48 public constant MAX_WARMUP_PERIOD = 60 * 60 * 24 * 365; // {s} 1 year	// @audit-issue

51:    uint48 public nonce; // {basketNonce} A unique identifier for this basket instance	// @audit-issue

52:    uint48 public timestamp; // The timestamp when this basket was last set	// @audit-issue

69:    uint48 public warmupPeriod; // {s} how long to wait until issuance/trading after regaining SOUND	// @audit-issue

73:    uint48 private lastStatusTimestamp;	// @audit-issue

91:    uint48 public lastCollateralized; // {basketNonce} most recent full collateralization	// @audit-issue

80:    mapping(uint48 => Basket) private basketHistory;	// @audit-issue

110:        uint48 warmupPeriod_,	// @audit-issue

343:    function quantity(IERC20 erc20) public view returns (uint192) {	// @audit-issue

359:    function quantityUnsafe(IERC20 erc20, IAsset asset) public view returns (uint192) {	// @audit-issue

366:    function issuancePremium(ICollateral coll) public view returns (uint192) {	// @audit-issue

373:            uint192 targetPerRef = coll.targetPerRef(); // {target/ref}	// @audit-issue

395:    ) internal view returns (uint192) {	// @audit-issue

396:        uint192 refPerTok = coll.refPerTok();	// @audit-issue

409:    function price() external view returns (uint192 low, uint192 high) {	// @audit-issue

419:    function price(bool applyIssuancePremium) public view returns (uint192 low, uint192 high) {	// @audit-issue

426:                uint192 qty = _quantity(basket.erc20s[i], coll, CEIL);	// @audit-issue

429:                (uint192 lowP, uint192 highP) = coll.price();	// @audit-issue

440:                        uint192 premium = issuancePremium(coll); // {1} always CEIL	// @audit-issue

467:    function quote(uint192 amount, RoundingMode rounding)	// @audit-issue

483:        uint192 amount,	// @audit-issue

496:            uint192 q = _quantity(basket.erc20s[i], coll, rounding).safeMul(amount, rounding);	// @audit-issue

500:                uint192 premium = issuancePremium(coll); // {1} always CEIL by definition	// @audit-issue

524:        uint192 amount	// @audit-issue

562:                uint192 amt = portions[i].mul(b.refAmts[b.erc20s[j]], FLOOR);	// @audit-issue

535:        for (uint48 i = 0; i < basketNonces.length; ++i) {	// @audit-issue

620:            uint192 q = _quantity(basket.erc20s[i], coll, CEIL);	// @audit-issue

624:            uint192 inBUs = coll.bal(account).div(q);	// @audit-issue

633:    function setWarmupPeriod(uint48 val) public {	// @audit-issue

704:    function getHistoricalBasket(uint48 basketNonce)	// @audit-issue
```
[31](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L31-L31), [32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L32-L32), [33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L33-L33), [51](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L51-L51), [52](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L52-L52), [69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L69-L69), [73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L73-L73), [91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L91-L91), [80](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L80-L80), [110](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L110-L110), [343](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L343-L343), [359](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L359-L359), [366](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L366-L366), [373](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L373-L373), [395](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L395-L395), [396](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L396-L396), [409](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L409-L409), [419](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L419-L419), [426](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L426-L426), [429](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L429-L429), [440](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L440-L440), [467](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L467-L467), [483](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L483-L483), [496](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L496-L496), [500](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L500-L500), [524](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L524-L524), [562](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L562-L562), [535](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L535-L535), [620](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L620-L620), [624](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L624-L624), [633](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L633-L633), [704](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L704-L704), 


```solidity
Path: ./contracts/p1/Deployer.sol

269:    function deployRTokenAsset(IRToken rToken, uint192 maxTradeVolume)	// @audit-issue
```
[269](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L269-L269), 


```solidity
Path: ./contracts/p1/RToken.sol

24:    uint192 public constant MAX_THROTTLE_PCT_AMT = 1e18; // {qRTok}	// @audit-issue

25:    uint192 public constant MIN_EXCHANGE_RATE = 1e9; // D18{BU/rTok}	// @audit-issue

26:    uint192 public constant MAX_EXCHANGE_RATE = 1e27; // D18{BU/rTok}	// @audit-issue

55:    uint192 public basketsNeeded; // D18{BU}	// @audit-issue

133:        uint192 amtBaskets = supply != 0	// @audit-issue

203:        uint192 baskets = _scaleDown(caller, amount);	// @audit-issue

281:        uint192 baskets = _scaleDown(_msgSender(), amount);	// @audit-issue

356:    function mint(uint192 baskets) external {	// @audit-issue

397:    function setBasketsNeeded(uint192 basketsNeeded_) external notTradingPausedOrFrozen {	// @audit-issue

485:        uint192 amtBaskets,	// @audit-issue

508:    function _scaleDown(address account, uint256 amtRToken) private returns (uint192 amtBaskets) {	// @audit-issue
```
[24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L24-L24), [25](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L25-L25), [26](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L26-L26), [55](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L55-L55), [133](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L133-L133), [203](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L203-L203), [281](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L281-L281), [356](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L356-L356), [397](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L397-L397), [485](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L485-L485), [508](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L508-L508), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

58:    function clock() public view returns (uint48) {	// @audit-issue

74:    function checkpoints(address account, uint48 pos) public view returns (Checkpoint memory) {	// @audit-issue

78:    function numCheckpoints(address account) public view returns (uint48) {	// @audit-issue

170:        uint8 v,	// @audit-issue
```
[58](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L58-L58), [74](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L74-L74), [78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L78-L78), [170](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L170-L170), 


```solidity
Path: ./contracts/p1/Broker.sol

25:    uint48 public constant MAX_AUCTION_LENGTH = 60 * 60 * 24 * 7; // {s} max valid duration, 1 week	// @audit-issue

28:    uint48 public constant MIN_AUCTION_LENGTH = 60; // {s} 60 seconds auction min duration	// @audit-issue

43:    uint48 public batchAuctionLength;	// @audit-issue

60:    uint48 public dutchAuctionLength;	// @audit-issue

77:        uint48 batchAuctionLength_,	// @audit-issue

79:        uint48 dutchAuctionLength_	// @audit-issue

197:    function setBatchAuctionLength(uint48 newAuctionLength) public governance {	// @audit-issue

219:    function setDutchAuctionLength(uint48 newAuctionLength) public governance {	// @audit-issue
```
[25](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L25-L25), [28](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L28-L28), [43](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L43-L43), [60](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L60-L60), [77](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L77-L77), [79](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L79-L79), [197](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L197-L197), [219](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L219-L219), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

22:    uint192 public constant MAX_TRADE_VOLUME = 1e29; // {UoA}	// @audit-issue

23:    uint192 public constant MAX_TRADE_SLIPPAGE = 1e18; // {%}	// @audit-issue

30:    uint48 public tradesOpen;	// @audit-issue

33:    uint192 public maxTradeSlippage; // {%}	// @audit-issue

34:    uint192 public minTradeVolume; // {UoA}	// @audit-issue

50:        uint192 maxTradeSlippage_,	// @audit-issue

51:        uint192 minTradeVolume_	// @audit-issue

146:    function setMaxTradeSlippage(uint192 val) public governance {	// @audit-issue

153:    function setMinTradeVolume(uint192 val) public governance {	// @audit-issue
```
[22](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L22-L22), [23](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L23-L23), [30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L30-L30), [33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L33-L33), [34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L34-L34), [50](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L50-L50), [51](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L51-L51), [146](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L146-L146), [153](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L153-L153), 


```solidity
Path: ./contracts/p1/mixins/TradeLib.sol

44:        uint192 minTradeVolume,	// @audit-issue

45:        uint192 maxTradeSlippage	// @audit-issue

64:        uint192 s = trade.sellAmount;	// @audit-issue

67:            uint192 maxSell = maxTradeSize(trade.sell, trade.buy, trade.prices.sellHigh);	// @audit-issue

76:        uint192 b = s.mul(FIX_ONE.minus(maxTradeSlippage)).safeMulDiv(	// @audit-issue

120:        uint192 minTradeVolume,	// @audit-issue

121:        uint192 maxTradeSlippage	// @audit-issue

137:        uint192 exactSellAmount = trade.buyAmount.mulDiv(	// @audit-issue

145:        uint192 slippedSellAmount = exactSellAmount.div(FIX_ONE.minus(maxTradeSlippage), CEIL);	// @audit-issue

158:        uint192 amt,	// @audit-issue

159:        uint192 price,	// @audit-issue

160:        uint192 minTradeVolume	// @audit-issue

174:    function minTradeSize(uint192 minTradeVolume, uint192 price) private pure returns (uint192) {	// @audit-issue

176:        uint192 size = price == 0 ? FIX_MAX : minTradeVolume.div(price, CEIL);	// @audit-issue

185:        uint192 price	// @audit-issue

186:    ) private view returns (uint192) {	// @audit-issue

188:        uint192 size = fixMin(sell.maxTradeVolume(), buy.maxTradeVolume()).safeDiv(price, FLOOR);	// @audit-issue

11:    uint192 sellAmount; // {sellTok}	// @audit-issue

12:    uint192 buyAmount; // {buyTok}	// @audit-issue
```
[44](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L44-L44), [45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L45-L45), [64](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L64-L64), [67](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L67-L67), [76](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L76-L76), [120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L120-L120), [121](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L121-L121), [137](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L137-L137), [145](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L145-L145), [158](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L158-L158), [159](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L159-L159), [160](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L160-L160), [174](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L174-L174), [176](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L176-L176), [185](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L185-L185), [186](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L186-L186), [188](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L188-L188), [11](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L11-L11), [12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L12-L12), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

116:        (uint192 buPriceLow, uint192 buPriceHigh) = ctx.bh.price(false); // {UoA/BU}	// @audit-issue

119:        uint192 basketsNeeded = ctx.rToken.basketsNeeded(); // {BU}	// @audit-issue

136:        uint192 uoaBottom; // {UoA} pessimistic UoA estimate of balances above basketsHeld.bottom	// @audit-issue

143:            (uint192 low, uint192 high) = reg.assets[i].price(); // {UoA/tok}	// @audit-issue

161:                uint192 anchor = ctx.quantities[i].mul(ctx.basketsHeld.top, CEIL);	// @audit-issue

185:                uint192 anchor = ctx.quantities[i].mul(ctx.basketsHeld.bottom, FLOOR);	// @audit-issue

189:                uint192 val = low.mul(ctx.bals[i] - anchor, FLOOR);	// @audit-issue

297:            uint192 needed = range.top.mul(ctx.quantities[i], CEIL); // {tok}	// @audit-issue

300:                (uint192 low, uint192 high) = reg.assets[i].price(); // {UoA/sellTok}	// @audit-issue

305:                uint192 delta = ctx.bals[i].minus(needed).mul(low, FLOOR);	// @audit-issue

337:                    uint192 amtShort = needed.minus(ctx.bals[i]); // {buyTok}	// @audit-issue

338:                    (uint192 low, uint192 high) = reg.assets[i].price(); // {UoA/buyTok}	// @audit-issue

341:                    uint192 delta = amtShort.mul(high, CEIL);	// @audit-issue

358:            (uint192 low, uint192 high) = reg.assets[rsrIndex].price(); // {UoA/RSR}	// @audit-issue

384:        uint192 surplusAmt	// @audit-issue
```
[116](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L116-L116), [119](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L119-L119), [136](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L136-L136), [143](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L143-L143), [161](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L161-L161), [185](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L185-L185), [189](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L189-L189), [297](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L297-L297), [300](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L300-L300), [305](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L305-L305), [337](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L337-L337), [338](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L338-L338), [341](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L341-L341), [358](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L358-L358), [384](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L384-L384), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

93:        uint192 weight	// @audit-issue

210:            uint192 targetWeight = config.targetAmts[_erc20];	// @audit-issue

265:                    uint192 backupWeight = totalWeights[i].minus(goodWeights[i]).div(	// @audit-issue

355:        uint192 price	// @audit-issue

362:        uint192 newPrice; // {UoA/BU}	// @audit-issue

367:            (uint192 low, uint192 high) = coll.price(); // {UoA/tok}	// @audit-issue
```
[93](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L93-L93), [210](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L210-L210), [265](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L265-L265), [355](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L355-L355), [362](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L362-L362), [367](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L367-L367), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

29:    uint96 public constant MAX_ORDERS = 5000; // bounded to avoid going beyond block gas limit	// @audit-issue

32:    uint192 public constant DEFAULT_MIN_BID = FIX_ONE / 100; // {tok}	// @audit-issue

50:    uint192 public sellAmount; // {sellTok}, quantity of whole tokens being sold, != initBal	// @audit-issue

51:    uint48 public endTime; // timestamp after which this trade's auction can be settled	// @audit-issue

52:    uint192 public worstCasePrice; // D27{qBuyTok/qSellTok}, the worst price we expect to get	// @audit-issue

88:        uint48 batchAuctionLength,	// @audit-issue

114:        uint96 _sellAmount = uint96(	// @audit-issue

123:        uint96 minBuyAmount = uint96(Math.max(1, req.minBuyAmount)); // Safe downcast; require'd	// @audit-issue

217:            uint192 clearingPrice = shiftl_toFix(adjustedBuyAmt, 9).divu(adjustedSoldAmt, FLOOR);	// @audit-issue
```
[29](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L29-L29), [32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L32-L32), [50](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L50-L50), [51](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L51-L51), [52](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L52-L52), [88](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L88-L88), [114](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L114-L114), [123](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L123-L123), [217](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L217-L217), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

107:    uint192 public sellAmount; // {sellTok}	// @audit-issue

110:    uint48 public startTime; // {s} when the dutch auction begins (one block after init()) lossy!	// @audit-issue

111:    uint48 public endTime; // {s} when the dutch auction ends	// @audit-issue

113:    uint192 public bestPrice; // {buyTok/sellTok} The best plausible price based on oracle data	// @audit-issue

114:    uint192 public worstPrice; // {buyTok/sellTok} The worst plausible price based on oracle data	// @audit-issue

139:    function bidAmount(uint48 timestamp) external view returns (uint256) {	// @audit-issue

161:        uint48 auctionLength,	// @audit-issue

180:        uint48 _startTime = uint48(block.timestamp) + 1; // cannot fulfill in current block	// @audit-issue

185:        uint192 _worstPrice = prices.sellLow.mulDiv(	// @audit-issue

190:        uint192 _bestPrice = prices.sellHigh.div(prices.buyLow, CEIL); // no additional slippage	// @audit-issue

204:        uint192 price = _price(uint48(block.timestamp)); // enforces auction ongoing	// @audit-issue

240:        uint192 price = _price(uint48(block.timestamp)); // enforces auction ongoing	// @audit-issue

314:    function _price(uint48 timestamp) private view returns (uint192) {	// @audit-issue

315:        uint48 _startTime = startTime; // {s} gas savings	// @audit-issue

316:        uint48 _endTime = endTime; // {s} gas savings	// @audit-issue

326:        uint192 progression = divuu(timestamp - _startTime, _endTime - _startTime); // {1}	// @audit-issue

330:            uint192 exp = MAX_EXP.mulDiv(TWENTY_PERCENT - progression, TWENTY_PERCENT, ROUND);	// @audit-issue

341:            uint192 _bestPrice = bestPrice; // gas savings	// @audit-issue

343:            uint192 highPrice = _bestPrice.mul(ONE_POINT_FIVE, CEIL);	// @audit-issue

351:            uint192 _bestPrice = bestPrice; // gas savings	// @audit-issue

365:    function _bidAmount(uint192 price) public view returns (uint256) {	// @audit-issue
```
[107](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L107-L107), [110](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L110-L110), [111](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L111-L111), [113](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L113-L113), [114](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L114-L114), [139](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L139-L139), [161](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L161-L161), [180](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L180-L180), [185](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L185-L185), [190](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L190-L190), [204](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L204-L204), [240](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L240-L240), [314](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L314-L314), [315](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L315-L315), [316](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L316-L316), [326](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L326-L326), [330](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L330-L330), [341](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L341-L341), [343](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L343-L343), [351](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L351-L351), [365](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L365-L365), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

196:    function clock() public view override(GovernorVotes, IGovernor) returns (uint48) {	// @audit-issue
```
[196](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L196-L196), 


#### Recommendation

Minimize gas overhead by using 'uint256' or 'int256' instead of smaller integer types in Solidity contracts. The EVM operates more efficiently with 32-byte sizes. Downcast to smaller types only when necessary, as operations with smaller types like 'uint8' incur extra gas due to additional EVM operations for size adjustment.

### Refactor modifiers to call a local function
Modifiers code is copied in all instances where it's used, increasing bytecode size. If deployment gas costs are a concern for this contract, refactoring modifiers into functions can reduce bytecode size significantly at the cost of one JUMP.

```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

28:    modifier onlyOwner() {	// @audit-issue
```
[28](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L28-L28), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

41:    modifier notTradingPausedOrFrozen() {	// @audit-issue

46:    modifier notIssuancePausedOrFrozen() {	// @audit-issue

51:    modifier notFrozen() {	// @audit-issue

56:    modifier governance() {	// @audit-issue

61:    modifier onlyMain() {	// @audit-issue
```
[41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L41-L41), [46](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L46-L46), [51](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L51-L51), [56](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L56-L56), [61](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L61-L61), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

58:    modifier stateTransition(TradeStatus begin, TradeStatus end) {	// @audit-issue
```
[58](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L58-L58), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

121:    modifier stateTransition(TradeStatus begin, TradeStatus end) {	// @audit-issue
```
[121](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L121-L121), 


#### Recommendation

Evaluate your contract's use of modifiers, particularly those applied across multiple functions, to identify candidates for refactoring into functions. Convert these modifiers into external or public functions that perform the same checks or actions. Then, replace the modifier usage in function declarations with calls to these functions at the start of your functions

### Avoid Unnecessary Public Variables
Public state variables in Solidity automatically generate getter functions, increasing contract size and potentially leading to higher deployment and interaction costs. To optimize gas usage and contract efficiency, minimize the use of public variables unless external access is necessary. Instead, use internal or private visibility combined with explicit getter functions when required. This practice not only reduces contract size but also provides better control over data access and manipulation, enhancing security and readability. Prioritize lean, efficient contracts to ensure cost-effectiveness and better performance on the blockchain.

```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

10:    RoleRegistry public roleRegistry;	// @audit-issue
```
[10](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L10-L10), 


```solidity
Path: ./contracts/registry/VersionRegistry.sol

13:    mapping(bytes32 => IDeployer) public deployments;	// @audit-issue

14:    mapping(bytes32 => bool) public isDeprecated;	// @audit-issue

16:    RoleRegistry public roleRegistry;	// @audit-issue
```
[13](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L13-L13), [14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L14-L14), [16](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L16-L16), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

13:    VersionRegistry public versionRegistry;	// @audit-issue

14:    RoleRegistry public roleRegistry;	// @audit-issue

17:    mapping(address => bool) public isDeprecated;	// @audit-issue
```
[13](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L13-L13), [14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L14-L14), [17](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L17-L17), 


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

36:    mapping(address => uint256) public longFreezes;	// @audit-issue

38:    uint48 public unfreezeAt; // {s} uint48.max to pause indefinitely	// @audit-issue

39:    uint48 public shortFreeze; // {s} length of an initial freeze	// @audit-issue

40:    uint48 public longFreeze; // {s} length of a freeze extension	// @audit-issue

45:    bool public tradingPaused;	// @audit-issue

46:    bool public issuancePaused;	// @audit-issue
```
[29](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L29-L29), [30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L30-L30), [31](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L31-L31), [32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L32-L32), [36](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L36-L36), [38](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L38-L38), [39](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L39-L39), [40](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L40-L40), [45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L45-L45), [46](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L46-L46), 


```solidity
Path: ./contracts/mixins/ComponentRegistry.sol

29:    }	// @audit-issue

30:	// @audit-issue

31:    // === Components ===	// @audit-issue

32:	// @audit-issue

36:        require(address(val) != address(0), "invalid RToken address");	// @audit-issue

38:        rToken = val;	// @audit-issue

39:    }	// @audit-issue

40:	// @audit-issue

45:        emit StRSRSet(stRSR, val);	// @audit-issue

46:        stRSR = val;	// @audit-issue

33:    IRToken public rToken;	// @audit-issue

41:    IStRSR public stRSR;	// @audit-issue

49:    IAssetRegistry public assetRegistry;	// @audit-issue

57:    IBasketHandler public basketHandler;	// @audit-issue

65:    IBackingManager public backingManager;	// @audit-issue

73:    IDistributor public distributor;	// @audit-issue

81:    IRevenueTrader public rsrTrader;	// @audit-issue

89:    IRevenueTrader public rTokenTrader;	// @audit-issue

97:    IFurnace public furnace;	// @audit-issue

105:    IBroker public broker;	// @audit-issue
```
[29](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L29-L29), [30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L30-L30), [31](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L31-L31), [32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L32-L32), [36](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L36-L36), [38](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L38-L38), [39](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L39-L39), [40](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L40-L40), [45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L45-L45), [46](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L46-L46), [33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L33-L33), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L41-L41), [49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L49-L49), [57](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L57-L57), [65](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L65-L65), [73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L73-L73), [81](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L81-L81), [89](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L89-L89), [97](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L97-L97), [105](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L105-L105), 


```solidity
Path: ./contracts/p1/Main.sol

29:    // solhint-disable-next-line no-empty-blocks	// @audit-issue

30:    constructor() initializer {}	// @audit-issue

31:	// @audit-issue

32:    /// Initializer	// @audit-issue

36:        uint48 shortFreeze_,	// @audit-issue

38:    ) public virtual initializer {	// @audit-issue

39:        require(address(rsr_) != address(0), "invalid RSR address");	// @audit-issue

40:        __Auth_init(shortFreeze_, longFreeze_);	// @audit-issue

45:        emit MainInitialized();	// @audit-issue

46:    }	// @audit-issue

33:    function init(	// @audit-issue

41:        __ComponentRegistry_init(components);	// @audit-issue

49:    /// @custom:interaction CEI	// @audit-issue

57:    }	// @audit-issue

65:        versionRegistry = VersionRegistry(versionRegistry_);	// @audit-issue

73:	// @audit-issue

81:        require(address(daoFeeRegistry) == address(0), "already set");	// @audit-issue

89:        override(IAccessControlUpgradeable, AccessControlUpgradeable)	// @audit-issue

97:     *      this must happen in the Governance proposal.	// @audit-issue

105:        );	// @audit-issue

23:    IERC20 public rsr;	// @audit-issue

24:    VersionRegistry public versionRegistry;	// @audit-issue

25:    AssetPluginRegistry public assetPluginRegistry;	// @audit-issue

26:    DAOFeeRegistry public daoFeeRegistry;	// @audit-issue
```
[29](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L29-L29), [30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L30-L30), [31](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L31-L31), [32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L32-L32), [36](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L36-L36), [38](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L38-L38), [39](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L39-L39), [40](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L40-L40), [45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L45-L45), [46](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L46-L46), [33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L33-L33), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L41-L41), [49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L49-L49), [57](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L57-L57), [65](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L65-L65), [73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L73-L73), [81](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L81-L81), [89](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L89-L89), [97](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L97-L97), [105](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L105-L105), [23](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L23-L23), [24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L24-L24), [25](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L25-L25), [26](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L26-L26), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

21:    IDistributor private distributor;	// @audit-issue

22:    IBackingManager private backingManager;	// @audit-issue

23:    IFurnace private furnace;	// @audit-issue

29:        IERC20 tokenToBuy_,	// @audit-issue

30:        uint192 maxTradeSlippage_,	// @audit-issue

33:        require(address(tokenToBuy_) != address(0), "invalid token address");	// @audit-issue

34:        __Component_init(main_);	// @audit-issue

37:        cacheComponents();	// @audit-issue

19:    IERC20 public tokenToBuy;	// @audit-issue
```
[21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L21-L21), [22](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L22-L22), [23](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L23-L23), [29](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L29-L29), [30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L30-L30), [33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L33-L33), [34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L34-L34), [37](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L37-L37), [19](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L19-L19), 


```solidity
Path: ./contracts/p1/Distributor.sol

21:    // distribution is nonzero. (That is, distribution has at least one nonzero value)	// @audit-issue

18:    mapping(address => RevenueShare) public distribution;	// @audit-issue

32:    address public constant FURNACE = address(1);	// @audit-issue

33:    address public constant ST_RSR = address(2);	// @audit-issue

35:    uint8 public constant MAX_DESTINATIONS_ALLOWED = MAX_DESTINATIONS; // 100	// @audit-issue
```
[21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L21-L21), [18](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L18-L18), [32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L32-L32), [33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L33-L33), [35](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L35-L35), 


```solidity
Path: ./contracts/p1/Furnace.sol

21:	// @audit-issue

15:    uint192 public constant MAX_RATIO = 1e14; // {1} 0.01%	// @audit-issue

20:    uint192 public ratio; // {1} What fraction of balance to melt each period	// @audit-issue

23:    uint48 public lastPayout; // {seconds} The last time we did a payout	// @audit-issue

24:    uint256 public lastPayoutBal; // {qRTok} The balance of RToken at the last payout	// @audit-issue
```
[21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L21-L21), [15](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L15-L15), [20](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L20-L20), [23](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L23-L23), [24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L24-L24), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

21:	// @audit-issue

15:    uint256 public constant GAS_FOR_BH_QTY = 100_000; // enough to call bh.quantity	// @audit-issue

16:    uint256 public constant GAS_FOR_DISABLE_BASKET = 900_000; // enough to disable basket on n=128	// @audit-issue

30:    uint48 public lastRefresh; // {s}	// @audit-issue
```
[21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L21-L21), [15](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L15-L15), [16](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L16-L16), [30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L30-L30), 


```solidity
Path: ./contracts/p1/BackingManager.sol

21:    using FixLib for uint192;	// @audit-issue

22:    using SafeERC20 for IERC20;	// @audit-issue

23:	// @audit-issue

29:    IERC20 private rsr;	// @audit-issue

30:    IStRSR private stRSR;	// @audit-issue

33:    uint48 public constant MAX_TRADING_DELAY = 60 * 60 * 24 * 365; // {s} 1 year	// @audit-issue

34:    uint192 public constant MAX_BACKING_BUFFER = FIX_ONE; // {1} 100%	// @audit-issue

37:    uint192 public backingBuffer; // {1} how much extra backing collateral to keep	// @audit-issue

36:    uint48 public tradingDelay; // {s} how long to wait until resuming trading after switching	// @audit-issue
```
[21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L21-L21), [22](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L22-L22), [23](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L23-L23), [29](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L29-L29), [30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L30-L30), [33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L33-L33), [34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L34-L34), [37](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L37-L37), [36](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L36-L36), 


```solidity
Path: ./contracts/p1/StRSR.sol

21: * @notice StRSR is an ERC20 token contract that allows people to stake their RSR as	// @audit-issue

45:    string public name; // immutable	// @audit-issue

46:    string public symbol; // immutable	// @audit-issue

48:    uint8 public constant decimals = 18;	// @audit-issue

84:    mapping(uint256 => mapping(address => CumulativeDraft[])) public draftQueues; // {qDrafts}	// @audit-issue

85:    mapping(uint256 => mapping(address => uint256)) public firstRemainingDraft; // draft index	// @audit-issue

88:    uint192 public draftRate; // The exchange rate between drafts and RSR. D18{qDrafts/qRSR}	// @audit-issue

138:    uint48 public unstakingDelay; // {s} The minimum length of time spent in the draft queue	// @audit-issue

139:    uint192 public rewardRatio; // {1} The fraction of the revenue balance to handout per period	// @audit-issue

161:    uint192 public withdrawalLeak; // {1} gov param -- % RSR that can be withdrawn without refresh	// @audit-issue
```
[21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L21-L21), [45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L45-L45), [46](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L46-L46), [48](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L48-L48), [84](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L84-L84), [85](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L85-L85), [88](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L88-L88), [138](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L138-L138), [139](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L139-L139), [161](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L161-L161), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

21: */	// @audit-issue

31:    uint192 public constant MAX_TARGET_AMT = 1e3 * FIX_ONE; // {target/BU} max basket weight	// @audit-issue

32:    uint48 public constant MIN_WARMUP_PERIOD = 60; // {s} 1 minute	// @audit-issue

33:    uint48 public constant MAX_WARMUP_PERIOD = 60 * 60 * 24 * 365; // {s} 1 year	// @audit-issue

51:    uint48 public nonce; // {basketNonce} A unique identifier for this basket instance	// @audit-issue

52:    uint48 public timestamp; // The timestamp when this basket was last set	// @audit-issue

69:    uint48 public warmupPeriod; // {s} how long to wait until issuance/trading after regaining SOUND	// @audit-issue

89:    bool public reweightable; // immutable after init	// @audit-issue

91:    uint48 public lastCollateralized; // {basketNonce} most recent full collateralization	// @audit-issue

96:    bool public enableIssuancePremium;	// @audit-issue
```
[21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L21-L21), [31](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L31-L31), [32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L32-L32), [33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L33-L33), [51](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L51-L51), [52](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L52-L52), [69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L69-L69), [89](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L89-L89), [91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L91-L91), [96](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L96-L96), 


```solidity
Path: ./contracts/p1/Deployer.sol

31:    string public constant ENS = "reserveprotocol.eth";	// @audit-issue

33:    IERC20Metadata public immutable rsr;	// @audit-issue

34:    IGnosis public immutable gnosis;	// @audit-issue

35:    IAsset public immutable rsrAsset;	// @audit-issue
```
[31](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L31-L31), [33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L33-L33), [34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L34-L34), [35](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L35-L35), 


```solidity
Path: ./contracts/p1/RToken.sol

21:	// @audit-issue

22:    uint256 public constant MIN_THROTTLE_RATE_AMT = 1e18; // {qRTok}	// @audit-issue

23:    uint256 public constant MAX_THROTTLE_RATE_AMT = 1e48; // {qRTok}	// @audit-issue

24:    uint192 public constant MAX_THROTTLE_PCT_AMT = 1e18; // {qRTok}	// @audit-issue

25:    uint192 public constant MIN_EXCHANGE_RATE = 1e9; // D18{BU/rTok}	// @audit-issue

26:    uint192 public constant MAX_EXCHANGE_RATE = 1e27; // D18{BU/rTok}	// @audit-issue

44:    string public mandate;	// @audit-issue

55:    uint192 public basketsNeeded; // D18{BU}	// @audit-issue
```
[21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L21-L21), [22](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L22-L22), [23](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L23-L23), [24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L24-L24), [25](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L25-L25), [26](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L26-L26), [44](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L44-L44), [55](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L55-L55), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

21:    // In particular, if the value changed during timepoint N, there will be exactly one	// @audit-issue

45:    // When RSR is seized, stakeholders are divested not only of their economic position,	// @audit-issue

46:    // but also of their governance position.	// @audit-issue

48:    // ===	// @audit-issue

84:    }	// @audit-issue

85:	// @audit-issue

88:        return pos == 0 ? 0 : _checkpoints[era][account][pos - 1].val;	// @audit-issue

138:	// @audit-issue

139:        if (length > 5) {	// @audit-issue

161:	// @audit-issue
```
[21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L21-L21), [45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L45-L45), [46](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L46-L46), [48](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L48-L48), [84](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L84-L84), [85](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L85-L85), [88](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L88-L88), [138](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L138-L138), [139](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L139-L139), [161](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L161-L161), 


```solidity
Path: ./contracts/p1/Broker.sol

21:    using FixLib for uint192;	// @audit-issue

25:    uint48 public constant MAX_AUCTION_LENGTH = 60 * 60 * 24 * 7; // {s} max valid duration, 1 week	// @audit-issue

28:    uint48 public constant MIN_AUCTION_LENGTH = 60; // {s} 60 seconds auction min duration	// @audit-issue

36:    ITrade public batchTradeImplementation;	// @audit-issue

39:    IGnosis public gnosis;	// @audit-issue

43:    uint48 public batchAuctionLength;	// @audit-issue

49:    bool public batchTradeDisabled;	// @audit-issue

57:    ITrade public dutchTradeImplementation;	// @audit-issue

60:    uint48 public dutchAuctionLength;	// @audit-issue

63:    mapping(IERC20Metadata => bool) public dutchTradeDisabled;	// @audit-issue
```
[21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L21-L21), [25](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L25-L25), [28](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L28-L28), [36](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L36-L36), [39](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L39-L39), [43](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L43-L43), [49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L49-L49), [57](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L57-L57), [60](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L60-L60), [63](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L63-L63), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

21:	// @audit-issue

22:    uint192 public constant MAX_TRADE_VOLUME = 1e29; // {UoA}	// @audit-issue

23:    uint192 public constant MAX_TRADE_SLIPPAGE = 1e18; // {%}	// @audit-issue

29:    mapping(IERC20 => ITrade) public trades;	// @audit-issue

30:    uint48 public tradesOpen;	// @audit-issue

33:    uint192 public maxTradeSlippage; // {%}	// @audit-issue

34:    uint192 public minTradeVolume; // {UoA}	// @audit-issue

37:    uint256 public tradesNonce; // to keep track of how many trades have been opened in total	// @audit-issue
```
[21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L21-L21), [22](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L22-L22), [23](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L23-L23), [29](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L29-L29), [30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L30-L30), [33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L33-L33), [34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L34-L34), [37](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L37-L37), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

21:    IMain public main;	// @audit-issue
```
[21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L21-L21), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

24:    TradeKind public constant KIND = TradeKind.BATCH_AUCTION;	// @audit-issue

25:    uint256 public constant FEE_DENOMINATOR = 1000;	// @audit-issue

29:    uint96 public constant MAX_ORDERS = 5000; // bounded to avoid going beyond block gas limit	// @audit-issue

32:    uint192 public constant DEFAULT_MIN_BID = FIX_ONE / 100; // {tok}	// @audit-issue

35:    TradeStatus public status;	// @audit-issue

39:    IGnosis public gnosis; // Gnosis Auction contract	// @audit-issue

40:    uint256 public auctionId; // The Gnosis Auction ID returned by gnosis.initiateAuction()	// @audit-issue

41:    IBroker public broker; // The Broker that cloned this contract into existence	// @audit-issue

46:    address public origin;	// @audit-issue

47:    IERC20Metadata public sell; // address of token this trade is selling	// @audit-issue

48:    IERC20Metadata public buy; // address of token this trade is buying	// @audit-issue

49:    uint256 public initBal; // {qSellTok}, this trade's balance of `sell` when init() was called	// @audit-issue

50:    uint192 public sellAmount; // {sellTok}, quantity of whole tokens being sold, != initBal	// @audit-issue

51:    uint48 public endTime; // timestamp after which this trade's auction can be settled	// @audit-issue

52:    uint192 public worstCasePrice; // D27{qBuyTok/qSellTok}, the worst price we expect to get	// @audit-issue
```
[24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L24-L24), [25](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L25-L25), [29](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L29-L29), [32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L32-L32), [35](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L35-L35), [39](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L39-L39), [40](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L40-L40), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L41-L41), [46](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L46-L46), [47](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L47-L47), [48](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L48-L48), [49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L49-L49), [50](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L50-L50), [51](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L51-L51), [52](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L52-L52), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

95:    TradeKind public constant KIND = TradeKind.DUTCH_AUCTION;	// @audit-issue

97:    BidType public bidType; // = BidType.NONE	// @audit-issue

99:    TradeStatus public status; // reentrancy protection	// @audit-issue

101:    IBroker public broker; // The Broker that cloned this contract into existence	// @audit-issue

102:    ITrading public origin; // the address that initialized the contract	// @audit-issue

105:    IERC20Metadata public sell;	// @audit-issue

106:    IERC20Metadata public buy;	// @audit-issue

107:    uint192 public sellAmount; // {sellTok}	// @audit-issue

110:    uint48 public startTime; // {s} when the dutch auction begins (one block after init()) lossy!	// @audit-issue

111:    uint48 public endTime; // {s} when the dutch auction ends	// @audit-issue

113:    uint192 public bestPrice; // {buyTok/sellTok} The best plausible price based on oracle data	// @audit-issue

114:    uint192 public worstPrice; // {buyTok/sellTok} The worst plausible price based on oracle data	// @audit-issue

117:    address public bidder;	// @audit-issue
```
[95](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L95-L95), [97](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L97-L97), [99](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L99-L99), [101](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L101-L101), [102](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L102-L102), [105](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L105-L105), [106](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L106-L106), [107](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L107-L107), [110](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L110-L110), [111](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L111-L111), [113](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L113-L113), [114](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L114-L114), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L117-L117), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

33:    uint256 public constant ONE_HUNDRED_PERCENT = 1e8; // {micro %}	// @audit-issue

36:    uint256 public constant MIN_VOTING_DELAY = 86400; // {s} ONE_DAY	// @audit-issue
```
[33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L33-L33), [36](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L36-L36), 


#### Recommendation

Avoid creating explicit getter functions for 'public' state variables in Solidity. The compiler automatically generates getters for such variables, making additional functions redundant. This practice helps reduce contract size, lowers deployment costs, and simplifies maintenance and understanding of the contract.

### Use `do while` loops intead of for loops
A `do while` loop will cost less gas since the condition is not being checked for the first iteration.
```solidity
uint256 i = 1;
do {                   
    param2 += i;
    i++;
}
while (i < 50);
``` 
is better than
```solidity
for(uint256 i = 1; i< 50; i++){
    param1 += i;
}
```


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

39:        for (uint256 i = 0; i < validForVersions.length; ++i) {	// @audit-issue

67:        for (uint256 i = 0; i < _versionHashes.length; ++i) {	// @audit-issue

95:        for (uint256 i = 0; i < _assets.length; ++i) {	// @audit-issue
```
[39](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L39-L39), [67](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L67-L67), [95](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L95-L95), 


```solidity
Path: ./contracts/libraries/Array.sol

11:        for (uint256 i = 1; i < arrLen; ++i) {	// @audit-issue

12:            for (uint256 j = 0; j < i; ++j) {	// @audit-issue

23:        for (uint256 i = 1; i < arrLen; ++i) {	// @audit-issue
```
[11](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L11-L11), [12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L12-L12), [23](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L23-L23), 


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

535:        for (uint48 i = 0; i < basketNonces.length; ++i) {	// @audit-issue

547:            for (uint256 j = 0; j < b.erc20s.length; ++j) {	// @audit-issue

554:                for (uint256 k = 0; k < len; ++k) {	// @audit-issue

593:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

615:        for (uint256 i = 0; i < length; ++i) {	// @audit-issue

676:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

684:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

713:        for (uint256 i = 0; i < b.erc20s.length; ++i) {	// @audit-issue

751:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue

769:        for (uint256 i = 0; i < erc20s.length; ++i) {	// @audit-issue
```
[142](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L142-L142), [251](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L251-L251), [260](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L260-L260), [296](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L296-L296), [321](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L321-L321), [424](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L424-L424), [491](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L491-L491), [535](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L535-L535), [547](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L547-L547), [554](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L554-L554), [593](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L593-L593), [615](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L615-L615), [676](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L676-L676), [684](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L684-L684), [713](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L713-L713), [751](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L751-L751), [769](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L769-L769), 


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

202:            for (targetIndex = 0; targetIndex < targetsLength; ++targetIndex) {	// @audit-issue

241:        for (uint256 i = 0; i < targetsLength; ++i) {	// @audit-issue

252:            for (uint256 j = 0; j < backup.erc20s.length && size < backup.max; ++j) {	// @audit-issue

263:            for (uint256 j = 0; j < backup.erc20s.length && assigned < size; ++j) {	// @audit-issue

325:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

337:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

363:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue

380:        for (uint256 i = 0; i < len; ++i) {	// @audit-issue
```
[72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L72-L72), [80](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L80-L80), [180](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L180-L180), [198](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L198-L198), [202](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L202-L202), [241](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L241-L241), [252](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L252-L252), [263](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L263-L263), [325](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L325-L325), [337](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L337-L337), [363](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L363-L363), [380](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L380-L380), 


#### Recommendation

Where appropriate, consider using a `do while` loop instead of a `for` loop in your Solidity contracts. This is especially beneficial when the first iteration of the loop does not require a condition check. Refactor your loop logic to fit the `do while` structure for more gas-efficient execution. However, ensure that the loop's logic and termination conditions are correctly implemented to avoid infinite loops or other logical errors. Always balance gas efficiency with code readability and the specific requirements of your contract's logic.

### Using XOR (^) and AND (&) bitwise equivalents for gas optimizations
Given 4 variables a, b, c and d represented as such:
```
0 0 0 0 0 1 1 0 <- a
0 1 1 0 0 1 1 0 <- b
0 0 0 0 0 0 0 0 <- c
1 1 1 1 1 1 1 1 <- d
```
To have a == b means that every 0 and 1 match on both variables. Meaning that a XOR (operator ^) would evaluate to 0 ((a ^ b) == 0), as it excludes by definition any equalities.Now, if a != b, this means that there’s at least somewhere a 1 and a 0 not matching between a and b, making (a ^ b) != 0.Both formulas are logically equivalent and using the XOR bitwise operator costs actually the same amount of gas.However, it is much cheaper to use the bitwise OR operator (|) than comparing the truthy or falsy values.These are logically equivalent too, as the OR bitwise operator (|) would result in a 1 somewhere if any value is not 0 between the XOR (^) statements, meaning if any XOR (^) statement verifies that its arguments are different.


```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

36:        if (address(_roleRegistry) == address(0)) {	// @audit-issue

45:        if (feeRecipient_ == address(0)) {	// @audit-issue

48:        if (feeRecipient_ == feeRecipient) {	// @audit-issue
```
[36](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L36-L36), [45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L45-L45), [48](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L48-L48), 


```solidity
Path: ./contracts/registry/VersionRegistry.sol

28:        if (address(_roleRegistry) == address(0)) {	// @audit-issue

42:        if (address(deployer) == address(0)) {	// @audit-issue
```
[28](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L28-L28), [42](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L42-L42), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

35:        if (_asset == address(0)) {	// @audit-issue

41:            if (address(versionRegistry.deployments(versionHash)) == address(0)) {	// @audit-issue

63:        if (_asset == address(0)) {	// @audit-issue

69:            if (address(versionRegistry.deployments(versionHash)) == address(0)) {	// @audit-issue

91:        if (address(versionRegistry.deployments(_versionHash)) == address(0)) {	// @audit-issue

97:            if (asset == address(0)) {	// @audit-issue
```
[35](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L35-L35), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L41-L41), [63](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L63-L63), [69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L69-L69), [91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L91-L91), [97](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L97-L97), 


```solidity
Path: ./contracts/mixins/Auth.sol

93:        if (role == LONG_FREEZER) longFreezes[account] = LONG_FREEZE_CHARGES;	// @audit-issue

143:        if (longFreezes[_msgSender()] == 0) _revokeRole(LONG_FREEZER, _msgSender());	// @audit-issue
```
[93](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L93-L93), [143](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L143-L143), 


```solidity
Path: ./contracts/libraries/Array.sol

13:                if (arr[i] == arr[j]) return false;	// @audit-issue
```
[13](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L13-L13), 


```solidity
Path: ./contracts/libraries/Throttle.sol

43:        if (throttle.params.amtRate == 0 && throttle.params.pctRate == 0) return;	// @audit-issue

52:        if (available != throttle.lastAvailable || available == limit) {	// @audit-issue
```
[43](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L43-L43), [52](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L52-L52), 


```solidity
Path: ./contracts/libraries/Fixed.sol

212:        if (x == 0) return 0;	// @audit-issue

213:        if (decimals <= -59) return (rounding == CEIL ? 1 : 0); // 59, because 1e58 > 2**192	// @audit-issue

319:        if (y == 1) return x_;	// @audit-issue

320:        if (x_ == FIX_ONE || y == 0) return FIX_ONE;	// @audit-issue

324:            if (y & 1 == 1) result = (result * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;	// @audit-issue

354:        return x == y;	// @audit-issue

393:        if (x == 0) return 0; // always computable, no matter what decimals is	// @audit-issue

394:        if (decimals <= -42) return (rounding == CEIL ? 1 : 0);	// @audit-issue

501:        if (a == 0 || b == 0) return 0;	// @audit-issue

504:        if (a == FIX_MAX || b == FIX_MAX) return FIX_MAX;	// @audit-issue

515:            if (rounding == RoundingMode.ROUND) shiftDelta += (FIX_ONE / 2);	// @audit-issue

516:            else if (rounding == RoundingMode.CEIL) shiftDelta += FIX_ONE - 1;	// @audit-issue

549:        if (a == 0) return 0;	// @audit-issue

550:        if (b == 0) return FIX_MAX;	// @audit-issue

567:        if (a == 0 || b == 0) return 0;	// @audit-issue

568:        if (a == FIX_MAX || b == FIX_MAX || c == 0) return FIX_MAX;	// @audit-issue

597:            if (rounding == CEIL) {	// @audit-issue

599:            } else if (rounding == ROUND) {	// @audit-issue
```
[212](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L212-L212), [213](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L213-L213), [319](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L319-L319), [320](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L320-L320), [324](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L324-L324), [354](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L354-L354), [393](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L393-L393), [394](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L394-L394), [501](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L501-L501), [504](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L504-L504), [515](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L515-L515), [516](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L516-L516), [549](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L549-L549), [550](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L550-L550), [567](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L567-L567), [568](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L568-L568), [597](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L597-L597), [599](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L599-L599), 


```solidity
Path: ./contracts/libraries/Allowance.sol

28:        require(token.allowance(address(this), spender) == 0, "allowance not 0");	// @audit-issue

30:        if (value == 0) return;	// @audit-issue

35:            success = token.allowance(address(this), spender) == value;	// @audit-issue
```
[28](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L28-L28), [30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L30-L30), [35](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L35-L35), 


```solidity
Path: ./contracts/libraries/Permit.sol

19:                IERC1271Upgradeable(owner).isValidSignature(hash, abi.encodePacked(r, s, v)) ==	// @audit-issue
```
[19](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Permit.sol#L19-L19), 


```solidity
Path: ./contracts/p1/Main.sol

63:        require(address(versionRegistry) == address(0), "already set");	// @audit-issue

72:        require(address(assetPluginRegistry) == address(0), "already set");	// @audit-issue

81:        require(address(daoFeeRegistry) == address(0), "already set");	// @audit-issue

108:        require(keccak256(abi.encodePacked(this.version())) == versionHash, "upgrade mismatch");	// @audit-issue

117:        require(keccak256(abi.encodePacked(this.version())) == versionHash, "upgrade main first");	// @audit-issue

154:        require(msg.sender == address(this), "not self");	// @audit-issue
```
[63](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L63-L63), [72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L72-L72), [81](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L81-L81), [108](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L108-L108), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L117-L117), [154](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L154-L154), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

62:            if (errData.length == 0) revert(); // solhint-disable-line reason-string	// @audit-issue

78:        if (tokenToBuy == rsr) {	// @audit-issue

79:            require(revTotals.rsrTotal == 0, "rsrTotal > 0");	// @audit-issue

80:        } else if (address(tokenToBuy) == address(rToken)) {	// @audit-issue

81:            require(revTotals.rTokenTotal == 0, "rTokenTotal > 0");	// @audit-issue

116:        require(len == kinds.length, "length mismatch");	// @audit-issue

119:            (tokenToBuy == rsr && revTotals.rsrTotal != 0) ||	// @audit-issue

120:                (address(tokenToBuy) == address(rToken) && revTotals.rTokenTotal != 0),	// @audit-issue

126:        bool involvesRToken = tokenToBuy == IERC20(address(rToken));	// @audit-issue

128:            if (erc20s[i] == IERC20(address(rToken))) involvesRToken = true;	// @audit-issue

129:            if (erc20s[i] == tokenToBuy) {	// @audit-issue

131:                if (len == 1) return; // return early if tokenToBuy is only entry	// @audit-issue

155:            if (erc20 == tokenToBuy) continue;	// @audit-issue

157:            require(address(trades[erc20]) == address(0), "trade open");	// @audit-issue
```
[62](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L62-L62), [78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L78-L78), [79](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L79-L79), [80](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L80-L80), [81](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L81-L81), [116](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L116-L116), [119](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L119-L119), [120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L120-L120), [126](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L126-L126), [128](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L128-L128), [129](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L129-L129), [131](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L131-L131), [155](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L155-L155), [157](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L157-L157), 


```solidity
Path: ./contracts/p1/Distributor.sol

85:        require(dests.length == shares.length, "array length mismatch");	// @audit-issue

124:        require(caller == rsrTrader || caller == rTokenTrader, "RevenueTraders only");	// @audit-issue

125:        require(erc20 == rsr || erc20 == rToken, "RSR or RToken");	// @audit-issue

126:        bool isRSR = erc20 == rsr; // if false: isRToken	// @audit-issue

151:            if (numberOfShares == 0) continue;	// @audit-issue

155:            if (addrTo == FURNACE) {	// @audit-issue

158:            } else if (addrTo == ST_RSR) {	// @audit-issue

247:        if (dest == FURNACE) require(share.rsrDist == 0, "Furnace must get 0% of RSR");	// @audit-issue

248:        if (dest == ST_RSR) require(share.rTokenDist == 0, "StRSR must get 0% of RToken");	// @audit-issue

252:        if (share.rsrDist == 0 && share.rTokenDist == 0) {	// @audit-issue
```
[85](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L85-L85), [124](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L124-L124), [125](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L125-L125), [126](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L126-L126), [151](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L151-L151), [155](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L155-L155), [158](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L158-L158), [247](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L247-L247), [248](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L248-L248), [252](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L252-L252), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

110:        require(assets[asset.erc20()] == asset, "asset not found");	// @audit-issue

203:            !_erc20s.contains(address(asset.erc20())) || assets[asset.erc20()] == asset,	// @audit-issue

216:                ICollateral(address(asset)).status() == CollateralStatus.SOUND,	// @audit-issue

234:            if (assets[erc20] == asset) return false;	// @audit-issue
```
[110](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L110-L110), [203](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L203-L203), [216](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L216-L216), [234](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L234-L234), 


```solidity
Path: ./contracts/p1/BackingManager.sol

90:        if (_msgSender() == address(trade)) {	// @audit-issue

97:                if (errData.length == 0) revert(); // solhint-disable-line reason-string	// @audit-issue

116:            _msgSender() == address(this) || tradeEnd[kind] < block.timestamp,	// @audit-issue

120:        require(tradesOpen == 0, "trade open");	// @audit-issue

159:            if (sellERC20 == rsr) {	// @audit-issue

187:        require(tradesOpen == 0, "trade open");	// @audit-issue

248:                if (tokensPerShare == 0) continue;	// @audit-issue

298:            if (reg.erc20s[i] == rsr) ctx.bals[i] += reg.assets[i].bal(address(stRSR));	// @audit-issue

308:        assert(tradesOpen == 0);	// @audit-issue
```
[90](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L90-L90), [97](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L97-L97), [116](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L116-L116), [120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L120-L120), [159](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L159-L159), [187](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L187-L187), [248](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L248-L248), [298](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L298-L298), [308](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L308-L308), 


```solidity
Path: ./contracts/p1/StRSR.sol

309:        if (endId == 0 || firstId >= endId) return;	// @audit-issue

329:        if (rsrAmount == 0) return;	// @audit-issue

355:        if (endId == 0 || firstId >= endId) return;	// @audit-issue

376:        if (rsrAmount == 0) return;	// @audit-issue

429:        require(caller == address(backingManager), "!bm");	// @audit-issue

450:        if (stakeRSR == 0 || stakeRate > MAX_STAKE_RATE) {	// @audit-issue

466:        if (draftRSR == 0 || draftRate > MAX_DRAFT_RATE) {	// @audit-issue

621:        stakeRate = (stakeRSR == 0 || totalStakes == 0)	// @audit-issue
```
[309](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L309-L309), [329](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L329-L329), [355](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L355-L355), [376](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L376-L376), [429](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L429-L429), [450](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L450-L450), [466](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L466-L466), [621](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L621-L621), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

138:        require(_msgSender() == address(assetRegistry), "asset registry only");	// @audit-issue

163:                (lastStatus == CollateralStatus.DISABLED && !main.tradingPausedOrFrozen()),	// @audit-issue

233:        require(erc20s.length != 0 && erc20s.length == targetAmts.length, "invalid lengths");	// @audit-issue

319:        if (disabled || size == 0) return CollateralStatus.DISABLED;	// @audit-issue

324:                if (s == CollateralStatus.DISABLED) return CollateralStatus.DISABLED;	// @audit-issue

333:            status() == CollateralStatus.SOUND &&	// @audit-issue

372:            if (pegPrice == 0) return FIX_ONE;	// @audit-issue

397:        if (refPerTok == 0) return FIX_MAX;	// @audit-issue

427:                if (qty == 0) continue;	// @audit-issue

434:                    if (highP == FIX_MAX) {	// @audit-issue

450:                if (errData.length == 0) revert(); // solhint-disable-line reason-string	// @audit-issue

472:        return quote(amount, rounding == CEIL, rounding);	// @audit-issue

526:        require(basketNonces.length == portions.length, "invalid lengths");	// @audit-issue

550:                if (address(b.erc20s[j]) == address(0)) continue;	// @audit-issue

555:                    if (b.erc20s[j] == erc20sAll[k]) {	// @audit-issue

563:                if (erc20Index == type(uint256).max) {	// @audit-issue

579:                        if (errData.length == 0) revert(); // solhint-disable-line reason-string	// @audit-issue

612:        if (length == 0 || disabled) return BasketRange(FIX_ZERO, FIX_MAX);	// @audit-issue

617:            if (coll.status() == CollateralStatus.DISABLED) return BasketRange(FIX_ZERO, FIX_MAX);	// @audit-issue

621:            if (q == FIX_MAX) return BasketRange(FIX_ZERO, FIX_MAX);	// @audit-issue

728:                if (errData.length == 0) revert(); // solhint-disable-line reason-string	// @audit-issue
```
[138](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L138-L138), [163](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L163-L163), [233](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L233-L233), [319](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L319-L319), [324](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L324-L324), [333](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L333-L333), [372](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L372-L372), [397](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L397-L397), [427](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L427-L427), [434](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L434-L434), [450](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L450-L450), [472](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L472-L472), [526](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L526-L526), [550](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L550-L550), [555](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L555-L555), [563](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L563-L563), [579](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L579-L579), [612](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L612-L612), [617](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L617-L617), [621](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L621-L621), [728](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L728-L728), 


```solidity
Path: ./contracts/p1/RToken.sol

215:            if (amounts[i] == 0) continue;	// @audit-issue

272:        require(portionsSum == FIX_ONE, "portions do not add up to FIX_ONE");	// @audit-issue

322:                if (amounts[i] == 0) continue; // unregistered ERC20s will have 0 amount	// @audit-issue

357:        require(_msgSender() == address(backingManager), "not backing manager");	// @audit-issue

372:        require(caller == address(furnace), "furnace only");	// @audit-issue

389:        require(caller == address(backingManager), "not backing manager");	// @audit-issue

398:        require(_msgSender() == address(backingManager), "not backing manager");	// @audit-issue
```
[215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L215-L215), [272](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L272-L272), [322](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L322-L322), [357](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L357-L357), [372](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L372-L372), [389](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L389-L389), [398](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L398-L398), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

88:        return pos == 0 ? 0 : _checkpoints[era][account][pos - 1].val;	// @audit-issue

158:            return high == 0 ? 0 : ckpts[high - 1].val;	// @audit-issue

181:        require(nonce == _useDelegationNonce(signer), "invalid nonce");	// @audit-issue

192:        if (delegatee == address(0) && currentDelegate == address(0)) {	// @audit-issue

265:            Checkpoint memory oldCkpt = pos == 0 ? Checkpoint(0, 0) : ckpts[pos - 1];	// @audit-issue

270:            if (pos != 0 && oldCkpt.fromTimepoint == clock()) {	// @audit-issue
```
[88](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L88-L88), [158](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L158-L158), [181](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L181-L181), [192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L192-L192), [265](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L265-L265), [270](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L270-L270), 


```solidity
Path: ./contracts/p1/Broker.sol

133:            caller == address(backingManager) ||	// @audit-issue

134:                caller == address(rsrTrader) ||	// @audit-issue

135:                caller == address(rTokenTrader),	// @audit-issue

140:        if (kind == TradeKind.BATCH_AUCTION) {	// @audit-issue

155:        if (kind == TradeKind.BATCH_AUCTION) {	// @audit-issue

158:        } else if (kind == TradeKind.DUTCH_AUCTION) {	// @audit-issue

160:            if (DutchTrade(address(trade)).origin() == backingManager) {	// @audit-issue

199:            newAuctionLength == 0 ||	// @audit-issue

221:            newAuctionLength == 0 ||	// @audit-issue

299:        return asset.lastSave() == block.timestamp || address(asset.erc20()) == address(rToken);	// @audit-issue
```
[133](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L133-L133), [134](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L134-L134), [135](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L135-L135), [140](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L140-L140), [155](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L155-L155), [158](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L158-L158), [160](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L160-L160), [199](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L199-L199), [221](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L221-L221), [299](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L299-L299), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

126:        assert(address(trades[sell]) == address(0)); // ensure calling class has checked this	// @audit-issue
```
[126](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L126-L126), 


```solidity
Path: ./contracts/p1/mixins/TradeLib.sol

71:            require(trade.prices.sellLow == 0, "trade pricing error");	// @audit-issue

176:        uint192 size = price == 0 ? FIX_MAX : minTradeVolume.div(price, CEIL);	// @audit-issue
```
[71](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L71-L71), [176](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L176-L176), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

62:        require(_msgSender() == address(main), "main only");	// @audit-issue
```
[62](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L62-L62), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

50:        if (address(trade.sell) == address(0) || address(trade.buy) == address(0)) {	// @audit-issue

58:            trade.prices.sellLow == 0 ||	// @audit-issue

141:            if (reg.erc20s[i] == IERC20(address(ctx.rToken))) continue;	// @audit-issue

148:                ctx.quantities[i] == 0 &&	// @audit-issue

289:            if (address(reg.erc20s[i]) == address(ctx.rToken)) continue;	// @audit-issue

290:            else if (reg.erc20s[i] == ctx.rsr) {	// @audit-issue

302:                if (high == 0) continue; // skip over worthless assets	// @audit-issue

357:        if (address(trade.sell) == address(0) && address(trade.buy) != address(0)) {	// @audit-issue

387:        if (curr.surplusStatus == CollateralStatus.DISABLED) {	// @audit-issue

388:            return other == CollateralStatus.DISABLED && surplusAmt.gt(curr.surplus);	// @audit-issue

389:        } else if (curr.surplusStatus == CollateralStatus.SOUND) {	// @audit-issue

391:                other == CollateralStatus.DISABLED ||	// @audit-issue

392:                (other == CollateralStatus.SOUND && surplusAmt.gt(curr.surplus));	// @audit-issue
```
[50](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L50-L50), [58](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L58-L58), [141](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L141-L141), [148](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L148-L148), [289](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L289-L289), [290](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L290-L290), [302](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L302-L302), [357](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L357-L357), [387](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L387-L387), [388](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L388-L388), [389](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L389-L389), [391](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L391-L391), [392](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L392-L392), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

99:        if (weight == FIX_ZERO) return;	// @audit-issue

203:                if (targetNames.at(targetIndex) == config.targetNames[_erc20]) break;	// @audit-issue

257:            if (size == 0) return false;	// @audit-issue

296:        if (address(erc20) == address(0)) return false;	// @audit-issue

303:                targetName == coll.targetName() &&	// @audit-issue

304:                coll.status() == CollateralStatus.SOUND &&	// @audit-issue

344:        require(_targetAmts.length() == 0, "missing target weights");	// @audit-issue

365:            require(coll.status() == CollateralStatus.SOUND, "unsound new collateral");	// @audit-issue
```
[99](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L99-L99), [203](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L203-L203), [257](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L257-L257), [296](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L296-L296), [303](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L303-L303), [304](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L304-L304), [344](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L344-L344), [365](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L365-L365), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

59:        require(status == begin, "Invalid trade state");	// @audit-issue

62:        assert(status == TradeStatus.PENDING);	// @audit-issue

133:        if (minBuyAmtPerOrder == 0) minBuyAmtPerOrder = 1;	// @audit-issue

180:        require(msg.sender == origin, "only origin can settle");	// @audit-issue

226:        require(status == TradeStatus.CLOSED, "only after trade is closed");	// @audit-issue

233:        return status == TradeStatus.OPEN && endTime <= block.timestamp;	// @audit-issue
```
[59](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L59-L59), [62](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L62-L62), [133](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L133-L133), [180](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L180-L180), [226](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L226-L226), [233](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L233-L233), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

122:        require(status == begin, "Invalid trade state");	// @audit-issue

125:        assert(status == TradeStatus.PENDING);	// @audit-issue

200:        require(bidder == address(0), "bid already received");	// @audit-issue

201:        assert(status == TradeStatus.OPEN);	// @audit-issue

225:        assert(status == TradeStatus.CLOSED);	// @audit-issue

236:        require(bidder == address(0), "bid already received");	// @audit-issue

237:        assert(status == TradeStatus.OPEN);	// @audit-issue

268:        assert(status == TradeStatus.CLOSED);	// @audit-issue

279:        require(msg.sender == address(origin), "only origin can settle");	// @audit-issue

282:        if (bidType == BidType.CALLBACK) {	// @audit-issue

284:        } else if (bidType == BidType.TRANSFER) {	// @audit-issue

299:        require(status == TradeStatus.CLOSED, "only after trade is closed");	// @audit-issue

306:        return status == TradeStatus.OPEN && (bidder != address(0) || block.timestamp > endTime);	// @audit-issue
```
[122](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L122-L122), [125](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L125-L125), [200](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L200-L200), [201](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L201-L201), [225](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L225-L225), [236](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L236-L236), [237](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L237-L237), [268](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L268-L268), [279](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L279-L279), [282](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L282-L282), [284](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L284-L284), [299](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L299-L299), [306](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L306-L306), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

189:        return currentEra == pastEra;	// @audit-issue
```
[189](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L189-L189), 


#### Recommendation

Review your Solidity contracts to identify any computations that are performed multiple times with the same inputs. Cache the results of these computations in local variables and reuse them within the function or across function calls if the state remains unchanged.

### Consider using `bytes32` rather than a `string`
Using the bytes types for fixed-length strings is more efficient than having the EVM have to incur the overhead of string processing. Consider whether the value needs to be a string. A good reason to keep it as a string would be if the variable is defined in an interface that this project does not own.


```solidity
Path: ./contracts/registry/VersionRegistry.sol

46:        string memory version = deployer.version();	// @audit-issue
```
[46](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L46-L46), 


```solidity
Path: ./contracts/p1/StRSR.sol

45:    string public name; // immutable	// @audit-issue

46:    string public symbol; // immutable	// @audit-issue
```
[45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L45-L45), [46](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L46-L46), 


```solidity
Path: ./contracts/p1/Deployer.sol

31:    string public constant ENS = "reserveprotocol.eth";	// @audit-issue

228:            string memory stRSRSymbol = string(abi.encodePacked(StringLib.toLower(symbol), "RSR"));	// @audit-issue

229:            string memory stRSRName = string(abi.encodePacked(stRSRSymbol, " Token"));	// @audit-issue
```
[31](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L31-L31), [228](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L228-L228), [229](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L229-L229), 


```solidity
Path: ./contracts/p1/RToken.sol

44:    string public mandate;	// @audit-issue
```
[44](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L44-L44), 


#### Recommendation

For fixed-length strings, prefer using 'bytes32' over 'string' to leverage EVM efficiency and reduce overhead. Evaluate the necessity of using a string, especially if the variable isn't part of an externally defined interface.

### The result of a function call should be cached rather than re-calling the function
The function calls in solidity are expensive. If the same result of the same function calls are to be used several times, the result should be cached to reduce the gas consumption of repeated calls.        

```solidity
Path: ./contracts/mixins/Auth.sol

140:        longFreezes[_msgSender()] -= 1; // reverts on underflow	// @audit-issue: Function call `_msgSender` is called multiple times at lines [143, 143].
```
[140](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L140-L140), 


```solidity
Path: ./contracts/libraries/Allowance.sol

35:            success = token.allowance(address(this), spender) == value;	// @audit-issue: Function call `allowance` is called multiple times at lines [28, 44].
```
[35](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L35-L35), 


```solidity
Path: ./contracts/libraries/Permit.sol

19:                IERC1271Upgradeable(owner).isValidSignature(hash, abi.encodePacked(r, s, v)) ==	// @audit-issue: Function call `encodePacked` is called multiple times at lines [28].
```
[19](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Permit.sol#L19-L19), 


```solidity
Path: ./contracts/p1/Main.sol

141:            assetRegistry.validateCurrentAssets();	// @audit-issue: Function call `validateCurrentAssets` is called multiple times at lines [125].

144:        IExtendedBroker(address(broker)).setBatchTradeImplementation(	// @audit-issue: Function call `IExtendedBroker` is called multiple times at lines [147].
```
[141](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L141-L141), [144](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L144-L144), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

126:        bool involvesRToken = tokenToBuy == IERC20(address(rToken));	// @audit-issue: Function call `IERC20` is called multiple times at lines [128].
```
[126](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L126-L126), 


```solidity
Path: ./contracts/p1/Distributor.sol

145:        for (uint256 i = 0; i < destinations.length(); ++i) {	// @audit-issue: Function call `length` is called multiple times at lines [139].

170:            IERC20Upgradeable(address(erc20)).safeTransferFrom(	// @audit-issue: Function call `IERC20Upgradeable` is called multiple times at lines [183].

180:                (address recipient, , ) = main.daoFeeRegistry().getFeeDetails(address(rToken));	// @audit-issue: Function call `daoFeeRegistry` is called multiple times at lines [177].

215:            (address feeRecipient, uint256 feeNumerator, uint256 feeDenominator) = main	// @audit-issue: Function call `daoFeeRegistry` is called multiple times at lines [212].
```
[145](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L145-L145), [170](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L170-L170), [180](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L180-L180), [215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L215-L215), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

91:        require(_erc20s.contains(address(asset.erc20())), "no ERC20 collision");	// @audit-issue: Function call `erc20` is called multiple times at lines [93].

96:            basketHandler.disableBasket();	// @audit-issue: Function call `disableBasket` is called multiple times at lines [94].

120:        emit AssetUnregistered(asset.erc20(), asset);	// @audit-issue: Function call `erc20` is called multiple times at lines [109, 119, 110, 118, 112].

115:            basketHandler.disableBasket();	// @audit-issue: Function call `disableBasket` is called multiple times at lines [113].

164:            reg.erc20s[i] = IERC20(_erc20s.at(i));	// @audit-issue: Function call `IERC20` is called multiple times at lines [165].

165:            reg.assets[i] = assets[IERC20(_erc20s.at(i))];	// @audit-issue: Function call `at` is called multiple times at lines [164].

203:            !_erc20s.contains(address(asset.erc20())) || assets[asset.erc20()] == asset,	// @audit-issue: Function call `erc20` is called multiple times at lines [203].

224:                main.assetPluginRegistry().isValidAsset(	// @audit-issue: Function call `assetPluginRegistry` is called multiple times at lines [221].
```
[91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L91-L91), [96](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L96-L96), [120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L120-L120), [115](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L115-L115), [164](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L164-L164), [165](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L165-L165), [203](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L203-L203), [224](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L224-L224), 


```solidity
Path: ./contracts/p1/BackingManager.sol

73:        IERC20(address(erc20)).safeApprove(address(rToken), type(uint256).max);	// @audit-issue: Function call `IERC20` is called multiple times at lines [72].

132:        if (basketsHeld.bottom >= rToken.basketsNeeded()) return; // return if now capitalized	// @audit-issue: Function call `basketsNeeded` is called multiple times at lines [125].

223:            rToken.mint(baskets - rToken.basketsNeeded());	// @audit-issue: Function call `basketsNeeded` is called multiple times at lines [222, 226, 190].

215:            IERC20(address(rsr)).safeTransfer(address(stRSR), rsr.balanceOf(address(this)));	// @audit-issue: Function call `balanceOf` is called multiple times at lines [213].

288:        ctx.quantities = new uint192[](reg.erc20s.length);	// @audit-issue: Function call is called multiple times at line(s) [293].
```
[73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L73-L73), [132](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L132-L132), [223](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L223-L223), [215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L215-L215), [288](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L288-L288), 


```solidity
Path: ./contracts/p1/StRSR.sol

193:        rsr = IERC20(address(main_.rsr()));	// @audit-issue: Function call `rsr` is called multiple times at lines [196].

476:        emit ExchangeRateSet(initRate, exchangeRate());	// @audit-issue: Function call `exchangeRate` is called multiple times at lines [437].

473:        rsrRewardsAtLastPayout = rsrRewards() - seizedRSR;	// @audit-issue: Function call `rsrRewards` is called multiple times at lines [438].

626:        emit ExchangeRateSet(initRate, exchangeRate());	// @audit-issue: Function call `exchangeRate` is called multiple times at lines [597].
```
[193](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L193-L193), [476](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L476-L476), [473](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L473-L473), [626](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L626-L626), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

563:                if (erc20Index == type(uint256).max) {	// @audit-issue: Function call is called multiple times at line(s) [553].

612:        if (length == 0 || disabled) return BasketRange(FIX_ZERO, FIX_MAX);	// @audit-issue: Function call `BasketRange` is called multiple times at lines [621, 617].
```
[563](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L563-L563), [612](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L612-L612), 


```solidity
Path: ./contracts/p1/Deployer.sol

156:                    new ERC1967Proxy(address(_implementations.components.distributor), new bytes(0))	// @audit-issue: Function call is called multiple times at line(s) [168, 127, 178, 123, 150, 134, 161, 118, 142, 174].
```
[156](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L156-L156), 


```solidity
Path: ./contracts/p1/RToken.sol

282:        emit Redemption(_msgSender(), recipient, amount, baskets);	// @audit-issue: Function call `_msgSender` is called multiple times at lines [267, 281].

312:            pastBals[i] = IERC20(expectedERC20sOut[i]).balanceOf(recipient);	// @audit-issue: Function call `balanceOf` is called multiple times at lines [340].

340:            uint256 bal = IERC20(expectedERC20sOut[i]).balanceOf(recipient);	// @audit-issue: Function call `IERC20` is called multiple times at lines [312].
```
[282](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L282-L282), [312](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L312-L312), [340](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L340-L340), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

270:            if (pos != 0 && oldCkpt.fromTimepoint == clock()) {	// @audit-issue: Function call `clock` is called multiple times at lines [275].

271:                ckpts[pos - 1].val = SafeCastUpgradeable.toUint224(newWeight);	// @audit-issue: Function call `toUint224` is called multiple times at lines [276].
```
[270](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L270-L270), [271](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L271-L271), 


```solidity
Path: ./contracts/p1/Broker.sol

98:        emit BatchTradeImplementationSet(ITrade(address(0)), batchTradeImplementation_);	// @audit-issue: Function call `ITrade` is called multiple times at lines [99].

151:        require(trades[_msgSender()], "unrecognized trade contract");	// @audit-issue: Function call `_msgSender` is called multiple times at lines [152].

287:        IERC20Upgradeable(address(req.sell.erc20())).safeTransferFrom(	// @audit-issue: Function call `erc20` is called multiple times at lines [274].
```
[98](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L98-L98), [151](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L151-L151), [287](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L287-L287), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

338:                    (uint192 low, uint192 high) = reg.assets[i].price(); // {UoA/buyTok}	// @audit-issue: Function call `price` is called multiple times at lines [300].

325:                    trade.sellAmount = ctx.bals[i].minus(needed);	// @audit-issue: Function call `minus` is called multiple times at lines [319, 305].

388:            return other == CollateralStatus.DISABLED && surplusAmt.gt(curr.surplus);	// @audit-issue: Function call `gt` is called multiple times at lines [392, 395].
```
[338](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L338-L338), [325](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L325-L325), [388](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L388-L388), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

183:        uint256 targetsLength = targetNames.length();	// @audit-issue: Function call `length` is called multiple times at lines [171, 172].

195:        uint192[] memory totalWeights = new uint192[](targetsLength);	// @audit-issue: Function call is called multiple times at line(s) [191].

264:                if (goodCollateral(_targetName, backup.erc20s[j], assetRegistry)) {	// @audit-issue: Function call `goodCollateral` is called multiple times at lines [253].

328:            (bool contains, uint256 amt) = _targetAmts.tryGet(targetName);	// @audit-issue: Function call `tryGet` is called multiple times at lines [339].
```
[183](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L183-L183), [195](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L195-L195), [264](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L264-L264), [328](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L328-L328), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

91:        require(req.sellAmount <= type(uint96).max, "sellAmount too large");	// @audit-issue: Function call is called multiple times at line(s) [92].

183:        if (!isAuctionCleared()) {	// @audit-issue: Function call `isAuctionCleared` is called multiple times at lines [188].
```
[91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L91-L91), [183](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L183-L183), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

260:            amountIn <= buy.balanceOf(address(this)) - balanceBefore,	// @audit-issue: Function call `balanceOf` is called multiple times at lines [257].

283:            soldAmt = lot(); // {qSellTok}	// @audit-issue: Function call `lot` is called multiple times at lines [285].
```
[260](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L260-L260), [283](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L283-L283), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

188:        uint256 currentEra = IStRSRVotes(address(token)).currentEra();	// @audit-issue: Function call `IStRSRVotes` is called multiple times at lines [187].
```
[188](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L188-L188), 


#### Recommendation

Cache the result of function calls in Solidity instead of making repeated calls to the same function. This practice significantly reduces gas consumption by minimizing costly function call operations.

### Avoid updating storage when the value hasn't changed
Manipulating storage in solidity is gas-intensive. It can be optimized by avoiding unnecessary storage updates when the new value equals the existing value. If the old value is equal to the new value, not re-storing the value will avoid a Gsreset (2900 gas), potentially at the expense of a Gcoldsload (2100 gas) or a Gwarmaccess (100 gas).

```solidity
Path: ./contracts/registry/VersionRegistry.sol

53:        deployments[versionHash] = deployer;	// @audit-issue
```
[53](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L53-L53), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

73:            _isValidAsset[versionHash][_asset] = _validities[i];	// @audit-issue

101:            _isValidAsset[_versionHash][asset] = _validities[i];	// @audit-issue
```
[73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L73-L73), [101](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L101-L101), 


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
Path: ./contracts/p1/Distributor.sol

259:        distribution[dest] = share;	// @audit-issue
```
[259](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L259-L259), 


```solidity
Path: ./contracts/p1/Furnace.sol

35:        rToken = main_.rToken();	// @audit-issue
```
[35](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L35-L35), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

44:        basketHandler = main_.basketHandler();	// @audit-issue

45:        backingManager = main_.backingManager();	// @audit-issue
```
[44](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L44-L44), [45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L45-L45), 


```solidity
Path: ./contracts/p1/StRSR.sol

187:        name = name_;	// @audit-issue

188:        symbol = symbol_;	// @audit-issue

190:        assetRegistry = main_.assetRegistry();	// @audit-issue

191:        backingManager = main_.backingManager();	// @audit-issue

192:        basketHandler = main_.basketHandler();	// @audit-issue

193:        rsr = IERC20(address(main_.rsr()));	// @audit-issue

196:        rsrRewardsAtLastPayout = main_.rsr().balanceOf(address(this));	// @audit-issue

883:        _allowances[era][owner][spender] = amount;	// @audit-issue
```
[187](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L187-L187), [188](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L188-L188), [190](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L190-L190), [191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L191-L191), [192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L192-L192), [193](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L193-L193), [196](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L196-L196), [883](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L883-L883), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

116:        assetRegistry = main_.assetRegistry();	// @audit-issue

117:        backingManager = main_.backingManager();	// @audit-issue

118:        rsr = main_.rsr();	// @audit-issue

119:        rToken = main_.rToken();	// @audit-issue

120:        stRSR = main_.stRSR();	// @audit-issue

123:        reweightable = reweightable_; // immutable thereafter	// @audit-issue

124:        enableIssuancePremium = enableIssuancePremium_;	// @audit-issue

267:            config.targetAmts[erc20s[i]] = targetAmts[i];	// @audit-issue

644:        enableIssuancePremium = val;	// @audit-issue
```
[116](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L116-L116), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L117-L117), [118](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L118-L118), [119](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L119-L119), [120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L120-L120), [123](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L123-L123), [124](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L124-L124), [267](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L267-L267), [644](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L644-L644), 


```solidity
Path: ./contracts/p1/RToken.sol

76:        assetRegistry = main_.assetRegistry();	// @audit-issue

77:        basketHandler = main_.basketHandler();	// @audit-issue

78:        backingManager = main_.backingManager();	// @audit-issue

79:        furnace = main_.furnace();	// @audit-issue

81:        mandate = mandate_;	// @audit-issue

400:        basketsNeeded = basketsNeeded_;	// @audit-issue

458:        issuanceThrottle.params = params;	// @audit-issue

469:        redemptionThrottle.params = params;	// @audit-issue
```
[76](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L76-L76), [77](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L77-L77), [78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L78-L78), [79](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L79-L79), [81](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L81-L81), [400](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L400-L400), [458](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L458-L458), [469](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L469-L469), 


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

94:        sell = req.sell.erc20();	// @audit-issue

95:        buy = req.buy.erc20();	// @audit-issue

96:        sellAmount = shiftl_toFix(req.sellAmount, -int8(sell.decimals()), FLOOR); // {sellTok}	// @audit-issue

103:        broker = broker_;	// @audit-issue

104:        origin = origin_;	// @audit-issue

105:        gnosis = gnosis_;	// @audit-issue

106:        endTime = uint48(block.timestamp) + batchAuctionLength;	// @audit-issue

109:        worstCasePrice = shiftl_toFix(req.minBuyAmount, 9).divu(req.sellAmount, FLOOR);	// @audit-issue
```
[94](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L94-L94), [95](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L95-L95), [96](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L96-L96), [103](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L103-L103), [104](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L104-L104), [105](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L105-L105), [106](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L106-L106), [109](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L109-L109), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

172:        origin = origin_;	// @audit-issue

173:        sell = sell_.erc20();	// @audit-issue

174:        buy = buy_.erc20();	// @audit-issue

182:        endTime = _startTime + auctionLength;	// @audit-issue
```
[172](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L172-L172), [173](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L173-L173), [174](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L174-L174), [182](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L182-L182), 


#### Recommendation

Optimize gas usage by avoiding storage updates in Solidity when the new value is the same as the existing one. This prevents unnecessary gas expenditure from storage resets, balancing the cost against cold or warm storage access as needed.

### Avoid zero transfers calls
In Solidity, unnecessary operations can waste gas. For example, a transfer function without a zero amount check uses gas even if called with a zero amount, since the contract state remains unchanged. Implementing a zero amount check avoids these unnecessary function calls, saving gas and improving efficiency.

```solidity
Path: ./contracts/p1/StRSR.sol

237:        IERC20Upgradeable(address(rsr)).safeTransferFrom(caller, address(this), rsrAmount);	// @audit-issue
```
[237](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L237-L237), 


#### Recommendation

Include a condition in your transfer functions to check for and prevent zero-value transfers. Implement a `require(amount > 0, "Transfer amount must be greater than zero");` statement at the beginning of the function. This preemptive check ensures that the function only proceeds with non-zero transfer amounts, avoiding wasteful operations and saving gas. Apply this optimization across all functions involving token transfers or similar operations to improve your contract's gas efficiency and operational effectiveness.

### Expression `bytes('')` is cheaper than `new bytes(0)`
In Solidity, initializing empty byte arrays can be done using either `bytes('')` or `new bytes(0)`. Contrary to a common belief, `bytes('')` is actually more gas-efficient than `new bytes(0)`. The expression `bytes('')` directly initializes an empty byte array, while `new bytes(0)` creates a new dynamic array and initializes its length to zero, which involves slightly more computation and consequently, a higher gas cost. This difference, though minimal, can be significant in contracts where such initializations are frequent, particularly in scenarios requiring high gas efficiency.

```solidity
Path: ./contracts/p1/Deployer.sol

118:            address(new ERC1967Proxy(address(_implementations.main), new bytes(0)))	// @audit-issue

123:            address(new ERC1967Proxy(address(_implementations.components.rToken), new bytes(0)))	// @audit-issue

127:                address(new ERC1967Proxy(address(_implementations.components.stRSR), new bytes(0)))	// @audit-issue

134:                        new bytes(0)	// @audit-issue

142:                        new bytes(0)	// @audit-issue

150:                        new bytes(0)	// @audit-issue

156:                    new ERC1967Proxy(address(_implementations.components.distributor), new bytes(0))	// @audit-issue

161:                    new ERC1967Proxy(address(_implementations.components.rsrTrader), new bytes(0))	// @audit-issue

168:                        new bytes(0)	// @audit-issue

174:                    new ERC1967Proxy(address(_implementations.components.furnace), new bytes(0))	// @audit-issue

178:                address(new ERC1967Proxy(address(_implementations.components.broker), new bytes(0)))	// @audit-issue
```
[118](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L118-L118), [123](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L123-L123), [127](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L127-L127), [134](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L134-L134), [142](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L142-L142), [150](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L150-L150), [156](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L156-L156), [161](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L161-L161), [168](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L168-L168), [174](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L174-L174), [178](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L178-L178), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

155:            new bytes(0)	// @audit-issue
```
[155](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L155-L155), 


#### Recommendation

Use `new bytes(0)` instead of `bytes("")` when initializing an empty byte array in Solidity. This approach is more gas-efficient as it avoids the overhead associated with string processing and conversion. Review your contracts to identify and replace instances where `bytes("")` is used for this purpose, applying this simple yet effective optimization to enhance contract performance and reduce gas costs.

### Empty Blocks Should Be Removed Or Emit Something
The code should be refactored such that empty blocks no longer exist, or the block should do something useful, such as emitting an event or reverting. Empty blocks can introduce confusion and potential errors when the code is modified in the future.


```solidity
Path: ./contracts/libraries/Allowance.sol

37:        } catch {}	// @audit-issue
```
[37](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L37-L37), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

58:        try this.distributeTokenToBuy() {} catch (bytes memory errData) {	// @audit-issue
```
[58](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L58-L58), 


```solidity
Path: ./contracts/p1/Distributor.sol

63:        try main.rsrTrader().distributeTokenToBuy() {} catch {}	// @audit-issue

65:        try main.rTokenTrader().distributeTokenToBuy() {} catch {}	// @audit-issue

88:        try main.rsrTrader().distributeTokenToBuy() {} catch {}	// @audit-issue

90:        try main.rTokenTrader().distributeTokenToBuy() {} catch {}	// @audit-issue
```
[63](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L63-L63), [65](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L65-L65), [88](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L88-L88), [90](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L90-L90), 


```solidity
Path: ./contracts/p1/BackingManager.sol

92:            try this.rebalance(trade.KIND()) {} catch (bytes memory errData) {	// @audit-issue
```
[92](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L92-L92), 


```solidity
Path: ./contracts/p1/StRSR.sol

740:    function _requireNotTradingPausedOrFrozen() private notTradingPausedOrFrozen {}	// @audit-issue

744:    function _requireNotFrozen() private notFrozen {}	// @audit-issue

748:    function _requireGovernanceOnly() private governance {}	// @audit-issue
```
[740](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L740-L740), [744](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L744-L744), [748](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L748-L748), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

651:    function requireGovernanceOnly() private governance {}	// @audit-issue
```
[651](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L651-L651), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

60:    function requireNotTradingPausedOrFrozen() internal view notTradingPausedOrFrozen {}	// @audit-issue
```
[60](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L60-L60), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

67:    function _authorizeUpgrade(address newImplementation) internal view override onlyMain {}	// @audit-issue
```
[67](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L67-L67), 


#### Recommendation

Remove empty blocks in Solidity code to avoid confusion and potential errors. If a block is necessary, ensure it performs a useful action, like emitting an event or reverting, to justify its presence and clarify its purpose.

### Overridden function has no body
In Solidity, when a function is overridden from a base contract, it's expected to have its own implementation or body. However, there are cases where an overridden function is left with an empty body, either intentionally or due to oversight. An empty body in an overridden function can be misleading and might indicate an incomplete implementation. If the function is intentionally left empty (for instance, in cases of interface adherence or specific design patterns), this should be clearly documented. Otherwise, it should be provided with a proper implementation to ensure the contract's functionality aligns with its intended design and behavior.

```solidity
Path: ./contracts/p1/RevenueTrader.sol

58:        try this.distributeTokenToBuy() {} catch (bytes memory errData) {	// @audit-issue
```
[58](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L58-L58), 


```solidity
Path: ./contracts/p1/BackingManager.sol

92:            try this.rebalance(trade.KIND()) {} catch (bytes memory errData) {	// @audit-issue
```
[92](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L92-L92), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

67:    function _authorizeUpgrade(address newImplementation) internal view override onlyMain {}	// @audit-issue
```
[67](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L67-L67), 


#### Recommendation

Review all overridden functions in your Solidity contracts to ensure they have appropriate implementations. If an overridden function is intentionally left empty, include clear comments explaining the rationale. This approach prevents confusion and ensures that the contract's design and purpose are accurately conveyed. For functions that require an implementation, provide a meaningful body that aligns with the contract's intended functionality and the expectations set by the base contract or interface.

### Functions guaranteed to `revert` when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

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

87:    function grantRole(bytes32 role, address account)	// @audit-issue

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
[68](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L68-L68), [87](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L87-L87), [124](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L124-L124), [139](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L139-L139), [152](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L152-L152), [161](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L161-L161), [169](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L169-L169), [176](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L176-L176), [183](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L183-L183), [190](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L190-L190), [198](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L198-L198), [205](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L205-L205), 


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

111:    function upgradeRTokenTo(	// @audit-issue
```
[61](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L61-L61), [70](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L70-L70), [79](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L79-L79), [99](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L99-L99), [111](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L111-L111), 


```solidity
Path: ./contracts/p1/Broker.sol

186:    function setBatchTradeImplementation(ITrade newTradeImplementation) public onlyMain {	// @audit-issue

208:    function setDutchTradeImplementation(ITrade newTradeImplementation) public onlyMain {	// @audit-issue
```
[186](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L186-L186), [208](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L208-L208), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

48:    function __Trading_init(	// @audit-issue
```
[48](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L48-L48), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

33:    function __Component_init(IMain main_) internal onlyInitializing {	// @audit-issue

67:    function _authorizeUpgrade(address newImplementation) internal view override onlyMain {}	// @audit-issue
```
[33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L33-L33), [67](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L67-L67), 


#### Recommendation

Mark functions with access restrictions like 'onlyOwner' as 'payable' in Solidity. This reduces gas costs for legitimate callers by removing the compiler's checks for incoming payments, as the function will revert for unauthorized users anyway.

### `abi.encode()` is less efficient than `abi.encodePacked()`
When working with Solidity, it is important to pay attention to gas efficiency in order to optimize smart contracts. In this blog post, we will compare the gas costs of `abi.encode()` and `abi.encodepacked()` and explore why the latter is more efficient.Source: [reference](https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison)


```solidity
Path: ./contracts/p1/StRSR.sol

934:            abi.encode(_PERMIT_TYPEHASH, owner, spender, value, _useNonce(owner), deadline)	// @audit-issue
```
[934](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L934-L934), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

176:            _hashTypedDataV4(keccak256(abi.encode(_DELEGATE_TYPEHASH, delegatee, nonce, expiry))),	// @audit-issue
```
[176](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L176-L176), 


#### Recommendation

Optimize gas usage by preferring 'abi.encodePacked()' over 'abi.encode()' where appropriate, as 'abi.encodePacked()' is generally more gas-efficient. However, ensure its suitability based on the data type and structure requirements, as 'abi.encodePacked()' can lead to ambiguity in certain cases.

### Multiple mappings can be replaced with a single struct mapping
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

```solidity
Path: ./contracts/p1/Main.sol

36:        uint48 shortFreeze_,	// @audit-issue: Can be combined with: 
	 longFreezes in Auth at line: 36
```
[36](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L36-L36), 


```solidity
Path: ./contracts/p1/StRSR.sol

85:    mapping(uint256 => mapping(address => uint256)) public firstRemainingDraft; // draft index	// @audit-issue: Can be combined with: 
	 stakes in StRSRP1 at line: 63

128:    mapping(address => CountersUpgradeable.Counter) private _delegationNonces;	// @audit-issue: Can be combined with: 
	 _nonces in StRSRP1 at line: 126
```
[85](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L85-L85), [128](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L128-L128), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

85:	// @audit-issue: Can be combined with: 
	 stakes in StRSRP1 at line: 63

128:        // the index high-1, if not out of bounds (in which case we're looking too far in the past	// @audit-issue: Can be combined with: 
	 _nonces in StRSRP1 at line: 126
```
[85](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L85-L85), [128](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L128-L128), 


#### Recommendation

Refactor your Solidity contracts to replace multiple related mappings with a single mapping that uses a struct. This struct should contain all the fields that were previously in separate mappings. This consolidation optimizes storage usage by reducing the number of storage slots and hash computations required, leading to significant gas savings. Ensure that the fields within the struct are logically related and frequently accessed or modified together. This strategy is most effective when the size of struct fields allows them to fit within a single storage slot, maximizing the benefits of the EVM's storage packing capabilities.

### `x += y` costs more gas than `x = x + y` for stack variables
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```solidity
Path: ./contracts/libraries/Fixed.sol

397:        decimals -= 18; // shift so that toUint happens at the same time.	// @audit-issue

515:            if (rounding == RoundingMode.ROUND) shiftDelta += (FIX_ONE / 2);	// @audit-issue

516:            else if (rounding == RoundingMode.CEIL) shiftDelta += FIX_ONE - 1;	// @audit-issue

575:            if (mm > lo) hi -= 1;	// @audit-issue

576:            lo -= mm;	// @audit-issue

582:            c_256 /= pow2;	// @audit-issue

583:            lo /= pow2;	// @audit-issue

584:            lo += hi * ((0 - pow2) / pow2 + 1);	// @audit-issue

586:            r *= 2 - c_256 * r;	// @audit-issue

587:            r *= 2 - c_256 * r;	// @audit-issue

588:            r *= 2 - c_256 * r;	// @audit-issue

589:            r *= 2 - c_256 * r;	// @audit-issue

590:            r *= 2 - c_256 * r;	// @audit-issue

591:            r *= 2 - c_256 * r;	// @audit-issue

592:            r *= 2 - c_256 * r;	// @audit-issue

593:            r *= 2 - c_256 * r;	// @audit-issue

598:                if (mm != 0) result_256 += 1;	// @audit-issue

600:                if (mm > ((c_256 - 1) / 2)) result_256 += 1;	// @audit-issue
```
[397](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L397-L397), [515](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L515-L515), [516](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L516-L516), [575](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L575-L575), [576](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L576-L576), [582](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L582-L582), [583](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L583-L583), [584](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L584-L584), [586](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L586-L586), [587](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L587-L587), [588](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L588-L588), [589](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L589-L589), [590](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L590-L590), [591](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L591-L591), [592](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L592-L592), [593](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L593-L593), [598](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L598-L598), [600](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L600-L600), 


```solidity
Path: ./contracts/p1/Distributor.sol

153:            paidOutShares += numberOfShares;	// @audit-issue

208:            revTotals.rTokenTotal += share.rTokenDist;	// @audit-issue

209:            revTotals.rsrTotal += share.rsrDist;	// @audit-issue

221:                revTotals.rsrTotal += uint24(	// @audit-issue
```
[153](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L153-L153), [208](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L208-L208), [209](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L209-L209), [221](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L221-L221), 


```solidity
Path: ./contracts/p1/BackingManager.sol

298:            if (reg.erc20s[i] == rsr) ctx.bals[i] += reg.assets[i].bal(address(stRSR));	// @audit-issue
```
[298](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L298-L298), 


```solidity
Path: ./contracts/p1/StRSR.sol

451:            seizedRSR += stakeRSR;	// @audit-issue

458:        seizedRSR += draftRSRToTake;	// @audit-issue

467:            seizedRSR += draftRSR;	// @audit-issue

472:        seizedRSR += (rewards * rsrAmount + (rsrBalance - 1)) / rsrBalance;	// @audit-issue

833:        eraStakes[to] += amount;	// @audit-issue
```
[451](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L451-L451), [458](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L458-L458), [467](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L467-L467), [472](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L472-L472), [833](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L833-L833), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

431:                low256 += qty.safeMul(lowP, FLOOR);	// @audit-issue

446:                    high256 += qty.safeMul(highP, CEIL);	// @audit-issue

584:                    refAmtsAll[erc20Index] += amt;	// @audit-issue
```
[431](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L431-L431), [446](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L446-L446), [584](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L584-L584), 


```solidity
Path: ./contracts/p1/RToken.sol

270:            portionsSum += portions[i];	// @audit-issue
```
[270](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L270-L270), 


#### Recommendation

Prefer using 'x = x + y' over 'x += y' for state variable assignments in Solidity to save gas. The latter incurs extra costs due to additional JUMP instructions and stack operations associated with non-inlined function calls.

### Use calldata instead of memory for function arguments that do not get mutated
Mark data types as `calldata` instead of `memory` where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as `calldata`. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies `memory` storage.

```solidity
Path: ./contracts/mixins/ComponentRegistry.sol

18:    function __ComponentRegistry_init(Components memory components_) internal onlyInitializing {	// @audit-issue
```
[18](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L18-L18), 


```solidity
Path: ./contracts/libraries/Array.sol

9:    function allUnique(IERC20[] memory arr) internal pure returns (bool) {	// @audit-issue

21:    function sortedAndAllUnique(IERC20[] memory arr) internal pure returns (bool) {	// @audit-issue
```
[9](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L9-L9), [21](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Array.sol#L21-L21), 


```solidity
Path: ./contracts/libraries/String.sol

11:    function toLower(string memory str) internal pure returns (string memory) {	// @audit-issue
```
[11](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/String.sol#L11-L11), 


```solidity
Path: ./contracts/p1/Main.sol

33:    function init(
34:        Components memory components,	// @audit-issue
35:        IERC20 rsr_,
36:        uint48 shortFreeze_,
37:        uint48 longFreeze_
38:    ) public virtual initializer {
```
[34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L33-L38), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

76:    function returnTokens(IERC20[] memory erc20s) external notTradingPausedOrFrozen {	// @audit-issue
```
[76](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L76-L76), 


```solidity
Path: ./contracts/p1/Distributor.sol

240:    function _setDistribution(address dest, RevenueShare memory share) internal {	// @audit-issue
```
[240](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L240-L240), 


```solidity
Path: ./contracts/p1/BackingManager.sol

273:    function tradingContext(BasketRange memory basketsHeld)	// @audit-issue
```
[273](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L273-L273), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

227:    function _setPrimeBasket(
228:        IERC20[] calldata erc20s,
229:        uint192[] memory targetAmts,	// @audit-issue
230:        bool disableTargetAmountCheck
231:    ) internal {

521:    function quoteCustomRedemption(
522:        uint48[] memory basketNonces,	// @audit-issue
523:        uint192[] memory portions,
524:        uint192 amount
525:    ) external view returns (address[] memory erc20s, uint256[] memory quantities) {

521:    function quoteCustomRedemption(
522:        uint48[] memory basketNonces,
523:        uint192[] memory portions,	// @audit-issue
524:        uint192 amount
525:    ) external view returns (address[] memory erc20s, uint256[] memory quantities) {
```
[229](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L227-L231), [522](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L521-L525), [523](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L521-L525), 


```solidity
Path: ./contracts/p1/Deployer.sol

42:    constructor(
43:        IERC20Metadata rsr_,
44:        IGnosis gnosis_,
45:        IAsset rsrAsset_,
46:        Implementations memory implementations_	// @audit-issue
47:    ) {

107:    function deploy(
108:        string memory name,	// @audit-issue
109:        string memory symbol,
110:        string calldata mandate,
111:        address owner,
112:        DeploymentParams memory params
113:    ) external returns (address) {

107:    function deploy(
108:        string memory name,
109:        string memory symbol,	// @audit-issue
110:        string calldata mandate,
111:        address owner,
112:        DeploymentParams memory params
113:    ) external returns (address) {

107:    function deploy(
108:        string memory name,
109:        string memory symbol,
110:        string calldata mandate,
111:        address owner,
112:        DeploymentParams memory params	// @audit-issue
113:    ) external returns (address) {
```
[46](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L42-L47), [108](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L107-L113), [109](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L107-L113), [112](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L107-L113), 


```solidity
Path: ./contracts/p1/RToken.sol

253:    function redeemCustom(
254:        address recipient,
255:        uint256 amount,
256:        uint48[] memory basketNonces,	// @audit-issue
257:        uint192[] memory portions,
258:        address[] memory expectedERC20sOut,
259:        uint256[] memory minAmounts
260:    ) external notFrozen {

253:    function redeemCustom(
254:        address recipient,
255:        uint256 amount,
256:        uint48[] memory basketNonces,
257:        uint192[] memory portions,	// @audit-issue
258:        address[] memory expectedERC20sOut,
259:        uint256[] memory minAmounts
260:    ) external notFrozen {

253:    function redeemCustom(
254:        address recipient,
255:        uint256 amount,
256:        uint48[] memory basketNonces,
257:        uint192[] memory portions,
258:        address[] memory expectedERC20sOut,	// @audit-issue
259:        uint256[] memory minAmounts
260:    ) external notFrozen {

253:    function redeemCustom(
254:        address recipient,
255:        uint256 amount,
256:        uint48[] memory basketNonces,
257:        uint192[] memory portions,
258:        address[] memory expectedERC20sOut,
259:        uint256[] memory minAmounts	// @audit-issue
260:    ) external notFrozen {
```
[256](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L253-L260), [257](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L253-L260), [258](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L253-L260), [259](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L253-L260), 


```solidity
Path: ./contracts/p1/Broker.sol

125:    function openTrade(
126:        TradeKind kind,
127:        TradeRequest memory req,	// @audit-issue
128:        TradePrices memory prices
129:    ) external returns (ITrade) {

125:    function openTrade(
126:        TradeKind kind,
127:        TradeRequest memory req,
128:        TradePrices memory prices	// @audit-issue
129:    ) external returns (ITrade) {

243:    function newBatchAuction(TradeRequest memory req, address caller) private returns (ITrade) {	// @audit-issue

268:    function newDutchAuction(
269:        TradeRequest memory req,	// @audit-issue
270:        TradePrices memory prices,
271:        ITrading caller
272:    ) private returns (ITrade) {

268:    function newDutchAuction(
269:        TradeRequest memory req,
270:        TradePrices memory prices,	// @audit-issue
271:        ITrading caller
272:    ) private returns (ITrade) {
```
[127](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L125-L129), [128](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L125-L129), [243](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L243-L243), [269](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L268-L272), [270](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L268-L272), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

120:    function tryTrade(
121:        TradeKind kind,
122:        TradeRequest memory req,	// @audit-issue
123:        TradePrices memory prices
124:    ) internal returns (ITrade trade) {

120:    function tryTrade(
121:        TradeKind kind,
122:        TradeRequest memory req,
123:        TradePrices memory prices	// @audit-issue
124:    ) internal returns (ITrade trade) {
```
[122](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L120-L124), [123](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L120-L124), 


```solidity
Path: ./contracts/p1/mixins/TradeLib.sol

42:    function prepareTradeSell(
43:        TradeInfo memory trade,	// @audit-issue
44:        uint192 minTradeVolume,
45:        uint192 maxTradeSlippage
46:    ) internal view returns (bool notDust, TradeRequest memory req) {

118:    function prepareTradeToCoverDeficit(
119:        TradeInfo memory trade,	// @audit-issue
120:        uint192 minTradeVolume,
121:        uint192 maxTradeSlippage
122:    ) internal view returns (bool notDust, TradeRequest memory req) {
```
[43](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L42-L46), [119](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L118-L122), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

33:    function prepareRecollateralizationTrade(TradingContext memory ctx, Registry memory reg)	// @audit-issue

108:    function basketRange(TradingContext memory ctx, Registry memory reg)	// @audit-issue

274:    function nextTradePair(
275:        TradingContext memory ctx,	// @audit-issue
276:        Registry memory reg,
277:        BasketRange memory range
278:    ) private view returns (TradeInfo memory trade) {

274:    function nextTradePair(
275:        TradingContext memory ctx,
276:        Registry memory reg,	// @audit-issue
277:        BasketRange memory range
278:    ) private view returns (TradeInfo memory trade) {

274:    function nextTradePair(
275:        TradingContext memory ctx,
276:        Registry memory reg,
277:        BasketRange memory range	// @audit-issue
278:    ) private view returns (TradeInfo memory trade) {

381:    function isBetterSurplus(
382:        MaxSurplusDeficit memory curr,	// @audit-issue
383:        CollateralStatus other,
384:        uint192 surplusAmt
385:    ) private pure returns (bool) {
```
[33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L33-L33), [108](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L108-L108), [275](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L274-L278), [276](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L274-L278), [277](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L274-L278), [382](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L381-L385), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

156:    function init(
157:        ITrading origin_,
158:        IAsset sell_,
159:        IAsset buy_,
160:        uint256 sellAmount_,
161:        uint48 auctionLength,
162:        TradePrices memory prices	// @audit-issue
163:    ) external stateTransition(TradeStatus.NOT_STARTED, TradeStatus.OPEN) {
```
[162](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L156-L163), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

104:    function propose(
105:        address[] memory targets,	// @audit-issue
106:        uint256[] memory values,
107:        bytes[] memory calldatas,
108:        string memory description
109:    ) public override(Governor, IGovernor) returns (uint256 proposalId) {

104:    function propose(
105:        address[] memory targets,
106:        uint256[] memory values,	// @audit-issue
107:        bytes[] memory calldatas,
108:        string memory description
109:    ) public override(Governor, IGovernor) returns (uint256 proposalId) {

104:    function propose(
105:        address[] memory targets,
106:        uint256[] memory values,
107:        bytes[] memory calldatas,	// @audit-issue
108:        string memory description
109:    ) public override(Governor, IGovernor) returns (uint256 proposalId) {

104:    function propose(
105:        address[] memory targets,
106:        uint256[] memory values,
107:        bytes[] memory calldatas,
108:        string memory description	// @audit-issue
109:    ) public override(Governor, IGovernor) returns (uint256 proposalId) {

114:    function queue(
115:        address[] memory targets,	// @audit-issue
116:        uint256[] memory values,
117:        bytes[] memory calldatas,
118:        bytes32 descriptionHash
119:    ) public override returns (uint256 proposalId) {

114:    function queue(
115:        address[] memory targets,
116:        uint256[] memory values,	// @audit-issue
117:        bytes[] memory calldatas,
118:        bytes32 descriptionHash
119:    ) public override returns (uint256 proposalId) {

114:    function queue(
115:        address[] memory targets,
116:        uint256[] memory values,
117:        bytes[] memory calldatas,	// @audit-issue
118:        bytes32 descriptionHash
119:    ) public override returns (uint256 proposalId) {

124:    function cancel(
125:        address[] memory targets,	// @audit-issue
126:        uint256[] memory values,
127:        bytes[] memory calldatas,
128:        bytes32 descriptionHash
129:    ) public override(Governor, IGovernor) returns (uint256) {

124:    function cancel(
125:        address[] memory targets,
126:        uint256[] memory values,	// @audit-issue
127:        bytes[] memory calldatas,
128:        bytes32 descriptionHash
129:    ) public override(Governor, IGovernor) returns (uint256) {

124:    function cancel(
125:        address[] memory targets,
126:        uint256[] memory values,
127:        bytes[] memory calldatas,	// @audit-issue
128:        bytes32 descriptionHash
129:    ) public override(Governor, IGovernor) returns (uint256) {

136:    function _execute(
137:        uint256 proposalId,
138:        address[] memory targets,	// @audit-issue
139:        uint256[] memory values,
140:        bytes[] memory calldatas,
141:        bytes32 descriptionHash
142:    ) internal override(Governor, GovernorTimelockControl) {

136:    function _execute(
137:        uint256 proposalId,
138:        address[] memory targets,
139:        uint256[] memory values,	// @audit-issue
140:        bytes[] memory calldatas,
141:        bytes32 descriptionHash
142:    ) internal override(Governor, GovernorTimelockControl) {

136:    function _execute(
137:        uint256 proposalId,
138:        address[] memory targets,
139:        uint256[] memory values,
140:        bytes[] memory calldatas,	// @audit-issue
141:        bytes32 descriptionHash
142:    ) internal override(Governor, GovernorTimelockControl) {

147:    function _cancel(
148:        address[] memory targets,	// @audit-issue
149:        uint256[] memory values,
150:        bytes[] memory calldatas,
151:        bytes32 descriptionHash
152:    ) internal override(Governor, GovernorTimelockControl) returns (uint256) {

147:    function _cancel(
148:        address[] memory targets,
149:        uint256[] memory values,	// @audit-issue
150:        bytes[] memory calldatas,
151:        bytes32 descriptionHash
152:    ) internal override(Governor, GovernorTimelockControl) returns (uint256) {

147:    function _cancel(
148:        address[] memory targets,
149:        uint256[] memory values,
150:        bytes[] memory calldatas,	// @audit-issue
151:        bytes32 descriptionHash
152:    ) internal override(Governor, GovernorTimelockControl) returns (uint256) {

166:    function _getVotes(
167:        address account,
168:        uint256 timepoint,
169:        bytes memory /*params*/	// @audit-issue
170:    ) internal view override(Governor, GovernorVotes) returns (uint256) {
```
[105](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L104-L109), [106](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L104-L109), [107](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L104-L109), [108](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L104-L109), [115](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L114-L119), [116](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L114-L119), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L114-L119), [125](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L124-L129), [126](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L124-L129), [127](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L124-L129), [138](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L136-L142), [139](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L136-L142), [140](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L136-L142), [148](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L147-L152), [149](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L147-L152), [150](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L147-L152), [169](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L166-L170), 


#### Recommendation

To optimize gas usage in your Solidity functions, mark data types as `calldata` instead of `memory` wherever applicable. This prevents unnecessary data loading into memory. Use `calldata` for function arguments that do not require changes within the function, except when passing them into another function that explicitly requires `memory` storage.

### Splitting `require()` statements that use `&&` saves gas
Instead of using the `&&` operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 3 GAS per `&&`:

```solidity
Path: ./contracts/mixins/Auth.sol

199:        require(shortFreeze_ != 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");	// @audit-issue

206:        require(longFreeze_ != 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");	// @audit-issue
```
[199](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L199-L199), [206](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L206-L206), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

150:        require(buyHigh != 0 && buyHigh != FIX_MAX, "buy asset price unknown");	// @audit-issue
```
[150](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L150-L150), 


```solidity
Path: ./contracts/p1/Distributor.sol

242:        require(	// @audit-issue
243:            dest != address(furnace) && dest != address(stRSR),
244:            "destination can not be furnace or strsr directly"
245:        );
```
[242](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L242-L245), 


```solidity
Path: ./contracts/p1/StRSR.sol

341:        require(basketHandler.isReady() && basketHandler.fullyCollateralized(), "RToken readying");	// @audit-issue

972:        require(val > MIN_UNSTAKING_DELAY && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");	// @audit-issue
```
[341](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L341-L341), [972](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L972-L972), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

233:        require(erc20s.length != 0 && erc20s.length == targetAmts.length, "invalid lengths");	// @audit-issue

264:            require(0 < targetAmts[i] && targetAmts[i] <= MAX_TARGET_AMT, "invalid target amount");	// @audit-issue

290:        require(max <= MAX_BACKUP_ERC20S && erc20s.length <= MAX_BACKUP_ERC20S, "too large");	// @audit-issue

536:            require(	// @audit-issue
537:                basketNonces[i] >= lastCollateralized && basketNonces[i] <= nonce,
538:                "invalid basketNonce"
539:            );

635:        require(val >= MIN_WARMUP_PERIOD && val <= MAX_WARMUP_PERIOD, "invalid warmupPeriod");	// @audit-issue

685:            require(	// @audit-issue
686:                erc20s[i] != rsr &&
687:                    erc20s[i] != IERC20(address(rToken)) &&
688:                    erc20s[i] != IERC20(address(stRSR)) &&
689:                    erc20s[i] != IERC20(address(0)),
690:                "invalid collateral"
691:            );
```
[233](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L233-L233), [264](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L264-L264), [290](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L290-L290), [536](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L536-L539), [635](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L635-L635), [685](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L685-L691), 


```solidity
Path: ./contracts/p1/Deployer.sol

48:        require(	// @audit-issue
49:            address(rsr_) != address(0) &&
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
65:            "invalid address"
66:        );

114:        require(owner != address(0) && owner != address(this), "invalid owner");	// @audit-issue
```
[48](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L48-L66), [114](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L114-L114), 


```solidity
Path: ./contracts/p1/RToken.sol

413:        require(low >= MIN_EXCHANGE_RATE && high <= MAX_EXCHANGE_RATE, "BU rate out of range");	// @audit-issue
```
[413](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L413-L413), 


```solidity
Path: ./contracts/p1/Broker.sol

273:        require(	// @audit-issue
274:            !dutchTradeDisabled[req.sell.erc20()] && !dutchTradeDisabled[req.buy.erc20()],
275:            "dutch auctions disabled for token pair"
276:        );

278:        require(	// @audit-issue
279:            pricedAtTimestamp(req.sell) && pricedAtTimestamp(req.buy),
280:            "dutch auctions require live prices"
281:        );
```
[273](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L273-L276), [278](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L278-L281), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

117:        require(buPriceLow != 0 && buPriceHigh != FIX_MAX, "BUs unpriced");	// @audit-issue
```
[117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L117-L117), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

340:            require(contains && amt >= targetAmts[i], "new target weights");	// @audit-issue

368:            require(low != 0 && high != FIX_MAX, "invalid price");	// @audit-issue
```
[340](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L340-L340), [368](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L368-L368), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

168:        require(prices.sellLow != 0 && prices.sellHigh < FIX_MAX / 1000, "bad sell pricing");	// @audit-issue

169:        require(prices.buyLow != 0 && prices.buyHigh < FIX_MAX / 1000, "bad buy pricing");	// @audit-issue
```
[168](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L168-L168), [169](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L169-L169), 


#### Recommendation

Split 'require()' statements in Solidity that use '&&' into multiple 'require()' statements, each with a single condition. This approach saves 3 gas per '&&', optimizing overall gas usage in the contract.

### Nesting `if` statements that uses `&&` saves gas
In Solidity, the way conditional checks are structured can impact the gas consumption of a transaction. When conditions are combined using `&&` within an `if` statement, Solidity short-circuits the evaluation, meaning that if the first condition is `false`, the subsequent conditions won't be evaluated. This behavior can lead to gas savings compared to using separate nested `if` statements because not all conditions might need to be checked every time. By efficiently structuring these conditional checks, contracts can optimize the gas required for execution, leading to reduced costs for users.


```solidity
Path: ./contracts/libraries/Throttle.sol

43:        if (throttle.params.amtRate == 0 && throttle.params.pctRate == 0) return;	// @audit-issue
```
[43](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L43-L43), 


```solidity
Path: ./contracts/libraries/String.sol

16:            if ((uint8(bStr[i]) >= 65) && (uint8(bStr[i]) <= 90)) {	// @audit-issue
```
[16](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/String.sol#L16-L16), 


```solidity
Path: ./contracts/p1/Distributor.sol

220:            if (feeRecipient != address(0) && feeNumerator != 0) {	// @audit-issue

252:        if (share.rsrDist == 0 && share.rTokenDist == 0) {	// @audit-issue
```
[220](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L220-L220), [252](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L252-L252), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

187:        if (reweightable && nonce > lastCollateralized && fullyCollateralized()) {	// @audit-issue

237:            (!reweightable || (reweightable && !disableTargetAmountCheck)) &&	// @audit-issue
238:            config.erc20s.length != 0
```
[187](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L187-L187), [237](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L237-L238), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

192:        if (delegatee == address(0) && currentDelegate == address(0)) {	// @audit-issue

195:        } else if (delegatee != address(0) && currentDelegate != delegatee) {	// @audit-issue

235:        if (src != dst && amount != 0) {	// @audit-issue

270:            if (pos != 0 && oldCkpt.fromTimepoint == clock()) {	// @audit-issue
```
[192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L192-L192), [195](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L195-L195), [235](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L235-L235), [270](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L270-L270), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

58:            trade.prices.sellLow == 0 ||	// @audit-issue
59:            (trade.sell.isCollateral() &&
60:                ICollateral(address(trade.sell)).status() != CollateralStatus.SOUND)

148:                ctx.quantities[i] == 0 &&	// @audit-issue
149:                !TradeLib.isEnoughToSell(reg.assets[i], ctx.bals[i], low, ctx.minTradeVolume)

316:                    isBetterSurplus(maxes, status, delta) &&	// @audit-issue
317:                    TradeLib.isEnoughToSell(
318:                        reg.assets[i],
319:                        ctx.bals[i].minus(needed),
320:                        low,
321:                        ctx.minTradeVolume
322:                    )

357:        if (address(trade.sell) == address(0) && address(trade.buy) != address(0)) {	// @audit-issue

362:                high != 0 &&	// @audit-issue
363:                TradeLib.isEnoughToSell(
364:                    reg.assets[rsrIndex],
365:                    ctx.bals[rsrIndex],
366:                    low,
367:                    ctx.minTradeVolume
368:                )
```
[58](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L58-L60), [148](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L148-L149), [316](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L316-L322), [357](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L357-L357), [362](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L362-L368), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

214:                goodCollateral(config.targetNames[_erc20], _erc20, assetRegistry) &&	// @audit-issue
215:                targetWeight.gt(FIX_ZERO)
```
[214](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L214-L215), 


#### Recommendation


When multiple conditions need to be checked successively, try to combine them in a single `if` statement using `&&` instead of nesting separate `if` statements. This will leverage short-circuit evaluation for potential gas savings.


### Use `!= 0` Instead of `> 0` for Unsigned Integer Comparison
In Solidity, unsigned integers (e.g., `uint256`, `uint8`, etc.) represent non-negative whole numbers, ranging from 0 to a maximum value determined by their bit size. When performing comparisons on these numbers, especially to check if they are non-zero, developers have options. A common practice is to compare against zero using the `>` operator, as in `value > 0`. However, given the nature of unsigned integers, a more straightforward and slightly gas-efficient comparison is to use the `!=` operator, as in `value != 0`.

The primary rationale is that the `!=` comparison directly checks for non-equality, whereas the `>` comparison checks if one value is strictly greater than another. For unsigned integers, where negative values don't exist, the `!= 0` check is both semantically clearer and potentially more efficient at the EVM bytecode level.


```solidity
Path: ./contracts/libraries/Throttle.sol

57:        if (amount > 0) {	// @audit-issue
```
[57](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L57-L57), 


#### Recommendation

Use '!= 0' instead of '> 0' for comparing unsigned integers in Solidity. This is semantically clearer and slightly more gas-efficient, as it directly checks for non-zero values without considering unnecessary relational comparisons.

### Operator `>=`/`<=` costs less gas than operator `>`/`<`
The compiler uses opcodes `GT` and `ISZERO` for code that uses `>`, but only requires `LT` for `>=`. A similar behaviour applies for `>`, which uses opcodes `LT` and `ISZERO`, but only requires `GT` for `<=`.


```solidity
Path: ./contracts/p1/StRSRVotes.sol

139:        if (length > 5) {	// @audit-issue
```
[139](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L139-L139), 


```solidity
Path: ./contracts/p1/mixins/TradeLib.sol

68:            require(maxSell > 1, "trade sizing error");	// @audit-issue

166:            amt.shiftl_toUint(int8(asset.erc20Decimals())) > 1;	// @audit-issue
```
[68](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L68-L68), [166](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L166-L166), 


#### Recommendation

Opt for '>=', '<=' operators over '>' and '<' in Solidity where logically appropriate, as they consume less gas. This is because '>=' and '<=' require only one opcode ('LT' or 'GT'), compared to the two opcodes ('GT'/'LT' and 'ISZERO') needed for '>' and '<'.

### Constructor Can Be Marked As Payable
`payable` functions cost less gas to execute, since the compiler does not have to add extra checks to ensure that a payment wasn't provided.

A `constructor` can safely be marked as `payable`, since only the deployer would be able to pass funds, and the project itself would not pass any funds.



```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

35:    constructor(RoleRegistry _roleRegistry, address _feeRecipient) {	// @audit-issue
```
[35](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L35-L35), 


```solidity
Path: ./contracts/registry/VersionRegistry.sol

27:    constructor(RoleRegistry _roleRegistry) {	// @audit-issue
```
[27](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L27-L27), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

26:    constructor(address _versionRegistry) {	// @audit-issue
```
[26](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L26-L26), 


```solidity
Path: ./contracts/registry/RoleRegistry.sol

14:    constructor() {	// @audit-issue
```
[14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/RoleRegistry.sol#L14-L14), 


```solidity
Path: ./contracts/p1/Main.sol

30:    constructor() initializer {}	// @audit-issue
```
[30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L30-L30), 


```solidity
Path: ./contracts/p1/Deployer.sol

42:    constructor(	// @audit-issue
```
[42](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L42-L42), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

25:    constructor() initializer {}	// @audit-issue
```
[25](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L25-L25), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

66:    constructor() {	// @audit-issue
```
[66](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L66-L66), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

145:    constructor() {	// @audit-issue
```
[145](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L145-L145), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

38:    constructor(	// @audit-issue
```
[38](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L38-L38), 


#### Recommendation

Mark constructors as 'payable' in Solidity contracts to reduce gas costs, as this eliminates the need for the compiler to add checks against incoming payments. This is safe because only the deployer can send funds during contract creation, and typically no funds are sent at this stage.

### Initializers Can Be Marked As Payable
`payable` functions cost less gas to execute, since the compiler does not have to add extra checks to ensure that a payment wasn't provided.

A `initializer` can safely be marked as `payable`, since only the deployer would be able to pass funds, and the project itself would not pass any funds.



```solidity
Path: ./contracts/p1/Main.sol

33:    function init(	// @audit-issue
```
[33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L33-L33), 


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
Path: ./contracts/p1/Deployer.sol

107:    function deploy(	// @audit-issue
```
[107](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L107-L107), 


```solidity
Path: ./contracts/p1/RToken.sol

61:    function init(	// @audit-issue
```
[61](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L61-L61), 


```solidity
Path: ./contracts/p1/Broker.sol

73:    function init(	// @audit-issue
```
[73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L73-L73), 


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

Mark initializers as 'payable' in Solidity contracts to reduce gas costs, as this eliminates the need for the compiler to add checks against incoming payments. This is safe because only the deployer can send funds during contract creation, and typically no funds are sent at this stage.

### Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92).


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
Path: ./contracts/mixins/Versioned.sol

13:abstract contract Versioned is IVersioned {	// @audit-issue
```
[13](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Versioned.sol#L13-L13), 


```solidity
Path: ./contracts/mixins/Auth.sol

16:abstract contract Auth is AccessControlUpgradeable, IAuth {	// @audit-issue
```
[16](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L16-L16), 


```solidity
Path: ./contracts/mixins/ComponentRegistry.sol

12:abstract contract ComponentRegistry is Initializable, Auth, IComponentRegistry {	// @audit-issue
```
[12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L12-L12), 


```solidity
Path: ./contracts/libraries/Allowance.sol

4:interface IERC20ApproveOnly {	// @audit-issue
```
[4](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L4-L4), 


```solidity
Path: ./contracts/p1/Main.sol

22:contract MainP1 is Versioned, Initializable, Auth, ComponentRegistry, UUPSUpgradeable, IMain {	// @audit-issue
```
[22](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L22-L22), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

14:contract RevenueTraderP1 is TradingP1, IRevenueTrader {	// @audit-issue
```
[14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L14-L14), 


```solidity
Path: ./contracts/p1/Distributor.sol

12:contract DistributorP1 is ComponentP1, IDistributor {	// @audit-issue
```
[12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L12-L12), 


```solidity
Path: ./contracts/p1/Furnace.sol

12:contract FurnaceP1 is ComponentP1, IFurnace {	// @audit-issue
```
[12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L12-L12), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

12:contract AssetRegistryP1 is ComponentP1, IAssetRegistry {	// @audit-issue
```
[12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L12-L12), 


```solidity
Path: ./contracts/p1/BackingManager.sol

20:contract BackingManagerP1 is TradingP1, IBackingManager {	// @audit-issue
```
[20](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L20-L20), 


```solidity
Path: ./contracts/p1/StRSR.sol

34:abstract contract StRSRP1 is Initializable, ComponentP1, IStRSR, EIP712Upgradeable {	// @audit-issue
```
[34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L34-L34), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

24:contract BasketHandlerP1 is ComponentP1, IBasketHandler {	// @audit-issue
```
[24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L24-L24), 


```solidity
Path: ./contracts/p1/Deployer.sol

28:contract DeployerP1 is IDeployer, Versioned {	// @audit-issue
```
[28](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L28-L28), 


```solidity
Path: ./contracts/p1/RToken.sol

17:contract RTokenP1 is ComponentP1, ERC20PermitUpgradeable, IRToken {	// @audit-issue
```
[17](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L17-L17), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

15:contract StRSRP1Votes is StRSRP1, IERC5805Upgradeable, IStRSRVotes {	// @audit-issue
```
[15](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L15-L15), 


```solidity
Path: ./contracts/p1/Broker.sol

19:contract BrokerP1 is ComponentP1, IBroker {	// @audit-issue
```
[19](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L19-L19), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

19:abstract contract TradingP1 is Multicall, ComponentP1, ReentrancyGuardUpgradeable, ITrading {	// @audit-issue
```
[19](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L19-L19), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

14:abstract contract ComponentP1 is	// @audit-issue
```
[14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L14-L14), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

19:library RecollateralizationLibP1 {	// @audit-issue
```
[19](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L19-L19), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

58:library BasketLibP1 {	// @audit-issue
```
[58](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L58-L58), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

19:contract GnosisTrade is ITrade, Versioned {	// @audit-issue
```
[19](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L19-L19), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

12:interface IDutchTradeCallee {	// @audit-issue

91:contract DutchTrade is ITrade, Versioned {	// @audit-issue
```
[12](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L12-L12), [91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L91-L91), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

24:contract Governance is	// @audit-issue
```
[24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L24-L24), 


#### Recommendation

Optimize gas usage by renaming 'public'/'external' functions and 'public' member variables in Solidity. Aim for shorter and more efficient names, especially for frequently called functions. This can save gas during deployment and reduce gas costs per call due to lower method ID sorting positions.

### Use `uint256(1)`/`uint256(2)` instead of `true`/`false` to save gas for changes
Avoids a Gsset (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past. Since most of the bools aren't changed twice in one transaction, I've counted the amount of gas as half of the full amount, for each variable. Note that public state variables can be re-written to be `private` and use `uint256`, but have public getters returning `bool`s.

```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

16:    mapping(address => bool) private rTokenFeeSet;	// @audit-issue

26:    event RTokenFeeNumeratorSet(address indexed rToken, uint256 feeNumerator, bool isActive);	// @audit-issue
```
[16](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L16-L16), [26](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L26-L26), 


```solidity
Path: ./contracts/registry/VersionRegistry.sol

14:    mapping(bytes32 => bool) public isDeprecated;	// @audit-issue
```
[14](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L14-L14), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

16:    mapping(bytes32 => mapping(address => bool)) private _isValidAsset;	// @audit-issue

17:    mapping(address => bool) public isDeprecated;	// @audit-issue

24:    event AssetPluginRegistryUpdated(bytes32 versionHash, address asset, bool validity);	// @audit-issue
```
[16](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L16-L16), [17](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L17-L17), [24](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L24-L24), 


```solidity
Path: ./contracts/mixins/Auth.sol

45:    bool public tradingPaused;	// @audit-issue

46:    bool public issuancePaused;	// @audit-issue
```
[45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L45-L45), [46](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L46-L46), 


```solidity
Path: ./contracts/p1/Distributor.sol

142:        bool accountRewards = false;	// @audit-issue
```
[142](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L142-L142), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

56:    bool private disabled;	// @audit-issue

89:    bool public reweightable; // immutable after init	// @audit-issue

96:    bool public enableIssuancePremium;	// @audit-issue
```
[56](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L56-L56), [89](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L89-L89), [96](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L96-L96), 


```solidity
Path: ./contracts/p1/RToken.sol

320:            bool allZero = true;	// @audit-issue
```
[320](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L320-L320), 


```solidity
Path: ./contracts/p1/Broker.sol

49:    bool public batchTradeDisabled;	// @audit-issue

52:    mapping(address => bool) private trades;	// @audit-issue

63:    mapping(IERC20Metadata => bool) public dutchTradeDisabled;	// @audit-issue
```
[49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L49-L49), [52](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L52-L52), [63](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L63-L63), 


#### Recommendation

To minimize gas overhead in your Solidity contracts, consider using `uint256(1)` and `uint256(2)` to represent `true` and `false`, respectively, instead of `bool` types for storage. This approach avoids additional `SLOAD` and `SSTORE` operations, resulting in more gas-efficient code.

### Consider activating `via-ir` for deploying
The IR-based code generator was developed to make code generation more performant by enabling optimization passes that can be applied across functions.

It is possible to activate the IR-based code generator through the command line by using the flag `--via-ir`or by including the option `{"viaIR": true}`.

Keep in mind that compiling with this option may take longer. However, you can simply test it before deploying your code. If you find that it provides better performance, you can add the `--via-ir` flag to your deploy command.
```solidity
/// @audit Global finding.
```
[1](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L1), 


#### Recommendation

Consider activating `via-ir`.

### Optimize Deployment Size by Fine-tuning IPFS Hash
The Solidity compiler appends 53 bytes of metadata to the smart contract code, incurring an extra cost of 10,600 gas. This additional expense arises from 200 gas per bytecode, plus calldata cost, which amounts to 16 gas for non-zero bytes and 4 gas for zero bytes. This results in a maximum of 848 extra gas in calldata cost.

Reducing this cost is crucial for the following reasons:

The metadata's 53-byte addition leads to a deployment cost increase of 10,600 gas. It can also result in an additional calldata cost of up to 848 gas. Ways to Minimize Gas Consumption:

Employ the `--no-cbor-metadata` compiler option to exclude metadata. Be cautious as this might impact contract verification. Search for code comments that yield an IPFS hash with more zeros, thereby reducing calldata costs.

```solidity
/// @audit Global finding.
```
[1](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L1), 


#### Recommendation

To optimize deployment size and reduce associated costs, consider the following strategies:
1. **Exclude Metadata with Compiler Option**: Use the `solc` compiler’s `--metadata-hash none` or `--no-cbor-metadata` option to prevent the inclusion of metadata in the compiled bytecode. This action reduces the bytecode size, thus lowering deployment gas costs. However, exercise caution with this approach, as it might affect the ability to verify the contract on platforms like Etherscan.

2. **Optimize IPFS Hash for More Zeros**: If excluding metadata is not desirable, another approach involves optimizing code comments or elements that influence the metadata hash generation to achieve an IPFS hash with a higher proportion of zeros. Since calldata costs are lower for zero bytes, a metadata hash with more zeros can reduce the calldata costs associated with contract interactions.

Example for excluding metadata:
```bash
solc --metadata-hash none YourContract.sol
```


### Low level `call` can be optimized with assembly
`returnData` is copied to memory even if the variable is not utilized: the proper way to handle this is through a low level assembly call.
```solidity
// before
(bool success,) = payable(receiver).call{gas: gas, value: value}("");

//after
bool success;
assembly {
    success := call(gas, receiver, value, 0, 0, 0, 0)
}
```


```solidity
Path: ./contracts/p1/Main.sol

158:        (bool success, ) = proxy.call(	// @audit-issue
```
[158](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L158-L158), 


#### Recommendation

Optimize low-level 'call' operations in Solidity using assembly, especially when 'returnData' is not needed. This avoids unnecessary copying to memory and can lead to gas savings. For example, replace '(bool success,) = payable(receiver).call{gas: gas, value: value}("");' with an assembly block: 'assembly { success := call(gas, receiver, value, 0, 0, 0, 0) }'. This ensures a more efficient execution.

### Assembly: Use scratch space for building calldata
If an external call's calldata can fit into two or fewer words, use the scratch space to build the calldata, rather than allowing Solidity to do a memory expansion.

```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

29:        if (!roleRegistry.isOwner(msg.sender)) {	// @audit-issue
```
[29](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L29-L29), 


```solidity
Path: ./contracts/registry/VersionRegistry.sol

38:        if (!roleRegistry.isOwner(msg.sender)) {	// @audit-issue

46:        string memory version = deployer.version();	// @audit-issue

60:        if (!roleRegistry.isOwnerOrEmergencyCouncil(msg.sender)) {	// @audit-issue

84:        version = deployer.version();	// @audit-issue

93:        return deployments[versionHash].implementations();	// @audit-issue
```
[38](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L38-L38), [46](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L46-L46), [60](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L60-L60), [84](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L84-L84), [93](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L93-L93), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

28:        roleRegistry = versionRegistry.roleRegistry();	// @audit-issue

32:        if (!roleRegistry.isOwner(msg.sender)) {	// @audit-issue

41:            if (address(versionRegistry.deployments(versionHash)) == address(0)) {	// @audit-issue

56:        if (!roleRegistry.isOwner(msg.sender)) {	// @audit-issue

69:            if (address(versionRegistry.deployments(versionHash)) == address(0)) {	// @audit-issue

84:        if (!roleRegistry.isOwner(msg.sender)) {	// @audit-issue

91:        if (address(versionRegistry.deployments(_versionHash)) == address(0)) {	// @audit-issue

108:        if (!roleRegistry.isOwnerOrEmergencyCouncil(msg.sender)) {	// @audit-issue
```
[28](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L28-L28), [32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L32-L32), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L41-L41), [56](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L56-L56), [69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L69-L69), [84](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L84-L84), [91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L91-L91), [108](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L108-L108), 


```solidity
Path: ./contracts/libraries/Allowance.sol

25:        token.approve(spender, 0);	// @audit-issue

28:        require(token.allowance(address(this), spender) == 0, "allowance not 0");	// @audit-issue

34:        try token.approve(spender, value) {	// @audit-issue

35:            success = token.allowance(address(this), spender) == value;	// @audit-issue

41:            token.approve(spender, type(uint256).max);	// @audit-issue

44:            require(token.allowance(address(this), spender) >= value, "allowance missing");	// @audit-issue
```
[25](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L25-L25), [28](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L28-L28), [34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L34-L34), [35](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L35-L35), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L41-L41), [44](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L44-L44), 


```solidity
Path: ./contracts/libraries/Permit.sol

17:        if (AddressUpgradeable.isContract(owner)) {	// @audit-issue

19:                IERC1271Upgradeable(owner).isValidSignature(hash, abi.encodePacked(r, s, v)) ==	// @audit-issue
```
[17](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Permit.sol#L17-L17), [19](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Permit.sol#L19-L19), 


```solidity
Path: ./contracts/p1/Main.sol

53:        assetRegistry.refresh(); // runs furnace.melt()	// @audit-issue

56:        stRSR.payoutRewards();	// @audit-issue

92:        return super.hasRole(role, account);	// @audit-issue

101:        require(!versionRegistry.isDeprecated(versionHash), "version deprecated");	// @audit-issue

103:        Implementations memory implementation = versionRegistry.getImplementationForVersion(	// @audit-issue

108:        require(keccak256(abi.encodePacked(this.version())) == versionHash, "upgrade mismatch");	// @audit-issue

117:        require(keccak256(abi.encodePacked(this.version())) == versionHash, "upgrade main first");	// @audit-issue

119:        Implementations memory implementation = versionRegistry.getImplementationForVersion(	// @audit-issue

125:            assetRegistry.validateCurrentAssets();	// @audit-issue

141:            assetRegistry.validateCurrentAssets();	// @audit-issue

144:        IExtendedBroker(address(broker)).setBatchTradeImplementation(	// @audit-issue

147:        IExtendedBroker(address(broker)).setDutchTradeImplementation(	// @audit-issue

158:        (bool success, ) = proxy.call(	// @audit-issue
```
[53](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L53-L53), [56](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L56-L56), [92](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L92-L92), [101](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L101-L101), [103](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L103-L103), [108](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L108-L108), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L117-L117), [119](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L119-L119), [125](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L125-L125), [141](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L141-L141), [144](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L144-L144), [147](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L147-L147), [158](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L158-L158), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

42:        assetRegistry = main.assetRegistry();	// @audit-issue

43:        distributor = main.distributor();	// @audit-issue

44:        backingManager = main.backingManager();	// @audit-issue

45:        furnace = main.furnace();	// @audit-issue

46:        rToken = main.rToken();	// @audit-issue

47:        rsr = main.rsr();	// @audit-issue

55:        trade = super.settleTrade(sell); // nonReentrant	// @audit-issue

58:        try this.distributeTokenToBuy() {} catch (bytes memory errData) {	// @audit-issue

77:        RevenueTotals memory revTotals = distributor.totals();	// @audit-issue

90:            require(assetRegistry.isRegistered(erc20s[i]), "unregistered erc20");	// @audit-issue

91:            erc20s[i].safeTransfer(address(backingManager), erc20s[i].balanceOf(address(this)));	// @audit-issue

117:        RevenueTotals memory revTotals = distributor.totals();	// @audit-issue

136:        IAsset assetToBuy = assetRegistry.toAsset(tokenToBuy);	// @audit-issue

139:        if (involvesRToken) assetRegistry.refresh();	// @audit-issue

143:                assetRegistry.toAsset(erc20s[i]).refresh();	// @audit-issue

145:            assetToBuy.refresh(); // invariant: can never be the RTokenAsset	// @audit-issue

149:        (uint192 buyLow, uint192 buyHigh) = assetToBuy.price(); // {UoA/tok}	// @audit-issue

158:            require(erc20.balanceOf(address(this)) != 0, "0 balance");	// @audit-issue

160:            IAsset assetToSell = assetRegistry.toAsset(erc20);	// @audit-issue

161:            (uint192 sellLow, uint192 sellHigh) = assetToSell.price(); // {UoA/tok}	// @audit-issue

166:                sellAmount: assetToSell.bal(address(this)),	// @audit-issue

190:        uint256 bal = tokenToBuy.balanceOf(address(this));	// @audit-issue

191:        tokenToBuy.safeApprove(address(distributor), 0);	// @audit-issue

192:        tokenToBuy.safeApprove(address(distributor), bal);	// @audit-issue

196:        distributor.distribute(tokenToBuy, bal);	// @audit-issue
```
[42](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L42-L42), [43](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L43-L43), [44](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L44-L44), [45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L45-L45), [46](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L46-L46), [47](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L47-L47), [55](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L55-L55), [58](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L58-L58), [77](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L77-L77), [90](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L90-L90), [91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L91-L91), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L117-L117), [136](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L136-L136), [139](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L139-L139), [143](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L143-L143), [145](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L145-L145), [149](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L149-L149), [158](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L158-L158), [160](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L160-L160), [161](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L161-L161), [166](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L166-L166), [190](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L190-L190), [191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L191-L191), [192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L192-L192), [196](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L196-L196), 


```solidity
Path: ./contracts/p1/Distributor.sol

63:        try main.rsrTrader().distributeTokenToBuy() {} catch {}	// @audit-issue

65:        try main.rTokenTrader().distributeTokenToBuy() {} catch {}	// @audit-issue

88:        try main.rsrTrader().distributeTokenToBuy() {} catch {}	// @audit-issue

90:        try main.rTokenTrader().distributeTokenToBuy() {} catch {}	// @audit-issue

139:        Transfer[] memory transfers = new Transfer[](destinations.length());	// @audit-issue

146:            address addrTo = destinations.at(i);	// @audit-issue

145:        for (uint256 i = 0; i < destinations.length(); ++i) {	// @audit-issue

177:        DAOFeeRegistry daoFeeRegistry = main.daoFeeRegistry();	// @audit-issue

180:                (address recipient, , ) = main.daoFeeRegistry().getFeeDetails(address(rToken));	// @audit-issue

195:                stRSR.payoutRewards();	// @audit-issue

197:                furnace.melt();	// @audit-issue

205:        uint256 length = destinations.length();	// @audit-issue

207:            RevenueShare storage share = distribution[destinations.at(i)];	// @audit-issue

212:        DAOFeeRegistry daoFeeRegistry = main.daoFeeRegistry();	// @audit-issue

215:            (address feeRecipient, uint256 feeNumerator, uint256 feeDenominator) = main	// @audit-issue

246:        require(dest != address(main.daoFeeRegistry()), "destination cannot be daoFeeRegistry");	// @audit-issue

253:            destinations.remove(dest);	// @audit-issue

255:            destinations.add(dest);	// @audit-issue

256:            require(destinations.length() <= MAX_DESTINATIONS_ALLOWED, "Too many destinations");	// @audit-issue

271:        rsr = main.rsr();	// @audit-issue

272:        rToken = IERC20(address(main.rToken()));	// @audit-issue

273:        furnace = main.furnace();	// @audit-issue

274:        stRSR = main.stRSR();	// @audit-issue

275:        rTokenTrader = address(main.rTokenTrader());	// @audit-issue

276:        rsrTrader = address(main.rsrTrader());	// @audit-issue
```
[63](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L63-L63), [65](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L65-L65), [88](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L88-L88), [90](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L90-L90), [139](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L139-L139), [146](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L146-L146), [145](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L145-L145), [177](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L177-L177), [180](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L180-L180), [195](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L195-L195), [197](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L197-L197), [205](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L205-L205), [207](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L207-L207), [212](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L212-L212), [215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L215-L215), [246](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L246-L246), [253](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L253-L253), [255](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L255-L255), [256](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L256-L256), [271](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L271-L271), [272](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L272-L272), [273](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L273-L273), [274](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L274-L274), [275](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L275-L275), [276](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L276-L276), 


```solidity
Path: ./contracts/p1/Furnace.sol

35:        rToken = main_.rToken();	// @audit-issue

38:        lastPayoutBal = rToken.balanceOf(address(this));	// @audit-issue

72:        uint192 payoutRatio = FIX_ONE.minus(FIX_ONE.minus(ratio).powu(numPeriods));	// @audit-issue

74:        uint256 amount = payoutRatio.mulu_toUint(lastPayoutBal);	// @audit-issue

77:        lastPayoutBal = rToken.balanceOf(address(this)) - amount;	// @audit-issue

78:        if (amount != 0) rToken.melt(amount);	// @audit-issue
```
[35](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L35-L35), [38](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L38-L38), [72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L72-L72), [74](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L74-L74), [77](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L77-L77), [78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Furnace.sol#L78-L78), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

44:        basketHandler = main_.basketHandler();	// @audit-issue

45:        backingManager = main_.backingManager();	// @audit-issue

62:        uint256 length = _erc20s.length();	// @audit-issue

64:            assets[IERC20(_erc20s.at(i))].refresh();	// @audit-issue

67:        basketHandler.trackStatus();	// @audit-issue

91:        require(_erc20s.contains(address(asset.erc20())), "no ERC20 collision");	// @audit-issue

93:        try basketHandler.quantity{ gas: _reserveGas() }(asset.erc20()) returns (uint192 quantity) {	// @audit-issue

94:            if (quantity != 0) basketHandler.disableBasket(); // not an interaction	// @audit-issue

96:            basketHandler.disableBasket();	// @audit-issue

109:        require(_erc20s.contains(address(asset.erc20())), "no asset to unregister");	// @audit-issue

110:        require(assets[asset.erc20()] == asset, "asset not found");	// @audit-issue

112:        try basketHandler.quantity{ gas: _reserveGas() }(asset.erc20()) returns (uint192 quantity) {	// @audit-issue

113:            if (quantity != 0) basketHandler.disableBasket(); // not an interaction	// @audit-issue

115:            basketHandler.disableBasket();	// @audit-issue

118:        _erc20s.remove(address(asset.erc20()));	// @audit-issue

119:        assets[asset.erc20()] = IAsset(address(0));	// @audit-issue

120:        emit AssetUnregistered(asset.erc20(), asset);	// @audit-issue

127:        require(_erc20s.contains(address(erc20)), "erc20 unregistered");	// @audit-issue

135:        require(_erc20s.contains(address(erc20)), "erc20 unregistered");	// @audit-issue

136:        require(assets[erc20].isCollateral(), "erc20 is not collateral");	// @audit-issue

143:        return _erc20s.contains(address(erc20));	// @audit-issue

149:        uint256 length = _erc20s.length();	// @audit-issue

152:            erc20s_[i] = IERC20(_erc20s.at(i));	// @audit-issue

160:        uint256 length = _erc20s.length();	// @audit-issue

164:            reg.erc20s[i] = IERC20(_erc20s.at(i));	// @audit-issue

165:            reg.assets[i] = assets[IERC20(_erc20s.at(i))];	// @audit-issue

172:        AssetPluginRegistry assetPluginRegistry = main.assetPluginRegistry();	// @audit-issue

180:                    assetPluginRegistry.isValidAsset(	// @audit-issue

181:                        keccak256(abi.encodePacked(this.version())),	// @audit-issue

192:        return _erc20s.length();	// @audit-issue

203:            !_erc20s.contains(address(asset.erc20())) || assets[asset.erc20()] == asset,	// @audit-issue

214:        if (asset.isCollateral()) {	// @audit-issue

216:                ICollateral(address(asset)).status() == CollateralStatus.SOUND,	// @audit-issue

221:        AssetPluginRegistry assetPluginRegistry = main.assetPluginRegistry();	// @audit-issue

224:                main.assetPluginRegistry().isValidAsset(	// @audit-issue

225:                    keccak256(abi.encodePacked(this.version())),	// @audit-issue

232:        IERC20Metadata erc20 = asset.erc20();	// @audit-issue

233:        if (_erc20s.contains(address(erc20))) {	// @audit-issue

237:            _erc20s.add(address(erc20));	// @audit-issue

244:        asset.refresh();	// @audit-issue

246:        if (!main.frozen()) {	// @audit-issue

247:            backingManager.grantRTokenAllowance(erc20);	// @audit-issue
```
[44](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L44-L44), [45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L45-L45), [62](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L62-L62), [64](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L64-L64), [67](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L67-L67), [91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L91-L91), [93](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L93-L93), [94](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L94-L94), [96](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L96-L96), [109](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L109-L109), [110](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L110-L110), [112](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L112-L112), [113](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L113-L113), [115](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L115-L115), [118](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L118-L118), [119](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L119-L119), [120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L120-L120), [127](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L127-L127), [135](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L135-L135), [136](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L136-L136), [143](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L143-L143), [149](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L149-L149), [152](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L152-L152), [160](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L160-L160), [164](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L164-L164), [165](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L165-L165), [172](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L172-L172), [180](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L180-L180), [181](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L181-L181), [192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L192-L192), [203](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L203-L203), [214](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L214-L214), [216](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L216-L216), [221](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L221-L221), [224](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L224-L224), [225](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L225-L225), [232](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L232-L232), [233](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L233-L233), [237](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L237-L237), [244](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L244-L244), [246](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L246-L246), [247](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L247-L247), 


```solidity
Path: ./contracts/p1/BackingManager.sol

70:        require(assetRegistry.isRegistered(erc20), "erc20 unregistered");	// @audit-issue

72:        IERC20(address(erc20)).safeApprove(address(rToken), 0);	// @audit-issue

73:        IERC20(address(erc20)).safeApprove(address(rToken), type(uint256).max);	// @audit-issue

87:        trade = super.settleTrade(sell); // nonReentrant	// @audit-issue

92:            try this.rebalance(trade.KIND()) {} catch (bytes memory errData) {	// @audit-issue

111:        assetRegistry.refresh();	// @audit-issue

121:        require(basketHandler.isReady(), "basket not ready");	// @audit-issue

122:        require(block.timestamp >= basketHandler.timestamp() + tradingDelay, "trading delayed");	// @audit-issue

124:        BasketRange memory basketsHeld = basketHandler.basketsHeldBy(address(this));	// @audit-issue

125:        require(basketsHeld.bottom < rToken.basketsNeeded(), "already collateralized");	// @audit-issue

130:        uint256 balance = rToken.balanceOf(address(this));	// @audit-issue

131:        if (balance >= MAX_DISTRIBUTION * MAX_DESTINATIONS) rToken.dissolve(balance);	// @audit-issue

132:        if (basketsHeld.bottom >= rToken.basketsNeeded()) return; // return if now capitalized	// @audit-issue

153:        ) = RecollateralizationLibP1.prepareRecollateralizationTrade(ctx, reg);	// @audit-issue

156:            IERC20 sellERC20 = req.sell.erc20();	// @audit-issue

160:                uint256 bal = sellERC20.balanceOf(address(this));	// @audit-issue

161:                if (req.sellAmount > bal) stRSR.seizeRSR(req.sellAmount - bal);	// @audit-issue

166:            tradeEnd[kind] = trade.endTime(); // {s}	// @audit-issue

167:            tokensOut[sellERC20] = trade.sellAmount(); // {tok}	// @audit-issue

181:        require(ArrayLib.allUnique(erc20s), "duplicate tokens");	// @audit-issue

183:        assetRegistry.refresh();	// @audit-issue

185:        BasketRange memory basketsHeld = basketHandler.basketsHeldBy(address(this));	// @audit-issue

188:        require(basketHandler.isReady(), "basket not ready");	// @audit-issue

189:        require(block.timestamp >= basketHandler.timestamp() + tradingDelay, "trading delayed");	// @audit-issue

190:        require(basketsHeld.bottom >= rToken.basketsNeeded(), "undercollateralized");	// @audit-issue

213:        if (rsr.balanceOf(address(this)) != 0) {	// @audit-issue

215:            IERC20(address(rsr)).safeTransfer(address(stRSR), rsr.balanceOf(address(this)));	// @audit-issue

216:            stRSR.payoutRewards();	// @audit-issue

221:        uint192 baskets = (basketsHeld.bottom.div(FIX_ONE + backingBuffer));	// @audit-issue

222:        if (baskets > rToken.basketsNeeded()) {	// @audit-issue

223:            rToken.mint(baskets - rToken.basketsNeeded());	// @audit-issue

226:        uint192 needed = rToken.basketsNeeded().mul(FIX_ONE + backingBuffer); // {BU}	// @audit-issue

235:        RevenueTotals memory totals = distributor.totals();	// @audit-issue

237:            IAsset asset = assetRegistry.toAsset(erc20s[i]);	// @audit-issue

241:            uint192 req = needed.mul(basketHandler.quantity(erc20s[i]), CEIL);	// @audit-issue

242:            uint192 bal = asset.bal(address(this));	// @audit-issue

244:            if (bal.gt(req)) {	// @audit-issue

246:                uint256 delta = bal.minus(req).shiftl_toUint(int8(asset.erc20Decimals()));	// @audit-issue

254:                    erc20s[i].safeTransfer(address(rsrTrader), tokensPerShare * totals.rsrTotal);	// @audit-issue

257:                    erc20s[i].safeTransfer(	// @audit-issue

278:        reg = assetRegistry.getRegistry();	// @audit-issue

290:            ctx.quantities[i] = basketHandler.quantityUnsafe(reg.erc20s[i], reg.assets[i]);	// @audit-issue

295:            ctx.bals[i] = reg.assets[i].bal(address(this)) + tokensOut[reg.erc20s[i]];	// @audit-issue

298:            if (reg.erc20s[i] == rsr) ctx.bals[i] += reg.assets[i].bal(address(stRSR));	// @audit-issue

309:        rToken.setBasketsNeeded(basketsHeldBottom);	// @audit-issue

330:        assetRegistry = main.assetRegistry();	// @audit-issue

331:        basketHandler = main.basketHandler();	// @audit-issue

332:        distributor = main.distributor();	// @audit-issue

333:        rToken = main.rToken();	// @audit-issue

334:        rsr = main.rsr();	// @audit-issue

335:        stRSR = main.stRSR();	// @audit-issue

336:        rsrTrader = main.rsrTrader();	// @audit-issue

337:        rTokenTrader = main.rTokenTrader();	// @audit-issue

338:        furnace = main.furnace();	// @audit-issue
```
[70](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L70-L70), [72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L72-L72), [73](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L73-L73), [87](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L87-L87), [92](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L92-L92), [111](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L111-L111), [121](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L121-L121), [122](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L122-L122), [124](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L124-L124), [125](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L125-L125), [130](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L130-L130), [131](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L131-L131), [132](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L132-L132), [153](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L153-L153), [156](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L156-L156), [160](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L160-L160), [161](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L161-L161), [166](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L166-L166), [167](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L167-L167), [181](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L181-L181), [183](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L183-L183), [185](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L185-L185), [188](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L188-L188), [189](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L189-L189), [190](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L190-L190), [213](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L213-L213), [215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L215-L215), [216](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L216-L216), [221](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L221-L221), [222](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L222-L222), [223](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L223-L223), [226](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L226-L226), [235](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L235-L235), [237](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L237-L237), [241](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L241-L241), [242](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L242-L242), [244](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L244-L244), [246](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L246-L246), [254](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L254-L254), [257](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L257-L257), [278](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L278-L278), [290](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L290-L290), [295](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L295-L295), [298](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L298-L298), [309](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L309-L309), [330](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L330-L330), [331](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L331-L331), [332](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L332-L332), [333](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L333-L333), [334](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L334-L334), [335](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L335-L335), [336](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L336-L336), [337](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L337-L337), [338](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BackingManager.sol#L338-L338), 


```solidity
Path: ./contracts/p1/StRSR.sol

190:        assetRegistry = main_.assetRegistry();	// @audit-issue

191:        backingManager = main_.backingManager();	// @audit-issue

192:        basketHandler = main_.basketHandler();	// @audit-issue

193:        rsr = IERC20(address(main_.rsr()));	// @audit-issue

196:        rsrRewardsAtLastPayout = main_.rsr().balanceOf(address(this));	// @audit-issue

337:        IERC20Upgradeable(address(rsr)).safeTransfer(account, rsrAmount);	// @audit-issue

341:        require(basketHandler.isReady() && basketHandler.fullyCollateralized(), "RToken readying");	// @audit-issue

431:        uint256 rsrBalance = rsr.balanceOf(address(this));	// @audit-issue

477:        IERC20Upgradeable(address(rsr)).safeTransfer(caller, seizedRSR);	// @audit-issue

606:            uint192 payoutRatio = FIX_ONE - FixLib.powu(FIX_ONE - rewardRatio, numPeriods);	// @audit-issue

663:        queue.push(CumulativeDraft(uint176(oldDrafts + draftAmount), availableAt));	// @audit-issue

694:        return rsr.balanceOf(address(this)) - stakeRSR - draftRSR;	// @audit-issue

701:        uint48 lastRefresh = assetRegistry.lastRefresh(); // {s}	// @audit-issue

716:            assetRegistry.refresh();	// @audit-issue

943:        return _nonces[owner].current();	// @audit-issue

947:        return _delegationNonces[owner].current();	// @audit-issue

957:        current = nonce.current();	// @audit-issue

958:        nonce.increment();	// @audit-issue

963:        current = nonce.current();	// @audit-issue

964:        nonce.increment();	// @audit-issue
```
[190](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L190-L190), [191](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L191-L191), [192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L192-L192), [193](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L193-L193), [196](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L196-L196), [337](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L337-L337), [341](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L341-L341), [431](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L431-L431), [477](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L477-L477), [606](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L606-L606), [663](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L663-L663), [694](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L694-L694), [701](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L701-L701), [716](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L716-L716), [943](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L943-L943), [947](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L947-L947), [957](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L957-L957), [958](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L958-L958), [963](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L963-L963), [964](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L964-L964), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

116:        assetRegistry = main_.assetRegistry();	// @audit-issue

117:        backingManager = main_.backingManager();	// @audit-issue

118:        rsr = main_.rsr();	// @audit-issue

119:        rToken = main_.rToken();	// @audit-issue

120:        stRSR = main_.stRSR();	// @audit-issue

159:        assetRegistry.refresh();	// @audit-issue

162:            main.hasRole(OWNER, _msgSender()) ||	// @audit-issue

163:                (lastStatus == CollateralStatus.DISABLED && !main.tradingPausedOrFrozen()),	// @audit-issue

263:            require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "erc20 is not collateral");	// @audit-issue

266:            config.erc20s.push(erc20s[i]);	// @audit-issue

268:            names[i] = assetRegistry.toColl(erc20s[i]).targetName();	// @audit-issue

299:            assetRegistry.toColl(erc20s[i]); // reverts if not collateral	// @audit-issue

300:            conf.erc20s.push(erc20s[i]);	// @audit-issue

309:        return held.bottom >= rToken.basketsNeeded();	// @audit-issue

322:            CollateralStatus s = assetRegistry.toColl(basket.erc20s[i]).status();	// @audit-issue

323:            if (s.worseThan(status_)) {	// @audit-issue

344:        try assetRegistry.toColl(erc20) returns (ICollateral coll) {	// @audit-issue

360:        if (!asset.isCollateral()) return FIX_ZERO;	// @audit-issue

368:        if (!enableIssuancePremium || coll.lastSave() != block.timestamp) return FIX_ONE;	// @audit-issue

371:        try coll.savedPegPrice() returns (uint192 pegPrice) {	// @audit-issue

373:            uint192 targetPerRef = coll.targetPerRef(); // {target/ref}	// @audit-issue

377:            return targetPerRef.safeDiv(pegPrice, CEIL);	// @audit-issue

396:        uint192 refPerTok = coll.refPerTok();	// @audit-issue

400:        return basket.refAmts[erc20].div(refPerTok, rounding);	// @audit-issue

425:            try assetRegistry.toColl(basket.erc20s[i]) returns (ICollateral coll) {	// @audit-issue

429:                (uint192 lowP, uint192 highP) = coll.price();	// @audit-issue

431:                low256 += qty.safeMul(lowP, FLOOR);	// @audit-issue

443:                        if (premium > FIX_ONE) qty = qty.safeMul(premium, CEIL);	// @audit-issue

446:                    high256 += qty.safeMul(highP, CEIL);	// @audit-issue

493:            ICollateral coll = assetRegistry.toColl(IERC20(erc20s[i]));	// @audit-issue

496:            uint192 q = _quantity(basket.erc20s[i], coll, rounding).safeMul(amount, rounding);	// @audit-issue

503:                if (premium > FIX_ONE) q = q.safeMul(premium, rounding);	// @audit-issue

507:            quantities[i] = q.shiftl_toUint(	// @audit-issue

508:                int8(IERC20Metadata(address(basket.erc20s[i])).decimals()),	// @audit-issue

528:        IERC20[] memory erc20sAll = new IERC20[](assetRegistry.size());	// @audit-issue

562:                uint192 amt = portions[i].mul(b.refAmts[b.erc20s[j]], FLOOR);	// @audit-issue

566:                    try assetRegistry.toAsset(b.erc20s[j]) returns (IAsset asset) {	// @audit-issue

567:                        if (!asset.isCollateral()) continue; // skip token if not collateral	// @audit-issue

597:            quantities[i] = amount	// @audit-issue

598:            .safeMulDiv(refAmtsAll[i], collsAll[i].refPerTok(), FLOOR)	// @audit-issue

599:            .shiftl_toUint(int8(collsAll[i].erc20Decimals()), FLOOR);	// @audit-issue

616:            ICollateral coll = assetRegistry.toColl(basket.erc20s[i]);	// @audit-issue

617:            if (coll.status() == CollateralStatus.DISABLED) return BasketRange(FIX_ZERO, FIX_MAX);	// @audit-issue

624:            uint192 inBUs = coll.bal(account).div(q);	// @audit-issue

667:            basket.setFrom(_newBasket);	// @audit-issue

668:            basketHistory[nonce].setFrom(_newBasket);	// @audit-issue

694:        require(ArrayLib.allUnique(erc20s), "contains duplicates");	// @audit-issue

716:            try assetRegistry.toAsset(IERC20(erc20s[i])) returns (IAsset asset) {	// @audit-issue

717:                if (!asset.isCollateral()) continue; // skip token if no longer registered	// @audit-issue

720:                quantities[i] = b	// @audit-issue

722:                .safeDiv(ICollateral(address(asset)).refPerTok(), FLOOR)	// @audit-issue

723:                .shiftl_toUint(int8(asset.erc20Decimals()), FLOOR);	// @audit-issue
```
[116](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L116-L116), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L117-L117), [118](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L118-L118), [119](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L119-L119), [120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L120-L120), [159](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L159-L159), [162](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L162-L162), [163](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L163-L163), [263](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L263-L263), [266](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L266-L266), [268](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L268-L268), [299](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L299-L299), [300](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L300-L300), [309](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L309-L309), [322](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L322-L322), [323](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L323-L323), [344](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L344-L344), [360](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L360-L360), [368](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L368-L368), [371](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L371-L371), [373](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L373-L373), [377](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L377-L377), [396](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L396-L396), [400](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L400-L400), [425](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L425-L425), [429](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L429-L429), [431](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L431-L431), [443](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L443-L443), [446](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L446-L446), [493](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L493-L493), [496](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L496-L496), [503](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L503-L503), [507](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L507-L507), [508](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L508-L508), [528](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L528-L528), [562](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L562-L562), [566](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L566-L566), [567](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L567-L567), [597](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L597-L597), [598](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L598-L598), [599](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L599-L599), [616](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L616-L616), [617](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L617-L617), [624](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L624-L624), [667](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L667-L667), [668](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L668-L668), [694](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L694-L694), [716](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L716-L716), [717](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L717-L717), [720](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L720-L720), [722](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L722-L722), [723](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L723-L723), 


```solidity
Path: ./contracts/p1/Deployer.sol

212:        components.distributor.init(main, params.dist);	// @audit-issue

215:        components.furnace.init(main, params.rewardRatio);	// @audit-issue

228:            string memory stRSRSymbol = string(abi.encodePacked(StringLib.toLower(symbol), "RSR"));	// @audit-issue

230:            main.stRSR().init(	// @audit-issue

256:        components.assetRegistry.init(main, assets);	// @audit-issue

259:        main.grantRole(OWNER, owner);	// @audit-issue

260:        main.renounceRole(OWNER, address(this));	// @audit-issue
```
[212](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L212-L212), [215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L215-L215), [228](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L228-L228), [230](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L230-L230), [256](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L256-L256), [259](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L259-L259), [260](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L260-L260), 


```solidity
Path: ./contracts/p1/RToken.sol

76:        assetRegistry = main_.assetRegistry();	// @audit-issue

77:        basketHandler = main_.basketHandler();	// @audit-issue

78:        backingManager = main_.backingManager();	// @audit-issue

79:        furnace = main_.furnace();	// @audit-issue

110:        assetRegistry.refresh();	// @audit-issue

117:        require(basketHandler.isReady(), "basket not ready");	// @audit-issue

121:        issuanceThrottle.useAvailable(supply, int256(amount)); // reverts on over-issuance	// @audit-issue

122:        redemptionThrottle.useAvailable(supply, -int256(amount)); // shouldn't revert	// @audit-issue

185:        assetRegistry.refresh();	// @audit-issue

193:        require(basketHandler.fullyCollateralized(), "partial redemption; use redeemCustom");	// @audit-issue

199:        issuanceThrottle.useAvailable(supply, -int256(amount));	// @audit-issue

200:        redemptionThrottle.useAvailable(supply, int256(amount)); // reverts on over-redemption	// @audit-issue

262:        assetRegistry.refresh();	// @audit-issue

277:        issuanceThrottle.useAvailable(supply, -int256(amount));	// @audit-issue

278:        redemptionThrottle.useAvailable(supply, int256(amount)); // reverts on over-redemption	// @audit-issue

300:                IERC20(erc20s[i]).balanceOf(address(backingManager)),	// @audit-issue

312:            pastBals[i] = IERC20(expectedERC20sOut[i]).balanceOf(recipient);	// @audit-issue

340:            uint256 bal = IERC20(expectedERC20sOut[i]).balanceOf(recipient);	// @audit-issue

419:        require(assetRegistry.isRegistered(erc20), "erc20 unregistered");	// @audit-issue

420:        IERC20Upgradeable(address(erc20)).safeTransfer(	// @audit-issue

422:            erc20.balanceOf(address(this))	// @audit-issue

430:        return issuanceThrottle.currentlyAvailable(issuanceThrottle.hourlyLimit(totalSupply()));	// @audit-issue

436:        available = redemptionThrottle.currentlyAvailable(redemptionThrottle.hourlyLimit(supply));	// @audit-issue

455:        issuanceThrottle.useAvailable(totalSupply(), 0);	// @audit-issue

466:        redemptionThrottle.useAvailable(totalSupply(), 0);	// @audit-issue

490:            ? amtBaskets.muluDivu(totalSupply, basketsNeeded) // {rTok} = {BU} * {qRTok} * {qRTok}	// @audit-issue

510:        amtBaskets = basketsNeeded.muluDivu(amtRToken, totalSupply()); // FLOOR	// @audit-issue
```
[76](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L76-L76), [77](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L77-L77), [78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L78-L78), [79](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L79-L79), [110](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L110-L110), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L117-L117), [121](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L121-L121), [122](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L122-L122), [185](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L185-L185), [193](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L193-L193), [199](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L199-L199), [200](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L200-L200), [262](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L262-L262), [277](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L277-L277), [278](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L278-L278), [300](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L300-L300), [312](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L312-L312), [340](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L340-L340), [419](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L419-L419), [420](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L420-L420), [422](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L422-L422), [430](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L430-L430), [436](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L436-L436), [455](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L455-L455), [466](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L466-L466), [490](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L490-L490), [510](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L510-L510), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

53:        super.beginEra();	// @audit-issue

59:        return SafeCastUpgradeable.toUint48(block.timestamp);	// @audit-issue

79:        return SafeCastUpgradeable.toUint48(_checkpoints[era][account].length);	// @audit-issue

140:            uint256 mid = length - MathUpgradeable.sqrt(length);	// @audit-issue

149:            uint256 mid = MathUpgradeable.average(low, high);	// @audit-issue

202:        super._mint(account, amount);	// @audit-issue

207:        super._burn(account, amount);	// @audit-issue

271:                ckpts[pos - 1].val = SafeCastUpgradeable.toUint224(newWeight);	// @audit-issue

273:                ckpts.push(	// @audit-issue

276:                        val: SafeCastUpgradeable.toUint224(newWeight)	// @audit-issue
```
[53](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L53-L53), [59](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L59-L59), [79](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L79-L79), [140](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L140-L140), [149](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L149-L149), [202](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L202-L202), [207](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L207-L207), [271](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L271-L271), [273](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L273-L273), [276](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L276-L276), 


```solidity
Path: ./contracts/p1/Broker.sol

107:        backingManager = main.backingManager();	// @audit-issue

108:        rsrTrader = main.rsrTrader();	// @audit-issue

109:        rTokenTrader = main.rTokenTrader();	// @audit-issue

110:        rToken = main.rToken();	// @audit-issue

153:        TradeKind kind = trade.KIND();	// @audit-issue

160:            if (DutchTrade(address(trade)).origin() == backingManager) {	// @audit-issue

161:                IERC20Metadata sell = trade.sell();	// @audit-issue

165:                IERC20Metadata buy = trade.buy();	// @audit-issue

246:        GnosisTrade trade = GnosisTrade(address(batchTradeImplementation).clone());	// @audit-issue

259:        IERC20Upgradeable(address(req.sell.erc20())).safeTransferFrom(	// @audit-issue

274:            !dutchTradeDisabled[req.sell.erc20()] && !dutchTradeDisabled[req.buy.erc20()],	// @audit-issue

283:        DutchTrade trade = DutchTrade(address(dutchTradeImplementation).clone());	// @audit-issue

287:        IERC20Upgradeable(address(req.sell.erc20())).safeTransferFrom(	// @audit-issue

299:        return asset.lastSave() == block.timestamp || address(asset.erc20()) == address(rToken);	// @audit-issue
```
[107](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L107-L107), [108](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L108-L108), [109](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L109-L109), [110](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L110-L110), [153](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L153-L153), [160](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L160-L160), [161](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L161-L161), [165](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L165-L165), [246](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L246-L246), [259](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L259-L259), [274](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L274-L274), [283](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L283-L283), [287](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L287-L287), [299](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L299-L299), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

53:        broker = main_.broker();	// @audit-issue

67:        RewardableLibP1.claimRewards(main.assetRegistry());	// @audit-issue

76:        RewardableLibP1.claimRewardsSingle(main.assetRegistry().toAsset(erc20));	// @audit-issue

97:        require(trade.canSettle(), "cannot settle yet");	// @audit-issue

103:        (uint256 soldAmt, uint256 boughtAmt) = trade.settle();	// @audit-issue

104:        emit TradeSettled(trade, sell, trade.buy(), soldAmt, boughtAmt);	// @audit-issue

125:        IERC20 sell = req.sell.erc20();	// @audit-issue

140:        emit TradeStarted(trade, sell, req.buy.erc20(), req.sellAmount, req.minBuyAmount);	// @audit-issue
```
[53](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L53-L53), [67](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L67-L67), [76](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L76-L76), [97](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L97-L97), [103](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L103-L103), [104](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L104-L104), [125](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L125-L125), [140](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L140-L140), 


```solidity
Path: ./contracts/p1/mixins/TradeLib.sol

76:        uint192 b = s.mul(FIX_ONE.minus(maxTradeSlippage)).safeMulDiv(	// @audit-issue

83:        req.sellAmount = s.shiftl_toUint(int8(trade.sell.erc20Decimals()), FLOOR);	// @audit-issue

84:        req.minBuyAmount = b.shiftl_toUint(int8(trade.buy.erc20Decimals()), CEIL);	// @audit-issue

145:        uint192 slippedSellAmount = exactSellAmount.div(FIX_ONE.minus(maxTradeSlippage), CEIL);	// @audit-issue

163:            amt.gte(minTradeSize(minTradeVolume, price)) &&	// @audit-issue

166:            amt.shiftl_toUint(int8(asset.erc20Decimals())) > 1;	// @audit-issue

176:        uint192 size = price == 0 ? FIX_MAX : minTradeVolume.div(price, CEIL);	// @audit-issue

188:        uint192 size = fixMin(sell.maxTradeVolume(), buy.maxTradeVolume()).safeDiv(price, FLOOR);	// @audit-issue
```
[76](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L76-L76), [83](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L83-L83), [84](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L84-L84), [145](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L145-L145), [163](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L163-L163), [166](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L166-L166), [176](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L176-L176), [188](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/TradeLib.sol#L188-L188), 


```solidity
Path: ./contracts/p1/mixins/RewardableLib.sol

25:        Registry memory registry = reg.getRegistry();	// @audit-issue

29:            address(registry.assets[i]).functionDelegateCall(	// @audit-issue

41:        address(asset).functionDelegateCall(	// @audit-issue
```
[25](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L25-L25), [29](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L29-L29), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RewardableLib.sol#L41-L41), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

42:        require(!main.tradingPausedOrFrozen(), "frozen or trading paused");	// @audit-issue

47:        require(!main.issuancePausedOrFrozen(), "frozen or issuance paused");	// @audit-issue

52:        require(!main.frozen(), "frozen");	// @audit-issue

57:        require(main.hasRole(OWNER, _msgSender()), "governance only");	// @audit-issue
```
[42](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L42-L42), [47](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L47-L47), [52](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L52-L52), [57](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L57-L57), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

59:            (trade.sell.isCollateral() &&	// @audit-issue

60:                ICollateral(address(trade.sell)).status() != CollateralStatus.SOUND)	// @audit-issue

64:            (doTrade, req) = trade.prepareTradeSell(ctx.minTradeVolume, ctx.maxTradeSlippage);	// @audit-issue

68:            (doTrade, req) = trade.prepareTradeToCoverDeficit(	// @audit-issue

116:        (uint192 buPriceLow, uint192 buPriceHigh) = ctx.bh.price(false); // {UoA/BU}	// @audit-issue

119:        uint192 basketsNeeded = ctx.rToken.basketsNeeded(); // {BU}	// @audit-issue

143:            (uint192 low, uint192 high) = reg.assets[i].price(); // {UoA/tok}	// @audit-issue

161:                uint192 anchor = ctx.quantities[i].mul(ctx.basketsHeld.top, CEIL);	// @audit-issue

185:                uint192 anchor = ctx.quantities[i].mul(ctx.basketsHeld.bottom, FLOOR);	// @audit-issue

189:                uint192 val = low.mul(ctx.bals[i] - anchor, FLOOR);	// @audit-issue

220:            uoaBottom.mulDiv(FIX_ONE.minus(ctx.maxTradeSlippage), buPriceHigh, FLOOR);	// @audit-issue

297:            uint192 needed = range.top.mul(ctx.quantities[i], CEIL); // {tok}	// @audit-issue

299:            if (ctx.bals[i].gt(needed)) {	// @audit-issue

300:                (uint192 low, uint192 high) = reg.assets[i].price(); // {UoA/sellTok}	// @audit-issue

305:                uint192 delta = ctx.bals[i].minus(needed).mul(low, FLOOR);	// @audit-issue

309:                if (reg.assets[i].isCollateral()) {	// @audit-issue

310:                    status = ICollateral(address(reg.assets[i])).status();	// @audit-issue

319:                        ctx.bals[i].minus(needed),	// @audit-issue

325:                    trade.sellAmount = ctx.bals[i].minus(needed);	// @audit-issue

334:                needed = range.bottom.mul(ctx.quantities[i], CEIL); // {buyTok};	// @audit-issue

336:                if (ctx.bals[i].lt(needed)) {	// @audit-issue

337:                    uint192 amtShort = needed.minus(ctx.bals[i]); // {buyTok}	// @audit-issue

338:                    (uint192 low, uint192 high) = reg.assets[i].price(); // {UoA/buyTok}	// @audit-issue

341:                    uint192 delta = amtShort.mul(high, CEIL);	// @audit-issue

344:                    if (delta.gt(maxes.deficit)) {	// @audit-issue

358:            (uint192 low, uint192 high) = reg.assets[rsrIndex].price(); // {UoA/RSR}	// @audit-issue

388:            return other == CollateralStatus.DISABLED && surplusAmt.gt(curr.surplus);	// @audit-issue

392:                (other == CollateralStatus.SOUND && surplusAmt.gt(curr.surplus));	// @audit-issue

395:            return other != CollateralStatus.IFFY || surplusAmt.gt(curr.surplus);	// @audit-issue
```
[59](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L59-L59), [60](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L60-L60), [64](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L64-L64), [68](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L68-L68), [116](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L116-L116), [119](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L119-L119), [143](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L143-L143), [161](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L161-L161), [185](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L185-L185), [189](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L189-L189), [220](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L220-L220), [297](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L297-L297), [299](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L299-L299), [300](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L300-L300), [305](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L305-L305), [309](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L309-L309), [310](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L310-L310), [319](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L319-L319), [325](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L325-L325), [334](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L334-L334), [336](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L336-L336), [337](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L337-L337), [338](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L338-L338), [341](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L341-L341), [344](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L344-L344), [358](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L358-L358), [388](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L388-L388), [392](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L392-L392), [395](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L395-L395), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

82:            self.erc20s.push(_erc20);	// @audit-issue

100:        if (self.refAmts[tok].eq(FIX_ZERO)) {	// @audit-issue

101:            self.erc20s.push(tok);	// @audit-issue

104:            self.refAmts[tok] = self.refAmts[tok].plus(weight);	// @audit-issue

171:        while (targetNames.length() != 0) {	// @audit-issue

172:            targetNames.remove(targetNames.at(targetNames.length() - 1));	// @audit-issue

176:        newBasket.empty();	// @audit-issue

181:            targetNames.add(config.targetNames[config.erc20s[i]]);	// @audit-issue

183:        uint256 targetsLength = targetNames.length();	// @audit-issue

203:                if (targetNames.at(targetIndex) == config.targetNames[_erc20]) break;	// @audit-issue

211:            totalWeights[targetIndex] = totalWeights[targetIndex].plus(targetWeight);	// @audit-issue

215:                targetWeight.gt(FIX_ZERO)	// @audit-issue

217:                goodWeights[targetIndex] = goodWeights[targetIndex].plus(targetWeight);	// @audit-issue

218:                newBasket.add(	// @audit-issue

220:                    targetWeight.div(	// @audit-issue

222:                        assetRegistry.toColl(_erc20).targetPerRef(),	// @audit-issue

242:            if (totalWeights[i].lte(goodWeights[i])) continue; // Don't need any backup weight	// @audit-issue

243:            bytes32 _targetName = targetNames.at(i);	// @audit-issue

265:                    uint192 backupWeight = totalWeights[i].minus(goodWeights[i]).div(	// @audit-issue

267:                        assetRegistry.toColl(backup.erc20s[j]).targetPerRef().mulu(size),	// @audit-issue

301:        try assetRegistry.toColl(erc20) returns (ICollateral coll) {	// @audit-issue

303:                targetName == coll.targetName() &&	// @audit-issue

304:                coll.status() == CollateralStatus.SOUND &&	// @audit-issue

305:                coll.refPerTok() != 0 &&	// @audit-issue

306:                coll.targetPerRef() != 0;	// @audit-issue

328:            (bool contains, uint256 amt) = _targetAmts.tryGet(targetName);	// @audit-issue

329:            _targetAmts.set(	// @audit-issue

338:            bytes32 targetName = assetRegistry.toColl(erc20s[i]).targetName();	// @audit-issue

339:            (bool contains, uint256 amt) = _targetAmts.tryGet(targetName);	// @audit-issue

341:            if (amt > targetAmts[i]) _targetAmts.set(targetName, amt - targetAmts[i]);	// @audit-issue

342:            else _targetAmts.remove(targetName);	// @audit-issue

344:        require(_targetAmts.length() == 0, "missing target weights");	// @audit-issue

364:            ICollateral coll = assetRegistry.toColl(erc20s[i]); // reverts if unregistered	// @audit-issue

365:            require(coll.status() == CollateralStatus.SOUND, "unsound new collateral");	// @audit-issue

367:            (uint192 low, uint192 high) = coll.price(); // {UoA/tok}	// @audit-issue

373:                coll.targetPerRef().mul(coll.refPerTok(), CEIL),	// @audit-issue
```
[82](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L82-L82), [100](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L100-L100), [101](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L101-L101), [104](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L104-L104), [171](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L171-L171), [172](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L172-L172), [176](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L176-L176), [181](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L181-L181), [183](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L183-L183), [203](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L203-L203), [211](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L211-L211), [215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L215-L215), [217](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L217-L217), [218](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L218-L218), [220](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L220-L220), [222](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L222-L222), [242](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L242-L242), [243](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L243-L243), [265](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L265-L265), [267](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L267-L267), [301](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L301-L301), [303](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L303-L303), [304](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L304-L304), [305](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L305-L305), [306](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L306-L306), [328](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L328-L328), [329](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L329-L329), [338](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L338-L338), [339](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L339-L339), [341](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L341-L341), [342](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L342-L342), [344](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L344-L344), [364](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L364-L364), [365](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L365-L365), [367](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L367-L367), [373](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L373-L373), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

94:        sell = req.sell.erc20();	// @audit-issue

95:        buy = req.buy.erc20();	// @audit-issue

96:        sellAmount = shiftl_toFix(req.sellAmount, -int8(sell.decimals()), FLOOR); // {sellTok}	// @audit-issue

98:        initBal = sell.balanceOf(address(this)); // {qSellTok}	// @audit-issue

109:        worstCasePrice = shiftl_toFix(req.minBuyAmount, 9).divu(req.sellAmount, FLOOR);	// @audit-issue

117:                FEE_DENOMINATOR + gnosis.feeNumerator(),	// @audit-issue

123:        uint96 minBuyAmount = uint96(Math.max(1, req.minBuyAmount)); // Safe downcast; require'd	// @audit-issue

125:        uint256 minBuyAmtPerOrder = Math.max(	// @audit-issue

127:            DEFAULT_MIN_BID.shiftl_toUint(int8(buy.decimals()))	// @audit-issue

187:            gnosis.settleAuction(auctionId);	// @audit-issue

194:        uint256 sellBal = sell.balanceOf(address(this));	// @audit-issue

203:        boughtAmt = buy.balanceOf(address(this));	// @audit-issue

205:        if (sellBal != 0) IERC20Upgradeable(address(sell)).safeTransfer(origin, sellBal);	// @audit-issue

206:        if (boughtAmt != 0) IERC20Upgradeable(address(buy)).safeTransfer(origin, boughtAmt);	// @audit-issue

213:            uint256 adjustedSoldAmt = Math.max(soldAmt, 1);	// @audit-issue

217:            uint192 clearingPrice = shiftl_toFix(adjustedBuyAmt, 9).divu(adjustedSoldAmt, FLOOR);	// @audit-issue

218:            if (clearingPrice.lt(worstCasePrice)) broker.reportViolation();	// @audit-issue

227:        IERC20Upgradeable(address(erc20)).safeTransfer(origin, erc20.balanceOf(address(this)));	// @audit-issue

239:        GnosisAuctionData memory data = gnosis.auctionData(auctionId);	// @audit-issue
```
[94](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L94-L94), [95](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L95-L95), [96](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L96-L96), [98](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L98-L98), [109](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L109-L109), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L117-L117), [123](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L123-L123), [125](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L125-L125), [127](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L127-L127), [187](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L187-L187), [194](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L194-L194), [203](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L203-L203), [205](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L205-L205), [206](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L206-L206), [213](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L213-L213), [217](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L217-L217), [218](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L218-L218), [227](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L227-L227), [239](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L239-L239), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

133:        return sellAmount.shiftl_toUint(int8(sell.decimals()));	// @audit-issue

173:        sell = sell_.erc20();	// @audit-issue

174:        buy = buy_.erc20();	// @audit-issue

176:        require(sellAmount_ <= sell.balanceOf(address(this)), "unfunded trade");	// @audit-issue

177:        sellAmount = shiftl_toFix(sellAmount_, -int8(sell.decimals()), FLOOR); // {sellTok}	// @audit-issue

186:            FIX_ONE - origin.maxTradeSlippage(),	// @audit-issue

190:        uint192 _bestPrice = prices.sellHigh.div(prices.buyLow, CEIL); // no additional slippage	// @audit-issue

214:        if (price > bestPrice.mul(ONE_POINT_FIVE, CEIL)) {	// @audit-issue

215:            broker.reportViolation();	// @audit-issue

222:        origin.settleTrade(sell);	// @audit-issue

250:        if (price > bestPrice.mul(ONE_POINT_FIVE, CEIL)) {	// @audit-issue

251:            broker.reportViolation();	// @audit-issue

255:        sell.safeTransfer(bidder, lot()); // {qSellTok}	// @audit-issue

257:        uint256 balanceBefore = buy.balanceOf(address(this)); // {qBuyTok}	// @audit-issue

260:            amountIn <= buy.balanceOf(address(this)) - balanceBefore,	// @audit-issue

265:        origin.settleTrade(sell);	// @audit-issue

286:            sell.safeTransfer(bidder, soldAmt); // {qSellTok}	// @audit-issue

290:        boughtAmt = buy.balanceOf(address(this)); // {qBuyTok}	// @audit-issue

291:        buy.safeTransfer(address(origin), boughtAmt); // {qBuyTok}	// @audit-issue

292:        sell.safeTransfer(address(origin), sell.balanceOf(address(this))); // {qSellTok}	// @audit-issue

300:        erc20.safeTransfer(address(origin), erc20.balanceOf(address(this)));	// @audit-issue

335:            return bestPrice.mulDiv(ONE_POINT_FIVE, BASE.powu(uint48(exp.toUint(ROUND))), CEIL);	// @audit-issue

343:            uint192 highPrice = _bestPrice.mul(ONE_POINT_FIVE, CEIL);	// @audit-issue

346:                (highPrice - _bestPrice).mulDiv(progression - TWENTY_PERCENT, TWENTY_FIVE_PERCENT);	// @audit-issue

355:                (_bestPrice - worstPrice).mulDiv(progression - FORTY_FIVE_PERCENT, FIFTY_PERCENT);	// @audit-issue

367:        return sellAmount.mul(price, CEIL).shiftl_toUint(int8(buy.decimals()), CEIL);	// @audit-issue
```
[133](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L133-L133), [173](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L173-L173), [174](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L174-L174), [176](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L176-L176), [177](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L177-L177), [186](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L186-L186), [190](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L190-L190), [214](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L214-L214), [215](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L215-L215), [222](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L222-L222), [250](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L250-L250), [251](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L251-L251), [255](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L255-L255), [257](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L257-L257), [260](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L260-L260), [265](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L265-L265), [286](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L286-L286), [290](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L290-L290), [291](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L291-L291), [292](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L292-L292), [300](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L300-L300), [335](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L335-L335), [343](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L343-L343), [346](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L346-L346), [355](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L355-L355), [367](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L367-L367), 


```solidity
Path: ./contracts/plugins/governance/Governance.sol

56:        return super.votingDelay();	// @audit-issue

60:        return super.votingPeriod();	// @audit-issue

65:        super.setVotingDelay(newVotingDelay); // has onlyGovernance modifier	// @audit-issue

75:        uint256 asMicroPercent = super.proposalThreshold(); // {micro %}	// @audit-issue

78:        uint256 pastSupply = token.getPastTotalSupply(clock() - 1);	// @audit-issue

92:        return super.quorum(timepoint);	// @audit-issue

101:        return GovernorTimelockControl.state(proposalId);	// @audit-issue

162:        return super._executor();	// @audit-issue

171:        return token.getPastVotes(account, timepoint); // {qStRSR}	// @audit-issue

180:        return super.supportsInterface(interfaceId);	// @audit-issue

187:        uint256 pastEra = IStRSRVotes(address(token)).getPastEra(startTimepoint);	// @audit-issue

188:        uint256 currentEra = IStRSRVotes(address(token)).currentEra();	// @audit-issue

197:        return SafeCast.toUint48(block.timestamp);	// @audit-issue
```
[56](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L56-L56), [60](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L60-L60), [65](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L65-L65), [75](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L75-L75), [78](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L78-L78), [92](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L92-L92), [101](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L101-L101), [162](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L162-L162), [171](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L171-L171), [180](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L180-L180), [187](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L187-L187), [188](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L188-L188), [197](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/governance/Governance.sol#L197-L197), 


#### Recommendation

Review your smart contracts to identify and remove `block.number` and `block.timestamp` from the parameters of emitted events. Instead of manually adding these fields, rely on the Ethereum blockchain's inherent inclusion of this information within the block context. Simplify your event definitions to include only the essential data specific to the event's purpose, excluding universally available block metadata.

### Use assembly to check for `address(0)`
In Solidity, it's a common practice to check whether an Ethereum address variable is set to the zero address (`address(0)`) to handle various scenarios, such as token transfers or contract interactions. Typically, this check is performed using a conditional statement like `if (addressVariable == address(0))`.

However, using this approach in high-frequency or gas-sensitive operations can lead to unnecessary gas costs. A more gas-efficient alternative is to use inline assembly to perform the zero address check, which can significantly reduce gas consumption, especially in loops or complex contract logic.

By utilizing inline assembly for this specific check, you can optimize gas usage and make your Solidity code more efficient.

```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

36:        if (address(_roleRegistry) == address(0)) {	// @audit-issue

45:        if (feeRecipient_ == address(0)) {	// @audit-issue
```
[36](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L36-L36), [45](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L45-L45), 


```solidity
Path: ./contracts/registry/VersionRegistry.sol

28:        if (address(_roleRegistry) == address(0)) {	// @audit-issue

42:        if (address(deployer) == address(0)) {	// @audit-issue

49:        if (address(deployments[versionHash]) != address(0)) {	// @audit-issue
```
[28](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L28-L28), [42](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L42-L42), [49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L49-L49), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

35:        if (_asset == address(0)) {	// @audit-issue

41:            if (address(versionRegistry.deployments(versionHash)) == address(0)) {	// @audit-issue

63:        if (_asset == address(0)) {	// @audit-issue

69:            if (address(versionRegistry.deployments(versionHash)) == address(0)) {	// @audit-issue

91:        if (address(versionRegistry.deployments(_versionHash)) == address(0)) {	// @audit-issue

97:            if (asset == address(0)) {	// @audit-issue
```
[35](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L35-L35), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L41-L41), [63](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L63-L63), [69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L69-L69), [91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L91-L91), [97](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L97-L97), 


```solidity
Path: ./contracts/mixins/Auth.sol

92:        require(account != address(0), "cannot grant role to address 0");	// @audit-issue
```
[92](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/Auth.sol#L92-L92), 


```solidity
Path: ./contracts/mixins/ComponentRegistry.sol

36:        require(address(val) != address(0), "invalid RToken address");	// @audit-issue

44:        require(address(val) != address(0), "invalid StRSR address");	// @audit-issue

52:        require(address(val) != address(0), "invalid AssetRegistry address");	// @audit-issue

60:        require(address(val) != address(0), "invalid BasketHandler address");	// @audit-issue

68:        require(address(val) != address(0), "invalid BackingManager address");	// @audit-issue

76:        require(address(val) != address(0), "invalid Distributor address");	// @audit-issue

84:        require(address(val) != address(0), "invalid RSRTrader address");	// @audit-issue

92:        require(address(val) != address(0), "invalid RTokenTrader address");	// @audit-issue

100:        require(address(val) != address(0), "invalid Furnace address");	// @audit-issue

108:        require(address(val) != address(0), "invalid Broker address");	// @audit-issue
```
[36](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L36-L36), [44](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L44-L44), [52](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L52-L52), [60](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L60-L60), [68](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L68-L68), [76](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L76-L76), [84](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L84-L84), [92](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L92-L92), [100](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L100-L100), [108](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/mixins/ComponentRegistry.sol#L108-L108), 


```solidity
Path: ./contracts/p1/Main.sol

39:        require(address(rsr_) != address(0), "invalid RSR address");	// @audit-issue

62:        require(address(versionRegistry_) != address(0), "invalid registry address");	// @audit-issue

63:        require(address(versionRegistry) == address(0), "already set");	// @audit-issue

71:        require(address(registry_) != address(0), "invalid registry address");	// @audit-issue

72:        require(address(assetPluginRegistry) == address(0), "already set");	// @audit-issue

80:        require(address(feeRegistry_) != address(0), "invalid registry address");	// @audit-issue

81:        require(address(daoFeeRegistry) == address(0), "already set");	// @audit-issue

100:        require(address(versionRegistry) != address(0), "no registry");	// @audit-issue

116:        require(address(versionRegistry) != address(0), "no registry");	// @audit-issue
```
[39](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L39-L39), [62](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L62-L62), [63](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L63-L63), [71](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L71-L71), [72](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L72-L72), [80](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L80-L80), [81](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L81-L81), [100](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L100-L100), [116](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Main.sol#L116-L116), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

33:        require(address(tokenToBuy_) != address(0), "invalid token address");	// @audit-issue

157:            require(address(trades[erc20]) == address(0), "trade open");	// @audit-issue
```
[33](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L33-L33), [157](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L157-L157), 


```solidity
Path: ./contracts/p1/Distributor.sol

178:        if (address(daoFeeRegistry) != address(0)) {	// @audit-issue

182:                if (recipient != address(0)) {	// @audit-issue

213:        if (address(daoFeeRegistry) != address(0)) {	// @audit-issue

220:            if (feeRecipient != address(0) && feeNumerator != 0) {	// @audit-issue

241:        require(dest != address(0), "dest cannot be zero");	// @audit-issue
```
[178](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L178-L178), [182](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L182-L182), [213](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L213-L213), [220](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L220-L220), [241](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L241-L241), 


```solidity
Path: ./contracts/p1/AssetRegistry.sol

119:        assets[asset.erc20()] = IAsset(address(0));	// @audit-issue

174:        if (address(assetPluginRegistry) != address(0)) {	// @audit-issue

222:        if (address(assetPluginRegistry) != address(0)) {	// @audit-issue
```
[119](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L119-L119), [174](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L174-L174), [222](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L222-L222), 


```solidity
Path: ./contracts/p1/StRSR.sol

849:        emit Transfer(address(0), account, amount);	// @audit-issue

850:        _afterTokenTransfer(address(0), account, amount);	// @audit-issue

871:        emit Transfer(account, address(0), amount);	// @audit-issue

872:        _afterTokenTransfer(account, address(0), amount);	// @audit-issue

912:        require(addr != address(0), "zero address");	// @audit-issue
```
[849](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L849-L849), [850](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L850-L850), [871](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L871-L871), [872](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L872-L872), [912](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L912-L912), 


```solidity
Path: ./contracts/p1/BasketHandler.sol

550:                if (address(b.erc20s[j]) == address(0)) continue;	// @audit-issue

689:                    erc20s[i] != IERC20(address(0)),	// @audit-issue
```
[550](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L550-L550), [689](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L689-L689), 


```solidity
Path: ./contracts/p1/Deployer.sol

49:            address(rsr_) != address(0) &&	// @audit-issue

50:                address(gnosis_) != address(0) &&	// @audit-issue

51:                address(rsrAsset_) != address(0) &&	// @audit-issue

52:                address(implementations_.main) != address(0) &&	// @audit-issue

53:                address(implementations_.trading.gnosisTrade) != address(0) &&	// @audit-issue

54:                address(implementations_.trading.dutchTrade) != address(0) &&	// @audit-issue

55:                address(implementations_.components.assetRegistry) != address(0) &&	// @audit-issue

56:                address(implementations_.components.backingManager) != address(0) &&	// @audit-issue

57:                address(implementations_.components.basketHandler) != address(0) &&	// @audit-issue

58:                address(implementations_.components.broker) != address(0) &&	// @audit-issue

59:                address(implementations_.components.distributor) != address(0) &&	// @audit-issue

60:                address(implementations_.components.furnace) != address(0) &&	// @audit-issue

61:                address(implementations_.components.rsrTrader) != address(0) &&	// @audit-issue

62:                address(implementations_.components.rTokenTrader) != address(0) &&	// @audit-issue

63:                address(implementations_.components.rToken) != address(0) &&	// @audit-issue

64:                address(implementations_.components.stRSR) != address(0),	// @audit-issue

114:        require(owner != address(0) && owner != address(this), "invalid owner");	// @audit-issue
```
[49](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L49-L49), [50](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L50-L50), [51](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L51-L51), [52](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L52-L52), [53](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L53-L53), [54](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L54-L54), [55](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L55-L55), [56](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L56-L56), [57](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L57-L57), [58](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L58-L58), [59](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L59-L59), [60](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L60-L60), [61](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L61-L61), [62](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L62-L62), [63](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L63-L63), [64](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L64-L64), [114](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L114-L114), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

192:        if (delegatee == address(0) && currentDelegate == address(0)) {	// @audit-issue

195:        } else if (delegatee != address(0) && currentDelegate != delegatee) {	// @audit-issue

236:            if (src != address(0)) {	// @audit-issue

245:            if (dst != address(0)) {	// @audit-issue
```
[192](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L192-L192), [195](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L195-L195), [236](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L236-L236), [245](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L245-L245), 


```solidity
Path: ./contracts/p1/Broker.sol

87:            address(batchTradeImplementation_) != address(0),	// @audit-issue

91:            address(dutchTradeImplementation_) != address(0),	// @audit-issue

98:        emit BatchTradeImplementationSet(ITrade(address(0)), batchTradeImplementation_);	// @audit-issue

99:        emit DutchTradeImplementationSet(ITrade(address(0)), dutchTradeImplementation_);	// @audit-issue

179:        require(address(newGnosis) != address(0), "invalid Gnosis address");	// @audit-issue

188:            address(newTradeImplementation) != address(0),	// @audit-issue

210:            address(newTradeImplementation) != address(0),	// @audit-issue
```
[87](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L87-L87), [91](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L91-L91), [98](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L98-L98), [99](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L99-L99), [179](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L179-L179), [188](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L188-L188), [210](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Broker.sol#L210-L210), 


```solidity
Path: ./contracts/p1/mixins/Trading.sol

96:        require(address(trade) != address(0), "no trade open");	// @audit-issue

126:        assert(address(trades[sell]) == address(0)); // ensure calling class has checked this	// @audit-issue
```
[96](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L96-L96), [126](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Trading.sol#L126-L126), 


```solidity
Path: ./contracts/p1/mixins/Component.sol

34:        require(address(main_) != address(0), "main is zero address");	// @audit-issue
```
[34](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/Component.sol#L34-L34), 


```solidity
Path: ./contracts/p1/mixins/RecollateralizationLib.sol

50:        if (address(trade.sell) == address(0) || address(trade.buy) == address(0)) {	// @audit-issue

357:        if (address(trade.sell) == address(0) && address(trade.buy) != address(0)) {	// @audit-issue
```
[50](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L50-L50), [357](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L357-L357), 


```solidity
Path: ./contracts/p1/mixins/BasketLib.sol

296:        if (address(erc20) == address(0)) return false;	// @audit-issue
```
[296](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/BasketLib.sol#L296-L296), 


```solidity
Path: ./contracts/plugins/trading/GnosisTrade.sol

101:        assert(origin_ != address(0));	// @audit-issue

154:            address(0),	// @audit-issue
```
[101](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L101-L101), [154](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/GnosisTrade.sol#L154-L154), 


```solidity
Path: ./contracts/plugins/trading/DutchTrade.sol

165:        assert(address(sell_) != address(0) && address(buy_) != address(0) && auctionLength >= 60);	// @audit-issue

200:        require(bidder == address(0), "bid already received");	// @audit-issue

236:        require(bidder == address(0), "bid already received");	// @audit-issue

280:        require(bidder != address(0) || block.timestamp > endTime, "auction not over");	// @audit-issue

306:        return status == TradeStatus.OPEN && (bidder != address(0) || block.timestamp > endTime);	// @audit-issue
```
[165](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L165-L165), [200](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L200-L200), [236](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L236-L236), [280](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L280-L280), [306](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L306-L306), 


#### Recommendation

To optimize gas usage in your Solidity code, consider using inline assembly for checking `address(0)`. This approach can significantly reduce gas costs, especially in high-frequency or gas-sensitive operations, leading to more efficient contract execution.

### Use assembly to check for `0`
Using assembly to check for zero can save gas by allowing more direct access to the evm and reducing some of the overhead associated with high-level operations in solidity.

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

57:        if (amount > 0) {	// @audit-issue

61:        } else if (amount < 0) {	// @audit-issue
```
[43](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L43-L43), [57](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L57-L57), [61](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Throttle.sol#L61-L61), 


```solidity
Path: ./contracts/libraries/Fixed.sol

212:        if (x == 0) return 0;	// @audit-issue

217:        return _safeWrap(decimals >= 0 ? x * coeff : _divrnd(x, coeff, rounding));	// @audit-issue

320:        if (x_ == FIX_ONE || y == 0) return FIX_ONE;	// @audit-issue

393:        if (x == 0) return 0; // always computable, no matter what decimals is	// @audit-issue

400:        return decimals >= 0 ? uint256(x * coeff) : uint256(_divrnd(x, coeff, rounding));	// @audit-issue

501:        if (a == 0 || b == 0) return 0;	// @audit-issue

549:        if (a == 0) return 0;	// @audit-issue

550:        if (b == 0) return FIX_MAX;	// @audit-issue

567:        if (a == 0 || b == 0) return 0;	// @audit-issue

568:        if (a == FIX_MAX || b == FIX_MAX || c == 0) return FIX_MAX;	// @audit-issue

598:                if (mm != 0) result_256 += 1;	// @audit-issue
```
[212](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L212-L212), [217](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L217-L217), [320](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L320-L320), [393](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L393-L393), [400](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L400-L400), [501](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L501-L501), [549](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L549-L549), [550](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L550-L550), [567](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L567-L567), [568](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L568-L568), [598](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Fixed.sol#L598-L598), 


```solidity
Path: ./contracts/libraries/Allowance.sol

28:        require(token.allowance(address(this), spender) == 0, "allowance not 0");	// @audit-issue

30:        if (value == 0) return;	// @audit-issue
```
[28](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L28-L28), [30](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/libraries/Allowance.sol#L30-L30), 


```solidity
Path: ./contracts/p1/RevenueTrader.sol

62:            if (errData.length == 0) revert(); // solhint-disable-line reason-string	// @audit-issue

79:            require(revTotals.rsrTotal == 0, "rsrTotal > 0");	// @audit-issue

81:            require(revTotals.rTokenTotal == 0, "rTokenTotal > 0");	// @audit-issue

115:        require(len != 0, "empty erc20s list");	// @audit-issue

119:            (tokenToBuy == rsr && revTotals.rsrTotal != 0) ||	// @audit-issue

120:                (address(tokenToBuy) == address(rToken) && revTotals.rTokenTotal != 0),	// @audit-issue

150:        require(buyHigh != 0 && buyHigh != FIX_MAX, "buy asset price unknown");	// @audit-issue

158:            require(erc20.balanceOf(address(this)) != 0, "0 balance");	// @audit-issue
```
[62](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L62-L62), [79](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L79-L79), [81](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L81-L81), [115](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L115-L115), [119](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L119-L119), [120](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L120-L120), [150](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L150-L150), [158](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RevenueTrader.sol#L158-L158), 


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

264:            require(0 < targetAmts[i] && targetAmts[i] <= MAX_TARGET_AMT, "invalid target amount");	// @audit-issue

319:        if (disabled || size == 0) return CollateralStatus.DISABLED;	// @audit-issue

372:            if (pegPrice == 0) return FIX_ONE;	// @audit-issue

397:        if (refPerTok == 0) return FIX_MAX;	// @audit-issue

427:                if (qty == 0) continue;	// @audit-issue

450:                if (errData.length == 0) revert(); // solhint-disable-line reason-string	// @audit-issue

579:                        if (errData.length == 0) revert(); // solhint-disable-line reason-string	// @audit-issue

612:        if (length == 0 || disabled) return BasketRange(FIX_ZERO, FIX_MAX);	// @audit-issue

728:                if (errData.length == 0) revert(); // solhint-disable-line reason-string	// @audit-issue
```
[233](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L233-L233), [238](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L238-L238), [264](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L264-L264), [319](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L319-L319), [372](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L372-L372), [397](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L397-L397), [427](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L427-L427), [450](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L450-L450), [579](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L579-L579), [612](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L612-L612), [728](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/BasketHandler.sol#L728-L728), 


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

205:        if (deltaTop < 0) {	// @audit-issue

302:                if (high == 0) continue; // skip over worthless assets	// @audit-issue

362:                high != 0 &&	// @audit-issue
```
[58](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L58-L58), [117](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L117-L117), [148](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L148-L148), [205](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L205-L205), [302](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L302-L302), [362](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/mixins/RecollateralizationLib.sol#L362-L362), 


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

To optimize gas usage in your Solidity code, consider using inline assembly for checking `0`. This approach can significantly reduce gas costs, especially in high-frequency or gas-sensitive operations, leading to more efficient contract execution.

### Use assembly to write `address` storage values
Using assembly `{ sstore(state.slot, addr)}` instead of `state = addr` can save gas.


```solidity
Path: ./contracts/registry/DAOFeeRegistry.sol

40:        roleRegistry = _roleRegistry;	// @audit-issue

41:        feeRecipient = _feeRecipient;	// @audit-issue

52:        feeRecipient = feeRecipient_;	// @audit-issue
```
[40](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L40-L40), [41](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L41-L41), [52](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/DAOFeeRegistry.sol#L52-L52), 


```solidity
Path: ./contracts/registry/VersionRegistry.sol

32:        roleRegistry = _roleRegistry;	// @audit-issue

53:        deployments[versionHash] = deployer;	// @audit-issue
```
[32](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L32-L32), [53](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/VersionRegistry.sol#L53-L53), 


```solidity
Path: ./contracts/registry/AssetPluginRegistry.sol

27:        versionRegistry = VersionRegistry(_versionRegistry);	// @audit-issue
```
[27](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/registry/AssetPluginRegistry.sol#L27-L27), 


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
Path: ./contracts/p1/AssetRegistry.sol

240:        assets[erc20] = asset;	// @audit-issue
```
[240](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/AssetRegistry.sol#L240-L240), 


```solidity
Path: ./contracts/p1/Deployer.sol

68:        rsr = rsr_;	// @audit-issue

69:        gnosis = gnosis_;	// @audit-issue

70:        rsrAsset = rsrAsset_;	// @audit-issue

71:        _implementations = implementations_;	// @audit-issue
```
[68](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L68-L68), [69](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L69-L69), [70](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L70-L70), [71](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Deployer.sol#L71-L71), 


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
```
[172](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/plugins/trading/DutchTrade.sol#L172-L172), 


#### Recommendation

To reduce gas costs in your Solidity code, consider using assembly with `{ sstore(state.slot, addr) }` for writing `address` storage values instead of `state = addr`. This approach can result in significant gas savings.

### Use assembly to emit an `event`
To efficiently emit events, it's possible to utilize assembly by making use of scratch space and the free memory pointer. This approach has the advantage of potentially avoiding the costs associated with memory expansion.

However, it's important to note that in order to safely optimize this process, it is preferable to cache and restore the free memory pointer.

A good example of such practice can be seen in [Solady's](https://github.com/Vectorized/solady/blob/main/src/tokens/ERC1155.sol#L167) codebase.


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

163:            transfers[numTransfers] = Transfer({ addrTo: addrTo, amount: transferAmt });	// @audit-issue

166:        emit RevenueDistributed(erc20, caller, amount);	// @audit-issue

260:        emit DistributionSet(dest, share.rTokenDist, share.rsrDist);	// @audit-issue
```
[163](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L163-L163), [166](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L166-L166), [260](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/Distributor.sol#L260-L260), 


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

282:        emit Redemption(_msgSender(), recipient, amount, baskets);	// @audit-issue

374:        emit Melted(amtRToken);	// @audit-issue

399:        emit BasketsNeededChanged(basketsNeeded, basketsNeeded_);	// @audit-issue

457:        emit IssuanceThrottleSet(issuanceThrottle.params, params);	// @audit-issue

468:        emit RedemptionThrottleSet(redemptionThrottle.params, params);	// @audit-issue

492:        emit BasketsNeededChanged(basketsNeeded, basketsNeeded + amtBaskets);	// @audit-issue

511:        emit BasketsNeededChanged(basketsNeeded, basketsNeeded - amtBaskets);	// @audit-issue
```
[136](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L136-L136), [204](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L204-L204), [282](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L282-L282), [374](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L374-L374), [399](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L399-L399), [457](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L457-L457), [468](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L468-L468), [492](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L492-L492), [511](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/RToken.sol#L511-L511), 


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

To optimize event emission in your Solidity code, consider using assembly with scratch space and the free memory pointer. This approach can help reduce gas costs by avoiding memory expansion expenses. However, it's crucial to ensure safe optimization by caching and restoring the free memory pointer, as demonstrated in examples like Solady's codebase.

### Use assembly to validate `msg.sender`
We can use assembly to efficiently validate `msg.sender` with the least amount of opcodes necessary. For more details check the following report [Here](https://code4rena.com/reports/2023-05-juicebox#g-06-use-assembly-to-validate-msgsender)


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

To optimize the validation of `msg.sender` in your Solidity code, consider using assembly to achieve this with the minimum number of opcodes required. You can refer to the detailed report [Here](https://code4rena.com/reports/2023-05-juicebox#g-06-use-assembly-to-validate-msgsender) for more insights and examples on efficient implementation.

### Use assembly to write hashes
Considering using [assembly](https://medium.com/@kalexotsu/understanding-solidity-assembly-hashing-a-string-from-calldata-fbd2ece82263) to write hashes, as it's possible to save a considerable amount of gas.


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


```solidity
Path: ./contracts/p1/StRSR.sol

933:        bytes32 structHash = keccak256(	// @audit-issue
```
[933](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSR.sol#L933-L933), 


```solidity
Path: ./contracts/p1/StRSRVotes.sol

176:            _hashTypedDataV4(keccak256(abi.encode(_DELEGATE_TYPEHASH, delegatee, nonce, expiry))),	// @audit-issue
```
[176](https://github.com/code-423n4/2024-07-reserve/blob/3f133997e186465f4904553b0f8e86ecb7bbacbf/./contracts/p1/StRSRVotes.sol#L176-L176), 


#### Recommendation

To achieve gas savings in your Solidity code when writing hashes, consider using assembly, as it can significantly reduce gas consumption. You can refer to this [article](https://medium.com/@kalexotsu/understanding-solidity-assembly-hashing-a-string-from-calldata-fbd2ece82263) for insights on how to efficiently implement hash operations.