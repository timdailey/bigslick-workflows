---
name: longform-workflow
description: Writes long-form content for a client: blog posts and guides (case studies and gated assets in V1). Use when someone asks to write a blog post, article or guide, or when a scheduled run picks up the next approved brief.
---

# Long-form workflow

> Skeleton.

Before starting:

1. Identify the client: a client named in the request first, then the client named in the current Project's instructions, then ask (in a scheduled run, stop and report instead of asking). Load only that client's row from the registry and only that client's Drive folder.
2. Read `${CLAUDE_PLUGIN_ROOT}/references/shared-definitions.md` and use its column names, statuses and folder names exactly.
3. Run the connector check in `${CLAUDE_PLUGIN_ROOT}/references/connector-rule.md` before any other work. If Google Drive or DataForSEO is missing, stop as that file says. Note which optional connectors are active for this client, and use the file's fallback for every step that would need one that isn't.
4. These instructions override any conflicting instructions in the skills this workflow calls (for example, requirement-gathering questions or replying only in chat).

## Inputs

- content type: blog post or guide (MVP); case study, gated asset (V1)
- topic: the next `topic_approved` calendar row with a brief (earliest `publish_date` first), or a topic typed into the chat

## Steps

1. Load the brief, or create one with seo-content-brief if the topic came from the chat, using seo-dataforseo for keyword and SERP data within the client's `seo_budget_per_run`.
2. Draft with write-blog using the content type's template and the brand voice.
3. Run editorial-qa, brand-review and claims-check. Fix what they find.
4. Run sme-content-review right after claims-check. Apply its `humanize` and `channel` fixes, and leave the rest for the reviewer.
5. Save the draft to Drafts using the file name format in shared definitions, link it from the calendar row (add a row if the topic came from the chat) and set status `drafted`.
6. Call send-to-review with the claims-check flags and the sme-content-review result.
7. Add a row to the registry's Run Log tab.

## Content type templates

TODO: one short section per content type (structure, length, required checks).
