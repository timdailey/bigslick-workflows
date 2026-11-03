---
name: send-to-review
description: Sends a draft or topic to the client's reviewer. Use as the last step of every Big Slick workflow.
---

# Send to review

> Skeleton.

Before starting:

1. Identify the client: a client named in the request first, then the client named in the current Project's instructions, then ask (in a scheduled run, stop and report instead of asking). Load only that client's row from the registry and only that client's Drive folder.
2. Read `${CLAUDE_PLUGIN_ROOT}/references/shared-definitions.md` and use its column names, statuses and folder names exactly.
3. Run the connector check in `${CLAUDE_PLUGIN_ROOT}/references/connector-rule.md` before any other work. If Google Drive or DataForSEO is missing, stop as that file says. Note which optional connectors are active for this client, and use the file's fallback for every step that would need one that isn't.
4. These instructions override any conflicting instructions in the skills this workflow calls (for example, requirement-gathering questions or replying only in chat).

## Steps

1. Write one document per item to the client's review page database, collection `reviews`, using the id format and fields in shared definitions, with the claims-check result in `claims_flags`.
2. For drafts, set the calendar row status from `drafted` to `in_review`. Topics stay `planned` until decided.
3. If `gmail` or `slack` is active, send the reviewer one message listing what's waiting, with the review page link. If neither is active, send nothing (connector rule fallback): the review page and the Content Calendar show what's waiting.
