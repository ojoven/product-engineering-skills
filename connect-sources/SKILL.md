---
name: connect-sources
description: Find and connect the product's evidence sources (analytics, feedback, revenue, errors, acquisition, the database) so agents can query them. Use when the user wants an analytics, feedback or revenue service integrated as context, or when a product skill needs a source that docs/product/sources.md does not list.
---

# Connect sources

Find every place evidence about this product lives, pick a working way to read each one, prove it with a real query, and write the result to `sources.md`. Every later skill queries through that file.

Load the `product-context` skill before anything else. Its guardrails on read-only access, credentials and user data govern every step here.

## Categories

A product has evidence in seven categories. Each one ends this skill as `connected`, `manual`, `absent` or `blocked`.

| Category | Answers |
| --- | --- |
| Product analytics | What users do inside the product |
| The database | Ground truth: accounts, the value moment, subscriptions |
| Revenue | Who pays, how much, who cancels and why |
| Feedback and support | What users say: tickets, reviews, issues, surveys, community, interviews |
| Errors and performance | Where the product fails or stalls for users |
| Acquisition | Where users come from: search, referrers, campaigns, store listings |
| Session replay | What a single visit looked like |

## 1. Sweep

Find the facts before asking for any.

- Sweep the code for the signs in [PROVIDERS.md](PROVIDERS.md): packages, script hosts, SDK calls. Cover the web client, the server, mobile apps and marketing sites.
- List the environment variable **names** in `.env.example`, deploy config and CI config.
- List the tools this session can already use: connected MCP servers, CLIs on the path (`gh`, `stripe`, `psql`, `sentry-cli` and the like).
- Read an existing `sources.md` and re-verify what it claims.

Done when every category is marked with what was found and where, or marked as leaving no trace in the repo.

## 2. Ask

Ask the user, in rounds, only what the sweep cannot settle:

- sources that leave no trace in code: the support inbox, store reviews, a community chat, interview notes, spreadsheets
- which of the found sources they trust and look at, and which are dead installs
- what a router or tag manager forwards to, since its destinations live in a dashboard

## 3. Pick the access path

For each source, take the first rung of this ladder that works:

1. an MCP server for the provider, already connected or official and addable by the user
2. the provider's official CLI
3. its HTTP API, with a read-only key held in an environment variable
4. for the database, a read-only role or a replica
5. a **manual** export the user drops into `docs/product/evidence/imports/`

Read the provider's current documentation before the first call: endpoints and tools change, and a remembered call that fails silently costs more than the lookup.

When a rung needs a credential only the user can create, give them the exact steps: the settings page, the read-only scope, the variable name. Mark the source `blocked` until they have done it.

## 4. Prove it

A source is `connected` once a read-only query has returned real data in this session.

Run one query per source that answers a plain question, such as signups in the last 7 days, and ask the user whether the number matches what their dashboard shows. Where two sources count the same thing (signups in the database and in analytics, payments in the processor and in the database), run both and record the gap: that gap is the first finding for `audit-analytics`.

## 5. Write `sources.md`

One section per source:

```markdown
## <Provider> · <category> · <connected | manual | blocked>

Answers: <the questions this source is good for>
Access: <rung, tool or command, environment variable names>
Proof: <the query that worked, its result, the date>
Caveats: <timezone, sampling, ad-blocker undercount, test and internal traffic, how users are identified>
```

End the file with a **Gaps** section: each `absent` category, what stays invisible without it, and the cheapest way to fill it (a feedback link that writes to the database fills a feedback gap in an afternoon). Propose installs; leave the installing to a separate task the user approves.

Done when every category has a status, every `connected` source has a proof dated today, and the gaps are named.
