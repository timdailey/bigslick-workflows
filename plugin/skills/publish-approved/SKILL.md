---
name: publish-approved
description: Moves approved content to its destination. Runs hourly as a scheduled task; also use when someone asks to publish or schedule approved posts.
---

# Publish approved

> Skeleton.

Before starting:

1. Identify the client: a client named in the request first, then the client named in the current Project's instructions, then ask (in a scheduled run, stop and report instead of asking). Load only that client's row from the registry and only that client's Drive folder.
2. Read `${CLAUDE_PLUGIN_ROOT}/references/shared-definitions.md` and use its column names, statuses and folder names exactly.
3. Run the connector check in `${CLAUDE_PLUGIN_ROOT}/references/connector-rule.md` before any other work. If Google Drive or DataForSEO is missing, stop as that file says. Note which optional connectors are active for this client, and use the file's fallback for every step that would need one that isn't.
4. These instructions override any conflicting instructions in the skills this workflow calls (for example, requirement-gathering questions or replying only in chat).

## Steps

1. Query the review page at `review_page_url` (ArtifactData, collection `reviews`) for documents with `status` `approved` or `declined` and no `synced_at`, plus calendar rows whose `approve` or `sme_approve` column is filled in (fallback). A calendar decision fills one `reviewer_decisions` entry: `approve` and `reason` fill the `any` entry, and `sme_approve` and `sme_reason` fill the SME's. Leave `decided_by` blank. Then set the item-level `decision`, `reason`, `status` and `decided_at` as shared definitions §8 says, so the page stops showing it as waiting: `declined` as soon as any entry is `no`, `approved` once every entry is `yes`. An item with an entry still blank stays `in_review`, so leave it for a later run.
2. Topic decisions: set the calendar row to `topic_approved` or `topic_declined`.
3. Draft approvals: set `approved`, copy the final version to Approved and fill `final_link`. Then publish by item type, using the connector rule's fallbacks:
   - Social post: if `typefully` or `taplio` is active, schedule it and set `scheduled`; otherwise set `ready_to_post`.
   - Blog post or guide: if `hubspot` is active, also create it as a draft post in HubSpot for a person to publish; either way make sure the Approved doc has title, slug, meta description and body, and set `ready_to_post`.
   - Newsletter or email: if `mailchimp`, `mailerlite` or `kit` is active, also create it there as a draft for a person to send; either way make sure the Approved doc has subject line, preview text and body, and set `ready_to_post`.
4. Draft declines: set `declined` and copy the reason to the calendar row's `reason`, for the redraft and the learning loop.
5. On each review document handled, set `synced_at` and, for drafts, the final `status` (`scheduled` or `ready_to_post`) in one update. Once `synced_at` is set the page locks the decision.
6. Pin every database write to the version read.
7. Add a row to the registry's Run Log tab.
