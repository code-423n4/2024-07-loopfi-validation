### ChefIncentivesController::endRewardTime(...)
If rewardsPerSecond is configured 0, then all the computation done in the `for loop ` for pools is irrelevant. Hence, gas could be saved if this check was done before the `for loop`, return without entering the loop.

```solidity
 
if (rewardsPerSecond == 0) {
          + endingTime.lastUpdatedTime = block.timestamp;
            endingTime.estimatedTime = type(uint256).max;
            return type(uint256).max;
        }
```

### MultiFeeDistribution::_updateReward(...) can be optimized for 0 reward tokens
If the rewardToken is marked as false in `isRewardToken`, then the logic can avoid calls to update the reward accumulated.

```
  function _updateReward(address account) internal {
        uint256 balance = _balances[account].lockedWithMultiplier;
        uint256 length = rewardTokens.length;
        for (uint256 i = 0; i < length; ) {
            address token = rewardTokens[i];
             
 ==>           if(!isRewardToken[token]){
 ==>             continue;
 ==>           } 
 

            uint256 rpt = rewardPerToken(token);

            Reward storage r = rewardData[token];
            r.rewardPerTokenStored = rpt;
            r.lastUpdateTime = lastTimeRewardApplicable(token);

            if (account != address(this)) {
                rewards[account][token] = _earned(account, token, balance, rpt);
                userRewardPerTokenPaid[account][token] = rpt;
            }
            unchecked {
                i++;
            }
        }
    }
```