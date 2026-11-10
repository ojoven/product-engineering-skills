---
name: what-next
description: Decide what to build next. Names the constraint on the product's goal, gathers candidates from evidence and backlog, and recommends one bet with its reasoning. Use when the user asks what to work on, build, fix or prioritise next.
---

# What next

Recommend the one thing to do next and show the reasoning: the goal, the **constraint** on it, the candidates, and the arithmetic that puts one **bet** ahead of the rest. The answer is one bet, with the next two in order behind it.

Load the `product-context` skill before anything else.

## 1. Check the footing

Read `PRODUCT.md`, the newest file of each kind in `docs/product/evidence/`, every file in `docs/product/bets/`, `DECISIONS.md`, the backlog (issue tracker, todo and idea files) and the git log since the last decision.

Then settle these in order:

| Situation | Do |
| --- | --- |
| `PRODUCT.md` is missing or has no goal | Load `product-grill`. Nothing can be ranked without a goal |
| A live bet is past its review date | Load `bet-review` for it first. Learning from the last bet outranks starting the next |
| A bet is already building, with nothing blocking it | Say so. With a small team the recommendation is to finish it, unless the user wants a second one |
| No pulse, or a stale one, and `sources.md` lists working sources | Load `pulse`, then continue |
| Feedback sources exist and the digest is stale or missing | Load `feedback-digest`, then continue |
| No sources at all | Continue, grade every claim `assumed`, and include connecting a source among the candidates |

## 2. Name the constraint

Find the funnel stage where an improvement would move the goal most. Start from the goal metric and walk back through the funnel using the pulse.

- A stage is weak against its own history, against the stages around it, or against what the user considers normal for this kind of product.
- Fix downstream before upstream: reach poured into a product users leave is wasted. Return comes before reach, and the value moment comes before return.
- The exception is a stage with too few users to learn from. Then reach is the constraint, and the aim is enough of the right users to learn, not growth.
- `pre-launch`: the constraint is always getting the product in front of real users.
- When blind spots keep you from locating the constraint, measurement is the constraint, and the next bet is the smallest instrumentation that reveals it.

State the constraint in one sentence with its numbers and grade. If it differs from the one in `PRODUCT.md`, say so and ask the user which holds.

## 3. Gather candidates

Collect candidates from every place they live: feedback problems, friction and audit findings, the backlog, the ledger, and the user's own ideas (ask for them in one question).

Feature ideas crowd out everything else, so make sure the list holds at least one candidate of each kind before judging any:

| Kind | Example |
| --- | --- |
| Build | A new capability |
| Fix | Repair or polish on the path to the value moment |
| Remove | Cut a step, a feature or an option |
| Test | Check a ledger assumption without building: five user conversations, a fake door, a price on a page |
| No code | Write to cancelled users, change the price, rewrite the landing headline, publish in the one channel |

## 4. Filter

Set aside every candidate that does not act on the constraint, each with a one-line reason. They go in the decision record as "not now".

One class skips the filter and the ranking: **trust breakers**. Data loss, a security hole, broken payments, a crash on the core path. If one is open, it is next.

## 5. Size what is left

For each remaining candidate, write:

- **Mechanism**: who does what differently because of it, and why that moves the constraint metric.
- **Arithmetic**: the expected effect from real numbers. "About 140 users a week reach this step and 62% leave. Winning back a quarter of those is about 20 more activations a week." Show a range, and say which input is the guess.
- **Grade**: the strongest evidence behind the mechanism.
- **Effort**: the smallest version that tests the mechanism, in days.
- **Time to signal**: how long after shipping until the result can be read, given the traffic at that step.

Rank by argument, from the written arithmetic. A candidate rises with stronger evidence, a smaller smallest version, a faster signal, and by being cheap to undo. Among close candidates, prefer the one that teaches something even if it fails.

## 6. Recommend

Present:

1. **The bet**: what it is, its mechanism, arithmetic, grade, smallest version and signal.
2. **Why it beats the runner-up**, in two sentences.
3. **What would change the pick**: the fact that, if false, puts another candidate first.
4. **Next two**, in order.
5. **Not now**, with reasons.

The decision is the user's. Where there is a real fork, ask it as a round with your recommended answer.

## 7. Record

Append to `docs/product/DECISIONS.md`:

```markdown
## <YYYY-MM-DD> · <the bet chosen>

Goal: <metric, now, target> · Constraint: <stage, numbers> [grade]
Chosen because: <mechanism and arithmetic in two or three lines>
Passed over: <runner-up and why> · Not now: <list with reasons>
Evidence used: <file names>
```

Done when the user has accepted or redirected the recommendation and the decision is recorded. Then load `shape-bet` for the chosen bet. A trust breaker goes straight to the fix.
