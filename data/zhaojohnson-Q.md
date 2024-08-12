[L01] When users use `_transferFrom()` to transfer funds via ApprovalType.PERMIT approval type, the transaction may be reverted if malicious user frontrun the related permit.
```javascript
    function _transferFrom(
        address token,
        address from,
        address to,
        uint256 amount,
        PermitParams memory params
    ) internal {
        if (params.approvalType == ApprovalType.PERMIT2) {
            ...
        } else if (params.approvalType == ApprovalType.PERMIT) {
            // Consume a standard ERC20 permit message
            // @audit this will be blocked.
            IERC20Permit(token).safePermit(
                from,
                to,
                params.approvalAmount,
                params.deadline,
                params.v,
                params.r,
                params.s
            );
            IERC20(token).safeTransferFrom(from, to, amount);
        } else {
            ...
        }
    }
```
[L02] In transferAndSwap(), if users use `ApprovalType.PERMIT2` approval type, the malicious user can frontrun `permitTransferFrom` to cause users' transferAndSwap() reverted.
```javascript
    function transferAndSwap(
        address from,
        PermitParams calldata permitParams,
        SwapParams calldata swapParams
    ) external returns (uint256) {
        if (from != address(this)) {
            uint256 amount = swapParams.swapType == SwapType.EXACT_IN ? swapParams.amount : swapParams.limit;
            _transferFrom(swapParams.assetIn, from, address(this), amount, permitParams);
        }
        return swap(swapParams);
    }
```
```javascript
    function _transferFrom(
        address token,
        address from,
        address to,
        uint256 amount,
        PermitParams memory params
    ) internal {
        if (params.approvalType == ApprovalType.PERMIT2) {
            ISignatureTransfer(permit2).permitTransferFrom(
                ISignatureTransfer.PermitTransferFrom({
                    permitted: ISignatureTransfer.TokenPermissions({token: token, amount: params.approvalAmount}),
                    nonce: params.nonce,
                    deadline: params.deadline
                }),
                ISignatureTransfer.SignatureTransferDetails({to: to, requestedAmount: amount}),
                from,
                bytes.concat(params.r, params.s, bytes1(params.v)) // Construct signature
            );
        }
    ...
}
```
[L03] Improper decimal convert between PositionAction and CDPVault.
In CDPVault, the return value of withdraw() is tokenAmount [wad].
```javascript
    /// @param amount Amount of tokens to withdraw [tokenScale]
    /// @return tokenAmount Amount of tokens withdrawn [wad]
    // decrease the collateral.
    function withdraw(address to, uint256 amount) external whenNotPaused returns (uint256 tokenAmount) {
        tokenAmount = wdiv(amount, tokenScale);
        ...
    }
```
In PositionAction, the _onWithdraw()'s expected token amount is token amount [scale].
```javascript
    /// @return Amount of collateral (or dst) withdrawn [CDPVault.tokenScale()]
    function _onWithdraw(address vault, address position, address dst, uint256 amount) internal virtual returns (uint256);
    function _onWithdraw(address vault, address position, address /*dst*/, uint256 amount) internal override returns (uint256) {
        return ICDPVault(vault).withdraw(position, amount);
    }
```
Considering that the underlying token in CDPVault is Pendle LPT token. And these Pendle LPT Token's decimal is 18. So the wad amount equals the scale amount. This vulnerability will not have one big effect based on this contest's scope.
Considering that LoopFi may integrate with some other underlying tokens, it's better to fix this to avoid all kinds of unexpected behaviour.

