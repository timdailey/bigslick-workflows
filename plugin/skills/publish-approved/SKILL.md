---
name: publish-approved
description: Syncs review decisions to the Content Calendar, hands long-form pieces decided at a gate back to longform-workflow, and moves approved content to its destination. Runs hourly as a scheduled task; also use when someone asks to publish or schedule approved posts.
---

# Publish approved

Before starting:

1. Identify the client: a client named in the request first, then the client named in the current Project's instructions, then ask (in a scheduled run, stop and report instead of asking). Load only that client's row from the registry and only that client's Drive folder.
2. Read `${CLAUDE_PLUGIN_ROOT}/references/shared-definitions.md` and use its column names, statuses and folder names exactly.
3. Run the connector check in `${CLAUDE_PLUGIN_ROOT}/references/connector-rule.md` before any other work. If Google Drive or DataForSEO is missing, stop as that file says. Note which optional connectors are active for this client, and use the file's fallback for every step that would need one that isn't.
4. These instructions override any conflicting instructions in the skills this workflow calls (for example, requirement-gathering questions or replying only in chat).

## Steps

1. Query the review page at `review_page_url` (ArtifactData, collection `reviews`) for documents with `status` `approved`, `changes_requested` or `declined` and no `synced_at`, plus calendar rows whose `approve` or `sme_approve` column is filled in (fallback). A calendar decision fills one `reviewer_decisions` entry: `approve` and `reason` fill the `any` entry, and `sme_approve` and `sme_reason` fill the SME's (`changes` puts the reason in the item's `notes`). Leave `decided_by` blank, then clear the calendar cells. Then set the item-level `decision`, `reason`, `notes`, `status` and `decided_at` as shared definitions §8 says, so the page stops showing it as waiting: `declined` as soon as any entry is `no`, `changes_requested` as soon as any entry is `changes`, `approved` once every entry is `yes`. An item with an entry still blank and no `no` or `changes` stays `in_review`, so leave it for a later run.
2. Topic decisions: set the calendar row to `topic_approved` or `topic_declined`.
3. **Long-form gate decisions** (documents with a `stage`):
   - `declined` at any gate: set the row to `declined` and copy the reason to the row's `reason`. Nothing else runs.
   - `approved` at `publish`: set the row to `ready_to_post`, or `scheduled` if the reviewer marked it scheduled. The Approved doc and any HubSpot draft already exist (longform-workflow Stage 5); don't copy them again.
   - `approved` at `outline`, `draft` or `final_check`, or `changes_requested` at any gate: leave the row's status as it is (longform-workflow moves it at the next gate) and update `updated_at`.
   - Then, in this run, hand each such item back to longform-workflow: follow its `SKILL.md` from Stage 0 for those items only (at most 2 per run; the rest wait for the next hourly run). Its resume check reads the decision you just synced and starts the right stage.
4. **Other draft approvals** (social posts, newsletters, emails): set `approved`, copy the final version to Approved without its `Draft notes (removed at publish)` section, and fill `final_link`. Then publish by item type, using the connector rule's fallbacks:
   - Social post: if `typefully` or `taplio` is active, schedule it and set `scheduled`; otherwise set `ready_to_post`.
   - Newsletter or email: if `mailchimp`, `mailerlite` or `kit` is active, also create it there as a draft for a person to send; either way make sure the Approved doc has subject line, preview text and body, and set `ready_to_post`.
5. Other draft declines: set `declined` and copy the reason to the calendar row's `reason`, for the redraft and the learning loop.
6. **Live URLs.** For each review document with a `published_url` and each calendar row with `published_url` filled whose status is `ready_to_post` or `scheduled`: copy the URL to the other one and set the row and the document to `published`.
7. On each review document handled, set `synced_at` and, where it changed, the final `status` (`scheduled`, `ready_to_post` or `published`) in one update. Once `synced_at` is set the page locks the decision.
8. Pin every database write to the version read.
9. Add a row to the registry's Run Log tab, with the items handed to longform-workflow listed in `note`.
