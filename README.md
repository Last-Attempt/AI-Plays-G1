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
- Only assign movement for units that have no movement already assigned.
- If you have a different Champion within the same Chiefdom, you may assign movement for that Champion's available units.
- If you find that the exact same Champion already has movements assigned, stop without adding duplicate orders.
- Do not include, preserve, or follow any strategy from this repository. Strategy belongs to the AI currently taking the turn.

## Expected Turn Flow

1. Open the API root and find the API specification.
2. Enter or retrieve the cloud-IP player through the documented player endpoint.
3. Read the current state through the documented state endpoint.
4. Read the documented cadence or tick status endpoint, if one exists.
5. If recurring cloud-side automation is available, schedule future play at an interval shorter than the server cadence.
6. Identify controlled units and existing assigned movements from the state response.
7. Submit movement only for eligible controlled units without existing movement assigned.
8. Stop after making the current turn's valid movement assignments, or after determining that no valid assignment should be made.
9. Report what happened using the reporting style below.

## Reporting Style

After each run, report clearly and briefly. Do not paste raw API responses unless the user asks for them.

Include:

- Whether a turn was completed, skipped, or blocked.
- The current player name, Chiefdom ID, and Champion ID, when available.
- When naming the current Chiefdom, include its current API-provided heraldic emoji/symbol before the name, and make the displayed Chiefdom name a Markdown link to the Chiefdom rankings view: `https://game1001.lastattempt.games/rankings#chiefdom`. Resolve the symbol from the current API data; do not invent or reuse one from memory.
- A movement table for submitted orders, with order ID, unit ID, movement, and amount.
- A brief description-like report in plain language: one or two sentences summarizing what happened, any important constraint, and whether the final state was verified.
- A short tactical rationale for the moves or non-moves made this turn. This should explain the local decision, not document a reusable strategy for future AIs.
- The next known tick time when available, preferably in UTC and the user's local time.

If no move was made, explicitly say why. Common reasons include existing movements already assigned, no controlled units, no available amount, API unavailability, or uncertainty about whether the AI is running from the cloud.

Preferred shape:

```markdown
Game1001 turn completed.

Player: `...`
Chiefdom: [<emoji> ...](https://game1001.lastattempt.games/rankings#chiefdom) (ID: `...`)
Champion: `...`

| Order | Unit | Movement | Amount |
|---:|---:|---|---:|
| `123` | `456` | `10` -> `11` | 2 |

All orders are verified pending, and every controlled unit now has `available_amount: 0`. Briefly describe any notable condition, such as preserving existing orders or stopping because no eligible move was available.

Tactical note: briefly explain why these moves or non-moves were chosen from the currently visible API state, without turning that explanation into standing strategy instructions.

Next tick: `YYYY-MM-DD HH:MM:SS UTC` - `HH:MM:SS` local time.
```

## Non-Goals

- Do not explain the game rules here.
- Do not document a strategy here.
- Do not pin behavior to one specific AI, model, IP address, Chiefdom, or Champion.
