---
name: publish-approved
description: Moves approved content to its destination. Runs hourly as a scheduled task; also use when someone asks to publish or schedule approved posts.
---

# Publish approved

> Skeleton.

Before starting:

1. Identify the client: a client named in the request first, then the client named in the current Project's instructions, then ask (in a scheduled run, stop and report instead of asking). Load only that client's row from the registry and only that client's Drive folder.
2. Read `${CLAUDE_PLUGIN_ROOT}/references/shared-definitions.md` and use its column names, statuses and folder names exactly.
3. Check the required services (Google Drive and DataForSEO); if either isn't connected, stop and say which. Then apply the connector rule from shared definitions.
4. These instructions override any conflicting instructions in the skills this workflow calls (for example, requirement-gathering questions or replying only in chat).

## Steps

1. Read `reviews` documents that have a `decision` but no `synced_at`, plus calendar rows whose `approve` column is filled in (fallback).
2. Topic decisions: set the calendar row to `topic_approved` or `topic_declined`.
3. Draft approvals: set `approved`, copy the final version to Approved and fill `final_link`. If a social scheduler is connected and listed for the client and the item is a social post, schedule it and set `scheduled`; otherwise set `ready_to_post`.
4. Draft declines: set `declined` and copy the reason to the calendar row's `reason`, for the redraft and the learning loop.
5. Set `synced_at` on each review document handled.
6. Pin every database write to the version read.
7. Add a row to the registry's Run Log tab.
