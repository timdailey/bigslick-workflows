---
name: bigslick-setup
description: Sets up Big Slick for a new company or client. Use when someone says set up Big Slick, add a client, onboard a client or types /bigslick-setup.
---

# Big Slick setup

> Skeleton.

## Onboarding checklist

Onboarding runs over days, across several chats, so the client's `Onboarding Checklist` Sheet (step 3) is how the user keeps track of it. Follow its rules in shared definitions §2:

- **Coming back to a client on schema 1.2.** If the row's `schema_version` is `1.2`, migrate first, as shared definitions §14 says: create the `Claims and Sources` Sheet from the templates in step 3, copy the Claims library and Sources and intake table rows from the doc into its tabs, replace each table in the doc with its pointer line by deleting the table's range in place (never upload a new doc), fill `claims_sheet_id` (add the column header at the end of the `Clients` tab if it's missing) and set `schema_version` to `1.3`. Read the doc back to check nothing else changed, tell the user it's done, then carry on (and migrate on to 1.4, below).
- **Coming back to a client on schema 1.3.** Migrate as shared definitions §15 says: add the headers `about`, `purpose` and `funnel_stage` after the last column of the client's Content Calendar, apply the Status, approve and sme_approve dropdown requests, the `funnel_stage` dropdown and the column widths from `templates/content-calendar-format.json` to the client's Content Calendar, republish the review page from `templates/review-page.html` over the existing `review_page_url` (same capabilities as step 7), and set `schema_version` to `1.4`. Tell the user it's done, then carry on.
- **Coming back to a client.** If the client already has a registry row and a checklist, start there instead of at step 1: read the checklist, show it, say what's changed since `updated_at` (new files, a client reply), and carry on from the first row that isn't `done` or `skipped`.
- **After every step,** update that step's row (the row ids are in brackets below), then show the user the checklist in the chat as a table (step, status, waiting on, detail) with a link to the Sheet. Keep the table short: show the rows in the current phase and the next, plus any `waiting` or `blocked` row, and say how many earlier rows are `done`.
- **When something is waiting on someone,** set `waiting`, put who and what in `waiting_on`, with a date if they gave one, and tell the user what to send or do next.

## Steps

1. Run the connector check in `${CLAUDE_PLUGIN_ROOT}/references/connector-rule.md`, then fix what's missing with the user. Required services: Google Drive, and DataForSEO as a custom connector at `https://mcp.dataforseo.com/mcp`, signed in with the client's own DataForSEO account (the client pays for its usage). Stop until both work. Then check the optional onboarding services: **Tavily** and **Perplexity** (deeper outside-in research, paid for by the client) and **ConnectSafely** (reading authors' LinkedIn posts for their voices). Look for their tools in this session, without making a paid call. If any is missing, say in one line what it adds and offer to connect it, but don't wait: onboarding works without all three, using Claude's own web research for outside-in and other sources for voices. Note which are present in the checklist's `connectors` row `detail`. Then list the optional connectors in that file, offer to connect any the client wants, and record the chosen ones in the registry's `optional_connectors`. Ask for the client's location: the country or region whose search results matter to them, which DataForSEO pulls keyword and search data for. Record it in `seo_location` with DataForSEO's location name (e.g. `United States`), and set `seo_language` to that location's main language unless they name another. If they don't have it to hand, leave both blank: bigslick-onboarding asks again before any research runs. Also ask for a per-run DataForSEO call budget for the registry, the client's website URL (so outside-in research can start as soon as the folder exists), and the name and email of the client contact who will send documents and answer questions.
2. If they don't exist yet, create the `Big Slick` admin folder and the `Big Slick Registry` Sheet: create it from `templates/registry-clients.csv`, add a second sheet and write `templates/registry-run-log.csv` into it, then apply `templates/registry-format.json` with the two sheet ids. Add the client's row, filling every column in shared definitions (`status` = `active`, `schema_version` = `1.4`).
3. Create the client's folder under `Big Slick/Clients/` from the templates in `${CLAUDE_PLUGIN_ROOT}/templates/` (see its README):
   - `Brand Context`: a Google Doc created from `brand-context.md` (upload as markdown so the headings convert). Replace `[Client Name]` with the client's name. This is the only time the doc is uploaded: from now on it's edited in place and keeps this ID.
   - `Claims and Sources`: a Google Sheet created from `claims-sources-claims.csv`; add a second sheet and write `claims-sources-sources.csv` into it, then apply `claims-sources-format.json` with the two sheet ids. Put its link in the pointer lines under `Claims library` and `Sources and intake` in the doc, replacing `[Claims and Sources link]`.
   - `Content Calendar`: a Google Sheet created from `content-calendar.csv`, then formatted with the requests in `content-calendar-format.json` (put the new sheet's id in place of `__SHEET_ID__`).
   - Empty `Intake`, `Briefs`, `Drafts`, `Approved` and `Research` folders. `Intake` is where the client's documents go: anything they share, and a copy of every file that arrives another way.
   - `Onboarding Questionnaire`: a Google Sheet created from `onboarding-questionnaire.csv`, then formatted with the requests in `onboarding-questionnaire-format.json`. Fill in the `contact`, `website` and `location` answers from step 1, with `answered_by` = the user. The client fills in the rest.
   - Ask the user before sharing anything with the client. Once they agree, share `Intake` and `Onboarding Questionnaire` with the client contact as an editor, and nothing else in the folder.
   - `Onboarding Checklist`: a Google Sheet created from `onboarding-checklist.csv`, then formatted with the requests in `onboarding-checklist-format.json` (put the new sheet's id in place of `__SHEET_ID__`). Fill in the rows for what has already happened: `connectors` and `drive_setup` are `done`, and `location` is `done`, or `waiting` on the user if it's blank. Then show it to the user for the first time.
   Record every new file and folder ID in the client's registry row (the Sheet goes in `claims_sheet_id`), except `Intake`, `Research`, `Onboarding Questionnaire` and `Onboarding Checklist`, which have no column: skills find them by name.
4. Build the Brand Context doc with bigslick-onboarding (`references/drive-intake.md`, then `references/sign-off.md`), through to the client's sign-off. It starts outside-in research straight away, from the website URL and location, so the research runs while the client gathers documents. It updates the rows from `intake_request` to `signoff_applied` itself, and outside-in updates `research`. Steps 5 to 7 can run while sign-off is out.
5. Write each SME's profile with sme-content-review's profile writer. It updates `sme_profiles`, which stays `waiting` on each SME until they approve their profile, or is `skipped` if the client has no SMEs.
6. Capture voices with bigslick-onboarding's `references/author-voices.md`, which uses brand-voice-extractor from the `bigslick-strategy` plugin, for every author and every SME. SMEs are authors too: their voices are built from the same material as their profiles, their LinkedIn posts and the writing their profile cites, so run this after step 5. It reads each author's LinkedIn posts from the profile URL the client gave, plus any uploaded samples, adds each to the registry `authors` column, and updates `voices`.
7. Publish the client's review page from `templates/review-page.html`: replace every `__CLIENT_NAME__` with the client's display name, publish it as an artifact with `capabilities: {"db": {}, "user": {"scopes": ["profile", "email"]}}` and icon `checklist`, and record its URL in the registry's `review_page_url`. Don't seed the database; send-to-review fills it. Tell the user to share the page with each person in `reviewers` at **Contributor** or above (the Share menu): Viewers and Commenters can read the page but not decide. Then publish the dashboard from its template. Set `review_page` to `waiting` on the user until they confirm the page is shared, then `done`.
Onboarding ends here, once sign-off, voices, SME profiles and the review page are done or skipped. Tell the user so, show the checklist, and say that the workflow phase comes next.

## Workflow phase

These steps run after onboarding, starting with the first content plan (content-plan-workflow), which builds the pillars and proposes the cadence for the client to approve. They fill the checklist's phase 4 rows. This phase also collects what onboarding doesn't ask for:
- **The annual marketing plan.** The first content plan asks for it, and plans from what exists if there isn't one.
- **The org chart and the approval rules:** who signs off what, turnaround and legal triggers. Ask for them before the first draft goes to send-to-review, and record them in `Team map` and the Claims library. Until then, the registry `reviewers` and the red lines from the questionnaire are what review uses.

8. Once the client has signed off **and** has confirmed their content pillars and content strategy, create the scheduled tasks for the chosen workflows and cadence. Each task names its client. Confirmed means both of these:
   - Every pillar under `Content pillars` is tagged `[confirmed]`. The first content plan proposes them; a pillar the client hasn't confirmed doesn't count.
   - The client has approved the content strategy: the first content plan, run in chat with content-plan-workflow, covering the topics by pillar and the cadence it proposes.

   Until both are true, create no scheduled task for any workflow, and tell the user which one is still open.

   **Go-live check.** Before creating any task, and whenever the user asks what's left, show this as a table, one line per condition, each marked met or not, with who owes it:
   - sign-off done (`signoff_applied`);
   - pillars confirmed (`pillars`) and the first content plan approved (`strategy`);
   - `reviewers` filled in the registry, and `cadence_plan`, `cadence_blog` and `cadence_social` filled (the first content plan sets these, as part of the strategy approval);
   - the review page shared with the reviewers (`review_page`);
   - for LinkedIn only, a `Voice:` section for each author (`voices`).

   Create a workflow's task only when every condition that workflow needs is met: LinkedIn needs voices, and the content plan and long-form don't. SME profiles don't block go-live, because an SME without a profile simply isn't listed as a reviewer. The `pillars` and `strategy` rows show which: each is `done` only when its condition above holds. Set `scheduled_tasks` to `done` once the tasks exist, with their names in `detail`.
9. Write Project instructions for the user to paste into a new Claude Project for this client. Set `project` to `waiting` on the user until they say it's pasted.
10. Run one workflow end to end with the user as reviewer, and set `first_run` with the result. Once every row is `done` or `skipped`, tell the user the client is live.
