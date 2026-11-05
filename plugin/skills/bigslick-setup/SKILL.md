---
name: bigslick-setup
description: Sets up Big Slick for a new company or client. Use when someone says set up Big Slick, add a client, onboard a client or types /bigslick-setup.
---

# Big Slick setup

> Skeleton.

## Steps

1. Run the connector check in `${CLAUDE_PLUGIN_ROOT}/references/connector-rule.md`, then fix what's missing with the user. Required services: Google Drive, and DataForSEO as a custom connector at `https://mcp.dataforseo.com/mcp`, signed in with the client's own DataForSEO account (the client pays for its usage). Stop until both work. Then list the optional connectors in that file, offer to connect any the client wants, and record the chosen ones in the registry's `optional_connectors`. Ask for the client's SEO market (location and language) and a per-run DataForSEO call budget for the registry.
2. If they don't exist yet, create the `Big Slick` admin folder and the `Big Slick Registry` Sheet: create it from `templates/registry-clients.csv`, add a second sheet and write `templates/registry-run-log.csv` into it, then apply `templates/registry-format.json` with the two sheet ids. Add the client's row, filling every column in shared definitions (`status` = `active`, `schema_version` = `1.2`).
3. Create the client's folder under `Big Slick/Clients/` from the templates in `${CLAUDE_PLUGIN_ROOT}/templates/` (see its README):
   - `Brand Context`: a Google Doc created from `brand-context.md` (upload as markdown so headings and the claims table convert). Replace `[Client Name]` with the client's name.
   - `Content Calendar`: a Google Sheet created from `content-calendar.csv`, then formatted with the requests in `content-calendar-format.json` (put the new sheet's id in place of `__SHEET_ID__`).
   - Empty `Briefs`, `Drafts`, `Approved` and `Research` folders.
   Record every new file and folder ID in the client's registry row, except `Research`, which has no column: skills find it by name.
4. Build the Brand Context doc with company-onboarding (writes to Drive), through to the client's sign-off. Steps 5 and 6 can run while it's out.
5. Capture voices with brand-voice-extractor: ask each author to paste 10-15 posts.
6. Publish the client's review page from `templates/review-page.html`: replace every `__CLIENT_NAME__` with the client's display name, publish it as an artifact with `capabilities: {"db": {}, "user": {"scopes": ["profile"]}}` and icon `checklist`, and record its URL in the registry's `review_page_url`. Don't seed the database; send-to-review fills it. Tell the user to share the page with each person in `reviewers` at **Contributor** or above (the Share menu): Viewers and Commenters can read the page but not decide. Then publish the dashboard from its template.
7. Once the client has signed off, create the scheduled tasks for the chosen workflows and cadence. Each task names its client.
8. Write Project instructions for the user to paste into a new Claude Project for this client.
9. Run one workflow end to end with the user as reviewer.
