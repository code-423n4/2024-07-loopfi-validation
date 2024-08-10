QA1: CDPVault.spotPrice() has no delay component for the returned spot price. Such a delay value is important to ensure that the stored price values are not taken before a certain delay has passed.

[https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L289-L291](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/CDPVault.sol#L289-L291)

Impact: due to the lack of the delay component, the spot price might become stale and the CDPVault functions might use a stale collateral price to evaluate whether a
position is safe or not. As a result, the protocol might miss some liquidation opportunities and loss funds. 
