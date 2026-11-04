# Context Pack MVP: Build Plan for Claude Code

Version: 4 November 2026 (option B). Source: "Big Slick Context Pack MVP Plan" doc.
Estimate: about 18 hours (2.25 days). Judgement estimate, not measured.

## How to use this file

1. Read this whole file, then `plugin/references/shared-definitions.md` (registry and Run Log in §3, Brand Context in §4, review page fields in §8) and `plugin/templates/README.md`.
2. **Do not start T1 until every item in "Resolve before T1" is settled.** Those items change the shared definitions, and the README says to change the Big Slick Shared Definitions Google Doc first, then the copy in this repo.
3. Read the current `SKILL.md` for each skill you are about to change before editing it. client-context, client-onboarding, company-onboarding and brand-voice-extractor are Big Slick core skills (frankdays/bigslick), not in this repo. The template, shared definitions and bigslick-setup are here.
4. Work through the tasks in order, T1 to T5. Each task ends with "Done when" checks. Do not start the next task until those pass.
5. Commit after each task, with the task ID in the message.
6. Do not build anything listed under "Out of scope". If a task seems to need it, stop and ask.
7. No real client names in any tracked file (repo rule). Use `acme` in examples.

## Resolve before T1: conflicts with shared definitions v1.1

This plan was written against an earlier draft spec. The repo's `shared-definitions.md` (v1.1, 3 Nov 2026) differs in these places. Settle each one in the Shared Definitions Google Doc, mirror it here, bump `schema_version`, then build.

| # | Topic | This plan | Shared definitions v1.1 | Suggested resolution |
|---|---|---|---|---|
| 1 | Brand Context headings | 16 sections (T1) | 8 level-2 headings that existing skills find by exact text (§4) | Keep the 8 headings exactly. Map: Company summary to `Company facts`; ICP and personas to `Audience and ICP`; Messaging and Product map to `Products and positioning`; Author voices to `Voices` with `Voice: <Author Name>`; Brand voice to `Style rules`. Add the rest as new headings in v1.2: Visual identity, Marketing plan, SME profiles, Content inventory, AI visibility baseline, Team map, Sources and intake. Keep `Content pillars`, which this plan lacks |
| 2 | Publishing cadence | A Brand Context section | Registry columns `cadence_plan`, `cadence_blog`, `cadence_social` (§3) | Keep cadence in the registry; drop the section |
| 3 | Claims library columns | Adds provenance, status (approved, candidate, forbidden, must-qualify), last checked, expiry, logo permission | `claim_id, claim, source, owner, approved_on, notes`; claims-check treats every row as supported (§4) | Add `status` and the other columns in v1.2 and update claims-check to accept only `approved` rows. Until then, keep candidate claims out of the table |
| 4 | Reviewer list | The review page's Settings tab | Registry `reviewers` column; the review page has no settings (§3, §10) | Use the registry `reviewers` column. T3 fills it from the org chart |
| 5 | Run log | Duration and outside-in spend, format in a separate definitions file | Run Log columns `run_at, client, workflow, trigger, items, result, note` (§3) | Use the §3 columns with `workflow` = `company-onboarding`; put duration in `note` |
| 6 | Registry | T2 creates a registry Sheet | It already exists, written by bigslick-setup (§3) | T2 reads the `Clients` tab by the `client` slug instead of creating one |
| 7 | SME approval fields | Review rows carry whether an SME must approve, which SME, and the SME's yes or no with a reason | No SME fields in `reviews` (§8) | Add the fields in v1.2, or route SME bylines through `reviewers` for the MVP |
| 8 | Provenance tags | On every fact | Not defined | Add the tag format to §4 in v1.2 |

## What the context pack is

For the MVP, the context pack is one **Brand Context** Google Doc per client, kept in that client's Google Drive folder. The three MVP workflows (content plan, long-form and LinkedIn) read it on every scheduled run. Scheduled runs have no one to answer questions, so the doc must answer them.

It is built from the client's own documents, their Drive folder, their website's resources section, and authors' LinkedIn posts. Gap questions cover only what those leave open. Outside-in research joins in V1.

"Done" means the first pilot client's content plan, blog draft and LinkedIn post come out without anyone re-explaining the business. That check happens in the pilot, not in this build.

## Ground rules for every task

- **No client or person detail inside a skill.** Everything specific to a client lives in that client's Brand Context.
- **Every fact carries a provenance tag** (see T1). Drafting skills state `confirmed` and `sourced A` facts plainly, hedge the rest, and never invent a value for a `TBD`.
- **Isolation.** A skill names the client before it works and never carries a number, claim or name from one client to another.
- **Intake never blocks.** Missing inputs become a logged follow-up or a gap question, and work continues on what has arrived.
- **Run log.** Onboarding adds one line to the dashboard run log: when it ran, for which client, whether it finished, what it produced and how long it took. Use the Run Log columns in shared definitions §3 (see "Resolve before T1", item 5).

## Skills this build changes

| Skill | Today | MVP change | Task |
|---|---|---|---|
| client-context | Finds and announces the active pack from local paths | Absorbs client-onboarding. Finds the client, loads Brand Context from Drive, keeps the isolation rules | T2 |
| client-onboarding | Roster, isolation, switching and close-out on local disk | Merged into client-context. The roster becomes the registry Sheet; "switch client" goes | T2 |
| company-onboarding | Interview-first; writes 10 files to local disk | Writes Brand Context to the client's Drive folder; adds intake, reconcile, gap questions, sign-off and provenance tags | T3, T4 |
| brand-voice-extractor | One brand voice | One voice section per author (executives and SMEs), from 10 to 15 samples each | T5 |

No new skills in the MVP. outside-in-report, the client-specific SME review skill and vcmo-content-review are not touched (V1).

---

## T1. Brand Context template and definitions (2 h)

**Goal:** a Google Doc template every client's Brand Context is created from.

**Build:**

1. A template doc with these 16 sections, in this order, each under an H2 heading with exactly this name. Fixed headings let a scheduled run read one section by heading.

| Section | Holds | Main source in the MVP | Read by |
|---|---|---|---|
| Company summary | What they sell, business model, commercial frame, depth tier | All phases | Every skill |
| ICP and personas | Firmographics, buying committee, triggers, customer language | Docs, reviews, gap questions | content-gap-analysis, content-strategy, seo-content-brief |
| Messaging | Category, core argument, value props with proof, objections | Brand docs, sales deck, site | write-blog, LinkedIn Content Engine, brand-review |
| Competitors | The 5 main competitors: where each wins and loses, credit where due | Battlecards, sales deck, gap questions | content-gap-analysis, content-strategy |
| Brand voice | Voice rules, banned words, registers | Brand guidelines | write-blog, editorial-qa, brand-review |
| Visual identity | Logo use, colors, type, imagery, template links | Brand guidelines | lead-magnets, brand-review |
| Author voices | One subsection per executive or SME: topics, stance, samples, channel rules | LinkedIn posts via ConnectSafely, uploaded pieces (T5) | linkedin-workflow, thread-writer |
| Publishing cadence | LinkedIn posts per author per week, blog frequency and length, channels, briefs per content-plan run | Marketing plan, gap questions | content-calendar, content-plan-workflow, linkedin-workflow |
| Marketing plan | Annual and quarterly goals, KPIs, themes, campaigns, launch dates | Client plans | content-strategy, content-calendar, performance-report |
| Claims library | Approved claims in exact wording, source, expiry; forbidden and must-qualify claims; nameable customers | Docs, website, client confirmation | claims-check, write-blog |
| SME profiles | MVP: name, role, topics owned and not owned, byline pieces. Full profile in V1 | Org chart, intake | send-to-review (MVP); sme-content-review (V1) |
| Product map and glossary | Products, solution boundaries, naming, trademarks | Docs, site | claims-check, brand-review |
| Content inventory | Existing assets by funnel stage, top performers, stale pages, keyword targets | Drive folder, website resources, analytics | content-gap-analysis, pillar-content-architecture |
| AI visibility baseline | MVP: left as `TBD (V1: outside-in area 8)` | None in the MVP | ai-search-workflow (V1) |
| Team map | Who holds each marketing lane and decision rights; reviewers themselves live in the registry `reviewers` column | Org chart | send-to-review, bigslick-setup |
| Sources and intake | Where each fact came from; each requested item and its status | All phases | client-context, bigslick-setup |

Dropped for the MVP: the skills-profile section (the workflows already fix which skills run), Metrics baseline and Stack and channels (no MVP skill reads them; they return in V1).

2. **Provenance tags.** Every fact carries one:
   - `confirmed`: the client said so.
   - `sourced A`: from the company's own live website. `sourced B`: from a credible third party (major press, analyst, review platform). `sourced C`: anything weaker.
   - `assumed`: an inference.
   - `TBD (owner)`: unknown, with who will supply it.

   Proposed inline format: `[confirmed]`, `[sourced A: <url>]`, `[assumed]`, `[TBD: <owner>]`. Settle the final format in shared definitions §4 (see "Resolve before T1", item 8).

3. **Claims library format.** One row per claim: exact wording, source, provenance tag, status (`approved`, `candidate`, `forbidden`, `must-qualify`), last checked date, expiry date, logo permission (yes, no or n/a).

4. **Sources and intake table.** One row per requested item: item, tier (Must, Should, Nice), status (`received`, `coming later` with owner and date, `does not exist`, `Big Slick to build`, `left as TBD`), location.

**Done when:**
- The template exists in the Big Slick Drive templates folder.
- A test read of one section by its heading returns only that section. This is the first half of the Brand Context size check (issue 8); record the result for the pilot.

---

## T2. client-context absorbs client-onboarding (4 h)

**Goal:** one skill finds the client, loads their Brand Context from Drive and enforces isolation.

**Build:**

1. Replace the local roster with the existing **Big Slick Registry** (`Clients` tab, shared definitions §3). Look clients up by the `client` slug and read `brand_context_doc_id` and the folder IDs from that row.
2. Find the client in this order: named in the request, then the current Project, then the registry Sheet.
3. Load that client's Brand Context from Drive. If there isn't one, stop and say so, and point to bigslick-setup to onboard them.
4. Keep the isolation rules: name the client before working; never carry a number, claim or name between clients.
5. Remove the "switch client" command. Move whatever else client-onboarding did (roster, close-out) into client-context or the registry Sheet, then retire client-onboarding.
6. Remove every reference to local paths.

**Done when:**
- With two test clients in the registry, a request naming client B loads B's doc and names B before working.
- A request that matches no client stops with a clear message.
- No local-path references remain in client-context, and client-onboarding is retired.

---

## T3. company-onboarding: intake into Drive (6 h)

**Goal:** onboarding asks once for everything the client already has, reads it wherever it lives, and writes Brand Context into the client's Drive folder, never blocking on what's missing.

**Build:**

1. Change the output: create the client's Brand Context from the T1 template in their Drive folder, instead of 10 local files. Add the client to the registry Sheet.
2. **Intake ask.** One request covering this list:

| Item | Feeds | Tier |
|---|---|---|
| Brand guidelines (voice and visual) | Brand voice, Visual identity | Must |
| Subject-matter experts: names, roles, topics they own | SME profiles, Team map | Must |
| Annual marketing plan | Marketing plan | Must |
| Current quarterly plan | Marketing plan | Must |
| Approved claims and proof: statistics with sources, nameable customers, logo permissions, certifications | Claims library | Must |
| Product and solution map with naming rules | Product map and glossary, Messaging | Must |
| LinkedIn profile URL per executive and/or SME, plus other writing samples | Author voices (T5) | Must |
| Org chart | Team map | Must |
| Approval rules: who signs off what, turnaround, legal triggers, required disclaimers | Team map, Claims library | Must |
| Google Drive folder of existing marketing content | Several sections, Content inventory | Should |
| Sales deck, battlecards, objection handling | Messaging, Competitors | Should |
| Customer voice: win and loss notes, call recordings, reviews | ICP and personas | Should |
| Launch and event calendar for the next two quarters, with embargoes | Marketing plan | Should |
| Analytics access or exports (GA4, Search Console), keyword targets | Content inventory | Should |
| Publishing channels (scheduler channels, email platform, CMS) | Publishing cadence | Should |
| Boilerplate, press kit, analyst coverage | Claims library, Messaging | Nice |

3. **Drive folder reader.** Given a folder link, list every document in it, read each one and sort what it finds into sections (a brand guide into Brand voice and Visual identity, a deck into Messaging, a plan into Marketing plan). List anything it could not place, for the client to sort.
4. **Website resources index.** Given the site, find its resources pages (blog, case studies, white papers, webinars, press) and index them into Content inventory. Put customer names, statistics and quotes into the Claims library as `candidate` with their source and grade.
5. **Org chart to team map.** Record who holds each marketing lane and who decides what. Fill the registry `reviewers` column once, at setup. The team map does not keep its own reviewer list.
6. **Missing items.** One follow-up per missing item: "coming later" (owner and date) or "does not exist". For "does not exist", ask whether Big Slick should build it or leave it as TBD. Log every item's status in Sources and intake. Start work at once on what has arrived and merge the rest when it lands.
7. Write the run-log line at the end.

**Done when:**
- On a test client with a Drive folder and a website, Brand Context is created in their folder and populated.
- The unplaced-documents list and the Sources and intake table are filled in.
- Website claims are present as `candidate`, never `approved`.
- The run-log line is written.

---

## T4. Onboarding flow: reconcile, gap questions, sign-off (4 h)

**Goal:** turn what intake gathered into a consistent, tagged Brand Context the client signs off.

**Build:**

1. **Reconcile.** Map every extracted fact to its section and tag its provenance. Where the documents and the website disagree, add the item to a conflict list for the client instead of picking one.
2. **Gap questions.** Ask only about Must sections that are still empty or TBD after intake. Do not ask about anything a document or the website already answered.
3. **Sign-off gate.** Send the client the doc, the conflict list and the candidate claims. This is the only gate. Approving a candidate claim sets it to `approved` and its tag to `confirmed`. A "no" sends the corrections back through reconcile for the affected sections only, not back to the start.
4. bigslick-setup runs this flow to add a company or client; client-context (T2) finds the result on every scheduled run.

**Done when:**
- On the test client, the conflict list shows at least the deliberate doc-versus-site mismatch you planted.
- Gap questions cover only empty Must sections.
- A sign-off with one correction changes only that section, and approved claims flip to `approved` and `confirmed`.

---

## T5. brand-voice-extractor: one section per author (2 h)

**Goal:** a separate voice for every executive and SME who will publish.

**Build:**

1. For each author from intake, read their 10 to 15 most recent LinkedIn posts through ConnectSafely from their profile URL, plus any uploaded writing samples.
2. Write one subsection per author under Author voices: topics, stance, sample excerpts and channel rules. For LinkedIn the channel rules are: one idea, a hook in line one, no link in the body, and a real closing question.
3. If ConnectSafely can't read an author's posts, use the uploads and mark what's missing as `TBD (author)`.

**Done when:**
- Two test authors produce two clearly different subsections.
- An author with no readable posts gets a section built from uploads, with the gaps tagged TBD.

---

## Decided rules to build to

- **Which claims a draft may use (MVP).** Only claims with status `approved`. In V1, claims sourced at grade A or B that pass outside-in's independent re-check also qualify. A source-confirmed claim expires 12 months after it was last checked. Naming a customer is fine when a credible source shows them; using their logo needs the company's approval.
- **Reviewers.** The registry `reviewers` column is the only reviewer list send-to-review uses.
- **SMEs approve** through the same approval workflow as every other reviewer: the review page, or the Approve column in the Content Calendar Sheet for anyone without Claude access. In the MVP, review rows use only the SME approval fields: whether an SME must approve, which SME, and the SME's yes or no with a reason.
- **MVP review chains** (in the workflows, not this build): long-form runs write-blog, editorial-qa, brand-review, claims-check, then send-to-review. LinkedIn runs the draft, claims-check, then send-to-review. Any piece under an SME's byline goes to that SME.
- **Workflow input map** (issue 1): each workflow hands over its answers up front from Brand Context. Tim's by-hand content-plan run on the test client finds the questions each skill asks.

## Out of scope (V1 or pilot)

| Item | Where it goes |
|---|---|
| Outside-in pack mode (company and 5 competitors, $15 cap, LLM visibility, graded records) | V1 |
| sme-content-review, generalizing the client-specific SME review skill, retiring vcmo-content-review, the full SME profile writer | V1 |
| Research tool keys (DataForSEO, Tavily, Perplexity) | V1 |
| AI visibility baseline, Metrics baseline, Stack and channels sections | V1 |
| Mem0 as the context store | V1 |
| Full-flow validation and three smoke-run drafts | Pilot |
| Brand Context size decision (issue 8): summary at the top or section-by-section reads | Pilot, using T1's check |
| Removing duplicate checks across editorial-qa, brand-review and claims-check (issue 9) | Pilot |

## Estimate

| Task | Writing (h) | Test runs and fixes (h) | Total (h) |
|---|---|---|---|
| T1 Brand Context template | 1 | 1 | 2 |
| T2 client-context merge | 1.5 | 2.5 | 4 |
| T3 Intake into Drive | 2 | 4 | 6 |
| T4 Reconcile, gap questions, sign-off | 1.5 | 2.5 | 4 |
| T5 Author voices | 0.5 | 1.5 | 2 |
| **Total** | **6.5** | **11.5** | **18** |

Suggested days: day 1 T1, T2 and the start of T3; day 2 the rest of T3, then T4; day 3 morning T5.
