# Outside-in research for Brand Context

For a client in the Big Slick Registry. Researches the company and five competitors, and writes the findings into that client's Brand Context in a neutral voice.

The research itself is the `outside-in-report` skill from the core `bigslick` plugin: load it, and run its Stages 3 to 6 as written, for six vendors, except that section 7 below says where the run is saved. This file replaces its Stages 0, 1, 2, 7 and 8, and its report's admirer's voice and scoring narrative don't apply here. Files named below without a path (`references/prompt-set.md`, `references/budget.md`, `budget.md`, `prompt-set.md` and the `.py` scripts) are outside-in-report's own. If that skill isn't installed, stop and say the core `bigslick` plugin is needed.

## 1. Before anything is spent

Load the client with `bigslick-client` and name them.

Two things must be in place. If either is missing, stop here, tell the caller what's missing, and add the Run Log row from section 8 with `result` = `failed`. bigslick-onboarding carries on with the client's documents, and anything that depends on the research stays `[TBD: <owner>]` until this runs.

- **`client_folder_id` in the client's row,** because the run is saved under it. Never search for a folder when it's blank.
- **DataForSEO tools,** present. Don't make a paid call to test them.

Tavily and Perplexity are optional. Check whether their tools are present, without a paid call, and take the route in section 1a for any that isn't. Record the route in the run's `config.json` (`research_route`: `full`, `no_tavily`, `no_perplexity` or `claude_only`) and in the Run Log note.

Then read the row's `seo_location` and `seo_language`. If `seo_location` is blank, ask the caller for the client's location and write both to the row before any paid call; in a scheduled run, stop as above. Pass them as `location_name` and `language_name` on every DataForSEO call that takes a location or language, in place of the US English codes in `references/prompt-set.md`. ChatGPT calls always use `United States` and `English`, whatever the client's location, because that's the only ChatGPT data there is, as that file says.

If the client's folder has a Google Sheet named `Onboarding Checklist`, update its `research` row as shared definitions §2 says: `in_progress` once collection starts, with spend so far in `detail`, then `done` with the spend and duration, or `blocked` with the missing tool or cell in `waiting_on` for a stop in this section.

## 1a. Without Tavily or Perplexity

outside-in-report's stages name Tavily and Perplexity for some steps. When one isn't connected, do that step with Claude's own web search and page fetching (the WebSearch and WebFetch tools, or whatever search and fetch tools this session has), plus DataForSEO, as below. It's slower and covers fewer pages, but the grades are the same, because a grade depends on the source, not the tool that fetched it.

| Step in outside-in-report | Without Tavily or Perplexity |
|---|---|
| Tavily extract (read one page) | Fetch the page. If the fetch is blocked or the page renders empty, use DataForSEO's `on_page_content_parsing` for it (counts against the $15 cap) |
| Tavily map (list a site's pages) | Fetch `/sitemap.xml` (and any sitemap index it lists) and read the navigation of the homepage. If neither works, search `site:<domain>` with the section's words (`case study`, `customers`, `blog`, `press`) |
| Tavily crawl (read a section, e.g. 100 blog posts) | List the section from its sitemap or index page, then fetch the most recent items one by one, up to 30 per vendor for the client and 15 per competitor. Say the sample size in the record |
| Tavily or Perplexity deep research (analysts, acquisitions, funding, press) | Run your own research: several web searches per question, each with the vendor's name and the topic (`Gartner Magic Quadrant`, `Forrester Wave`, `acquired`, `raises`, `press release`), then fetch and read the primary sources the results point to. Prefer the analyst's or company's own page over a summary of it, and grade by that source |
| Perplexity for paid search | DataForSEO's paid-search data, which the stage already uses |
| AI visibility (area 8) | Unchanged: it runs through DataForSEO's AI optimization data, not the Perplexity connector |

Every record still needs a URL you fetched yourself. Never record a fact that only appears in a search result's snippet. Without Tavily, expect the run to take longer, and to spend less, because page fetching is free.

## 2. Reuse this client's run, and only this client's

Find the client's `Research/` folder by name under `client_folder_id`, and create it if it isn't there. Look there for a run folder from the last 30 days.

- **There is one:** reuse its ledger, unless the request asks for a fresh run. Skip collection and section 7. Re-check only the Claims library rows with `owner` = `outside-in` (Stage 6, on those rows), write what changed, and add the Run Log row. Spend only what the re-check needs.
- **There isn't, or the request asks for a fresh run:** do a full run.

Never look anywhere else, and never reuse another client's research, even when you share a competitor. Each client's run starts from nothing.

## 3. Pick five competitors

Start with any competitor the request names, then the `Competitors` section of Brand Context, then fill the set as in Stage 1. Don't pause to confirm: the client sees the list at sign-off. Record a one-line reason for each in `config.json`.

## 4. Budget

The cap is fixed at **$15** for the run. Don't ask for one. Most runs spend $2 to $8, because free search and page fetching do most of the collection. Log every paid call and check the cap as in `references/budget.md`, with `--budget 15`. At 90%, stop collecting and write what you have. The planner overstates spend, so a high estimate is no reason to trim.

## 5. Collect, score and re-check

Run Stages 3 to 6 for all six vendors. Stage 6 matters more here than in a report, because a record it passes can reach drafts as an `approved` claim. If the browser steps for reviews or paid media aren't possible, record the gap and carry on.

**Stage 6 must be independent.** Its re-checks run in fresh agents that didn't collect the records. An agent running in the background usually can't start agents. So if you were started as one (bigslick-onboarding runs collection that way), stop after Stage 5: save the run as in section 7, mark the `research` row `in_progress` with `collected; re-check and write to follow` in `detail`, and hand back. The session that started you runs Stage 6 and sections 6 to 8. If no fresh agent can be started anywhere, skip Stage 6, and hold every claims row at `candidate`, saying why in `notes`. Never approve a row from a re-check that the collecting agent ran itself. Record who re-checked each record in a `stage6_by` field, `independent` or `collector`; only `independent` counts for approval. A record the independent re-check changed and read again counts as verified, in its new form. After Stage 6, run `score_proof.py` again if a changed record feeds a vendor's score.

**In a cloud session,** a few steps need another route:

- **Martech (area 13).** If `detect_stack.py` can't reach the site because the shell's network is blocked, use DataForSEO's domain technologies data instead, graded B. Say in the run's `config.json` which you used.
- **LLM visibility (area 8).** Responses can run to many thousands of tokens. Ask for no more than 20 results a call, save each raw response to the run folder, and pull out only the fields `prompt-set.md` lists with a short script, rather than reading whole responses.
- **Spend.** Log a DataForSEO call's `cost` field when the response has one. When it doesn't, and for Tavily and Perplexity when they're used, which don't report cost, log the planning rate from `budget.md` and mark it estimated. The Run Log note gives actual and estimated spend separately, e.g. `$0.73 actual + $3.16 estimated`.
- **A call that times out,** such as a deep-research call through the user's computer, may still be charged. Log it as estimated at its planning rate, retry it at most once, then use another research tool for that question.

Stage 6 re-checks each changed record once, from what the source says now. Keep that reading as the record, or drop it. Records observed on LinkedIn, in ad libraries or in paid data keep their grade, but Stage 6 can't pass them, so they don't count as verified.

## 6. Write each record to its section

bigslick-onboarding starts this run before its own intake, so the client's documents may still be going into the doc. Don't write anything to Brand Context until bigslick-onboarding says its step 8 is done. Until then, finish collection and Stage 6, and wait with the findings ready. If you can't tell, ask the caller. Read the doc again just before you write, because the rules below depend on what's in it.

Where each area lands:

| Area | Brand Context section |
|---|---|
| 1 Homepage and pillar messaging | Products and positioning, and each figure stated on the homepage to the Claims library |
| 2 Customer proof inventory, 3 Proprietary research, 4 Boilerplate figures | Claims library |
| 5 Analyst standing, 6 Ownership and funding | Competitors, Company facts |
| 7 Peer reviews | Audience and ICP (customer language), Competitors |
| 8 LLM visibility | AI visibility baseline |
| 9 llms.txt and robots.txt, 10 Localization, 15 Video recency | Content inventory |
| 16 Trust pages | Content inventory, and each certification, compliance or partner claim on them to the Claims library |
| 11 Authorship, 12 Founder story | Voices (candidate authors), SME profiles (public record), Products and positioning |
| 13 Martech stack | Not written to the doc; stays in the run folder |
| 14 Paid media mix, 17 Defunct or acquired sites | Competitors |

**A record about a competitor always goes in `Competitors`,** or in `AI visibility baseline` for area 8. The client's own paid media and retired sites go there too, as the baseline. The Claims library holds only the client's own claims. A finding that something is missing is not a claim, so it never gets a row. Keep everything intake already wrote in `Competitors`: add to it, and don't repeat a line it already has.

**Write findings, not records.** One finding can draw on many records. Land what a writer or reviewer would use, and leave repeated detail and audit notes in the ledger.

**Tag every line `sourced`, with its grade and URL:** `[sourced A: <url>]`, `[sourced B: <url>]` or `[sourced C: <url>]`. A finding from paid data with no public page, such as AI visibility or paid media, uses the Drive link to its file in the run folder as the URL. Never `[confirmed]`: the client confirms at sign-off. Write plainly and neutrally, one fact per line.

**Claims tab rows** (the Claims and Sources Sheet), following its columns:

| Record | `status` | Also set |
|---|---|---|
| Grade A or B, verified in Stage 6, with no hold below | `approved` | `owner` = `outside-in`, `last_checked` = `verified_on`, `expires_on` = 12 months later |
| Anything else | `candidate` | `last_checked` = `verified_on`, if it has one |

Leave `approved_on` blank, because the company hasn't approved it. A row can only be `approved` if a verified record backs its exact wording. When intake's row says the same thing as a verified record in other words, change the row's `claim` to the source's exact wording, keep the old wording in `notes` as `intake wording: <text>`, and judge the row on that. Don't reword a row if the change would alter a number, the scope or the meaning: hold it instead, and say why.

**Hold a row at `candidate`, even when its source checked out,** and say why in `notes`, if:

- Another row, another live page or a `[confirmed]` fact disagrees with it.
- Its wording doesn't match the source, or makes no sense without words the row leaves out.
- It carries a condition on use, such as naming a customer only when citing their case study. Only sign-off sets `must_qualify`, so put the condition in `notes`.
- The client has an open question on it, or a source is its only basis and is years old.

Set `logo_ok` to `no` on any claim that names a customer, unless the company has already said `yes`. Only the company can set `yes`.

**Respect what's already in the doc:**

- If a row already has this claim, update it instead of adding another. Never change a `forbidden` row, or a row the client approved.
- Never overwrite a `[confirmed]` fact. If a record disagrees with one, keep the fact and add both sources to `Disagreements` in `Sources and intake`.
- Replace a `[TBD: <owner>]` line in a section the research fills, such as `AI visibility baseline` or `Competitors`, once the research answers it.
- Don't write a value that a `see D-` line has cleared. Add your source to that item under `Disagreements` instead.
- Don't add a `forbidden` value to any section, even when you find it on the public web, and that includes a URL in a source tag. Log it in `Disagreements` by `claim_id`, because the client needs to know it's public.

Then report how many records landed in each section, and how many were left out and why.

## 7. Save the run

Create `Research/<YYYY-MM-DD>/`, using the client's local date, and save `config.json`, `scores.json`, `budget_log.json`, `llm/prompts.json` and the martech output, then the ledger split into `evidence/<vendor>.json`. Stage 6 results that a later session adds go in new files, `evidence/<vendor>_stage6.json`, with a `stage6_summary.json`, so the collected ledger stays as it was.

Files reach the Drive connector as inline text, and a large one can stop partway with no error. Split any ledger file over 100 KB into `<vendor>_part1.json`, `<vendor>_part2.json` and so on. Read each file's metadata back and compare its size with the local file, and trash any failed upload. A matching size is enough; you don't need to read the records back. If a file still won't save, say which, and keep the records that reached Brand Context as the record of that run.

Never use the personal `Outside-In Reports` folder, and don't create the two Docs.

## 8. Log it

Add one row to the registry's Run Log tab, once per run, from the session that finishes this section. A collection agent that hands back after Stage 5 adds none. `note` gives the duration from the start of collection to now: `client` = the client's slug, `workflow` = `outside-in-report`, `trigger` = `chat` or `scheduled`, `items` = `0`. `result` is `failed` for a stop in section 1. Otherwise it's `ok`, or `partial` if the cap stopped collection or a source an area depends on was blocked. `run_at` is UTC, so it can fall a day after the folder's date. `note` gives the duration and spend, e.g. `22 min; $3.12 paid research`, then anything left thin.

Finish by telling the caller what was written, and which areas came up thin.
