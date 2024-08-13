## [L-01] Pausable Contracts are Non-Compliant with OpenZeppelin

The contract CDPVault inherits from pause contract whose current implementation lacks proper checks in the pause and unPause functions, potentially leading to security vulnerabilities. 

```javascript
import {Pause, PAUSER_ROLE} from "./utils/Pause.sol";
```

```javascript
contract CDPVault is AccessControl, `Pause`, Permission, ICDPVaultBase {
```

If the contract allows pausing even when it is already paused, it may result in unintended consequences such as redundant state changes or disruptions to normal contract operations. This can confuse users and affect the expected behavior of the contract.

Allowing the contract to be unpaused when it is not already paused can introduce security risks by potentially enabling unauthorized actions or changes to the contract state. This could lead to exploitation by malicious actors, resulting in financial losses or other detrimental outcomes.

Failure to enforce proper pausing and unpausing conditions undermines the contract owner's ability to effectively manage and control contract operations. This loss of control may lead to scenarios where critical functions are executed inadvertently or inappropriately.

```javascript
    /// @notice Pauses the contract
    /// @dev Sender has to be allowed to call this method
    function pause() external onlyRole(PAUSER_ROLE) {
        _pause();
    }

    /// @notice Unpauses the contract
    /// @dev Sender has to be allowed to call this method
    function unpause() external onlyRole(PAUSER_ROLE) {
        _unpause();
        pausedAt = 0;
    }
```

As it is the functions lack modifiers to prevent the functions from being called when the contract is paused or unpaused

POC

The OpenZeppelin implementation of pausable contracts includes specific requirements in the comments for the pause and unpause functions:

Pause Function:
According to the comments, the contract must not be paused when calling _pause.
```javascript
 /**
     * @dev Triggers stopped state.
     *
     * Requirements:
     *
@>>>>  * - The contract must not be paused.
     */
    function _pause() internal virtual whenNotPaused {
        _paused = true;
        emit Paused(_msgSender());
    }

   
```
Unpause Function:
As per the comments, the contract must be paused when calling _unpause.

```javascript
 /**
     * @dev Returns to normal state.
     *
     * Requirements:
     *
@>>>     * - The contract must be paused.
     */
    function _unpause() internal virtual whenPaused {
        _paused = false;
        emit Unpaused(_msgSender());
    }
```
recommendation

the contract should be modified to include proper checks in the pause and unPause functions:

Add Modifier to pause: Incorporate the `whenNotPaused` modifier to ensure that the contract can only be paused when it is not already in the paused state.

```javascript
function pause() external onlyRole(PAUSER_ROLE) whenNotPaused {
        _pause();
    }
```


Add Modifier to unPause: Utilize the `whenPaused` modifier to guarantee that the contract can only be unpaused when it is in the paused state.

```javascript
function unpause() external onlyRole(PAUSER_ROLE) whenPaused {
        _unpause();
        pausedAt = 0;
    }
```

## [L-02] User could deposit and get 0 shares in return

When a user deposits tokens into the protocol, the user could receive 0 shares in return as there is no check to prevent this from happening, This issue can occur due to the calculations involved in determining the amount of shares to be minted.

Once a user deposits some amount in the protocol it should always be checked that they user dont get minted 0 shares in return

```javascript
/// @notice Deposits given amount of underlying tokens to the pool in exchange for pool shares
    /// @param assets Amount of underlying to deposit
    /// @param receiver Account to mint pool shares to
    /// @return shares Number of shares minted
    function deposit(
        uint256 assets,
        address receiver
    )
        public
        override(ERC4626, IERC4626)
        whenNotPaused // U:[LP-2A]
        nonReentrant // U:[LP-2B]
        nonZeroAddress(receiver) // U:[LP-5]
        returns (uint256 shares)
    {
        uint256 assetsReceived = _amountMinusFee(assets); // U:[LP-6]
        shares = _convertToShares(assetsReceived); // U:[LP-6]
        _deposit(receiver, assets, assetsReceived, shares); // U:[LP-6]
    }
```

```javascript
function _deposit(address receiver, uint256 assetsSent, uint256 assetsReceived, uint256 shares) internal {
        IERC20(underlyingToken).safeTransferFrom({from: msg.sender, to: address(this), value: assetsSent}); // U:[LP-6,7]

        _updateBaseInterest({
            expectedLiquidityDelta: assetsReceived.toInt256(),
            availableLiquidityDelta: 0,
            checkOptimalBorrowing: false
        }); // U:[LP-6,7]

        _mint(receiver, shares); // U:[LP-6,7]
        emit Deposit(msg.sender, receiver, assetsSent, shares); // U:[LP-6,7]
    }
```

The mint function is surposed to mint the receiver some shares based on the assets they deposit but it lacs logic to revert if no shares are minted which might cause a user to deposit but get minted 0 shares in return

## Proof of Concept(POC)

The user initiates a deposit transaction with some amount of assets
Due to some state manipulation or calculation errors, the function calculates the assets deposited to mint as 0 shares.
Result:

The user receives 0 shares in return.

## Recommendation
implement a check to ensure that the amount of shares to be minted is non-zero before proceeding with the deposit. If the scaled amount is 0, the transaction should revert with an appropriate error message.

```javascript
// Revert if the amount of shares minted is zero
    if (shares == 0) {
        revert Errors.ZERO_SHARES_MINTED();
    }
```
## [L-03] Users will not be able to deposit collateral when paused

currently the function below in `CDPVault` allows users to deposit collateral in the protocol to avoid liquidation.

```javascript
 /// @notice Deposits collateral tokens into this contract and increases a user's collateral balance
    /// @dev The caller needs to approve this contract to transfer tokens on their behalf
    /// @param to Address of the user to attribute the collateral to
    /// @param amount Amount of tokens to deposit [tokenScale]
    /// @return tokenAmount Amount of collateral deposited [wad]
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
```
since this function can only be called when the contract is not paused based on the modifier `whenNotPaused` this will cause users to get liquidated when the contract is paused. see the liquidation function below

```javascript
/// @notice Liquidates a single unsafe position by selling collateral at a discounted (`liquidationDiscount`)
    /// oracle price. The liquidator has to provide the amount he wants to repay or sell (`repayAmounts`) for
    /// the position. From that repay amount a penalty (`liquidationPenalty`) is subtracted to mitigate against
    /// profitable self liquidations. If the available collateral of a position is not sufficient to cover the debt
    /// the vault accumulates 'bad debt'.
    /// @dev The liquidator has to approve the vault to transfer the sum of `repayAmounts`.
    /// @param owner Owner of the position to liquidate
    /// @param repayAmount Amount the liquidator wants to repay [wad]
    function liquidatePosition(address owner, uint256 repayAmount) external whenNotPaused {}
```
When contract is unPaused liquidations will start and now that users were not able to add collateral to save their positions in time they might end up being liquidated.

## Recommendation

consider removing the `whenNotPaused` modifier in the deposit function so that users will still be able do add collateral even when the contract is paused



## [L-04] Liquidation does not prioritize lower LTV assets

Impact
The current implementation of the liquidatePositionBadDebt function does not account for the potential imbalance caused by liquidating positions with varying Loan-to-Value (LTV) ratios. Specifically, if a user has multiple positions with different LTVs—such as one at 20% and another at 80%—partial liquidation of the higher LTV position could inadvertently worsen the overall risk profile of the remaining positions. This is because the function does not prioritize or account for LTV ratios when deciding which positions to liquidate. As a result, liquidating high-LTV collateral could lead to a scenario where the remaining positions are even more under-collateralized, increasing the risk of further financial instability or losses.

```javascript
 /// @dev The liquidator has to approve the vault to transfer the sum of `repayAmounts`.
    /// @param owner Owner of the position to liquidate
    /// @param repayAmount Amount the liquidator wants to repay [wad]
    function liquidatePositionBadDebt(address owner, uint256 repayAmount) external whenNotPaused {
        // validate params
        if (owner == address(0) || repayAmount == 0) revert CDPVault__liquidatePosition_invalidParameters();

        // load configs
        VaultConfig memory config = vaultConfig;
        LiquidationConfig memory liqConfig_ = liquidationConfig;

        // load liquidated position
        Position memory position = positions[owner];
        DebtData memory debtData = _calcDebt(position);
        uint256 spotPrice_ = spotPrice();
        if (spotPrice_ == 0) revert CDPVault__liquidatePosition_invalidSpotPrice();
        // verify that the position is indeed unsafe
        if (_isCollateralized(calcTotalDebt(debtData), wmul(position.collateral, spotPrice_), config.liquidationRatio))
            revert CDPVault__liquidatePosition_notUnsafe();

        // load price and calculate discounted price
        uint256 discountedPrice = wmul(spotPrice_, liqConfig_.liquidationDiscount);
        // Enusure that the debt is greater than the collateral at discounted price
        if (calcTotalDebt(debtData) <= wmul(position.collateral, discountedPrice)) revert CDPVault__noBadDebt();
        // compute collateral to take, debt to repay
        uint256 takeCollateral = wdiv(repayAmount, discountedPrice);
        if (takeCollateral < position.collateral) revert CDPVault__repayAmountNotEnough();

        // account for bad debt
        takeCollateral = position.collateral;
        repayAmount = wmul(takeCollateral, discountedPrice);
        uint256 loss = calcTotalDebt(debtData) - repayAmount;

        // transfer the repay amount from the liquidator to the vault
        poolUnderlying.safeTransferFrom(msg.sender, address(pool), repayAmount);

        position.cumulativeQuotaInterest = 0;
        position.cumulativeQuotaIndexLU = debtData.cumulativeQuotaIndexNow;
        // update liquidated position
        position = _modifyPosition(
            owner,
            position,
            0,
            debtData.cumulativeIndexNow,
            -toInt256(takeCollateral),
            totalDebt
        );

        pool.repayCreditAccount(debtData.debt, 0, loss); // U:[CM-11]
        // transfer the collateral amount from the vault to the liquidator
        token.safeTransfer(msg.sender, takeCollateral);

        int256 quotaRevenueChange = _calcQuotaRevenueChange(-int(debtData.debt));
        if (quotaRevenueChange != 0) {
            IPoolV3(pool).updateQuotaRevenue(quotaRevenueChange); // U:[PQK-15]
        }
    }

```

## POC

A user has two positions:
Position A: 20% LTV, $10,000 collateral, $2,000 debt.
Position B: 80% LTV, $5,000 collateral, $4,000 debt.
The liquidation discount is set to 10%.
Initial Conditions:

Total debt: $6,000
Total collateral: $15,000
Liquidation ratio: 150%
Liquidation Process:

Calculate Collateral Value at Discounted Price:

Discounted price = Spot price * (1 - liquidation discount)
For simplicity, assume the spot price is 1 (normalized for this example).
Partial Liquidation Decision:

Suppose the liquidator decides to repay $2,000.
Discounted price = 1 * (1 - 0.10) = 0.90
Collateral taken = repayAmount / discountedPrice = $2,000 / 0.90 = $2,222.22
Liquidation Outcome:

If the function liquidates Position B (with 80% LTV), the collateral taken will be the full amount of Position B, which is $5,000.
This repayment ($5,000 at discounted price) would be more than enough to cover the debt.

## Recommendation
To address this issue, the liquidation logic should be enhanced to consider the LTV ratios of the positions being liquidated. Specifically, the function should:

Prioritize Liquidation Based on LTV: Implement a mechanism to sort and prioritize positions for liquidation based on their LTV ratios. Positions with higher LTV ratios should be given precedence to ensure that the liquidation process addresses the most at-risk positions first.


## [L-05] Incorrect Prices Returned During Flash Crashes


The ChainlinkOracle contract currently implements a function `_fetchAndValidate` to retrieve the price from an oracle. it lacks a direct check for both minimum and maximum acceptable prices (minAnswer and maxAnswer).

```javascript
 function _fetchAndValidate(address token) internal view returns (bool isValid, uint256 price) {
        Oracle memory oracle = oracles[token];
        try AggregatorV3Interface(oracle.aggregator).latestRoundData() returns (
            uint80 /*roundId*/,
            int256 answer,
            uint256 /*startedAt*/,
            uint256 updatedAt,
            uint80 /*answeredInRound*/
        ) {
            isValid = (answer > 0 && block.timestamp - updatedAt <= oracle.stalePeriod);
            return (isValid, wdiv(uint256(answer), oracle.aggregatorScale));
        } catch {
            // return the default values (false, 0) on failure
        }
    }
```

The absence of a minimum acceptable price check leaves the protocol vulnerable to accepting abnormally low prices, particularly during flash crashes or sudden market downturns. Introducing a minAnswer parameter would allow the protocol to reject prices below a specified threshold.

Similarly, the lack of a maximum acceptable price check poses risks associated with accepting excessively high prices from the oracle. Without an upper limit, the protocol may incorrectly value assets or allocate resources based on inflated prices, leading to potential financial losses and disruptions in operations.

* Other Instances
  
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/vendor/AuraVault.sol#L365

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/vendor/AuraVault.sol#L381

Recommendation:
To mitigate these vulnerabilities and ensure the integrity of the price feed mechanism, it is recommended to introduce both minAnswer and maxAnswer parameters in the getMintRate function. This would enable the function to enforce a range of acceptable prices, reducing the risk of oracle manipulation and ensuring accurate asset valuations within the protocol.

```javascript
 if (answer >= maxPrice or price <= minPrice) revert();
```

## [L-06] nonReentrant should start

The order of modifiers can influence the behavior of a function. Generally, NonReentrant must come first than other modifiers.

 The nonReentrant modifier could be placed and executed before other modifiers in functions to prevent reentrancies through other modifiers and make code more efficient. To follow the best practice, please consider placing the nonReentrant modifier first. 

## Recommendation:
Reorder modifiers so that NonReentrant is placed before other modifiers.

***Instances***

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/PoolV3.sol#L238

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/PoolV3.sol#L268

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/PoolV3.sol#L301

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/PoolV3.sol#L325

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/PoolV3.sol#L492

## [L-07] Potential Decimal Issue in _fetchAndValidate Function

Impact
The `_fetchAndValidate` function currently does not scale the price data to 18 decimal places, which is a common standard for financial calculations in Ethereum smart contracts. If the oracle returns prices with fewer decimal places (e.g., 8 decimals), and these prices are not scaled appropriately, it could lead to significant inaccuracies in calculations that depend on these prices. This can affect price validations, trading logic, or any financial operations that rely on accurate price data, potentially resulting in incorrect behavior, financial losses, or vulnerabilities in the contract.

## Proof of Concept (PoC)
To demonstrate how this issue might affect the system, consider the following scenario:

Setup:

An oracle returns a price with 8 decimals. For example, it returns a price of 1.23456789.
The system expects prices to be scaled to 18 decimals for consistency.
Example Calculation:

Oracle price (with 8 decimals): 1.23456789
Expected system price (with 18 decimals): 1.23456789 * 10^10

If the function does not scale the price, the returned price will be interpreted as 1.23456789 without the necessary scaling adjustment.
This discrepancy can lead to incorrect price calculations, potentially affecting any logic that depends on these prices.
Detailed PoC Code:

```javascript
// Assume oracle.aggregatorDecimals is 8
Oracle memory oracle = oracles[token];
try AggregatorV3Interface(oracle.aggregator).latestRoundData() returns (
    uint80 /*roundId*/,
    int256 answer,
    uint256 /*startedAt*/,
    uint256 updatedAt,
    uint80 /*answeredInRound*/
) {
    isValid = (answer > 0 && block.timestamp - updatedAt <= oracle.stalePeriod);
    // Directly using the answer without scaling
    price = wdiv(uint256(answer), oracle.aggregatorScale);
    return (isValid, price);
} catch {
    // return default values on failure
}
```
In this PoC:

If the answer from the oracle is 123456789 with 8 decimals, without proper scaling, it might be treated as 0.123456789 (incorrectly).

* Other Instances
  
https://github.com/code-423n4/2024-07-loopfi/blob/main/src/vendor/AuraVault.sol#L365

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/vendor/AuraVault.sol#L381


## Recommendation
To address this issue, modify the `_fetchAndValidate` function to ensure that the price returned by the oracle is correctly scaled to 18 decimal places.

## [L-08] Avoid making withdraw function Pausable 

## Resolution 
Making withdraw or unstake functions pausable can create a risk by potentially locking users' funds indefinitely, especially in scenarios where the contract is paused for a prolonged period. This design could undermine trust and may not align with the decentralization principles of blockchain. It's advisable to design these functions with user security and accessibility in mind, ensuring that pausing capabilities are used judiciously and transparently, with clear conditions for resuming normal operations.

* Instances

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/PoolV3.sol#L292

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/CDPVault.sol#L239

https://github.com/code-423n4/2024-07-loopfi/blob/main/src/StakingLPEth.sol#L66

## [L-09] Ensuring Synchronization Before Modifying Fees

The PoolV3 contract has a `setWithdrawFee` function allows the controllerOnly to modify withdrawal fees. However, there's a potential issue where changing fees without performing a synchronization step could lead to inaccurate fee application,

Proof of Concept
The `setWithdrawFee` function modifies fee without ensuring that the calculations depending on these fees are synchronized with the updated rates. This could lead to inconsistencies, especially in time-sensitive operations affected by fee changes, such as calculating withdrawalfees.


## Recommendation
Implement a Synchronization Step: Add a synchronization step before modifying the fees. This ensures that any calculations performed afterward use the most up-to-date fee rates.

```javascript
   /// @notice Sets new withdrawal fee, can only be called by controller
    /// @param newWithdrawFee New withdrawal fee in bps
    function setWithdrawFee(
        uint256 newWithdrawFee
    )
        external
        override
        controllerOnly // U:[LP-2C]
    {
        // Perform synchronization step here
   +     syncFees();
        if (newWithdrawFee > MAX_WITHDRAW_FEE) {
            revert IncorrectParameterException(); // U:[LP-26A]
        }
        if (newWithdrawFee == withdrawFee) return;

        withdrawFee = newWithdrawFee.toUint16(); // U:[LP-26B]
        emit SetWithdrawFee(newWithdrawFee); // U:[LP-26B]
    }


```

```javascript
function syncFees() internal {
    // Implement synchronization logic here if necessary
}
```

## [L-10] Activate the Optimizer

Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

module.exports = {
solidity: {
version: "0.8.24",
settings: {
optimizer: {
enabled: true,
runs: 1000,
},
},
},
};
Please visit this site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:

for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI
Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past.

A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.


## [L- 11] incorrect implementation of totalAssets as per ERC4626 standards

The current implementation of the totalAssets function does not account for fees that should be deducted from the total assets of the pool, as required by ERC-4626. This omission means that the total assets reported by the function may be overstated, as it does not include any fees charged against the assets. Without fee deductions, users might see inflated asset values, leading to potential discrepancies in asset management, reporting, and financial operations. This could affect the accuracy of the pool’s liquidity reporting and potentially result in financial mismanagement or incorrect user balances. 

```javascript
/// @notice Total amount of underlying tokens managed by the pool, same as `expectedLiquidity`
    /// @dev Since `totalAssets` doesn't depend on underlying balance, pool is not vulnerable to the inflation attack
    function totalAssets() public view override(ERC4626, IERC4626) returns (uint256 assets) {
        return expectedLiquidity();
    }
```
```javascript
 /// @notice Amount of underlying that would be in the pool if debt principal, base interest
    ///         and quota revenue were fully repaid
    function expectedLiquidity() public view override returns (uint256) {
        return _expectedLiquidityLU + _calcBaseInterestAccrued() + _calcQuotaRevenueAccrued(); // U:[LP-4]
    }
```
```javascript
 /// @dev Computes base interest accrued since the last update
    function _calcBaseInterestAccrued() internal view returns (uint256) {
        uint256 timestampLU = lastBaseInterestUpdate;
        if (block.timestamp == timestampLU) return 0; // U:[LP-17]
        return _calcBaseInterestAccrued(timestampLU); // U:[LP-17]
    }
```
```javascript
/// @dev Computes quota revenue accrued since the last update
    function _calcQuotaRevenueAccrued() internal view returns (uint256) {
        uint256 timestampLU = lastQuotaRevenueUpdate;
        if (block.timestamp == timestampLU) return 0; // U:[LP-21]
        return _calcQuotaRevenueAccrued(timestampLU); // U:[LP-21]
    }
```

the implementation above is missing the requirement for the totalAsset implementation

As per the docs totalAssets:

```
MUST be inclusive of any fees that are charged against assets in the Vault.
```

## Recommendation

ensure compliance with ERC-4626, incorporate fee deductions into the totalAssets calculation. 

## [L-12]  _convertToShares not rounding down as per ERC-4626 Specification

The `_convertToShares` function, as currently implemented, does not perform the necessary rounding down as required by the ERC-4626 specification. This can lead to significant discrepancies in share allocations. According to ERC-4626, the conversion of assets to shares must always round down towards zero.

```
MUST round down towards 0.
```
The failure to adhere to this requirement can result in:

Inconsistent Share Allocations: Users may receive more shares than they are entitled to, leading to an imbalance in the total shares outstanding.
Regulatory and Compliance Risks: Non-compliance with ERC-4626 could expose the contract to regulatory scrutiny or legal challenges, as it deviates from the standard specifications.
Potential Financial Discrepancies: Inaccurate share calculations may affect the pool’s financial integrity, impacting user trust and the overall stability of the pool.
## Proof of Concept (PoC)
To illustrate how the current implementation fails to round down, consider the following example:

Setup:

Assume the total supply of shares is 1,000,000.
The total assets in the pool are 2,000,000.
A user deposits 1,000 assets.
Expected Behavior:

According to ERC-4626, the conversion should round down the number of shares based on the asset-to-share ratio.
Current Implementation:

```javascript
function _convertToShares(uint256 assets) internal pure returns (uint256 shares) {
    return assets; // Incorrect conversion
}
```
Example Calculation:

Conversion Ratio: (1,000,000 shares) / (2,000,000 assets) = 0.5 shares per asset.
Expected Shares for 1,000 Assets: 1,000 assets * 0.5 shares/asset = 500 shares.
Current Behavior: Returns 1,000 shares directly without conversion.
In this scenario, the function returns 1,000 shares instead of the correctly rounded-down 500 shares, leading to an excess allocation.

* similar issue in `_convertToAssets`

## Recommendation
To address the compliance issue and ensure accurate rounding down, modify the _convertToShares function as follows:

Implement Proper Rounding Down: Update the function to calculate shares based on the conversion ratio and always round down.

## [L-13]  Withdrawal Fees Not Accounted for in previewRedeem Function thus not complying to ERC4626

The `previewRedeem` function, as currently implemented, does not include withdrawal fees in its calculations. According to ERC-4626, functions that estimate the amount of underlying tokens to be received should account for all applicable fees, including withdrawal fees.

```
MUST be inclusive of withdrawal fees. Integrators should be aware of the existence of withdrawal fees.
```

The omission of withdrawal fees can lead to:
Users being shown inflated amounts they will receive upon redemption, leading to potential dissatisfaction or disputes when the actual amount received is less due to fees.

## Proof of Concept (PoC)
To demonstrate the issue, consider the following example:

Setup:

Assume a withdrawal fee of 1% (0.01).
A user wishes to redeem 1,000 shares.
The conversion from shares to assets should account for the withdrawal fee.
Current Implementation:

```javascript
function previewRedeem(uint256 shares) public view override(ERC4626, IERC4626) returns (uint256) {
    return _amountMinusFee(_amountMinusWithdrawalFee(_convertToAssets(shares))); // U:[LP-10]
}
```
Example Calculation:

Convert Shares to Assets: If _convertToAssets(shares) returns 1,000 assets.
make sure to apply the withdrawal fee
Expected Behavior: The final amount should reflect the withdrawal fee deduction.
Potential Issue:

since `_amountMinusWithdrawalFee` does not correctly handle or apply the withdrawal fee, the result will not accurately reflect the amount after fees are deducted.
## Recommendation
To ensure compliance with ERC-4626 and accurately account for withdrawal fees, update the `previewRedeem` function to explicitly include withdrawal fees in its calculations:
