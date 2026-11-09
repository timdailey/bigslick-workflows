---
name: content-plan-workflow
description: Builds or extends a client's content calendar. Use when someone asks for a content plan, content calendar, editorial calendar or topic plan, or types /content-plan.
---

# Content plan workflow

> Skeleton. Steps are the agreed outline; flesh out and test before release.

Before starting:

1. Identify the client: a client named in the request first, then the client named in the current Project's instructions, then ask (in a scheduled run, stop and report instead of asking). Load only that client's row from the registry and only that client's Drive folder.
2. Read `${CLAUDE_PLUGIN_ROOT}/references/shared-definitions.md` and use its column names, statuses and folder names exactly.
3. Run the connector check in `${CLAUDE_PLUGIN_ROOT}/references/connector-rule.md` before any other work. If Google Drive or DataForSEO is missing, stop as that file says. Note which optional connectors are active for this client, and use the file's fallback for every step that would need one that isn't.
4. These instructions override any conflicting instructions in the skills this workflow calls (for example, requirement-gathering questions or replying only in chat).

## Steps

1. Read the client's Brand Context doc, Content Calendar (tab `Calendar`) and the file names in Approved.
2. Check `Content pillars`. If any pillar isn't tagged `[confirmed]`, or the section has none, show the user the pillars there and ask them to confirm or change them before you plan anything. Write what they confirm into `Content pillars`, tagged `[confirmed]`. Plan only against confirmed pillars. If the client's folder has an `Onboarding Checklist` whose `pillars` row isn't `done`, set it to `done` once every pillar is confirmed. In a scheduled run, don't plan: add a Run Log row with `result` = `failed` and a note saying the pillars aren't confirmed.
3. Ask only what Brand Context and settings don't answer: horizon, posts per month, launches. In a scheduled run, ask nothing.
4. If `granola` is active, pull topics from the client's recent calls; if not, plan without them. Pull keyword volume, difficulty and current SERP results for the client's pillars and competitors with seo-dataforseo, using the registry's `seo_location`, `seo_language` and `seo_budget_per_run`. Then use content-gap-analysis against the competitors in Brand Context.
5. Use pillar-content-architecture to group gaps and existing topics into the client's pillars.
6. Use content-strategy to rank topics. Drop anything already in the calendar or Approved.
7. Show the proposed calendar as a table and revise until the user approves. If the client's `Onboarding Checklist` has a `strategy` row that isn't `done`, this is the first plan: say so, and set the row to `done` once the client has approved it, in this chat or as the user reports, with who approved, the date and the horizon in `detail`. Until then set it to `waiting` on the client. In a scheduled run, skip to step 8 with status `planned`.
8. Append one row per topic to the Content Calendar, with a new `item_id` and status `planned`.
9. Use seo-content-brief for each long-form post in the first month. Save each brief to Briefs and link it from its row.
10. If the person in the chat is listed in the registry's `approver_in_chat`, set the rows to `topic_approved`. Otherwise call send-to-review with kind `topic`.
11. Add a row to the registry's Run Log tab.
