# Context Pack MVP: Build Log

Results for `CONTEXT-PACK-MVP-BUILD-PLAN.md`. Where the plan and the earlier build brief disagree, this build follows the plan: it is newer (4 November) and builds to shared definitions v1.2.

## T1. Shared definitions v1.2, template and claims-check

Status: **all four checks recorded.** Two caveats: claims-check has only had a dry run, and the size check hasn't been run on a full-size doc from a scheduled run. Neither blocks T2.

| Done when | Result | Evidence |
| --- | --- | --- |
| Both copies of the shared definitions say v1.2 and match | Pass | Both say v1.2. A row-by-row and sentence-by-sentence comparison found all v1.2 content in both. What still differs is the doc's own style and wording from v1.1: it writes "section 5" for "§5", describes the three SEO columns in prose, and has the v1.2 changes as section 11 (section 12 here, because it keeps the v1.1 notes in section 10) |
| A doc from the template has all 15 headings, and skills still find the first 8 | Pass | Real Google Doc created from `brand-context.md`, then trashed: 15 level-2 headings, an 11-column claims table and a 6-column intake table. The first 8 headings match `main` exactly |
| claims-check passes an approved claim and flags candidate and expired claims | Pass (dry run) | A separate agent followed the skill text on a fictional client. One draft passed with no flags. The other got all 7 expected flags: candidate, expired, forbidden, missing qualifier, conflicting, unsupported and logo not approved. Not yet run from a scheduled task |
| Size-check result recorded | Pass | Result below; a full-size read from a scheduled run is still to do |

### Size check (issue 8, first half)

- **Only that section came back: no.** Neither Drive read tool accepts a heading or section. `read_file_content` returned the whole test doc, and `read_doc` takes only a document ID. Reading one section means fetching the whole doc and keeping that section, so it saves model context at most and no connector load.
- **Not tested:** a full-size doc of about 15,000 words, or a read from a scheduled run. `read_file_content` warns that very large files may come back incomplete, so this still needs running before the pilot decision.
- **Use `read_file_content`, not `read_doc`, to load Brand Context.** `read_doc` returned about 320,000 characters of JSON for this roughly 15,000-character spec, around 20 times the text.
- **Escaping:** `read_file_content` returns escaped text, more heavily inside table cells (for example `must\\\_qualify`). Skills must read the value, not match the raw characters. Worth rechecking when claims-check first reads a real library.

### Choices the plan left open

- Claims columns are added after `notes`, so the first six stay in place.
- `notes` holds the qualifier for a `must_qualify` row, because decision 3 adds no column for it.
- `Research/` has no registry column: skills find it by name under `client_folder_id`.
- `owner` is `outside-in` for a claim a credible source confirmed.
- A claim dated today counts as expired: `expires_on` must be later than today.
- bigslick-setup now writes `schema_version` = `1.2`, and the registry dropdown accepts only `1.2`. Any registry built before this needs that value and dropdown updated.

### Small gaps in claims-check wording

Found in the dry run; none changed a result: whether an image placeholder counts as a claim, whether a changed number is `conflicting` or `unsupported`, and what to return when there are no flags.

## Decisions taken on 4 November

These override the plan where they differ.

| Blocks | Decision | Effect |
| --- | --- | --- |
| T4 | outside-in-report ships in the public core repo, report mode included | Core's rules limit it to infrastructure and bar new marketing skills, so T4 must amend those rules in that repo's `CLAUDE.md` |
| T2, T3 | The local-disk route stays in core | Overrides T2 steps 5 and 6. client-context gains the registry and Drive route and keeps its local route, and client-onboarding stays for local installs. No script or release test changes |
| T6 | brand-voice-extractor changes through a patch | Lives in `overlay/patches/` in core, accepting the merge cost at upstream refreshes |
| T7 | Per-reviewer decisions | `reviews` gains `reviewer_decisions` and the calendar gains `sme_approve` and `sme_reason`, in both copies of v1.2. An `any` entry keeps today's behaviour for a piece with no SME. T7 updates send-to-review, the review page and publish-approved. The page knows user IDs, not emails, so it can't restrict an SME's entry to that SME; it records who decided in `decided_by` |
| T3, T7 | The `[internal]` tag is dropped on purpose | Nothing blocks pricing or roadmap budgets by tag. To keep one out of drafts, record it as a `forbidden` claim |
| T3 to T7 | Test with the test client's documents in the BigSlick Harness folder | Harness holds the source documents. Tim's `Templates/Big Slick Registry` stays blank; tests use their own registry |

## Still needed

- T3 to T7 test runs need DataForSEO, Tavily, Perplexity and ConnectSafely access, and T4 a paid run of up to $15. The DataForSEO scheduled-run check is still open.

v1.2 doesn't list ConnectSafely as a service, though the plan doc puts it on the setup checklist. T6 handles it being missing, so this only affects the checklist.
