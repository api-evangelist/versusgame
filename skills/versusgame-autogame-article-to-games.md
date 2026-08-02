---
name: Turn an article into prediction games with autogame
description: >-
  Submit a partner article URL to the VersusGame autogame AI pipeline, review the generated game
  drafts, version them, and promote a draft into a live game. The v2 article pipeline.
api: openapi/versusgame-openapi-original.json
generated: '2026-08-02'
method: generated
source: openapi/versusgame-openapi-original.json
operations:
  - ArticleControllerV2_getDomains_v2
  - ArticleControllerV2_createArticle_v2
  - ArticleControllerV2_findArticles_v2
  - ArticleControllerV2_createDraft_v2
  - ArticleControllerV2_listGameDrafts_v2
  - ArticleControllerV2_drafyById_v2
  - ArticleControllerV2_newVersion_v2
  - ArticleControllerV2_setState_v2
  - ArticleControllerV2_createGame_v2
  - ScraperController_scrape
---

# Turn an article into prediction games with autogame

Base URL: `https://api.versusgame.com`. Every operation here requires
`Authorization: Bearer <jwt>`.

Use the **v2** tier (`/v2/autogame/article/*`). The v1 tier (`/autogame/article/*`) still exists and
is not flagged deprecated, but v2 is the partner-scoped shape.

## 1. Find the partner domain

`GET /v2/autogame/article/partners/{partnerId}/domains` (`ArticleControllerV2_getDomains_v2`) returns
the `PartnerDomain` records for a partner — `hostName` and `draftsCount`.

`GET /v2/autogame/article/partners/{partnerId}/domains/{domainId}/articles`
(`ArticleControllerV2_getDomainArticles_v2`) lists what has already been ingested for that domain, so
you do not re-submit.

## 2. Ingest the article

`POST /v2/autogame/article` (`ArticleControllerV2_createArticle_v2`) with `CreateArticleDtoV2` —
the article `url`, its `partnerId`, and an `ArticleSourceType`. Returns an `ArticleEntity`.

To inspect a page before submitting it, `GET /autogame/scraper` (`ScraperController_scrape`) fetches
and parses it into a `ParsedDocument`.

`GET /v2/autogame/article` (`ArticleControllerV2_findArticles_v2`) finds existing articles; the
`articleUrl` query parameter matches by source URL.

## 3. Generate drafts

`POST /v2/autogame/article/drafts/{articleId}` (`ArticleControllerV2_createDraft_v2`) with
`CreateDraftDto` returns `201` and produces `GameDraft` records — an AI-generated `Question`,
`Answers`, `Side` pair, `Synopsis` and `Overlay` per draft.

Read them back:
- `GET /v2/autogame/article/drafts` (`ArticleControllerV2_listGameDrafts_v2`) — `ListGameDraftV2`.
- `GET /v2/autogame/article/drafts/{id}` (`ArticleControllerV2_drafyById_v2`) — one draft.
- `GET /v2/autogame/article/partners/{partnerId}/articles/{articleId}/drafts`
  (`ArticleControllerV2_getArticlesDraft_v2`) — drafts scoped to one partner article.

## 4. Iterate

- `POST /v2/autogame/article/drafts/{id}/version` (`ArticleControllerV2_newVersion_v2`) with
  `NewVersionDto` returns `204` and cuts a new `GameDraftVersion` — regenerate without losing the
  previous take.
- `POST /v2/autogame/article/drafts/{id}/set_state` (`ArticleControllerV2_setState_v2`) with
  `SetStateDto` returns `204` and moves the draft through `GameDraftState` (review, approve, reject).

## 5. Promote a draft into a live game

`POST /v2/autogame/article/drafts/{draftId}/game` (`ArticleControllerV2_createGame_v2`) returns
`201`.

**`x-idempotency-key` is a REQUIRED header on this call.** It is the boundary between a draft and a
real, playable, money-bearing game — reuse the same key on retry or you will publish the draft twice.

From here the game behaves like any other: see
`skills/versusgame-create-and-settle-a-game.md` for declaring the result.

## Supporting model surfaces

The autogame tag also proxies the underlying vendors, all under `Authorization: Bearer`:

- `POST /autogame/openai/chat_completions` (`OpenAIController_chatCompletionCreate`)
- `GET|POST /autogame/google_search` (`GoogleSearchController_search`, `GoogleSearchController_insert`)
- `GET /autogame/elevenlabs`, `POST /autogame/elevenlabs/voice`, `/voice-asset`, `/speech_marks`
- `GET /autogame/polly`, `POST /autogame/polly/voice`, `/voice-asset`, `/speech_marks`
- `POST /autogame/remotion/render` (`RemotionController_render`) — video render, returns a `JobStatus`
- `POST /autogame/upload` (`AutogameController_createUpload`)

Treat these as privileged passthroughs: they spend third-party model credits per call. They are
classified `acting` in `agentic-access/versusgame-agentic-access.yml` and should not be exposed to an
agent without a spend ceiling.

## Rules

- **Idempotency.** Required on `ArticleControllerV2_createGame_v2` (header) and on the v1 equivalents
  `ArticleController_createGame` (header) and `AutogameGameController_createGame` (**query parameter
  `idempotencyKey`, not a header** — the one place the carrier changes).
- **Errors.** `{"message", "error", "statusCode"}`; none of the autogame operations declare a 4xx.
- **Cost.** Every draft generation runs a model. Check `GET /autogame/chat-history`
  (`ChatHistoryController_listChatHistories`) before regenerating.
