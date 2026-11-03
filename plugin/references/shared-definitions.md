# Big Slick Shared Definitions

Version 1.0 · Nov 3, 2026. The Google Doc "Big Slick Shared Definitions" in the BigSlick Harness folder is the master copy; this file mirrors it.

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
        ├── Briefs/
        ├── Drafts/
        └── Approved/
```

A single-company install is the same layout with one client.

### File names

| Folder | Name format | Example |
| --- | --- | --- |
| Briefs | `<date> <title> (<item_id>)` | `2026-11-19 Meeting-free mornings (acme-0042)` |
| Drafts | `<date> <title> (<item_id>) v<version>` | `2026-11-19 Meeting-free mornings (acme-0042) v2` |
| Approved | `<date> <title> (<item_id>)` | `2026-11-19 Meeting-free mornings (acme-0042)` |

`<date>` is the planned publish date. The `(<item_id>)` suffix is how a run finds an item's files; never remove it. A declined draft stays in Drafts; the redraft is a new file with the next version number.

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
| reviewers | list | emails | setup, people |
| approver_in_chat | list | emails of people whose go-ahead in a chat counts as approval | setup, people |
| channels | list | values from §5 | setup, people |
| content_types | list | values from §5 | setup, people |
| cadence_plan | text | `monthly` or `quarterly` | setup, people |
| cadence_blog | number | posts per month | setup, people |
| cadence_social | number | posts per week, per author | setup, people |
| authors | list | names, each matching a `Voice:` section in Brand Context | setup, people |
| optional_connectors | list | values from §5 that this client uses | setup, people |
| schema_version | text | `1.0` | setup |

The registry is the one home for settings. Runs read it on every start, so a change takes effect on the next run.

### Tab `Run Log` (one row per run, newest at the bottom)

| Column | Type | Format |
| --- | --- | --- |
| run_at | timestamp | UTC |
| client | text | registry slug |
| workflow | value | skill name, e.g. `content-plan-workflow` |
| trigger | value | `chat` or `scheduled` |
| items | number | calendar rows created or updated |
| result | value | `ok`, `partial`, `failed` |
| note | text | one line; what failed and where, if anything |

## 4. Brand Context doc

Skills find sections by their exact heading text. Keep the headings (level 2) as written; add content under them freely.

1. `Company facts`
2. `Audience and ICP`
3. `Products and positioning`
4. `Content pillars` (one level-3 heading per pillar)
5. `Competitors`
6. `Claims library` (a table, below)
7. `Voices` (one level-3 heading per author: `Voice: <Author Name>`)
8. `Style rules` (house style, banned words, formatting)

### Claims library table

| Column | Meaning |
| --- | --- |
| claim_id | `C-001`, `C-002`, … never reused |
| claim | The approved wording |
| source | Link or document that proves it |
| owner | Who approved it |
| approved_on | Date |
| notes | Limits, e.g. "US customers only", or an expiry date |

`claims-check` treats anything in a draft that isn't backed by this table as unsupported.

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
| status | value | `in_review`, `approved`, `declined`, then `scheduled`, `ready_to_post`, `published` | send-to-review, review page, publish-approved |
| decision | value | `yes`, `no`, or absent until decided | review page |
| reason | text | required when decision is `no` | review page |
| decided_by | text | the reviewer's user id | review page |
| decided_at | timestamp | | review page |
| synced_at | timestamp | when publish-approved copied the outcome to the calendar | publish-approved |

The review page writes the decision; publish-approved copies it to the calendar row (§7) and sets `synced_at`. Every write pins the version it read.

## 9. Connector rule

Google Drive (with Docs and Sheets) is required. Before any step that uses an optional connector from §5, check that it is connected and listed in the client's `optional_connectors`. If not, write the content to Google Docs in the client's folder, set the status that step would otherwise skip to, and continue. Never stop a run for a missing optional connector.

## 10. Decisions taken in v1.0

- Topics and drafts have separate approval statuses (§7).
- All per-client settings live in the registry `Clients` tab. The review page has no settings of its own.
- The registry lives in a `Big Slick` admin folder, with client folders under `Clients`.
