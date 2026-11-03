# bigslick-workflows

The workflow layer on top of [Big Slick](https://github.com/frankdays/bigslick). Big Slick provides the curated marketing skills; this repo holds the first-party workflows that chain them into a content operation: plan, write, review and publish, for one company or a consultant with many clients.

**Status:** skeleton (v0.1.0). Every skill has its agreed outline; none is ready for client use yet.

## Install

```
/plugin marketplace add <owner>/bigslick-workflows
/plugin install bigslick-workflows
```

Install the Big Slick core and the content, SEO and strategy bundles first. Two services are required: Google Drive, and DataForSEO as a custom connector (`https://mcp.dataforseo.com/mcp`, OAuth sign-in). Everything else is optional.

## Layout

- `plugin/` — the installable plugin (skills and `references/shared-definitions.md`)
- `.claude-plugin/marketplace.json` — makes this repo installable as a marketplace

## Rules

- No real client names in any tracked file. Use `<client>` or an obviously fictional name such as `acme` in examples. Client data lives in each client's Drive folder, never here.
- The shared definitions (`plugin/references/shared-definitions.md`) mirror the Big Slick Shared Definitions Google Doc in the BigSlick Harness folder. Change the doc first, then this copy.
