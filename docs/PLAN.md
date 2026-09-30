# Happy Rose LLC website: plan

The company site for Happy Rose LLC (Colorado, formed September 29, 2026), which publishes mobile apps. Live at https://happyrose.io on GitHub Pages. The owner makes all git commits.

**Why it exists:**
- Apple needs a real public website on the company's domain before converting the developer account (Team ID C677TFUAV4) from individual to organization.
- App Store Connect and Google Play need marketing, support and privacy policy URLs.

## Decisions

| # | Decision | Choice |
|---|---|---|
| D1 | Stack | Plain HTML and CSS. No framework, no build step, no JavaScript |
| D2 | Hosting | GitHub Pages from `main` (repo `inakirosa/happyrose-site`), with a `CNAME` for happyrose.io. DNS on Cloudflare |
| D3 | Folder | `~/Documents/happyrose-site`. iCloud is fine here because nothing gets code-signed |
| D4 | Brand | Happy Rose has its own look: a smiling rose, rose pink on cream, Nunito. See [DESIGN.md](DESIGN.md) |
| D5 | App pages | Each app keeps its own look on its own pages, through a body class that swaps the design tokens (`.app-potato`) |
| D6 | Legal layout | Per app: `/apps/<app>/privacy/` and `/apps/<app>/support/` (the URLs for the stores). Company-wide: `/privacy/` (the website, plus links to each app's policy), `/terms/` and `/support/` (a list of the apps) |
| D7 | Email | App pages and app policies use **support@happyrose.io**. Company pages use **hello@happyrose.io**. Both forward through Cloudflare Email Routing |
| D8 | Personal details | Never publish a home address or phone number |
| D9 | Fonts | Nunito and Fredoka, self-hosted from `assets/fonts` (SIL OFL, licences included), so no Google Fonts requests |
| D10 | Potato Tracker content | Copy and art come from `PotatoTracker/marketing/website-kit` (fact-checked against the app). Approved wording is in `PotatoTracker/docs/APPROVED.md`, and "potato points" is always spelled out |
| D11 | 404 | Uses the app's style: the dropped-potato art and "Well, that's a hot potato." |
| D12 | Store button | A "Coming soon to the App Store" button with no link yet. On launch day, swap in Apple's official badge with the real link |

## Pages

- `/`: company intro, app grid, values, contact
- `/apps/potato-tracker/`: the product page
- `/apps/potato-tracker/support/`: FAQ and support@
- `/apps/potato-tracker/privacy/`: first draft
- `/apps/potato-tracker/community-guidelines/`: first draft
- `/privacy/`, `/terms/`, `/support/`: company-wide
- `/404.html`

## Adding an app

1. Make a folder `apps/<app>/` with `index.html`, `support/`, `privacy/` and an `img/` folder. Copy Potato Tracker's pages as a starting point.
2. If the app has its own look, add a `.app-<name>` token block to `assets/css/site.css`.
3. Add a card to the grid on `/` and on `/support/`, a link on `/privacy/`, and the URLs to `sitemap.xml`.

## TODO

- [x] Build the site (home, app page, support, first drafts of the legal pages, 404, CNAME, favicon, meta and Open Graph tags)
- [ ] Legal: inspect the app's SDKs and data, then write the full privacy policy, terms and community guidelines, with lawyer flags and the App Store privacy label and Google Play Data safety answers
- [ ] Screenshots: add a phone-frame grid once the 8 shots are in `PotatoTracker/marketing/website-kit/screenshots/` (WebP plus a fallback)
- [ ] Deploy: GitHub repo, Pages, Cloudflare DNS (apex and www), domain verification, Enforce HTTPS
- [ ] Email: Cloudflare Email Routing for hello@, support@ and inaki@. Sending from Gmail or iCloud+
- [ ] Apple readiness check (D-U-N-S case 11043942). Put Happy Rose LLC as the organization on the domain's registrant contact
- [ ] Launch day: Apple's badge plus the App Store link, and a TestFlight note if needed
- [ ] Later: `/.well-known/apple-app-site-association` and `assetlinks.json` for universal links
