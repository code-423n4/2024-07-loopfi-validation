## Lack of Withdrawal Limit Check in CDPVault::modifyCollateralAndDebt Function

When a user attempts to withdraw collateral using the `modifyCollateralAndDebt` function, the position data should be retrieved to ensure that position.collateral >= withdrawAmount.

Current implementation:

```js
    if (deltaCollateral < 0) {
            uint256 amount = wmul(abs(deltaCollateral), tokenScale);

            token.safeTransfer(collateralizer, amount);
        }
```

In the scenario where a user has a position with 100 Ether as collateral and 80 Ether as debt, the user can call the modifyCollateralAndDebt function to repay the debt and withdraw any amount of collateral. This withdrawal amount is sent to the user without any check. After the funds are sent, the following line executes:


```js
    position = _modifyPosition(owner, position, newDebt, newCumulativeIndex, deltaCollateral, totalDebt);
```

Then, the collateral amount is updated:

```js
    position.collateral = add(position.collateral, deltaCollateral);
```

This transaction will revert due to an overflow caused by the addition function.

Proposed Solution:

Implement an explicit check to ensure the user cannot withdraw more collateral than they have. 


```js
    else if (deltaCollateral < 0) {
            uint256 amount = wmul(abs(deltaCollateral), tokenScale);
            if (amount > position.collateral) revert CDPVault__tooHighRepayAmount();
            token.safeTransfer(collateralizer, amount);
    }
```

or modify the position before actually sending the funds  to apply the checks-effects-interactions pattern. 
