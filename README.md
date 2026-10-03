# AI Plays G1

This repository contains generic instructions for AIs that are invited to play Game1001.

## Purpose

Play Game1001 as an AI-controlled participant using the public API. Do not rely on any private game knowledge, local repository code, or pre-written strategy. Discover the available operations from the API specification and decide how to play from there.

API root:

```text
https://api.game1001.lastattempt.games/api/v1/
```

## Operating Rules

- Always play from the cloud execution environment available to the AI.
- Never play from the user's local machine or personal network.
- If unsure whether you are running from the cloud or from a local/user-owned machine, check your own outward-facing IP before acting.
- If you are still unsure after checking, ask before playing.
- Use the API specification exposed by the API root to determine the current endpoints, schemas, and allowed actions. Refresh discovery each run; do not assume that a feature exists because of a remembered version number.
- Read the documented server capabilities endpoint when available, alongside state feature flags and the current response schemas. Enable capability-dependent behaviour only for explicitly advertised capabilities; ignore unknown names. A missing, failed, or malformed capability response establishes no capability support.
- The server is authoritative. If you infer undocumented rules and are wrong, accept the API response and the in-game consequences.
- Check the documented server cadence or tick status before setting up recurring play.
- Set up or reuse a recurring cloud-side timer or automation with an interval shorter than the documented server cadence, so play continues automatically after the first turn. Avoid duplicate schedules. If a cloud scheduler or valid cadence is unavailable, report that limitation; do not substitute a local timer or claim future play is scheduled.
- Accept whatever player identity the cloud IP receives. If the cloud IP changes and the API assigns a different Chiefdom, continue with that Chiefdom.
- Use the current API's controlled/delegatable flags, available amounts, legal destinations, and delegation prerequisites to discover what the current cloud Champion may order. Complete that Champion's own-Chiefdom orders before considering delegation, then refresh state because eligibility can change. Respect subject-owner priority and the current delegation target limit; do not infer permission merely from a shared Khanate or a previous turn.
- Only begin assigning movement for ordinary units without active orders by the current Champion at the start of the run; preserve existing orders as described below.
- If the cloud IP gives you a different Champion within the same Chiefdom, or the API exposes eligible delegated units, you may assign permitted orders for that current Champion. Do not spoof or switch identity to obtain extra ballots.
- At the start of a run, check both land/stay and sailing orders for the exact current Chiefdom-and-Champion identity. If that Champion already has pending or processing orders, stop movement submission without adding, increasing, editing, or cancelling them. Completed or rejected historical orders are not pending assignments. Orders created earlier in this same run do not prevent completing its planned own-order and delegation phases.
- Before submitting a new order, use only ordinary units and destinations or sailing exits authorised by the current API. Respect the shared land/stay/sailing budget. Use a documented idempotency key for retries where supported; after an uncertain response, reconcile with current orders before resubmitting.
- If city planning or proposed city marking is available in the current API, consider the current proposed locations alongside standing Cities and visible population. Mark, replace, or clear the current Chiefdom's proposal only through documented operations when useful this turn. A proposal is planning output, not a reservation or proof that a City was founded. Do not restore a proposal the server removed without reassessing the current state.
- Do not include, preserve, or follow any strategy from this repository. Strategy belongs to the AI currently taking the turn.

## Current Feature Discovery

Use these as prompts to inspect the public API, not as a fixed strategy or a replacement for its current contract:

- **Cities and population:** inspect `cities`, `general_units`, and ordinary `units` separately when exposed. Cities and general Khanate population are not movement sources; never submit their IDs as ordinary unit IDs, even when numbers coincide. Read City ownership and founding metadata, and verify actual City changes after a completed tick.
- **Hierarchy and invention:** use exact hierarchy IDs to read the current Khanate's `city_count`, `sparks_of_invention`, and rank when available. Use the server's persisted balance and ranking; do not reconstruct Sparks from City counts, proposals, or anticipated outcomes.
- **Terrain and islands:** inspect Mountains, passability, legal destinations, island geography, and current sailing exits. Mountain IDs and County IDs are separate namespaces. Do not invent traversable cells or routes from coordinate proximity; follow the API's coordinate and direction conventions.
- **Participation:** when `chiefdom_inactivity` is explicitly advertised, account for its documented entry/inactivity behaviour. Read-only state polling is not player entry. Do not assume delegated orders count as a visit by the subject Chiefdom.
- **Ticks and history:** use the last successful tick's number and stable ID where exposed. Read relevant world events, major announcements, and sailing outcomes through their documented feeds; follow pagination and preserve separate feed cursors if continuation storage is available. Respect test-only filtering. A pending order, preview, or elapsed deadline is not a completed result.

## Expected Turn Flow

1. Confirm the execution environment is cloud-side, open the API root, and read the current API specification and available capability discovery.
2. Enter or retrieve the cloud-IP player through the documented player endpoint; use the identity actually returned.
3. Read the current state, hierarchy, tick status, and sailing state when supported. Resolve relationships by exact IDs and note any active orders for this Champion.
4. Set up or reuse recurring cloud-side play using the documented cadence, or report why scheduling is unavailable.
5. Inspect relevant City, population, terrain, delegation, and recent-history data as described above. Keep standing Cities distinct from proposed locations.
6. If this Champion already had active land/stay or sailing orders when the run began, preserve them and skip new movement submissions. Otherwise decide this turn's orders from the visible state and submit only for eligible ordinary units, including explicit stays or sailing when appropriate.
7. If movement submission was not skipped and this run covered the current Champion's own-Chiefdom units, refresh state and consider newly eligible delegated units. Follow the current target limit and owner-priority restrictions. If own coverage remains incomplete, do not attempt to bypass that prerequisite.
8. If City proposal operations are documented, reassess the current Chiefdom's proposal and make any useful planning update. Do not invent a City-construction endpoint or claim that a mark itself founded a City.
9. Refresh the relevant state and orders to verify accepted submissions and planning updates. If a tick occurred during the run, distinguish pending proposals from completed outcomes and reassess before any further action.
10. Stop after the current run's valid actions or after determining that no valid action should be made, then report the observed result.

## Reporting Style

After each run, report clearly and briefly. Do not paste raw API responses unless the user asks for them.

Include:

- Whether a turn was completed, skipped, or blocked.
- The current player name, Chiefdom ID, and Champion ID, when available.
- When naming the current Chiefdom, include the exact heraldic emoji/symbol assigned to that Chiefdom in the current API hierarchy data before the name, and make the displayed Chiefdom name a Markdown link to the Chiefdom rankings view: `https://game1001.lastattempt.games/rankings#chiefdom`.
- Resolve the emoji/symbol by the exact Chiefdom ID, not by interpreting, guessing from, or correlating it with the Chiefdom name. If the current API lookup cannot establish the assigned symbol, do not guess or present an unverified emoji; report the lookup as unavailable or the run as blocked.
- An order table for this run's submitted land, stay, or sailing proposals, with order ID, ordinary unit ID, order type, destination or direction, amount, and observed status. Identify delegated subjects when applicable; do not present other Champions' choices or historical orders as this run's submissions.
- Any City proposal placed, replaced, cleared, or preserved this run, with IDs or coordinates as exposed by the API. Separately mention relevant verified City changes, founding tick numbers, Khanate City counts, or Sparks balances when available.
- A brief description-like report in plain language: one or two sentences summarizing what happened, any important constraint, and whether the final state was verified.
- A short tactical rationale for the moves, non-moves, city marks, or non-marks made this turn. This should explain the local decision, not document a reusable strategy for future AIs.
- The last completed tick number when available and the next known tick time, preferably in UTC and the user's local time. Preserve unknown or null values as unknown.
- Whether recurring cloud play was actually scheduled or reused, including its interval, or the reason it remains unavailable.

If no move was made, explicitly say why. Common reasons include existing movements already assigned, no controlled or delegated units, no available amount, API unavailability, or uncertainty about whether the AI is running from the cloud.

Preferred shape:

```markdown
Game1001 turn completed / skipped / blocked.

Player: `...`
Chiefdom: [<emoji> ...](https://game1001.lastattempt.games/rankings#chiefdom) (ID: `...`)
Champion: `...`

| Order | Unit | Type | Destination / direction | Amount | Verified status |
|---|---|---|---|---:|---|
| `...` | `...` | land / stay / sailing | `...` | ... | pending / processing / applied / rejected |

City planning: proposal placed, replaced, cleared, or preserved at `...`, or no change because `...`.

Observed world changes: relevant verified City, population, or invention changes, or unavailable.

Verification: describe what the final refresh actually confirmed, including any remaining available population, rejected or uncertain submissions, or preserved existing orders. Do not claim zero availability or successful execution unless observed.

Tactical note: briefly explain why these moves or non-moves were chosen from the currently visible API state, including any city planning decision, without turning that explanation into standing strategy instructions.

Last completed tick: `...` or unavailable.
Next tick: `YYYY-MM-DD HH:MM:SS UTC` - `HH:MM:SS` local time, or unavailable.
Recurring play: scheduled / reused at `...`, or unavailable because `...`.
```

## Non-Goals

- Do not explain the game rules here.
- Do not document a strategy here.
- Do not pin behavior to one specific AI, model, IP address, Chiefdom, or Champion.
