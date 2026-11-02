---
name: publish-approved
description: Moves approved content to its destination. Runs hourly as a scheduled task; also use when someone asks to publish or schedule approved posts.
---

# Publish approved

> Skeleton.

Before starting:

1. Identify the client: a client named in the request first, then the client named in the current Project's instructions, then ask (in a scheduled run, stop and report instead of asking). Load only that client's row from the registry and only that client's Drive folder.
2. Read `${CLAUDE_PLUGIN_ROOT}/references/shared-definitions.md` and use its column names, statuses and folder names exactly.
3. Apply the connector rule from shared definitions.
4. These instructions override any conflicting instructions in the skills this workflow calls (for example, requirement-gathering questions or replying only in chat).

## Steps

1. Read decisions from the review page database, plus any `Yes` in the Calendar Sheet's approve column (fallback).
2. For each approval: copy the final version to Approved. If a social scheduler is connected and the item is a social post, schedule it and set status `scheduled`; otherwise set `ready to post`.
3. For each decline: set status `declined` and keep the reason in notes for the redraft and the learning loop.
4. Pin every database write to the version read.
5. Add a run-log line.
