---
name: sme-content-review
description: Reviews a draft through a subject-matter expert's lens, then for AI tells and audience risk, and returns one verdict with a severity per issue. Use as a step inside the Big Slick workflows, right after claims-check, or when someone asks for an expert review of a draft. Also writes SME profiles.
---

# SME content review

Runs on every long-form and LinkedIn draft. Every draft gets the claims, fit, humanize, persona-safety and channel checks. When an SME covers the draft, their expert lens runs too. Everything specific to a client or person comes from their Brand Context, and nothing in this skill is specific to one.

To write an SME's profile instead of reviewing a draft, follow `references/profile-writer.md`.

Before starting:

1. Identify the client: a client named in the request first, then the client named in the current Project's instructions, then ask (in a scheduled run, stop and report instead of asking). Load only that client's row from the registry and only that client's Drive folder.
2. Read `${CLAUDE_PLUGIN_ROOT}/references/shared-definitions.md` and use its column names, statuses and folder names exactly.
3. Run the connector check in `${CLAUDE_PLUGIN_ROOT}/references/connector-rule.md` before any other work. If Google Drive or DataForSEO is missing, stop as that file says. Note which optional connectors are active for this client, and use the file's fallback for every step that would need one that isn't.
4. These instructions override any conflicting instructions in the skills this workflow calls (for example, requirement-gathering questions or replying only in chat).

## Ground rules

- **An expert lens, never an impersonation.** Apply the SME's knowledge and convictions. Never write in their first person, and never invent a quote from them. Write "The draft says X, which is wrong because Y", not "I think".
- **When unsure, flag rather than pass.** If you can't confirm a fact against Brand Context, raise it. A false claim that ships costs far more than one that gets a second look.
- **Use each fact as its provenance tag allows.** A `[TBD: <owner>]` line is not evidence for anything. Never fill one in.

## How a review runs

Load `Audience and ICP`, `Products and positioning`, `Content pillars`, `Claims library`, `Voices`, `Style rules`, `SME profiles` and `Team map` from Brand Context, plus the draft and claims-check's result. If claims-check hasn't run, stop and say so.

Quote or point to the exact line for every issue, say why it's a problem, and give a safer wording. Where the fix needs the author's own words, say what's needed instead of writing them. Give each a severity as you go. If two checks hit the same line, write one flag, at the higher severity.

### 1. Classify the piece

- **Owned marketing:** the client's blog, site, social, email or decks. Selling is expected here. Product names and calls to action are fine, as long as what they sell is true.
- **Contributed:** a piece with a byline, placed in a third-party publication. The bar is stricter, because a named person's reputation is at stake and the reader isn't a prospect.

If you can't tell, assume contributed, and say so as a Minor issue. A LinkedIn post under a person's name is owned, but read it with that person's reputation in mind. If `Style rules` sets a different register for a channel, use that.

### 2. Technical accuracy

Find the SME: the piece's `author` if they have a profile, otherwise the SME whose topics owned cover the draft's subject. If you're unsure whether they do, treat it as covered. Skip this check and say so if no SME covers it.

Read the draft through their profile: their reviewer convictions, red lines and how they phrase findings. Name each finding **false** (contradicts how the science or product works), **unsubstantiated** (may be true, but nothing supports it) or **imprecise** (loose enough to mislead). Don't soften a false claim into an imprecise one.

A claim that is wrong or unsupported on the facts of the field is a finding here, even if no profile rule mentions it. If the profile has `[TBD: <owner>]` lines where the convictions should be, review from what it does say, and add a Minor issue saying it's thin.

For a contributed piece, a loose technical claim that a marketing reader would forgive is a Major.

### 3. Claims overreach

Start from claims-check's flags. Don't redo its matching. For each flag still in the draft:

| Flag | Severity |
|---|---|
| `forbidden`, `conflicting` | Blocker |
| `candidate`, `expired`, `missing qualifier`, `logo not approved` | Major, or Blocker if it's a wrong number, a regulatory claim or an attributed quote |
| `unsupported` | As above, unless the line is the author's opinion, a stance in their `Voice:` section, general knowledge in the field, or a `[confirmed]` or `[sourced A: <url>]` fact in Brand Context. Then it's a Minor at most |

Then read what claims-check can't see in a table match:

- Regulatory wording. Cleared, validated and compliant are different claims. A claim about one product doesn't cover another.
- Compliance as an adjective, with no mechanism behind it.
- Superlatives and competitive claims with no verifiable basis, such as "the only", "the best" or "no competitor".
- A supported claim stretched past what its row says.
- A figure stated as a guarantee when it's a typical or target value.
- Legal triggers and required disclaimers in `Style rules` or `Team map` that the draft misses. If those are still `[TBD: <owner>]`, say this part didn't run.

A contributed piece fails a superlative twice: it's unsubstantiated and out of register.

### 4. Messaging and ICP fit

Check the draft against `Audience and ICP` and `Products and positioning`:

- The right buyer for the product, spoken to as that buyer.
- Each product described as itself. Language that belongs to one product used on another misstates it, and is a Blocker if it misstates a regulatory position.
- Product names and naming rules as written, and nothing that contradicts the positioning.
- The register for the type. In a contributed piece, a product pitch, a call to action or copy that reads like an ad is a Major, even when every fact is right. In owned marketing the same copy is fine.

### 5. Humanize

Run the checks in `references/humanize.md`. These are Minors, unless the whole piece fails the genericness test, which is a Major.

### 6. Persona safety

Read the draft as each audience the author's `Voice:` section names, following `references/persona-safety.md`. If the piece has no author, the author has no section, or it names no audiences, use `Audience and ICP` and say so.

### 7. Channel rules

Check the draft against the Channel rules lines in the author's `Voice:` section, and the rest of that section for tone. A LinkedIn post doesn't go through brand-review, so check it against `Style rules` too. Where the two differ, the author's section wins. A miss is a Minor, or a Major if it breaks a rule the client gave. For a piece with no `Voice:` section, skip this and say so.

### 8. Verdict

| Verdict | When |
|---|---|
| `approve` | Nothing above a Minor |
| `approve_with_edits` | No Blockers, and the Majors are quick to fix |
| `revise` | One or more Blockers, or enough Majors to change the substance, and the piece's main point survives the fixes |
| `reject` | The main point is false or it's for the wrong audience, so fixing the flagged lines would leave nothing worth keeping |

| Severity | Meaning |
|---|---|
| Blocker | Must not publish. A factual error, a wrong number, a false regulatory claim, an unpermissioned quote, or anything else with legal or credibility exposure |
| Major | Fix before publishing. An unsubstantiated claim, imprecise technical or regulatory language, off-ICP framing, or a weak proof point |
| Minor | Worth fixing. Softer imprecision, or a missed chance to sharpen a line |

## What to return

Return this with the draft, so the workflow can pass it to send-to-review:

- `sme_verdict`: one of the four verdicts.
- `sme_flags`: one line per issue, written `<Severity> | <check> | <the line> | <why, and a safer wording>`, where `<check>` is `technical`, `claims`, `fit`, `humanize`, `persona`, `channel` or `not checked`. Use `technical` for any issue about whether the science or the product works as stated, whichever check found it, because that decides whether the SME sees the piece. Add one Minor `not checked` line for each check, or part of one, that didn't run, so the reviewer can see it.
- `sme_name`: the SME from check 2, or blank if none covers it.
- **What's working:** two or three short notes, so the writer knows what to keep.

This step never stops the workflow, whatever the verdict. The reviewer decides.
