# VersusGame

VersusGame (branded "Versus") is an interactive gaming and audience-engagement company. Its AI
content-gamification engine turns publisher articles, live broadcasts and on-demand video into
embeddable prediction games — the MiniGames product — with leaderboards, virtual wallets,
coin/ticket economies, prize payouts and contextual "seamless ads" layered on top of partner
content. Publicly named partners include ABC, Microsoft, Disney, BuzzFeed, Billboard, ESPN and UFC.

- Website: https://www.versusgame.com/
- API reference (Swagger UI): https://api.versusgame.com/api
- OpenAPI 3.0.0 (live): https://api.versusgame.com/api-json

## The API

One production REST API at `https://api.versusgame.com` — **239 operations across 190 paths and 286
component schemas**, covering authentication (email/password plus Apple, Google, Facebook and
Microsoft social login), games and gameplays, gamesets, leaderboards and prize awards, creator
tooling (games, playlists, widgets, custom URLs), embeddable widget delivery, categories, products,
assets, keyframes, wallets and ledger, Stripe and GCOW payments/payouts, meter ads, partner domains,
notifications, profiles, and the "autogame" AI pipeline.

Auth is a bearer JWT (`access-token`) for user/creator/admin calls and an `x-api-key` header for the
two `/internal/*` service-to-service operations. Twelve write operations require an idempotency key.

## Artifacts

| Artifact | Path |
|---|---|
| OpenAPI (verbatim) | `openapi/versusgame-openapi-original.json` |
| Authentication | `authentication/versusgame-authentication.yml` |
| Conventions (idempotency, pagination, errors) | `conventions/versusgame-conventions.yml` |
| Error catalog | `errors/versusgame-problem-types.yml` |
| Lifecycle | `lifecycle/versusgame-lifecycle.yml` |
| Conformance | `conformance/versusgame-conformance.yml` |
| Data model | `data-model/versusgame-data-model.yml` |
| Embedded components | `components/versusgame-components.yml` |
| Agentic access contracts | `agentic-access/versusgame-agentic-access.yml` |
| Agent skills | `skills/_index.yml` |
| Candidate MCP tools (not published by VersusGame) | `mcp/versusgame-mcp.yml` |
| OpenAPI overlay | `overlays/versusgame-api-overlay.yaml` |
| llms.txt | `llms/versusgame-llms.txt` |
| Domain security probe | `security/versusgame-domain-security.yml` |
| Well-known probe (nothing found) | `well-known/versusgame-well-known.yml` |
| Packages (none published) | `packages/versusgame-packages.yml` |

## What VersusGame does not publish

No developer portal, getting-started guide, SDK, CLI, Postman collection, changelog, status page,
deprecation policy, SLA, security.txt, trust center, pricing page, blog, roadmap, MCP server, A2A
agent card, webhooks or AsyncAPI. The Swagger UI at https://api.versusgame.com/api is the entire
public developer surface.

- Secondary-market listing: https://forgeglobal.com/versusgame_stock/
