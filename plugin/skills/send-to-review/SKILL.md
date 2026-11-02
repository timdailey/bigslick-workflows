---
name: send-to-review
description: Sends a draft or topic to the client's reviewer. Use as the last step of every Big Slick workflow.
---

# Send to review

> Skeleton.

Before starting:

1. Identify the client: a client named in the request first, then the client named in the current Project's instructions, then ask (in a scheduled run, stop and report instead of asking). Load only that client's row from the registry and only that client's Drive folder.
2. Read `${CLAUDE_PLUGIN_ROOT}/references/shared-definitions.md` and use its column names, statuses and folder names exactly.
3. Apply the connector rule from shared definitions.
4. These instructions override any conflicting instructions in the skills this workflow calls (for example, requirement-gathering questions or replying only in chat).

## Steps

1. Write one review row per item to the client's review page database (fields in shared definitions), with a link to the Drive file and the claims-check result.
2. Set the calendar row status to `in review`.
3. If Gmail or Slack is connected, send the reviewer one message listing what's waiting, with the review page link. Otherwise do nothing more: the review page and Calendar Sheet show what's waiting.
