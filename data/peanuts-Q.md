## Summary of Contents

| Index | Title                                                                                        |
| ----- | -------------------------------------------------------------------------------------------- |
| L-01  | Oracle in CDPVault can never change, which will be an issue if the oracle fails.             |
| L-02  | Min-max answer not checked in ChainlinkOracle.sol                                            |
| L-03  | Flashlender.creditFlashLoan() does not really work                                           |
| L-04  | PoolV3.supplyRate() is not calculated properly in RAY                                        |
| L-05  | Reducing max cooldown in stakingLPEth.sol is not fair for users with the original cool down  |
| L-06  | PoolV3.repayCreditAccount calls \_updateBaseInterest.expectedLiquidityDelta with wrong input |
| L-07  | PoolV3 is not supposed to be ERC4626 compatible                                              |
| L-08  | Liquidation and repayment in CDPVault should not be affected by pausing mechanism            |

### [L-01] Oracle in CDPVault can never change, which will be an issue if the oracle fails.

CDPVault adds an oracle in the constructor, but have no way of changing the oracle.

```
    constructor(CDPVaultConstants memory constants, CDPVaultConfig memory config) {
        pool = constants.pool;
        oracle = constants.oracle;
```

The `spotiPrice()` is used for the oracle function:

```
    function spotPrice() public view returns (uint256) {
        return oracle.spot(address(token));
    }
```

CDPVault has no way to swap the oracle or change the oracle. If the oracle goes down, the whole vault will be compromised as the oracle will fail. `spotPrice()` will constantly revert, and all operations that interacts with `spotPrice()`, especially `repay()`, `withdraw()`, and `borrow()` will not work. All collateral will be stuck in the vault.

Consider having an option of changing oracles in case the oracle is down.

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L166

### [L-02] Min-max answer not checked in ChainlinkOracle.sol

Chainlink aggregators have a built in circuit breaker if the price of an asset goes outside of a predetermined price band. The result is that if an asset experiences a huge drop in value (i.e. LUNA crash) the price of the oracle will continue to return the minPrice instead of the actual price of the asset. This would allow user to continue borrowing with the asset but at the wrong price. This is exactly what happened to Venus on BSC when LUNA imploded.

ChainlinkFeedRegistry#latestRoundData pulls the associated aggregator and requests round data from it. ChainlinkAggregators have minPrice and maxPrice circuit breakers built into them. This means that if the price of the asset drops below the minPrice, the protocol will continue to value the token at minPrice instead of it's actual value. This will allow users to take out huge amounts of bad debt and bankrupt the protocol.

Example: TokenA has a minPrice of $1. The price of TokenA drops to $0.10. The aggregator still returns $1 allowing the user to borrow against TokenA as if it is $1 which is 10x it's actual value.

ChainlinkAdapterOracle should check the returned answer against the minPrice/maxPrice and revert if the answer is outside of the bounds:

```
    (, int256 answer, , uint256 updatedAt, ) = registry.latestRoundData(
        token,
        USD
    );
+   if (answer >= maxPrice or answer <= minPrice) revert();
```

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/ChainlinkOracle.sol#L96-L110

### [L-03] Flashlender.creditFlashLoan() does not really work

The `creditFlashLoan()` and `flashloan()` function in Flashlender.sol works exactly the same as intended. This is the supposed difference:

> (flashloan) Flashlender lends `token` to `receiver`
> (creditFlashloan) Flashlender lends ICreditFlashBorrower Credit to `receiver`

The problem is that the receiver will not receive any "credit" because the msg.sender is the Flashlender contract when `pool.lendCreditAccount` is called.

Consider removing the function to prevent any confusion.

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/Flashlender.sol#L117-L137

### [L-04] PoolV3.supplyRate() is not calculated properly in RAY

This is loop's PoolV3 `supplyRate()`

```
LoopFI
    /// @notice Annual interest rate in ray that liquidity providers receive per unit of deposited capital,
    ///         consists of base interest and quota revenue,
    /// @dev This function expects all liquidity providers to be staked.
    function supplyRate() external view override returns (uint256) {
        uint256 assets = expectedLiquidity();
        uint256 baseInterestRate_ = baseInterestRate();
        if (assets == 0) return baseInterestRate_;
        return
>           ((baseInterestRate_ * _totalDebt.borrowed) * (PERCENTAGE_FACTOR - withdrawFee)) /
            PERCENTAGE_FACTOR /
            assets; // U:[LP-15]
    }
```

This is GearBox:

```
  /// @notice Annual interest rate in ray that liquidity providers receive per unit of deposited capital,
    ///         consists of base interest and quota revenue
    function supplyRate() external view override returns (uint256) {
        uint256 assets = expectedLiquidity();
        uint256 baseInterestRate_ = baseInterestRate();
        if (assets == 0) return baseInterestRate_;
>       return (baseInterestRate_ * _totalDebt.borrowed + quotaRevenue() * RAY) * (PERCENTAGE_FACTOR - withdrawFee)
            / PERCENTAGE_FACTOR / assets; // U:[LP-15]
    }
```

Gearbox includes the quotaRevenue but not Loop. Add the quotaRevenue to the supplyRate().

Loop: https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L600-L611

Gearbox: https://github.com/Gearbox-protocol/core-v3/blob/bbe94b9bfcae21708ae32022439a9035fcf813e8/contracts/pool/PoolV3.sol#L535

### [L-05] Reducing max cooldown in stakingLPEth.sol is not fair for users with the original cool down

If cooldown is implemented in StakingLPEth.sol, users will need to wait for the cooldown period to exchange slpETH for lpETH.

```
  function unstake(address receiver) external {
        UserCooldown storage userCooldown = cooldowns[msg.sender];
        uint256 assets = userCooldown.underlyingAmount;

        if (block.timestamp >= userCooldown.cooldownEnd || cooldownDuration == 0) {
            userCooldown.cooldownEnd = 0;
            userCooldown.underlyingAmount = 0;

            silo.withdraw(receiver, assets);
        } else {
            revert InvalidCooldown();
        }
    }
```

If the cool down is set at 30 days, then Alice has to wait 30 days to exchange her slpETH for ETH. If the cooldown period is changed to 7 days, then Alice still has to wait for 30 days, and the rest of the users only need 7 days.

One thing Alice can do is to deposit 1 wei of lpETH, then call `cooldownAssets()` and withdraw that 1 wei of slpETH to reset the cooldown to 7 days instead of 30 days, but that means that Alice needs to know that her cooldown is being unfairly treated in the first place and she has the technical skill to bypass the unfairness.

Otherwise, do not allow for reduction of cooldown. Either the cooldown stays on or off.

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L104

### [L-06] PoolV3.repayCreditAccount calls \_updateBaseInterest.expectedLiquidityDelta with wrong input

In Loop's version of PoolV3, `repayCreditAmount()` calls `_updateBaseInterest()` like this:

```

        _updateBaseInterest({
>           expectedLiquidityDelta: -loss.toInt256(),
            availableLiquidityDelta: 0,
            checkOptimalBorrowing: false
        }); // U:[LP-14B,14C,14D]
```

In GearBox contract, `repayCreditAmount()` calls `_updateBaseInterest()` like this:

```
        _updateBaseInterest({
>           expectedLiquidityDelta: profit.toInt256() - loss.toInt256(),
            availableLiquidityDelta: 0,
            checkOptimalBorrowing: false
        }); // U:[LP-14B,14C,14D]
```

If there is a profit in Loop's version, it is not added in `expectedLiquidityDelta`. Ensure similarity between both contracts as `expectedLiquidityDelta` is calculated incorrectl and `baseInterestRate` will not be accurate..

PoolV3: https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L567
Gearbox: https://github.com/Gearbox-protocol/core-v3/blob/bbe94b9bfcae21708ae32022439a9035fcf813e8/contracts/pool/PoolV3.sol#L503C13-L503C35

### [L-07] PoolV3 is not supposed to be ERC4626 compatible 

```
/// @title Pool V3
/// @notice Pool contract that implements lending and borrowing logic, compatible with ERC-4626 standard
/// @notice Pool shares implement EIP-2612 permits
```

PoolV3 in Loop simply converts ETH to lpETH and back. The ratio should always be 1:1, since `_convertToShares()` returns assets and `_convertToAssets()` returns shares.

```
    /// @dev Internal conversion function (from assets to shares) with support for rounding direction
    /// @dev Pool is not vulnerable to the inflation attack, so the simplified implementation w/o virtual shares is used
    function _convertToShares(uint256 assets) internal pure returns (uint256 shares) {
        // uint256 supply = totalSupply();
        return assets; //(assets == 0 || supply == 0) ? assets : assets.mulDiv(supply, totalAssets(), rounding);
    }

    /// @dev Internal conversion function (from shares to assets) with support for rounding direction
    /// @dev Pool is not vulnerable to the inflation attack, so the simplified implementation w/o virtual shares is used
    function _convertToAssets(uint256 shares) internal pure returns (uint256 assets) {
        //uint256 supply = totalSupply();
        return shares; //(supply == 0) ? shares : shares.mulDiv(totalAssets(), supply, rounding);
    }
```

Users will deposit ETH inside the PoolV3 contract for lpETH, and the ETH inside PoolV3 can be lent out as loans through the CDPVault.

As such, the PoolV3 contract is not ERC4626 compatible as asset and shares work the same way, and there is no need to round up or down.

### [L-08] Liquidation and repayment in CDPVault should not be affected by pausing mechanism

In CDPVault, repayment and liquidation calls `pool.repayCreditAccount()`,

```
            quotaRevenueChange = _calcQuotaRevenueChange(-int(debtData.debt - newDebt));
            pool.repayCreditAccount(debtData.debt - newDebt, profit, 0); // U:[CM-11]
```

which has the `whenNotPaused` modifier.

```
   function repayCreditAccount(
        uint256 repaidAmount,
        uint256 profit,
        uint256 loss
    )
        external
        override
        creditManagerOnly // U:[LP-2C]
>       whenNotPaused // U:[LP-2A]
        nonReentrant // U:[LP-2B]
    {
```

Typically in a lending protocol, repayment and liquidation should not be affected by a pausing mechanism as more debt will incur which is unfair for the position holders.

Remove `whenNotPaused` from any form of credit repayment.

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L529-L538
