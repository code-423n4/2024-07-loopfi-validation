
In the `Flashlender` contract, the use of `total - fee` to represent the loan amount (`amount`) in the call to `pool.repayCreditAccount` is redundant, as `total` is itself `amount + fee`. This redundant calculation not only increases gas consumption but also potentially reduces code readability.

```solidity
src/Flashlender.sol

pool.repayCreditAccount(total - fee, fee, 0);
```

[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/Flashlender.sol#L106)
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/Flashlender.sol#L134)

If a function modifier such as `poolQuotaKeeperOnly` or `creditManagerOnly` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

```solidity
/src/PoolV3.sol

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
/src/PoolV3.sol

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
/src/PoolV3.sol

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
/src/PoolV3.sol

function mintProfit(uint256 amount) external creditManagerOnly {
```
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/PoolV3.sol#L899)

```solidity
src/Silo.sol

    function withdraw(address to, uint256 amount) external onlyStakingVault {
        lpETH.safeTransfer(to, amount);
    }
```
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/Silo.sol#L28)

```solidity
src/reward/ChefIncentivesController.sol

function manualStopEmissionsFor(address _user, address[] memory _tokens) public isWhitelisted {
```
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/reward/ChefIncentivesController.sol#L813)

```solidity
src/reward/ChefIncentivesController.sol

function manualStopAllEmissionsFor(address _user) external isWhitelisted {
        manualStopEmissionsFor(_user, registeredTokens);
    }
```
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/reward/ChefIncentivesController.sol#L844)

```solidity
src/quotas/GaugeV3.sol

    function vote(
        address user,
        uint96 votes,
        bytes calldata extraData
    )
        external
        override
        onlyVoter // U:[GA-02]
    {
        (address token, bool lpSide) = abi.decode(extraData, (address, bool)); // U:[GA-10,11,12]
        _vote({user: user, token: token, votes: votes, lpSide: lpSide}); // U:[GA-10,11,12]
    }
```
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/quotas/GaugeV3.sol#L133)

```solidity
src/quotas/GaugeV3.sol

    function unvote(
        address user,
        uint96 votes,
        bytes calldata extraData
    )
        external
        override
        onlyVoter // U:[GA-02]
    {
        (address token, bool lpSide) = abi.decode(extraData, (address, bool)); // U:[GA-10,11,13]
        _unvote({user: user, token: token, votes: votes, lpSide: lpSide}); // U:[GA-10,11,13]
    }
```
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/quotas/GaugeV3.sol#L176)

```solidity
src/quotas/PoolQuotaKeeperV3.sol

    function addQuotaToken(
        address token
    )
        external
        override
        gaugeOnly // U:[PQK-3]
    {
```
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/quotas/PoolQuotaKeeperV3.sol#L166)

```solidity
src/quotas/PoolQuotaKeeperV3.sol

    function updateRates()
        external
        override
        gaugeOnly // U:[PQK-3]
    {
```

[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/quotas/PoolQuotaKeeperV3.sol#L186)

```solidity
src/proxy/PositionAction.sol

    modifier onlyDelegatecall() {
        if (address(this) == self) revert PositionAction__onlyDelegatecall();
        _;
    }

    modifier onlyRegisteredVault(address vault) {
        if (!vaultRegistry.isVaultRegistered(vault)) revert PositionAction__unregisteredVault();
        _;
    }
```
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PositionAction.sol#L194)
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PositionAction.sol#L206)
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PositionAction.sol#L214)
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PositionAction.sol#L228)
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PositionAction.sol#L243)
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PositionAction.sol#L260)
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PositionAction.sol#L273)
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PositionAction.sol#L302)
[Link to code](https://github.com/code-423n4/2024-07-loopfi/blob/main/src/proxy/PositionAction.sol#L349)