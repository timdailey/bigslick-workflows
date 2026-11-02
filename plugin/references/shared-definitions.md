# Shared definitions

Every workflow reads and writes these exact names. Do not rename a column, status or folder in one skill without changing it here and in every skill that uses it.

> Skeleton: the values below are the starting proposal. Confirm them before building the workflows.

## Client registry (Google Sheet, one per install)

| Column | Meaning |
| --- | --- |
| client | Short lowercase slug, e.g. `<client>` |
| client_name | Display name |
| drive_folder_id | ID of the client's Drive folder |
| brand_context_doc_id | ID of the client's Brand Context doc |
| calendar_sheet_id | ID of the client's Content Calendar Sheet |
| review_page_url | URL of the client's review page |
| reviewers | Comma-separated emails |
| channels | e.g. `blog, linkedin` |
| cadence | e.g. `plan monthly; social 3x/week; blog 2x/month` |
| optional_connectors | Which optional connectors this client uses |

## Drive folder layout (one folder per client)

- `Brand Context` (Google Doc)
- `Content Calendar` (Google Sheet)
- `Briefs/` (one Google Doc per approved topic)
- `Drafts/` (drafts waiting for review)
- `Approved/` (final versions, ready to post by hand or with a link to where each was published)

## Brand Context doc sections

1. Company facts
2. Audience and ICP
3. Products and positioning
4. Content pillars
5. Competitors
6. Claims library (each claim with source and owner)
7. Voices (one section per author)
8. Settings (cadence, channels) if not kept on the review page

## Content Calendar columns

| Column | Meaning |
| --- | --- |
| id | Stable row id |
| date | Planned publish date (YYYY-MM-DD) |
| content_type | blog post, guide, case study, gated asset, social post, newsletter |
| channel | blog, linkedin, x, community, email |
| title | Working title |
| keyword | Target keyword (long-form only) |
| pillar | Content pillar |
| author | Author, matching a voice section |
| brief_link | Link to the brief doc |
| draft_link | Link to the draft doc |
| status | See statuses below |
| approve | Fallback approval column for reviewers without Claude access (Yes / No) |
| notes | Reviewer reason or other notes |

## Statuses (in order)

`planned` → `approved` (topic) → `drafted` → `in review` → `approved` or `declined` → `scheduled` or `ready to post` → `published`

## Review row fields (review page database)

client, row_id, content_type, channel, title, author, draft_link, proposed_date, status, decision (yes/no), reason, reviewer, decided_at

## Connector rule

Google Drive (with Docs and Sheets) is required. Before any step that uses an optional connector (Gmail, Slack, a social scheduler, an email platform, HubSpot, Granola), check that it is connected. If it is not, write the content to Google Docs in the client's Drive folder instead and continue.
