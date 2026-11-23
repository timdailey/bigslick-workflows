# How every gate works

Each gate stops the run, tells the reviewer and resumes only on a decision. It is ContentForge's "Approve & continue" button, moved to the client's review page so a reviewer outside Claude can press it.

## At each gate

1. **Check the stage contract.** The stage can't hand on until the fields the next stage reads are present (ContentForge's `STEP_CONTRACTS`; each stage in `SKILL.md` lists its own). A failed contract keeps the partial work in Drafts, records what's missing in the Run Log and in the chat, and doesn't write a review item or email anyone. The calendar status stays where it was.
2. **Save the work.** Write the stage output to Drafts as a new Google Doc version, `<date> <title> (<item_id>) v<n>`, where `<n>` is the row's `version` + 1. Never overwrite an earlier version. End the doc with a level-2 heading `Draft notes (removed at publish)` holding: item_id, stage, author, SME, keyword, the claim_ids used, any `[CHECK]` markers left, and for a re-run, each note from the reviewer and what was done about it. Update the row: `draft_link`, `version`, `updated_at`.
3. **Write a review item** with send-to-review, kind `draft`, `stage` = `outline`, `draft`, `final_check` or `publish`, the stage's `summary`, `scores`, `findings`, `link_gate` and `links` as shared definitions §8 defines them, and `doc_link` = the Drafts file (Gate 4: the Approved doc).
4. **Set the calendar status** for the gate: `outline_review`, `draft_review`, `final_review` or `publish_review`. send-to-review sets it.
5. **Email the reviewer.** send-to-review emails everyone in the registry `reviewers`, plus the item's SME at Gates 1 and 3. Subject: `[<client_name>] <Stage> ready: <title>`. Body: what's waiting, what to decide, the review page link and the Drafts link. Without `gmail` (or `slack`) active, send nothing (connector rule fallback): the review page and the calendar show what's waiting. Say so in the Run Log.
6. **Stop.** In a chat, say what's waiting and list the review page and Drafts links as plain URLs, because the Gmail connector rewrites the email's links as redirects. In a scheduled run, log it and move to the next item.
7. **Resume on a decision.** The hourly publish-approved run reads gate decisions and hands decided items back to this workflow, which starts the next stage (or re-runs the same one). If someone listed in `approver_in_chat` is in the chat, they can decide there: write their decision to the review item as the page would (`decided_by` = `chat:<email>`), and continue at once.

## The three decisions

| Decision | Review item | What happens |
|---|---|---|
| Approve | `decision` `yes`, `status` `approved` | The next stage starts. Reviewer edits to the Drafts file are kept as its input: always read the Drafts file again, never reuse the text from memory |
| Request changes | `decision` `changes`, `status` `changes_requested`, `notes` = their instructions | The same stage re-runs with the notes as instructions. New version, new review item, new email |
| Stop | `decision` `no`, `status` `declined`, `reason` required | publish-approved sets the calendar row to `declined` with the reason. Nothing else runs |

With two entries in `reviewer_decisions` (the reviewers and the SME), a `no` or `changes` from either one decides the item at once; `yes` needs both.
