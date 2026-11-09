# Templates

`bigslick-setup` builds each client's Drive folder from these files, following `references/shared-definitions.md`:

| File | Becomes | Notes |
| --- | --- | --- |
| `brand-context.md` | `Brand Context` (Google Doc) | Create from the markdown so headings and the claims table convert. Fill it with company-onboarding, brand-voice-extractor and sme-content-review's profile writer. |
| `content-calendar.csv` | `Content Calendar` (Google Sheet) | Create from the CSV, then apply `content-calendar-format.json` (tab name `Calendar`, frozen header, dropdowns). |
| — | `Briefs/`, `Drafts/`, `Approved/`, `Research/` | Empty folders. |
| `registry-clients.csv`, `registry-run-log.csv` | `Big Slick Registry` (Google Sheet, tabs `Clients` and `Run Log`) | One per install, in the `Big Slick` admin folder. Create from the Clients CSV, add a second sheet with the Run Log header, then apply `registry-format.json`. |
| `review-page.html` | The client's review page (artifact) | Replace `__CLIENT_NAME__`, publish with the `db` and `user` (scopes `profile` and `email`) capabilities, store the URL in `review_page_url`. Reviewers need Contributor access to decide. Reads and writes the `reviews` collection in shared definitions §8. |

Reference copies are in the BigSlick Harness Drive folder, under Templates: `Client Folder Template` and `Big Slick Registry`.
