## A. Token Transfer Order in Flash Loan Implementation
[Flashlender.sol#L101-L102](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/Flashlender.sol#L101-L102)
In the current implementation of the `flashLoan` function in the `Flashlender` contract, the token transfer to the receiver happens after the callback is executed. The provided [documentation](https://eips.ethereum.org/EIPS/eip-3156#:~:text=The%20flashLoan%20function%20MUST%20transfer%20amount%20of%20token%20to%20receiver%20before%20the%20callback%20to%20the%20receiver.) specifies that the token amount must be transferred to the receiver before invoking the callback.

Here's the relevant code snippet from the flashLoan function:
```solidity
    function flashLoan(
        IERC3156FlashBorrower receiver,
        address token,
        uint256 amount,
        bytes calldata data
    ) external override nonReentrant returns (bool) {
        if (token != address(underlyingToken)) revert Flash__flashLoan_unsupportedToken();
        uint256 fee = wmul(amount, protocolFee);
        uint256 total = amount + fee;

        pool.lendCreditAccount(amount, address(receiver));

        emit FlashLoan(address(receiver), token, amount, fee);

        if (receiver.onFlashLoan(msg.sender, token, amount, fee, data) != CALLBACK_SUCCESS)
            revert Flash__flashLoan_callbackFailed();

        // reverts if not enough Stablecoin have been send back
        underlyingToken.transferFrom(address(receiver), address(pool), total);
        pool.repayCreditAccount(total - fee, fee, 0);

        return true;
    }
```
In the code above:
- `pool.lendCreditAccount(amount, address(receiver));` is used for internal credit management.
- `receiver.onFlashLoan(...)` is the callback that should be executed after the token transfer.
### Impact:

If the token transfer to the receiver occurs after the callback, it could lead to unintended behaviors or attacks where the callback execution might depend on the availability of the borrowed tokens. This can compromise the intended functionality of the flash loan if the callback assumes that the tokens have already been transferred.

### Mitigation:

Ensure that the tokens are transferred to the receiver before executing the callback. 