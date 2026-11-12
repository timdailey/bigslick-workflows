# Connector rule

Every Big Slick workflow runs this check before it does any work, and follows the fallbacks below whenever a step would use an optional connector. It implements §9 of `shared-definitions.md`.

## 1. Check, before any work

Decide what is connected by looking at the tools available in this session. Connector tools are often deferred when a session starts, especially in a scheduled run: if a service's tools aren't listed, search for them (for example with ToolSearch, `dataforseo`) before deciding it's missing. Don't make a paid call just to test a connector.

### Required services

| Service | Counts as connected when these tools are present | If missing |
| --- | --- | --- |
| Google Drive | Google Drive tools that search, read and create files (e.g. `search_files`, `read_file_content`, `create_file`) **and** Google Sheets tools that read and write rows (e.g. `get_values`, `update_values`, `append_values`) | Stop |
| DataForSEO | Tools from the DataForSEO custom connector (`https://mcp.dataforseo.com/mcp`), e.g. SERP, keyword data or DataForSEO Labs tools | Stop |

A Google Docs connector (e.g. `read_doc`, `update_doc`) is helpful but not required. Without it, edit a doc by creating a new version with the Drive connector instead of changing it in place.

Then read the client's registry row. An optional connector is **active** only when its tools are present **and** it is listed in the row's `optional_connectors`. A connector that is present but not listed stays unused, because it may belong to another client or to the consultant.

### Optional connectors

| Registry value | Used for | Tools to look for |
| --- | --- | --- |
| `gmail` | Reviewer alerts | send or draft email |
| `slack` | Reviewer alerts | send message |
| `typefully`, `taplio` | Scheduling social posts | create draft, schedule post |
| `mailchimp`, `mailerlite`, `kit` | Newsletters and emails | create campaign or broadcast |
| `hubspot` | Publishing blog posts | create or update blog post |
| `granola` | Topics and quotes from calls | list meetings, get transcript |

## 2. When a required service is missing: stop

Do no other work, then:

- **In a chat:** say which service is missing and how to connect it. Google Drive: connect Google Drive and Google Sheets in Claude's connector settings. DataForSEO: add a custom connector at `https://mcp.dataforseo.com/mcp` and sign in with the client's own DataForSEO account.
- **In a scheduled run:** if Google Drive is present, add a Run Log row with `result` = `failed` and a note naming the missing service. If Google Drive itself is missing, end the run with a one-line report naming it; the run's own report is the only record.

Never fall back to local files, chat-only output, or skipping SEO data in place of a required service.

## 3. When an optional connector isn't active: fall back and continue

Never stop a run for an optional connector. Use the fallback for the step, then keep going.

| Step | Connector it would use | Fallback |
| --- | --- | --- |
| Alert the reviewer | `gmail` or `slack` | Send nothing. The review page and the Content Calendar show what's waiting. |
| Schedule a social post | `typefully` or `taplio` | Write the final post to a Google Doc in Approved, ready to paste, and set status `ready_to_post`. |
| Send a newsletter or email | `mailchimp`, `mailerlite` or `kit` | Write it to a Google Doc in Approved with subject line, preview text and body, and set status `ready_to_post`. |
| Publish a blog post | `hubspot` | Write it to a Google Doc in Approved with title, slug, meta description and body, and set status `ready_to_post`. |
| Pull topics or quotes from calls | `granola` | Plan or draft without them, and write "No call notes used" at the top of the brief. |

Files written as fallbacks follow the folder and file-name rules in `shared-definitions.md`.

## 4. Record what was used

The Run Log row's `note` starts with the connector summary, then anything else worth noting:

`connectors: drive, sheets, dataforseo, gmail; fallback: typefully → Approved doc`

In a chat, end with one line naming any fallback used, e.g. "Typefully isn't connected for this client, so the post is in Approved, ready to paste."
