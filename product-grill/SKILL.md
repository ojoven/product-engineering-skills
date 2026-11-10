---
name: product-grill
description: Grill the user about their whole product (user, job, value moment, model, goal, constraint) and write the product brief. Use when the user wants their product grilled or its context and goals captured, or when a product skill finds docs/product/PRODUCT.md missing.
---

# Product grill

Interview the user until the product brief holds up: who the product is for, what they get from it, how it earns, what the goal is, and what stands in the way. The output is `PRODUCT.md`, the file every other product skill reads first.

Load the `product-context` skill before anything else. It defines the workspace, the evidence grades and the interview rounds used here.

## 1. Gather the facts

Before the first question, read everything that already answers part of the brief:

- an existing `PRODUCT.md` and the rest of `docs/product/`
- the README, landing page, pricing page and store listing
- the signup and onboarding flow, the main routes, the data model
- the tracking calls, and if `sources.md` lists working sources, the basics for the last 28 days: signups, active users, paying users
- the last few months of git history, to see where the effort went
- idea and todo files, open issues

Draft every section of the brief from what you found, each claim tagged with its grade. This step is done when every section has a draft or is marked empty.

If `PRODUCT.md` already exists, this run is a **refresh**: keep what still holds and grill only the sections the user names, the sections newer evidence contradicts, and the ledger entries whose status changed.

## 2. Grill

Work the brief in rounds, in this order, because each group depends on the one before:

1. user, job
2. value moment, return
3. model, distribution
4. stage, goal
5. constraint
6. limits, non-goals, taste

Show your draft as the recommended answer to each question. Push once, with a sharper question, on any answer that shows one of these tells:

| Tell | Push with |
| --- | --- |
| The user is a category ("developers", "language learners") | "Describe one real person who used it last week. What were they doing right before they opened it?" |
| The job is a feature of the product | "What were they trying to get done, and what did they use before this existed?" |
| The alternative is "nothing like it exists" | "What do they do today instead? Doing nothing and a spreadsheet both count." |
| The value moment is something the product shows | "At which moment would they be annoyed if you took the product away?" |
| The goal has no number or no date | "Which number, by when, would make you say this worked?" |
| The metric can only go up (total signups, downloads) | "Which number could fall next week and tell you something broke?" |
| There are several goals | "If only one could move in the next eight weeks, which one?" |
| "Users want..." with no source | "Who said it, how many of them, and what did they do next?" |
| The constraint is whatever is most fun to build | "Where in the funnel do you lose the most people, and how do you know?" |

When the pushed answer is still a belief, record it in the ledger as `assumed` and continue. An honest gap in the brief is worth more than a confident guess.

The grill is done when every section holds either graded evidence or a ledger entry, and the frontier is empty.

## 3. Write the brief

Write `docs/product/PRODUCT.md` in this shape. Keep it to one screen per section at most: the brief is read at the start of every product task.

```markdown
# <Product>

Last reviewed: <YYYY-MM-DD> · Stage: <pre-launch | pre-pmf | growth | mature>

## User
<One specific person in one specific situation. What they use today instead.> [grade]

## Job
<The progress they are trying to make, in their words. How often it comes up and what it costs them when it goes badly.> [grade]

## Value moment
<The first moment a user gets what they came for.>
Marked by: <the event or observable action>. Typical time from signup: <duration>. [grade]

## Return
<Natural frequency (daily, weekly, monthly, episodic) and what brings them back.> [grade]

## Model
<Who pays, for what, how much, when. Rough cost to serve and to acquire a user.> [grade]

## Distribution
<How users find the product today, by share. The one channel being worked on.> [grade]

## Goal
<One measurable outcome with a date.>
Metric: <name and exact definition>. Now: <value> [grade]. Target: <value> by <date>.
Inputs: <the two or three numbers that drive the metric>.

## Constraint
<The funnel stage that most limits the goal right now, and why you think so.> [grade]

## Limits
Time: <hours per week>. Team: <who>. Money: <budget, runway>. Deadlines: <dates>.

## Non-goals
<What this product will not do, and who it will not serve, for now.>

## Taste
<Three to five principles the product holds to: how it should feel, what it refuses to do.>

## Ledger
| # | Assumption | What rests on it | Cheapest check | Status |
| --- | --- | --- | --- | --- |
| 1 | <riskiest first: the most rests on it, the least evidence behind it> | | | open |
```

Ask the user to read the brief and confirm it reads true. Then close with the three riskiest ledger entries and the cheapest check for each, and name the next step: `connect-sources` when `sources.md` is missing, otherwise `what-next`.
