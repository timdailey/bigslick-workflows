# Planning rules

How the content plan turns data into calendar rows. Column names and values are in `shared-definitions.md` §5 to §7.

## 1. Horizon

- `cadence_plan` = `monthly`: the next calendar month after the last `publish_date` in the calendar, or the next month after today if the calendar is empty.
- `quarterly`: the next three calendar months on the same rule.
- First plan, with no cadence yet: propose one month, unless the client asks for more or their `Marketing plan` runs by quarter.
- **First plan, work already in progress:** before planning, ask for drafts in review or scheduled posts that aren't in the calendar (`Marketing plan` and `Content inventory` may mention them). Add each as a row with its real status (`drafted`, `in_review` or `scheduled`) and date, so the plan works around them and doesn't repeat them. In a scheduled run, skip this.
- The user can name another horizon in chat. A scheduled run uses the rule above and nothing else.

## 2. How many rows

Plan only the content types in the registry's `content_types` that the MVP drafts: `blog_post`, `guide` and `social_post`. Others (`case_study`, `gated_asset`, `newsletter`, `email`) wait for their workflows; note any the client wanted in the plan summary.

- **Long-form** (`blog_post` and `guide`, channel `blog`): `cadence_blog` per month.
- **LinkedIn** (`social_post`, channel `linkedin`, only if `linkedin` is in `channels`): `cadence_social` per author per week, for each name in `authors` that has a `Voice:` section. If `cadence_social` is 3 or more, plan one fewer per author per week and leave that slot for repurposed posts, which repurpose-workflow adds with a `source_item_id`.
- If a cadence cell is blank (first plan), use the proposed cadence the client is approving in this chat.

## 3. Candidate topics

Build one list from these sources, keeping where each came from:

1. The candidate list and competitor gaps from the SEO data (`seo-data.md`).
2. The client's own keywords at positions 11 to 30: pages close to page one, worth a new supporting piece. A keyword where the client already ranks in the top 10 isn't a new topic.
3. Questions rivals win in `AI visibility baseline`. Write each as a topic that answers the question directly, with the client's evidence.
4. Launches, events and themes in `Marketing plan` inside the horizon. Each event gives LinkedIn angles before it (who to meet, what to watch), during it and after it (one takeaway), plus a long-form topic if a theme is strong enough.
5. Topics from calls, if `granola` is active (connector rule).
6. Topics the user names in chat. These skip the cut below but are still scored and mapped to a pillar.

## 4. First cut

Drop a candidate that:

- doesn't fit any confirmed pillar;
- is a competitor's brand term, unless it's a comparison or alternatives topic for a competitor in `Competitors` and `Marketing plan` or the user asks for comparison content; or is a term for something the client doesn't sell (`Products and positioning`);
- touches a red line or a banned subject in `Style rules` or an SME's red lines in `SME profiles`;
- is already covered: the same keyword or the same question is in the Content Calendar (any status except `topic_declined`), in the file names in Approved, or in `Content inventory`. A covered page that has gone stale is a refresh, which is a V1 workflow: list it in the summary, don't add it;
- has under 20 searches a month in the client's location, unless it came from the AI visibility baseline, a launch, a call or the user.

Group what's left by meaning: one row per topic, not per keyword. Keep the strongest keyword as the row's `keyword` and the others for the brief.

## 5. Score

Give each remaining topic 1 to 5 on each line, then rank by the weighted total.

| Factor | Weight | 5 means |
|---|---|---|
| Business fit | 3 | Leads straight to a product in `Products and positioning`, for the ICP in `Audience and ICP` |
| Winnability | 2 | Difficulty well below what the client already ranks for, or a SERP of forums and thin pages |
| Demand | 2 | Highest volume in this list, or a launch or campaign needs it |
| AI-search gap | 2 | A question rivals win in `AI visibility baseline`, or an AI Overview that cites competitors |
| Timing | 1 | Tied to a launch, event or season inside the horizon |

Then balance the long-form picks:

- Every confirmed pillar gets at least one long-form piece per month where the cadence allows, more for the pillars `Marketing plan` puts first.
- Mix the funnel: no more than half the month on one stage (awareness, consideration, decision).
- **Content type:** `guide` for a broad how-to or what-is topic on a pillar's main term that needs the full picture; `blog_post` for everything else.

LinkedIn rows are angles, not keywords: a point of view, a lesson, a customer story, or a short take on that month's long-form topic. Spread each author's rows across their pillars and their own topics in `SME profiles`. Leave `keyword` blank.

## 6. Authors and dates

- **Long-form author:** a suggestion, not a decision: the long-form workflow asks the user who the piece is by before it drafts. Suggest the SME whose `SME profiles` topics cover the pillar if the piece is first-person (their view or experience) and they're in `authors`; otherwise the company, as `client_name`. Don't give one SME every row: check their review time in `SME profiles`.
- **LinkedIn author:** each row belongs to one author, at that author's cadence.
- **Dates:** spread long-form evenly across the horizon on Tuesdays to Thursdays. Spread each author's LinkedIn rows across Monday to Thursday, never two by the same author on one day. Put a launch-related topic one to two weeks before its launch date. Skip any blackout or embargo dates in `Marketing plan`. Dates are in the client's `timezone`.

## 7. The proposed plan in chat

Show one table, in date order:

| Date | Type | Channel | Title | Keyword | Pillar | Author | Why |
|---|---|---|---|---|---|---|---|

`Why` is one short line from the scoring, e.g. `Gap: 2 rivals rank, 880/mo, KD 24`. Below the table give, in a few lines: the horizon, the counts by pillar and type, the cadence (proposed or current), DataForSEO calls and spend, and anything held back (refresh candidates, content types that wait for V1, topics dropped for a red line).

## 8. Writing rows

- **item_id:** `<client>-<4-digit number>`, starting at the highest existing number + 1, in date order.
- Fill `publish_date`, `content_type`, `channel`, `title`, `keyword`, `pillar`, `author`, `status` = `planned`, `updated_at` = now in UTC. Leave every other column blank.
- Append all rows in one write, in the 20-column order of §6.
- Read the calendar again just before writing. If someone added rows since you read it, renumber from the new highest `item_id`.

## 9. Briefs

For each long-form row in the first month of the horizon, write a brief with seo-content-brief's method and output format, using the SEO data from this run in place of its SemRush steps. Add to the top of each brief: the item_id, pillar, author, the questions from `AI visibility baseline` it should answer, and the SERP notes from `seo-data.md` step 5. Save it as a Google Doc in Briefs, named `<date> <title> (<item_id>)`, and put its link in `brief_link`. Later months get briefs when the next plan runs.
