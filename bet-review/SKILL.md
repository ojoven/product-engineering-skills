---
name: bet-review
description: Read out a shipped bet against the signal and kill criteria written when it was shaped, and decide to keep, iterate or kill it. Use when the user asks whether a feature, change or experiment worked, or when a live bet has reached its review date.
---

# Bet review

Close the loop on a shipped **bet**: read its signal the same way the baseline was read, hold it against the kill criteria written before the build, and record what was learned. A killed bet with a clear lesson counts as the process working.

Load the `product-context` skill before anything else.

## 1. Pick the bet

Take the bet the user names, or every file in `docs/product/bets/` with status `live` and a review date that has passed. Review one bet at a time.

A change that shipped with no bet file can still be reviewed: reconstruct the hypothesis and the baseline from git history and the pulses on either side of the release, and mark the whole review `assumed` where a criterion was never written down.

## 2. Check it shipped as shaped

Before reading any result, confirm:

- the change is live, since when, and for whom (the switch and its setting)
- what shipped matches the slice in the bet file, and what differs
- the events the signal needs are arriving, with volume
- enough users have been exposed to read the signal by the method the bet chose

If exposure is too low, the verdict is **not readable yet**: set a new review date from the traffic and stop.

## 3. Read the signal

- Run the same query that produced the baseline, on the window since release. Report it with denominator and previous window.
- Read each guardrail the same way.
- Segment once: new against returning, platform, channel. An average can hide a gain in one group and a loss in another.
- List what else changed in the window: other releases, a campaign, a price change, a holiday, an outage. For a before-and-after reading, each of these is a rival explanation.
- Add what users did and said: feedback that mentions the change, five sessions that used it, five that had it available and ignored it.

## 4. Decide

Hold the result against the criteria in the bet file, as written.

| Verdict | When | Then |
| --- | --- | --- |
| `kept` | Keep criteria met, guardrails hold | Remove the switch and the old path |
| `iterating` | Between the kill and keep lines | Name one specific change and a new review date. A bet gets one iteration |
| `killed` | Kill criteria met, or the one iteration did not clear the keep line | Remove the code |

When the user wants to depart from the written criteria, record it as an **override** with their reason. Criteria fixed before the result are what make the result mean something, so a departure is written down, never folded quietly into the verdict.

Present the reading and your recommended verdict. The verdict is the user's.

## 5. Record what was learned

- Fill **Result** and **Learned** in the bet file and set its status.
- Update `PRODUCT.md`: mark ledger assumptions the bet confirmed or refuted with the date and this file, refresh the goal metric's current value, and revise the constraint if the result moved it.
- Append one line under the bet's entry in `DECISIONS.md` with the verdict.
- Name the cleanup as a task: the switch and the losing code path to delete.

Done when the bet file carries a verdict with its numbers, `PRODUCT.md` reflects what was learned, and the cleanup task is stated. If the constraint moved or the bet was killed, suggest `what-next`.
