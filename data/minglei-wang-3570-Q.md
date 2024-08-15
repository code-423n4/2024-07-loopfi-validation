## Summary

| No | Title |
| --- | --- |
| [L-01] | In PoolV3, the treasury address cannot be changed |
| [L-02] | In StakingLPEth, the cool period in `cooldownShares()` can be bypassed |
| [L-03] | Without try-catch, permit can be DOSed |
| [L-04] | Hardcoded values |
| [L-05] | Improper slippage control on pool deposit and withdraw | 
| [L-06] | `onCreditFlashLoan()` should validate the input params |
| [L-07] | The reward related contracts are forked from radiant protocol and comments are not updated |
| [L-08] | Functions updating state doesn't emit events | 

## Low findings
### [L-01] In PoolV3, the treasury address cannot be changed 

```solidity
     treasury = IAddressProviderV3(addressProvider_).getAddressOrRevert({
            key: AP_TREASURY,
            _version: NO_VERSION_CONTROL
        }); // U:[LP-1B]
```
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L171

treasure address is set and cannot be changed, but in case the treasury is updated in addressProvider_,



the treasury addrss in Pool contract is out of sync.

#### Recommendation

add a function to update and sync the treasury address.

### [L-02] In StakingLPEth, the cool period in `cooldownShares()` can be bypassed.
#### Impact
The cool down period in `cooldownShares()` can be bypassed.
Suppose the timelock is set to 1 days. User A wants to queue withdraw at timestamp 1 day, he burn the share and schedule the timelock. After 12 hours (half a day), user A wants to queue withdraw again, but if he queue the withdraw, the timelock is extended and user has to wait for another day. User A can transfer the share out and use a different account to queue the withdraw and bypass the timelock. The root cause is transfer token (share) out does not update cooldowns[msg.sender].cooldownEnd.

```solidity
    function cooldownShares(uint256 shares) external ensureCooldownOn returns (uint256 assets) {
        if (shares > maxRedeem(msg.sender)) revert ExcessiveRedeemAmount();

        assets = previewRedeem(shares);

        cooldowns[msg.sender].cooldownEnd = uint104(block.timestamp) + cooldownDuration;
        cooldowns[msg.sender].underlyingAmount += uint152(assets);

        _withdraw(msg.sender, address(silo), msg.sender, assets, shares);
    }
```

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L104
#### Recommendation
Token (share) out should update `cooldowns[msg.sender].cooldownEnd.`


### [L-03] Without try-catch, permit can be DOSed.
#### Impact
Attacker can frontrun the transaction and extracts signature parameters from `_transferFrom()` call and frontruns it with a direct permit(). In this case, the end result is harmful, since the user loses the functionality that follows the permit().

In fact, any function call that unconditionally performs permit() can be forced to revert this way. In case there is a fallback code path (using direct user approval), the DOS is short-term, as eventually the user / dApp would switch to using an alternative. Otherwise, the DOS is long-term (there are bypasses through flashbots in some chains, but that's a really bad scenario to resort to). 

This would consume the nonce associated with the user's permit and cause the original transaction to fail due to the now-invalid nonce.

```solidity
  function _transferFrom(
        address token,
        address from,
        address to,
        uint256 amount,
        PermitParams memory params
    ) internal {
        if (params.approvalType == ApprovalType.PERMIT2) {
            // Consume a permit2 message and transfer tokens.
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
        } else if (params.approvalType == ApprovalType.PERMIT) {
            // Consume a standard ERC20 permit message
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
            // No signature provided, just transfer tokens.
            IERC20(token).safeTransferFrom(from, to, amount);
        }
    }
```
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/TransferAction.sol#L67

#### Recommendation
We recommend to consider implementing a mechanism to check if an allowance for the desired amount exists in case the call to permit() fails.


### [L-04]  Hardcoded values
Do not hardcode values and these values can be passed in case these values / contract address changed.

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L47

```solidity
    address private constant BAL = 0xba100000625a3754423978a60c9317c58a424e3D;

    address private constant BAL_CHAINLINK_FEED = 0xdF2917806E30300537aEB49A7663062F4d1F2b5F;
    uint256 private constant BAL_CHAINLINK_DECIMALS = 1e8;

    address private constant ETH_CHAINLINK_FEED = 0x5f4eC3Df9cbd43714FE2740f5E3616155c5b8419;
    uint256 private constant ETH_CHAINLINK_DECIMALS = 1e8;

    /// @notice The AURA token
    address private constant AURA = 0xC0c293ce456fF0ED870ADd98a0828Dd4d2903DBF;

    // Utilities for AURA mining calcs
    uint256 private constant EMISSIONS_MAX_SUPPLY = 5e25; // 50m
    uint256 private constant INIT_MINT_AMOUNT = 5e25; // 50m
    uint256 private constant TOTAL_CLIFFS = 500;
    uint256 private constant REDUCTION_PER_CLIFF = 1e23;
    uint256 private constant INFLATION_PROTECTION_TIME = 1749120350;
```
#### Recommendation
Implement setter functions to set the values.

### [L-05] Improper slippage control on pool deposit

#### Impact
In `AuraVault::deposit` the function mints shares on depositing assets but function doesn't have a slippage control parameters to set the minimum mint shares, could lead to loss of funds by mev, frontrunning attacks.

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L199
```solidity
     * @notice Mints `shares` Vault shares to `receiver`.
     * @dev Because `asset` is not actually what is collected here, first wrap to required token in the booster.
     *
     * TODO: account for unclaimed rewards
     */
    function deposit(uint256 assets, address receiver) public virtual override(IERC4626, ERC4626) returns (uint256) {
        uint256 shares = previewDeposit(assets);
        _deposit(_msgSender(), receiver, assets, shares);

        // Deposit  in reward pool
        IERC20(asset()).safeApprove(rewardPool, assets);
        IPool(rewardPool).deposit(assets, address(this));

        return shares;
    }
```

#### Recommendation
We recommend adding slippage control parameters on depositing assets to the vault.



### [L-06] `onCreditFlashLoan()` should validate the input params
#### Impact
The function `onCreditFlashLoan()` is the callback function for the credit flash loan taken out in `decreaseLever`.
But three parameters 
```solidity
        address /*initiator*/,
        uint256 /*amount*/,
        uint256 /*fee*/,
```
should be sanitized so that params aren't vulnerable. One of the vulnerability is shown in our original high/medium reports.

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction.sol#L446
```solidity
  function onCreditFlashLoan(
        address /*initiator*/,
        uint256 /*amount*/,
        uint256 /*fee*/,
        bytes calldata data
    ) external returns (bytes32) {
        if (msg.sender != address(flashlender)) revert PositionAction__onCreditFlashLoan__invalidSender();
        (
            LeverParams memory leverParams,
            uint256 subCollateral,
            address residualRecipient
        ) = abi.decode(data,(LeverParams, uint256, address));

        uint256 subDebt = leverParams.primarySwap.amount;

        underlyingToken.forceApprove(address(leverParams.vault), subDebt);
        // sub collateral and debt
        ICDPVault(leverParams.vault).modifyCollateralAndDebt(
            leverParams.position,
            address(this),
            address(this),
            0,
            -toInt256(subDebt)
        );
```
#### Recommendation
Sanitize the values.

### [L-07] The reward related contracts are forked from radiant protocol and comments are not updated
#### Impact
Here the the reward related contracts are fork from radiant protocol, here the comment `Returns locked RDNT and LP token value in USD` is incorrect a/c to this protocol.

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/EligibilityDataProvider.sol#L271
```solidity
    /********************** Internal functions ***********************/

    /**
     * @notice Returns locked RDNT and LP token value in USD
     * @param lockedLP is locked lp amount
     */
    function _lockedUsdValue(uint256 lockedLP) internal view returns (uint256) {
        uint256 lpPrice = priceProvider.getLpTokenPriceUsd();
        return (lockedLP * lpPrice) / 10 ** 18;
    }
```
#### Recommendation
Update the comments
### [L-08] Functions updating state doesn't emit events
#### Impact
To record the init parameters for off-chain monitoring and transparency reasons, please consider emitting an event after the `setAllowed()` and `setLock()` functions.

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L838
```solidity
   function setAllowed(address account, bool status) external controllerOnly {
        _allowed[account] = status;
        // @audit
        // missing state emission is QA
    }

    /// @notice Locks or unlocks the pool, can only be called by controller
    /// @param status `true` to lock, `false` to unlock, default pool status is locked
    function setLock(bool status) external controllerOnly {
        locked = status;
        // missing state emission is QA
    }
```
#### Recommendation
We recommend to emit events as the state is changed.


