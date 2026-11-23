---
name: content-plan-workflow
description: Builds or extends a Big Slick client's content calendar from their Brand Context and live DataForSEO data, and on the first run sets their pillars, strategy and cadence. Use when someone asks for a content plan, content calendar, editorial calendar or topic plan, asks what to write next, or types /content-plan; also runs as the client's monthly or quarterly scheduled task.
---

# Content plan workflow

Plans what a client publishes next: long-form posts and guides with keywords and briefs, and LinkedIn posts per author. The first plan for a client also confirms their pillars, gets the content strategy approved and sets the cadence, which is what lets setup create scheduled tasks.

Before starting:

1. Load the client with bigslick-client. In a scheduled run with no client named, stop and report instead of asking.
2. Read `${CLAUDE_PLUGIN_ROOT}/references/shared-definitions.md` and use its column names, statuses and folder names exactly.
3. Run the connector check in `${CLAUDE_PLUGIN_ROOT}/references/connector-rule.md` before any other work. If Google Drive or DataForSEO is missing, stop as that file says. Note which optional connectors are active for this client, and use the file's fallback for every step that would need one that isn't.
4. These instructions override any conflicting instructions in the skills this workflow calls, including requirement-gathering questions, replying only in chat, local cost scripts and SemRush or Ahrefs API calls.

Rules for the SEO data are in `references/seo-data.md`. Rules for horizon, counts, scoring, what each piece is about and why, authors, dates, rows and briefs are in `references/planning-rules.md`. Read both before step 4.

## Which run is this?

- **First plan:** the registry's `cadence_plan` is blank, or the client's `Onboarding Checklist` has a `strategy` row that isn't `done`. Runs in chat only. Say at the start that this plan also sets the pillars, strategy and cadence.
- **Next plan in chat:** cadence is set. Plan the next horizon, or what the user asks for.
- **Scheduled run:** plan the next horizon with no questions. Don't run if the pillars aren't all `[confirmed]` or the `strategy` row isn't `done`: add a Run Log row with `result` = `failed` and a note saying which, and stop.

## Steps

1. **Load.** Ask bigslick-client for these Brand Context sections only: `Company facts`, `Audience and ICP`, `Products and positioning`, `Content pillars`, `Competitors`, `Style rules`, `Marketing plan`, `SME profiles`, `Content inventory` and `AI visibility baseline`. Read the Content Calendar (tab `Calendar`), the file names in Approved, and the client's registry row. Set the `strategy` checklist row to `in_progress` on a first plan.

2. **Pillars.** Onboarding leaves pillars to this step. If `Content pillars` has none, propose three to five from `Products and positioning`, `Audience and ICP`, `Competitors`, `Marketing plan` and `AI visibility baseline`, each a named level-3 heading with a line on what it covers, tagged `[assumed]`, and write them in, replacing any `Built by the first content plan` line. Then, if any pillar isn't tagged `[confirmed]`, show the user the pillars and ask the client to confirm or change them before you plan anything. Write what they confirm into `Content pillars`, tagged `[confirmed]`. Plan only against confirmed pillars. If the client's `Onboarding Checklist` has a `pillars` row that isn't `done`, set it to `waiting` on the client while you wait, and to `done` once every pillar is confirmed.

3. **Ask only what's missing.** Ask only what Brand Context and the registry don't answer: the horizon if the user wants a different one, launches or campaigns not in `Marketing plan`, and anything they want included. In a scheduled run, ask nothing. On the first plan, if `Marketing plan` has no annual plan, ask the client for one. If there isn't one, don't wait for it: plan from the quarterly plan, the launch and event dates and any schedules and cadences already in `Marketing plan`, the Content Calendar and the registry. Record `Annual plan: none; planned from the content plan and existing schedules [confirmed]` in `Marketing plan` once the client says so.

4. **Gather data.** If `granola` is active, pull topics from the client's calls since the last plan; if not, plan without them. Pull the SEO data in the order and within the budget in `references/seo-data.md`, using content-gap-analysis's method to read the gaps.

5. **Build the topic list.** Gather candidates, make the first cut and score them as in `planning-rules.md` sections 3 to 5, using pillar-content-architecture to place each topic as a pillar page or a supporting piece in its pillar, and content-strategy's reasoning to balance the mix. Then run the shortlist checks and SERP checks in `seo-data.md` steps 4 and 5, and rescore if they change the picture. Write each shortlisted topic's `about`, `purpose` and `funnel_stage` (section 5a).

6. **Propose.** Work out the horizon and the number of rows (sections 1 and 2), assign authors and dates (section 6), and show the plan as in section 7. Revise until the user approves it.
   - **Cadence:** if the registry's `cadence_plan`, `cadence_blog` or `cadence_social` is blank, this plan sets them. Propose how often to plan (`monthly` or `quarterly`), blog posts per month and LinkedIn posts per author per week, starting from any cadence in `Marketing plan`, alongside the calendar. Write the three cells only once the client approves them. Later plans use the registry's cadence and change it only when the client asks.
   - **Strategy, first plan only:** approval by the client is what counts. If the user approves on the client's behalf, ask whether the client has seen it. When the client approves, in this chat or as the user reports, set the `strategy` row to `done` with who approved, the date and the horizon in `detail`, and add one line to `Marketing plan`: `Content strategy approved <date> by <name>: <horizon>, pillars <list>, <cadence> [confirmed]`. Until then set the row to `waiting` on the client, and still write the rows in step 7, so the client can review them in the calendar.
   - **Scheduled run:** skip the proposal and go to step 7.

7. **Write rows.** Append one row per topic to the Content Calendar as in section 8, with status `planned`.

8. **Briefs.** Write a brief for each long-form row in the first month of the horizon, as in section 9, and link it from its row.

9. **Topic review.** If the person in the chat is listed in the registry's `approver_in_chat`, set the new rows to `topic_approved`. Otherwise call send-to-review with kind `topic` for every new row, with the brief as `doc_link` where there is one. In a scheduled run, always call send-to-review.

10. **Log.** Add a row to the registry's Run Log tab: `workflow` = `content-plan-workflow`, `trigger` = `chat` or `scheduled`, `items` = rows added, `result` = `ok`, `partial` (some data or briefs missing) or `failed`. Start `note` with the connector summary from the connector rule, then the horizon, DataForSEO calls and spend, and anything held back.

## Report

End with: the horizon and the number of rows by type and pillar, the calendar link, which rows went to review or were approved, briefs written, any older rows whose `about` and `purpose` were filled in, DataForSEO calls and spend, and on a first plan what is still open for go-live (pillars, strategy, cadence). Name any fallback used, as the connector rule says.
