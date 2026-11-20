# Big Slick quick start

From nothing to a company's onboarding underway, in about 20 minutes. You'll install two sets of plugins, connect two services, make a Claude Project for the company, and send one prompt.

## 1. Before you start

- **A paid Claude plan** (Pro, Max, Team or Enterprise). On Team and Enterprise, an Owner may need to allow the two plugin sources below and the DataForSEO connector.
- **A Google account with Drive.** Big Slick keeps everything there: the registry, each company's folder, Brand Context, the calendar and drafts. Use the account the team shares files from.
- **A DataForSEO account with some balance**, in the company's own name if you're a consultant: each client pays for its own keyword and search data. A content plan costs well under $1; onboarding research up to about $15.

## 2. Install the plugins

In Claude, open **Customize** in the left sidebar, then the **Plugins** tab.

**Big Slick (the marketing skills)**

1. Click **Add**, then **Add marketplace**, then **Add from a repository**.
2. Enter `frankdays/bigslick`.
3. In **Discover**, add these four plugins:
   - `bigslick` (the core, including onboarding and research)
   - `bigslick-strategy` (brand voice and positioning)
   - `bigslick-content` (writing and editing)
   - `bigslick-seo` (briefs and SEO)

**Big Slick workflows (the content operation)**

1. Click **Add**, then **Add marketplace**, then **Add from a repository**.
2. Enter `timdailey/bigslick-workflows`.
3. In **Discover**, add `bigslick-workflows`.

The workflows plugin calls the Big Slick skills, so install both sets. Use the latest version of each; the workflows README says which Big Slick version it was tested with.

**Updating later:** when either repo changes, update the plugin from the Plugins page. If you don't see an update option, remove the plugin and add it again from Discover.

## 3. Connect the services

Still in **Customize**, open **Connectors**.

**Required**

- **Google Drive** and **Google Sheets**, signed in to the shared Google account. **Google Docs** is strongly recommended too: it lets Big Slick edit Brand Context in place.
- **DataForSEO**, as a custom connector:
  1. Click **Add**, then **Add custom connector**.
  2. Name: `DataForSEO`. URL: `https://mcp.dataforseo.com/mcp`.
  3. Click **Add**, then **Connect**, sign in to DataForSEO and approve.
  4. On Team or Enterprise, an Owner adds it under **Organization settings > Connectors**, and then each person clicks **Connect**.

**Optional** (Big Slick works without them and says what it did instead)

- **Gmail:** drafts the messages to the company, such as the intake request. Without it, the text appears in the chat for you to send.
- **Slack:** alerts reviewers.
- **Tavily** and **Perplexity:** faster, deeper outside-in research. Without them, Claude researches with its own web search.
- **ConnectSafely:** reads authors' LinkedIn posts for their voices. Without it, Claude uses public posts, other writing and samples the author sends.

## 4. Make a Project for the company

1. Open **Projects** in the left sidebar and click **+ New Project**.
2. Name it after the company, e.g. `Acme: Big Slick`. On Team and Enterprise, keep it private unless the whole team works on this company.
3. Leave the instructions empty for now. Setup writes them near its end, for you to paste in.

**One Project per company.** A consultant with five clients makes five Projects. That's what keeps each client's context out of the others' chats.

## 5. The first prompt

Open a new chat inside the Project and send this, with the brackets filled in. Giving everything up front means setup doesn't stop to ask.

```
Set up Big Slick for a new client.

- Client name: [Client Name]
- Website: [https://...]
- Location for search data: [United States]   Language: [English]
- Timezone: [America/New_York]
- Client contact (sends documents, answers questions): [Name, email]
- Reviewers: [name, email; name, email]
- Authors / SMEs: [Name, role, LinkedIn URL; note who is an SME]
- Channels: [blog, linkedin]
- Content types: [blog_post, guide, social_post]
- DataForSEO budget per run: [25] calls
- Optional connectors to use: [none, or gmail / slack / hubspot ...]
```

For your own company rather than a client, the same prompt works: say "my company" and put yourself as the contact.

## 6. What happens next

1. **Setup checks your connectors** and helps fix anything missing.
2. **It builds the company's Drive folder** under `Big Slick/Clients/`, adds the company to the `Big Slick Registry`, and shows you the **Onboarding Checklist**: the list of every onboarding step and where it stands.
3. **It drafts the intake request**, one message asking the company for its documents (brand guidelines, messaging, customer stories, plans). It drafts, never sends: you send it.
4. **It starts outside-in research** on the company and five competitors from the website.

From there, onboarding runs as documents arrive:

- **The company drops files** in its `Intake` folder in Drive, or answers the Onboarding Questionnaire sheet.
- **Pick it up in a new chat** in the same Project: say `Continue onboarding [Client Name]`. The checklist tells it where it left off, so you never need to repeat yourself.
- **Sign-off:** when Brand Context is filled, onboarding drafts a short list of questions for the company and asks them to review the doc. Their answers turn assumptions into confirmed facts.
- **Go-live:** once the Project instructions from setup are pasted in and sign-off is done, say `Build a content plan`. The first plan sets the pillars and cadence with the company's approval.

## If something goes wrong

| You see | Do this |
|---|---|
| "Google Drive (or DataForSEO) isn't connected" | Connect it in **Customize > Connectors**, then start a new chat in the Project |
| A skill you expected isn't found | Check all five plugins are installed and up to date |
| Adding a marketplace fails | Check the spelling (`frankdays/bigslick`, `timdailey/bigslick-workflows`). On Team or Enterprise, ask an Owner to allow GitHub plugin sources |
| Onboarding asks something you already told it | Note it for the team: that's a bug worth fixing |
