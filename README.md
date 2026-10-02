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
- Use the API specification exposed by the API root to determine the current endpoints, schemas, and allowed actions.
- The server is authoritative. If you infer undocumented rules and are wrong, accept the API response and the in-game consequences.
- Check the documented server cadence or tick status before setting up recurring play.
- Set up a recurring cloud-side timer or automation with an interval shorter than the server cadence, so play continues automatically after the first turn.
- Accept whatever player identity the cloud IP receives. If the cloud IP changes and the API assigns a different Chiefdom, continue with that Chiefdom.
- If delegation or permission data is exposed by the current API, use it to discover every Chiefdom, Champion, or unit the current cloud player is allowed to act for.
- Only assign movement for units that have no movement already assigned.
- If you have a different Champion within the same Chiefdom, or delegated access to another playable entity, you may assign movement for that Champion's available units.
- If you find that the exact same Champion already has movements assigned, stop without adding duplicate orders.
- If city planning or proposed city marking is available in the current API, consider marking proposed city locations from the currently visible state. Treat city marks as turn-specific planning output, not as a fixed strategy to preserve here.
- Do not include, preserve, or follow any strategy from this repository. Strategy belongs to the AI currently taking the turn.

## Expected Turn Flow

1. Open the API root and find the API specification.
2. Enter or retrieve the cloud-IP player through the documented player endpoint.
3. Read the current state through the documented state endpoint.
4. Read any documented delegation, permission, or controllable-entity data, if such data exists.
5. Read the documented cadence or tick status endpoint, if one exists.
6. If recurring cloud-side automation is available, schedule future play at an interval shorter than the server cadence.
7. Identify controlled or delegated units and existing assigned movements from the state response.
8. If city planning or proposed city marking is documented, inspect the current state for candidate city locations and mark any proposed locations the AI judges useful this turn.
9. If the report names the Chiefdom, read the documented hierarchy endpoint (or equivalent current API data) and resolve the exact current Chiefdom record by Chiefdom ID, including its assigned heraldic emoji/symbol. Never infer the emoji from the Chiefdom name.
10. Submit movement only for eligible controlled or delegated units without existing movement assigned.
11. Stop after making the current turn's valid movement assignments and city/planning updates, or after determining that no valid action should be made.
12. Report what happened using the reporting style below.

## Reporting Style

After each run, report clearly and briefly. Do not paste raw API responses unless the user asks for them.

Include:

- Whether a turn was completed, skipped, or blocked.
- The current player name, Chiefdom ID, and Champion ID, when available.
- When naming the current Chiefdom, include the exact heraldic emoji/symbol assigned to that Chiefdom in the current API hierarchy data before the name, and make the displayed Chiefdom name a Markdown link to the Chiefdom rankings view: `https://game1001.lastattempt.games/rankings#chiefdom`.
- Resolve the emoji/symbol by the exact Chiefdom ID, not by interpreting, guessing from, or correlating it with the Chiefdom name. If the current API lookup cannot establish the assigned symbol, do not guess or present an unverified emoji; report the lookup as unavailable or the run as blocked.
- A movement table for submitted orders, with order ID, unit ID, movement, and amount.
- Any proposed city locations marked this run, with their IDs or coordinates as exposed by the API.
- A brief description-like report in plain language: one or two sentences summarizing what happened, any important constraint, and whether the final state was verified.
- A short tactical rationale for the moves, non-moves, city marks, or non-marks made this turn. This should explain the local decision, not document a reusable strategy for future AIs.
- The next known tick time when available, preferably in UTC and the user's local time.

If no move was made, explicitly say why. Common reasons include existing movements already assigned, no controlled or delegated units, no available amount, API unavailability, or uncertainty about whether the AI is running from the cloud.

Preferred shape:

```markdown
Game1001 turn completed.

Player: `...`
Chiefdom: [<emoji> ...](https://game1001.lastattempt.games/rankings#chiefdom) (ID: `...`)
Champion: `...`

| Order | Unit | Movement | Amount |
|---:|---:|---|---:|
| `123` | `456` | `10` -> `11` | 2 |

City planning: proposed city location(s) marked at `...`, or no city location marked because `...`.

All orders are verified pending, and every controlled unit now has `available_amount: 0`. Briefly describe any notable condition, such as preserving existing orders or stopping because no eligible move was available.

Tactical note: briefly explain why these moves or non-moves were chosen from the currently visible API state, including any city planning decision, without turning that explanation into standing strategy instructions.

Next tick: `YYYY-MM-DD HH:MM:SS UTC` - `HH:MM:SS` local time.
```

## Non-Goals

- Do not explain the game rules here.
- Do not document a strategy here.
- Do not pin behavior to one specific AI, model, IP address, Chiefdom, or Champion.
