# Rewrite guards

Every rewrite (polish, humanize, word_count_fix, revise with instructions, and applying Final Check findings) passes these checks before its output replaces the input. Breaking one is a regression, not a tradeoff.

## 1. A rewrite may lose facts, never gain them

After the rewrite, list every number in the output that is 10 or more, or carries `%`, `$`, `£` or `€`, and every URL. Compare them to the input (plus the fact pack and Claims library for polish). Anything new: revert that passage to the input and name it in `Draft notes` and the review item's `summary`. From ContentForge `provenance.js`.

## 2. Whole document out, never a fragment

Reject the rewrite and keep the input if the output:

- is much shorter: under 85% of the input's word count, unless the step was word_count_fix aiming lower;
- drops an H2 or H3 the input had, or the FAQ when `has_faq` is `true`;
- leaves a placeholder: `[CHECK`, `[TBD`, `TODO`, `lorem`, `XX`, or an empty heading.

Log what was rejected. From ContentForge `assertWholeDocument` in `final_stage.js`.

## 3. Citations stay byte for byte

Every link, its anchor text and any quoted source text stay exactly as they were. A humanize pass never touches them.

## 4. Banned characters

Strip every character `Style rules` bans (em dashes and others) after each rewrite.

## 5. Humanize stop rule

1. **Deterministic clean-up first:** replace banned characters and the vocabulary in `../../sme-content-review/references/humanize.md` where a plain word fits; no model call.
2. Smell test (judge). If `human_pct` ≥ 70, stop with `exit_reason` = `threshold`.
3. Rewrite aimed at the surviving tells only. Guards 1 to 4 apply.
4. Smell test (judge) again. Stop at the threshold, or after the second rewrite with `exit_reason` = `max_passes`.

Save `humanize_iterations` (0, 1 or 2) and `exit_reason` to the review item. Every rewrite loop has a logged stop.

## 6. Scores describe the text handed on

Any rewrite after scoring makes the scores stale. Re-score (Stage 3 step 7) after the last rewrite and before the gate. After applying Final Check findings, the link check resets and the findings are marked stale.
