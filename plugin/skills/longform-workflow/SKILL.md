---
name: longform-workflow
description: Writes long-form content for a Big Slick client from an approved brief, in the chosen author's voice, and sends it for review: blog posts and guides (case studies and gated assets in V1). Use when someone asks to write a blog post, article or guide, to redraft a declined draft, or when a scheduled run picks up the next approved brief.
---

# Long-form workflow

Turns an approved topic into a reviewed-ready draft: one workflow for every long-form content type, with a template per type in `references/templates.md`. In a chat it always asks who the piece is by before it drafts.

Before starting:

1. Load the client with bigslick-client. In a scheduled run with no client named, stop and report instead of asking.
2. Read `${CLAUDE_PLUGIN_ROOT}/references/shared-definitions.md` and use its column names, statuses and file names exactly.
3. Run the connector check in `${CLAUDE_PLUGIN_ROOT}/references/connector-rule.md` before any other work. If Google Drive or DataForSEO is missing, stop as that file says. Note which optional connectors are active for this client, and use the file's fallback for every step that would need one that isn't.
4. These instructions override any conflicting instructions in the skills this workflow calls, including requirement-gathering questions, replying only in chat, publishing steps, local scripts and SemRush or Ahrefs API calls. Use those skills for their method, not their commands.

## Which items

| Run | Picks up |
|---|---|
| Chat, topic named | That calendar row, or a new row if the topic isn't in the calendar (the next `item_id`, status `topic_approved`, the content type and keyword the user gives) |
| Chat, nothing named | The earliest `topic_approved` long-form row with a `brief_link`; say which and go on unless the user picks another |
| Scheduled | First every `declined` long-form row (redrafts), then `topic_approved` long-form rows with a `brief_link` and a `publish_date` within the next 21 days, earliest first. At most 2 items a run |

Long-form means `content_type` `blog_post` or `guide` (MVP). `case_study` and `gated_asset` are V1: if a row has one, say its template isn't built yet and skip it. A row in any other status is skipped and noted in the Run Log, as shared definitions §7 says.

## Steps

1. **Load.** Ask bigslick-client for these sections only: `Company facts`, `Audience and ICP`, `Products and positioning`, `Content pillars`, `Style rules`, `Claims library`, `SME profiles`, `Content inventory` and `AI visibility baseline`. Read the calendar row and its brief.

2. **Author.** Settle who the piece is by before writing anything.
   - **In a chat, always ask,** even when the row names someone, in one short question. Offer:
     - the row's current `author`, if any, marked as the plan's suggestion;
     - each name in the registry `authors` column (each has a `Voice:` section);
     - the company, as `<client_name>`, written in the company voice from `Style rules`;
     - someone else.
     Add one line on what each choice means: a named author's voice is used, and that person should see the draft before it publishes; a company byline uses the house voice, with the pillar's SME (from `SME profiles`) reviewing.
   - **Someone else:** if they have no `Voice:` section, offer to capture it first with bigslick-onboarding's `references/author-voices.md`, or to draft in the company voice now and adapt it once their voice exists. Don't invent a voice.
   - Write the answer to the row's `author` cell (the name exactly as in `authors`, or `client_name`). Ask bigslick-client for that author's `Voice:` section, or use `Style rules` for the company.
   - **In a scheduled run,** use the row's `author`. If it's blank, or names someone with no `Voice:` section who isn't the company, skip the item and log `needs author: <item_id>`, so the next chat can settle it.

3. **Brief.** Use the row's brief. If there's none (a topic from the chat), write one first with content-plan-workflow's `references/planning-rules.md` section 9 and its `references/seo-data.md` (a keyword overview call for the topic's terms and one SERP check), save it to Briefs and link it from the row. Then read the live SERP notes in the brief: if the brief is more than 60 days old, re-run its SERP check (1 call) and note what changed.

4. **Draft.** Write to the content type's template in `references/templates.md`, using write-blog for structure and on-page SEO only.
   - **Voice:** the author's `Voice:` section, or `Style rules` for the company. `Style rules` (house style, banned words, formatting) apply either way and win over the template.
   - **Answer first.** Open with the direct answer to the brief's main question, and answer each AI-visibility question the brief lists in a passage that stands on its own, so AI search can quote it.
   - **Claims:** state figures, customer names and certifications only from `approved` Claims rows, or `must_qualify` rows with their qualifier, using the row's wording. If the piece needs a fact you don't have, write it as a `[CHECK: what's needed]` marker rather than inventing it or borrowing a competitor's number.
   - **Provenance:** state `[confirmed]` and grade A facts plainly and hedge `[assumed]`, B and C facts. Never fill a `[TBD]`.
   - **Links:** 2 to 4 internal links to the client's own pages from `Content inventory`, and outbound links only to primary sources (regulators, standards bodies, peer-reviewed papers, the client's own data).
   - **Red lines:** nothing from `Style rules` never-say lists or an SME's red lines in `SME profiles`.

5. **Check and fix.** Run each, in this order, and fix before the next:
   1. editorial-qa: brief adherence, structure, clarity and the AI-content audit. Fix everything it finds.
   2. brand-review against `Style rules` and the author's voice. Fix everything it finds.
   3. claims-check. Rewrite a flagged claim to its approved wording, or remove it. Keep it, flagged, only if the piece doesn't stand without it.
   4. sme-content-review, right after claims-check. Apply its `humanize` and `channel` fixes, and leave the rest for the reviewer.

6. **Save.** Write the draft as a Google Doc in Drafts named `<date> <title> (<item_id>) v<version>`, in the template's order. End it with a level-2 heading `Draft notes (removed at publish)` holding: item_id, author, keyword, the claim_ids used, any `[CHECK]` markers left, the flags from step 5 left for the reviewer, and, for a redraft, what changed and why. Update the row: `draft_link`, `version`, `author`, status `drafted`, `updated_at`.

7. **Review.** Call send-to-review with kind `draft`, the claims-check flags and the sme-content-review result.

8. **Log.** Add a Run Log row: `workflow` = `longform-workflow`, `trigger` = `chat` or `scheduled`, `items` = drafts written, `result` = `ok`, `partial` (an item skipped or a flag left) or `failed`. Start `note` with the connector summary from the connector rule, then DataForSEO calls, items skipped and why (e.g. `needs author: acme-0042`).

## Redraft

A `declined` row comes back here. Read the decline `reason` on the row and the reviewer decisions on the review page, and the previous version from `draft_link`. Keep the author unless the reason says otherwise. Write version + 1 that answers every point in the reason, and change nothing the reason doesn't touch. List each point and what you did in `Draft notes`. Then continue from step 5. The declined version stays in Drafts.

## Report

In a chat, end with the draft link, the author, word count, claims used, anything flagged for the reviewer, and that it's on the review page. Name any fallback used, as the connector rule says.
