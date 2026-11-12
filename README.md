# bigslick-workflows

The workflow layer on top of [Big Slick](https://github.com/frankdays/bigslick). Big Slick provides the curated marketing skills; this repo holds the first-party workflows that chain them into a content operation: plan, write, review and publish, for one company or a consultant with many clients.

**Status:** v0.7.0. Onboarding and review are built and tested on a test client. content-plan-workflow is built but not yet run against DataForSEO; the other content workflows are still outlines.

## Install

```
/plugin marketplace add <owner>/bigslick-workflows
/plugin install bigslick-workflows
```

Install the Big Slick core plugin and the `bigslick-content`, `bigslick-seo` and `bigslick-strategy` plugins first (tested with Big Slick 0.2.13). Two services are required: Google Drive, and DataForSEO as a custom connector (`https://mcp.dataforseo.com/mcp`, OAuth sign-in). Everything else is optional.

## Layout

- `plugin/` — the installable plugin (skills and `references/shared-definitions.md`)
- `.claude-plugin/marketplace.json` — makes this repo installable as a marketplace

## What goes where

- **frankdays/bigslick** holds general marketing skills that work for anyone, with or without this plugin: the vendored skills and the general infrastructure skills (`client-context`, `company-onboarding`, `outside-in-report` and the rest).
- **This repo** holds everything that depends on the Big Slick setup: the shared definitions, the registry, the Drive folder layout, Brand Context, statuses and the review page. That includes onboarding into Drive (`bigslick-onboarding`), loading a registry client (`bigslick-client`) and every workflow.
- This plugin calls Big Slick skills unchanged, through their documented steps. It never patches them. If a Big Slick skill needs a change to work here, write the Big Slick part of the change in this repo.

## Rules

- No real client names in any tracked file. Use `<client>` or an obviously fictional name such as `acme` in examples. Client data lives in each client's Drive folder, never here.
- The shared definitions (`plugin/references/shared-definitions.md`) mirror the Big Slick Shared Definitions Google Doc in the BigSlick Harness folder. Change the doc first, then this copy.
