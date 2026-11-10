# Big Slick Shared Definitions

Version 1.2 · Nov 10, 2026. The Google Doc "Big Slick Shared Definitions" in the BigSlick Harness folder is the master copy; this file mirrors it.

Workflows never call each other. They hand off through Drive and the review page, so every skill must read and write the exact names below. A name that differs between two skills breaks a hand-off without any error. To change a name, change this spec first, then every skill that uses it, then bump `schema_version`.

## 1. Conventions

- **Machine values** (statuses, channels, content types, connector names) are lowercase `snake_case`, written exactly as listed. People may see friendlier labels, but cells and database fields hold these values.
- **Dates** are `YYYY-MM-DD` in the client's timezone (registry column `timezone`).
- **Timestamps** are ISO 8601 in UTC, e.g. `2026-11-03T14:05:00Z`.
- **Links** are full URLs. **IDs** are the Google Drive file or folder ID, not the URL.
- **Lists** inside one cell are comma-separated with a space: `blog, linkedin`.
- Never put one client's data in another client's folder, row or review page.

## 2. Drive layout

The install's admin folder holds the registry. Each client gets one folder under `Clients`.

```
Big Slick/
├── Big Slick Registry            (Google Sheet: tabs Clients, Run Log)
└── Clients/
    └── <Client Name>/
        ├── Brand Context         (Google Doc)
        ├── Content Calendar      (Google Sheet: tab Calendar)
        ├── Onboarding Checklist  (Google Sheet: tab Checklist)
        ├── Onboarding Questionnaire (Google Sheet: tab Questions; shared with the client contact)
        ├── Intake/               (the client's documents; shared with the client contact)
        ├── Briefs/
        ├── Drafts/
        ├── Approved/
        └── Research/             (outside-in runs for this client only)
```

A single-company install is the same layout with one client. `Research/` is used only during onboarding, by outside-in-report. Find it by name under `client_folder_id`, and create it if a client set up before v1.2 doesn't have one.

### File names

| Folder | Name format | Example |
| --- | --- | --- |
| Briefs | `<date> <title> (<item_id>)` | `2026-11-19 Meeting-free mornings (acme-0042)` |
| Drafts | `<date> <title> (<item_id>) v<version>` | `2026-11-19 Meeting-free mornings (acme-0042) v2` |
| Approved | `<date> <title> (<item_id>)` | `2026-11-19 Meeting-free mornings (acme-0042)` |

`<date>` is the planned publish date. The `(<item_id>)` suffix is how a run finds an item's files; never remove it. A declined draft stays in Drafts; the redraft is a new file with the next version number.

### Intake and the onboarding questionnaire

`Intake/` holds every document the client provides. The client puts files there, and onboarding saves a copy there of any file that arrives another way (uploaded in chat, forwarded by email), so a later run can find it. `Sources and intake` in Brand Context lists each file read, with the modified time read, under `Files read`, so a later pass reads only what's new or changed.

`Onboarding Questionnaire` collects what is an answer rather than a document: one row per question, with `question_id`, `question`, `why_we_ask`, `answer`, `answered_by`, `answered_on` and `feeds`. bigslick-setup creates it from `templates/onboarding-questionnaire.csv` and fills in `contact`, `website` and `location`. The client fills in the rest. company-onboarding reads every answer into the place its `feeds` column names, tagged `[confirmed]`. The intake request links to both. setup shares only these two with the client contact, after the user agrees. Neither has a registry column: find them by name under `client_folder_id`.

### Onboarding checklist

`Onboarding Checklist` is a Google Sheet in the client's folder, tab `Checklist`, one row per onboarding step in process order. bigslick-setup creates it from `templates/onboarding-checklist.csv` and keeps it until go-live. Find it by name under `client_folder_id`, like `Research/`; it has no registry column. It shows where onboarding stands. `Sources and intake` in Brand Context stays the record of each requested item, and the checklist only summarises it.

| Column | Values / format |
| --- | --- |
| step_id | fixed id from the template; never change, reuse or reorder |
| phase | `1 Connect`, `2 Intake`, `3 Sign-off` (onboarding), then `4 Workflow phase`, which the workflow skills fill after onboarding |
| step | what the step is, in plain words |
| owner | `big_slick` (Claude does it), `user` (the person running setup), `client` (someone at the client) |
| status | `not_started`, `in_progress`, `waiting`, `done`, `skipped`, `blocked` |
| waiting_on | who or what the step needs, with a date if one was given; blank unless `waiting` or `blocked` |
| detail | one line of progress, e.g. `6 of 8 must items received; 3 unplaced files` |
| link | the doc, folder, page or message the step produced |
| updated_at | when a run last changed the row |

The skill that does a step updates its row when it starts and when it finishes or stops: `in_progress` while working, `waiting` when it needs someone, `blocked` when a stop needs fixing first, `done` when its outcome is in place, `skipped` when it doesn't apply (for example `sme_profiles` for a client with no SMEs). Update only the rows below that the skill owns, and never mark a row `done` that the outcome doesn't support.

| Rows | Updated by |
| --- | --- |
| `connectors`, `location`, `drive_setup`, `review_page`, `pillars`, `strategy`, `scheduled_tasks`, `project`, `first_run` | bigslick-setup (`location` also by company-onboarding, if setup left it blank) |
| `intake_request`, `documents`, `website`, `reconcile`, `signoff_sent`, `signoff_applied` | company-onboarding (`drive-intake.md`, `sign-off.md`) |
| `research` | outside-in-report in pack mode |
| `voices` | brand-voice-extractor |
| `sme_profiles` | sme-content-review's profile writer |
| `pillars`, `strategy` | also content-plan-workflow, which builds the pillars and has the client confirm them and the first plan |

After updating it, show the user the checklist in the chat as a table (step, status, waiting on, detail), with a link to the Sheet. When the user comes back to onboarding, start by reading and showing it, then carry on from the first row that isn't `done` or `skipped`.

## 3. Big Slick Registry

### Tab `Clients` (one row per client)

| Column | Type | Allowed values / format | Written by |
| --- | --- | --- | --- |
| client | text | lowercase slug, letters, digits and hyphens, unique, never changes | setup |
| client_name | text | display name | setup |
| status | value | `active`, `paused`, `archived` | setup, people |
| timezone | text | IANA name, e.g. `America/New_York` | setup |
| client_folder_id | Drive ID | the `<Client Name>` folder | setup |
| brand_context_doc_id | Drive ID | | setup |
| calendar_sheet_id | Drive ID | | setup |
| briefs_folder_id | Drive ID | | setup |
| drafts_folder_id | Drive ID | | setup |
| approved_folder_id | Drive ID | | setup |
| review_page_url | URL | the client's review page | setup |
| reviewers | list | emails; the only reviewer list | setup, people |
| approver_in_chat | list | emails of people whose go-ahead in a chat counts as approval | setup, people |
| channels | list | values from §5 | setup, people |
| content_types | list | values from §5 | setup, people |
| cadence_plan | text | `monthly` or `quarterly` | content-plan-workflow (first plan), people |
| cadence_blog | number | posts per month | content-plan-workflow (first plan), people |
| cadence_social | number | posts per week, per author | content-plan-workflow (first plan), people |
| authors | list | names, each matching a `Voice:` section in Brand Context | setup, people |
| optional_connectors | list | values from §5 that this client uses | setup, people |
| schema_version | text | `1.2` | setup |
| seo_location | text | The client's location: the country or region whose search results matter, as a DataForSEO location name, e.g. `United States`. Asked at setup; if blank, company-onboarding asks before any research | setup, company-onboarding, people |
| seo_language | text | DataForSEO language name, e.g. `English`; defaults to the location's main language | setup, company-onboarding, people |
| seo_budget_per_run | number | most DataForSEO calls one run may make, e.g. `25` | setup, people |

The registry is the one home for settings. Runs read it on every start, so a change takes effect on the next run. bigslick-setup writes the registry; client-context reads the `Clients` tab by the `client` slug. Publishing cadence lives in the `cadence_*` columns, not in Brand Context, and the first content plan fills them. Onboarding also fills `reviewers` once, at setup; send-to-review uses no other list.

### Tab `Run Log` (one row per run, newest at the bottom)

| Column | Type | Format |
| --- | --- | --- |
| run_at | timestamp | UTC |
| client | text | registry slug |
| workflow | value | skill name, e.g. `content-plan-workflow`, `company-onboarding`, `outside-in-report` |
| trigger | value | `chat` or `scheduled` |
| items | number | calendar rows created or updated |
| result | value | `ok`, `partial`, `failed` |
| note | text | one line; what failed and where, if anything. `company-onboarding` and `outside-in-report` also give the duration and, for outside-in, the paid spend, e.g. `14 min; $3.12 paid research` |

## 4. Brand Context doc

Skills find sections by their exact heading text, and load only the sections they name through client-context, because a filled doc runs past 100,000 characters. Keep the headings (level 2) as written and in this order; add content under them freely. Headings 1 to 8 are unchanged from v1.1.

1. `Company facts`
2. `Audience and ICP`
3. `Products and positioning`
4. `Content pillars` (one level-3 heading per pillar)
5. `Competitors`
6. `Claims library` (a table, below)
7. `Voices` (one level-3 heading per author: `Voice: <Author Name>`; the template writes it as `Voice: [Author Name]`)
8. `Style rules` (house style, banned words, formatting)
9. `Visual identity`
10. `Marketing plan`
11. `SME profiles` (one level-3 heading per SME)
12. `Content inventory`
13. `AI visibility baseline`
14. `Team map` (who holds each marketing lane and decision rights; no reviewer list)
15. `Sources and intake` (a table, below)

### Provenance tags

Every fact in Brand Context ends with one tag, written exactly like this:

| Tag | Meaning | Drafting skills may |
| --- | --- | --- |
| `[confirmed]` | The client said so or approved it | State it plainly |
| `[sourced A: <url>]` | Outside-in evidence, grade A | State it plainly |
| `[sourced B: <url>]` | Outside-in evidence, grade B | State it with a hedge |
| `[sourced C: <url>]` | Outside-in evidence, grade C | State it with a hedge |
| `[assumed]` | Inference, not yet confirmed | State it with a hedge |
| `[TBD: <owner>]` | Unknown; the named person owns the answer | Never fill it in |

Grade A is the company's own live website. Grade B is a credible third party: major press, an analyst or a review platform. Outside-in records enter as `sourced`, never `confirmed`; the client confirms them at sign-off. A finding from paid data with no public page (AI visibility, paid media) uses the Drive link to its file in the client's `Research/` run folder as the `<url>`. A `[TBD: <owner>]` owner is a person or a role at the client, never a process such as outside-in research.

### Claims library table

| Column | Meaning |
| --- | --- |
| claim_id | `C-001`, `C-002`, … never reused |
| claim | The exact wording |
| source | Link or document that proves it |
| owner | Who approved it, or `outside-in` for a claim a credible source confirmed |
| approved_on | Date; blank until `status` is `approved` |
| notes | Limits, e.g. "US customers only". For a `must_qualify` row, the qualifier wording a draft must include |
| status | `approved`, `candidate`, `forbidden`, `must_qualify` |
| provenance | One provenance tag |
| last_checked | Date the source was last re-checked; blank for a claim the company approved |
| expires_on | Date, or blank if none. A claim a credible source confirmed expires 12 months after `last_checked` |
| logo_ok | `yes`, `no`, or blank for a claim that names no customer. Only the company can set `yes` |

A row is `approved` in one of two ways: the company approved it (provenance `[confirmed]`), or a grade A or B source confirms it and it passed outside-in's independent re-check. Everything else outside-in or intake finds is `candidate` until the company approves it, which sets `status` to `approved` and `provenance` to `[confirmed]`.

`claims-check` passes a claim only if it matches an `approved` row, or a `must_qualify` row with its qualifier in the draft, and `expires_on` is blank or later than today. A `candidate`, `forbidden` or expired row is a flag, and so is anything in a draft that this table doesn't back. A claim may name a customer without `logo_ok`; showing their logo needs `logo_ok` = `yes`.

### Sources and intake table

One row per item onboarding asked for.

| Column | Meaning |
| --- | --- |
| item | The requested item |
| tier | `must`, `should`, `nice` |
| status | `received`, `coming_later`, `does_not_exist`, `big_slick_to_build`, `left_tbd` |
| owner | Who is responsible for it |
| date | Date it is due, or date it was received |
| location | Link to the item in Drive, once received |

## 5. Value lists

| List | Values |
| --- | --- |
| content_type | `blog_post`, `guide`, `case_study`, `gated_asset`, `social_post`, `newsletter`, `email` |
| channel | `blog`, `linkedin`, `x`, `community`, `newsletter`, `email` |
| optional connectors | `gmail`, `slack`, `typefully`, `taplio`, `hubspot`, `granola`, `mailchimp`, `mailerlite`, `kit` |
| decision | `yes`, `no` |

## 6. Content Calendar (tab `Calendar`)

One row per content item. Never delete a row; set its status instead.

| Column | Type | Format / values | Written by |
| --- | --- | --- | --- |
| item_id | text | `<client>-<4-digit number>`, e.g. `acme-0042`; next number = highest existing + 1 | the workflow that adds the row |
| publish_date | date | planned publish date | content plan, people |
| content_type | value | §5 | content plan |
| channel | value | §5 | content plan |
| title | text | working title | content plan, people |
| keyword | text | target keyword; blank for social | content plan |
| pillar | text | a `Content pillars` heading from Brand Context | content plan |
| author | text | an `authors` name from the registry | content plan, people |
| source_item_id | text | for repurposed pieces, the item_id they came from | repurpose |
| brief_link | URL | | content plan |
| draft_link | URL | the latest version | long-form, social, repurpose |
| final_link | URL | the file in Approved | publish-approved |
| published_url | URL | where it went live, once known | publish-approved, people |
| status | value | §7 | each workflow, per §7 |
| version | number | draft version, starts at 1 | long-form, social, repurpose |
| approve | value | `yes`, `no` or blank: the fallback for reviewers without Claude access | people |
| reason | text | required when `approve` is `no` | people |
| updated_at | timestamp | last change by a run | every workflow |
| sme_approve | value | `yes`, `no` or blank: the fallback for an SME without Claude access (§8) | people |
| sme_reason | text | required when `sme_approve` is `no` | people |

## 7. Statuses

One `status` column, ten values. Topics and drafts have separate approvals, so "approved" is never ambiguous.

| Status | Meaning | Set by | Next |
| --- | --- | --- | --- |
| `planned` | Topic proposed (send-to-review puts it on the review page; the calendar status stays `planned` until decided) | content-plan-workflow | `topic_approved`, `topic_declined` |
| `topic_approved` | Topic accepted; ready to draft | publish-approved syncing a yes, or content plan when the person in chat is in `approver_in_chat` | `drafted` |
| `topic_declined` | Topic rejected | publish-approved syncing a no | end |
| `drafted` | Draft saved, not yet sent for review | long-form, social, repurpose | `in_review` |
| `in_review` | Waiting on a reviewer | send-to-review | `approved`, `declined` |
| `approved` | Draft accepted | the review page, or publish-approved reading the `approve` column | `scheduled`, `ready_to_post` |
| `declined` | Draft rejected, with a reason | the review page, or publish-approved reading the `approve` column | `drafted` (redraft, version + 1) |
| `scheduled` | Queued in a connected scheduler | publish-approved | `published` |
| `ready_to_post` | Final in Approved, to post by hand | publish-approved | `published` |
| `published` | Live | publish-approved, or a person | end |

A workflow only moves a row along an arrow in this table. If a row isn't in the status a step expects, the step skips it and notes that in the run log.

## 8. Review page database

Collection `reviews`, one document per item version. Document id: `<item_id>-v<version>`, e.g. `acme-0042-v2`; topics use `<item_id>-topic`.

| Field | Type | Values / format | Written by |
| --- | --- | --- | --- |
| client | text | registry slug | send-to-review |
| item_id | text | calendar item_id | send-to-review |
| kind | value | `topic` or `draft` | send-to-review |
| version | number | 0 for topics | send-to-review |
| content_type | value | §5 | send-to-review |
| channel | value | §5 | send-to-review |
| title | text | | send-to-review |
| author | text | | send-to-review |
| publish_date | date | | send-to-review |
| doc_link | URL | brief (topic) or draft | send-to-review |
| claims_flags | list | claim problems found by claims-check, one line each; empty if none | send-to-review |
| sme_verdict | value | `approve`, `approve_with_edits`, `revise`, `reject`; absent until sme-content-review has run | send-to-review |
| sme_flags | list | issues found by sme-content-review, one line each with its severity (Blocker, Major, Minor); empty if none | send-to-review |
| reviewer_decisions | list | one entry per required reviewer, each with `reviewer`, `decision`, `reason`, `decided_by` and `decided_at` | send-to-review, review page, publish-approved |
| status | value | `in_review`, `approved`, `declined`, then `scheduled`, `ready_to_post`, `published` | send-to-review, review page, publish-approved |
| decision | value | `yes`, `no`, or absent until decided | review page |
| reason | text | required when decision is `no` | review page |
| decided_by | text | the reviewer's user id | review page |
| decided_at | timestamp | | review page |
| synced_at | timestamp | when publish-approved copied the outcome to the calendar | publish-approved |

The review page writes the decision; publish-approved copies it to the calendar row (§7) and sets `synced_at`. Every write pins the version it read.

sme-content-review runs on every long-form and LinkedIn draft. A piece under an SME's byline, or in an SME's topics where the review finds a Blocker or a Major on a technical claim, lists that SME as a reviewer on the item, so the SME's yes is the approval.

send-to-review writes one `reviewer_decisions` entry with `reviewer` = `any`, which anyone in the registry `reviewers` column can fill, plus one entry with the SME's email when the piece lists an SME. `reason` is required on a `no`. The item-level `decision`, `reason`, `decided_by`, `decided_at` and `status` then record the outcome: `approved` once every entry is `yes`, `declined` as soon as any entry is `no`. SMEs approve like any other reviewer: on the review page, or in the calendar's `sme_approve` column (`approve` fills the `any` entry).

## 9. Required services and connector rule

Two services are required to use Big Slick:

- **Google Drive** (with Docs and Sheets): every file, the registry and the calendar.
- **DataForSEO**: keyword, SERP, competitor and AI-visibility data. Added in Claude as a custom connector (`https://mcp.dataforseo.com/mcp`) and signed in with OAuth, so there are no API keys to manage. Each client connects its own DataForSEO account and pays for its own usage. DataForSEO bills per call, so each run stays within the client's `seo_budget_per_run`.

If a required service isn't connected, stop and say which one, before doing any work.

**Onboarding-only services.** outside-in-report also needs Tavily and Perplexity, paid for by the client. It checks them and DataForSEO before spending anything; if one is missing it stops and names it, and onboarding carries on with the client's documents. The day-to-day workflows don't need either service.

Everything else is optional. The exact check, the fallback for each optional connector and what to log are in `connector-rule.md`. Before any step that uses an optional connector from §5, check that it is connected and listed in the client's `optional_connectors`. If not, write the content to Google Docs in the client's folder, set the status that step would otherwise skip to, and continue. Never stop a run for a missing optional connector.

## 10. Decisions taken in v1.0

- Topics and drafts have separate approval statuses (§7).
- All per-client settings live in the registry `Clients` tab. The review page has no settings of its own.
- The registry lives in a `Big Slick` admin folder, with client folders under `Clients`.

## 11. Changes in v1.1

- DataForSEO is a required service alongside Google Drive (§9).
- Registry columns `seo_location`, `seo_language` and `seo_budget_per_run` added (§3).

## 12. Changes in v1.2

- Brand Context keeps the first 8 headings and adds 7 after them (§4). Publishing cadence stays in the registry (§3).
- Claims library gains `status`, `provenance`, `last_checked`, `expires_on` and `logo_ok`, and claims-check passes only approved, unexpired rows (§4).
- Provenance tags and a Sources and intake table are defined (§4).
- The registry `reviewers` column is the only reviewer list (§3). The Run Log gains `company-onboarding` and `outside-in-report`, with duration and spend in `note` (§3).
- SMEs approve as listed reviewers, and `reviews` gains `sme_verdict` and `sme_flags` (§8). `reviewer_decisions` holds one entry per required reviewer (§8), and the calendar gains `sme_approve` and `sme_reason` (§6).
- Tavily and Perplexity are onboarding-only services (§9), and each client folder gains `Research/` (§2).
- Each client folder gains an `Onboarding Checklist` Sheet, updated by every onboarding step and shown to the user as it goes (§2).
- Each client folder gains `Intake/` and an `Onboarding Questionnaire` Sheet, both shared with the client contact and both linked from the intake request (§2).
- Onboarding messages to the client are drafted, never sent: a Gmail draft when `gmail` is active, otherwise text in the chat.
- Claims don't hold up onboarding: the claims item is a `should`, and sign-off doesn't wait for answers on claims.
- Outside-in research starts as soon as the website URL and location are known, and writes to Brand Context after intake.
- Intake settles a disagreement by an authority document or a newer version before listing it, keeps personal data out, uses confidential documents for everything but their sensitive facts, and checks a link is shared before a message goes out. Outside-in's re-check runs in the session that started collection (§4).
- Workflows load only the Brand Context sections they name, through client-context (§4).
- Cadence is set by the first content plan, when the client approves the strategy, not during onboarding (§3).
- Pillars, cadence, scheduled tasks and the first workflow run move to a workflow phase after onboarding, started by the first content plan. Every SME is also an author, with a `Voice:` section built during onboarding from the same material as their profile (§2).
