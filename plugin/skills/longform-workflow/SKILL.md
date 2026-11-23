---
name: longform-workflow
description: Writes long-form content for a Big Slick client from an approved topic through five stages (Intake, Outline, Draft and review, Final Check, Publish), stopping at four reviewer gates, in the chosen author's voice: blog posts and guides (case studies and gated assets in V1). Use when someone asks to write a blog post, article or guide, to approve or revise a piece waiting at a gate, or when a scheduled or hourly run picks up the next item.
---

# Long-form workflow

Turns a `topic_approved` calendar row into a publish-ready post in five stages, ported from ContentForge. The run stops at four sign-off gates (Outline, Draft, Final Check and Publish), emails the reviewer at each one and resumes only when they decide. There is no separate client review at the end: the Final Check and Publish gates replace it.

```
Intake → Research and outline ⏸ Gate 1 → Draft and review ⏸ Gate 2 → Final Check ⏸ Gate 3 → Publish ⏸ Gate 4 → ready_to_post
```

Read these before running a stage:

- `references/gates.md`: what every gate does, and the three decisions.
- `references/judge-prompts.md`: the judging passes, and the rule that the judge is never the writer.
- `references/rewrite-guards.md`: the checks every rewrite must pass, and the humanize stop rule.
- `references/publish.md`: the Stage 5 conversions and the publish checklist.
- `references/templates.md`: the outline and draft template for each content type.

## Before starting (Stage 0)

1. Load the client with bigslick-client. In a scheduled run with no client named, stop and report instead of asking.
2. Read `${CLAUDE_PLUGIN_ROOT}/references/shared-definitions.md` and use its column names, statuses and file names exactly.
3. Run the connector check in `${CLAUDE_PLUGIN_ROOT}/references/connector-rule.md` before any other work. If Google Drive or DataForSEO is missing, stop as that file says. Note whether `gmail` and `hubspot` are active for this client, and whether Tavily and Perplexity tools are present, and use the file's fallback for every step that would need one that isn't.
4. Ask bigslick-client for these sections only: `Company facts`, `Audience and ICP`, `Products and positioning`, `Content pillars`, `Claims library`, `Voices`, `Style rules`, `SME profiles`, `Content inventory` and `AI visibility baseline`.
5. **Resume check.** For each item, read the calendar row's `status` and the item's latest review document (the highest `<item_id>-v<version>`). Start at the stage they point to, never at the beginning:

| Calendar status | Latest review document | Do |
|---|---|---|
| `topic_approved` | none for this stage | Stage 1 |
| `outline_review`, `draft_review`, `final_review`, `publish_review` | `in_review`, no decision | Nothing: it's waiting. Say so in a chat; skip it in a scheduled run |
| same | `approved` | Start the next stage |
| same | `changes_requested` | Re-run the same stage with the document's `notes` (and, at Final Check, its ticked findings) |
| same | `declined` | Nothing; publish-approved sets the row to `declined` |
| `declined` | any | Nothing, unless a person in the chat asks to restart it (see Restart) |

Reuse completed work the way ContentForge does: a re-run of Stage 3 keeps the approved outline and fact pack; a re-run of Stage 2 keeps the fact pack unless the notes say it's wrong.

6. These instructions override any conflicting instructions in the skills this workflow calls, including requirement-gathering questions, replying only in chat, publishing steps, local scripts and SemRush or Ahrefs API calls. Use those skills for their method, not their commands.

## Which items

| Run | Picks up |
|---|---|
| Chat, topic named | That calendar row, at the stage the resume check gives. If the topic isn't in the calendar, add a row (the next `item_id`, status `topic_approved`, the content type and keyword the user gives) |
| Chat, nothing named | First a row with a decided gate waiting to resume, then the earliest `topic_approved` long-form row with a `brief_link`. Say which and go on unless the user picks another |
| Scheduled, or handed on by publish-approved | First rows with a decided gate waiting to resume, then `topic_approved` long-form rows with a `brief_link` and a `publish_date` within the next 21 days, earliest first. At most 2 items a run |

Long-form means `content_type` `blog_post` or `guide` (MVP). `case_study` and `gated_asset` are V1: if a row has one, say its template isn't built yet and skip it. A row in any other status is skipped and noted in the Run Log, as shared definitions §7 says.

Passes marked **(judge)** run as `references/judge-prompts.md` says: in a fresh subagent that never saw the writing. Every other pass is the writer.

## Stage 1. Intake (no gate)

1. Take the item's row and brief. If there's no brief (a topic from the chat), write one first with content-plan-workflow's `references/planning-rules.md` section 9 and its `references/seo-data.md` (a keyword overview call for the topic's terms and one SERP check), save it to Briefs and link it from the row.
2. Fill the intake fields from the row and brief: `target_keyword`, `title_hint`, `content_type` (`standard` or `listicle`, the ContentForge format; a `guide` is `standard`), `target_words`, `persona` (from `Audience and ICP`) and `author`.
3. **Author.** Settle who the piece is by before any writing.
   - **In a chat, always ask,** even when the row names someone, in one short question. Offer the row's current `author` (marked as the plan's suggestion), each name in the registry `authors` column (each has a `Voice:` section), the company as `<client_name>` (the company voice from `Style rules`), or someone else. Add one line on what each means: a named author's voice is used and they should see the piece before it publishes; a company byline uses the house voice, with the pillar's SME reviewing.
   - **Someone else:** if they have no `Voice:` section, offer to capture it first with bigslick-onboarding's `references/author-voices.md`, or to write in the company voice now. Don't invent a voice.
   - Write the answer to the row's `author` cell (the name exactly as in `authors`, or `client_name`).
   - **SME.** If the author has a heading in `SME profiles`, they are the item's SME. Otherwise, the SME whose topics in `SME profiles` cover the pillar is. Record `sme_name` (blank if none). When there is one, the SME passes in Stages 2 and 4 run and the SME is emailed at Gates 1 and 3.
4. Missing keyword, author or persona: ask in the chat. In a scheduled run, skip the item and log `needs <field>: <item_id>`.
5. If the brief is more than 60 days old, note it; Stage 2 re-checks the SERP.

## Stage 2. Research and outline → Gate 1

1. **keyword_pick.** DataForSEO search volume, keyword difficulty and related keywords for the brief's terms, within `seo_budget_per_run`. Pick `primary_keyword` and up to 5 `secondary_keywords`.
2. **serp_paa.** One SERP call: the top 10 results and the People Also Ask questions.
3. **angle.** Angle, differentiating thesis, dominant format of the ranking pages, and key points. Write `slug` (keyword-focused, lowercase, hyphens, 3 to 6 words) and `meta_description` (140 to 155 characters, as `references/templates.md` says). Then check deterministically, not by judgment: the slug contains the primary keyword's main term, and the meta description contains the primary keyword. Fix and re-check until both pass.
4. **fact_research.** Tavily first, then Perplexity, then Claude's web search, whichever is present. Build the `fact_pack`: one row per fact with claim, source URL and confidence (`high`, `medium`, `low`). Add the `approved` and `must_qualify` rows from the Claims library that fit the topic, with their `claim_id` and qualifier. Never put a competitor's number in as the client's.
5. **outline_pass.** Write the outline to the content type's template in `references/templates.md`: each heading with its search intent and target words, a voice brief (the author's `Voice:` section in five lines), and a keyword plan (where the primary and secondary keywords go). Answer the brief's main question first, and give each AI-visibility question in the brief its own section that stands alone.
6. **sme_review (judge).** Only when the item has an SME. Run sme-content-review on the outline, checks 2 (technical accuracy) and 4 (messaging and ICP fit) only, and keep its notes as `sme_notes`.
7. **Contract.** `primary_keyword`, `angle`, `outline` and `fact_pack` must be present and non-empty. If not, follow the contract rule in `references/gates.md`.
8. **Gate 1: Outline sign-off.** Save and send as `references/gates.md` says, with stage `outline` and calendar status `outline_review`. The Drafts file holds the angle, thesis, outline, fact pack with sources, slug, meta description, PAA questions and SME notes, so the reviewer can edit any of them. The SME is emailed too.
   - Approve: Stage 3, using the Drafts file as the reviewer left it.
   - Request changes: regenerate the outline (steps 3 and 5, then 6) from the notes, keeping the fact pack unless the notes say a fact is wrong.

## Stage 3. Draft and review → Gate 2

One automated run, then one gate.

1. **full_draft.** Write the whole piece from the approved outline, fact pack and voice brief, using write-blog for structure and on-page SEO only.
   - **Voice:** the author's `Voice:` section, or `Style rules` for the company. `Style rules` (house style, banned words, formatting) apply either way and win over the template.
   - **Answer first,** and answer each AI-visibility question in a passage that stands on its own, so AI search can quote it.
   - **Facts:** only from the fact pack and the Claims library. State figures, customer names and certifications only from `approved` Claims rows, or `must_qualify` rows with their qualifier. If the piece needs a fact you don't have, write a `[CHECK: what's needed]` marker. State `[confirmed]` and grade A facts plainly and hedge `[assumed]`, B and C facts. Never fill a `[TBD]`.
   - **Links:** 2 to 4 internal links from `Content inventory`, and outbound links only to the fact pack's primary sources.
   - **Red lines:** nothing from a `Style rules` never-say list or an SME's red lines.
   - **FAQ:** if the template or outline has an FAQ section, write it and set `has_faq` = `true`; otherwise `false`. Set it here and never re-parse it later.
   - Strip every character `Style rules` bans (em dashes and others).
2. **Review the raw draft (judge).** One subagent, given the draft once as a shared prefix and each rubric in turn, returns:
   - recommendations: quick wins, SEO/GEO, content gaps and H2 rewrites;
   - `persona` score (0 to 100);
   - `five_lens` score: credibility, voice consistency, persona alignment, structure, and brand safety (the company not named before the halfway point, and not a pitch);
   - `seo` on-page audit score;
   - `smell` AI smell test, using `../sme-content-review/references/humanize.md`;
   - editorial-qa, brand-review and claims-check results.
3. **polish.** One rewrite that fixes every finding from step 2. Apply the guards in `references/rewrite-guards.md`.
4. **Smell re-test (judge)** on the polished text.
5. **humanize.** As `references/rewrite-guards.md` says: deterministic clean-up first, then a rewrite aimed at the tells that survive, re-tested (judge) after each pass. Stop after 2 passes or when `smell` reaches the threshold (70% human). Record `humanize_iterations` and `exit_reason` (`threshold` or `max_passes`).
6. **word_count_fix.** Only if the word count is outside ±20% of `target_words`. Guards apply.
7. **Re-score (judge).** Every check in step 2 again on the final text, so every score describes the text being handed on. Rebuild the remaining SEO/GEO list from it. Run sme-content-review in full here when the item has an SME, so `sme_verdict` and `sme_flags` describe this text.
8. **Link check.** Fetch every outbound URL. A dead link (404, 410, DNS failure): remove the link and the claim it supports, or replace the source from the fact pack. Blocked by the site (403, bot wall, timeout): keep it and flag it for a person to open. Set `link_gate` to `ok` or `broken`, and record which text it ran on.
9. **Contract.** `final_text`, the four scores (`persona`, `five_lens`, `seo`, `smell`), the claims-check result and `has_faq`.
10. **Gate 2: Draft sign-off.** Stage `draft`, calendar status `draft_review`. The review item carries the scorecard, the remaining improvements, claims flags, sme-content-review's verdict and flags, and the humanize exit reason.
    - Approve: Stage 4.
    - Request changes ("revise with instructions"): steps 3 to 8 on the reviewer's notes, as one polish aimed at the notes.
    - If the notes ask only for a re-score (for example after the reviewer edited the Drafts file), run steps 7 and 8 on the file as it stands, with no rewrite.

## Stage 4. Final Check → Gate 3

1. **Link gate.** The link check must be `ok` for the current text. If the text changed since it ran (the reviewer edited the Drafts file, or findings were applied), run it again first.
2. **Cold SME read (judge).** sme-content-review in **Final Check mode**: told the piece is finished and this is its last review. It returns a verdict and itemized findings (id, severity, check, line, safer wording) and changes nothing. It runs on every piece; the technical lens runs only when the item has an SME.
3. **Second smell test (judge).** Its own list of tells, as findings with ids `H-1`, `H-2`, … and `check` = `humanize`, in the same shape as the SME findings, kept apart from the SME findings, because humanizing and fixing a fact are different changes.
4. Write all findings to the review item with `selected` = `false` and `applied` = `false`, keeping any finding already `applied` from an earlier round so the page can hide it.
5. **Contract.** The cold read's verdict, the findings list (it may be empty) and `link_gate` = `ok`.
6. **Gate 3: Final Check sign-off.** Stage `final_check`, calendar status `final_review`. The SME is emailed too. On the review page the reviewer ticks the findings to apply, in two lists (SME findings and humanize fixes).
   - **Request changes** with ticked findings (and optional notes): apply only the ticked items and change nothing else. Run the guards: a rewrite that adds a number, link or placeholder is reverted and the item named. Mark the applied findings `applied`. The link check resets, so re-run it, and the earlier findings are stale, so re-run steps 2 and 3. New version, new gate.
   - **Approve for publish,** with or without ticks. If any ticked finding isn't applied yet, apply it as above and re-run the link check. If a guard reverted something or the link check isn't `ok`, come back to Gate 3 with a note saying why. Otherwise go to Stage 5.

## Stage 5. Publish (no content judgment) → Gate 4

Publish formats the approved text and judges nothing. Follow `references/publish.md`.

1. **html_convert.** Body HTML with no H1; the CMS adds it.
2. **geo_faq.** Only if `has_faq` is `false`: add a short question block that answers the page's main questions outright, using only facts already in the text.
3. **schema.** JSON-LD: `Article`, plus `FAQPage` when there's an FAQ. Run it after geo_faq.
4. **personal_post.** The author's LinkedIn post linking the article, in their `Voice:` section and its channel rules, with `[ARTICLE URL]` where the link goes.
5. **Assemble.** Write the Approved doc `<date> <title> (<item_id>)`: title, slug, meta description, tags (the secondary keywords), author, the HTML, the schema and the LinkedIn post, without the `Draft notes`. Fill the row's `final_link`. If `hubspot` is active, also create a draft post there with the same fields, for a person to publish.
6. **Contract.** `final_link`, the HTML, the schema and the LinkedIn post.
7. **Gate 4: Publish sign-off.** Stage `publish`, calendar status `publish_review`. The email carries the Approved doc, the HTML, the LinkedIn post and the publish checklist in `references/publish.md`.
   - Approve: publish-approved sets the row to `ready_to_post`, or `scheduled` if the reviewer scheduled it. When the live URL is entered (on the review page or in the row's `published_url`), the row moves to `published` and repurpose-workflow can pick it up.
   - Request changes: re-run Stage 5 from the notes. A note that asks to change the text itself (not its formatting) goes back to Gate 3 instead, so the text is judged again.

## Restart

A `declined` long-form row ends there. If a person in the chat asks to restart it, set it back to `topic_approved` (shared definitions §7), keep the earlier versions in Drafts, and run from Stage 1 with the decline reason as an extra instruction. A scheduled run never restarts one.

## Log

Add a Run Log row per run: `workflow` = `longform-workflow`, `trigger` = `chat` or `scheduled`, `items` = items moved to a gate, `result` = `ok`, `partial` (an item skipped, a contract failed or a flag left) or `failed`. Start `note` with the connector summary from the connector rule, then per item `<item_id>: <gate reached>`, DataForSEO calls, the humanize exit reason, and any item skipped and why (e.g. `needs author: acme-0042`).

## Report

In a chat, end with what's waiting and where: the item, the gate, the Drafts link, the review page link, and who was emailed. List the links as plain URLs (the Gmail connector rewrites links in the email as redirects). Name any fallback used, as the connector rule says.
