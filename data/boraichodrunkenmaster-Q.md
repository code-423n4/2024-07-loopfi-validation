### StakingLPEth Contract : Withdrawal Period Timing Issue
___

When an EOA or contract makes multiple deposits during an ongoing staking period, the unlock duration for all previous deposits is updated to match the latest deposit, disregarding their individual timings. Consequently, users who increase their stake cannot withdraw earlier deposits based on their original staking times but must wait until the latest deposit's unlock period.

Current Implementation of Cooldown Period:
cooldowns[msg.sender].cooldownEnd = uint104(block.timestamp) + cooldownDuration;

---
### Recommendation
---

Introduce an ID variable to track individual user stakes. This approach enables users to have multiple stakes simultaneously, each reflecting a unique deposit and timing. Users can interact with each specific stake entry using its unique ID.

---



