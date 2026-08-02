---
name: Create and settle a VersusGame prediction game
description: >-
  Authenticate as a creator, create a two-sided prediction game, watch it fill, then declare the
  winning side and settle it. The core VersusGame creator flow.
api: openapi/versusgame-openapi-original.json
generated: '2026-08-02'
method: generated
source: openapi/versusgame-openapi-original.json
operations:
  - AuthController_login
  - CategoriesController_list
  - GamesCreatorController_createGame
  - GamesCreatorController_byId
  - GamesUserController_getStats
  - GamesCreatorController_updateGame
  - GamesCreatorController_declareGame
  - GamesCreatorController_cancelGame
---

# Create and settle a VersusGame prediction game

Base URL: `https://api.versusgame.com` (the harvested OpenAPI ships an empty `servers[]` — use this
host explicitly).

## 1. Authenticate

`POST /auth/login` (`AuthController_login`) with `SigninDto`. The response carries a JWT. Send it on
every later call as `Authorization: Bearer <jwt>`.

Social login is available instead: `GET /auth/{provider}` then the matching
`/auth/{provider}/callback` for `apple`, `google`, `facebook`, `microsoft`.

Tokens are refreshed with `POST /auth/refresh` (`AuthController_refresh`), which requires the refresh
token, not the access token.

## 2. Pick a category

`GET /v1/categories` (`CategoriesController_list`) returns `ListCategory`. Keep the `id` of the
category the game belongs to — `Game.categoryId`.

## 3. Create the game

`POST /v1/creator/games` (`GamesCreatorController_createGame`) with `CreateGameDto`.

- **`x-idempotency-key` is a REQUIRED header.** Generate one unique value per logical creation and
  reuse it verbatim on any retry. Do not generate a fresh key when retrying — that creates a second
  game.
- A game is two-sided: `sideA` and `sideB` (`Side`), with `gameTitle`, `text`, `categoryId`,
  `gameType` (`GameType`), `orientation` (`Orientation`), `language` (`Language`).
- Timing fields drive the lifecycle: `startTime`, `lastJoinTime`, `endTime`.
- Optional flags: `isPrivate`, `isAd`. Media is attached by asset id — `videoAssetId`,
  `questionVideoAssetId`, `landscapeVideoAssetId`, `audioAssetId`, `resultAssetId` — upload first via
  `POST /v1/assets` (`AssetsController_create`).

## 4. Watch it fill

- `GET /v1/creator/games/{gameId}` (`GamesCreatorController_byId`) for the creator view. Pass
  `expand` (or `_expand`, both are accepted) with values from `GameExpand` to inline related objects.
- `GET /v1/games/{id}/stats` (`GamesUserController_getStats`) for the public vote split —
  `votePercentageSideA` / `votePercentageSideB` and the `StatsEntity` breakdown by ticket type.
- `GET /v1/creator/games` (`GamesCreatorController_findAll`) lists your games. Paginate with `limit`
  and `offset`; filter with `search`, `state`, `includeState`, `excludeState`; order with `sort` +
  `asc`.

## 5. Amend before settlement

`PATCH /v1/creator/games/{gameId}` (`GamesCreatorController_updateGame`) with `UpdateGameDto`.

## 6. Declare the winner

`POST /v1/creator/games/{gameId}/declare` (`GamesCreatorController_declareGame`) with
`DeclareGameDto`. Returns `204`. This settles the game, sets `winningSide`, and makes rewards
claimable by players (see the play-and-claim skill).

`GameEntity` exposes `isPendingResult`, `isFinalResult` and `resultChanged` — read those before
declaring again.

## 7. Or cancel it

`DELETE /v1/creator/games/{gameId}` (`GamesCreatorController_cancelGame`) returns `204`.

## Rules

- **Idempotency.** `x-idempotency-key` is required on creation. See
  `conventions/versusgame-conventions.yml` for the full list of 12 operations that require it.
- **Errors.** Failures return `{"message": ..., "error": ..., "statusCode": ...}`. Only 17 of 239
  operations declare a 4xx in the spec, and none declare 401/403/5xx — do not assume an undeclared
  status cannot occur. See `errors/versusgame-problem-types.yml`.
- **Roles.** `/v1/creator/*` requires a creator role; `/v1/admin/*` and `/superadmin/*` are staff
  surfaces and are not part of this flow.
- **Rate limits.** None are published. Back off on your own signal.
