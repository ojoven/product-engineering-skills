# How and when to use the skills

The [README](README.md) says what each skill is. This guide says when to reach for which one, how to call it, what to have ready, and what comes out.

None of the skills has been run on a real product yet. Treat the rhythm described here as a starting point and change it once you have used them.

## Before you start

- **Install** the skills as described in the [README](README.md#install).
- **Work inside the product's repo.** The skills read the code and write their notes to `docs/product/` there. To keep the notes somewhere else, say where in the repo's `AGENTS.md` or `CLAUDE.md`.
- **Commit `docs/product/`.** It is the product's memory between sessions. Add `docs/product/evidence/imports/` to `.gitignore`, since raw exports can hold personal data.
- **Give the agent read-only access.** A read-only API key or database role per source is enough. `connect-sources` tells you which ones to create and where to put them.

## Calling a skill

In Claude Code there are two ways:

- **Type its name**: `/what-next`. This always works. Installed as a plugin, the name carries the plugin's prefix: `/product-engineering:what-next`.
- **Ask in plain words**: "what should I build next?". The agent matches the request to the skill's description. If it answers without loading the skill, type the name.

Text after the name narrows the run:

```
/pulse last 7 days against the 7 before
/product-grill only the goal and the constraint
/shape-bet a reminder email for users who stop after day 2
/bet-review 003
```

Skills call each other, so one command is usually enough. Every skill loads `product-context` first. `what-next` loads `product-grill`, `pulse`, `feedback-digest` or `bet-review` when what they produce is missing or stale, and hands the chosen bet to `shape-bet`.

Skills pass state to each other through the files in `docs/product/`, so you can start a fresh session between two skills and lose nothing.

## Which skill, when

| You are thinking | Use |
| --- | --- |
| "I am starting to work on this product with an agent" | `product-grill` |
| "What should I build next?" | `what-next` |
| "I have an idea for a feature" | `shape-bet` |
| "Did the thing I shipped work?" | `bet-review` |
| "How is the product doing?" | `pulse` |
| "What are users complaining about?" | `feedback-digest` |
| "People sign up and disappear" | `audit-first-run` |
| "I do not trust my numbers", "what should I track?" | `audit-analytics` |
| "The agent cannot see my analytics, payments or support inbox" | `connect-sources` |
| "The brief no longer describes the product" | `product-grill`, as a refresh |

When in doubt, call `what-next`. It checks what is missing and loads the skill that fills it.

## The first time on a product

1. **`/product-grill`**. The agent reads the repo, then asks in rounds. Each question comes with a suggested answer you can accept with "yes". Say "I don't know" when you don't: the gap goes into the ledger of assumptions. Read the resulting `PRODUCT.md` and correct anything that reads false. This is the one skill that needs you for the whole run.
2. **`/connect-sources`**. It sweeps the code for analytics, payment, error and feedback tools, asks about the sources that leave no trace in code (the support inbox, store reviews, interview notes), and runs one real query per source. Expect to create a few read-only keys along the way.
3. **`/audit-analytics`**. Run it before you trust a number. If the product has no tracking at all, the audit still gives you the short tracking plan to add.
4. **`/pulse`, `/feedback-digest`, `/audit-first-run`**, in any order. Each writes a dated file under `evidence/`.
5. **`/what-next`**.

The short version is `/product-grill`, then `/what-next`. `what-next` works with no sources at all: it marks its claims as assumed and puts connecting a source among the candidates. Do the full run when you want the recommendation to rest on evidence.

## The loop

After the first run, the work is a loop:

```
/what-next  ->  /shape-bet  ->  build  ->  /bet-review  ->  /what-next
```

- `what-next` ends with a decision recorded in `DECISIONS.md` and passes the chosen bet to `shape-bet`.
- `shape-bet` ends with a bet file, status `shaped`.
- You build it with the repo's normal flow (see [From bet to build](#from-bet-to-build)). Set the status to `building` when work starts and to `live`, with the date, when users can see it. `bet-review` picks up only bets marked `live`.
- The review date is written in the bet file and nothing reminds you of it, so put it in your calendar. `what-next` also checks for overdue bets each time it runs.
- `bet-review` ends with a verdict (`kept`, `iterating` or `killed`) and updates `PRODUCT.md` with what was learned.

Keep one bet in flight. With a small team, `what-next` recommends finishing the bet that is building before starting another.

## Rhythm

| How often | Skill | Why then |
| --- | --- | --- |
| Once per product | `product-grill`, `connect-sources` | Everything else reads what they write |
| Every one or two weeks | `pulse` | A pulse counts as stale after 14 days |
| Monthly | `feedback-digest` | A digest counts as stale after 30 days |
| When a flow changes | `audit-analytics`, `audit-first-run` | An audit is stale once the flow it covers has changed |
| When you finish or drop a piece of work | `what-next` | |
| Before building anything larger than a fix | `shape-bet` | |
| On each bet's review date | `bet-review` | |
| When the stage changes, the goal's date passes, or a review contradicts the brief | `product-grill`, as a refresh | It keeps what still holds and grills only what changed |

A possible week: `/pulse` on Monday, then `/bet-review` if a review date has passed, or `/what-next` if nothing is building.

## Skill by skill

### product-grill

- **Use it when** you start on a product, when `PRODUCT.md` is missing, or as a refresh when the brief has drifted.
- **Skip it when** you want one feature scoped. That is `shape-bet`.
- **Have ready** nothing but time and honest answers. The agent looks up what the repo and the sources can tell it.
- **You get** `PRODUCT.md`: user, job, value moment, return, model, distribution, goal, constraint, limits, non-goals, taste, and the ledger. It closes with the three riskiest assumptions and the cheapest way to check each.
- **Watch for** the push on vague answers ("developers", a goal with no number). It pushes once. A recorded gap is a better outcome than an answer made up to end the question.

### connect-sources

- **Use it when** the grill is done, when you add a tool (analytics, payments, a helpdesk), or when a skill reports a source as missing or a query stops working.
- **Have ready** access to the dashboards where keys are created, and the list of places feedback arrives that the code does not show.
- **You get** `sources.md`: each source with its status (`connected`, `manual` or `blocked`), the query that proved it, and its caveats. A Gaps section lists what stays invisible and the cheapest way to fill it.
- **Watch for** the question about whether a number matches your dashboard. Check it properly: it is the first test of whether the data can be trusted. A `manual` source needs you to drop an export into `evidence/imports/`. The skill proposes installs for gaps and leaves the installing to you.

### pulse

- **Use it when** you want to know how the product is doing, before `what-next` if the last pulse is older than two weeks, or after a release to compare before and after.
- **Skip it when** you are tempted to check daily. The default window is 28 days against the 28 before, and with few users a day-to-day change is noise.
- **Have ready** at least one connected source. Without `sources.md`, it loads `connect-sources` first.
- **You get** a dated file: a three-line headline (what moved, the weakest stage, the biggest unknown), one or two numbers per funnel stage with their denominators, one segment cut, blind spots, data defects, and every query used. Under 100 weekly active users it adds one line per user for the last 20 signups.
- **Watch for** the fact that a pulse reports and stops. What to do about it is `what-next`. It reuses the previous pulse's queries so the two are comparable. If you want a definition changed, say so, and it reports the number both ways once.

### feedback-digest

- **Use it when** feedback has piled up, before `what-next` if the last digest is older than a month, after a launch or a price change, or after a round of user conversations (put the notes in `evidence/imports/`).
- **Skip it when** there are a handful of items. Read them yourself.
- **Have ready** the feedback sources in `sources.md`, or exports in `evidence/imports/`.
- **You get** problems ranked by distinct users, each with segments, severity and one quote. Then the requests mapped to the problem each one serves, the praise, and Silence: the segments that never write.
- **Watch for** the ranking. It counts users, so one person writing five times counts once, and a request is filed under the problem behind it. The most requested feature may not be at the top. Read the Silence section: users who signed up and never reached the value moment are usually the largest group and the quietest.

### audit-analytics

- **Use it when** you are about to trust a pulse for the first time, when two tools disagree on a number, when you ask "what should I track?", before a bet whose signal needs new events, or just before launch to get the tracking plan.
- **Skip it when** the goal and the value moment are not written down. The audit starts from the questions the product needs answered, so it will ask for them first.
- **Have ready** a development or staging build it can run, to watch the events as they are sent. Without one, the live checks are graded as assumed.
- **You get** a verdict (can the goal metric be measured, can the constraint be located), findings ranked as `wrong`, `blind`, `noise` or `style` with a fix and an effort each, and `tracking-plan.md` as the target state.
- **Watch for** where it stops: at the report. Choose the fixes and ask for them as normal engineering work. Expect a short plan. A product with a few hundred users is usually served by under ten events.

### audit-first-run

- **Use it when** signups do not activate, before spending time or money on acquisition, after changing signup or onboarding, or when you have seen your own onboarding too often to judge it.
- **Skip it when** there is no build to run or no tool to drive it. In that case give it a screen recording of a fresh signup.
- **Have ready** a browser tool for the web or a simulator for an app, a staging build if you have one, and your go-ahead if the walk has to happen on production.
- **You get** the distance to value (steps, time, fields, decisions), a friction log with screenshots, a verdict per step (`keep`, `defer`, `default`, `remove`), the shortest path that could replace the current one, and findings ranked by users lost at the step.
- **Watch for** the cold walk: it does not read the onboarding code first, on purpose. On production it stops before any real payment and tells you which test identity it created, so you can filter it out of analytics.

### what-next

- **Use it when** you finish or drop a piece of work, when the backlog is too long to read, or when you are torn between ideas.
- **Skip it when** a bet is building and nothing blocks it. It will tell you to finish that.
- **Have ready** your own ideas. It asks for them in one question and ranks them with the rest.
- **You get** one bet with its mechanism, arithmetic, evidence grade and smallest version. Then why it beats the runner-up, the fact that would change the pick, the next two in order, and the "not now" list with reasons. All of it is recorded in `DECISIONS.md`.
- **Watch for** candidates that are not features: removing a step, testing an assumption with five conversations, changing the price. The pick may be one of those. A trust breaker (data loss, a security hole, broken payments, a crash on the core path) goes first and straight to the fix. The decision is yours: redirect it and your choice is what gets recorded.

### shape-bet

- **Use it when** `what-next` has picked something, or when you arrive with your own idea and want it tested from the product side before any code.
- **Skip it when** the work is a bug fix, a chore or a trust breaker. There is no hypothesis to test.
- **Have ready** an opinion on the appetite: how many days this is worth to you.
- **You get** `bets/NNN-slug.md`: problem, hypothesis, appetite, smallest slice, signal with its baseline, kill criteria, review date and rollout.
- **Watch for** the attempts to avoid building: a fake door, a manual version for five users, a copy change. With low traffic it chooses a before-and-after reading or a qualitative one over an experiment, and says that the reading is weaker. If the idea acts on neither the goal nor the constraint, it tells you.

### bet-review

- **Use it when** a bet's review date has passed, or when you ask whether something worked. It also reviews a change that shipped without a bet file, with a weaker reading.
- **Skip it when** too few users have seen the change. It answers "not readable yet" and sets a new date.
- **Have ready** the bet marked `live` with its date, and what else changed in the window: a campaign, a price change, an outage.
- **You get** the result read with the same query as the baseline, the guardrails, one segment cut, a verdict, the updates to `PRODUCT.md`, and a cleanup task for the switch and the losing code path.
- **Watch for** the criteria. It holds the result against what was written before the build. To depart from that, give your reason and it is recorded as an override. A bet gets one iteration before the keep or kill call.

### product-context

You never call it. The other nine load it. Read it once, because it defines the evidence grades, the rules for reporting numbers and the file layout. Edit it to change a rule for all skills at once, such as how long a pulse stays fresh.

## By stage

`PRODUCT.md` records the product's stage, and the skills adapt to it. What to lean on:

| Stage | Lean on | Leave for later |
| --- | --- | --- |
| `pre-launch` | `product-grill`. `shape-bet` with qualitative signals. `audit-first-run` on the staging build. `audit-analytics` once, just before launch, for the tracking plan | `pulse`. `feedback-digest`, unless you have interview notes |
| `pre-pmf` | All of them. `pulse` reads individual journeys. Bets are read before and after, backed by watched sessions and conversations | Experiments |
| `growth` | `pulse` with cohort rates. Experiments in `shape-bet` when the traffic fills both arms within four weeks. `feedback-digest` monthly | |
| `mature` | `pulse`, `feedback-digest`, `bet-review` | Segment economics and cost to serve have no skill yet |

## From bet to build

The skills stop at the decision. A shaped bet is the brief for the build:

- **Smallest slice** is the scope. Its "Cut" list is what stays out.
- **Events needed** is part of the work. A bet whose signal cannot be read was not worth shipping.
- **Rollout** names the switch to build behind and how to undo it.

Hand the bet file to whatever you build with. With Matt Pocock's engineering skills, that is `/to-spec` or `/implement` pointed at the file.

## The files

Everything is plain Markdown under `docs/product/`, and you can edit it by hand.

| File | Editing it yourself |
| --- | --- |
| `PRODUCT.md` | Yes, whenever you know better. Update the review date |
| `sources.md` | Yes, when access or a query changes |
| `tracking-plan.md` | Yes, as events ship |
| `evidence/` | Leave as written. A new snapshot is a new file, so old numbers stay comparable |
| `bets/` | Update the status. Leave the hypothesis and kill criteria alone once the bet is live |
| `DECISIONS.md` | Append only |

## Common problems

**The product has no analytics at all.** Start anyway. The database usually answers the first questions (signups, the value moment, who came back), and `connect-sources` treats it as a source. `audit-analytics` gives you the minimum to add.

**I asked in plain words and the skill did not load.** Type its name with a slash.

**The numbers differ from my dashboard.** Say so when `connect-sources` asks. The gap is recorded and becomes the first finding of `audit-analytics`, which reconciles analytics against the database and the payment processor.

**There are too few users for an experiment.** That is the normal case for a small product. `shape-bet` picks a before-and-after or a qualitative reading and states its limits.

**The agent asks me for something it could look up.** Tell it to look it up. Finding facts is its job in every skill. You are there for decisions and for what only you know.

**I disagree with the recommendation.** Say which candidate you want and why. `what-next` records your choice and the reason, and `shape-bet` continues from there.

**The product spans several repos.** Run the skills in the repo where `docs/product/` should live and give the agent the paths to the others, so the sweeps in `connect-sources` and `audit-analytics` cover them.

**The session got long.** Start a new one. The next skill reads `docs/product/` and picks up from the files.

**I am worried about what it does with real user data.** Sources are read, never written to. Any write to an outside service waits for your go-ahead. Keys stay in environment variables. What lands in the repo is aggregated, and journeys and quotes are labelled by segment, with names, emails and ids left in the source.
