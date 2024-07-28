### Explanation of Withdrawal Period Timing Issue
___

When an EOA or contract makes multiple deposits during an ongoing staking period, the unlock duration for all previous deposits is updated to match the latest deposit, disregarding their individual timings. Consequently, users who increase their stake cannot withdraw earlier deposits based on their original staking times but must wait until the latest deposit's unlock period.

Current Implementation of Cooldown Period:
cooldowns[msg.sender].cooldownEnd = uint104(block.timestamp) + cooldownDuration;

---

### Source Code References

1. **Cooldown Period Calculation**:
   [StakingLPEth.sol#L88-L113](https://github.com/code-423n4/2024-07-loopfi/blob/4f508781a49ffa53511e7e5ed6cda0ff0eb5bdc5/src/StakingLPEth.sol#L88-L113)

2. **Full Contract Implementation**:
   [StakingLPEth.sol](https://github.com/code-423n4/2024-07-loopfi/blob/4f508781a49ffa53511e7e5ed6cda0ff0eb5bdc5/src/StakingLPEth.sol)
