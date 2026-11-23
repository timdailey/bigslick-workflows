---
name: send-to-review
description: Sends a topic, a draft, or a long-form piece at one of its four gates (outline, draft, final check, publish) to the client's reviewer, and emails them. Use as the last step of every Big Slick workflow and at every long-form gate.
---

# Send to review

Before starting:

1. Identify the client: a client named in the request first, then the client named in the current Project's instructions, then ask (in a scheduled run, stop and report instead of asking). Load only that client's row from the registry and only that client's Drive folder.
2. Read `${CLAUDE_PLUGIN_ROOT}/references/shared-definitions.md` and use its column names, statuses and folder names exactly.
3. Run the connector check in `${CLAUDE_PLUGIN_ROOT}/references/connector-rule.md` before any other work. If Google Drive or DataForSEO is missing, stop as that file says. Note which optional connectors are active for this client, and use the file's fallback for every step that would need one that isn't.
4. These instructions override any conflicting instructions in the skills this workflow calls (for example, requirement-gathering questions or replying only in chat).

## Steps

1. Write one document per item to the review page at the registry's `review_page_url` (ArtifactData `set`, collection `reviews`), using the id format and fields in shared definitions §8, with the calendar row's `about`, `purpose` and `funnel_stage`, the claims-check result in `claims_flags`, the sme-content-review result in `sme_verdict` and `sme_flags`, and `status` = `in_review`. Leave out `sme_verdict` and `sme_flags` if sme-content-review didn't run. Write `reviewer_decisions` as in step 2. Leave out `decision`, `reason`, `notes`, `decided_by`, `decided_at` and `synced_at`. A new version is a new document (`<item_id>-v<version>`), so earlier versions stay on the page as history. If a document with the same id already exists, read it and pin the write to its version; never overwrite one that has a `decision`. Write several items in one batch.
   - **Long-form gates** (the caller passes a `stage`): also write `stage`, `summary`, `scores`, `humanize_iterations`, `exit_reason`, `findings`, `link_gate` and `links`, whichever the stage has (§8 says which). At `final_check`, copy forward every finding from the item's previous `final_check` document that has `applied` = `true`, so the page can hide it.
2. Build `reviewer_decisions`, loading only the `SME profiles` section of Brand Context with bigslick-client. Always add one entry with `reviewer` = `any`. Add a second entry with the SME's review email, from their heading in `SME profiles`:
   - **Long-form gates:** at `outline` and `final_check` when the item has an SME (the caller passes `sme_name`). At `draft` only if `sme_flags` has a Blocker, or a Major with `technical` as its check. Never at `publish`.
   - **Everything else:** if the piece's `author` has a heading in `SME profiles`, or sme-content-review named an `sme_name` and `sme_flags` has a Blocker or a Major with `technical` as its check.
   If the SME has no review email in their profile, write only the `any` entry, and add a Major line to `sme_flags` saying the SME couldn't be listed. Leave each entry's `decision`, `reason`, `decided_by` and `decided_at` blank.
3. Set the calendar row status:
   - Long-form gates: `outline_review`, `draft_review`, `final_review` or `publish_review` for the stage, from `topic_approved` or the previous gate's status (or the same status, for a re-run).
   - Other drafts: from `drafted` to `in_review`.
   - Topics stay `planned` until decided.
4. Alert the reviewer, if `gmail` or `slack` is active. Send automatically, one message per item, to everyone in the registry `reviewers`, plus the SME when step 2 listed one.
   - **Long-form gates:** subject `[<client_name>] <Stage> ready: <title>`, where `<Stage>` is `Outline`, `Draft`, `Final Check` or `Publish`. Body: what's waiting and its `about` line, what to decide (approve, request changes or stop; at Final Check, tick the findings to apply), the scores or findings counts from `summary`, the review page link and the doc link. At Publish, add the Approved doc link and the publish checklist.
   - **Topics and other drafts:** one message listing what's waiting (for each, the title and its `about` line), with the review page link.
   If neither connector is active, send nothing (connector rule fallback): the review page and the Content Calendar show what's waiting.
5. Return the review page link and the doc link as plain URLs, so the calling workflow can list them in the chat (the Gmail connector rewrites links in a sent email as redirects).
