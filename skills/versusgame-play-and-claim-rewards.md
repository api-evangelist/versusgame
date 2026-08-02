---
name: Play a VersusGame game and claim the reward
description: >-
  Authenticate as a player, check the wallet, place a prediction on an open game, then claim the
  reward once the game settles and reconcile it against the ledger.
api: openapi/versusgame-openapi-original.json
generated: '2026-08-02'
method: generated
source: openapi/versusgame-openapi-original.json
operations:
  - AuthController_login
  - ProfilesController_me
  - WalletsController_getAccountBalance
  - WalletsController_claimDailyGift
  - GameplaysController_create
  - GameplaysController_list
  - GameplaysController_byId
  - GameplaysController_claimReward
  - GameplaysController_cancel
  - LedgerController_list
  - NotificationsController_list
---

# Play a VersusGame game and claim the reward

Base URL: `https://api.versusgame.com`. All operations below require
`Authorization: Bearer <jwt>` from `POST /auth/login` (`AuthController_login`).

## 1. Establish the player context

- `GET /v1/profiles` (`ProfilesController_me`) — the authenticated `Profile` (`username`, `avatar`,
  `profileColor`).
- `GET /v1/wallets/account-balance` (`WalletsController_getAccountBalance`) — `WalletResponse`, with
  the player's `WalletLeague` and `dailySetCount`.
- `POST /v1/wallets/claim-daily-gift` (`WalletsController_claimDailyGift`) — returns
  `ClaimDailyGiftResponse`. Call once per day; a second call is not idempotent-protected, so read the
  response rather than retrying blindly.

Funds are typed (`FundType`) and every movement carries a reason (`FundsUpdatedReasons`).

## 2. Place the prediction

`POST /v1/gameplays` (`GameplaysController_create`) with `CreateGameplayDto`. Returns `201`.

- **`x-idempotency-key` is a REQUIRED header.** One key per intended play; reuse it verbatim on
  retry. A fresh key on retry spends the stake twice.
- The play names the `gameId`, the chosen `Side`, and a `TicketType`.
- Only open games accept plays — check `Game.lastJoinTime` and `GameStates` first
  (`GamesUserController_getStats` gives the public read).

## 3. Track it

- `GET /v1/gameplays` (`GameplaysController_list`) — `ListGameplays`, paginated with `limit`/`offset`.
- `GET /v1/gameplays/{id}` (`GameplaysController_byId`) — one `Gameplay` with its `GameplayStates`.

## 4. Cancel while still open

`DELETE /v1/gameplays/{id}` (`GameplaysController_cancel`) returns `204`. **This also requires
`x-idempotency-key`** — one of the few DELETE operations in the API that does.

## 5. Claim after settlement

Once the creator declares the result (`GamesCreatorController_declareGame`), the winning play becomes
claimable:

`POST /v1/gameplays/{id}/claim_reward` (`GameplaysController_claimReward`) returns `204` and
**requires `x-idempotency-key`**. This is the highest-consequence call in the flow — it moves funds.
Treat a network timeout as unknown, not as failure: retry with the same key, then verify with
`GameplaysController_byId` and the ledger.

## 6. Reconcile

- `GET /v1/ledger` (`LedgerController_list`) — `LedgerListEntity`, the authoritative record of fund
  movements.
- `GET /v1/notifications` (`NotificationsController_list`) and `GET /v1/notifications/count`
  (`NotificationsController_count`) — outcome notifications (`GameOutcomeNotification`,
  `GamePlayedNotification`, `LeaderboardPointsNotification`, `UserAchievementNotification`).
  **Notifications are polled — VersusGame publishes no webhook or event stream**, so there is nothing
  to subscribe to.
- `GET /v1/achievements` (`AchievementsController_list`) — unlocked achievements.

## Rules

- **Idempotency.** Three of the five operations in this flow require `x-idempotency-key`:
  `GameplaysController_create`, `GameplaysController_claimReward`, `GameplaysController_cancel`.
  Retention and replay semantics are not documented — verify state after any retry.
- **No events.** Poll; do not wait for a callback. `/v1/payments/callback/*` are inbound callbacks
  from Stripe and GCOW to VersusGame, not outbound webhooks to you.
- **Errors.** `{"message", "error", "statusCode"}`. See `errors/versusgame-problem-types.yml`.
