### ChefIncentivesController::endRewardTime(...)
If rewardsPerSecond is configured 0, then all the computation done in the `for loop ` for pools is irrelevant. Hence, gas could be saved if this check was done before the `for loop`, return without entering the loop.

```solidity
 
if (rewardsPerSecond == 0) {
          + endingTime.lastUpdatedTime = block.timestamp;
            endingTime.estimatedTime = type(uint256).max;
            return type(uint256).max;
        }
```