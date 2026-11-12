# Reconcile, gap questions and sign-off

For a client in the Big Slick Registry, once intake has written to Brand Context and outside-in has run or stopped. Read the whole doc first. Start here too when the client answers sign-off (section 4), or when a late item arrives (section 1, for the sections it touches).

Write only to this client's Brand Context and their registry row, plus the Run Log, the client's `Onboarding Checklist` if it has one, the Google Doc of lists in section 3 if the message needs one, and a Gmail draft of the message. Update three of its rows, and show the user the checklist after each: `reconcile` is `done` after sections 1 and 2, with the number of conflicts and gap questions in `detail`. `signoff_sent` is `done` once the message has gone, or `waiting` on the user until they say they've sent it. `signoff_applied` is `waiting` on the client once the user has sent the message, and `done` once they've said yes to the doc, with anything still open in `detail`.

## 1. Reconcile

The first pass covers the whole doc. After that, cover only the sections a change touches.

**Check every section.** Each fact sits in the section that `drive-intake.md` or `outside-in.md` gives it, and ends with exactly one provenance tag. Fix a fact that has no tag or is in the wrong section.

**Find every disagreement** between any two sources, including two pieces of research:

- Start from the lines already under `Disagreements`. Lines on the same point become one item.
- Add each Claims library row held at `candidate` because another source disagrees with it, or the source disagrees with itself. Holds for other reasons, such as wording or age, aren't disagreements.
- Compare sections for the same fact with different values.

Apply the three rules under "When sources disagree" in step 2 of `drive-intake.md` first: an authority document wins on its own subject, a newer version wins over an older one, and a difference no draft would state isn't a disagreement. Only what those rules don't settle goes on the list.

Leave out a competitor's own inconsistent figures, unless a client document or claims row is on one side. The client can't settle them, so they stay in `Competitors` as a finding.

**Don't pick one.** Each disagreement goes on the conflict list for the client to settle:

- **List it.** Rewrite `Disagreements` as one line per item: `D-001 | <section> | <what differs, with each source and its value> | <which has the later stated date, if either> | <status>`. The status is one of `open`, `settled by rule <n>` (one of the three rules in `drive-intake.md`, which needs no answer), or `settled <date>: <what the client chose>`. Number them in order and never reuse a number. Refer to a `forbidden` value by its `claim_id`. These lines take no provenance tag, because drafts don't use them.
- **Clear the section.** Replace each disputed fact with a line that names the item, such as `Imaging sites: see D-001 [TBD: <owner>]`, with no value beside it. This covers `[confirmed]` facts too, so no draft states a disputed value. Where the value runs all through the doc, such as a name, clear the line that states it and leave the rest. Two kinds of line stay: a red line or ban, which only stops drafts saying something, because clearing it would loosen it, so add the item's ID to it instead; and a fact the client settled at sign-off, with the new disagreement going on the list.
- **Hold the claims.** Set each row involved to `candidate` and add the item's ID to `notes`, and if its `owner` is `outside-in`, blank `owner` and `expires_on`. Leave a row alone if it's `forbidden` or the client approved it.

## 2. Gap questions

Ask only about must items, in the sign-off message, never about a `workflow` item. A must item is a gap if its `Sources and intake` row isn't `received`, or if the sections it feeds still have `[TBD: <owner>]` lines that only it would answer, other than conflicts.

Read what intake and outside-in found before you ask. Never ask for something a document, the website or the research already answered. If part of an item is answered, say what you have and ask for the rest.

Don't ask about `should` or `nice` items, which stay as they are and go in your report, or about conflicts, which sign-off settles. The one exception is the `reviewers` cell, if step 6 of `drive-intake.md` left it blank, because send-to-review needs it. Don't ask about cadence: the first content plan sets it.

Write the questions under a level-3 heading `Gap questions` in `Sources and intake`, numbered `G-1`, `G-2` and so on, so they're kept between sessions. Section 3 copies them into the message. For each gap, ask whether it's coming later, with an owner and a date, or doesn't exist. If it doesn't exist, ask whether Big Slick should build it or leave it as TBD. Record each answer in `Sources and intake`:

| Answer | `status` | Also set |
|---|---|---|
| Coming later | `coming_later` | `owner`, `date` |
| Doesn't exist, Big Slick builds it | `big_slick_to_build` | `owner` |
| Doesn't exist, leave it | `left_tbd` | `owner` |
| No answer yet | Stays `left_tbd` | |

## 3. Send sign-off

This is the only gate, and `bigslick-setup` creates scheduled tasks only after it. Claims are never part of the gate: sign-off doesn't wait for any answer on a claim.

Prepare the message only once outside-in has finished or stopped, because the competitor list comes from it. If it stopped, leave out part 3, and say the list will follow.

**Check the links will open.** Read each file's permissions; owning a file counts as access. The client contact needs the Brand Context doc at comment access or above, and the Doc of lists, if there is one, at view access or above. If either isn't shared, ask the user whether to share it now, and share it once they agree. Otherwise say the links won't open until it's shared, and keep `signoff_sent` `waiting` on the user.

Prepare one message to your client contact, plus anyone the doc names as owning messaging if the user agrees to include them. Write it for the client, with no provenance tags:

1. **The doc:** a link to Brand Context, and a sentence asking them to correct anything that's wrong.
2. **Conflicts:** each open item with its ID, the values and where each came from.
3. **Competitors:** the outside-in set, numbered `K-1` to `K-5`, with the one-line reason for each. Ask for a yes, a change or a replacement on each, and say that a yes to the doc doesn't confirm the list. Pillars aren't part of sign-off: the first content plan proposes them. Take the competitors from `config.json` in the latest run in the client's `Research/` folder.
4. **Claims, optional:** one short paragraph, not a list. Say how many candidate claims the Claims library holds and how many are already `approved` because a credible source confirmed them, that none of this holds up sign-off, and that a draft using a claim that isn't approved is flagged for the reviewer, who can approve it then. Link a Google Doc in the client's folder listing the candidates (each with its `claim_id`, wording, source, any condition in its `notes`, and a `logo_ok` question where it names a customer, leaving out `forbidden` rows), for anyone who wants to answer some now.
5. **Gaps:** the questions under `Gap questions`, with their numbers.
6. **How to answer:** reply by ID (`D-`, `K-`, `C-` or `G-`), or reply "yes to all" to confirm the doc and the competitors together. Anything they don't mention stays as it is.

If that's too long for one message, put parts 2 and 3, and the claims list for part 4, in one Google Doc in the client's folder, and link to it.

**Draft it; never send it.** If Gmail tools are connected and the registry row's `optional_connectors` lists `gmail`, create a Gmail draft to the recipients and tell the user it's in their drafts. Otherwise give the message in the chat, ready to paste. Either way the user sends it, and `signoff_sent` stays `waiting` on the user until they say they have.

**Use plain links.** Write every link in a message to the client as the file's own address, built from its Drive ID: `https://docs.google.com/document/d/<id>/edit` for a doc, or `https://drive.google.com/drive/folders/<id>` for a folder. Never paste a link copied out of an email. The Gmail connector rewrites every link in a draft it saves into a `google.com/url?q=` redirect, which still opens, but through a Google notice page, and nothing you write avoids that. So when you create a Gmail draft, tell the user in one line that its links will show as Google redirects, and list the plain links in the chat, in case they'd rather swap them in before sending.

Then log it, as in step 11 of `drive-intake.md`.

## 4. Apply the answer

Apply only what the client said. Silence is never approval, and a yes to the doc doesn't change a tag on its own. A fact becomes `[confirmed]` only if the client answered it, or said a named section is right.

Before you start, save the text of each level-2 section. Edit table cells by position, last first, holding the doc's revision ID. When you finish, read the doc back and compare: only the sections the answer touches may differ.

**Claims:**

| Client says | `status` | Also set |
|---|---|---|
| Yes | `approved` | `provenance` = `[confirmed]`, `owner` = who said it, `approved_on` = the date; clear `last_checked`, and clear `expires_on` unless they gave an end date |
| Yes, with a condition, or yes to a row whose `notes` hold one they didn't drop | `must_qualify` | As above, with the qualifier wording in `notes` |
| Yes, in other wording | `approved` | As above, with the new wording in `claim` and a dated quote from their reply in `source` |
| No | `forbidden` | `provenance` = `[confirmed]`, `owner` = who said it, the reason in `notes`, and replace the value with its `claim_id` in any section that repeats it |

On any row you approve, remove the hold reasons this answer settles and keep real limits. Set `logo_ok` only from their answer, on every row whose `claim` or `notes` names only that customer. If an answer runs into an open conflict, or a row's source doesn't back it, apply it and say so in your report.

**Conflicts:** only an answer that names the item settles it. Put the value they chose in the section, in place of the `[TBD: <owner>]` line, tagged `[confirmed]`. Change the item's `open` to `settled <date>: <what they chose>`. Treat the claims it held as above: yes for the row that matches, and no for a row that doesn't.

**"Yes to all":** a yes to the doc, plus a yes to the competitor set as listed. It approves no claim and settles no conflict.

**Pillars:** if the client names pillars in their reply anyway, record them under `Content pillars`, tagged `[confirmed]`, for the first content plan to use.

**Competitors:** a yes to the set, by its `K-` IDs or in "yes to all", confirms it as listed. Replace any line about the proposed set with one line giving the agreed set, tagged `[confirmed]`. Delete the lines that are only about a competitor they removed, in any section, and report any line or figure that still counts it. Add a competitor they named with a `[TBD: <owner>]` line for its research, and say that it needs a fresh `outside-in.md` run naming it.

**Corrections:** a "no" sends only the affected sections back through section 1, never the whole doc. Change the fact wherever it appears, drop anything on the line that only supported the old value, and tag it `[confirmed]` if they gave the value, or use a `[TBD: <owner>]` line if they didn't. If the fix raises a new disagreement, add it to the list. Ask only about what changed, and don't send the rest again.

**Record it.** Under a level-3 heading `Sign-off log` in `Sources and intake`, add one line for each reply, with no provenance tag: the date, who answered, what they approved, and which sections changed. Sign-off is done once the client has said yes to the doc, with or without corrections, whether or not they answered any claims. Whatever they left open stays `candidate` or `[TBD: <owner>]`, which is safe to run with.

Add a Run Log row: `workflow` = `company-onboarding`, `trigger` = `chat`, `items` = `0`, `result` = `ok`, or `partial` if anything is still open. Start `note` with the duration, e.g. `9 min; sign-off: 41 claims approved, 2 corrections, 3 conflicts open`.

Set the doc's `Last reviewed` date to today.

Then tell the caller what changed and what's still open. If the client's folder has an `Onboarding Checklist`, end with what's left of onboarding: every row in phases 1 to 3 that isn't `done` or `skipped`, each with what it's waiting on and who owes it. Add `reviewers` if it's still blank. Then say that the workflow phase comes next: the first content plan builds the pillars and the cadence for the client to approve, and only then are scheduled tasks created.
