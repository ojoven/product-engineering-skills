# Providers

Signs to sweep for, by category. A sign is a package name, a script host, an SDK call or a pattern in environment variable names. The lists are a starting point: any unfamiliar SDK that sends events, messages or payments is a candidate too.

This file tells you how to **detect** a provider. For how to **query** it, read the provider's current documentation.

## Product analytics

| Provider | Signs |
| --- | --- |
| PostHog | `posthog-js`, `posthog-node`, `posthog-react-native`, `posthog` (Python, PHP), env `*POSTHOG*` |
| Amplitude | `@amplitude/analytics-*`, `amplitude-js`, env `*AMPLITUDE*` |
| Mixpanel | `mixpanel-browser`, `mixpanel`, env `*MIXPANEL*` |
| Google Analytics 4 | `gtag(`, `googletagmanager.com/gtag`, ids shaped `G-XXXXXXX`, `firebase/analytics`, `@react-native-firebase/analytics` |
| Plausible | a script tag with `data-domain`, host `plausible.io` or self-hosted, `window.plausible`, `plausible-tracker` |
| Umami | a script tag with `data-website-id` |
| Fathom | `cdn.usefathom.com`, `fathom-client` |
| Simple Analytics | `scripts.simpleanalyticscdn.com` |
| Matomo | `_paq.push`, `matomo.js` |
| Vercel Analytics | `@vercel/analytics`, `@vercel/speed-insights` |
| Cloudflare Web Analytics | `static.cloudflareinsights.com` |

Two kinds of install hide what they send:

| Kind | Signs | What to ask |
| --- | --- | --- |
| Router (Segment, RudderStack) | `@segment/analytics-next`, `analytics.track(`, `@rudderstack/analytics-js` | Which destinations the router forwards to |
| Tag manager (Google Tag Manager) | ids shaped `GTM-XXXXXX` | The list of tags in the container, or its export |

Events can be sent from the server as well as the client. Search server code and background jobs, and webhook handlers in particular.

## The database

| Signs | Notes |
| --- | --- |
| `DATABASE_URL`, `DB_HOST`, ORM schemas and migrations (Prisma, Drizzle, Eloquent, ActiveRecord, Django) | Read the schema for the tables that hold accounts, the value moment and subscriptions |
| Columns such as `created_at`, `last_seen_at`, `utm_source`, `referrer`, `plan`, `cancelled_at` | Each one answers a funnel question without any analytics tool |

Connect with a read-only role or to a replica. The application's own production credentials can write, so they stay unused here.

## Revenue

| Provider | Signs |
| --- | --- |
| Stripe | `stripe`, `@stripe/stripe-js`, `stripe/stripe-php`, env `STRIPE_*`, a webhook route |
| Paddle | `@paddle/paddle-js`, `@paddle/paddle-node-sdk`, env `PADDLE_*` |
| Lemon Squeezy | `@lemonsqueezy/lemonsqueezy.js`, env `LEMONSQUEEZY_*` |
| Polar | `@polar-sh/sdk` |
| RevenueCat | `react-native-purchases`, `purchases_flutter`, `@revenuecat/purchases-capacitor`, the `RevenueCat` pod |
| Store billing used directly | StoreKit, Google Play Billing. Revenue then lives in App Store Connect and Play Console reports |

Cancellation reasons are feedback as well as revenue data: note where they are stored.

## Feedback and support

Most of this category leaves no trace in the repo. Ask about every row.

| Source | Signs, when there are any |
| --- | --- |
| Support inbox | A `support@` or `hello@` address in the UI or email templates |
| Chat and helpdesk (Intercom, Crisp, Zendesk, Help Scout, Plain) | `@intercom/messenger-js-sdk`, `widget.intercom.io`, `crisp-sdk-web`, `client.crisp.chat`, `beacon-v2.helpscout.net` |
| Feedback boards (Canny, Featurebase) | `canny.io/sdk.js` |
| In-app feedback kept in the database | Tables, routes or forms named `feedback`, `contact`, `report` |
| Issue trackers | GitHub Issues and Discussions, Linear, a public roadmap |
| Store reviews | App Store and Google Play listings |
| Surveys | NPS prompts, Typeform, Tally, Google Forms, an exit or cancel survey |
| Community | Discord, Slack, Telegram, a forum, replies on social posts |
| Conversations | Interview notes, call transcripts, sales emails |

## Errors and performance

| Provider | Signs |
| --- | --- |
| Sentry | `@sentry/*`, `sentry-sdk`, `sentry/sentry-laravel`, env `SENTRY_DSN` |
| Bugsnag | `@bugsnag/*` |
| Rollbar | `rollbar` |
| Datadog RUM | `@datadog/browser-rum` |
| Firebase Crashlytics | `@react-native-firebase/crashlytics`, `firebase_crashlytics` |
| Server and platform logs | Log drains, hosting dashboards, uptime monitors |

## Acquisition

| Source | Signs |
| --- | --- |
| Search | Google Search Console, Bing Webmaster Tools: a verification meta tag or DNS record |
| Campaign parameters | Code that reads `utm_*` or `ref` and where it stores them |
| Email (Resend, Postmark, Loops, Customer.io, Mailchimp) | Their SDKs and env names, plus the templates sent in the first week |
| App stores | App Store Connect and Play Console: impressions, page views, install rate |
| Ads | Meta, Google Ads or TikTok pixels and conversion calls |

## Session replay

| Provider | Signs |
| --- | --- |
| PostHog replay | `session_recording` in the PostHog config |
| Microsoft Clarity | `clarity.ms` |
| Hotjar | `static.hotjar.com`, `@hotjar/browser` |
| FullStory | `@fullstory/browser` |
| LogRocket | `logrocket` |
| Sentry Replay | `replayIntegration` |

## Flags and experiments

Not an evidence category of its own, but note it in `sources.md` when present: it shows what is being tested and to whom.

| Provider | Signs |
| --- | --- |
| PostHog flags | `isFeatureEnabled`, `getFeatureFlag` |
| LaunchDarkly | `launchdarkly-*` |
| GrowthBook | `@growthbook/growthbook` |
| Statsig | `statsig-*` |
| Unleash | `unleash-client`, `unleash-proxy-client` |
