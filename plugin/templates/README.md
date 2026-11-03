# Templates

`bigslick-setup` builds each client's Drive folder from these files, following `references/shared-definitions.md`:

| File | Becomes | Notes |
| --- | --- | --- |
| `brand-context.md` | `Brand Context` (Google Doc) | Create from the markdown so headings and the claims table convert. Fill it with company-onboarding and brand-voice-extractor. |
| `content-calendar.csv` | `Content Calendar` (Google Sheet) | Create from the CSV, then apply `content-calendar-format.json` (tab name `Calendar`, frozen header, dropdowns). |
| — | `Briefs/`, `Drafts/`, `Approved/` | Empty folders. |

A reference copy of the finished folder is in the BigSlick Harness Drive folder, under Templates/Client Folder Template.
