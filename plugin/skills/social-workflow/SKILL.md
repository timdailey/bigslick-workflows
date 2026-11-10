---
name: social-workflow
description: Writes social posts for a client in each author's voice. LinkedIn first, then X and communities. Use when someone asks for LinkedIn posts, social posts or a thread, or when a scheduled social run fires.
---

# Social workflow

> Skeleton. One workflow, with a rules section per platform.

Before starting:

1. Identify the client: a client named in the request first, then the client named in the current Project's instructions, then ask (in a scheduled run, stop and report instead of asking). Load only that client's row from the registry and only that client's Drive folder.
2. Read `${CLAUDE_PLUGIN_ROOT}/references/shared-definitions.md` and use its column names, statuses and folder names exactly.
3. Run the connector check in `${CLAUDE_PLUGIN_ROOT}/references/connector-rule.md` before any other work. If Google Drive or DataForSEO is missing, stop as that file says. Note which optional connectors are active for this client, and use the file's fallback for every step that would need one that isn't.
4. These instructions override any conflicting instructions in the skills this workflow calls (for example, requirement-gathering questions or replying only in chat).

## Inputs

A calendar row, a topic typed into the chat, or a repurposing request (source doc link).

## Steps

1. Load the client with client-context, asking for these Brand Context sections only: `Voices` (keep only the author's `Voice:` section), `Style rules`, `Audience and ICP`, `Products and positioning`, `Content pillars` and `Claims library`.
2. Draft the post using the platform rules below.
3. Run claims-check.
4. For a LinkedIn post, run sme-content-review right after claims-check. Apply its `humanize` and `channel` fixes, and leave the rest for the reviewer.
5. Save to Drafts using the file name format in shared definitions, link it from the calendar row and set status `drafted`.
6. Call send-to-review with the claims-check flags and any sme-content-review result.
7. Add a row to the registry's Run Log tab.

## Platform rules

### LinkedIn

TODO: adapt from LinkedIn Content Engine (hooks, length, formatting, no client-specific content).

### X (V1)

TODO.

### Communities (V1)

TODO.
