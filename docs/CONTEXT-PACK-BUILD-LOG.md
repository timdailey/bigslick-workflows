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

## T2. client-context

Status: **passing with the limits below, committed to core `main` as `17036c4`, not pushed.** The plan's checks that no local paths remain and that client-onboarding is retired no longer apply, because the local route stays. client-context gains a registry and Drive route ahead of it, plus the isolation rules. client-onboarding stays for local installs, so the skill count stays at 262 and no script or release test changes.

| Done when | Result | Evidence |
| --- | --- | --- |
| A request naming client B loads B's doc and names B before working | Pass | A separate agent followed the skill against a real test registry in Drive. Each client's run opened only that client's doc and saw only its marker fact, and the announcement named the right client |
| A request matching no client stops with a clear message | Pass | Stopped and named what it searched for, with no fallback to local files |
| Local paths and client-onboarding | Replaced by a decision | A scheduled run never falls back to a local path. Scenario 6 stopped and named the missing column |

Also passed: choosing the real registry over a same-named template, stopping on an archived client, and asking when no client is named and several are active. Core's release gate passes: 262 skills, T1 to T9 and F1.

The first dry run found three wording gaps: use only the matched row because Drive returns the whole tab, treat several active rows as no match, and never name other clients in a stop message. Review then found that a scheduled run on a local install with no registry would stop instead of using its pack, so the no-fallback rule now applies only once a registry is found.

A second agent reran all six scenarios on the final wording, plus two scheduled runs: an unknown client, which stopped and named the client as missing, and no registry, which was simulated. Both stop messages named no other client. Limits: one agent ran every scenario, so it had already seen all the rows, and the stop wording is its own because the skill sets the content, not the text.

The rerun exposed two problems in core, both now fixed:

- **The release gate wrote to the real home folder.** F1 in `scripts/test.sh` ran `activate_client.sh __testco`, which overwrote `~/.claude/product-marketing.md` with the blank template, then deleted `core/clients/_active` and `.agents`. It now runs on a throwaway copy with its own `HOME`. A gate run after the fix left the home file and `core/clients/` unchanged. What that file held before the first gate run on 4 November is unknown. Client packs in `core/clients/` weren't touched.
- **A blank template counted as a pack.** Section 3 used the first file that existed, so a literal run announced `{CLIENT NAME}`, and a pack for another company would have loaded too. It now skips a blank template, and skips any other company's pack when the request names a client. The wording before this fix predates T2.

A third agent then tested this and section filtering blind, on the final wording. Both earlier runs had used a doc holding only the sections the request named, so they proved nothing about filtering.

| Check | Result | Evidence |
| --- | --- | --- |
| Blank template on the home path | Pass | Skipped, reached "nothing found" |
| Filled pack for another company | Pass | Skipped, its marker fact not used |
| Filled pack for the named client | Pass | Loaded and named the client |
| One section named, from a four-section doc | Pass | Kept only that section, dropped the marker fact in another |
| Two sections named | Pass | Kept both, dropped the other two |

Limit: to skip another company's pack the agent has to open it first, so the marker fact reached its context even though it wasn't used. The local route can't avoid that.

The test registry came from a CSV, so it isn't formatted by `registry-format.json`, and the skills haven't yet been run from a scheduled task.

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
