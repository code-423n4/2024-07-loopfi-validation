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



 