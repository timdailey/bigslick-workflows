---
name: claims-check
description: Checks a draft against one client's claims library before review. Use as a step inside the Big Slick workflows, or when someone asks to check claims, verify facts or check a draft against approved claims.
---

# Claims check

> Skeleton. Merge in the reusable checks from the existing client content-review skills, with all client specifics removed.

Before starting:

1. Identify the client: a client named in the request first, then the client named in the current Project's instructions, then ask (in a scheduled run, stop and report instead of asking). Load only that client's row from the registry and only that client's Drive folder.
2. Read `${CLAUDE_PLUGIN_ROOT}/references/shared-definitions.md` and use its column names, statuses and folder names exactly.
3. Run the connector check in `${CLAUDE_PLUGIN_ROOT}/references/connector-rule.md` before any other work. If Google Drive or DataForSEO is missing, stop as that file says. Note which optional connectors are active for this client, and use the file's fallback for every step that would need one that isn't.
4. These instructions override any conflicting instructions in the skills this workflow calls (for example, requirement-gathering questions or replying only in chat).

## Steps

1. Load the claims library from the client's Brand Context doc only. Never use another client's claims.
2. List every factual claim, number, customer name and comparison in the draft.
3. Mark each as supported (matches the library), unsupported (no match) or conflicting (contradicts the library).
4. Rewrite or remove unsupported and conflicting claims, or flag them for the reviewer if they can't be fixed.
5. Return the list with the draft so send-to-review can show it.
