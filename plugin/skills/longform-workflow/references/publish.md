# Publish stage

Publish formats the approved text and judges nothing. Nothing here may change what the text says.

## html_convert

PROMPT PENDING: paste from ContentForge `publish.js`.

Convert the approved Drafts text to clean body HTML: no H1 (the CMS adds the title), `<h2>`/`<h3>` as in the text, `<p>`, `<ul>`/`<ol>`, `<a href>` with the original URLs, `<strong>`/`<em>` only where the text has them. No inline styles, classes or scripts. Remove the `Draft notes` section.

## geo_faq

PROMPT PENDING: paste from ContentForge `publish.js`.

Only when `has_faq` is `false`. Add an `<h2>Frequently asked questions</h2>` block with 3 to 5 questions from the brief's AI-visibility questions and the PAA list, each answered outright in 40 to 70 words, using only facts already in the text. Guard 1 in `rewrite-guards.md` applies: no new numbers or links.

## schema

PROMPT PENDING: paste from ContentForge `publish.js`.

JSON-LD in one `<script type="application/ld+json">` block, built after geo_faq:

- `Article`: `headline`, `description` (the meta description), `author` (a `Person` with the author's name, or the `Organization` for a company byline), `publisher` (the client's `Organization`, from `Company facts`), `datePublished` (the row's `publish_date`), `keywords`.
- `FAQPage`, when the piece has an FAQ, with each question and its answer text exactly as on the page.

## personal_post

The author's LinkedIn post about the article, in their `Voice:` section, following its channel rules: a hook that works without the link, the article's one main point, and `[ARTICLE URL]` where the link goes (the checklist puts it in the first comment).

## Publish checklist

Put this in the Approved doc and the Gate 4 email:

1. Create the post in the CMS (or open the HubSpot draft).
2. Paste the title, the HTML, the meta description and the slug.
3. Set the author and tags.
4. Preview: check the heading hierarchy and the links.
5. Publish, and copy the live URL.
6. Publish the LinkedIn post, and add the live URL as the first comment.
7. Enter the live URL on the review page (or in the calendar's `published_url`).
