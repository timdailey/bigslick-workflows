# Judge passes

## The judge is never the writer

Every pass marked **(judge)** runs in a fresh subagent that never saw the writing: give it only the text being judged, the rubric below and the Brand Context sections the rubric names. Never pass it the outline reasoning, earlier drafts or the writer's notes. Use the Agent (or Task) tool to start it. If the session has no subagent tool (claude.ai chat), run the pass in this context, judging only the text as saved in Drafts, and write `judge: same-context` in the review item's `summary` so the reviewer knows. A Gemini judge can replace the subagent later if a Gemini connector exists.

Each judge returns JSON only, in the shape given for its rubric.

## Prompts move verbatim

ContentForge's prompts for these passes (`review.js`, and `final_stage.js` for the final check) are to be copied here **word for word**, as the spec's "Prompts move verbatim" rule says. Paste each one under its heading, replacing the `PROMPT PENDING` line, and keep the output shape below unless ContentForge's differs, in which case ContentForge's wins.

Until a prompt is pasted, run the pass from the interim rubric under its heading, and add `judge prompts: interim` to the Run Log note, so it's clear the scores aren't ContentForge's yet.

### Recommendations

PROMPT PENDING: paste from ContentForge `review.js`.

Interim rubric: read the draft against the brief, outline and SERP notes. Return `{"quick_wins": [], "seo_geo": [], "content_gaps": [], "h2_rewrites": [{"from": "", "to": ""}]}`, each item one specific, actionable line pointing at the exact passage.

### Persona fit

PROMPT PENDING: paste from ContentForge `review.js`.

Interim rubric: read as the persona from `Audience and ICP`. Score 0 to 100 how well the piece speaks to their job, their pains and their level of knowledge. Return `{"score": 0, "reasons": [], "fixes": []}`.

### Five-lens

PROMPT PENDING: paste from ContentForge `review.js`.

Interim rubric: score each lens 0 to 100 and give the weakest passages. Lenses: credibility (claims backed, sources primary), voice consistency (against the author's `Voice:` section), persona alignment, structure (answer first, scannable, headings carry the argument), and brand safety (the company isn't named before the halfway point and the piece doesn't read as a pitch). Return `{"credibility": 0, "voice": 0, "persona": 0, "structure": 0, "brand_safety": 0, "overall": 0, "findings": []}`, where `overall` is the mean.

### On-page SEO audit

PROMPT PENDING: paste from ContentForge `review.js`.

Interim rubric: check the primary keyword in the title, first 100 words, one H2, slug and meta description; secondary keywords used naturally; heading hierarchy; internal and outbound link counts; passages that answer each PAA question on their own. Return `{"score": 0, "passes": [], "fails": [], "geo_gaps": []}`.

### AI smell test

PROMPT PENDING: paste from ContentForge `review.js`.

Interim rubric: apply `../../sme-content-review/references/humanize.md`. Estimate the percentage of the piece a reader would take as human-written. Return `{"human_pct": 0, "tells": [{"line": "", "tell": "", "fix": ""}]}`. The threshold is 70 (ContentForge's green band).

### Humanize rewrite

PROMPT PENDING: paste from ContentForge `review.js`.

Interim instruction (writer, not judge): rewrite only the passages named in the smell test's `tells`, in the author's voice, keeping every citation and number byte for byte.

### Final check

PROMPT PENDING: paste from ContentForge `final_stage.js`.

Interim rubric: use sme-content-review in Final Check mode for the SME findings, and the AI smell test above for the humanize findings.
