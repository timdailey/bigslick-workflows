---
name: bigslick-onboarding
description: Fills a Big Slick client's Brand Context in Google Drive from one intake request, outside-in research and each author's posts, then reconciles it and gets the client's sign-off. Use from bigslick-setup, or when someone asks to onboard a client into Big Slick, run intake, re-run outside-in research, handle a late document, send or apply sign-off, or build an author's voice.
---

# Big Slick onboarding

Builds one client's Brand Context: the doc every workflow reads. The client must already have a registry row and a client folder; `bigslick-setup` creates both and calls this skill. Column names, tags, statuses and headings are in `${CLAUDE_PLUGIN_ROOT}/references/shared-definitions.md`; follow them exactly.

Before starting:

1. Load the client with bigslick-client and name them.
2. Run the connector check in `${CLAUDE_PLUGIN_ROOT}/references/connector-rule.md`. Note whether Tavily, Perplexity and ConnectSafely are present. All three are optional: outside-in research and author voices each have a route without them (in their reference files), so a missing one never blocks a step.
3. This plugin calls two skills from the core Big Slick plugins, unchanged: `outside-in-report` (core `bigslick` plugin) for the research stages, and `brand-voice-extractor` (`bigslick-strategy` plugin) for the voice dimensions. If one isn't installed, say which plugin is needed and skip the step that uses it.
4. These instructions override any conflicting instructions in the skills this one calls, including the local context-pack route in `company-onboarding`, which doesn't apply to a registry client.

## Steps

| Step | Follow | Checklist rows it updates |
|---|---|---|
| Ask once, read what arrives, tag every fact, fill the Claims library, team map and channels, and write to the doc | `references/drive-intake.md` | `intake_request` to `website` |
| Outside-in research on the company and five competitors, started as soon as the website URL and location are known | `references/outside-in.md` | `research` |
| Reconcile disagreements, ask the gap questions, send the doc for sign-off and apply the answers | `references/sign-off.md` | `reconcile` to `signoff_applied` |
| One `Voice:` section per author and per SME | `references/author-voices.md` | `voices` |

Start with `drive-intake.md`: it says when to start outside-in research and when to hand over to `sign-off.md`. Run `author-voices.md` after the SME profiles are written (sme-content-review's `references/profile-writer.md`), because SMEs' voices come from the same material.

Each reference says which Run Log row it adds. Messages to the client are drafted, never sent: a Gmail draft when `gmail` is active, otherwise text in the chat.
