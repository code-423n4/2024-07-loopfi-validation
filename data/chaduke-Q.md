QA1: CDPVault.spotPrice() has no delay component for the returned spot price. Such a delay value is important to ensure that the stored price values are not taken before a certain delay has passed.

[https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L289-L291](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L289-L291)

Impact: due to the lack of the delay component, the spot price might become stale and the CDPVault functions might use a stale collateral price to evaluate whether a
position is safe or not. As a result, the protocol might miss some liquidation opportunities and loss funds. 


QA2. CDPVault.deposit() and CDPVault.withdraw() have the modifier ```whenNotPaused``` to ensure these two functions can only be called when the contract is not paused. However, a user can simply call ```modifyCollateralAndDebt``` to bypass the check enforced by this modifier. As a result, a user can still perform withdraw and deposit when the contract is paused.

[https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L223-L249](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L223-L249) 

Mitigation: the modifier ```whenNotPaused``` should be added to ```modifyCollateralAndDebt()``` instead. 

QA3. CDPVault.liquidatePosition() will mint the penalty from the vault to the treasury:

[https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L569](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L569)

However, it does not convert the the units of assets to shares:

[https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L899-L907](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L899-L907)

Therefore, when asset/share != 1, it will be a problem. 

Mitigation: convert assets to shares when minting shares to the treasury for the pentalty part. 

QA4: CDPVault.liquidatePositionBadDebt() will call pool.repayCreditAccount() at L624 to: 
)
1) deal with Bad debt accounting, in particular, it will try to burn shares from the treasury to cover the loss;

2) The problm is that inside poolrepayCreditAccount(), it will deduct ```debtData.debt``` from ``` _totalDebt.borrowed``` and ```cmdDebt.borrowed``` as if the whole debt is recovered from the liquidator and the treasury. This is not true since the treasury might not have sufficient shares to burn to cover the loss. In this case, ``` _totalDebt.borrowed``` and ```cmdDebt.borrowed``` should be deducted by an amount less than ```debtData.debt``` - by considering ```uncoveredLoss```.

[https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L529-L576](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L529-L576)

Mitigation: 
we need to adjust the amount to deduct from ```_totalDebt.borrowed``` and ```cmdDebt.borrowed``` when the burning from the treasury cannot cover the whole loss. See the code as follows:

```diff
function repayCreditAccount(
        uint256 repaidAmount,
        uint256 profit,
        uint256 loss
    )
        external
        override
        creditManagerOnly // U:[LP-2C]
        whenNotPaused // U:[LP-2A]
        nonReentrant // U:[LP-2B]
    {
+       uint256 uncoveredLoss;

        uint128 repaidAmountU128 = repaidAmount.toUint128();

        DebtParams storage cmDebt = _creditManagerDebt[msg.sender];
        uint128 cmBorrowed = cmDebt.borrowed;
        if (cmBorrowed == 0) {
            revert CallerNotCreditManagerException(); // U:[LP-2C,14A]
        }

        if (profit > 0) {
            _mint(treasury, convertToShares(profit)); // U:[LP-14B]
        } else if (loss > 0) {
            address treasury_ = treasury;
            uint256 sharesInTreasury = balanceOf(treasury_);
            uint256 sharesToBurn = convertToShares(loss);
            if (sharesToBurn > sharesInTreasury) {
+               uncoveredLoss = convertToAssets(sharesToBurn - sharesInTreasury);
                unchecked {
                    emit IncurUncoveredLoss({
                        creditManager: msg.sender,
                        loss: convertToAssets(sharesToBurn - sharesInTreasury)
                    }); // U:[LP-14D]
                }
                sharesToBurn = sharesInTreasury;
            }
            _burn(treasury_, sharesToBurn); // U:[LP-14C,14D]
        }

        _updateBaseInterest({
            expectedLiquidityDelta: -loss.toInt256(),
            availableLiquidityDelta: 0,
            checkOptimalBorrowing: false
        }); // U:[LP-14B,14C,14D]

-       _totalDebt.borrowed -= repaidAmountU128; // U:[LP-14B,14C,14D]
-       cmDebt.borrowed = cmBorrowed - repaidAmountU128; // U:[LP-14B,14C,14D]

+       _totalDebt.borrowed = _totalDebt.borrowed + uncoveredLoss.toUint128()  repaidAmountU128; // U:[LP-14B,14C,14D]
+       cmDebt.borrowed = cmBorrowed + uncoveredLoss.toUint128() - repaidAmountU128; // U:[LP-14B,14C,14D]

        emit Repay(msg.sender, repaidAmount, profit, loss); // U:[LP-14B,14C,14D]
    }

```

QA5. PositionAction.deposit() will first swap input tokens to the vault tokens first and then perform a deposit to the vault.  However, it fails to check whether the output token of the swap is the same as the vault collatral tokens below: 

[https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction.sol#L511-L514](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction.sol#L511-L514)

Mitigation: make sure they are the same token.

