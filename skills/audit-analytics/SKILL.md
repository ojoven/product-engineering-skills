---
name: audit-analytics
description: Audit product analytics. Checks whether the tracking can answer the product's questions and whether its numbers can be trusted, then writes ranked findings and a tracking plan. Use when the user wants their analytics, tracking, events or instrumentation audited, or asks for a tracking plan.
---

# Audit analytics

Tracking exists to answer questions that decisions hang on. This audit starts from those **questions**, maps them to what is tracked, and tests whether the numbers are true. A question no event can answer is a **blind spot**. An event no question uses is **noise**.

Load the `product-context` skill before anything else.

The target is the smallest tracking plan that answers every question. A product with a few hundred users is usually served by under ten events.

## 1. Write the questions

Read `PRODUCT.md` and derive the questions the product needs answered, each one specific enough to be a query:

- the goal metric and each of its inputs
- each funnel transition: reach to signup, signup to value moment, value moment to return, return to pay
- the signal of every bet in `docs/product/bets/` that is building or live
- the standing cuts: acquisition channel, platform, plan, signup cohort

Example: "Of the users who signed up in week W, what share reached the value moment within 7 days, by acquisition channel?"

If `PRODUCT.md` is missing, load `product-grill`, or ask the user for the goal and the value moment and continue.

## 2. Inventory

Build two lists.

- **In code**: every tracking call across web client, server, mobile and marketing site, with file and line. Include identify, alias, group and reset calls, page view and autocapture settings, consent gating, and which project or key each environment sends to.
- **Live**: every event the analytics source received in the last 30 days, with volume, pulled through `sources.md`.

Compare them. An event in code that never arrives is broken or dead. An event that arrives from no call site comes from autocapture, an old client or another codebase.

Done when every call site and every live event sits on a list.

## 3. Map

Map each question to the events and properties that answer it. Mark every question `answerable`, `partly` or `blind spot`, and every event `used` or `noise`.

## 4. Test the numbers

Run every check below against the events marked `used`.

| Check | What a pass looks like |
| --- | --- |
| Reconcile | Counts agree with the ground truth within a few percent: signups against the database, payments against the processor. Run this first: a failure here changes how far to trust everything else |
| Value moment | One event marks it, fired when the user outcome happens, from the place that knows it happened (usually the server) |
| Server truth | Signup, payment and cancellation are sent from the server or a webhook, where ad blockers and closed tabs cannot drop them |
| Identity | The anonymous visitor and the signed-up user are one person in the data. Logout resets identity. Client and server send the same user id |
| Duplicates | Each real action produces one event: no double fire from re-renders, retries, or client and server both sending it |
| Pollution | Team, test accounts, bots, and local and staging builds are filtered out or sent elsewhere |
| Attribution | Campaign parameters, referrer and landing page are captured on first visit and still attached at signup |
| Properties | The events carry what the standing cuts need: plan, platform, channel, cohort |
| Naming | One convention across all events and properties. Names are fixed strings, with ids and values carried in properties |
| Privacy | Consent gating does what the privacy policy says. Emails, names and free text stay out of properties. Replays mask inputs |
| Time | The reporting timezone is known. Events queued offline keep the time they happened |

## 5. Watch it live

Reading code shows what should be sent. Run the product through the path from landing to value moment and watch what is sent: the network tab, the SDK's debug mode, or the source's live event view. Confirm the events arrive once, in order, with the expected properties and one identity throughout.

Use a development or staging build. When only production is available, ask the user first and report the test identity you used so it can be filtered out. A check you could not watch live is reported as `assumed`.

## 6. Write

Rank findings by what they do to a decision:

1. `wrong`: a number someone decides on is false
2. `blind`: a question cannot be answered
3. `noise`: an event costs attention and answers nothing
4. `style`: naming and consistency

Write `docs/product/evidence/YYYY-MM-DD-analytics-audit.md`:

```markdown
# Analytics audit <YYYY-MM-DD>

## Verdict
Can the goal metric be measured today? <yes | partly | no>
Can the constraint be located today? <yes | partly | no>

## Findings
| # | Severity | Finding | Question it affects | Where | Evidence | Fix | Effort |

## Questions
| Question | Status | Events used |

## Checks
| Check | Result | Grade |
```

Then write or update `docs/product/tracking-plan.md` as the target state:

```markdown
| Event | Fires when | Sent from | Properties | Answers | Status |
```

Status is one of `live`, `fix`, `add`, `retire`.

Done when every question has a status, every check has a result with its grade, and each finding names its fix. The audit ends at the report: which fixes to make is the user's call, and making them is ordinary engineering work.
