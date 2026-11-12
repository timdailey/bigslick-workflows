# bigslick-workflows

Content workflows for Big Slick. Works for one company or a consultant with many clients.

**Status:** v0.9.0, shared definitions v1.3. Onboarding and review are built and tested on a test client. content-plan-workflow (simulated on a real client) and longform-workflow are built; social and repurpose are still outlines.

## Skills

| Skill | What it does |
| --- | --- |
| content-plan-workflow | Builds or extends a client's content calendar |
| longform-workflow | Blog posts and guides (case studies, gated assets in V1) |
| social-workflow | Social posts per author voice; LinkedIn first |
| repurpose-workflow | One published piece into social posts, newsletter and email |
| claims-check | Checks a draft against that client's claims library |
| sme-content-review | Reviews a draft through an SME's lens, with humanize and persona-safety passes, and writes SME profiles |
| send-to-review | Writes review rows and alerts the reviewer |
| publish-approved | Hourly: files approved content and schedules it if possible |
| bigslick-setup | Adds a company or client, from connectors to go-live |
| bigslick-onboarding | Fills a client's Brand Context: intake, outside-in research, sign-off and author voices |
| bigslick-client | Loads one client from the registry, with only the Brand Context sections a skill names |

Shared names (registry, folders, calendar columns, statuses) are in `references/shared-definitions.md`. The connector check and fallbacks every workflow runs are in `references/connector-rule.md`.

## Requirements

- Big Slick core plugin (`bigslick`) and the `bigslick-content`, `bigslick-seo` and `bigslick-strategy` plugins, from [frankdays/bigslick](https://github.com/frankdays/bigslick). Tested with Big Slick 0.2.13. This plugin calls their skills unchanged; onboarding uses `outside-in-report` (core) and `brand-voice-extractor` (strategy).
- Google Drive connector (with Google Docs and Sheets)
- DataForSEO, added as a custom connector (`https://mcp.dataforseo.com/mcp`, OAuth sign-in). Each client uses its own DataForSEO account; DataForSEO bills per call.

Everything else is optional.
