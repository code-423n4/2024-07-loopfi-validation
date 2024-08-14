____________________________________
# 🔵 L01 - should use safeCast, as a wrong input would still be written but overflown
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/CDPVault.sol#L196

## Vulnerability details
`L196`, the `data` parameter is casted to uint128, but as `data` is a uint256 number, this could overflow and write a very different value.
Better to use safeCast

```solidity
File: src/CDPVault.sol
195:     function setParameter(bytes32 parameter, uint256 data) external whenNotPaused onlyRole(VAULT_CONFIG_ROLE) {
196:         if (parameter == "debtFloor") vaultConfig.debtFloor = uint128(data);
197:         else if (parameter == "liquidationRatio") vaultConfig.liquidationRatio = uint64(data);
198:         else if (parameter == "liquidationPenalty") liquidationConfig.liquidationPenalty = uint64(data);
199:         else if (parameter == "liquidationDiscount") liquidationConfig.liquidationDiscount = uint64(data);
200:         else revert CDPVault__setParameter_unrecognizedParameter();
201:         emit SetParameter(parameter, data);
202:     }
203: 
```

## Tools Used
Manual review

## Recommended Mitigation Steps
Use safeCast

____________________________________
# 🔵 L02 - vulnerable to permit front-run causing a revert
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/TransferAction.sol#L67-L67

## Vulnerability details
When calling a permit, the data of the permit will be logged in the blockchain, and anyone is able to frontrun the permit by duplicating the TX arguments. This is not an issue according to the EIP since the permit creator can just create another permit.

However, if the permit is used in conjunction with an external function call, frontrunning the permit will cause the function to be griefed.

This is what's hapenning in `TransferAction::_transferFrom`:

```solidity
File: src/proxy/TransferAction.sol
46:     function _transferFrom(
47:         address token,
48:         address from,
49:         address to, //! to is not part of the permit signature
50:         uint256 amount,
51:         PermitParams memory params
52:     ) internal {
...:
...:      //* ------ some code ----- *//
...:
65:         } else if (params.approvalType == ApprovalType.PERMIT) {
66:             // Consume a standard ERC20 permit message
67:             IERC20Permit(token).safePermit(
68:                 from,
69:                 to,
70:                 params.approvalAmount,
71:                 params.deadline,
72:                 params.v,
73:                 params.r,
74:                 params.s
75:             );
76:             IERC20(token).safeTransferFrom(from, to, amount);
77:         } else {
...:
...:      //* ------ some code ----- *//
...:
81:     }
```
## Impact
User actions can be DoS'd repeatedly by a malicious actor.

## Tools Used
Manual review

## Recommended Mitigation Steps
Add a try/catch statement around `IERC20Permit(token).safePermit`, if this reverts then proceed to the transfer of funds. This way, in case of permit frontrun, the transfer still occurs.

____________________________________
# 🔵 L03 - `PoolAction::_balancerJoin` should return excess funds as proxy gets `maxAmountIn` but consume only `assetsIn`
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PoolAction.sol#L93-L93
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PoolAction.sol#L148-L148

## Vulnerability details
As Balancer pool is called using `EXACT_TOKENS_IN_FOR_BPT_OUT`, we know that nothing more than `assetsIn` will be transfered to the pool:

```solidity
File: src/proxy/PoolAction.sol
141: 
142:         balancerVault.joinPool(
143:             poolId,
144:             address(this),
145:             poolActionParams.recipient,
146:             JoinPoolRequest({
147:                 assets: assets,
148:                 maxAmountsIn: maxAmountsIn,
149:                 userData: abi.encode(JoinKind.EXACT_TOKENS_IN_FOR_BPT_OUT, assetsIn, poolActionParams.minOut),
150:                 fromInternalBalance: false
151:             })
```

When a user call `PoolAction::transferAndJoin`, if `maxAmountIn` is set higher than `assetsIn`, then `maxAmountIn - assetsIn` will stay in the proxy, as `_transferFrom` is called with `maxAmountsIn` as the amount, while only `assetsIn` is transfered to the pool:

```solidity
File: src/proxy/PoolAction.sol
75:     function transferAndJoin(
76:         address from,
77:         PermitParams[] calldata permitParams,
78:         PoolActionParams calldata poolActionParams
79:     ) external {
80:         if (from != address(this)) {     //* this "if" statement is to _transferFrom the assets before calling 'join'
81:             if (poolActionParams.protocol == Protocol.BALANCER) {
82:                 (, address[] memory assets, , uint256[] memory maxAmountsIn) = abi.decode(
83:                     poolActionParams.args,
84:                     (bytes32, address[], uint256[], uint256[])
85:                 );
86: 
87:                 if (assets.length != permitParams.length) {
88:                     revert PoolAction__transferAndJoin_invalidPermitParams();
89:                 }
90: 
91:                 for (uint256 i = 0; i < assets.length; ) {
92:                     if (maxAmountsIn[i] != 0) {
93:❌                      _transferFrom(assets[i], from, address(this), maxAmountsIn[i], permitParams[i]);
94:                     }
95: 
96:                     unchecked {
97:                         ++i;
98:                     }
99:                 }
```

## Impact
Users could see their funds temporarily stuck in the proxy


## Tools Used
Manual review

## Recommended Mitigation Steps
Return the excess funds to the caller after joining the pool. 


____________________________________
# I-01 - `config.js` PendleLPOracle twap value is set to `180s` which is very low

## Vulnerability details

This file is not in scope, that is why this is "informational"

Pendle advise integrator to use a twap duration of [900s or 1800s](https://docs.pendle.finance/Developers/Oracles/HowToIntegratePtAndLpOracle#second-choose-a-market--duration)
> The recommended duration is 15 mins (900 secs) or 30 mins (1800 secs), but it can vary depending on the market.

This might be a typo that could have a large impact

```solidity
File: scripts/config.js
77:   "Vaults": {
78:     "PendleVault": {
79:       "name": "Pendle CDP Vault",
80:       "description": "This vault allows for borrowing and lending of assets",
81:       "type": "CDPVault",
82:       "collateralType": "ERC20",
83:       "oracle": {
84:         "type": "PendleLPOracle",
85:         "deploymentArguments": {
86:           "ptOracle": "0x66a1096C6366b2529274dF4f5D8247827fe4CEA8",
87:           "market": "0xC8eDd52D0502Aa8b4D5C77361D4B3D300e8fC81c",
88:❌         "twap": 180,
89:           "aggregator": "0x5c9C449BbC9a6075A2c061dF312a35fd1E05fF22",
90:           "stalePeriod": 86400
```

## Impact
Manipulable oracle