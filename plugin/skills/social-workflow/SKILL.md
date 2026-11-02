---
name: social-workflow
description: Writes social posts for a client in each author's voice. LinkedIn first, then X and communities. Use when someone asks for LinkedIn posts, social posts or a thread, or when a scheduled social run fires.
---

# Social workflow

> Skeleton. One workflow, with a rules section per platform.

Before starting:

1. Identify the client: a client named in the request first, then the client named in the current Project's instructions, then ask (in a scheduled run, stop and report instead of asking). Load only that client's row from the registry and only that client's Drive folder.
2. Read `${CLAUDE_PLUGIN_ROOT}/references/shared-definitions.md` and use its column names, statuses and folder names exactly.
3. Apply the connector rule from shared definitions.
4. These instructions override any conflicting instructions in the skills this workflow calls (for example, requirement-gathering questions or replying only in chat).

## Inputs

A calendar row, a topic typed into the chat, or a repurposing request (source doc link).

## Steps

1. Load the author's voice section from Brand Context.
2. Draft the post using the platform rules below.
3. Run claims-check.
4. Save to Drafts, link from the calendar row, set status `in review`.
5. Call send-to-review.
6. Add a run-log line.

## Platform rules

### LinkedIn

TODO: adapt from LinkedIn Content Engine (hooks, length, formatting, no client-specific content).

### X (V1)

TODO.

### Communities (V1)

TODO.
