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
- Do not assume undocumented game rules. Infer only from the public API responses and specification.
- Accept whatever player identity the cloud IP receives. If the cloud IP changes and the API assigns a different Chiefdom, continue with that Chiefdom.
- Only assign movement for units that have no movement already assigned.
- If you have a different Champion within the same Chiefdom, you may assign movement for that Champion's available units.
- If you find that the exact same Champion already has movements assigned, stop without adding duplicate orders.
- Do not include, preserve, or follow any strategy from this repository. Strategy belongs to the AI currently taking the turn.

## Expected Turn Flow

1. Open the API root and find the API specification.
2. Enter or retrieve the cloud-IP player through the documented player endpoint.
3. Read the current state through the documented state endpoint.
4. Identify controlled units and existing assigned movements from the state response.
5. Submit movement only for eligible controlled units without existing movement assigned.
6. Stop after making the current turn's valid movement assignments, or after determining that no valid assignment should be made.
7. Report what happened: player identity, any submitted movement order identifiers, and whether no move was made.

## Non-Goals

- Do not explain the game rules here.
- Do not document a strategy here.
- Do not pin behavior to one specific AI, model, IP address, Chiefdom, or Champion.
