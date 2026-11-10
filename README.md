# Product engineer skills

Agent skills for the product side of product engineering: understanding the product, reading its evidence, deciding what to build next, and checking whether it worked.

They are written for a small team or a solo builder working with a coding agent inside the product's repo. They follow the Agent Skills format (`<name>/SKILL.md`), so they work in Claude Code and in other harnesses that read it.

For when to reach for each one, how to call it and what to have ready, read the [guide](GUIDE.md).

## The loop

```
product-grill ──> connect-sources ──> audit-analytics
      │                  │
      │                  ├──> pulse
      │                  ├──> feedback-digest
      │                  └──> audit-first-run
      │                            │
      └────────────────────────────┴──> what-next ──> shape-bet ──> (build) ──> bet-review
                                            ▲                                       │
                                            └───────────────────────────────────────┘
```

The first run on a product goes left to right. After that the loop is `what-next`, `shape-bet`, build, `bet-review`, with a fresh `pulse` when the numbers are stale. `what-next` checks what is missing and loads the skill that fills it, so it is a safe place to start.

## The skills

| Skill | What it does | Writes |
| --- | --- | --- |
| `product-grill` | Interviews you about the whole product: user, job, value moment, model, goal, constraint. Looks up the facts itself and pushes on vague answers | `PRODUCT.md` |
| `connect-sources` | Finds where evidence lives (analytics, database, revenue, feedback, errors, acquisition, replay), picks a way to query each and proves it works | `sources.md` |
| `pulse` | Pulls one or two numbers per funnel stage for a window and says where the funnel is weakest | `evidence/<date>-pulse.md` |
| `feedback-digest` | Reduces raw feedback to ranked problems with user counts and quotes, and names the segments nobody hears from | `evidence/<date>-feedback.md` |
| `audit-analytics` | Starts from the questions the product needs answered, maps them to what is tracked, and tests whether the numbers are true | `evidence/<date>-analytics-audit.md`, `tracking-plan.md` |
| `audit-first-run` | Walks the product cold as a new user and measures the distance to the value moment | `evidence/<date>-first-run.md` |
| `what-next` | Names the constraint on the goal, gathers candidates, and recommends one bet with the arithmetic behind it | `DECISIONS.md` |
| `shape-bet` | Grills one idea into a bet: hypothesis, appetite, smallest slice, signal, kill criteria | `bets/NNN-slug.md` |
| `bet-review` | Reads a shipped bet against the criteria written before the build: keep, iterate or kill | the bet file, `PRODUCT.md` |
| `product-context` | Shared reference the others load: workspace layout, evidence grades, number rules, interview rounds, data guardrails | nothing |

Everything is written under `docs/product/` in the product's repo. The layout is defined once, in [product-context](product-context/SKILL.md).

## What they have in common

- **Facts are the agent's job.** A skill reads the repo, the live product and the connected sources before it asks you anything. You are asked for decisions and for what only you know.
- **Every claim carries a grade**: `measured`, `observed`, `reported` or `assumed`. A recommendation says which one it rests on, and assumptions that decisions rest on go into a ledger with the cheapest way to check each.
- **Numbers come with their denominator.** Under 100 users the skills report counts and read individual journeys, because most advice about product analytics assumes traffic that a small product does not have.
- **One bet at a time, with the exit written first.** Kill criteria are fixed before the build so the result cannot be argued with afterwards.
- **Read-only on real data.** Sources are queried, never written to. Credentials stay in environment variables, and user data enters the repo aggregated or anonymised.

## Install

For every project on this machine:

```sh
mkdir -p ~/.claude/skills
ln -s "$PWD"/skills/*/ ~/.claude/skills/
```

For one product only, link them into that repo's `.claude/skills/` instead.

Once this folder is in a GitHub repo, `npx skills add <owner>/<repo>` installs them the same way as any other skill set.

Then, in a product's repo, start with `/product-grill`. The [guide](GUIDE.md) walks through the first run.

## Where they sit on the atlas

| Skill | Territory | Capabilities |
| --- | --- | --- |
| `product-grill` | Discovery, Strategy | User understanding, Problem identification, Product vision, Goal setting |
| `connect-sources` | Measurement, Operations | Product instrumentation, Product observability |
| `pulse` | Measurement | Product metrics, Behavioral analysis |
| `feedback-digest` | Discovery, Measurement | User research, Qualitative evaluation |
| `audit-analytics` | Measurement | Product instrumentation |
| `audit-first-run` | Activation | First-user experience, Onboarding design, Time to value, Activation diagnosis |
| `what-next` | Strategy, Measurement | Prioritization, Evidence-based decisions |
| `shape-bet` | Strategy, Measurement, Shipping | Product scoping, Experiment design, Controlled releases |
| `bet-review` | Measurement, Shipping | Evidence-based decisions, Statistical reasoning, Iterative delivery |

## Not here yet

Experience, Retention and Distribution have no skill of their own. The obvious next ones: an interface and taste review, a retention and churn diagnosis, a pricing review, and a distribution review. Each would write to `evidence/` and feed `what-next` like the audits above.

None of these skills has been run against a real product yet. Expect the first runs on Linguaso and Etengabe to change them.

## Credits

The interview mechanics (rounds, the frontier, a recommended answer per question, facts looked up by the agent) come from Matt Pocock's `grilling` skill, and the files follow his notes on writing for agents.
