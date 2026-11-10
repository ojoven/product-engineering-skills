# Bet format

One file per bet at `docs/product/bets/NNN-slug.md`. `shape-bet` writes everything down to **Rollout**. `bet-review` fills **Result** and **Learned**.

```markdown
# <NNN> <Name of the bet>

Status: <shaped | building | live | kept | iterating | killed>
Shaped: <YYYY-MM-DD> · Live: <YYYY-MM-DD> · Review: <YYYY-MM-DD>
Constraint: <the funnel stage this acts on>

## Problem
<Who has it, when it happens, what it costs them.> [grade]

## Hypothesis
We believe <change> for <users> will <behaviour that changes>,
moving <metric> from <baseline> to <target> by <date>.

## Appetite
<n> days. Past that, cut scope or stop.

## Smallest slice
In: <what ships>
Cut: <what was left out to fit the appetite>
Tested first without building: <the fake door, manual run or copy change tried, or "no, because...">

## Signal
Primary: <metric, exact definition, query>
Baseline: <value, window, denominator> [grade]
Guardrails: <metrics that must hold, with their current values>
Read by: <experiment | before and after | qualitative>, needing <users or weeks>
Events needed: <each event, and whether it exists in the tracking plan>

## Kill criteria
Kill if <metric> is below <value> at the review date.
Keep if <metric> is at or above <value> and the guardrails hold.
Between the two: one iteration, then decide.

## Rollout
Who sees it: <everyone | segment | percentage>
Switch: <flag or config>
Undo: <how, and how long it takes>

## Riskiest assumption
<The claim this bet falls with, its grade, and the cheapest check done before building.>

## Result
<Filled at review: the signal, same query as the baseline, with window and denominator. Guardrails. What users said and did. What else changed in the window.>

## Learned
<Filled at review: verdict, what this taught about the user or the product, and what it changes in PRODUCT.md.>
```
