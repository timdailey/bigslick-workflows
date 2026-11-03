# Templates

`bigslick-setup` builds each client's Drive folder from these files, following `references/shared-definitions.md`:

| File | Becomes | Notes |
| --- | --- | --- |
| `brand-context.md` | `Brand Context` (Google Doc) | Create from the markdown so headings and the claims table convert. Fill it with company-onboarding and brand-voice-extractor. |
| `content-calendar.csv` | `Content Calendar` (Google Sheet) | Create from the CSV, then apply `content-calendar-format.json` (tab name `Calendar`, frozen header, dropdowns). |
| — | `Briefs/`, `Drafts/`, `Approved/` | Empty folders. |
| `registry-clients.csv`, `registry-run-log.csv` | `Big Slick Registry` (Google Sheet, tabs `Clients` and `Run Log`) | One per install, in the `Big Slick` admin folder. Create from the Clients CSV, add a second sheet with the Run Log header, then apply `registry-format.json`. |

Reference copies are in the BigSlick Harness Drive folder, under Templates: `Client Folder Template` and `Big Slick Registry`.
