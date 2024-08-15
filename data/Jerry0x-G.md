
In the `Flashlender` contract, the use of `total - fee` to represent the loan amount (`amount`) in the call to `pool.repayCreditAccount` is redundant, as `total` is itself `amount + fee`. This redundant calculation not only increases gas consumption but also potentially reduces code readability.

```solidity
pool.repayCreditAccount(total - fee, fee, 0);
```

[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/Flashlender.sol#L106)
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/Flashlender.sol#L134)

If a function modifier such as `poolQuotaKeeperOnly` or `creditManagerOnly` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
```solidity
/src/PoolV3.sol
```

```solidity
function lendCreditAccount(
        uint256 borrowedAmount,
        address creditAccount
    )
        external
        override
        creditManagerOnly // U:[LP-2C]
        whenNotPaused // U:[LP-2A]
        nonReentrant // U:[LP-2B]
    {
```
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/PoolV3.sol#L490)

```solidity
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
```
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/PoolV3.sol#L536)

```solidity
    function updateQuotaRevenue(
        int256 quotaRevenueDelta
    )
        external
        override
        nonReentrant // U:[LP-2B]
        //poolQuotaKeeperOnly // U:[LP-2C]
        creditManagerOnly
    {
```
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/PoolV3.sol#L698)

```solidity
function mintProfit(uint256 amount) external creditManagerOnly {
```
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/PoolV3.sol#L899)