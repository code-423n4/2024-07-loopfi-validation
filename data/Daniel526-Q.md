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

## B. Inadequate Time Checks for Updating Base Interest and Quota Revenue
[PoolV3.sol#L642-L668](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L642-L668)
Vulnerability Detail
The current implementation of the `_updateBaseInterest` function uses `!=` to check if the current time (`block.timestamp`) is different from the last recorded update time ([lastBaseInterestUpdate_](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L651) and [lastQuotaRevenueUpdate](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L656)). This check can lead to incorrect updates if the function is called multiple times within the same block or before sufficient time has passed.
The function checks if the current block timestamp is different from `lastBaseInterestUpdate_` and `lastQuotaRevenueUpdate`.
If the condition is true, it updates `_baseInterestIndexLU` and sets `lastBaseInterestUpdate` to the current timestamp. Similarly, it updates `lastQuotaRevenueUpdate`.
The use of `!=` may not correctly handle cases where the function is called in rapid succession or if it is invoked before the intended update interval has elapsed.

### Impact
Using `!=` for time checks can lead to scenarios where the function does not update the interest or revenue correctly, especially if multiple updates are attempted within the same block or if the function is called before the designated update time. This could result in stale interest or revenue values, which may affect the accuracy of calculations and financial operations dependent on these values.

### Mitigation
Replace the `!=` checks with `>=` to ensure that the updates occur if and only if the current timestamp is greater than or equal to the last recorded update time.

## C. Missing whenNotLocked Check in deposit and mint Functions
[PoolV3.sol#L261-L275](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L261-L275)
[PoolV3.sol#L231-L245](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L231-L245)
The `deposit` and `mint` functions in the provided smart contract are missing the whenNotLocked modifier. This could allow users to deposit or mint tokens even when the pool is in a locked state, potentially leading to undesired behavior.
Both functions perform critical operations: `deposit` allows users to deposit underlying tokens in exchange for pool `shares`, and `mint` allows minting a specified number of pool `shares`. Without the `whenNotLocked` check, these operations can be performed even when the pool should not accept deposits or mint new shares, which could disrupt the intended functionality and integrity of the pool.

### Impact:

Allowing deposits and mints while the pool is locked can lead to unwanted token transactions and state changes, potentially causing inconsistencies in the pool's state and affecting other users who expect the pool to be in a non-active state during the lock period.

### Mitigation:

Add the `whenNotLocked` modifier to both the `deposit` and `mint` functions to ensure they can only be executed when the pool is not locked.

## D. Potential Denial of Service (DoS) Due to Out-of-Gas (OOG) Error in transferAndJoin Function
[PoolAction.sol#L75-L110](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PoolAction.sol#L75-L110)
The `transferAndJoin` function in the `PoolAction` contract is designed to facilitate the transfer of tokens from an externally owned account (EOA) and subsequently join a liquidity pool using the specified protocol (Balancer or Pendle). This function processes multiple token transfers and interactions within loops, potentially leading to high gas consumption and an Out-of-Gas (OOG) error.
```solidity
            for (uint256 i = 0; i < assets.length; ) {
                if (maxAmountsIn[i] != 0) {
                    _transferFrom(assets[i], from, address(this), maxAmountsIn[i], permitParams[i]);
                }

                unchecked {
                    ++i;
                }
            }
```
The function decodes `poolActionParams.args` to obtain the list of assets and their corresponding maximum amounts to be transferred. It then iterates over the `assets` and `permitParams` arrays to execute `_transferFrom` operations, transferring tokens from the `from` address to the contract. If these arrays are large, the cumulative gas usage can exceed the block gas limit, causing an OOG error and failing the transaction.

### Impact:
A Denial of Service (DoS) can occur if the transaction runs out of gas due to the high number of token transfers and approvals. This prevents users from successfully executing the `transferAndJoin` function, potentially disrupting the contract's intended operations and affecting user experience.

### Mitigation:
To mitigate the risk of an OOG error, impose a limit on the maximum length of `assets` and `permitParams` arrays.

## E. Array Length Mismatch in `updateLeverJoin` Function
The `updateLeverJoin` function in the `PoolAction` contract is designed to update the `join` parameters for a levered position when using the Balancer protocol. It decodes `poolActionParams.args` into `poolId`, `assets`, `assetsIn`, and `maxAmountsIn`, and then updates these arrays based on the provided input parameters. However, there is a potential issue where the lengths of `assets`, `assetsIn`, and `maxAmountsIn` may not be equal, leading to out-of-bounds array access and subsequent reversion of the transaction.

```solidity
        if (poolActionParams.protocol == Protocol.BALANCER) {
            (bytes32 poolId, address[] memory assets, uint256[] memory assetsIn, uint256[] memory maxAmountsIn) = abi
                .decode(poolActionParams.args, (bytes32, address[], uint256[], uint256[]));

            uint256 len = assets.length;
            // the offset is needed because of the BPT token that needs to be skipped from the join
            bool skipIndex = false;
            uint256 joinAmount = flashLoanAmount;
            if (upFrontToken == joinToken) {
                joinAmount += upfrontAmount;
            }

            // update the join parameters with the new amounts // @audit array lengths mismatch
            for (uint256 i = 0; i < len; ) {
                uint256 assetIndex = i - (skipIndex ? 1 : 0);
                if (assets[i] == joinToken) {
                    maxAmountsIn[i] = joinAmount;
                    assetsIn[assetIndex] = joinAmount;
                } else if (assets[i] == upFrontToken && assets[i] != poolToken) {
                    maxAmountsIn[i] = upfrontAmount;
                    assetsIn[assetIndex] = upfrontAmount;
                } else {
                    skipIndex = skipIndex || assets[i] == poolToken;
                }
                unchecked {
                    i++;
                }
            }
```
The primary issue lies in the assumption that `assetsIn` and `maxAmountsIn` have the same length as `assets`, which may not always be the case. This mismatch can cause out-of-bounds errors when accessing or modifying elements of these arrays.

### Impact:
An array length mismatch can lead to out-of-bounds array access, causing the transaction to revert. This disrupts the normal functionality of the `updateLeverJoin` function, preventing users from successfully updating their join parameters for leveraged positions. It can also potentially lock funds or create inconsistencies in the protocol's state.
### Mitigation:
To mitigate this issue, explicitly check that the lengths of assets, assetsIn, and maxAmountsIn are equal before performing any operations on these arrays. This ensures that any mismatch is caught early, preventing out-of-bounds errors and ensuring the function operates correctly.