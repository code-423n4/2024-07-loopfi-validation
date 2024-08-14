## Summary

| |Issue|Instances| 
|-|:-|:-:|
| [[L-01](#l-01)] | Consider adding validation of user inputs | 133| 
| [[L-02](#l-02)] | Array is `push()`ed but not `pop()`ed | 5| 
| [[L-03](#l-03)] | Consider bounding input array length | 3| 
| [[L-04](#l-04)] | Constant decimal values | 2| 
| [[L-05](#l-05)] | Duplicate import statements | 2| 
| [[L-06](#l-06)] | Possible reentrancy with callback on transfer tokens | 8| 
| [[L-07](#l-07)] | Events may be emitted out of order due to reentrancy | 23| 
| [[L-08](#l-08)] | External calls in an unbounded loop can result in a DoS (Not captured by 4nalyzer) | 5| 
| [[L-09](#l-09)] | Functions calling contracts/addresses with transfer hooks are missing reentrancy guards | 22| 
| [[L-10](#l-10)] | Input array lengths may differ | 19| 
| [[L-11](#l-11)] | Mapping arrays can grow in size without a way to shrink them | 2| 
| [[L-12](#l-12)] | Missing zero address check in constructor | 14| 
| [[L-13](#l-13)] | Missing contract-existence checks before low-level calls | 2| 
| [[L-14](#l-14)] | Named return variable used before assignment | 1| 
| [[L-15](#l-15)] | The `nonReentrant` modifier should be placed first in a function declaration | 6| 
| [[L-16](#l-16)] | `onlyOwner` functions not accessible if owner renounces ownership | 30| 
| [[L-17](#l-17)] | Variables shadowing other definitions | 28| 
| [[L-18](#l-18)] | Sending tokens in a loop | 3| 
| [[L-19](#l-19)] | File allows a version of solidity that is susceptible to `.selector` related optimizer bug | 14| 
| [[L-20](#l-20)] | State variables not capped at reasonable values | 12| 
| [[L-21](#l-21)] | Consider implementing two-step procedure for updating protocol addresses | 17| 
| [[L-22](#l-22)] | Using zero as a parameter | 13| 

### Low Risk Issues

### [L-01]<a name="l-01"></a> Consider adding validation of user inputs

There are no validations done on the arguments below. Consider that the Solidity [documentation](https://docs.soliditylang.org/en/latest/control-structures.html#panic-via-assert-and-error-via-require) states that `Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix`. This means that there should be explicit checks for expected ranges of inputs. Underflows/overflows result in panics should not be used as range checks, and allowing funds to be sent to `0x0`, which is the default value of address variables and has many gotchas, should be avoided.

*There are 133 instance(s) of this issue:*

```solidity
📁 File: ./src/CDPVault.sol

// @audit missing checks for -->  user
25:     function enter(address user, uint256 amount) external;

// @audit missing checks for -->  user
27:     function exit(address user, uint256 amount) external;

// @audit missing checks for -->  user
29:     function addAvailable(address user, int256 amount) external;

// @audit missing checks for -->  data
208:     function setParameter(bytes32 parameter, address data) external whenNotPaused onlyRole(VAULT_CONFIG_ROLE) {

// @audit missing checks for -->  to
223:     function deposit(address to, uint256 amount) external whenNotPaused returns (uint256 tokenAmount) {

// @audit missing checks for -->  to
239:     function withdraw(address to, uint256 amount) external whenNotPaused returns (uint256 tokenAmount) {

// @audit missing checks for -->  borrower, position
256:     function borrow(address borrower, address position, uint256 amount) external {

// @audit missing checks for -->  borrower, position
272:     function repay(address borrower, address position, uint256 amount) external {

// @audit missing checks for -->  owner, collateralizer, creditor
367:     function modifyCollateralAndDebt(
368:         address owner,
369:         address collateralizer,
370:         address creditor,
371:         int256 deltaCollateral,
372:         int256 deltaDebt
373:     ) public {

// @audit missing checks for -->  position
729:     function virtualDebt(address position) external view returns (uint256) {

// @audit missing checks for -->  position
745:     function quotasInterest(address position) external view returns (uint256) {

// @audit missing checks for -->  position
751:     function getDebtData(address position) external view returns (DebtData memory) {

// @audit missing checks for -->  position
756:     function getDebtInfo(
757:         address position
758:     ) external view returns (uint256 debt, uint256 accruedInterest, uint256 cumulativeQuotaInterest) {

```


*GitHub* : [25](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L25-L25), [27](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L27-L27), [29](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L29-L29), [208](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L208-L208), [223](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L223-L223), [239](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L239-L239), [256](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L256-L256), [272](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L272-L272), [367](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L367-L373), [729](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L729-L729), [745](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L745-L745), [751](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L751-L751), [756](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L756-L758)

```solidity
📁 File: ./src/Flashlender.sol

// @audit missing checks for -->  token
64:     function maxFlashLoan(address token) external view override returns (uint256 max) {

// @audit missing checks for -->  token
75:     function flashFee(address token, uint256 amount) external view override returns (uint256) {

// @audit missing checks for -->  receiver, token
87:     function flashLoan(
88:         IERC3156FlashBorrower receiver,
89:         address token,
90:         uint256 amount,
91:         bytes calldata data
92:     ) external override nonReentrant returns (bool) {

// @audit missing checks for -->  receiver
117:     function creditFlashLoan(
118:         ICreditFlashBorrower receiver,
119:         uint256 amount,
120:         bytes calldata data
121:     ) external override nonReentrant returns (bool) {

```


*GitHub* : [64](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/Flashlender.sol#L64-L64), [75](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/Flashlender.sol#L75-L75), [87](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/Flashlender.sol#L87-L92), [117](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/Flashlender.sol#L117-L121)

```solidity
📁 File: ./src/PoolV3.sol

// @audit missing checks for -->  receiver
231:     function deposit(
232:         uint256 assets,
233:         address receiver
234:     )
235:         public
236:         override(ERC4626, IERC4626)
237:         whenNotPaused // U:[LP-2A]
238:         nonReentrant // U:[LP-2B]
239:         nonZeroAddress(receiver) // U:[LP-5]
240:         returns (uint256 shares)
241:     {

// @audit missing checks for -->  receiver
248:     function depositWithReferral(
249:         uint256 assets,
250:         address receiver,
251:         uint256 referralCode
252:     ) external override returns (uint256 shares) {

// @audit missing checks for -->  receiver
261:     function mint(
262:         uint256 shares,
263:         address receiver
264:     )
265:         public
266:         override(ERC4626, IERC4626)
267:         whenNotPaused // U:[LP-2A]
268:         nonReentrant // U:[LP-2B]
269:         nonZeroAddress(receiver) // U:[LP-5]
270:         returns (uint256 assets)
271:     {

// @audit missing checks for -->  receiver
278:     function mintWithReferral(
279:         uint256 shares,
280:         address receiver,
281:         uint256 referralCode
282:     ) external override returns (uint256 assets) {

// @audit missing checks for -->  receiver, owner
292:     function withdraw(
293:         uint256 assets,
294:         address receiver,
295:         address owner
296:     )
297:         public
298:         override(ERC4626, IERC4626)
299:         whenNotPaused // U:[LP-2A]
300:         whenNotLocked
301:         nonReentrant // U:[LP-2B]
302:         nonZeroAddress(receiver) // U:[LP-5]
303:         returns (uint256 shares)
304:     {

// @audit missing checks for -->  receiver, owner
316:     function redeem(
317:         uint256 shares,
318:         address receiver,
319:         address owner
320:     )
321:         public
322:         override(ERC4626, IERC4626)
323:         whenNotPaused // U:[LP-2A]
324:         whenNotLocked
325:         nonReentrant // U:[LP-2B]
326:         nonZeroAddress(receiver) // U:[LP-5]
327:         returns (uint256 assets)
328:     {

// @audit missing checks for -->  owner
366:     function maxWithdraw(address owner) public view override(ERC4626, IERC4626) returns (uint256) {

// @audit missing checks for -->  owner
376:     function maxRedeem(address owner) public view override(ERC4626, IERC4626) returns (uint256) {

// @audit missing checks for -->  creditManager
456:     function creditManagerBorrowed(address creditManager) external view override returns (uint256) {

// @audit missing checks for -->  creditManager
461:     function creditManagerDebtLimit(address creditManager) external view override returns (uint256) {

// @audit missing checks for -->  creditManager
466:     function creditManagerBorrowable(address creditManager) external view override returns (uint256 borrowable) {

// @audit missing checks for -->  creditAccount
484:     function lendCreditAccount(
485:         uint256 borrowedAmount,
486:         address creditAccount
487:     )
488:         external
489:         override
490:         creditManagerOnly // U:[LP-2C]
491:         whenNotPaused // U:[LP-2A]
492:         nonReentrant // U:[LP-2B]
493:     {

// @audit missing checks for -->  newInterestRateModel
746:     function setInterestRateModel(
747:         address newInterestRateModel
748:     )
749:         external
750:         override
751:         configuratorOnly // U:[LP-2C]
752:         nonZeroAddress(newInterestRateModel) // U:[LP-22A]
753:     {

// @audit missing checks for -->  newPoolQuotaKeeper
761:     function setPoolQuotaKeeper(
762:         address newPoolQuotaKeeper
763:     )
764:         external
765:         override
766:         configuratorOnly // U:[LP-2C]
767:         nonZeroAddress(newPoolQuotaKeeper) // U:[LP-23A]
768:     {

// @audit missing checks for -->  creditManager
797:     function setCreditManagerDebtLimit(
798:         address creditManager,
799:         uint256 newLimit
800:     )
801:         external
802:         override
803:         controllerOnly // U:[LP-2C]
804:         nonZeroAddress(creditManager) // U:[LP-25A]
805:     {

// @audit missing checks for -->  account
838:     function setAllowed(address account, bool status) external controllerOnly {

// @audit missing checks for -->  account
850:     function isAllowed(address account) external view returns (bool) {

```


*GitHub* : [231](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L231-L241), [248](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L248-L252), [261](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L261-L271), [278](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L278-L282), [292](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L292-L304), [316](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L316-L328), [366](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L366-L366), [376](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L376-L376), [456](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L456-L456), [461](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L461-L461), [466](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L466-L466), [484](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L484-L493), [746](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L746-L753), [761](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L761-L768), [797](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L797-L805), [838](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L838-L838), [850](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L850-L850)

```solidity
📁 File: ./src/Silo.sol

// @audit missing checks for -->  to
28:     function withdraw(address to, uint256 amount) external onlyStakingVault {

```


*GitHub* : [28](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/Silo.sol#L28-L28)

```solidity
📁 File: ./src/StakingLPEth.sol

// @audit missing checks for -->  receiver, _owner
66:     function withdraw(
67:         uint256 assets,
68:         address receiver,
69:         address _owner
70:     ) public virtual override ensureCooldownOff returns (uint256) {

// @audit missing checks for -->  receiver, _owner
77:     function redeem(
78:         uint256 shares,
79:         address receiver,
80:         address _owner
81:     ) public virtual override ensureCooldownOff returns (uint256) {

// @audit missing checks for -->  receiver
88:     function unstake(address receiver) external {

```


*GitHub* : [66](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/StakingLPEth.sol#L66-L70), [77](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/StakingLPEth.sol#L77-L81), [88](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/StakingLPEth.sol#L88-L88)

```solidity
📁 File: ./src/VaultRegistry.sol

// @audit missing checks for -->  vault
39:     function addVault(ICDPVault vault) external override(IVaultRegistry) onlyRole(VAULT_MANAGER_ROLE) {

// @audit missing checks for -->  vault
49:     function removeVault(ICDPVault vault) external override(IVaultRegistry) onlyRole(VAULT_MANAGER_ROLE) {

// @audit missing checks for -->  user
65:     function getUserTotalDebt(address user) external view override(IVaultRegistry) returns (uint256 totalNormalDebt) {

// @audit missing checks for -->  vault
95:     function isVaultRegistered(address vault) external view returns (bool) {

```


*GitHub* : [39](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/VaultRegistry.sol#L39-L39), [49](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/VaultRegistry.sol#L49-L49), [65](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/VaultRegistry.sol#L65-L65), [95](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/VaultRegistry.sol#L95-L95)

```solidity
📁 File: ./src/oracle/BalancerOracle.sol

// @audit missing checks for -->  admin, manager
98:     function initialize(address admin, address manager) external initializer {

```


*GitHub* : [98](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/BalancerOracle.sol#L98-L98)

```solidity
📁 File: ./src/oracle/ChainlinkOracle.sol

// @audit missing checks for -->  _tokens
45:     function setOracles(address[] calldata _tokens, Oracle[] calldata _oracles) external onlyRole(DEFAULT_ADMIN_ROLE) {

// @audit missing checks for -->  admin, manager
58:     function initialize(address admin, address manager) external initializer {

// @audit missing checks for -->  token
79:     function getStatus(address token) public view virtual override returns (bool status) {

// @audit missing checks for -->  token
87:     function spot(address token) external view virtual override returns (uint256 price) {

```


*GitHub* : [45](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/ChainlinkOracle.sol#L45-L45), [58](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/ChainlinkOracle.sol#L58-L58), [79](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/ChainlinkOracle.sol#L79-L79), [87](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/ChainlinkOracle.sol#L87-L87)

```solidity
📁 File: ./src/oracle/PendleLPOracle.sol

// @audit missing checks for -->  admin, manager
72:     function initialize(address admin, address manager) external initializer {

```


*GitHub* : [72](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/PendleLPOracle.sol#L72-L72)

```solidity
📁 File: ./src/proxy/PoolAction.sol

// @audit missing checks for -->  from, permitParams
75:     function transferAndJoin(
76:         address from,
77:         PermitParams[] calldata permitParams,
78:         PoolActionParams calldata poolActionParams
79:     ) external {

// @audit missing checks for -->  joinToken, upFrontToken, poolToken
181:     function updateLeverJoin(
182:         PoolActionParams memory poolActionParams,
183:         address joinToken,
184:         address upFrontToken,
185:         uint256 flashLoanAmount,
186:         uint256 upfrontAmount,
187:         address poolToken
188:     ) external pure returns (PoolActionParams memory outParams) {

```


*GitHub* : [75](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PoolAction.sol#L75-L79), [181](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PoolAction.sol#L181-L188)

```solidity
📁 File: ./src/proxy/PositionAction.sol

// @audit missing checks for -->  position, vault
189:     function deposit(
190:         address position,
191:         address vault,
192:         CollateralParams calldata collateralParams,
193:         PermitParams calldata permitParams
194:     ) external onlyRegisteredVault(vault) onlyDelegatecall {

// @audit missing checks for -->  position, vault
202:     function withdraw(
203:         address position,
204:         address vault,
205:         CollateralParams calldata collateralParams
206:     ) external onlyRegisteredVault(vault) onlyDelegatecall {

// @audit missing checks for -->  position, vault
214:     function borrow(address position, address vault, CreditParams calldata creditParams) external onlyRegisteredVault(vault) onlyDelegatecall {

// @audit missing checks for -->  position, vault
223:     function repay(
224:         address position,
225:         address vault,
226:         CreditParams calldata creditParams,
227:         PermitParams calldata permitParams
228:     ) external onlyRegisteredVault(vault) onlyDelegatecall {

// @audit missing checks for -->  position, vault
237:     function depositAndBorrow(
238:         address position,
239:         address vault,
240:         CollateralParams calldata collateralParams,
241:         CreditParams calldata creditParams,
242:         PermitParams calldata permitParams
243:     ) external onlyRegisteredVault(vault) onlyDelegatecall {

// @audit missing checks for -->  position, vault
254:     function withdrawAndRepay(
255:         address position,
256:         address vault,
257:         CollateralParams calldata collateralParams,
258:         CreditParams calldata creditParams,
259:         PermitParams calldata permitParams
260:     ) external onlyRegisteredVault(vault) onlyDelegatecall {

// @audit missing checks for -->  targets
269:     function multisend(
270:         address[] calldata targets,
271:         bytes[] calldata data,
272:         bool[] calldata delegateCall
273:     ) external onlyDelegatecall {

// @audit missing checks for -->  upFrontToken, collateralizer
296:     function increaseLever(
297:         LeverParams calldata leverParams,
298:         address upFrontToken,
299:         uint256 upFrontAmount,
300:         address collateralizer,
301:         PermitParams calldata permitParams
302:     ) external onlyDelegatecall {

```


*GitHub* : [189](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L189-L194), [202](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L202-L206), [214](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L214-L214), [223](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L223-L228), [237](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L237-L243), [254](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L254-L260), [269](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L269-L273), [296](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L296-L302)

```solidity
📁 File: ./src/proxy/SwapAction.sol

// @audit missing checks for -->  from
93:     function transferAndSwap(
94:         address from,
95:         PermitParams calldata permitParams,
96:         SwapParams calldata swapParams
97:     ) external returns (uint256) {

```


*GitHub* : [93](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/SwapAction.sol#L93-L97)

```solidity
📁 File: ./src/quotas/GaugeV3.sol

// @audit missing checks for -->  tokens
97:     function getRates(address[] calldata tokens) external view override returns (uint16[] memory rates) {

// @audit missing checks for -->  user
126:     function vote(
127:         address user,
128:         uint96 votes,
129:         bytes calldata extraData
130:     )
131:         external
132:         override
133:         onlyVoter // U:[GA-02]
134:     {

// @audit missing checks for -->  user
169:     function unvote(
170:         address user,
171:         uint96 votes,
172:         bytes calldata extraData
173:     )
174:         external
175:         override
176:         onlyVoter // U:[GA-02]
177:     {

// @audit missing checks for -->  token
232:     function addQuotaToken(
233:         address token,
234:         uint16 minRate,
235:         uint16 maxRate
236:     )
237:         external
238:         override
239:         nonZeroAddress(token) // U:[GA-04]
240:         configuratorOnly // U:[GA-03]
241:     {

// @audit missing checks for -->  token
264:     function changeQuotaMinRate(
265:         address token,
266:         uint16 minRate
267:     )
268:         external
269:         override
270:         nonZeroAddress(token) // U: [GA-04]
271:         controllerOnly // U: [GA-03]
272:     {

// @audit missing checks for -->  token
278:     function changeQuotaMaxRate(
279:         address token,
280:         uint16 maxRate
281:     )
282:         external
283:         override
284:         nonZeroAddress(token) // U: [GA-04]
285:         controllerOnly // U: [GA-03]
286:     {

// @audit missing checks for -->  token
312:     function isTokenAdded(address token) public view override returns (bool) {

```


*GitHub* : [97](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/GaugeV3.sol#L97-L97), [126](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/GaugeV3.sol#L126-L134), [169](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/GaugeV3.sol#L169-L177), [232](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/GaugeV3.sol#L232-L241), [264](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/GaugeV3.sol#L264-L272), [278](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/GaugeV3.sol#L278-L286), [312](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/GaugeV3.sol#L312-L312)

```solidity
📁 File: ./src/quotas/PoolQuotaKeeperV3.sol

// @audit missing checks for -->  token
87:     function cumulativeIndex(address token) public view override returns (uint192) {

// @audit missing checks for -->  token
95:     function getQuotaRate(address token) external view override returns (uint16) {

// @audit missing checks for -->  token
105:     function isQuotedToken(address token) external view override returns (bool) {

// @audit missing checks for -->  token
110:     function getTokenQuotaParams(
111:         address token
112:     )
113:         external
114:         view
115:         override
116:         returns (
117:             uint16 rate,
118:             uint192 cumulativeIndexLU,
119:             uint16 quotaIncreaseFee,
120:             uint96 totalQuoted,
121:             uint96 limit,
122:             bool isActive
123:         )
124:     {

// @audit missing checks for -->  token
161:     function addQuotaToken(
162:         address token
163:     )
164:         external
165:         override
166:         gaugeOnly // U:[PQK-3]
167:     {

// @audit missing checks for -->  _gauge
225:     function setGauge(
226:         address _gauge
227:     )
228:         external
229:         override
230:         configuratorOnly // U:[PQK-2]
231:     {

// @audit missing checks for -->  token, vault
238:     function setCreditManager(
239:         address token,
240:         address vault
241:     )
242:         external
243:         override
244:         configuratorOnly // U:[PQK-2]
245:     {

```


*GitHub* : [87](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/PoolQuotaKeeperV3.sol#L87-L87), [95](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/PoolQuotaKeeperV3.sol#L95-L95), [105](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/PoolQuotaKeeperV3.sol#L105-L105), [110](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/PoolQuotaKeeperV3.sol#L110-L124), [161](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/PoolQuotaKeeperV3.sol#L161-L167), [225](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/PoolQuotaKeeperV3.sol#L225-L231), [238](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/PoolQuotaKeeperV3.sol#L238-L245)

```solidity
📁 File: ./src/reward/ChefIncentivesController.sol

// @audit missing checks for -->  _bountyManager
272:     function setBountyManager(address _bountyManager) external onlyOwner {

// @audit missing checks for -->  _token
301:     function addPool(address _token, uint256 _allocPoint) external {

// @audit missing checks for -->  _tokens
318:     function batchUpdateAllocPoint(address[] calldata _tokens, uint256[] calldata _allocPoints) external onlyOwner {

// @audit missing checks for -->  tokenAddress
430:     function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyOwner {

// @audit missing checks for -->  _user, _tokens
493:     function pendingRewards(address _user, address[] memory _tokens) public view returns (uint256[] memory) {

// @audit missing checks for -->  _user, _tokens
518:     function claim(address _user, address[] memory _tokens) public whenNotPaused {

// @audit missing checks for -->  _contract
570:     function setEligibilityExempt(address _contract, bool _value) public {

// @audit missing checks for -->  _address
581:     function setContractAuthorization(address _address, bool _authorize) external onlyOwner {

// @audit missing checks for -->  _user
596:     function handleActionAfter(address _user, uint256 _balance, uint256 _totalSupply) external {

// @audit missing checks for -->  _user
660:     function handleActionBefore(address _user) external {}

// @audit missing checks for -->  _user
667:     function beforeLockUpdate(address _user) external {}

// @audit missing checks for -->  _user
674:     function afterLockUpdate(address _user) external {

// @audit missing checks for -->  _user
715:     function hasEligibleDeposits(address _user) public view returns (bool hasDeposits) {

// @audit missing checks for -->  _user
781:     function claimBounty(address _user, bool _execute) public returns (bool issueBaseBounty) {

// @audit missing checks for -->  _tokens
813:     function manualStopEmissionsFor(address _user, address[] memory _tokens) public isWhitelisted {

// @audit missing checks for -->  _user
844:     function manualStopAllEmissionsFor(address _user) external isWhitelisted {

// @audit missing checks for -->  _user
942:     function claimAll(address _user) external {

// @audit missing checks for -->  _user
951:     function allPendingRewards(address _user) public view returns (uint256 pending) {

// @audit missing checks for -->  user
1005:     function setAddressWLstatus(address user, bool status) external onlyOwner {

```


*GitHub* : [272](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L272-L272), [301](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L301-L301), [318](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L318-L318), [430](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L430-L430), [493](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L493-L493), [518](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L518-L518), [570](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L570-L570), [581](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L581-L581), [596](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L596-L596), [660](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L660-L660), [667](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L667-L667), [674](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L674-L674), [715](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L715-L715), [781](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L781-L781), [813](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L813-L813), [844](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L844-L844), [942](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L942-L942), [951](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L951-L951), [1005](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L1005-L1005)

```solidity
📁 File: ./src/reward/EligibilityDataProvider.sol

// @audit missing checks for -->  _user
164:     function setDqTime(address _user, uint256 _time) external {

// @audit missing checks for -->  user
177:     function lockedUsdValue(address user) public view returns (uint256) {

// @audit missing checks for -->  user
187:     function requiredUsdValue(address user) public view returns (uint256 required) {

// @audit missing checks for -->  _user
197:     function isEligibleForRewards(address _user) public view returns (bool) {

// @audit missing checks for -->  _user
208:     function getDqTime(address _user) public view returns (uint256) {

// @audit missing checks for -->  user
220:     function lastEligibleTime(address user) public view returns (uint256 lastEligibleTimestamp) {

```


*GitHub* : [164](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/EligibilityDataProvider.sol#L164-L164), [177](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/EligibilityDataProvider.sol#L177-L177), [187](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/EligibilityDataProvider.sol#L187-L187), [197](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/EligibilityDataProvider.sol#L197-L197), [208](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/EligibilityDataProvider.sol#L208-L208), [220](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/EligibilityDataProvider.sol#L220-L220)

```solidity
📁 File: ./src/reward/MultiFeeDistribution.sol

// @audit missing checks for -->  _rewardToken
365:     function removeReward(address _rewardToken) external {

// @audit missing checks for -->  onBehalfOf
484:     function stake(uint256 amount, address onBehalfOf, uint256 typeIndex) external {

// @audit missing checks for -->  user
496:     function vestTokens(address user, uint256 amount, bool withPenalty) external whenNotPaused {

// @audit missing checks for -->  address_
674:     function withdrawExpiredLocksForWithOptions(
675:         address address_,
676:         uint256 limit_,
677:         bool isRelockAction_
678:     ) external returns (uint256) {

// @audit missing checks for -->  user
689:     function zapVestingToLp(address user) external returns (uint256 zapped) {

// @audit missing checks for -->  onBehalf
725:     function claimFromConverter(address onBehalf) external whenNotPaused {

// @audit missing checks for -->  tokenAddress
770:     function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyOwner {

// @audit missing checks for -->  user
795:     function lockInfo(address user) external view returns (LockedBalance[] memory) {

// @audit missing checks for -->  user
803:     function totalBalance(address user) external view returns (uint256) {

// @audit missing checks for -->  rewardToken
822:     function getRewardForDuration(address rewardToken) external view returns (uint256) {

// @audit missing checks for -->  user
830:     function getBalances(address user) external view returns (Balances memory) {

// @audit missing checks for -->  user
843:     function claimBounty(address user, bool execute) public whenNotPaused returns (bool issueBaseBounty) {

// @audit missing checks for -->  rewardTokens_
864:     function getReward(address[] memory rewardTokens_) public {

// @audit missing checks for -->  user
888:     function requalifyFor(address user) public {

// @audit missing checks for -->  user
900:     function lockedBalances(
901:         address user
902:     )
903:         public
904:         view
905:         returns (
906:             uint256 total,
907:             uint256 unlockable,
908:             uint256 locked,
909:             uint256 lockedWithMultiplier,
910:             LockedBalance[] memory lockData
911:         )
912:     {

// @audit missing checks for -->  user
940:     function lockedBalance(address user) public view returns (uint256 locked) {

// @audit missing checks for -->  user
961:     function earnedBalances(
962:         address user
963:     ) public view returns (uint256 totalVesting, uint256 unlocked, EarnedBalance[] memory earningsData) {

// @audit missing checks for -->  user
998:     function withdrawableBalance(
999:         address user
1000:     ) public view returns (uint256 amount, uint256 penaltyAmount, uint256 burnAmount) {

// @audit missing checks for -->  rewardToken
1024:     function lastTimeRewardApplicable(address rewardToken) public view returns (uint256) {

// @audit missing checks for -->  rewardToken
1035:     function rewardPerToken(address rewardToken) public view returns (uint256 rptStored) {

// @audit missing checks for -->  account
1049:     function claimableRewards(address account) public view returns (IFeeDistribution.RewardData[] memory rewardsData) {

```


*GitHub* : [365](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L365-L365), [484](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L484-L484), [496](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L496-L496), [674](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L674-L678), [689](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L689-L689), [725](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L725-L725), [770](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L770-L770), [795](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L795-L795), [803](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L803-L803), [822](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L822-L822), [830](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L830-L830), [843](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L843-L843), [864](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L864-L864), [888](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L888-L888), [900](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L900-L912), [940](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L940-L940), [961](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L961-L963), [998](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L998-L1000), [1024](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L1024-L1024), [1035](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L1035-L1035), [1049](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L1049-L1049)

```solidity
📁 File: ./src/utils/Permission.sol

// @audit missing checks for -->  caller
38:     function modifyPermission(address caller, bool permitted) external {

// @audit missing checks for -->  owner, caller
47:     function modifyPermission(address owner, address caller, bool permitted) external {

// @audit missing checks for -->  agent
57:     function setPermissionAgent(address agent, bool permitted) external {

// @audit missing checks for -->  owner, caller
66:     function hasPermission(address owner, address caller) public view returns (bool) {

```


*GitHub* : [38](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/utils/Permission.sol#L38-L38), [47](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/utils/Permission.sol#L47-L47), [57](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/utils/Permission.sol#L57-L57), [66](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/utils/Permission.sol#L66-L66)

```solidity
📁 File: ./src/vendor/AuraVault.sol

// @audit missing checks for -->  receiver
199:     function deposit(uint256 assets, address receiver) public virtual override(IERC4626, ERC4626) returns (uint256) {

// @audit missing checks for -->  receiver
216:     function mint(uint256 shares, address receiver) public virtual override(IERC4626, ERC4626) returns (uint256) {

// @audit missing checks for -->  receiver, owner
233:     function withdraw(
234:         uint256 assets,
235:         address receiver,
236:         address owner
237:     ) public virtual override(IERC4626, ERC4626) returns (uint256) {

// @audit missing checks for -->  receiver, owner
256:     function redeem(
257:         uint256 shares,
258:         address receiver,
259:         address owner
260:     ) public virtual override(IERC4626, ERC4626) returns (uint256) {

```


*GitHub* : [199](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/AuraVault.sol#L199-L199), [216](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/AuraVault.sol#L216-L216), [233](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/AuraVault.sol#L233-L237), [256](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/AuraVault.sol#L256-L260)

```solidity
📁 File: ./src/vendor/IAuraPool.sol

// @audit missing checks for -->  receiver, owner
10:     function redeem(uint256 shares, address receiver, address owner) external returns (uint256);

// @audit missing checks for -->  account
14:     function earned(address account) external view returns (uint256);

```


*GitHub* : [10](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/IAuraPool.sol#L10-L10), [14](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/IAuraPool.sol#L14-L14)

```solidity
📁 File: ./src/vendor/IBalancerVault.sol

// @audit missing checks for -->  assets
161:     function batchSwap(
162:         SwapKind kind,
163:         BatchSwapStep[] memory swaps,
164:         address[] memory assets,
165:         FundManagement memory funds,
166:         int256[] memory limits,
167:         uint256 deadline
168:     ) external payable returns (int256[] memory);

// @audit missing checks for -->  sender, recipient
202:     function joinPool(
203:         bytes32 poolId,
204:         address sender,
205:         address recipient,
206:         JoinPoolRequest memory request
207:     ) external payable;

// @audit missing checks for -->  sender, recipient
244:     function exitPool(
245:         bytes32 poolId,
246:         address sender,
247:         address payable recipient,
248:         ExitPoolRequest memory request
249:     ) external;

// @audit missing checks for -->  assets
284:     function queryBatchSwap(
285:         SwapKind kind,
286:         BatchSwapStep[] memory swaps,
287:         address[] memory assets,
288:         FundManagement memory funds
289:     ) external view returns (int256[] memory assetDeltas);

```


*GitHub* : [161](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/IBalancerVault.sol#L161-L168), [202](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/IBalancerVault.sol#L202-L207), [244](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/IBalancerVault.sol#L244-L249), [284](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/IBalancerVault.sol#L284-L289)

### [L-02]<a name="l-02"></a> Array is `push()`ed but not `pop()`ed

Array entries are added but are never removed. Consider whether this should be the case, or whether there should be a maximum, or whether old entries should be removed. Cases where there are specific potential problems will be flagged separately under a different issue.

*There are 5 instance(s) of this issue:*

```solidity
📁 File: ./src/reward/ChefIncentivesController.sol

306:         registeredTokens.push(_token);

412:             emissionSchedule.push(

```


*GitHub* : [306](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L306-L306), [412](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L412-L412)

```solidity
📁 File: ./src/reward/MultiFeeDistribution.sol

310:             _lockPeriod.push(lockPeriod_[i]);

311:             _rewardMultipliers.push(rewardMultipliers_[i]);

1421:         locks.push();

```


*GitHub* : [310](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L310-L310), [311](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L311-L311), [1421](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L1421-L1421)

### [L-03]<a name="l-03"></a> Consider bounding input array length

Unbounded array inputs in functions can lead to unintentional excessive gas consumption, potentially causing a transaction to revert after expending substantial gas. To enhance user experience and prevent such scenarios, consider implementing a `require()` statement that limits the array length to a defined maximum. This constraint ensures that transactions won't proceed if they're likely to hit gas limits due to array size, saving users from unnecessary gas costs and offering a more predictable interaction with the contract.

*There are 3 instance(s) of this issue:*

```solidity
📁 File: ./src/oracle/BalancerOracle.sol

//@audit weights.length not bounded 
126:         for (uint256 i = 0; i < weights.length; i++) {

```


*GitHub* : [126](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/BalancerOracle.sol#L126-L126)

```solidity
📁 File: ./src/oracle/ChainlinkOracle.sol

//@audit _tokens.length not bounded 
46:         for (uint256 i = 0; i < _tokens.length; i++) {

```


*GitHub* : [46](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/ChainlinkOracle.sol#L46-L46)

```solidity
📁 File: ./src/proxy/PoolAction.sol

//@audit assets.length not bounded 
132:         for (uint256 i = 0; i < assets.length; ) {

```


*GitHub* : [132](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PoolAction.sol#L132-L132)

### [L-04]<a name="l-04"></a> Constant decimal values

The use of fixed decimal values such as 1e18 or 1e8 in Solidity contracts can lead to inaccuracies, bugs, and vulnerabilities, particularly when interacting with tokens having different decimal configurations. Not all ERC20 tokens follow the standard 18 decimal places, and assumptions about decimal places can lead to miscalculations.

Resolution: Always retrieve and use the `decimals()` function from the token contract itself when performing calculations involving token amounts. This ensures that your contract correctly handles tokens with any number of decimal places, mitigating the risk of numerical errors or under/overflows that could jeopardize contract integrity and user funds.

*There are 2 instance(s) of this issue:*

```solidity
📁 File: ./src/vendor/AuraVault.sol

53:     uint256 private constant BAL_CHAINLINK_DECIMALS = 1e8;

56:     uint256 private constant ETH_CHAINLINK_DECIMALS = 1e8;

```


*GitHub* : [53](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/AuraVault.sol#L53-L53), [56](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/AuraVault.sol#L56-L56)

### [L-05]<a name="l-05"></a> Duplicate import statements



*There are 2 instance(s) of this issue:*

```solidity
📁 File: ./src/oracle/PendleLPOracle.sol

6: import "pendle/oracles/PendleLpOracleLib.sol";

13: import {PendleLpOracleLib} from "pendle/oracles/PendleLpOracleLib.sol";

```


*GitHub* : [6](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/PendleLPOracle.sol#L6-L6), [13](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/PendleLPOracle.sol#L13-L13)

### [L-06]<a name="l-06"></a> Possible reentrancy with callback on transfer tokens

The following functions don't apply the [CEI](https://blockchain-academy.hs-mittweida.de/courses/solidity-coding-beginners-to-intermediate/lessons/solidity-11-coding-patterns/topic/checks-effects-interactions/) pattern. It's possible to reenter after the transfer if the token has some kind of callback functionality (e.g. ERC777/ERC1155).

*There are 8 instance(s) of this issue:*

```solidity
📁 File: ./src/CDPVault.sol

// @audit state update on line 445
410:             poolUnderlying.safeTransferFrom(creditor, address(pool), amount);

// @audit state update on line 445
439:             token.safeTransferFrom(collateralizer, address(this), amount);

// @audit state update on line 445
442:             token.safeTransfer(collateralizer, amount);

// @audit state update on line 561
539:         poolUnderlying.safeTransferFrom(msg.sender, address(pool), repayAmount - penalty);

// @audit state update on line 621
610:         poolUnderlying.safeTransferFrom(msg.sender, address(pool), repayAmount);

```


*GitHub* : [410](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L410-L410), [439](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L439-L439), [442](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L442-L442), [539](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L539-L539), [610](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L610-L610)

```solidity
📁 File: ./src/reward/MultiFeeDistribution.sol

// @audit state update on line 1295
1285:         rdntToken.safeTransfer(onBehalfOf, amount);

// @audit state update on line 1295
1288:                 rdntToken.safeTransfer(starfleetTreasury, burnAmount);

// @audit state update on line 1295
1290:             rdntToken.safeTransfer(daoTreasury, penaltyAmount - burnAmount);

```


*GitHub* : [1285](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L1285-L1285), [1288](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L1288-L1288), [1290](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L1290-L1290)

### [L-07]<a name="l-07"></a> Events may be emitted out of order due to reentrancy

If a reentrancy occurs, some events may be emitted in an unexpected order, and this may be a problem if a third party expects a specific order for these events. Ensure that events are emitted before external calls and follow the best practice of CEI.

*There are 23 instance(s) of this issue:*

```solidity
📁 File: ./src/CDPVault.sol

339:         emit ModifyPosition(owner, position.debt, position.collateral, totalDebt_);

459:         emit ModifyCollateralAndDebt(owner, collateralizer, creditor, deltaCollateral, deltaDebt);

```


*GitHub* : [339](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L339-L339), [459](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L459-L459)

```solidity
📁 File: ./src/Flashlender.sol

99:         emit FlashLoan(address(receiver), token, amount, fee);

127:         emit CreditFlashLoan(address(receiver), amount, fee);

```


*GitHub* : [99](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/Flashlender.sol#L99-L99), [127](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/Flashlender.sol#L127-L127)

```solidity
📁 File: ./src/PoolV3.sol

394:         emit Deposit(msg.sender, receiver, assetsSent, shares); // U:[LP-6,7]

424:         emit Withdraw(msg.sender, receiver, owner, assetsReceived, shares); // U:[LP-8,9]

513:         emit Borrow(msg.sender, creditAccount, borrowedAmount); // U:[LP-13B]

778:         emit SetPoolQuotaKeeper(newPoolQuotaKeeper); // U:[LP-23D]

811:             emit AddCreditManager(creditManager); // U:[LP-25D]

814:         emit SetCreditManagerDebtLimit(creditManager, newLimit); // U:[LP-25D]

```


*GitHub* : [394](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L394-L394), [424](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L424-L424), [513](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L513-L513), [778](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L778-L778), [811](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L811-L811), [814](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L814-L814)

```solidity
📁 File: ./src/quotas/GaugeV3.sol

89:             emit UpdateEpoch(epochNow); // U:[GA-14]

259:         emit AddQuotaToken({token: token, minRate: minRate, maxRate: maxRate}); // U:[GA-05]

```


*GitHub* : [89](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/GaugeV3.sol#L89-L89), [259](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/GaugeV3.sol#L259-L259)

```solidity
📁 File: ./src/quotas/PoolQuotaKeeperV3.sol

176:         emit AddQuotaToken(token); // U:[PQK-5]

```


*GitHub* : [176](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/PoolQuotaKeeperV3.sol#L176-L176)

```solidity
📁 File: ./src/reward/ChefIncentivesController.sol

749:             emit Disqualified(_user);

```


*GitHub* : [749](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L749-L749)

```solidity
📁 File: ./src/reward/MultiFeeDistribution.sol

739:                     emit RewardPaid(onBehalf, token, reward);

1147:         emit Locked(
1148:             onBehalfOf,
1149:             amount,
1150:             _balances[onBehalfOf].locked,
1151:             _lockPeriod[typeIndex],
1152:             stakingToken != address(rdntToken)
1153:         );

1211:         emit RevenueEarned(rewardToken, reward);

1214:         emit NewTransferAdded(rewardToken, lpUsdValue);

1257:                 emit RewardPaid(user, token, reward);

1300:         emit Withdrawn(onBehalfOf, amount, _balances[onBehalfOf].locked, penaltyAmount, burnAmount, false);

1375:                 emit Withdrawn(address_, amount, _balances[address_].locked, 0, 0, stakingToken != address(rdntToken));

```


*GitHub* : [739](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L739-L739), [1147](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L1147-L1153), [1211](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L1211-L1211), [1214](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L1214-L1214), [1257](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L1257-L1257), [1300](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L1300-L1300), [1375](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L1375-L1375)

```solidity
📁 File: ./src/reward/RecoverERC20.sol

21:         emit Recovered(tokenAddress, tokenAmount);

```


*GitHub* : [21](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/RecoverERC20.sol#L21-L21)

```solidity
📁 File: ./src/vendor/AuraVault.sol

309:         emit Claimed(msg.sender, amounts[0], amounts[1], amountIn);

```


*GitHub* : [309](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/AuraVault.sol#L309-L309)

### [L-08]<a name="l-08"></a> External calls in an unbounded loop can result in a DoS (Not captured by 4nalyzer)

Consider limiting the number of iterations in loops that make external calls, as just a single one of them failing will result in a revert.

*There are 5 instance(s) of this issue:*

```solidity
📁 File: ./src/VaultRegistry.sol

68:             (, uint256 debt, , , , ) = ICDPVault(vaultList[i]).positions(user);

```


*GitHub* : [68](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/VaultRegistry.sol#L68-L68)

```solidity
📁 File: ./src/proxy/PoolAction.sol

134:                 IERC20(assets[i]).forceApprove(address(balancerVault), maxAmountsIn[i]);

```


*GitHub* : [134](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PoolAction.sol#L134-L134)

```solidity
📁 File: ./src/reward/ChefIncentivesController.sol

693:             (, uint256 newBal, , , , ) = ICDPVault(registeredTokens[i]).positions(_user);

```


*GitHub* : [693](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L693-L693)

```solidity
📁 File: ./src/reward/MultiFeeDistribution.sol

738:                     IERC20(token).safeTransfer(rewardConverter, reward);

1256:                 IERC20(token).safeTransfer(user, reward);

```


*GitHub* : [738](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L738-L738), [1256](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L1256-L1256)

### [L-09]<a name="l-09"></a> Functions calling contracts/addresses with transfer hooks are missing reentrancy guards

Even if the function follows the best practice of check-effects-interaction, not using a reentrancy guard when there may be transfer hooks will open the users of this protocol up to [read-only reentrancies](https://chainsecurity.com/curve-lp-oracle-manipulation-post-mortem/) with no way to protect against it, except by block-listing the whole protocol.

*There are 22 instance(s) of this issue:*

```solidity
📁 File: ./src/CDPVault.sol

// @audit function 'modifyCollateralAndDebt()' is missing Reentrancy guard
410:             poolUnderlying.safeTransferFrom(creditor, address(pool), amount);

// @audit function 'liquidatePosition()' is missing Reentrancy guard
539:         poolUnderlying.safeTransferFrom(msg.sender, address(pool), repayAmount - penalty);

// @audit function 'liquidatePositionBadDebt()' is missing Reentrancy guard
610:         poolUnderlying.safeTransferFrom(msg.sender, address(pool), repayAmount);

```


*GitHub* : [410](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L410-L410), [539](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L539-L539), [610](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L610-L610)

```solidity
📁 File: ./src/Silo.sol

// @audit function 'withdraw()' is missing Reentrancy guard
29:         lpETH.safeTransfer(to, amount);

```


*GitHub* : [29](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/Silo.sol#L29-L29)

```solidity
📁 File: ./src/StakingLPEth.sol

// @audit function 'withdraw()' is missing Reentrancy guard
71:         return super.withdraw(assets, receiver, _owner);

// @audit function 'unstake()' is missing Reentrancy guard
96:             silo.withdraw(receiver, assets);

// @audit function 'cooldownAssets()' is missing Reentrancy guard
105:         if (assets > maxWithdraw(msg.sender)) revert ExcessiveWithdrawAmount();

// @audit function 'cooldownShares()' is missing Reentrancy guard
125:         _withdraw(msg.sender, address(silo), msg.sender, assets, shares);

```


*GitHub* : [71](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/StakingLPEth.sol#L71-L71), [96](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/StakingLPEth.sol#L96-L96), [105](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/StakingLPEth.sol#L105-L105), [125](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/StakingLPEth.sol#L125-L125)

```solidity
📁 File: ./src/proxy/PoolAction.sol

// @audit function 'transferAndJoin()' is missing Reentrancy guard
93:                         _transferFrom(assets[i], from, address(this), maxAmountsIn[i], permitParams[i]);

```


*GitHub* : [93](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PoolAction.sol#L93-L93)

```solidity
📁 File: ./src/proxy/PositionAction.sol

// @audit function 'withdraw()' is missing Reentrancy guard
207:         _withdraw(vault, position, collateralParams);

// @audit function 'withdrawAndRepay()' is missing Reentrancy guard
262:         _withdraw(vault, position, collateralParams);

// @audit function 'increaseLever()' is missing Reentrancy guard
323:                 _transferFrom(upFrontToken, collateralizer, self, upFrontAmount, permitParams);

// @audit function 'onCreditFlashLoan()' is missing Reentrancy guard
485:                 IERC20(leverParams.primarySwap.assetIn).safeTransfer(residualRecipient, residualAmount);

```


*GitHub* : [207](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L207-L207), [262](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L262-L262), [323](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L323-L323), [485](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L485-L485)

```solidity
📁 File: ./src/proxy/SwapAction.sol

// @audit function 'transferAndSwap()' is missing Reentrancy guard
100:             _transferFrom(swapParams.assetIn, from, address(this), amount, permitParams);

// @audit function 'swap()' is missing Reentrancy guard
141:             IERC20(swapParams.assetIn).safeTransfer(swapParams.recipient, swapParams.limit - retAmount);

```


*GitHub* : [100](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/SwapAction.sol#L100-L100), [141](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/SwapAction.sol#L141-L141)

```solidity
📁 File: ./src/reward/MultiFeeDistribution.sol

// @audit function 'withdraw()' is missing Reentrancy guard
610:         _withdrawTokens(_address, amount, penaltyAmount, burnAmount, false);

// @audit function 'individualEarlyExit()' is missing Reentrancy guard
621:         (uint256 amount, uint256 penaltyAmount, uint256 burnAmount, uint256 index) = _ieeWithdrawableBalance(
622:             onBehalfOf,
623:             unlockTime
624:         );

// @audit function 'withdrawExpiredLocksForWithOptions()' is missing Reentrancy guard
681:         return _withdrawExpiredLocksFor(address_, isRelockAction_, true, limit_);

// @audit function 'zapVestingToLp()' is missing Reentrancy guard
708:         rdntToken.safeTransfer(_lockZap, zapped);

// @audit function 'claimFromConverter()' is missing Reentrancy guard
738:                     IERC20(token).safeTransfer(rewardConverter, reward);

```


*GitHub* : [610](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L610-L610), [621](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L621-L624), [681](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L681-L681), [708](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L708-L708), [738](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L738-L738)

```solidity
📁 File: ./src/vendor/AuraVault.sol

// @audit function 'redeem()' is missing Reentrancy guard
266:         _withdraw(_msgSender(), receiver, owner, assets, shares);

// @audit function 'claim()' is missing Reentrancy guard
290:         IERC20(asset()).safeTransferFrom(msg.sender, address(this), amountIn);

```


*GitHub* : [266](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/AuraVault.sol#L266-L266), [290](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/AuraVault.sol#L290-L290)

### [L-10]<a name="l-10"></a> Input array lengths may differ

If the caller makes a copy-paste error, the lengths may be mismatched and an operation believed to have been completed may not in fact have been completed (e.g. if the array being iterated over is shorter than the one being indexed into).

*There are 19 instance(s) of this issue:*

```solidity
📁 File: ./src/oracle/ChainlinkOracle.sol

// @audit _oracles[]
47:             oracles[_tokens[i]] = _oracles[i];

```


*GitHub* : [47](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/ChainlinkOracle.sol#L47-L47)

```solidity
📁 File: ./src/proxy/PositionAction.sol

// @audit delegateCall[]
276:             if (delegateCall[i]) {

// @audit targets[]
277:                 _delegateCall(targets[i], data[i]);

// @audit targets[]
279:                 (bool success, bytes memory response) = targets[i].call(data[i]);

```


*GitHub* : [276](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L276-L276), [277](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L277-L277), [279](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L279-L279)

```solidity
📁 File: ./src/proxy/SwapAction.sol

// @audit poolIds[]
231:                         poolId: poolIds[i],

```


*GitHub* : [231](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/SwapAction.sol#L231-L231)

```solidity
📁 File: ./src/quotas/GaugeV3.sol

// @audit tokens[]
103:                 address token = tokens[i]; // U:[GA-15]

```


*GitHub* : [103](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/GaugeV3.sol#L103-L103)

```solidity
📁 File: ./src/reward/ChefIncentivesController.sol

// @audit _startTimeOffsets[]
403:                 if (_startTimeOffsets[i - 1] > _startTimeOffsets[i]) revert NotAscending();

// @audit _startTimeOffsets[]
405:             if (_startTimeOffsets[i] > type(uint128).max) revert ExceedsMaxInt();

// @audit _startTimeOffsets[]
407:             if (_checkDuplicateSchedule(_startTimeOffsets[i])) revert DuplicateSchedule();

// @audit _startTimeOffsets[]
410:                 if (_startTimeOffsets[i] < block.timestamp - startTime) revert InvalidStart();

// @audit _startTimeOffsets[]
414:                     startTimeOffset: uint128(_startTimeOffsets[i]),

// @audit _tokens[]
497:             address token = _tokens[i];

// @audit _tokens[]
533:             if (!validRTokens[_tokens[i]]) revert InvalidRToken();

// @audit _tokens[]
534:             VaultInfo storage pool = vaultInfo[_tokens[i]];

// @audit _tokens[]
537:             UserInfo storage user = userInfo[_tokens[i]][_user];

// @audit _tokens[]
817:             address token = _tokens[i];

```


*GitHub* : [403](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L403-L403), [405](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L405-L405), [407](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L407-L407), [410](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L410-L410), [414](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L414-L414), [497](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L497-L497), [533](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L533-L533), [534](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L534-L534), [537](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L537-L537), [817](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L817-L817)

```solidity
📁 File: ./src/reward/MultiFeeDistribution.sol

// @audit minters_[]
269:             if (minters_[i] == address(0)) revert AddressZero();

// @audit minters_[]
270:             minters[minters_[i]] = true;

// @audit rewardTokens_[]
1249:             address token = rewardTokens_[i];

```


*GitHub* : [269](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L269-L269), [270](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L270-L270), [1249](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L1249-L1249)

### [L-11]<a name="l-11"></a> Mapping arrays can grow in size without a way to shrink them

It's a good practice to maintain control over the size of array mappings in Solidity, especially if they are dynamically updated. If a contract includes a mechanism to push items into an array, it should ideally also provide a mechanism to remove items. This is because Solidity arrays don't automatically shrink when items are deleted - their length needs to be manually adjusted.

Ignoring this can lead to bloated and inefficient contracts. For instance, iterating over a large array can cause your contract to hit the block gas limit. Additionally, if entries are only marked for deletion but never actually removed, you may end up dealing with stale or irrelevant data, which can cause logical errors.

Therefore, implementing a method to 'pop' items from mapping arrays helps manage contract's state, improve efficiency and prevent potential issues related to gas limits or stale data. Always ensure to handle potential underflow conditions when popping elements from the mapping array.

*There are 2 instance(s) of this issue:*

```solidity
📁 File: ./src/reward/MultiFeeDistribution.sol

88:     mapping(address => LockedBalance[]) internal _userLocks;

89:     mapping(address => LockedBalance[]) private _userEarnings;

```


*GitHub* : [88](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L88-L88), [89](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L89-L89)

### [L-12]<a name="l-12"></a> Missing zero address check in constructor

Constructors often take address parameters to initialize important components of a contract, such as owner or linked contracts. However, without a checking, there's a risk that an address parameter could be mistakenly set to the zero address `(0x0)`. This could be due to an error or oversight during contract deployment. A zero address in a crucial role can cause serious issues, as it cannot perform actions like a normal address, and any funds sent to it will be irretrievable. It's therefore crucial to include a zero address check in constructors to prevent such potential problems. If a zero address is detected, the constructor should revert the transaction.

*There are 14 instance(s) of this issue:*

```solidity
📁 File: ./src/Flashlender.sol

// @audit missing checks for -->  pool_
50:     constructor(IPoolV3 pool_, uint256 protocolFee_) {

```


*GitHub* : [50](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/Flashlender.sol#L50-L50)

```solidity
📁 File: ./src/PoolV3.sol

// @audit missing checks for -->  addressProvider_, underlyingToken_, interestRateModel_
152:     constructor(
153:         address addressProvider_,
154:         address underlyingToken_,
155:         address interestRateModel_,
156:         uint256 totalDebtLimit_,
157:         string memory name_,
158:         string memory symbol_
159:     )
160:         ACLNonReentrantTrait(addressProvider_) // U:[LP-1A]
161:         ContractsRegisterTrait(addressProvider_)
162:         ERC4626(IERC20(underlyingToken_)) // U:[LP-1B]
163:         ERC20(name_, symbol_) // U:[LP-1B]
164:         ERC20Permit(name_) // U:[LP-1B]
165:         nonZeroAddress(underlyingToken_) // U:[LP-1A]
166:         nonZeroAddress(interestRateModel_) // U:[LP-1A]
167:     {

```


*GitHub* : [152](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L152-L167)

```solidity
📁 File: ./src/Silo.sol

// @audit missing checks for -->  _stakingVault, _lpEth
18:     constructor(address _stakingVault, address _lpEth) {

```


*GitHub* : [18](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/Silo.sol#L18-L18)

```solidity
📁 File: ./src/StakingLPEth.sol

// @audit missing checks for -->  _liquidityPool
54:     constructor(
55:         address _liquidityPool,
56:         string memory _name,
57:         string memory _symbol
58:     ) ERC4626(IERC20(_liquidityPool)) ERC20(_name, _symbol) {

```


*GitHub* : [54](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/StakingLPEth.sol#L54-L58)

```solidity
📁 File: ./src/oracle/BalancerOracle.sol

// @audit missing checks for -->  balancerVault_, chainlinkOracle_, pool_
68:     constructor(
69:         address balancerVault_,
70:         address chainlinkOracle_,
71:         address pool_,
72:         uint256 updateWaitWindow_,
73:         uint256 stalePeriod_
74:     ) initializer {

```


*GitHub* : [68](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/BalancerOracle.sol#L68-L74)

```solidity
📁 File: ./src/oracle/PendleLPOracle.sol

// @audit missing checks for -->  ptOracle_, market_, aggregator_
56:     constructor(address ptOracle_, address market_, uint32 twap_, AggregatorV3Interface aggregator_, uint256 stalePeriod_) initializer {

```


*GitHub* : [56](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/PendleLPOracle.sol#L56-L56)

```solidity
📁 File: ./src/proxy/PoolAction.sol

// @audit missing checks for -->  balancerVault_, _pendleRouter
61:     constructor(address balancerVault_, address _pendleRouter) {

```


*GitHub* : [61](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PoolAction.sol#L61-L61)

```solidity
📁 File: ./src/proxy/PositionAction20.sol

// @audit missing checks for -->  flashlender_, swapAction_, poolAction_, vaultRegistry_
24:     constructor(
25:         address flashlender_,
26:         address swapAction_,
27:         address poolAction_, 
28:         address vaultRegistry_
29:     ) PositionAction(flashlender_, swapAction_, poolAction_, vaultRegistry_) {}

```


*GitHub* : [24](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction20.sol#L24-L29)

```solidity
📁 File: ./src/proxy/PositionAction4626.sol

// @audit missing checks for -->  flashlender_, swapActions_, poolAction_, vaultRegistry_
25:     constructor(
26:         address flashlender_,
27:         address swapActions_,
28:         address poolAction_,
29:         address vaultRegistry_
30:     ) PositionAction(flashlender_, swapActions_, poolAction_, vaultRegistry_) {}

```


*GitHub* : [25](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction4626.sol#L25-L30)

```solidity
📁 File: ./src/proxy/PositionActionPendle.sol

// @audit missing checks for -->  flashlender_, swapAction_, poolAction_, vaultRegistry_
25:     constructor(address flashlender_, address swapAction_, address poolAction_, address vaultRegistry_ ) PositionAction(flashlender_, swapAction_, poolAction_, vaultRegistry_) {}

```


*GitHub* : [25](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionActionPendle.sol#L25-L25)

```solidity
📁 File: ./src/proxy/SwapAction.sol

// @audit missing checks for -->  balancerVault_, uniRouter_, pendleRouter_
78:     constructor(IVault balancerVault_, IUniswapV3Router uniRouter_, IPActionAddRemoveLiqV3 pendleRouter_) {

```


*GitHub* : [78](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/SwapAction.sol#L78-L78)

```solidity
📁 File: ./src/quotas/GaugeV3.sol

// @audit missing checks for -->  _pool, _voter
52:     constructor(
53:         address _pool,
54:         address _voter
55:     )
56:         ACLNonReentrantTrait(IPoolV3(_pool).addressProvider())
57:         nonZeroAddress(_voter) // U:[GA-01]
58:     {

```


*GitHub* : [52](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/GaugeV3.sol#L52-L58)

```solidity
📁 File: ./src/quotas/PoolQuotaKeeperV3.sol

// @audit missing checks for -->  _pool
79:     constructor(
80:         address _pool
81:     ) ACLNonReentrantTrait(IPoolV3(_pool).addressProvider()) ContractsRegisterTrait(IPoolV3(_pool).addressProvider()) {

```


*GitHub* : [79](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/PoolQuotaKeeperV3.sol#L79-L81)

```solidity
📁 File: ./src/vendor/AuraVault.sol

// @audit missing checks for -->  rewardPool_, asset_, feed_, auraPriceOracle_
120:     constructor(
121:         address rewardPool_,
122:         address asset_,
123:         address feed_,
124:         address auraPriceOracle_,
125:         uint32 maxClaimerIncentive_,
126:         uint32 maxLockerIncentive_,
127:         string memory tokenName_,
128:         string memory tokenSymbol_
129:     ) ERC4626(IERC20(asset_)) ERC20(tokenName_, tokenSymbol_) {

```


*GitHub* : [120](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/AuraVault.sol#L120-L129)

### [L-13]<a name="l-13"></a> Missing contract-existence checks before low-level calls

Low-level calls return success if there is no code present at the specified address.

*There are 2 instance(s) of this issue:*

```solidity
📁 File: ./src/proxy/BaseAction.sol

21:         (bool success, bytes memory returnData) = to.delegatecall(data);

```


*GitHub* : [21](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/BaseAction.sol#L21-L21)

```solidity
📁 File: ./src/proxy/PositionAction.sol

279:                 (bool success, bytes memory response) = targets[i].call(data[i]);

```


*GitHub* : [279](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L279-L279)

### [L-14]<a name="l-14"></a> Named return variable used before assignment

As no value is written to the variable, the default value is always read. This is usually due to a bug in the code logic that causes an invalid value to be used.

*There are 1 instance(s) of this issue:*

```solidity
📁 File: ./src/PoolV3.sol

// @audit assets
431:         return assets; //(assets == 0 || supply == 0) ? assets : assets.mulDiv(supply, totalAssets(), rounding);

```


*GitHub* : [431](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L431-L431)

### [L-15]<a name="l-15"></a> The `nonReentrant` modifier should be placed first in a function declaration

In functions with multiple modifiers, including `nonReentrant`, the `nonReentrant` modifier should be placed first in the ordering to prevent reentrancy in prior modifiers.

*There are 6 instance(s) of this issue:*

```solidity
📁 File: ./src/PoolV3.sol

231:     function deposit(
232:         uint256 assets,
233:         address receiver
234:     )
235:         public
236:         override(ERC4626, IERC4626)
237:         whenNotPaused // U:[LP-2A]
238:         nonReentrant // U:[LP-2B]
239:         nonZeroAddress(receiver) // U:[LP-5]
240:         returns (uint256 shares)
241:     {


261:     function mint(
262:         uint256 shares,
263:         address receiver
264:     )
265:         public
266:         override(ERC4626, IERC4626)
267:         whenNotPaused // U:[LP-2A]
268:         nonReentrant // U:[LP-2B]
269:         nonZeroAddress(receiver) // U:[LP-5]
270:         returns (uint256 assets)
271:     {


292:     function withdraw(
293:         uint256 assets,
294:         address receiver,
295:         address owner
296:     )
297:         public
298:         override(ERC4626, IERC4626)
299:         whenNotPaused // U:[LP-2A]
300:         whenNotLocked
301:         nonReentrant // U:[LP-2B]
302:         nonZeroAddress(receiver) // U:[LP-5]
303:         returns (uint256 shares)
304:     {


316:     function redeem(
317:         uint256 shares,
318:         address receiver,
319:         address owner
320:     )
321:         public
322:         override(ERC4626, IERC4626)
323:         whenNotPaused // U:[LP-2A]
324:         whenNotLocked
325:         nonReentrant // U:[LP-2B]
326:         nonZeroAddress(receiver) // U:[LP-5]
327:         returns (uint256 assets)
328:     {


484:     function lendCreditAccount(
485:         uint256 borrowedAmount,
486:         address creditAccount
487:     )
488:         external
489:         override
490:         creditManagerOnly // U:[LP-2C]
491:         whenNotPaused // U:[LP-2A]
492:         nonReentrant // U:[LP-2B]
493:     {


529:     function repayCreditAccount(
530:         uint256 repaidAmount,
531:         uint256 profit,
532:         uint256 loss
533:     )
534:         external
535:         override
536:         creditManagerOnly // U:[LP-2C]
537:         whenNotPaused // U:[LP-2A]
538:         nonReentrant // U:[LP-2B]
539:     {

```

*GitHub* : [231](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L231-L241), [261](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L261-L271), [292](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L292-L304), [316](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L316-L328), [484](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L484-L493), [529](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L529-L539)

### [L-16]<a name="l-16"></a> `onlyOwner` functions not accessible if owner renounces ownership

The owner is able to perform certain privileged activities, but it's possible to set the owner to address(0). This can represent a certain risk if the ownership is renounced for any other reason than by design.

Renouncing ownership will leave the contract without an owner, therefore limiting any functionality that needs authority.

*There are 30 instance(s) of this issue:*

```solidity
📁 File: ./src/StakingLPEth.sol

130:     function setCooldownDuration(uint24 duration) external onlyOwner {

```


*GitHub* : [130](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/StakingLPEth.sol#L130-L130)

```solidity
📁 File: ./src/reward/ChefIncentivesController.sol

272:     function setBountyManager(address _bountyManager) external onlyOwner {

281:     function setEligibilityMode(EligibilityModes _newVal) external onlyOwner {

291:     function start() public onlyOwner {

318:     function batchUpdateAllocPoint(address[] calldata _tokens, uint256[] calldata _allocPoints) external onlyOwner {

342:     function setRewardsPerSecond(uint256 _rewardsPerSecond, bool _persist) external onlyOwner {

394:     function setEmissionSchedule(
395:         uint256[] calldata _startTimeOffsets,
396:         uint256[] calldata _rewardsPerSecond
397:     ) external onlyOwner {

430:     function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyOwner {

581:     function setContractAuthorization(address _address, bool _authorize) external onlyOwner {

909:     function setEndingTimeUpdateCadence(uint256 _lapse) external onlyOwner {

920:     function registerRewardDeposit(uint256 _amount) external onlyOwner {

966:     function pause() external onlyOwner {

973:     function unpause() external onlyOwner {

1005:     function setAddressWLstatus(address user, bool status) external onlyOwner {

1012:     function toggleWhitelist() external onlyOwner {

```


*GitHub* : [272](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L272-L272), [281](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L281-L281), [291](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L291-L291), [318](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L318-L318), [342](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L342-L342), [394](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L394-L397), [430](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L430-L430), [581](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L581-L581), [909](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L909-L909), [920](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L920-L920), [966](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L966-L966), [973](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L973-L973), [1005](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L1005-L1005), [1012](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L1012-L1012)

```solidity
📁 File: ./src/reward/EligibilityDataProvider.sol

118:     function setChefIncentivesController(IChefIncentivesController _chef) external onlyOwner {

127:     function setLPToken(address _lpToken) external onlyOwner {

139:     function setRequiredDepositRatio(uint256 _requiredDepositRatio) external onlyOwner {

150:     function setPriceToleranceRatio(uint256 _priceToleranceRatio) external onlyOwner {

```


*GitHub* : [118](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/EligibilityDataProvider.sol#L118-L118), [127](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/EligibilityDataProvider.sol#L127-L127), [139](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/EligibilityDataProvider.sol#L139-L139), [150](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/EligibilityDataProvider.sol#L150-L150)

```solidity
📁 File: ./src/reward/MultiFeeDistribution.sol

266:     function setMinters(address[] calldata minters_) external onlyOwner {

282:     function setBountyManager(address bounty) external onlyOwner {

293:     function addRewardConverter(address rewardConverter_) external onlyOwner {

304:     function setLockTypeInfo(uint256[] calldata lockPeriod_, uint256[] calldata rewardMultipliers_) external onlyOwner {

324:     function setAddresses(IChefIncentivesController controller_, address treasury_) external onlyOwner {

336:     function setLPToken(address stakingToken_) external onlyOwner {

452:     function setLookback(uint256 lookback) external onlyOwner {

464:     function setOperationExpenses(
465:         address _operationExpenseReceiver,
466:         uint256 _operationExpenseRatio
467:     ) external onlyOwner {

770:     function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyOwner {

873:     function pause() public onlyOwner {

880:     function unpause() public onlyOwner {

```


*GitHub* : [266](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L266-L266), [282](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L282-L282), [293](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L293-L293), [304](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L304-L304), [324](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L324-L324), [336](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L336-L336), [452](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L452-L452), [464](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L464-L467), [770](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L770-L770), [873](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L873-L873), [880](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L880-L880)

### [L-17]<a name="l-17"></a> Variables shadowing other definitions

A variable declaration shadowing any other existing definition is error-prone. It can cause confusion for developers, potentially introduce bugs, and reduce the readability and maintainability.

*There are 28 instance(s) of this issue:*

```solidity
📁 File: ./src/Flashlender.sol

// @audit Shadows state variable  token
64:     function maxFlashLoan(address token) external view override returns (uint256 max) {

// @audit Shadows state variable  token
75:     function flashFee(address token, uint256 amount) external view override returns (uint256) {

// @audit Shadows state variable  token
87:     function flashLoan(
88:         IERC3156FlashBorrower receiver,
89:         address token,
90:         uint256 amount,
91:         bytes calldata data
92:     ) external override nonReentrant returns (bool) {

```


*GitHub* : [64](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/Flashlender.sol#L64-L64), [75](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/Flashlender.sol#L75-L75), [87](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/Flashlender.sol#L87-L92)

```solidity
📁 File: ./src/oracle/ChainlinkOracle.sol

// @audit Shadows state variable  token
79:     function getStatus(address token) public view virtual override returns (bool status) {

// @audit Shadows state variable  token
87:     function spot(address token) external view virtual override returns (uint256 price) {

// @audit Shadows state variable  token
96:     function _fetchAndValidate(address token) internal view returns (bool isValid, uint256 price) {

// @audit Shadows state variable  token
115:     function _getStatus(address token) private view returns (bool status) {

```


*GitHub* : [79](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/ChainlinkOracle.sol#L79-L79), [87](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/ChainlinkOracle.sol#L87-L87), [96](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/ChainlinkOracle.sol#L96-L96), [115](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/ChainlinkOracle.sol#L115-L115)

```solidity
📁 File: ./src/proxy/TransferAction.sol

// @audit Shadows state variable  token
46:     function _transferFrom(
47:         address token,
48:         address from,
49:         address to,
50:         uint256 amount,
51:         PermitParams memory params
52:     ) internal {

```


*GitHub* : [46](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/TransferAction.sol#L46-L52)

```solidity
📁 File: ./src/quotas/GaugeV3.sol

// @audit Shadows state variable  token
144:     function _vote(address user, uint96 votes, address token, bool lpSide) internal {

// @audit Shadows state variable  token
187:     function _unvote(address user, uint96 votes, address token, bool lpSide) internal {

// @audit Shadows state variable  token
232:     function addQuotaToken(
233:         address token,
234:         uint16 minRate,
235:         uint16 maxRate
236:     )
237:         external
238:         override
239:         nonZeroAddress(token) // U:[GA-04]
240:         configuratorOnly // U:[GA-03]
241:     {

// @audit Shadows state variable  token
264:     function changeQuotaMinRate(
265:         address token,
266:         uint16 minRate
267:     )
268:         external
269:         override
270:         nonZeroAddress(token) // U: [GA-04]
271:         controllerOnly // U: [GA-03]
272:     {

// @audit Shadows state variable  token
278:     function changeQuotaMaxRate(
279:         address token,
280:         uint16 maxRate
281:     )
282:         external
283:         override
284:         nonZeroAddress(token) // U: [GA-04]
285:         controllerOnly // U: [GA-03]
286:     {

// @audit Shadows state variable  token
291:     function _changeQuotaTokenRateParams(address token, uint16 minRate, uint16 maxRate) internal {

// @audit Shadows state variable  token
312:     function isTokenAdded(address token) public view override returns (bool) {

```


*GitHub* : [144](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/GaugeV3.sol#L144-L144), [187](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/GaugeV3.sol#L187-L187), [232](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/GaugeV3.sol#L232-L241), [264](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/GaugeV3.sol#L264-L272), [278](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/GaugeV3.sol#L278-L286), [291](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/GaugeV3.sol#L291-L291), [312](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/GaugeV3.sol#L312-L312)

```solidity
📁 File: ./src/quotas/PoolQuotaKeeperV3.sol

// @audit Shadows state variable  token
87:     function cumulativeIndex(address token) public view override returns (uint192) {

// @audit Shadows state variable  token
95:     function getQuotaRate(address token) external view override returns (uint16) {

// @audit Shadows state variable  token
105:     function isQuotedToken(address token) external view override returns (bool) {

// @audit Shadows state variable  token
110:     function getTokenQuotaParams(
111:         address token
112:     )
113:         external
114:         view
115:         override
116:         returns (
117:             uint16 rate,
118:             uint192 cumulativeIndexLU,
119:             uint16 quotaIncreaseFee,
120:             uint96 totalQuoted,
121:             uint96 limit,
122:             bool isActive
123:         )
124:     {

// @audit Shadows state variable  token
161:     function addQuotaToken(
162:         address token
163:     )
164:         external
165:         override
166:         gaugeOnly // U:[PQK-3]
167:     {

// @audit Shadows state variable  token
238:     function setCreditManager(
239:         address token,
240:         address vault
241:     )
242:         external
243:         override
244:         configuratorOnly // U:[PQK-2]
245:     {

```


*GitHub* : [87](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/PoolQuotaKeeperV3.sol#L87-L87), [95](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/PoolQuotaKeeperV3.sol#L95-L95), [105](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/PoolQuotaKeeperV3.sol#L105-L105), [110](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/PoolQuotaKeeperV3.sol#L110-L124), [161](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/PoolQuotaKeeperV3.sol#L161-L167), [238](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/PoolQuotaKeeperV3.sol#L238-L245)

```solidity
📁 File: ./src/quotas/QuotasLogic.sol

// @audit Shadows state variable  lastQuotaRateUpdate
17:     function cumulativeIndexSince(uint192 cumulativeIndexLU, uint16 rate, uint256 lastQuotaRateUpdate)
18:         internal
19:         view
20:         returns (uint192)
21:     {

```


*GitHub* : [17](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/QuotasLogic.sol#L17-L21)

```solidity
📁 File: ./src/reward/ChefIncentivesController.sol

// @audit Shadows state variable  pool
469:     function _updatePool(VaultInfo storage pool, uint256 _totalAllocPoint) internal {

// @audit Shadows state variable  pool
982:     function _newRewards(
983:         VaultInfo memory pool,
984:         uint256 _totalAllocPoint
985:     ) internal view returns (uint256 newReward, uint256 newAccRewardPerShare) {

```


*GitHub* : [469](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L469-L469), [982](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L982-L985)

```solidity
📁 File: ./src/reward/MultiFeeDistribution.sol

// @audit Shadows state variable  token
1223:     function _notifyUnseenReward(address token) internal {

```


*GitHub* : [1223](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L1223-L1223)

```solidity
📁 File: ./src/vendor/IBalancerVault.sol

// @audit Shadows state variable  poolId
202:     function joinPool(
203:         bytes32 poolId,
204:         address sender,
205:         address recipient,
206:         JoinPoolRequest memory request
207:     ) external payable;

// @audit Shadows state variable  poolId
244:     function exitPool(
245:         bytes32 poolId,
246:         address sender,
247:         address payable recipient,
248:         ExitPoolRequest memory request
249:     ) external;

// @audit Shadows state variable  poolId
265:     function getPoolTokens(
266:         bytes32 poolId
267:     ) external view returns (address[] memory tokens, uint256[] memory balances, uint256 lastChangeBlock);

```


*GitHub* : [202](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/IBalancerVault.sol#L202-L207), [244](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/IBalancerVault.sol#L244-L249), [265](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/IBalancerVault.sol#L265-L267)

### [L-18]<a name="l-18"></a> Sending tokens in a loop

Performing token transfers in a loop in a Solidity contract is generally not recommended due to various reasons. One of these reasons is the 'Fail-Silently' issue.

In a Solidity loop, if one transfer operation fails, it causes the entire transaction to fail. This issue can be particularly troublesome when you're dealing with multiple transfers in one transaction. For instance, if you're looping through an array of recipients to distribute dividends or rewards, a single failed transfer will prevent all the subsequent recipients from receiving their transfers. This could be due to the recipient contract throwing an exception or due to other issues like a gas limit being exceeded.

Moreover, such a design could also inadvertently lead to a situation where a malicious contract intentionally causes a failure when receiving Ether to prevent other participants from getting their rightful transfers. This could open up avenues for griefing attacks in the system.

Resolution: To mitigate this problem, it's typically recommended to follow the 'withdraw pattern' in your contracts instead of pushing payments. In this model, each recipient would be responsible for triggering their own payment. This separates each transfer operation, so a failure in one doesn't impact the others. Additionally, it greatly reduces the chance of malicious interference as the control over fund withdrawal lies with the intended recipient and not with an external loop operation.

*There are 3 instance(s) of this issue:*

```solidity
📁 File: ./src/proxy/PoolAction.sol

93:                         _transferFrom(assets[i], from, address(this), maxAmountsIn[i], permitParams[i]);

```


*GitHub* : [93](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PoolAction.sol#L93-L93)

```solidity
📁 File: ./src/reward/MultiFeeDistribution.sol

738:                     IERC20(token).safeTransfer(rewardConverter, reward);

1256:                 IERC20(token).safeTransfer(user, reward);

```


*GitHub* : [738](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L738-L738), [1256](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L1256-L1256)

### [L-19]<a name="l-19"></a> File allows a version of solidity that is susceptible to `.selector` related optimizer bug

In solidity versions prior to `0.8.21`, there is a legacy code generation [bug](https://soliditylang.org/blog/2023/07/19/missing-side-effects-on-selector-access-bug/) where if `foo().selector` is called, `foo()` doesn't actually get evaluated. It is listed as low-severity, because projects usually use the contract name rather than a function call to look up the selector.

*There are 14 instance(s) of this issue:*

```solidity
📁 File: ./src/proxy/ERC165Plugin.sol

20:         methods[0] = this.onERC1155Received.selector;

21:         methods[1] = this.onERC1155BatchReceived.selector;

22:         methods[2] = this.onERC721Received.selector;

```


*GitHub* : [20](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/ERC165Plugin.sol#L20-L20), [21](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/ERC165Plugin.sol#L21-L21), [22](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/ERC165Plugin.sol#L22-L22)

```solidity
📁 File: ./src/proxy/PositionAction.sol

397:                 abi.encodeWithSelector(swapAction.swap.selector, leverParams.auxSwap)

405:             abi.encodeWithSelector(swapAction.swap.selector, leverParams.primarySwap)

462:                 swapAction.swap.selector,

479:                         swapAction.swap.selector,

540:                 abi.encodeWithSelector(swapAction.swap.selector, collateralParams.auxSwap)

561:             _delegateCall(address(swapAction), abi.encodeWithSelector(swapAction.swap.selector, creditParams.auxSwap));

601:             abi.encodeWithSelector(swapAction.transferAndSwap.selector, sender, permitParams, swapParams)

```


*GitHub* : [397](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L397-L397), [405](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L405-L405), [462](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L462-L462), [479](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L479-L479), [540](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L540-L540), [561](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L561-L561), [601](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction.sol#L601-L601)

```solidity
📁 File: ./src/proxy/PositionAction4626.sol

115:             _delegateCall(address(poolAction), abi.encodeWithSelector(poolAction.join.selector, poolActionParams));

149:                 abi.encodeWithSelector(poolAction.exit.selector, leverParams.auxAction)

```


*GitHub* : [115](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction4626.sol#L115-L115), [149](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionAction4626.sol#L149-L149)

```solidity
📁 File: ./src/proxy/PositionActionPendle.sol

63:                 address(poolAction), abi.encodeWithSelector(poolAction.join.selector, leverParams.auxAction)

84:                 address(poolAction), abi.encodeWithSelector(poolAction.exit.selector, leverParams.auxAction)

```


*GitHub* : [63](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionActionPendle.sol#L63-L63), [84](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionActionPendle.sol#L84-L84)

### [L-20]<a name="l-20"></a> State variables not capped at reasonable values

Consider adding minimum/maximum value checks to ensure that the state variables below can never be used to excessively harm users, including via griefing

*There are 12 instance(s) of this issue:*

```solidity
📁 File: ./src/CDPVault.sol

// @audit totalDebt_
333:         totalDebt = totalDebt_;

```


*GitHub* : [333](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L333-L333)

```solidity
📁 File: ./src/Flashlender.sol

// @audit protocolFee_
53:         protocolFee = protocolFee_;

```


*GitHub* : [53](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/Flashlender.sol#L53-L53)

```solidity
📁 File: ./src/oracle/BalancerOracle.sol

// @audit updateWaitWindow_
76:         updateWaitWindow = updateWaitWindow_;

// @audit stalePeriod_
77:         stalePeriod = stalePeriod_;

```


*GitHub* : [76](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/BalancerOracle.sol#L76-L76), [77](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/BalancerOracle.sol#L77-L77)

```solidity
📁 File: ./src/oracle/PendleLPOracle.sol

// @audit stalePeriod_
58:         stalePeriod = stalePeriod_;

// @audit twap_
61:         twapWindow = twap_;

```


*GitHub* : [58](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/PendleLPOracle.sol#L58-L58), [61](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/oracle/PendleLPOracle.sol#L61-L61)

```solidity
📁 File: ./src/reward/ChefIncentivesController.sol

// @audit _rewardsPerSecond
255:         rewardsPerSecond = _rewardsPerSecond;

// @audit _rewardsPerSecond
344:         rewardsPerSecond = _rewardsPerSecond;

```


*GitHub* : [255](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L255-L255), [344](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L344-L344)

```solidity
📁 File: ./src/reward/MultiFeeDistribution.sol

// @audit lockDuration_
255:         defaultLockDuration = lockDuration_;

// @audit vestDuration_
257:         vestDuration = vestDuration_;

```


*GitHub* : [255](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L255-L255), [257](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L257-L257)

```solidity
📁 File: ./src/vendor/AuraVault.sol

// @audit maxClaimerIncentive_
133:         maxClaimerIncentive = maxClaimerIncentive_;

// @audit maxLockerIncentive_
134:         maxLockerIncentive = maxLockerIncentive_;

```


*GitHub* : [133](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/AuraVault.sol#L133-L133), [134](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/AuraVault.sol#L134-L134)

### [L-21]<a name="l-21"></a> Consider implementing two-step procedure for updating protocol addresses

A copy-paste error or a typo may end up bricking protocol functionality, or sending tokens to an address with no known private key. Consider implementing a two-step procedure for updating protocol addresses, where the recipient is set as pending, and must 'accept' the assignment by making an affirmative call. A straight forward way of doing this would be to have the target contracts implement [EIP-165](https://eips.ethereum.org/EIPS/eip-165), and to have the 'set' functions ensure that the recipient is of the right interface type.

*There are 17 instance(s) of this issue:*

```solidity
📁 File: ./src/PoolV3.sol

746:     function setInterestRateModel(
747:         address newInterestRateModel
748:     )
749:         external
750:         override
751:         configuratorOnly // U:[LP-2C]
752:         nonZeroAddress(newInterestRateModel) // U:[LP-22A]
753:     {

761:     function setPoolQuotaKeeper(
762:         address newPoolQuotaKeeper
763:     )
764:         external
765:         override
766:         configuratorOnly // U:[LP-2C]
767:         nonZeroAddress(newPoolQuotaKeeper) // U:[LP-23A]
768:     {

838:     function setAllowed(address account, bool status) external controllerOnly {

```


*GitHub* : [746](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L746-L753), [761](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L761-L768), [838](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L838-L838)

```solidity
📁 File: ./src/quotas/PoolQuotaKeeperV3.sol

225:     function setGauge(
226:         address _gauge
227:     )
228:         external
229:         override
230:         configuratorOnly // U:[PQK-2]
231:     {

238:     function setCreditManager(
239:         address token,
240:         address vault
241:     )
242:         external
243:         override
244:         configuratorOnly // U:[PQK-2]
245:     {

```


*GitHub* : [225](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/PoolQuotaKeeperV3.sol#L225-L231), [238](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/quotas/PoolQuotaKeeperV3.sol#L238-L245)

```solidity
📁 File: ./src/reward/ChefIncentivesController.sol

272:     function setBountyManager(address _bountyManager) external onlyOwner {

570:     function setEligibilityExempt(address _contract, bool _value) public {

581:     function setContractAuthorization(address _address, bool _authorize) external onlyOwner {

1005:     function setAddressWLstatus(address user, bool status) external onlyOwner {

```


*GitHub* : [272](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L272-L272), [570](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L570-L570), [581](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L581-L581), [1005](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/ChefIncentivesController.sol#L1005-L1005)

```solidity
📁 File: ./src/reward/EligibilityDataProvider.sol

127:     function setLPToken(address _lpToken) external onlyOwner {

164:     function setDqTime(address _user, uint256 _time) external {

```


*GitHub* : [127](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/EligibilityDataProvider.sol#L127-L127), [164](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/EligibilityDataProvider.sol#L164-L164)

```solidity
📁 File: ./src/reward/MultiFeeDistribution.sol

282:     function setBountyManager(address bounty) external onlyOwner {

293:     function addRewardConverter(address rewardConverter_) external onlyOwner {

324:     function setAddresses(IChefIncentivesController controller_, address treasury_) external onlyOwner {

336:     function setLPToken(address stakingToken_) external onlyOwner {

464:     function setOperationExpenses(
465:         address _operationExpenseReceiver,
466:         uint256 _operationExpenseRatio
467:     ) external onlyOwner {

```


*GitHub* : [282](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L282-L282), [293](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L293-L293), [324](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L324-L324), [336](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L336-L336), [464](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/reward/MultiFeeDistribution.sol#L464-L467)

```solidity
📁 File: ./src/utils/Permission.sol

57:     function setPermissionAgent(address agent, bool permitted) external {

```


*GitHub* : [57](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/utils/Permission.sol#L57-L57)

### [L-22]<a name="l-22"></a> Using zero as a parameter

Passing `0` or `0x0` as a function argument can sometimes result in a security issue(e.g. passing zero as the slippage parameter). A historical example is the infamous 0x0 address bug where numerous tokens were lost. This happens because `0` can be interpreted as an uninitialized address, leading to transfers to the `0x0` address, effectively burning tokens. Moreover, `0` as a denominator in division operations would cause a runtime exception. It's also often indicative of a logical error in the caller's code.

Consider using a constant variable with a descriptive name, so it's clear that the argument is intentionally being used, and for the right reasons.

*There are 13 instance(s) of this issue:*

```solidity
📁 File: ./src/CDPVault.sol

226:         modifyCollateralAndDebt({
227:             owner: to,
228:             collateralizer: msg.sender,
229:             creditor: msg.sender,
230:             deltaCollateral: deltaCollateral,
231:             deltaDebt: 0
232:         });

242:         modifyCollateralAndDebt({
243:             owner: to,
244:             collateralizer: msg.sender,
245:             creditor: msg.sender,
246:             deltaCollateral: deltaCollateral,
247:             deltaDebt: 0
248:         });

258:         modifyCollateralAndDebt({
259:             owner: position,
260:             collateralizer: position,
261:             creditor: borrower,
262:             deltaCollateral: 0,
263:             deltaDebt: deltaDebt
264:         });

274:         modifyCollateralAndDebt({
275:             owner: position,
276:             collateralizer: position,
277:             creditor: borrower,
278:             deltaCollateral: 0,
279:             deltaDebt: deltaDebt
280:         });

615:         position = _modifyPosition(
616:             owner,
617:             position,
618:             0,
619:             debtData.cumulativeIndexNow,
620:             -toInt256(takeCollateral),
621:             totalDebt
622:         );

```


*GitHub* : [226](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L226-L232), [242](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L242-L248), [258](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L258-L264), [274](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L274-L280), [615](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/CDPVault.sol#L615-L622)

```solidity
📁 File: ./src/PoolV3.sol

387:         _updateBaseInterest({
388:             expectedLiquidityDelta: assetsReceived.toInt256(),
389:             availableLiquidityDelta: 0,
390:             checkOptimalBorrowing: false
391:         }); // U:[LP-6,7]

503:         _updateBaseInterest({
504:             expectedLiquidityDelta: 0,
505:             availableLiquidityDelta: -borrowedAmount.toInt256(),
506:             checkOptimalBorrowing: true
507:         }); // U:[LP-13B]

566:         _updateBaseInterest({
567:             expectedLiquidityDelta: -loss.toInt256(),
568:             availableLiquidityDelta: 0,
569:             checkOptimalBorrowing: false
570:         }); // U:[LP-14B,14C,14D]

755:         _updateBaseInterest(0, 0, false); // U:[LP-22B]

902:         _updateBaseInterest({
903:             expectedLiquidityDelta: amount.toInt256(),
904:             availableLiquidityDelta: 0,
905:             checkOptimalBorrowing: false
906:         }); // U:[LP-14B,14C,14D]

```


*GitHub* : [387](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L387-L391), [503](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L503-L507), [566](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L566-L570), [755](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L755-L755), [902](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/PoolV3.sol#L902-L906)

```solidity
📁 File: ./src/proxy/PoolAction.sol

104:                     _transferFrom(input.tokenIn, from, address(this), input.netTokenIn, permitParams[0]);

```


*GitHub* : [104](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PoolAction.sol#L104-L104)

```solidity
📁 File: ./src/proxy/PositionActionPendle.sol

80:         _onWithdraw(leverParams.vault, leverParams.position, address(0), subCollateral);

```


*GitHub* : [80](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/proxy/PositionActionPendle.sol#L80-L80)

```solidity
📁 File: ./src/vendor/AuraVault.sol

286:         amountIn = _previewReward(amounts[0], amounts[1], _config);

```


*GitHub* : [286](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/./src/vendor/AuraVault.sol#L286-L286)

