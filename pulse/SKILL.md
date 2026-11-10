---
name: pulse
description: Pull a dated snapshot of the product's funnel numbers from its connected sources and say where the funnel is weakest. Use when the user asks how the product is doing, wants its metrics or a weekly check, or when another product skill needs fresh numbers.
---

# Pulse

Take the product's vital signs for one window and write them down: one or two numbers per funnel stage, what moved, and where the funnel looks weakest. A pulse reports. Deciding what to do about it belongs to `what-next`.

Load the `product-context` skill before anything else. Its number rules apply to every figure here.

## 1. Set up

- Read `PRODUCT.md` for the goal metric, the value moment and the stage, and `sources.md` for what can be queried. If `sources.md` is missing, load `connect-sources` first.
- Read the latest pulse in `docs/product/evidence/` and reuse its queries, so this pulse is comparable with the last one. Change a definition only for a stated reason, and then report the number both ways once.
- Window: the last 28 days against the 28 before, unless the user names another.

## 2. Pull

For each row, pull the number, its denominator and the previous window. A row no source can answer is a **blind spot**: write it down as one and leave the cell empty.

| Stage | Pull |
| --- | --- |
| Goal | The goal metric and its inputs, exactly as `PRODUCT.md` defines them |
| Reach | Visitors or store page views, split by channel |
| Signup | New accounts, and the rate from reach |
| Value moment | Of each signup cohort, the share that reached the value moment within the typical time, and the median time taken |
| Return | Of each cohort, the share active again in week 1 and week 4, or at the product's natural frequency |
| Pay | New paying users, the rate from activated users, recurring revenue, cancellations with reasons |
| Refer | Invites sent and accepted, share of signups arriving by referral |
| Health | Errors and slow responses on the path to the value moment, as users affected |

Then:

- **Segment the biggest mover once.** Split the number that changed most by channel, platform, plan and new against returning, and report the cut that explains the change.
- **Reconcile once.** Where two sources count the same thing, compare them. A gap above a few percent is a data defect: list it for `audit-analytics`.

## 3. Read the rows

When the product has fewer than 100 weekly active users, add what the totals hide: take the last 20 signups and write one line each on what that user did, from the database or event stream, labelled by segment. Three users stalling at the same step is a finding that a 28-day rate with a denominator of 31 would bury.

## 4. Write

Write `docs/product/evidence/YYYY-MM-DD-pulse.md`:

```markdown
# Pulse <YYYY-MM-DD>

Window: <dates> against <previous dates> · Stage: <stage>

## Headline
- What moved: <the largest real change, with numbers>
- Weakest stage: <the stage, and the numbers that say so>
- Unknown: <the blind spot that matters most>

## Funnel
| Stage | Metric | Now | Before | Denominator | Source |

## Segments
<the one cut that explains the biggest mover>

## Rows
<only under 100 weekly active users: the per-user lines>

## Blind spots
## Data defects
## Queries
<each query or call, labelled by the metric it produced>
```

Done when every stage has numbers with source, window and denominator or is listed as a blind spot, the headline is filled from those numbers, and every query is saved.
