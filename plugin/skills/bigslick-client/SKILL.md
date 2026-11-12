---
name: bigslick-client
description: Loads one Big Slick client from the Big Slick Registry in Google Drive, with only the Brand Context sections the caller names, and keeps clients apart. Use at the start of every Big Slick workflow, and whenever someone asks which client this is, to switch client, or to list the clients in the registry.
---

# Big Slick client

Every Big Slick skill loads its client through this one. It finds the client's row in the registry, loads their Brand Context, says which client it is, and refuses to guess. Column names, tags and headings are in `${CLAUDE_PLUGIN_ROOT}/references/shared-definitions.md`.

## 1. Find the registry

Google Drive, with Sheets tools that read rows, must be connected; if not, stop as `${CLAUDE_PLUGIN_ROOT}/references/connector-rule.md` says. Look for a Google Sheet titled `Big Slick Registry`. If several have that title, use the one directly inside a folder named `Big Slick`. If that doesn't settle it, or there is no registry, stop and say so, and point to `bigslick-setup`, which creates one.

Never fall back to a context pack on local disk, or to a `company-context` skill, for a Big Slick client: a file on one machine is not that client's context.

## 2. Find the client

In this order:

1. The client named in the request.
2. The client named in the current Project.
3. The only row in the registry's `Clients` tab whose `status` is `active`.

Read the tab with Google Sheets tools if you have them, because Drive's text view is labelled as sample data and may not show every row. Match on the `client` slug or `client_name`. Either read returns the whole tab, so use only the matched row: never repeat another row's name or details, even in a stop message. The one exception is a direct request to list clients, which you answer from this tab, never from memory.

**Stop rather than guess:**

| What you find | What to do |
|---|---|
| No matching row, two matches, or no client named and several active rows | Stop and ask which client. Name what you searched for |
| `status` is `archived` | Stop. Say the engagement is closed |
| `status` is `paused` | Say so. Scheduled runs stop here |
| `brand_context_doc_id` is blank, or the doc is missing or empty | Stop and say so. Point to `bigslick-setup`, which creates a client's Brand Context |

A scheduled run has no one to ask. It stops, logs why in the Run Log, and names what is missing: the client, or the registry column or Brand Context section it needed.

## 3. Load Brand Context

Load it from `brand_context_doc_id`. Read the whole doc unless the caller names sections. If it does, keep only those level-2 sections, matching each heading's exact text. Drive returns the whole doc either way, so read it as plain text (`read_file_content`), not as the document's JSON structure, which is far larger.

**The two tables are in a Sheet.** When the caller asks for `Claims library`, also read the `Claims` tab of the Sheet at `claims_sheet_id`; for `Sources and intake`, read the `Sources` tab as well as the logs under that heading in the doc. Use Google Sheets tools, and return the rows with their column names. If `claims_sheet_id` is blank and `schema_version` is `1.2`, the tables are still in the doc: read them there, and in a chat tell the user once that bigslick-setup will move them to a Sheet the next time they come back to this client.

**Use each fact as its provenance tag allows.** State `[confirmed]` and `[sourced A: <url>]` facts plainly, and hedge the rest. Never fill in a `[TBD: <owner>]`: ask, or leave it out.

## 4. Say which client

Before your first substantive answer in a session, and at the top of every substantive output:

`Working from <client_name>'s Brand Context.`

One line. It is the only way the user can tell customised work from generic work.

## 5. Keep clients apart

Presenting one client's positioning, competitors or numbers in another's work ends engagements, and it happens silently, because a stale context looks exactly like a correct one.

- **Name the client before doing the work.** If you cannot say which client you are on, you are not ready to answer.
- **Never carry a number, claim or name between clients.** Method travels; data does not. Two clients can share a competitor, but never each other's research.
- **Open only this client's files.** Use only the Drive IDs in this client's registry row, and the folders found by name under its `client_folder_id`. Never open another client's folder, even to compare.
