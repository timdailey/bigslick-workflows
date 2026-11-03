---
name: bigslick-setup
description: Sets up Big Slick for a new company or client. Use when someone says set up Big Slick, add a client, onboard a client or types /bigslick-setup.
---

# Big Slick setup

> Skeleton.

## Steps

1. Check the required services: Google Drive, and DataForSEO as a custom connector at `https://mcp.dataforseo.com/mcp` (signed in with OAuth). Stop until both work. Then list optional connectors and offer to connect any the client wants. Ask for the client's SEO market (location and language) and a per-run DataForSEO call budget for the registry.
2. If they don't exist yet, create the `Big Slick` admin folder and the `Big Slick Registry` Sheet (tabs `Clients` and `Run Log`). Add the client's row, with every column in shared definitions.
3. Create the client's folder under `Big Slick/Clients/`, with the files and subfolders in shared definitions, and record their IDs in the registry row.
4. Build the Brand Context doc with company-onboarding (writes to Drive).
5. Capture voices with brand-voice-extractor: ask each author to paste 10-15 posts.
6. Publish the client's review page and dashboard from the templates.
7. Create the scheduled tasks for the chosen workflows and cadence. Each task names its client.
8. Write Project instructions for the user to paste into a new Claude Project for this client.
9. Run one workflow end to end with the user as reviewer.
