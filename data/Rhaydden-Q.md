# QA for LoopFi

## Table of Contents

| Issue ID | Description |
| -------- | ----------- |
| [QA-01](#qa-01-users-unable-to-immediately-unstake-when-cooldown-duration-is-set-to-0) | Users unable to immediately unstake when cooldown duration is set to `0` |
| [QA-02](#qa-02-abrupt-oracle-status-transition-may-cause-operational-issues) | Abrupt oracle status transition may cause operational issues |
| [QA-03](#qa-03-unfair-cooldown-duration-due-to-time-setting-of-global-cooldown-period) | Unfair cooldown duration due to time-setting of global `cooldown` period |
| [QA-04](#qa-04-flashloan-end-result-isnt-controlled) | Flashloan end result isn't controlled |
| [QA-05](#qa-05-unfair-reward-distribution-in-low-reward-scenarios-due-to-premature-reward-capping) | Unfair reward distribution in low-reward scenarios due to premature reward capping |
| [QA-06](#qa-06-incorrect-reward-updates-for-scheduled-emissions-in-chefincentivescontroller) | Incorrect reward updates for scheduled emissions in `ChefIncentivesController` |
| [QA-07](#qa-07-delayed-price-update-in-balanceroracle-leads-to-safeprice-always-being-one-update-behind-the-actual-current-price) | Delayed price update in `BalancerOracle` leads to `safePrice` always being one update behind the actual current price |
| [QA-08](#qa-08-inflexible-withdrawal-management-in-stakinglpeth-leads-to-potential-liquidity-constraints) | Inflexible withdrawal management in `StakingLPEth` leads to potential liquidity constraints |
| [QA-09](#qa-09-cooldown-extension-flaw-in-stakinglpeth-allows-unintended-prolonging-of-withdrawal-period) | Cooldown extension flaw in `StakingLPEth` allows unintended prolonging of withdrawal period |
| [QA-10](#qa-10-lack-of-lp-token-approval-check-in-_pendleexit-function-may-lead-to-failed-exits) | Lack of LP token approval check in `_pendleExit` function may lead to failed exits |
| [QA-11](#qa-11-empty-_authorizeupgrade-function-allows-unrestricted-contract-upgrades) | Empty `_authorizeUpgrade` function allows unrestricted contract upgrades |
| [QA-12](#qa-12-unverified-assumption-that-the-lplockdata-array-is-sorted-in-descending-order-by-unlocktime-could-lead-to-an-incorrect-eligibility-time-calculation) | Unverified assumption that the `lpLockData` array is sorted in descending order by `unlockTime` could lead to an incorrect eligibility time calculation |
| [QA-13](#qa-13-chainlink-oracle-price-validation-lacks-minmax-bounds-risking-use-of-incorrect-asset-prices) | Chainlink oracle price validation lacks min/max bounds, risking use of incorrect asset prices |
| [QA-14](#qa-14-unfair-distribution-of-aura-rewards-after-inflation-protection-time-removes-claimer-incentive) | Unfair distribution of AURA rewards after inflation protection time removes claimer incentive |
| [QA-15](#qa-15-risk-of-compromising-reward-distribution-by-recovering-rdnt-token-in-recovererc20-function) | Risk of compromising reward distribution by recovering RDNT Token in `recoverERC20` function |
| [QA-16](#qa-16-poolv3setinterestratemodel-lacks-sanity-checks-allowing-potentially-faulty-interest-rate-models) | `PoolV3.setInterestRateModel` lacks sanity checks, allowing potentially faulty interest rate models |
| [QA-17](#qa-17-improper-handling-of-upfront-collateral-in-_onincreaselever-could-cause-loss-of-funds) | Improper handling of upfront Collateral in `_onIncreaseLever` could cause loss of funds |
| [QA-18](#qa-18-potential-overestimation-of-aura-tokens-in-_previewmining-function-due-to-hardcoded-minterminted-value) | Potential overestimation of AURA Tokens in `_previewMining` function due to hardcoded `minterMinted` value |
| [QA-19](#qa-19-precision-loss-in-weighted-geometric-mean-calculation-could-lead-to-inaccurate-oracle-prices) | Precision loss in weighted geometric mean calculation could lead to inaccurate oracle prices |
| [QA-20](#qa-20-inconsistent-handling-of-amount-variable-in-_repay-function) | Inconsistent handling of `amount` Variable in `_repay` function |
| [QA-21](#qa-21-potential-inaccuracy-in-pendlelporacles-spot-function-due-to-missing-pt-oracle-price) | Potential inaccuracy in PendleLPOracle's `spot` function due to missing PT oracle price |
| [QA-22](#qa-22-multiple-functions-vulnerable-to-dos-due-to-unbounded-iteration-over-pools-potentially-exceeding-block-gas-limit) | Multiple functions vulnerable to DoS due to unbounded iteration over pools, potentially exceeding block gas limit |
| [QA-23](#qa-23-inefficient-access-control-implementation-in-setdqtime-function) | Inefficient access control implementation in `setDqTime` Function |
| [QA-24](#qa-24-lack-of-input-validation-for-empty-arrays-in-batchupdateallocpoint-function) | Lack of Input Validation for Empty Arrays in `batchUpdateAllocPoint` Function |

## [QA-01] Users unable to immediately unstake when cooldown duration is set to `0`

### Impact
Users who have initiated a cooldown when the duration was non-zero are unable to immediately unstake their assets if the owner subsequently sets the cooldown duration to zero. This prevents users from accessing their funds promptly when the cooldown mechanism is disabled.

### Proof of Concept
The current implementation of the `unstake` function in the `StakingLPEth` contract does not fully address the scenario where a user has an active cooldown and the owner sets the cooldown duration to zero: 
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L88-L100

```solidity
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

While this function does check if `cooldownDuration == 0`, it still requires the user's individual cooldown to have ended (`block.timestamp >= userCooldown.cooldownEnd`). This means that if a user initiated a cooldown when the duration was non-zero (e.g., 30 days), and the owner subsequently sets the cooldown duration to zero, the user would still need to wait for their original cooldown period to end before unstaking.

This behavior is inconsistent with the expected functionality when cooldown is disabled (i.e., set to `0`), which should allow immediate unstaking for all users.

### Recommended Mitigation Steps
Modify the `unstake` function to prioritize the global `cooldownDuration` setting over individual user cooldowns. This ensures that when cooldown is disabled, all users can unstake immediately regardless of their individual cooldown status.

```diff
function unstake(address receiver) external {
    UserCooldown storage userCooldown = cooldowns[msg.sender];
    uint256 assets = userCooldown.underlyingAmount;

-   if (block.timestamp >= userCooldown.cooldownEnd || cooldownDuration == 0) {
+   if (cooldownDuration == 0 || block.timestamp >= userCooldown.cooldownEnd) {
        userCooldown.cooldownEnd = 0;
        userCooldown.underlyingAmount = 0;

        silo.withdraw(receiver, assets);
    } else {
        revert InvalidCooldown();
    }
}
```

>This change ensures that:
>1. If the cooldown duration is set to zero, users can withdraw immediately regardless of their individual cooldown status.
>2. If the cooldown duration is non-zero, users must wait for their cooldown period to end before withdrawing.




## [QA-02] Abrupt oracle status transition may cause operational issues

### Impact
The way the `_getStatus()` function could lead to issues due to the abrupt transition of the oracle's status from valid to invalid exactly when the `stalePeriod` elapses. 

### Proof of Concept
Look at this part of the code: https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/BalancerOracle.sol#L150-L152

```solidity
function _getStatus() internal view returns (bool status) {
    status = (safePrice != 0) && block.timestamp - lastUpdate < stalePeriod;
}
```

In this function, the status of the oracle is determined by two conditions: `safePrice` must not be zero, and the difference between the current timestamp and `lastUpdate` must be less than `stalePeriod`. The use of the `<` operator means that the status becomes invalid immediately after the `stalePeriod` has passed since the last update. This could lead to situations where the oracle's status changes from valid to invalid without any grace period, potentially causing issues in a protocol that relies on a more gradual transition or expect the status to remain valid until the very end of the `stalePeriod`.

### Recommended Mitigation Steps
Ensure a more intuitive and gradual transition of the oracle's status, I'd recommend adjusting the comparison operator from `<` to `<=`. This change allows the oracle's status to remain valid until the exact moment the `stalePeriod` expires, aligning more closely with typical expectations for handling staleness in pricing oracles.

```diff
function _getStatus() internal view returns (bool status) {
-    status = (safePrice != 0) && block.timestamp - lastUpdate < stalePeriod;
+    status = (safePrice != 0) && block.timestamp - lastUpdate <= stalePeriod;
}
```




## [QA-03] Unfair cooldown duration due to time-setting of global `cooldown` period

### Impact
The cooldown setting allows for unfair scenarios where users who initiated their cooldown earlier might have to wait longer than users who started later, due to changes in the global `cooldownDuration`. This can lead to user funds being locked for longer periods than necessary.

### Proof of Concept
Let's look at the interaction between the mutable `cooldownDuration` and how cooldown periods are set and checked:

The `cooldownDuration` can be changed by the owner [here](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L130-L139): 

```solidity
function setCooldownDuration(uint24 duration) external onlyOwner {
    if (duration > MAX_COOLDOWN_DURATION) {
        revert InvalidCooldown();
    }

    uint24 previousDuration = cooldownDuration;
    cooldownDuration = duration;
    emit CooldownDurationUpdated(previousDuration, cooldownDuration);
}
```

Then when a user initiates a cooldown, the end time is calculated using the current `cooldownDuration`:

```solidity
function cooldownAssets(uint256 assets) external ensureCooldownOn returns (uint256 shares) {
    // ... (other parts of the code)
    cooldowns[msg.sender].cooldownEnd = uint104(block.timestamp) + cooldownDuration;
    // ... (other code)
}
```

The `unstake` function checks against this fixed end time:

```solidity
function unstake(address receiver) external {
    UserCooldown storage userCooldown = cooldowns[msg.sender];
    uint256 assets = userCooldown.underlyingAmount;

    if (block.timestamp >= userCooldown.cooldownEnd || cooldownDuration == 0) {
        // Allow unstaking
    } else {
        revert InvalidCooldown();
    }
}
```

Consider a scenario:
1. Initially, `cooldownDuration` is set to 35 days.
2. Alice calls `cooldownAssets` and starts her cooldown.
3. After 3 days, the owner changes `cooldownDuration` to 5 days using `setCooldownDuration`.
4. Bob calls `cooldownAssets` and starts his cooldown.
5. Alice has to wait 32 more days to unstake, while Bob only needs to wait 5 days.

which shows that changes to the global `cooldownDuration` can create unfair situations for users who initiated their cooldown earlier.

### Recommended Mitigation Steps
We recommend storing the cooldown duration for each user when they initiate their cooldown. This will ensure that changes to the global `cooldownDuration` don't affect ongoing cooldowns.

```diff
 struct UserCooldown {
-    uint104 cooldownEnd;
+    uint104 cooldownStart;
+    uint24 userCooldownDuration;
     uint256 underlyingAmount;
 }

 function cooldownAssets(uint256 assets) external ensureCooldownOn returns (uint256 shares) {
     if (assets > maxWithdraw(msg.sender)) revert ExcessiveWithdrawAmount();

     shares = previewWithdraw(assets);

-    cooldowns[msg.sender].cooldownEnd = uint104(block.timestamp) + cooldownDuration;
+    cooldowns[msg.sender].cooldownStart = uint104(block.timestamp);
+    cooldowns[msg.sender].userCooldownDuration = cooldownDuration;
     cooldowns[msg.sender].underlyingAmount += uint152(assets);

     _withdraw(msg.sender, address(silo), msg.sender, assets, shares);
 }

 function unstake(address receiver) external {
     UserCooldown storage userCooldown = cooldowns[msg.sender];
     uint256 assets = userCooldown.underlyingAmount;

-    if (block.timestamp >= userCooldown.cooldownEnd || cooldownDuration == 0) {
-        userCooldown.cooldownEnd = 0;
+    if (block.timestamp >= userCooldown.cooldownStart + userCooldown.userCooldownDuration || cooldownDuration == 0) {
+        userCooldown.cooldownStart = 0;
+        userCooldown.userCooldownDuration = 0;
         userCooldown.underlyingAmount = 0;

         silo.withdraw(receiver, assets);
     } else {
         revert InvalidCooldown();
     }
 }
```






## [QA-04] Flashloan end result isn't controlled

### Impact
The Flashlender contract could be tricked by some malicious tokens. If a token with non-standard behavior is used (e.g., a token that reports successful transfers without actually moving funds), it could lead to draining of the pool's funds. This could affect any token that the protocol supports, including major stablecoins if they introduce problematic upgrades in the future. 

### Proof of Concept
The `flashLoan` function doesn't verify the actual token balance before and after the flash loan operation:
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/Flashlender.sol#L87-L109

```solidity
function flashLoan(
    IERC3156FlashBorrower receiver,
    address token,
    uint256 amount,
    bytes calldata data
) external override nonReentrant returns (bool) {
    // ... (code omitted for brevity)

    pool.lendCreditAccount(amount, address(receiver));

    // ... (code omitted for brevity)

    underlyingToken.transferFrom(address(receiver), address(pool), total);
    pool.repayCreditAccount(total - fee, fee, 0);

    return true;
}
```

The function relies solely on the `transferFrom` call to ensure the correct amount is returned. However, if a token implements non-standard behavior (e.g., reporting successful transfers without actually moving funds), this check can be bypassed. The contract doesn't verify that the actual token balance of the pool has increased by the expected amount after the flash loan is repaid.

This issue is particularly concerning because Loopfi plans to work with arbitrary ERC20 tokens, which may have varying implementations or could be upgraded in the future to introduce problematic behavior.

### Recommended Mitigation Steps
Consider adding a balance control check to ensure that flash loan invariant remains. Implement balance checks before and after the flash loan operation. Record the pool's token balance before initiating the flash loan and verify that it has increased by at least the fee amount after the loan is repaid. 






## [QA-05] Unfair reward distribution in low-reward scenarios due to premature reward capping

### Impact
The current implementation of the `_newRewards` can lead to unfair reward distribution, particularly when available rewards are low. High-priority pools (those with higher allocation points) may not receive their intended share of rewards, undermining the purpose of the allocation point system. 

### Proof of Concept
Take a look at the `_newRewards` function: https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L982-L998

```solidity
function _newRewards(
    VaultInfo memory pool,
    uint256 _totalAllocPoint
) internal view returns (uint256 newReward, uint256 newAccRewardPerShare) {
    uint256 lpSupply = pool.totalSupply;
    if (lpSupply > 0) {
        uint256 duration = block.timestamp - pool.lastRewardTime;
        uint256 rawReward = duration * rewardsPerSecond;

        uint256 rewards = availableRewards();
        if (rewards < rawReward) {
            rawReward = rewards;
        }
        newReward = (rawReward * pool.allocPoint) / _totalAllocPoint;
        newAccRewardPerShare = (newReward * ACC_REWARD_PRECISION) / lpSupply;
    }
}
```

`rawReward` are calculated based on the duration and `rewardsPerSecond`, then caps it at `availableRewards()` if necessary. Only after this capping does it apply the pool's allocation points to calculate `newReward`.

This leads to cases where;
1. If available rewards are low, all pools have their rewards reduced equally, regardless of their allocation points.
2. The order in which pools are updated can affect the reward distribution unfairly.

take for example, if there are two pools A and B with allocation points 80 and 20 respectively, and the available rewards are only 50% of the calculated raw reward, both pools will have their rewards halved before the allocation points are applied. This results in A getting 40% of the original raw reward and B getting 10%, instead of A getting 50% and B getting 0% as the allocation points would suggest.

### Recommended Mitigation Steps
Consider modifying the reward calculation process to ensure that allocation points are respected even when available rewards are low. Something like a 2-step process:

1. First, calculate the total raw reward across all pools without capping.
2. If the total raw reward exceeds available rewards, scale down all rewards proportionally.
3. Then apply the allocation points to distribute the adjusted rewards.






## [QA-06] Incorrect reward updates for scheduled emissions in `ChefIncentivesController`

### Impact
> Although the protocol team made a [comment](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L635-L637
) stating that:
```
// Although we would want the pools to be as up to date as possible when users
        // transfer rTokens or dTokens, updating all pools on every r-/d-Token interaction would be too gas intensive.
        // _updateEmissions();
```


>The recommendations in this report can help balance the need for accurate reward calculations with the potential gas costs of frequent updates.



The `ChefIncentivesController` contract may distribute incorrect reward amounts when using scheduled emissions. This occurs because the `rewardsPerSecond` value is not updated according to the emission schedule in all relevant functions. Specifically, the `_handleActionAfterForToken` and `afterLockUpdate` functions do not call `_updateEmissions`, leading to potential discrepancies in reward calculations until a new pool is added or a claim is triggered. This can result in users receiving more or fewer rewards than intended.

### Proof of Concept

`ChefIncentivesController` supports two methods of distributing rewards:
- Fixed `rewardsPerSecond` (when `persistRewardsPerSecond` is true)
- Scheduled emissions (when `persistRewardsPerSecond` is false)

The [`setScheduledRewardsPerSecond`](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L352-L368) function is responsible for updating the `rewardsPerSecond` according to the emission schedule. This function is called within `_updateEmissions` as seen [here](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L446).

```solidity
  function setScheduledRewardsPerSecond() internal {
      if (!persistRewardsPerSecond) {
          uint256 length = emissionSchedule.length;
          uint256 i = emissionScheduleIndex;
          uint128 offset = uint128(block.timestamp - startTime);
          for (; i < length && offset >= emissionSchedule[i].startTimeOffset; ) {
              unchecked {
                  i++;
              }
          }
          if (i > emissionScheduleIndex) {
              emissionScheduleIndex = i;
              _massUpdatePools();
>              rewardsPerSecond = uint256(emissionSchedule[i - 1].rewardsPerSecond);
          }
      }
  }
```

However, `_updateEmissions` is only called in two places:

- In the [`addPool` function:](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L304)
```solidity
  function addPool(address _token, uint256 _allocPoint) external {
      if (msg.sender != poolConfigurator) revert NotAllowed();
      if (vaultInfo[_token].lastRewardTime != 0) revert PoolExists();
>     _updateEmissions();
      totalAllocPoint = totalAllocPoint + _allocPoint;
      registeredTokens.push(_token);
      VaultInfo storage pool = vaultInfo[_token];
      pool.allocPoint = _allocPoint;
      pool.lastRewardTime = block.timestamp;
      validRTokens[_token] = true;
  }
```

- In the [`claim` function:](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L524)
```solidity
    function claim(address _user, address[] memory _tokens) public whenNotPaused {
        if (eligibilityMode != EligibilityModes.DISABLED) {
            if (!eligibleDataProvider.isEligibleForRewards(_user)) revert EligibleRequired();
            checkAndProcessEligibility(_user, true, true);
        }
    
 >      _updateEmissions();
    
        uint256 currentTimestamp = block.timestamp;
    
        uint256 pending = userBaseClaimable[_user];
        userBaseClaimable[_user] = 0;
        uint256 _totalAllocPoint = totalAllocPoint;
        uint256 length = _tokens.length;
        for (uint256 i; i < length; ) {
            if (!validRTokens[_tokens[i]]) revert InvalidRToken();
            VaultInfo storage pool = vaultInfo[_tokens[i]];
            if (pool.lastRewardTime == 0) revert UnknownPool();
            _updatePool(pool, _totalAllocPoint);
            UserInfo storage user = userInfo[_tokens[i]][_user];
            uint256 rewardDebt = (user.amount * pool.accRewardPerShare) / ACC_REWARD_PRECISION;
            pending = pending + rewardDebt - user.rewardDebt;
            user.rewardDebt = rewardDebt;
            user.lastClaimTime = currentTimestamp;
            unchecked {
                i++;
            }
        }
    
        _vestTokens(_user, pending);
    
        eligibleDataProvider.updatePrice();
    }
```

The `_handleActionAfterForToken` and `afterLockUpdate` functions, which are called when user balances change, do not call `_updateEmissions`. Instead, they directly call `_updatePool`, which uses the current `rewardsPerSecond` value without checking if it needs to be updated according to the schedule.

- [`_handleActionAfterForToken` function:](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L627-L654)
```solidity
function _handleActionAfterForToken(
    address _token,
    address _user,
    uint256 _balance,
    uint256 _totalSupply
) internal {
    VaultInfo storage pool = vaultInfo[_token];
    if (pool.lastRewardTime == 0) revert UnknownPool();
    _updatePool(pool, totalAllocPoint);
    UserInfo storage user = userInfo[_token][_user];
    uint256 amount = user.amount;
    uint256 accRewardPerShare = pool.accRewardPerShare;
    if (amount != 0) {
        uint256 pending = (amount * accRewardPerShare) / ACC_REWARD_PRECISION - user.rewardDebt;
        if (pending != 0) {
            userBaseClaimable[_user] = userBaseClaimable[_user] + pending;
        }
    }
    pool.totalSupply = pool.totalSupply - user.amount;
    user.amount = _balance;
    user.rewardDebt = (_balance * accRewardPerShare) / ACC_REWARD_PRECISION;
    pool.totalSupply = pool.totalSupply + _balance;

    emit BalanceUpdated(_token, _user, _balance, _totalSupply);
}
```

- `afterLockUpdate` function:
```solidity
function afterLockUpdate(address _user) external {
    if (eligibilityMode != EligibilityModes.DISABLED) {
        if (msg.sender != address(mfd)) revert NotMFD();
        bool isCurrentlyEligible = eligibleDataProvider.refresh(_user);
        if (isCurrentlyEligible) {
            _updateRegisteredBalance(_user);
        } else {
            _processEligibility(_user, isCurrentlyEligible, true);
        }
    }
}
```

This can lead to incorrect reward calculations if the `rewardsPerSecond` should have changed according to the emission schedule, but hasn't been updated because neither `addPool` nor `claim` has been called recently.

### Recommended Mitigation Steps
To ensure that the `rewardsPerSecond` value is always up-to-date with the current emission schedule before calculating and distributing rewards, consider the following:
1. Implement a mechanism to periodically call `_updateEmissions` to keep the `rewardsPerSecond` value in sync with the emission schedule.
2. Identify critical points in the contract where the rewards calculation is most likely to be affected by emission schedule changes and call `_updateEmissions` at those points.
3. Allow users to trigger `_updateEmissions` when they interact with the contract, possibly incentivizing them to do so by offering a small reward.









## [QA-07] Delayed price update in `BalancerOracle` leads to `safePrice` always being one update behind the actual current price

### Impact
The `update()` function sets the `safePrice` to the previous `currentPrice` before calculating the new price. This leads to the `safePrice` always being one update behind the actual current price, potentially causing:

1. Inaccurate price reporting, especially during periods of high volatility.
2. Initial `safePrice` of 0 after deployment, which could break dependent systems.
3. False positives in price validity checks, as `_getStatus()` might return true even when the actual calculated price is 0.

### Proof of Concept
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/BalancerOracle.sol#L114-L136

```solidity
function update() external virtual onlyRole(KEEPER_ROLE) returns (uint256 safePrice_) {
    if (block.timestamp - lastUpdate < updateWaitWindow) revert BalancerOracle__update_InUpdateWaitWindow();
    // update the safe price first
    safePrice = safePrice_ = currentPrice;
    lastUpdate = block.timestamp;

    // ... (price calculation logic)

    currentPrice = wdiv(wmul(totalPi, IWeightedPool(pool).getInvariant()), totalSupply);
}
```

Here, `safePrice` is set to the old `currentPrice` at the beginning of the function. The new price is then calculated and stored in `currentPrice`, but `safePrice` is not updated with this new value. This means that `safePrice` will always reflect the price from the previous update, not the current one.

### Recommended Mitigation Steps
Update the `safePrice` after calculating the new `currentPrice`.  Something like this:

```diff
function update() external virtual onlyRole(KEEPER_ROLE) returns (uint256 safePrice_) {
    if (block.timestamp - lastUpdate < updateWaitWindow) revert BalancerOracle__update_InUpdateWaitWindow();
-   // update the safe price first
-   safePrice = safePrice_ = currentPrice;
+   uint256 oldPrice = currentPrice;
    lastUpdate = block.timestamp;

    // ... (price calculation logic)

    currentPrice = wdiv(wmul(totalPi, IWeightedPool(pool).getInvariant()), totalSupply);
+   safePrice = safePrice_ = currentPrice;

+   // Optional: Add a check for significant price deviations
+   // require(absDifference(oldPrice, currentPrice) <= maxAllowedDeviation, "Price change too large");
}
```






## [QA-08] Inflexible withdrawal management in `StakingLPEth` leads to potential liquidity constraints

### Impact

The StakingLPEth contract's cooldown mechanism significantly restricts users' ability to manage their liquidity effectively. Users can only increase their withdrawal amounts during the cooldown period, with each addition resetting the entire cooldown duration. This design forces users to either commit to longer waiting periods or withdraw their entire staked amount at once, potentially leading to suboptimal financial decisions. In volatile market conditions especially in Defi, this inflexibility could result in missed opportunities or forced hodling, directly impacting users' ability to respond to market changes. Furthermore, the inability to partially withdraw funds or reduce the withdrawal amount during cooldown may lead to over-commitment of assets, reducing overall liquidity in the DeFi ecosystem.

### Proof of Concept

Looking at the `cooldownAssets` and `cooldownShares` functions: https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L104-L126

```solidity
function cooldownAssets(uint256 assets) external ensureCooldownOn returns (uint256 shares) {
    if (assets > maxWithdraw(msg.sender)) revert ExcessiveWithdrawAmount();

    shares = previewWithdraw(assets);

    cooldowns[msg.sender].cooldownEnd = uint104(block.timestamp) + cooldownDuration;
    cooldowns[msg.sender].underlyingAmount += uint152(assets);

    _withdraw(msg.sender, address(silo), msg.sender, assets, shares);
}

function cooldownShares(uint256 shares) external ensureCooldownOn returns (uint256 assets) {
    if (shares > maxRedeem(msg.sender)) revert ExcessiveRedeemAmount();

    assets = previewRedeem(shares);

    cooldowns[msg.sender].cooldownEnd = uint104(block.timestamp) + cooldownDuration;
    cooldowns[msg.sender].underlyingAmount += uint152(assets);

    _withdraw(msg.sender, address(silo), msg.sender, assets, shares);
}
```

In both functions, the cooldown end time is reset to the current timestamp plus the full cooldown duration whenever additional assets or shares are added to the cooldown. This means that users who wish to increase their withdrawal amount must accept a full reset of their cooldown period, potentially extending their wait significantly.

The unstake function further compounds this issue by only allowing full withdrawals:

```solidity
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

This design forces users to withdraw their entire staked amount, preventing partial withdrawals and reducing flexibility in managing their positions.

### Recommended Mitigation Steps

Consider implementing a more flexible cooldown mechanism. Allow users to both increase and decrease their withdrawal amounts during the cooldown period without resetting the entire cooldown duration. Instead, apply a weighted average approach where only the additional amount is subject to a new cooldown period. This could be achieved by tracking separate cooldown periods for each deposit or by using a more sophisticated cooldown calculation.

Modify the unstake function to allow partial withdrawals, giving users more control over their liquidity. This could be implemented by adding a parameter for the amount to withdraw and updating the UserCooldown struct accordingly.






## [QA-09] Cooldown extension flaw in `StakingLPEth` allows unintended prolonging of withdrawal period

### Impact
`StakingLPEth` allows users to unintentionally extend their cooldown periods when initiating multiple cooldowns. This can lead to users being unable to withdraw their assets at the expected time, effectively locking their funds for longer than intended.

So therefeore, users may be unable to withdraw their assets at the expected time, leading to locked funds for longer.

### Proof of Concept

This issue stems from the way the cooldown mechanism is implemented. The contract uses a single cooldown entry per user, which gets overwritten each time a new cooldown is initiated. This can lead to a scenario where a user's earlier cooldown is extended by a later one.

1. The `cooldowns` [mapping structure](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L22-L32):

```solidity
mapping(address => UserCooldown) public cooldowns;

struct UserCooldown {
    uint104 cooldownEnd;
    uint256 underlyingAmount;
}
```

2. The [`cooldownAssets`](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/StakingLPEth.sol#L104-L113) function (similar issue exists in `cooldownShares`):

```solidity
function cooldownAssets(uint256 assets) external ensureCooldownOn returns (uint256 shares) {
    if (assets > maxWithdraw(msg.sender)) revert ExcessiveWithdrawAmount();

    shares = previewWithdraw(assets);

    cooldowns[msg.sender].cooldownEnd = uint104(block.timestamp) + cooldownDuration;
    cooldowns[msg.sender].underlyingAmount += uint152(assets);

    _withdraw(msg.sender, address(silo), msg.sender, assets, shares);
}
```

Scenario:
1. Alice has 100 tokens in the `StakingLPEth` contract.
2. On Day 1, Alice calls `cooldownAssets(60)` to start a cooldown for 60 tokens. The `cooldownEnd` is set to Day 31 (assuming a 30-day cooldown period).
3. On Day 15, Alice decides to cooldown her remaining 40 tokens and calls `cooldownAssets(40)`.
4. This second call overwrites the `cooldownEnd` in Alice's `UserCooldown` struct, setting it to Day 45.
5. On Day 31, when Alice expected to be able to unstake her initial 60 tokens, she calls the `unstake` function:

```solidity
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

6. The `unstake` function reverts with `InvalidCooldown` because the `cooldownEnd` is now set to Day 45, even though Alice's initial 60 tokens should have been available for withdrawal.

### Recommended Mitigation Steps

Instead of using a single cooldown entry per user, the contract should maintain separate cooldown entries for each cooldown action by modifying the `cooldowns` mapping to use a nested mapping or an array to store multiple cooldown entries per user.
Also, by implementing a cooldown ID system to uniquely identify each cooldown action. Then updating the `cooldownAssets` and `cooldownShares` functions to create new cooldown entries instead of overwriting existing ones. Then lastly, modifying the `unstake` function to allow unstaking of individual cooldown entries based on their ID.






## [QA-10] Lack of LP token approval check in `_pendleExit` function may lead to failed exits

### Impact

This issue can cause the `_pendleExit` function to fail unexpectedly, preventing users from exiting their positions in Pendle markets. This could lead to locked funds, as users would be unable to withdraw their LP tokens

### Proof of Concept
Take a look at the `_pendleExit` function:

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PoolAction.sol#L278-L307

```solidity
function _pendleExit(PoolActionParams memory poolActionParams) internal returns (uint256 retAmount){
        (
        address market, uint256 netLpIn, address tokenOut
        ) = abi.decode(poolActionParams.args, (address,uint256, address));
            
        (IStandardizedYield SY, IPPrincipalToken PT, IPYieldToken YT) = IPMarket(market).readTokens();

        if(poolActionParams.recipient != address(this)){
            IPMarket(market).transferFrom(poolActionParams.recipient, market, netLpIn);
        } else {
            IPMarket(market).transfer(market, netLpIn);
        }


        uint256 netSyToRedeem;

        if (PT.isExpired()) {
            (uint256 netSyRemoved, ) = IPMarket(market).burn(address(SY), address(YT), netLpIn);
            uint256 netSyFromPt = YT.redeemPY(address(SY));
            netSyToRedeem = netSyRemoved + netSyFromPt;
        } else {
            (uint256 netSyRemoved, uint256 netPtRemoved) = IPMarket(market).burn(address(SY), market, netLpIn);
            bytes memory empty;
            (uint256 netSySwappedOut, ) = IPMarket(market).swapExactPtForSy(address(SY), netPtRemoved, empty);
            netSyToRedeem = netSyRemoved + netSySwappedOut;
        }

        return SY.redeem(poolActionParams.recipient, netSyToRedeem, tokenOut, poolActionParams.minOut, true);
     }
}
```

The function attempts to transfer LP tokens from `poolActionParams.recipient` to the market contract using `transferFrom`. However, it doesn't check if the current contract has been approved to spend these tokens on behalf of the recipient. If the recipient hasn't granted this approval, the `transferFrom` call will fail, causing the entire exit operation to revert.

Even in the case where the recipient is the contract itself (`else` branch), there's no check to ensure the contract has approved the market to spend its tokens, which could also lead to a failed transfer.

### Recommended Mitigation Steps
Consider implementing a check to ensure the necessary approvals are in place before attempting the transfer. Consider adding a function that allows users to approve the contract for LP token spending before calling the exit function. Alternatively, modify the function to accept LP tokens directly, requiring users to transfer tokens to the contract before calling exit. Additionally, ensure that when the contract is the recipient, it has approved the market to spend its tokens.






## [QA-11] Empty _authorizeUpgrade function allows unrestricted contract upgrades

### Impact
The `_authorizeUpgrade` function in the ChainlinkOracle contract is empty, allowing any address with the MANAGER_ROLE to upgrade the contract without any additional checks or validations. This contradicts the intended behavior described in the Natspec 

### Proof of Concept
Take a look at the `_authorizeUpgrade` function: https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/ChainlinkOracle.sol#L70-L71

```solidity
/// @notice Authorizes an upgrade
/// @param /*implementation*/ The address of the new implementation
/// @dev reverts if the caller is not a manager or if the status check succeeds
function _authorizeUpgrade(address /*implementation*/) internal virtual override onlyRole(MANAGER_ROLE) {}
```

Despite the comment indicating that the function should revert if a status check succeeds, the function body is empty. This means:

1. No additional checks are performed beyond the `onlyRole(MANAGER_ROLE)` modifier.
2. The function never reverts based on any status check.
3. The defined error `ChainlinkOracle__authorizeUpgrade_validStatus()` is never used.

This implementation allows any address with the MANAGER_ROLE to upgrade the contract without restrictions, potentially leading to unauthorized changes in the contract's behavior.

### Recommended Mitigation Steps
Implement the necessary status checks within the `_authorizeUpgrade` function to ensure that upgrades are only authorized under specific conditions. 






## [QA-12] Unverified assumption that the `lpLockData` array is sorted in descending order by `unlockTime` could lead to an incorrect eligibility time calculation 

### Impact
The `lastEligibleTime` function may return an incorrect timestamp if the `lpLockData` array is not sorted in descending order by `unlockTime`. This could lead to users being considered eligible earlier or later than they should be, potentially affecting reward distributions and eligibility status.

### Proof of Concept
The `lastEligibleTime` function in the `EligibilityDataProvider` contract assumes that the `lpLockData` array is sorted in descending order by `unlockTime`, but this assumption is not verified: https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/EligibilityDataProvider.sol#L220-L241

```solidity
function lastEligibleTime(address user) public view returns (uint256 lastEligibleTimestamp) {
    if (!isEligibleForRewards(user)) {
        return 0;
    }

    uint256 requiredValue = requiredUsdValue(user);

    LockedBalance[] memory lpLockData = IMultiFeeDistribution(multiFeeDistribution).lockInfo(user);

    uint256 lockedLP;
    for (uint256 i = lpLockData.length; i > 0; ) {
        LockedBalance memory currentLockData = lpLockData[i - 1];
        lockedLP += currentLockData.amount;

        if (_lockedUsdValue(lockedLP) >= requiredValue) {
            return currentLockData.unlockTime;
        }
        unchecked {
            i--;
        }
    }
}
```

If the `lpLockData` array is not sorted as expected, the function will return the `unlockTime` of the first lock that causes the total locked value to exceed the required value, which may not be the correct earliest eligibility time.

For example, if the locks are in ascending order instead of descending, the function would return the latest unlock time instead of the earliest, incorrectly extending the user's eligibility period.

### Recommended Mitigation Steps
Consider implementing a check to verify that the `lpLockData` array is correctly sorted before processing it. If the array is found to be unsorted, either sort it within the function or revert the transaction to prevent incorrect calculations. 

Additionally, add clear documentation about the expected ordering of the `lpLockData` array, both in this contract and in the `IMultiFeeDistribution` interface, to ensure that any implementations of `lockInfo()` maintain the correct ordering.








## [QA-13] Chainlink oracle price validation lacks min/max bounds, risking use of incorrect asset prices

### Impact
The `ChainlinkOracle` contract does not implement checks for minimum and maximum price boundaries when fetching prices from Chainlink oracles. 

Chainlink aggregators have a built in circuit breaker if the price of an asset goes outside of a predetermined price band. The result is that if an asset experiences a huge drop in value (i.e. LUNA crash) the price of the oracle will continue to return the minPrice instead of the actual price of the asset. This would allow user to continue borrowing with the asset but at the wrong price. This is exactly what happened to [Venus on BSC when LUNA imploded](https://rekt.news/venus-blizz-rekt/). However, the protocol misses to implement such a check.

### Proof of Concept
Look at the `_fetchAndValidate` function: https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/ChainlinkOracle.sol#L96-L110

```solidity
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

This function only checks if the `answer` is greater than 0 and if the price is not stale. It does not verify if the price falls within an acceptable range, making the protocol  vulnerable to using extreme or incorrect prices.

### Recommended Mitigation Steps

Add logic along the lines of:

```solidity
    require(answer >= minPrice && answer <= maxPrice, "invalid price");
```

Alternatively, Min & Max prices can be gathered using [one of these ways](https://medium.com/cyfrin/chainlink-oracle-defi-attacks-93b6cb6541bf#99af:~:text=Developers%20%26%20Auditors%20can%20find%20Chainlink%E2%80%99s%20oracle%20feed).






## [QA-14] Unfair distribution of AURA rewards after inflation protection time removes claimer incentive

### Impact
After the `INFLATION_PROTECTION_TIME`, the `claim` function transfers all AURA rewards to the locker rewards address, leaving no AURA rewards for the claimer. This removes the incentive for users to call the `claim` function leading to unclaimed rewards.

### Proof of Concept
Look at this part of the code: https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L301-L307

```solidity
if (block.timestamp <= INFLATION_PROTECTION_TIME) {
    IERC20(AURA).safeTransfer(_config.lockerRewards, (amounts[1] * _config.lockerIncentive) / INCENTIVE_BASIS);
    IERC20(AURA).safeTransfer(msg.sender, amounts[1]);
} else {
    // after INFLATION_PROTECTION_TIME
    IERC20(AURA).safeTransfer(_config.lockerRewards, IERC20(AURA).balanceOf(address(this)));
}
```

Before `INFLATION_PROTECTION_TIME`, the AURA rewards are distributed between the locker rewards address and the claimer.
 
After `INFLATION_PROTECTION_TIME`, all AURA rewards are transferred to the locker rewards address, leaving nothing for the claimer.

This contradicts the purpose of the `claim` function, which is supposed to incentivize users to claim rewards. Without any AURA rewards, users have no incentive to call this function after the `INFLATION_PROTECTION_TIME`.

### Recommended Mitigation Steps
Ensure that the `claim` function provides a fair distribution of AURA rewards to both the locker rewards address and the claimer, even after the `INFLATION_PROTECTION_TIME`. Adjust the distribution percentages as needed to maintain the incentive structure for users to claim rewards throughout the contract's lifecycle.






## [QA-15] Risk of compromising reward distribution by recovering RDNT Token in `recoverERC20` function

### Proof of Concept

The `ChefIncentivesController` contract contains a function named `recoverERC20` that allows the owner to withdraw an arbitrary amount of any ERC20 token (tokenAddress) from the contract. While this functionality provides flexibility for the owner to recover funds, there is a significant flaw in how it handles potential withdrawals of the contract's reward token (`RDNT`).

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L430-L432

```solidity
function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyOwner {
    _recoverERC20(tokenAddress, tokenAmount);
}
```

When the owner calls `recoverERC20` with `tokenAddress` set to the RDNT token address, it triggers a transfer of RDNT tokens from the contract to the owner. However, since the contract's reward distribution mechanism is based on the RDNT token balance, this withdrawal could lead to inconsistencies in reward calculations, potentially breaking the entire reward system.

The contract relies on its RDNT balance for various critical operations:

1. Distributing rewards to users 
2. Calculating available rewards 
3. Determining the end time for reward distribution

Withdrawing RDNT tokens through `recoverERC20` would bypass these mechanisms, leading to a mismatch between the contract's actual RDNT balance and its internal accounting.

### Recommended Mitigation Steps

Restrict RDNT Token Withdrawals: Modify the recoverERC20 function to prevent the withdrawal of the RDNT token. This can be achieved by implementing an additional check at the beginning of the function. Something like this:

```solidity
function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyOwner {
    require(tokenAddress != rdntToken, "Cannot recover RDNT token");
    _recoverERC20(tokenAddress, tokenAmount);
}
```






## [QA-16] `PoolV3.setInterestRateModel` lacks sanity checks, allowing potentially faulty interest rate models

### Impact
The `setInterestRateModel` function in the `PoolV3` contract does not perform necessary sanity checks on the new interest rate model provided by the governance. As a result,
1. The new interest rate model could be the same as the current one, resulting in unnecessary state changes and gas costs.
2. The new interest rate model could be faulty and revert during interest rate calculations, leaving the pool in an inconsistent state or with an incorrect interest rate. 


### Proof of Concept
The `setInterestRateModel` function in the `PoolV3` contract is defined as follows: https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L746-L757

```solidity
function setInterestRateModel(
    address newInterestRateModel
)
    external
    override
    configuratorOnly // U:[LP-2C]
    nonZeroAddress(newInterestRateModel) // U:[LP-22A]
{
    interestRateModel = newInterestRateModel; // U:[LP-22B]
    _updateBaseInterest(0, 0, false); // U:[LP-22B]
    emit SetInterestRateModel(newInterestRateModel); // U:[LP-22B]
}
```

The function does not check if the `newInterestRateModel` is the same as the current one. This can lead to unnecessary state changes and gas costs.

Also the function does not validate the new interest rate model before setting it. If the new model is faulty and reverts during the `_updateBaseInterest` call, it can leave the pool in an inconsistent state or with an incorrect interest rate.

The `_updateBaseInterest` function calls the new interest rate model to calculate the borrow rate:

```solidity
_baseInterestRate = ILinearInterestRateModelV3(interestRateModel)
    .calcBorrowRate({
        expectedLiquidity: expectedLiquidity_,
        availableLiquidity: availableLiquidity_,
        checkOptimalBorrowing: checkOptimalBorrowing
    })
    .toUint128(); // U:[LP-18]
```

If the new model is faulty and reverts during this call, it can disrupt the pool's operations.

### Recommended Mitigation Steps
The `setInterestRateModel` function should include checks to ensure the new model is different from the current one and validate the new model before setting it.  






## [QA-17] Improper handling of upfront Collateral in `_onIncreaseLever` could cause loss of funds

### Impact
The `_onIncreaseLever` function in the `PositionAction4626` contract incorrectly handles the upfront collateral when it's the same as the ERC4626 token and there's no auxiliary swap. This can result in the upfront collateral not being deposited into the ERC4626 vault potentially leading to a loss of user funds

### Proof of Concept
Take a look at the `_onIncreaseLever` function: https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction4626.sol#L79-L130

```solidity
function _onIncreaseLever(
    LeverParams memory leverParams,
    address upFrontToken,
    uint256 upFrontAmount,
    uint256 swapAmountOut
) internal override returns (uint256) {
    uint256 upFrontCollateral;
    uint256 addCollateralAmount = swapAmountOut;
    if (leverParams.collateralToken == upFrontToken && leverParams.auxSwap.assetIn == address(0)) {
        // if there was no aux swap then treat this amount as the ERC4626 token
        upFrontCollateral = upFrontAmount;
    } else {
        // otherwise treat as the ERC4626 underlying
        addCollateralAmount += upFrontAmount;
    }

    // ... (other parts of the code)

    // deposit into the ERC4626 vault
    IERC20(underlyingToken).forceApprove(leverParams.collateralToken, addCollateralAmount);
    addCollateralAmount =
        IERC4626(leverParams.collateralToken).deposit(addCollateralAmount, address(this)) +
        upFrontCollateral;

    // deposit into the CDP vault
    IERC20(leverParams.collateralToken).forceApprove(leverParams.vault, addCollateralAmount);
    return ICDPVault(leverParams.vault).deposit(address(this), addCollateralAmount);
}
```

When `leverParams.collateralToken == upFrontToken` and `leverParams.auxSwap.assetIn == address(0)`, the `upFrontAmount` is assigned to `upFrontCollateral` instead of being added to `addCollateralAmount`. Because of this, this amount is not included in the deposit to the ERC4626 vault. It's only added to `addCollateralAmount` after the ERC4626 deposit, meaning it's counted towards the CDP vault deposit but not actually deposited into the ERC4626 vault.


### Recommended Mitigation Steps
Modify the `_onIncreaseLever` function to always include the `upFrontAmount` in the `addCollateralAmount`:

```diff
function _onIncreaseLever(
    LeverParams memory leverParams,
    address upFrontToken,
    uint256 upFrontAmount,
    uint256 swapAmountOut
) internal override returns (uint256) {
-   uint256 upFrontCollateral;
    uint256 addCollateralAmount = swapAmountOut;
    if (leverParams.collateralToken == upFrontToken && leverParams.auxSwap.assetIn == address(0)) {
-       // if there was no aux swap then treat this amount as the ERC4626 token
-       upFrontCollateral = upFrontAmount;
+       // if there was no aux swap then treat this amount as the ERC4626 token
+       addCollateralAmount += upFrontAmount;
    } else {
        // otherwise treat as the ERC4626 underlying
        addCollateralAmount += upFrontAmount;
    }

    // ... (other code)

    // deposit into the ERC4626 vault
    IERC20(underlyingToken).forceApprove(leverParams.collateralToken, addCollateralAmount);
-   addCollateralAmount =
-       IERC4626(leverParams.collateralToken).deposit(addCollateralAmount, address(this)) +
-       upFrontCollateral;
+   addCollateralAmount = IERC4626(leverParams.collateralToken).deposit(addCollateralAmount, address(this));

    // deposit into the CDP vault
    IERC20(leverParams.collateralToken).forceApprove(leverParams.vault, addCollateralAmount);
    return ICDPVault(leverParams.vault).deposit(address(this), addCollateralAmount);
}
```





## [QA-18] Potential overestimation of AURA Tokens in `_previewMining` function due to hardcoded `minterMinted` value

### Impact
The hardcoded `minterMinted` value in the `_previewMining` function can lead to an overestimation of the `emissionsMinted` value, resulting in incorrect calculations of the AURA token amount to mint. 

### Proof of Concept
The `_previewMining` function is used to calculate the amount of AURA tokens to mint based on the BAL supply schedule. However, the `minterMinted` variable is hardcoded to `0` because the contract is unable to fetch the actual value due to it being private in the AURA token contract. This leads to an overestimation of `emissionsMinted`:

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/vendor/AuraVault.sol#L340-L363

```solidity
function _previewMining(uint256 _amount) private view returns (uint256 amount) {
    uint256 supply = IERC20(AURA).totalSupply();
    uint256 minterMinted = 0; // Cannot fetch because private in AURA
    uint256 emissionsMinted = supply - INIT_MINT_AMOUNT - minterMinted;

    uint256 cliff = emissionsMinted / REDUCTION_PER_CLIFF;

    if (cliff < TOTAL_CLIFFS) {
        uint256 reduction = ((TOTAL_CLIFFS - cliff) * 5) / 2 + 700;
        amount = (_amount * reduction) / TOTAL_CLIFFS;
        uint256 amtTillMax = EMISSIONS_MAX_SUPPLY - emissionsMinted;
        if (amount > amtTillMax) {
            amount = amtTillMax;
        }
    }
}
```

Additionally, there is a comment in the function that mentions:

```solidity
// Should not be used after INFLATION_PROTECTION_TIME since minterMinted is unknown
```

While the `previewReward` function includes a check to prevent the use of `_previewMining` after `INFLATION_PROTECTION_TIME`, this check could be more helpful and necessary if implemented directly in `_previewMining`:

```solidity
function previewReward() public view returns (uint256 amount) {
    VaultConfig memory config = vaultConfig;
    uint256 balReward = IPool(rewardPool).earned(address(this)) + IERC20(BAL).balanceOf(address(this));
    // No AURA rewards after INFLATION_PROTECTION_TIME
    uint256 auraReward = (block.timestamp > INFLATION_PROTECTION_TIME)
        ? 0
        : _previewMining(balReward) + IERC20(AURA).balanceOf(address(this));
    amount = _previewReward(balReward, auraReward, config);
}
```

### Recommended Mitigation Steps
Implement a mechanism to accurately obtain the `minterMinted` value from the AURA token contract, if possible. Also consider adding a check directly in the `_previewMining` function to prevent its use after `INFLATION_PROTECTION_TIME`.






## [QA-19] Precision loss in weighted geometric mean calculation could lead to inaccurate oracle prices

### Impact

The `update()` function in the BalancerOracle contract suffers from potential precision loss when calculating the weighted geometric mean of token prices. This can result in inaccurate oracle prices, especially for pools with tokens that have significantly different prices or weights. Inaccurate oracle prices can lead to mispriced assets in the protocol.

### Proof of Concept

The current implementation in the `update()` function calculates the weighted geometric mean as follows:
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/BalancerOracle.sol#L123-L133

```solidity
uint256 totalPi = WAD;
for (uint256 i = 0; i < weights.length; i++) {
    prices[i] = _getTokenPrice(i);
    uint256 val = wdiv(prices[i], weights[i]);
    uint256 indivPi = uint256(wpow(int256(val), int256(weights[i])));

    totalPi = wmul(totalPi, indivPi);
}
```

This approach multiplies individual terms sequentially, which can lead to significant precision loss due to repeated multiplication of potentially small numbers. The precision loss becomes more pronounced when dealing with tokens that have very different prices or weights.

For example, consider a pool with three tokens:
1. Token A: price = 1000 USDC, weight = 0.5
2. Token B: price = 0.001 USDC, weight = 0.3
3. Token C: price = 10 USDC, weight = 0.2

The correct calculation would be:
(1000/0.5)^0.5 * (0.001/0.3)^0.3 * (10/0.2)^0.2 ≈ 21.54

However, due to precision loss from repeated multiplication, the current implementation might produce a significantly different result, leading to an inaccurate oracle price.


This is problematic in Balancer's weighted geometric mean price oracle, where the correct formula should be:
```solidity
totalPi = (price1 / weight1) ^ weight1 * (price2 / weight2) ^ weight2 * ... * (priceN / weightN) ^ weightN
```

### Recommended Mitigation Steps

To mitigate this issue, implement the weighted geometric mean calculation using logarithms. This approach avoids precision loss from repeated multiplication of small numbers. Replace the current calculation in the `update()` function with a logarithmic sum followed by exponentiation. Use a fixed-point math library that supports logarithm and exponentiation operations like PRBMath.






## [QA-20] Inconsistent handling of `amount` Variable in `_repay` function

### Impact
The `_repay` function, the `amount` variable is not set when there is no auxiliary swap (`creditParams.auxSwap.assetIn == address(0)`). This can lead to inconsistencies in the approval and debt modification steps, potentially causing incorrect debt repayment amounts.

### Proof of Concept
Take a look at the relevant part of the `_repay` function is shown below: https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/proxy/PositionAction.sol#L569-L591

```solidity
function _repay(address vault, address position, CreditParams calldata creditParams, PermitParams calldata permitParams) internal {
    // transfer arbitrary token and swap to underlying token
    uint256 amount;
    if (creditParams.auxSwap.assetIn != address(0)) {
        if (creditParams.auxSwap.recipient != address(this)) revert PositionAction__repay_InvalidAuxSwap();

        amount = _transferAndSwap(creditParams.creditor, creditParams.auxSwap, permitParams);
    } else {
        if (creditParams.creditor != address(this)) {
            // transfer directly from creditor
            _transferFrom(address(underlyingToken), creditParams.creditor, address(this), creditParams.amount, permitParams);
        }
    }

    underlyingToken.forceApprove(address(vault), creditParams.amount);
    ICDPVault(vault).modifyCollateralAndDebt(
        position,
        address(this),
        address(this),
        0,
        -toInt256(creditParams.amount)
    );
}
```

The issue here is that when there's no auxiliary swap (`creditParams.auxSwap.assetIn == address(0)`), the `amount` variable is not set. However, the function continues to use `creditParams.amount` for the approval and debt modification, which may not reflect the actual amount transferred if `creditParams.creditor == address(this)`.

### Recommended Mitigation Steps
The fix is simple. The `amount` variable should be set in the else clause as well. 

```diff
function _repay(address vault, address position, CreditParams calldata creditParams, PermitParams calldata permitParams) internal {
    // transfer arbitrary token and swap to underlying token
    uint256 amount;
    if (creditParams.auxSwap.assetIn != address(0)) {
        if (creditParams.auxSwap.recipient != address(this)) revert PositionAction__repay_InvalidAuxSwap();

        amount = _transferAndSwap(creditParams.creditor, creditParams.auxSwap, permitParams);
    } else {
+        amount = creditParams.amount;
        if (creditParams.creditor != address(this)) {
            // transfer directly from creditor
            _transferFrom(address(underlyingToken), creditParams.creditor, address(this), amount, permitParams);
        }
    }

-    underlyingToken.forceApprove(address(vault), creditParams.amount);
+    underlyingToken.forceApprove(address(vault), amount);
    ICDPVault(vault).modifyCollateralAndDebt(
        position,
        address(this),
        address(this),
-        0,
-        -toInt256(creditParams.amount)
+        0,
+        -toInt256(amount)
    );
}
```






## [QA-21] Potential inaccuracy in PendleLPOracle's `spot` function due to missing PT oracle price

### Impact
The `spot` function may not accurately represent the true value of the Pendle LP token. This could lead to incorrect pricing information being used in the protocol.

### Proof of Concept
The current implementation of the `spot` function fetches and validates the price from Chainlink and checks the validity of the PT oracle, but it does not use the PT oracle's price in the final calculation. Instead, it only uses the Chainlink price and the LP to asset rate from the market.

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/PendleLPOracle.sol#L103-L112

```solidity
function spot(address /* token */) external view virtual override returns (uint256 price) {
    bool isValid;
    (isValid, price) = _fetchAndValidate();
    if (!isValid) revert PendleLPOracle__spot_invalidValue();
    
    bool isValidPtOracle = _validatePtOracle();
    if (!isValidPtOracle) revert PendleLPOracle__validatePtOracle_invalidValue();
    
    uint256 lpRate = market.getLpToAssetRate(twapWindow);
    price = wmul(price, lpRate);
}
```

The issue is that the function does not incorporate the price of the PT (Principal Token) from the PT oracle. The Chainlink price is likely for the underlying asset, not for the Pendle LP token itself. 

### Recommended Mitigation Steps
To accurately represent the value of the Pendle LP token, the `spot` function should incorporate the PT price from the PT oracle in its calculations. This can be done by fetching the PT price and using it along with the underlying asset price and the LP to asset rate to calculate the LP token price. 






## [QA-22] Multiple functions vulnerable to DoS due to unbounded iteration over pools, potentially exceeding block gas limit

### Impact
Informational.
As the number of pools increases, several critical functions in the ChefIncentivesController contract may become unusable due to excessive gas consumption. This could lead to a DOS, preventing essential operations such as updating po ol allocations, claiming rewards, and managing emissions.

### Proof of Concept
The contract contains multiple functions that iterate over all pools without any upper bound:

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L452-L462

1. `_massUpdatePools()`:
```solidity
function _massUpdatePools() internal {
    uint256 totalAP = totalAllocPoint;
    uint256 length = poolLength();
    for (uint256 i; i < length; ) {
        _updatePool(vaultInfo[registeredTokens[i]], totalAP);
        unchecked {
            i++;
        }
    }
    lastAllPoolUpdate = block.timestamp;
}
```

2. `_updateRegisteredBalance()`:
```solidity
function _updateRegisteredBalance(address _user) internal {
    uint256 length = poolLength();
    for (uint256 i; i < length; ) {
        // ... (code omitted for brevity)
        unchecked {
            i++;
        }
    }
}
```

3. `stopEmissionsFor()`:
```solidity
function stopEmissionsFor(address _user) internal {
    // ... (code omitted for brevity)
    uint256 length = poolLength();
    for (uint256 i; i < length; ) {
        // ... (code omitted for brevity)
        unchecked {
            i++;
        }
    }
    eligibleDataProvider.setDqTime(_user, block.timestamp);
}
```

These functions, among others, iterate over all pools without any mechanism to limit the number of iterations. As the number of pools grows, the gas cost of these operations will increase linearly, eventually exceeding the block gas limit and causing transactions to fail.

### Recommended Mitigation Steps
Restructure the update mechanism to handle a fixed number of updates per transaction or use patterns like the "checkpointing" system to spread state updates over multiple transactions.






## [QA-23] Inefficient access control implementation in `setDqTime` Function

### Impact
Informational

### Proof of concept

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/EligibilityDataProvider.sol#L158-L169

```solidity
/**
     * @notice Sets DQ time of the user
     * @dev Only callable by CIC
     * @param _user's address
     * @param _time for DQ
     */
    function setDqTime(address _user, uint256 _time) external {
        if (msg.sender != address(chef)) revert OnlyCIC();
        disqualifiedTime[_user] = _time;

        emit DqTimeUpdated(_user, _time);
    }
```

In the `EligibilityDataProvider` contract, the function `setDqTime` is designed to be exclusively invoked by the chef contract, as indicated by the error message "OnlyCIC". The current implementation uses a manual conditional check to enforce this access control within the function body. Although effective, this approach is less gas-efficient and can decrease code readability compared to the use of Solidity's built-in modifiers. Utilizing modifiers also aligns with best practices for maintaining clear, concise, and reusable code, which can mitigate risks related to future contract updates or modifications.

Scenario:

If an unauthorized address tries to invoke `setDqTime`, the contract depends on an if-statement check to throw an exception and revert the transaction. This method, while straightforward, consumes more gas due to the additional computational steps and is prone to errors in scenarios where contract logic becomes complex.

### Recommended Mitigation Steps

Implement a dedicated modifier to handle access control for the `setDqTime` function. This would centralize the access control logic, making it easier to manage and potentially reducing gas costs by optimizing the conditional checks. For instance, defining an `onlyChef` modifier would encapsulate the access rule and enhance security. 






## [QA-24] Lack of Input Validation for Empty Arrays in batchUpdateAllocPoint Function


### Impact
Informational

### Proof of Concept

The batchUpdateAllocPoint function in ChefIncentivesController.sol lacks input validation for the _tokens and _allocPoints arrays. These arrays can be empty, yet there is no check to verify the presence of elements before proceeding with the function execution. As a result, the function may proceed with empty arrays, leading to unexpected behavior or unintended consequences due to the absence of data to process.

https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/reward/ChefIncentivesController.sol#L318

```solidity
function batchUpdateAllocPoint(address[] calldata _tokens, uint256[] calldata _allocPoints) external onlyOwner
```

### Recommended Mitigation Step

To address this issue, it is recommended to implement input validation checks to ensure that both the _tokens and _allocPoints arrays contain valid data. 
