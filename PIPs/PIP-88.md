---
PIP: 88
Title: Recalibrate CHECKPOINT_REWARD for reduced Polygon block times
Author: Simon Dosch (@simonDos)
Description: Scale down `CHECKPOINT_REWARD` in lockstep with the planned ~2s → 1.5s → 1s block-time reductions to hold annual POL emission constant.
Discussion: TBA
Status: Draft
Type: Core
Date: 2026-04-29
---

  ## Abstract

  Polygon PoS is reducing its average block time in two steps, from approximately 2.1s to 1.5s, and subsequently to 1.0s. Because checkpoints are submitted every `checkPointBlockInterval = 5120` blocks, a shorter
  block time directly increases the number of checkpoints submitted per year. The per-checkpoint emission parameter `CHECKPOINT_REWARD`, set on the L1 `StakeManager` (`0x5e3Ef299fDDf15eAa0432E6e66473ace8c13D908`)
  and updated through `updateCheckpointReward(uint256)`, must therefore be reduced proportionally.  
  This PIP proposes two new values, each to be applied at the moment its corresponding
  block-time change goes live:  
  **24,782.84 POL** when block time becomes 1.5s, and **16,521.89 POL** when block time becomes 1.0s.

  ## Motivation

  `CHECKPOINT_REWARD` defines the amount of POL distributed to validators per checkpoint. Annual emission is therefore a function of two variables: the per-checkpoint reward and the number of checkpoints produced
  per year. The number of checkpoints per year is itself a function of average block time:

  ```
  checkpoints_per_year = seconds_per_year / (block_time × checkPointBlockInterval)
  ```

  If block time is reduced without adjusting `CHECKPOINT_REWARD`, the validator reward budget increases mechanically. At a 1.0s block time, leaving the
  parameter unchanged would increase emission by roughly 2× over the current target.

  The intent of this PIP is therefore narrow and mechanical: hold annualized emission constant in POL terms while the block-time change ships.

  ## Specification

  `StakeManager.updateCheckpointReward(uint256 newReward)` (`onlyGovernance`) is invoked through the `Governance` contract twice, each call timed to coincide with the corresponding block-time change on Polygon PoS:

  1. **At block-time transition to 1.5s:**
     - `newReward` = `24782840000000000000000` (24,782.84 POL, 18 decimals)

  2. **At block-time transition to 1.0s:**
     - `newReward` = `16521890000000000000000` (16,521.89 POL, 18 decimals)

  Both transactions target the L1 `StakeManager` proxy at `0x5e3Ef299fDDf15eAa0432E6e66473ace8c13D908`. Each successful execution emits `RewardUpdate(newReward, oldReward)` on the `StakingInfo` logger at
  `0xa59C847Bd5aC0172Ff4FE912C5d29E5A71A7512B`.

  No contract upgrades are required. No other parameters (`checkPointBlockInterval`, `proposerBonus`, `dynasty`, etc.) are changed by this PIP.

  ## Rationale

  The current value of `CHECKPOINT_REWARD` is **34,695.98 POL** (set 2026-02-19, see `RewardUpdate` history). At the current ~2.1s average block time:

  ```
  seconds_per_year      = 365 × 24 × 3600 = 31,536,000
  checkpoints_per_year  = 31,536,000 / (2.1 × 5,120) ≈ 2,933.04
  annual_emission       = 34,695.98 × 2,933.04        ≈ 101,763,260 POL
  emission_rate         = 101,763,260 / 10,264,044,460 ≈ 0.991% of POL supply
  ```

  To hold `annual_emission` constant across a block-time change we can use:

  ```
  new_reward = old_reward × (new_block_time / old_block_time)
  ```

  Applying this to the planned transitions (anchored on the current 2.1s baseline):

  | Phase | Block time | Checkpoints / yr | `CHECKPOINT_REWARD` (POL) 
  |-------|-----------:|-----------------:|--------------------------:
  | Today | 2.1s       | 2,933.04         | 34,695.98                 
  | Step 1 | **1.5s**  | 4,106.25         | **24,782.84**             
  | Step 2 | **1.0s**  | 6,159.375        | **16,521.89**             


  ## Backwards Compatibility

  This PIP introduces no backwards-incompatible behavior.
  
  ## Test Cases

  `updateCheckpointReward` and the surrounding reward-distribution path are covered by the existing test suite in `pos-contracts`:

  - `test/units/staking/stakeManager/StakeManager.test.js` exercises governance-gated updates and the resulting reward-calculation behavior.
  - `test/units/root/RootChain.test.js` exercises checkpoint submission and reward emission end-to-end.

  ## Security Considerations

  - **Authorization.** `updateCheckpointReward` is `onlyGovernance`. Execution requires the governance multisig and follows the existing parameter-change process; no new privileged surface is introduced.
  - **Misconfiguration risk.** Setting `CHECKPOINT_REWARD` to an incorrect value (wrong magnitude, wrong decimals) would either over-emit POL or under-pay validators. Mitigations: (a) the values in this PIP are
  stated in both human-readable POL and 18-decimal wei form, (b) the deployer script asserts the post-state matches the intended value, (c) the change is reversible by a subsequent governance call.
  - **Timing / sequencing.** The parameter change should be applied at — or as close as operationally feasible to — the block at which Polygon's average block time actually crosses to the new target. A large lag in
   either direction causes a transient deviation from the target emission rate (over-emission if the parameter lags a faster block time; under-emission if the parameter changes before the block-time reduction takes
   effect).
  - **Validator economics.** The proposed values hold annual POL emission constant in absolute POL terms. Per-checkpoint reward decreases, but validators submit proportionally more checkpoints per unit time, so
  per-unit-time validator rewards are unchanged in expectation.

  ## Copyright

  Copyright and related rights waived via [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
