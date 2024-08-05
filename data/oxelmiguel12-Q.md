## L1 Lack of Withdrawal Limit Check in modifyCollateralAndDebt Function

When a user attempts to withdraw collateral using the modifyCollateralAndDebt function, the position data should be retrieved to ensure that position.collateral >= withdrawAmount.

Current implementation:

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L440C16-L442C56

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

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L315

```js
    position.collateral = add(position.collateral, deltaCollateral);
```

This transaction will revert due to an overflow caused by the addition function.

* Proposed Solution:

Implement an explicit check to ensure the user cannot withdraw more collateral than they have. 


```js
    else if (deltaCollateral < 0) {
            uint256 amount = wmul(abs(deltaCollateral), tokenScale);
            if (amount > position.collateral) revert CDPVault__tooHighRepayAmount();
            token.safeTransfer(collateralizer, amount);
    }
```

or modify the position before actually sending the funds  to apply the checks-effects-interactions pattern. 




## L2 Lack of Validation for Zero Address and Zero Amount in CDPVault financial Operations

* Impact

This issue highlights the absence of necessary validation checks for zero address and zero amount in the deposit, withdraw, repay and borrow functions. The lack of these checks can lead to unintended behaviors, such as sending funds to an invalid address or processing transactions with no effect.



This line of code to the function :

  https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L223C1-L233C6

 https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L239C5-L249C6

 https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L256C1-L265C6

 https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L272C1-L281C6



* Recommended Mitigation

1. Add Validation Checks for Zero Address:

    Ensure that the to and borrower addresses are not zero addresses by adding require statements to validate that they are valid non-zero addresses.

2. Add Validation Checks for Zero Amount:

    Ensure that the amount parameter is greater than zero by adding require statements to validate that the amount is a positive non-zero value.





