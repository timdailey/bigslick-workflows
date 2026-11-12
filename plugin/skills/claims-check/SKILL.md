---
name: claims-check
description: Checks a draft against one client's claims library before review. Use as a step inside the Big Slick workflows, or when someone asks to check claims, verify facts or check a draft against approved claims.
---

# Claims check

> Skeleton. Merge in the reusable checks from the existing client content-review skills, with all client specifics removed.

Before starting:

1. Identify the client: a client named in the request first, then the client named in the current Project's instructions, then ask (in a scheduled run, stop and report instead of asking). Load only that client's row from the registry and only that client's Drive folder.
2. Read `${CLAUDE_PLUGIN_ROOT}/references/shared-definitions.md` and use its column names, statuses and folder names exactly.
3. Run the connector check in `${CLAUDE_PLUGIN_ROOT}/references/connector-rule.md` before any other work. If Google Drive or DataForSEO is missing, stop as that file says. Note which optional connectors are active for this client, and use the file's fallback for every step that would need one that isn't.
4. These instructions override any conflicting instructions in the skills this workflow calls (for example, requirement-gathering questions or replying only in chat).

## Steps

1. Load the `Claims library` section of the client's Brand Context with bigslick-client, and no other section. Never use another client's claims.
2. List every factual claim, number, customer name and comparison in the draft.
3. Match each one to a row in the claims library, then mark it:
   - **Supported:** an `approved` row whose `expires_on` is blank or later than today (in the client's timezone), or a `must_qualify` row that also passes that date check and whose qualifier from `notes` appears in the draft.
   - **Flag:** `candidate` (not yet approved), `forbidden`, `expired`, `missing qualifier`, `unsupported` (no matching row), `conflicting` (contradicts a row) or `logo not approved` (the draft shows a customer's logo and that row's `logo_ok` isn't `yes`). Naming a customer doesn't need `logo_ok`.
4. Rewrite or remove flagged claims where you can, and leave the rest for the reviewer. Keep the flag either way, so the reviewer sees what changed.
5. Return the flags with the draft, one line each with the `claim_id` (if any), the claim as drafted and the flag, so send-to-review can show them in `claims_flags`.
