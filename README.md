# VersusGame

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
