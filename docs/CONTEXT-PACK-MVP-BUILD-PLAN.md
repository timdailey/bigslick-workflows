# Context Pack MVP: Build Plan for Claude Code

Version: 4 November 2026 (outside-in in the MVP; builds to shared definitions v1.2). Source: "Big Slick Context Pack MVP Plan" doc.
Estimate: about 26.5 hours (3.3 days). Judgement estimate, not measured.

## How to use this file

1. Read this whole file, then `plugin/references/shared-definitions.md` (registry and Run Log in §3, Brand Context in §4, review page fields in §8, services in §9) and `plugin/templates/README.md`.
2. Read the current `SKILL.md` for each skill you are about to change before editing it. Where each skill lives:
   - **This repo:** shared definitions, templates, bigslick-setup, claims-check.
   - **Big Slick core (frankdays/bigslick):** client-context, client-onboarding, company-onboarding, brand-voice-extractor. T4 adds the shipped outside-in-report here.
   - **Frank's personal Claude skills:** outside-in-report today, the starting point for T4.
3. Work through the tasks in order, T1 to T6. Each task ends with "Done when" checks. Do not start the next task until those pass.
4. Commit after each task, with the task ID in the message.
5. Do not build anything listed under "Out of scope". If a task seems to need it, stop and ask.
6. No real client or company names in any tracked file (repo rule). Use `acme` in examples.

## Shared definitions v1.2: decisions this build makes

This plan was first written against an earlier draft spec. These decisions reconcile it with shared definitions v1.1. T1 writes them into v1.2: the Big Slick Shared Definitions Google Doc first (the master copy), then `plugin/references/shared-definitions.md`, with `schema_version` bumped to `1.2`.

| # | Topic | Decision for v1.2 |
|---|---|---|
| 1 | Brand Context headings | Keep the 8 v1.1 headings exactly, in their order, because existing skills find them by text. Add 7 new level-2 headings after them (T1 lists all 15). This plan's names map onto v1.1 as: Company summary to `Company facts`; ICP and personas to `Audience and ICP`; Messaging and Product map to `Products and positioning`; Author voices to `Voices`; Brand voice to `Style rules` |
| 2 | Publishing cadence | No Brand Context section. Cadence stays in the registry columns `cadence_plan`, `cadence_blog` and `cadence_social`; onboarding fills them |
| 3 | Claims library | Add columns `status` (`approved`, `candidate`, `forbidden`, `must_qualify`), `provenance`, `last_checked`, `expires_on` and `logo_ok`. claims-check accepts only `approved` rows that have not expired |
| 4 | Reviewer list | The registry `reviewers` column is the only reviewer list. The review page keeps no settings |
| 5 | Run log | Use the §3 Run Log columns. `workflow` is `company-onboarding` or `outside-in-report`; duration and outside-in spend go in `note` |
| 6 | Registry | Already exists, written by bigslick-setup. client-context reads the `Clients` tab by the `client` slug |
| 7 | SME approval (MVP) | No new review fields. A piece under an SME's byline lists that SME in the item's reviewers, so the SME's yes is the approval. SME-specific fields arrive with sme-content-review in V1 |
| 8 | Provenance tags | Defined in §4: `[confirmed]`, `[sourced A: <url>]`, `[sourced B: <url>]`, `[sourced C: <url>]`, `[assumed]`, `[TBD: <owner>]` |
| 9 | Research tools and storage | DataForSEO stays the required connector (§9). Add Tavily and Perplexity as onboarding-only services that outside-in needs; the day-to-day workflows do not. Add a `Research/` folder to each client folder (§2) for outside-in runs |

## What the context pack is

For the MVP, the context pack is one **Brand Context** Google Doc per client, kept in that client's Google Drive folder. The three MVP workflows (content plan, long-form and LinkedIn) read it on every scheduled run. Scheduled runs have no one to answer questions, so the doc must answer them.

It is built from the client's own documents, their Drive folder, their website's resources section, authors' LinkedIn posts, and outside-in research on the company and 5 competitors. Intake and outside-in start at the same moment. Gap questions cover only what both leave open.

"Done" means the first pilot client's content plan, blog draft and LinkedIn post come out without anyone re-explaining the business. That check happens in the pilot, not in this build.

## Ground rules for every task

- **No client or person detail inside a skill.** Everything specific to a client lives in that client's Brand Context or registry row.
- **Every fact carries a provenance tag** (decision 8). Drafting skills state `confirmed` and `sourced A` facts plainly, hedge the rest, and never invent a value for a `TBD`.
- **Isolation.** A skill names the client before it works and never carries a number, claim or name from one client to another.
- **Intake never blocks.** Missing inputs become a logged follow-up or a gap question, and work continues on what has arrived.
- **Run log.** Onboarding and every outside-in run add one Run Log row (decision 5).

## Skills this build changes

| Skill | Where | Today | MVP change | Task |
|---|---|---|---|---|
| claims-check | This repo | Treats every claims row as supported | Accepts only `approved`, unexpired rows | T1 |
| client-context | Core | Finds and announces the active pack from local paths | Absorbs client-onboarding. Finds the client in the registry, loads Brand Context from Drive, keeps the isolation rules | T2 |
| client-onboarding | Core | Roster, isolation, switching and close-out on local disk | Merged into client-context, then retired | T2 |
| company-onboarding | Core | Interview-first; writes 10 files to local disk | Writes Brand Context to the client's Drive folder; adds intake, reconcile, gap questions, sign-off and provenance tags | T3, T5 |
| outside-in-report | Frank's personal skills, shipped to core | Prospecting report on a company and 7 rivals, rendered as two Docs | Ships in Big Slick core with a new pack mode: the company and 5 competitors, a $15 cap, graded records written into Brand Context | T4 |
| brand-voice-extractor | Core | One brand voice | One `Voice:` section per author (executives and SMEs), from 10 to 15 samples each | T6 |

The client-specific SME review skill and vcmo-content-review are not touched (V1).

---

## T1. Shared definitions v1.2, Brand Context template and claims-check (3.5 h)

**Goal:** the spec, the template and claims-check all match the decisions above, so every later task builds on fixed names.

**Build:**

1. **Shared definitions v1.2.** Write the nine decisions into the Big Slick Shared Definitions Google Doc, then mirror them in `plugin/references/shared-definitions.md`. Bump `schema_version` to `1.2` in §3 and the templates, and add a "Changes in v1.2" section.
2. **Brand Context template** (`plugin/templates/brand-context.md`). These 15 level-2 headings, in this order. The first 8 are unchanged from v1.1.

| # | Heading | Holds | Main source | Read by |
|---|---|---|---|---|
| 1 | Company facts | What they sell, business model, commercial frame, size, funding | All phases | Every skill |
| 2 | Audience and ICP | Firmographics, buying committee, triggers, customer language | Docs, reviews, outside-in, gap questions | content-gap-analysis, content-strategy, seo-content-brief |
| 3 | Products and positioning | Products and solution boundaries, naming and trademarks, category, core argument, value props with proof, objections | Docs, sales deck, site, outside-in | write-blog, LinkedIn Content Engine, brand-review, claims-check |
| 4 | Content pillars | One level-3 heading per pillar | Marketing plan, gap questions | content-plan-workflow |
| 5 | Competitors | The 5 competitors: where each wins and loses, credit where due | Outside-in (T4), battlecards, sales deck | content-gap-analysis, content-strategy |
| 6 | Claims library | The claims table (decision 3) | Docs, website, outside-in, client sign-off | claims-check, write-blog |
| 7 | Voices | One `Voice: <Author Name>` per executive or SME: topics, stance, samples, channel rules | LinkedIn posts, uploads (T6) | linkedin-workflow, thread-writer |
| 8 | Style rules | Voice rules, banned words, registers, house style | Brand guidelines | write-blog, editorial-qa, brand-review |
| 9 | Visual identity | Logo use, colors, type, imagery, template links | Brand guidelines | lead-magnets, brand-review |
| 10 | Marketing plan | Annual and quarterly goals, KPIs, themes, campaigns, launch dates | Client plans | content-strategy, content-calendar, performance-report |
| 11 | SME profiles | MVP: name, role, topics owned and not owned, byline pieces. Full profile in V1 | Org chart, intake | send-to-review (decision 7); sme-content-review (V1) |
| 12 | Content inventory | Existing assets by funnel stage, top performers, stale pages, keyword targets | Drive folder, website resources, analytics, outside-in | content-gap-analysis, pillar-content-architecture |
| 13 | AI visibility baseline | Prompt set, mention share against the 5 competitors, questions rivals win | Outside-in area 8 (T4) | content-gap-analysis, content-strategy; ai-search-workflow in V1 |
| 14 | Team map | Who holds each marketing lane and decision rights | Org chart | bigslick-setup |
| 15 | Sources and intake | Where each fact came from; each requested item and its status | All phases | client-context, bigslick-setup |

   Add the claims table with the v1.2 columns, and a Sources and intake table with: item, tier (`must`, `should`, `nice`), status (`received`, `coming_later`, `does_not_exist`, `big_slick_to_build`, `left_tbd`), owner and date, location.
3. **claims-check.** Read the `status` and `expires_on` columns. Only `approved` rows that haven't expired support a claim. A draft that uses a `candidate`, `forbidden` or expired claim gets a flag, and a `must_qualify` claim used without its qualifier gets a flag.
4. **Size check** (issue 8, first half). Read one section of a full-size test Brand Context by its heading, and record whether only that section came back. The decision on how workflows load the doc happens in the pilot.

**Done when:**
- Both copies of the shared definitions say v1.2 and match each other.
- A doc created from the template has all 15 headings, and existing skills still find the first 8.
- claims-check passes a draft using an approved claim and flags one using a candidate claim and one using an expired claim.
- The size-check result is recorded.

---

## T2. client-context absorbs client-onboarding (4 h)

**Goal:** one skill finds the client, loads their Brand Context from Drive and enforces isolation.

**Build:**

1. Replace the local roster with the Big Slick Registry (`Clients` tab). Look clients up by the `client` slug and read `brand_context_doc_id` and the folder IDs from that row.
2. Find the client in this order: named in the request, then the current Project, then the registry.
3. Load that client's Brand Context from Drive. If there isn't one, stop and say so, and point to bigslick-setup.
4. Keep the isolation rules: name the client before working; never carry a number, claim or name between clients.
5. Remove the "switch client" command. Move whatever else client-onboarding did (roster, close-out) into client-context or the registry (`status` = `archived` for close-out), then retire client-onboarding.
6. Remove every reference to local paths.

**Done when:**
- With two test clients in the registry, a request naming client B loads B's doc and names B before working.
- A request that matches no client stops with a clear message.
- No local-path references remain, and client-onboarding is retired.

---

## T3. company-onboarding: intake into Drive (6 h)

**Goal:** onboarding asks once for everything the client already has, reads it wherever it lives, and writes Brand Context into the client's Drive folder, never blocking on what's missing.

**Build:**

1. Change the output: fill the client's Brand Context (created by bigslick-setup from the T1 template) instead of writing 10 local files.
2. **Intake ask.** One request covering this list:

| Item | Feeds | Tier |
|---|---|---|
| Brand guidelines (voice and visual) | Style rules, Visual identity | Must |
| Subject-matter experts: names, roles, topics they own | SME profiles, Team map | Must |
| Annual marketing plan | Marketing plan, Content pillars | Must |
| Current quarterly plan | Marketing plan | Must |
| Approved claims and proof: statistics with sources, nameable customers, logo permissions, certifications | Claims library | Must |
| Product and solution map with naming rules | Products and positioning | Must |
| LinkedIn profile URL per executive and/or SME, plus other writing samples | Voices (T6) | Must |
| Org chart | Team map, registry `reviewers` | Must |
| Approval rules: who signs off what, turnaround, legal triggers, required disclaimers | Team map, Claims library | Must |
| Google Drive folder of existing marketing content | Several sections, Content inventory | Should |
| Sales deck, battlecards, objection handling | Products and positioning, Competitors | Should |
| Customer voice: win and loss notes, call recordings, reviews | Audience and ICP | Should |
| Launch and event calendar for the next two quarters, with embargoes | Marketing plan | Should |
| Analytics access or exports (GA4, Search Console), keyword targets | Content inventory | Should |
| Publishing channels and cadence (scheduler channels, email platform, CMS; posts per week and month) | Registry `channels`, `cadence_*` | Should |
| Boilerplate, press kit, analyst coverage | Claims library, Products and positioning | Nice |

3. **Drive folder reader.** Given a folder link, list every document in it, read each one and sort what it finds into sections (a brand guide into Style rules and Visual identity, a deck into Products and positioning, a plan into Marketing plan). List anything it could not place, for the client to sort.
4. **Website resources index.** Given the site, find its resources pages (blog, case studies, white papers, webinars, press) and index them into Content inventory. Add customer names, statistics and quotes to the Claims library with `status` = `candidate`, plus their source and grade.
5. **Org chart to team map.** Record who holds each marketing lane and who decides what. Fill the registry `reviewers` column once, at setup.
6. **Cadence.** Fill the registry `cadence_plan`, `cadence_blog` and `cadence_social` columns from the marketing plan, or from a gap question.
7. **Missing items.** Outside-in research (T4) fills what it can. Then one follow-up per item still missing: "coming later" (owner and date) or "does not exist". For "does not exist", ask whether Big Slick should build it or leave it as TBD. Log every item's status in Sources and intake. Start work at once on what has arrived and merge the rest when it lands.
8. Write a Run Log row at the end (`workflow` = `company-onboarding`).

**Done when:**
- On a test client with a Drive folder and a website, Brand Context is populated in their folder.
- The unplaced-documents list and the Sources and intake table are filled in.
- Website claims are present with `status` = `candidate`, never `approved`.
- The registry `reviewers` and cadence columns are filled.
- The Run Log row is written.

---

## T4. outside-in-report: ship it with pack mode (7 h)

**Goal:** ship your existing outside-in-report skill in Big Slick core, with a pack mode that writes graded findings into Brand Context instead of two prospect Docs. It starts at the same moment as intake, so the research is usually done by the time the client's documents arrive.

**Start from the existing skill.** Copy Frank's personal outside-in-report skill (`SKILL.md`, `references/`, `scripts/`) into Big Slick core. Most of what pack mode needs is already there; keep it:

- The 17 research areas (`references/areas.md`) and the evidence record format and A/B/C grades (`references/evidence-schema.md`).
- The LLM visibility prompt set run through DataForSEO (`references/prompt-set.md`, area 8).
- The budget planner and spend log (`scripts/budget.py`, `references/budget.md`), including the rule that collection stops at 90% of the budget.
- Reuse of a run from the last 30 days, re-checking only the records that are used first.
- The independent re-check by fresh agents of every A and B record.
- `score_proof.py` and `detect_stack.py`.

**Generalize the shipped copy:**

- Remove everything about Frank and prospecting from the skill text: his name, the reference-model edition named after a real company, and the pointer to cmo-pitch-report. The report mode stays, written for any consultant or marketer.
- Remove every real company name from examples (repo rule).

**Add pack mode:**

1. **Mode flag `pack`,** called by company-onboarding. The company and 5 competitors (report mode keeps 7). No pause to confirm the competitor list: it goes to the client at sign-off (T5) instead.
2. **Fixed $15 cap** instead of asking for a budget. Expect $2 to $4 a run: recent 8-company runs spent $2.11 to $4.93, with free web search and page fetching doing most of the collection. The cost planner overstates real spend by about three times (placeholder rates), so it is not a limit.
3. **Research tools.** Check DataForSEO, Tavily and Perplexity before spending anything (decision 9). If one is missing, stop outside-in and name it. Onboarding carries on with the client's documents, and anything that depends on the research is tagged `TBD` until outside-in runs.
4. **New ending.** Skip the two Docs (Stages 7 and 8 and their layouts). Write each record into its Brand Context section, in a neutral voice (not the admirer's voice the report uses), keeping its grade and tagged `sourced`, never `confirmed`. The client confirms at sign-off (T5).
5. **Where each area lands:**

| Outside-in area | Brand Context section |
|---|---|
| 1 Homepage and pillar messaging | Products and positioning |
| 2 Customer proof inventory, 3 Proprietary research, 4 Boilerplate figures | Claims library: `approved` when grade A or B and re-checked; otherwise `candidate` |
| 5 Analyst standing, 6 Ownership and funding | Competitors, Company facts |
| 7 Peer reviews | Audience and ICP (customer language), Competitors |
| 8 LLM visibility | AI visibility baseline |
| 9 llms.txt and robots.txt, 10 Localization, 15 Video recency, 16 Trust pages | Content inventory |
| 11 Authorship, 12 Founder story | Voices (candidate authors), Products and positioning |
| 13 Martech stack | Kept in the run folder for V1 (Stack and channels) |
| 14 Paid media mix, 17 Defunct or acquired sites | Competitors |

6. **Client-safe storage.** Save each run under the client's `Research/` folder (decision 9), not the personal "Outside-In Reports" folder. Reuse looks only in that client's folder, so one client's research is never reused for another, even when they share competitors. Today the full ledger is too large for the Drive connector and is zipped into chat. In pack mode, save the ledger split per vendor (`evidence/<vendor>.json`) into `Research/`, or rely on the Brand Context records for reuse. Pick whichever works on the test run and note which.
7. Write a Run Log row (`workflow` = `outside-in-report`, spend and duration in `note`).

**Done when:**
- The shipped skill has no mention of Frank, prospecting-only framing or real company names, and report mode still produces its two Docs on a test company.
- One real pack-mode run on the test client finishes under $15, and every record lands in its mapped section with its grade and a `sourced` tag.
- A run with one research tool missing stops outside-in, names the tool, and intake carries on.
- A second run on the same client within 30 days reuses that client's run, and a run on a second client with an overlapping competitor does not.

---

## T5. Onboarding flow: reconcile, gap questions, sign-off (4 h)

**Goal:** turn what intake and outside-in gathered into a consistent, tagged Brand Context the client signs off.

**Build:**

1. **Reconcile.** Map every extracted fact and research record to its section and tag its provenance. Where the documents, the website and the research disagree, add the item to a conflict list for the client instead of picking one.
2. **Gap questions.** Ask only about Must items that are still empty or TBD after intake and outside-in. Do not ask about anything a document, the website or the research already answered.
3. **Sign-off gate.** Send the client the doc, the conflict list, the competitor list and the candidate claims. This is the only gate. Approving a candidate claim sets `status` to `approved` and its provenance to `confirmed`. A "no" sends the corrections back through reconcile for the affected sections only, not back to the start.
4. bigslick-setup runs this flow to add a company or client; client-context (T2) finds the result on every scheduled run.

**Done when:**
- On the test client, the conflict list shows at least the deliberate doc-versus-site mismatch you planted.
- Gap questions cover only empty Must items.
- A sign-off with one correction changes only that section, and approved claims flip to `approved` and `confirmed`.

---

## T6. brand-voice-extractor: one section per author (2 h)

**Goal:** a separate voice for every executive and SME who will publish.

**Build:**

1. For each author from intake, read their 10 to 15 most recent LinkedIn posts through ConnectSafely from their profile URL, plus any uploaded writing samples.
2. Write one `Voice: <Author Name>` section per author under Voices: topics, stance, sample excerpts and channel rules. For LinkedIn the channel rules are: one idea, a hook in line one, no link in the body, and a real closing question. Add each author to the registry `authors` column with the same name.
3. If ConnectSafely can't read an author's posts, use the uploads and mark what's missing as `[TBD: <author>]`.

**Done when:**
- Two test authors produce two clearly different `Voice:` sections, and both names are in the registry `authors` column.
- An author with no readable posts gets a section built from uploads, with the gaps tagged TBD.

---

## Decided rules to build to

- **Which claims a draft may use.** A claim the company approved, or one a credible source confirms (grade A: the company's own live website; grade B: major press, an analyst or a review platform) that also passed outside-in's independent re-check. Everything else is unapproved: grade C findings, anything not re-checked, forbidden claims and expired claims. A source-confirmed claim expires 12 months after `last_checked`. Naming a customer is fine when a credible source shows them; using their logo needs the company's approval (`logo_ok`).
- **Reviewers.** The registry `reviewers` column is the only reviewer list send-to-review uses.
- **SMEs approve** through the same approval workflow as every other reviewer: the review page, or the `approve` column in the Content Calendar for anyone without Claude access. In the MVP, a piece under an SME's byline lists that SME as a reviewer (decision 7).
- **MVP review chains** (in the workflows, not this build): long-form runs write-blog, editorial-qa, brand-review, claims-check, then send-to-review. LinkedIn runs the draft, claims-check, then send-to-review.
- **Workflow input map** (issue 1): each workflow hands over its answers up front from Brand Context and the registry. Tim's by-hand content-plan run on the test client finds the questions each skill asks.

## Out of scope (V1 or pilot)

| Item | Where it goes |
|---|---|
| sme-content-review, generalizing the client-specific SME review skill, retiring vcmo-content-review, the full SME profile writer, SME fields on review rows | V1 |
| Metrics baseline, Stack and channels sections | V1 |
| Mem0 as the context store | V1 |
| Full-flow validation and three smoke-run drafts | Pilot |
| Brand Context size decision (issue 8): summary at the top or section-by-section reads | Pilot, using T1's check |
| Removing duplicate checks across editorial-qa, brand-review and claims-check (issue 9) | Pilot |

## Estimate

| Task | Writing (h) | Test runs and fixes (h) | Total (h) |
|---|---|---|---|
| T1 Shared definitions v1.2, template, claims-check | 2 | 1.5 | 3.5 |
| T2 client-context merge | 1.5 | 2.5 | 4 |
| T3 Intake into Drive | 2 | 4 | 6 |
| T4 Ship outside-in-report with pack mode, one real run | 2.5 | 4.5 | 7 |
| T5 Reconcile, gap questions, sign-off | 1.5 | 2.5 | 4 |
| T6 Author voices | 0.5 | 1.5 | 2 |
| **Total** | **10** | **16.5** | **26.5** |

T1 grows by 1.5 hours for the spec and claims-check changes, and T4 by 1 hour for shipping and generalizing the skill.

Suggested days: day 1 T1 and T2; day 2 T3, then start T4 (start the real outside-in run early so it finishes while you work); day 3 the rest of T4, T5, and T6 into the morning of day 4.
