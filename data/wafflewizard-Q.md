
Title: Precision Loss in Fixed-Point Arithmetic Operations in deposit Function

Severity: Low 


Description:

The deposit function in the contract uses fixed-point arithmetic operations that may lead to precision loss. Specifically, the function performs a division using the wdiv function, which scales down the input amount by a tokenScale factor. This division may result in the truncation of small fractional values, leading to a loss of precision. When the result of this division is later multiplied by tokenScale using the wmul function, the original value cannot be fully recovered, causing discrepancies.

Affected Code:

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L223C4-L225C56
function deposit(address to, uint256 amount) external whenNotPaused returns (uint256 tokenAmount) {
        tokenAmount = wdiv(amount, tokenScale);
        int256 deltaCollateral = toInt256(tokenAmount);
        modifyCollateralAndDebt({
            owner: to,
            collateralizer: msg.sender,
            creditor: msg.sender,
            deltaCollateral: deltaCollateral,
            deltaDebt: 0
        });
    }
The wdiv function performs the following operation:


function wdiv(uint256 x, uint256 y) pure returns (uint256 z) {
    assembly ("memory-safe") {
        // Equivalent to `require(y != 0 && (WAD == 0 || x <= type(uint256).max / WAD))`.
        if iszero(mul(y, iszero(mul(WAD, gt(x, div(not(0), WAD)))))) {
            // Store the function selector of `Math__div_overflow()`.
            mstore(0x00, 0xbcbede65)

            // Revert with (offset, size).
            revert(0x1c, 0x04)
        }
        z := div(mul(x, WAD), y)
    }
}
Impact:

The precision loss may lead to minor discrepancies in the collateral amounts recorded for users. In certain edge cases, these discrepancies could accumulate over multiple transactions, leading to potential financial inaccuracies. While the individual impact per transaction may be small, it could become significant over time or in specific scenarios involving large amounts or frequent transactions.

Reproducibility:

Deploy the contract with a tokenScale factor that is not an exact multiple of the amount.
Execute the deposit function with an amount that has a small fractional part when divided by tokenScale.
Observe that the tokenAmount calculated by wdiv truncates the fractional part, leading to a discrepancy when multiplied back by tokenScale.
Recommendation:

To mitigate the precision loss, consider the following approaches:

Delay Division: Perform multiplication before division where possible to preserve precision. For example:


uint256 scaledAmount = amount * WAD;
tokenAmount = wdiv(scaledAmount, tokenScale);

Avoid Premature Scaling: Reassess the use of tokenScale and consider whether it is necessary to scale down the amount at this stage of the calculation.

Conclusion:

While the precision loss identified in the deposit function may only lead to minor discrepancies, it is essential to address it to ensure the accuracy and integrity of the contract's financial calculations. Given the potential for cumulative impact, this finding is rated as Low Severity.


Title :Reentrancy Risk in SafeTransferFrom and SafeTransfer in ModifyCollateralAndDebt

The ModifyCollateralAndDebt function in this protocol includes calls to SafeTransferFrom and SafeTransfer, which, while generally safe, can introduce a reentrancy risk under certain conditions. Although the protocol does not utilize ERC-777 tokens (which are particularly vulnerable to reentrancy due to their tokensReceived hook), it is still important to adhere to best practices to mitigate any potential risks.

Details:

The function modifies a position’s collateral and debt balances by transferring tokens either to or from the user’s account.
The token transfer operations (safeTransferFrom and safeTransfer) occur after certain state variables are updated. Although the protocol’s token does not have reentrancy hooks, it’s generally safer to ensure that all state changes are completed before interacting with external contracts.
The current implementation is less likely to be exploited because it doesn’t involve ERC-777 tokens or other complex token standards with hooks that can be triggered during transfers. However, using the checks-effects-interactions pattern would further enhance security.

Recommendation:
To adhere to best practices, it is recommended to structure the modifyCollateralAndDebt function following the checks-effects-interactions pattern:

Checks: Perform all necessary validations and checks.
Effects: Update state variables to reflect the changes.
Interactions: Transfer tokens or interact with external contracts.
By implementing this pattern, you can further mitigate the risk of reentrancy, even if the protocol’s environment changes in the future.

Conclusion:
While the current implementation is relatively safe, adopting the checks-effects-interactions pattern is a good practice to prevent potential reentrancy vulnerabilities in the future. The risk is assessed as non-critical due to the specific context of the protocol.