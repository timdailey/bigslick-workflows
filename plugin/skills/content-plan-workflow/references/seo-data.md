# SEO data for a content plan

Every call goes through the DataForSEO connector, signed in with the client's own account, within the registry's `seo_budget_per_run` (the most calls one run may make; use `25` if it's blank). These rules replace the local cost script in `seo-dataforseo` and the SemRush calls in `content-gap-analysis` and `seo-content-brief`, which don't work in Claude. Use those skills for their method, not their commands.

## Location and language

Pass the registry's `seo_location` as `location_name` on every call that takes it. For language, the connector's tools take `language_code` (e.g. `en` for `English`): convert `seo_language` to its code, or pass it as `language_name` on a tool that asks for the name. Never fall back to US English. If `seo_location` is blank, ask the user before any call and write both cells; in a scheduled run, stop and log it.

## Tools

The names below were confirmed on the live connector on 12 November 2026 (prefixed `mcp__DataForSEO__`). Names can change, so use whichever present tool does each job. Don't call a tool just to see whether it works.

| Job | Usual tool | Planning cost |
|---|---|---|
| Keywords the client's site ranks for | `dataforseo_labs_google_ranked_keywords` or `dataforseo_labs_google_keywords_for_site` | $0.05 |
| Keywords a competitor ranks for and the client doesn't | `dataforseo_labs_google_domain_intersection` with `intersections` = `false`, the competitor as `target1` and the client as `target2` | $0.05 |
| Volume, intent and difficulty for a candidate list | `dataforseo_labs_google_keyword_overview` (up to 700 terms in one call) | $0.05 |
| Longer phrases containing a term, to widen a thin pillar | `dataforseo_labs_google_keyword_suggestions` (phrase match) | $0.05 |
| Difficulty where the overview left it blank | `dataforseo_labs_bulk_keyword_difficulty` | $0.01 |
| Current results for one keyword | `serp_organic_live_advanced` (depth 10 is the minimum) | $0.002 |

**Don't use** `dataforseo_labs_google_keyword_ideas` (category matching returns off-topic terms for a specialist client: CPR certification and lab coats for a clinical-imaging company) or `kw_data_google_ads_search_volume` (returns only the first 10 keywords of a batch). Both checked 12 November 2026.

**Large responses:** ranked-keyword and intersection calls return 250,000 to 430,000 characters for 100 rows. Ask for the rows you need (`limit`, a volume filter, `order_by` volume), and when a response is saved to a file because it's too large, pull out keyword, volume, difficulty, intent, position and URL with a short script instead of reading it.

**Reading SERP results:** the `domain` field on organic results comes back as `google.com`, with `google.com` redirect URLs. Take each result's real site from its `website_name` or breadcrumb field instead.

## Call order and budget

Spend in this order, and stop adding calls when the next one would pass the budget. Each line says what to drop when the budget is tight.

1. **Client's ranked keywords:** 1 call, limit 200 rows, ordered by volume. Always run it.
2. **Competitor gaps:** 1 call per competitor, for up to 3 competitors from `Competitors`, in the order listed, keeping only `[confirmed]` competitors once sign-off is done. Limit 100 rows with search volume of 20 or more. Throw away rows that are the competitor's own brand or product names, careers, jobs, login or support pages, and anything outside the confirmed pillars. Expect few usable rows for a specialist client; that's normal, not a failed call. Drop to 1 competitor first when tight.
3. **Candidate list:** write 50 to 150 terms from the client's own material: `Products and positioning` and `Audience and ICP` terms, buyer language, the questions in `AI visibility baseline`, each pillar's main terms, the competitor-gap rows you kept, and launch and event names in `Marketing plan`. Size the whole list in 1 keyword overview call. If a pillar ends up with fewer than three terms over 20 searches a month, add 1 keyword suggestions call for that pillar's main term (limit 50).
4. **Shortlist difficulty:** after the first cut (see `planning-rules.md`), 1 bulk difficulty call for shortlist terms the overview left blank.
5. **SERP checks:** 1 call per long-form topic in the first month of the plan, up to 6. Note who ranks, whether an AI Overview or a People Also Ask box shows, and the content format that wins. Drop these first when tight, and say the briefs were written without a live SERP.

A first plan with 4 pillars and 3 competitors makes about 10 to 14 calls and costs about $0.50 (the QMENTA simulation on 12 November 2026 made 15, including the 5 calls this file now rules out).

## Spend and records

- The connector's responses carry no `cost` field (checked 12 November 2026), so count calls and log the planning cost above for each, marked estimated. Actual spend is in the client's DataForSEO dashboard. If a response does carry `cost`, log that instead.
- Keep the numbers you use (volume, difficulty, intent, position) with each candidate topic for the scoring step and the briefs. Don't save raw responses to Drive.
- The Run Log `note` gives calls and spend, e.g. `14 DataForSEO calls, $0.72 estimated`.
- If a call fails, retry it once. If it fails again, carry on without it and say which data is missing in the plan and the Run Log.
