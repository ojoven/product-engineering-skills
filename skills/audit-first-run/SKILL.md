---
name: audit-first-run
description: Walk the product cold as a new user, from the entry point to the value moment, and log every step and point of friction. Use when the user wants onboarding, signup, activation or the first-run experience audited, or asks why new users drop off.
---

# Audit first run

Go through the product the way a new user does and measure the distance to the **value moment**: how many steps, how many seconds, how many things the product demands before it gives anything. The output is a friction log and the shortest path that could replace it.

Load the `product-context` skill before anything else.

## 1. Set the walk

Read `PRODUCT.md` for the user, the value moment and the main channel. Fix three things before starting:

- **Who**: the target user, with their device and what they know on arrival.
- **Entry**: the place the main channel lands them. A search result, a store listing, a shared link. Rarely the home page typed by hand.
- **End**: the value moment, as an observable outcome.

The walk needs a real build and a tool that can drive it: a browser tool for the web, a simulator or device for an app. Without one, ask the user for a screen recording of a fresh signup. A walk done by reading code is reported as `assumed`.

On production, ask the user before creating an account. Use an identity that is plainly a test, stop before any real payment, and report what you created so it can be filtered out of analytics.

## 2. Walk it cold

**Cold** means a fresh session, no saved state, and no reading of the onboarding code beforehand: knowing what the screen meant to say spoils the read.

From entry to value moment, log every step as you take it:

| Field | Value |
| --- | --- |
| Screen | Where you are, with a screenshot |
| Ask | What the product wants: a click, a field, a choice, a permission, a wait |
| Cost | Seconds spent, fields typed, decisions made |
| Friction | Anything unclear, slow, surprising or broken, in plain words |

Then walk the edges a new user actually hits:

- the phone viewport, when the main channel is mobile
- the first mistake: a wrong input, a weak password, an email already in use
- every empty state on the way
- a slow connection
- leaving halfway and coming back the next day: where the product puts you, and whether it wrote to you in between
- the first email the product sends, and whether its link lands somewhere useful
- whether the promise on the entry page matches the first screen after signup

Done when you reached the value moment, or documented the exact step that stopped you, with every step logged.

## 3. Judge each step

Ask of every step: does this move the user toward the value moment, or does it serve the product? Then sort it:

| Verdict | Meaning |
| --- | --- |
| `keep` | The user needs it now to reach value |
| `defer` | Useful, but it can wait until after the value moment: email verification, profile, invites, upsell |
| `default` | The product can decide or prefill it: a sensible setting, sample data, a template, something inferred |
| `remove` | Nobody would miss it |

From the `keep` steps alone, write the **shortest path**: the minimum sequence that would deliver the value moment.

## 4. Check against the data

With connected sources, pull the funnel for the steps you logged, for the last 28 days. A step where the walk found friction and the data shows a drop is `observed` and `measured`, and leads the findings. A step with a drop and no friction you could see deserves five session replays or two user conversations: say which.

## 5. Write

Write `docs/product/evidence/YYYY-MM-DD-first-run.md`:

```markdown
# First run <YYYY-MM-DD>

Who: <user and device> · Entry: <where> · Build: <production | staging | local>

## Distance to value
Steps: <n> · Time: <duration> · Fields: <n> · Decisions: <n>
Asked for before any value: <account, verification, permissions, payment, tour>

## Friction log
| # | Screen | Ask | Cost | Friction | Verdict |

## Shortest path
<the steps that remain, and the new distance to value>

## Findings
| # | Finding | Step | Drop at this step | Grade | Proposed change | Effort |
```

Rank findings by users lost at the step, then by effort. Done when each finding names a proposed change and the shortest path states its new distance to value.
