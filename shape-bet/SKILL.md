---
name: shape-bet
description: Shape one idea into a bet before it is built. Settles the hypothesis, appetite, smallest slice, signal and kill criteria, and writes the bet file. Use when the user has picked what to build and wants it scoped, or wants a feature idea grilled from a product point of view.
---

# Shape bet

Turn a chosen idea into a **bet**: a wager with a stated hypothesis, a fixed **appetite**, the smallest slice that can produce a **signal**, and the criteria for keeping or killing it, all written down before any code. The output is one file in `docs/product/bets/`.

Load the `product-context` skill before anything else. It defines the interview rounds used here.

## 1. Gather the facts

- Read `PRODUCT.md`, the entry for this bet in `DECISIONS.md` if there is one, and the evidence it cites.
- Read the code the bet touches, enough to size the smallest slice.
- Pull the **baseline**: the current value of the metric the bet aims at, with its query, from the latest pulse or straight from the source.
- Count the traffic: how many users a week reach the place where the bet acts.
- Read `tracking-plan.md` for whether the events needed to read the signal exist.

If the idea arrived without a decision behind it, check it against the goal and constraint in `PRODUCT.md` and tell the user plainly when it acts on neither.

## 2. Grill the bet

Settle these in rounds, each with a recommended answer drafted from the facts:

1. **Problem**: who has it, when, and the evidence, with its grade.
2. **Hypothesis**: the change, the users, the behaviour expected to change, the metric, baseline and target.
3. **Appetite**: how many days this is worth. Time is fixed and scope bends to fit it.
4. **Smallest slice**: the thinnest version that can produce the signal, and what is cut to get there.
5. **Signal**: the primary metric, the guardrail metrics that must hold, and how the result will be read.
6. **Kill criteria and review date**: the result that means remove it, decided now.
7. **Rollout**: who sees it, behind what switch, how it is undone.

Push on the slice with these, in order, and take the first that holds:

- Can the mechanism be tested with no build: a fake door, a manual version done by hand for five users, a change of copy?
- Can it ship for one segment, one platform or one entry point only?
- Can the hard part be hardcoded or done manually behind the screen for now?

## 3. Choose how the signal is read

The traffic decides the method.

- **Experiment**: for a rate with baseline `p` and the smallest absolute lift worth detecting `d`, each arm needs about `16 × p × (1 − p) / d²` users. Moving 20% to 25% needs about 1,000 per arm. Use an experiment when the traffic fills both arms within four weeks.
- **Before and after**: when it does not, compare the cohorts after release with the cohorts before, and back the comparison with direct observation: five sessions watched or five users asked. State in the bet that this reading is weaker, and name what else changed in the window.
- **Qualitative**: at `pre-launch` and for most `pre-pmf` bets, the signal is what a named number of real users did and said.

A bet whose signal cannot be read is unshaped. If an event is missing, adding it becomes part of the slice.

## 4. Write the bet

Number the file after the highest existing one and write it using [BET-FORMAT.md](BET-FORMAT.md), with status `shaped`.

Done when every section of the format is filled, the baseline is a real number with its query (or instrumenting it is the first task of the slice), the kill criteria name a number and a date, and the user has confirmed the bet.

Building it is ordinary engineering work, through whatever flow the repo uses. Set the status to `building` when work starts and `live` when users can see it, with the date.
