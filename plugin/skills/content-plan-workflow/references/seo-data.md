# SEO data for a content plan

Every call goes through the DataForSEO connector, signed in with the client's own account, within the registry's `seo_budget_per_run` (the most calls one run may make; use `25` if it's blank). These rules replace the local cost script in `seo-dataforseo` and the SemRush calls in `content-gap-analysis` and `seo-content-brief`, which don't work in Claude. Use those skills for their method, not their commands.

## Location and language

Pass the registry's `seo_location` as `location_name` and `seo_language` as `language_name` on every call that takes them. Never fall back to US English. If `seo_location` is blank, ask the user before any call and write both cells; in a scheduled run, stop and log it.

## Tools

The names below were confirmed on the live connector on 12 November 2026 (prefixed `mcp__DataForSEO__`). Names can change, so use whichever present tool does each job. Don't call a tool just to see whether it works.

| Job | Usual tool | Planning cost |
|---|---|---|
| Keywords the client's site ranks for | `dataforseo_labs_google_ranked_keywords` or `dataforseo_labs_google_keywords_for_site` | $0.05 |
| Keywords a competitor ranks for and the client doesn't | `dataforseo_labs_google_domain_intersection` (client and one competitor, keep rows where only the competitor ranks) | $0.05 |
| Keyword ideas from a pillar seed | `dataforseo_labs_google_keyword_ideas` or `dataforseo_labs_google_keyword_suggestions` | $0.05 |
| Difficulty for a shortlist | `dataforseo_labs_bulk_keyword_difficulty` | $0.01 |
| Intent for a shortlist | `dataforseo_labs_search_intent` | $0.01 |
| Volume, if the ideas call didn't return it | `kw_data_google_ads_search_volume` (one batch) | $0.05 |
| Current results for one keyword | `serp_organic_live_advanced` (depth 10 is the minimum) | $0.002 |

**Reading SERP results:** the `domain` field on organic results comes back as `google.com`, with `google.com` redirect URLs. Take each result's real site from its `website_name` or breadcrumb field instead.

## Call order and budget

Spend in this order, and stop adding calls when the next one would pass the budget. Each line says what to drop when the budget is tight.

1. **Client's ranked keywords:** 1 call, limit 500 rows. Always run it.
2. **Competitor gaps:** 1 call per competitor, for up to 3 competitors from `Competitors`, in the order listed, keeping only `[confirmed]` competitors once sign-off is done. Drop to 1 competitor first when tight.
3. **Pillar ideas:** 1 call per confirmed pillar, seeded with the pillar's main term, limit 100 rows each.
4. **Shortlist checks:** after the first cut (see `planning-rules.md`), 1 difficulty call and 1 intent call for the whole shortlist. Skip intent when tight; judge it from the SERP or the keyword.
5. **SERP checks:** 1 call per long-form topic in the first month of the plan, up to 6. Note who ranks, whether an AI Overview or a People Also Ask box shows, and the content format that wins. Drop these first when tight, and say the briefs were written without a live SERP.

A typical first plan with 4 pillars and 3 competitors makes about 15 calls and costs under $1.

## Spend and records

- The connector's responses carry no `cost` field (checked 12 November 2026), so count calls and log the planning cost above for each, marked estimated. Actual spend is in the client's DataForSEO dashboard. If a response does carry `cost`, log that instead.
- Keep the numbers you use (volume, difficulty, intent, position) with each candidate topic for the scoring step and the briefs. Don't save raw responses to Drive.
- The Run Log `note` gives calls and spend, e.g. `14 DataForSEO calls, $0.72 estimated`.
- If a call fails, retry it once. If it fails again, carry on without it and say which data is missing in the plan and the Run Log.
