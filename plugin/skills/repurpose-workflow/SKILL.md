---
name: repurpose-workflow
description: Turns a published long-form piece into 3-5 social posts, a newsletter section and an email blurb. Use when someone asks to repurpose, atomize or turn an article into social posts.
---

# Repurpose workflow

> Skeleton.

Before starting:

1. Identify the client: a client named in the request first, then the client named in the current Project's instructions, then ask (in a scheduled run, stop and report instead of asking). Load only that client's row from the registry and only that client's Drive folder.
2. Read `${CLAUDE_PLUGIN_ROOT}/references/shared-definitions.md` and use its column names, statuses and folder names exactly.
3. Run the connector check in `${CLAUDE_PLUGIN_ROOT}/references/connector-rule.md` before any other work. If Google Drive or DataForSEO is missing, stop as that file says. Note which optional connectors are active for this client, and use the file's fallback for every step that would need one that isn't.
4. These instructions override any conflicting instructions in the skills this workflow calls (for example, requirement-gathering questions or replying only in chat).

## Steps

1. Read the source piece from Approved (or a link given in the chat).
2. Use content-repurposing to plan the derived pieces.
3. Hand each social post to social-workflow so it gets the author's voice and platform rules.
4. Save the newsletter section and email blurb to Drafts.
5. Add calendar rows for each derived piece, then call send-to-review.
6. Add a row to the registry's Run Log tab.
