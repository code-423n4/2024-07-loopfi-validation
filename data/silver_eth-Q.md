[L-1]
Setters should enforce limit checks on the new values they are assigning state variables too

    function setParameter(bytes32 parameter, uint256 data) external whenNotPaused onlyRole(VAULT_CONFIG_ROLE) {
        if (parameter == "debtFloor") vaultConfig.debtFloor = uint128(data);
        else if (parameter == "liquidationRatio") vaultConfig.liquidationRatio = uint64(data);
        else if (parameter == "liquidationPenalty") liquidationConfig.liquidationPenalty = uint64(data);
        else if (parameter == "liquidationDiscount") liquidationConfig.liquidationDiscount = uint64(data);
        else revert CDPVault__setParameter_unrecognizedParameter();
        emit SetParameter(parameter, data);
    }

a malicious owner could dos users by setting the debt floor to high or raise the liquidation discount to benefit themselves 

[L-2]
withdrawals should be allowed even if contracts are paused 


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
        uint256 assetsToUser = _amountWithFee(assets); // returns assets
        uint256 assetsSent = _amountWithWithdrawalFee(assetsToUser); // U:[LP-8] returns assets + fees
        shares = _convertToShares(assetsSent); // U:[LP-8] returns asset sent
        _withdraw(receiver, owner, assetsSent, assets, assetsToUser, shares); // U:[LP-8]
    }

    function redeem(
        uint256 shares,
        address receiver,
        address owner
    )
        public
        override(ERC4626, IERC4626)
        whenNotPaused // U:[LP-2A]
        whenNotLocked
        nonReentrant // U:[LP-2B]
        nonZeroAddress(receiver) // U:[LP-5]
        returns (uint256 assets)
    {
        uint256 assetsSent = _convertToAssets(shares); // U:[LP-9]
        uint256 assetsToUser = _amountMinusWithdrawalFee(assetsSent);
        assets = _amountMinusFee(assetsToUser); // U:[LP-9]
        _withdraw(receiver, owner, assetsSent, assets, assetsToUser, shares); // U:[LP-9]
    }



this is to ensure users can still save some of their funds in case of an emergency and that protocols owners cannot dos them

[L-3]
nonreentrant should not internal functions as they might collide with the one on the parent function 


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
[L-1]
Setters should enforce limit checks on the new values they are assigning state variables too

    function setParameter(bytes32 parameter, uint256 data) external whenNotPaused onlyRole(VAULT_CONFIG_ROLE) {
        if (parameter == "debtFloor") vaultConfig.debtFloor = uint128(data);
        else if (parameter == "liquidationRatio") vaultConfig.liquidationRatio = uint64(data);
        else if (parameter == "liquidationPenalty") liquidationConfig.liquidationPenalty = uint64(data);
        else if (parameter == "liquidationDiscount") liquidationConfig.liquidationDiscount = uint64(data);
        else revert CDPVault__setParameter_unrecognizedParameter();
        emit SetParameter(parameter, data);
    }
...
a malicious owner could dos users by setting the debt floor to high or raise the liquidation discount to benefit themselves 

[L-2]
withdrawals should be allowed even if contracts are paused 


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
        uint256 assetsToUser = _amountWithFee(assets); // returns assets
        uint256 assetsSent = _amountWithWithdrawalFee(assetsToUser); // U:[LP-8] returns assets + fees
        shares = _convertToShares(assetsSent); // U:[LP-8] returns asset sent
        _withdraw(receiver, owner, assetsSent, assets, assetsToUser, shares); // U:[LP-8]
    }

    function redeem(
        uint256 shares,
        address receiver,
        address owner
    )
        public
        override(ERC4626, IERC4626)
        whenNotPaused // U:[LP-2A]
        whenNotLocked
        nonReentrant // U:[LP-2B]
        nonZeroAddress(receiver) // U:[LP-5]
        returns (uint256 assets)
    {
        uint256 assetsSent = _convertToAssets(shares); // U:[LP-9]
        uint256 assetsToUser = _amountMinusWithdrawalFee(assetsSent);
        assets = _amountMinusFee(assetsToUser); // U:[LP-9]
        _withdraw(receiver, owner, assetsSent, assets, assetsToUser, shares); // U:[LP-9]
    }



this is to ensure users can still save some of their funds in case of an emergency and that protocols owners cannot dos them

[L-3]
nonreentrant should not internal functions as they might collide with the one on the parent function 


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


    function _deposit(address caller,
    address receiver,
    uint256 assets, 
    uint256 
    shares)
    internal override nonReentrant {
        super._deposit(caller, receiver, assets, shares);
        _checkMinShares();
    }
    




[L-4]
if total collateral ever reach uint256.max, a user could withdraw all the collateral


    else if (deltaCollateral < 0) {
            uint256 amount = wmul(abs(deltaCollateral), tokenScale);
            token.safeTransfer(collateralizer, amount);
    }


delta collateral is set to -uint256.max 



    position.collateral = add(position.collateral, deltaCollateral);
    function add(uint256 x, int256 y) pure returns (uint256 z) {
    assembly ("memory-safe") {
        z := add(x, y)
    }
    if ((y > 0 && z < x) || (y < 0 && z > x)) revert Math__add_overflow_signed();
}
since operations involving int dont underflow, z would end up being x -1 which would pass all checks allowing the malicious actor to withdraw all the collateral in the protocol 




[L-4]
if total collateral ever reach uint256.max, a user could withdraw all the collateral



    else if (deltaCollateral < 0) {
            uint256 amount = wmul(abs(deltaCollateral), tokenScale);
            token.safeTransfer(collateralizer, amount);
        }
delta collateral is set to -uint256.max 


    position.collateral = add(position.collateral, deltaCollateral);
    function add(uint256 x, int256 y) pure returns (uint256 z) {
    assembly ("memory-safe") {
        z := add(x, y)
    }
    if ((y > 0 && z < x) || (y < 0 && z > x)) revert Math__add_overflow_signed();
}
since operations involving int dont underflow, z would end up being x -1 which would pass all checks allowing the malicious actor to withdraw all the collateral in the protocol 