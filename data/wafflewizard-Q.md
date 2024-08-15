
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

While the precision loss identified in the deposit function may only lead to minor discrepancies, it is essential to address it to ensure the accuracy and integrity of the contract's financial calculations. Given the potential for cumulative impact, this finding is rated as Low Severity