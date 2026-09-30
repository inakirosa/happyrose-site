# Happy Rose LLC website

The company site for Happy Rose LLC (a Colorado LLC that publishes mobile apps), live at **https://happyrose.io**. Decisions and the TODO list are in [docs/PLAN.md](docs/PLAN.md), and the company brand is in [docs/DESIGN.md](docs/DESIGN.md). [CONTRIBUTING.md](CONTRIBUTING.md) has the full how-to: structure, adding an app, DNS and HTTPS, and gotchas.

## Start of every session: check the website kit

The Potato Tracker pages (`apps/potato-tracker/`) get their content from the app repo's website kit:
`/Users/inakirosa/Documents/PotatoTracker/marketing/website-kit/`
- `CHANGELOG.md`: newest first. Each entry says what changed and which site pages it affects
- `README.md`: the asset guide, the colour table and links to the app's design docs
- `copy.md`: the page text
- `privacy-facts.md` (once the app repo adds it): the source of truth for the legal pages. It lists every SDK, permission, piece of data stored on the phone or sent off it, third party, retention period, deletion method, age rule and price. When it changes, update `apps/potato-tracker/privacy/`, `community-guidelines/` and, where relevant, `/terms/` and `/privacy/`

Before anything else:
1. Read the **Kit sync** section at the bottom of this file for the last synced changelog entry and PotatoTracker commit.
2. Read every `CHANGELOG.md` entry newer than that entry.
3. Catch kit changes nobody logged, including uncommitted ones:
   ```
   git -C /Users/inakirosa/Documents/PotatoTracker log --oneline <last synced commit>..HEAD -- marketing/website-kit
   git -C /Users/inakirosa/Documents/PotatoTracker status --short marketing/website-kit
   ```
4. If anything is new, tell the user in a few lines what changed and which pages it affects. **Ask before applying it**, unless they've already asked for a sync.

## Syncing

1. Re-copy changed assets from the kit into `apps/potato-tracker/img/`. Compare with `cmp` and copy only what differs.
2. Update the affected pages from `copy.md` and `README.md`. Use the copy word for word. Don't invent features or facts.
   - For legal pages, the facts come from `privacy-facts.md`. Keep the plain-English style, and change the policy's effective date whenever what it says changes.
   - List anything a lawyer should check, and any change the App Store privacy label or Google Play Data safety form needs, in `~/Documents/happyrose-legal-checklist.md`. That file is outside the repo, so it's never published.
3. Update **Kit sync** below with today's date, the newest changelog entry applied, and the PotatoTracker commit (`git -C /Users/inakirosa/Documents/PotatoTracker rev-parse --short HEAD`). If you applied uncommitted kit changes, say so there.

## Rules

- **The app repo is read-only.** Never edit, commit or regenerate anything in `/Users/inakirosa/Documents/PotatoTracker`. If the kit looks wrong, tell the user.
- **On Potato Tracker pages, follow the app's rules:**
  - Always write "potato points" in full, never "points" or "pts" alone.
  - Don't reword lines listed in the app's `docs/APPROVED.md`.
  - Use the colours in the **Light** column of the kit README (the `.app-potato` tokens in `assets/css/site.css`) and the fonts in the app's `docs/DESIGN.md` (Fredoka for headings, Nunito for text).
- **Light only for now.** Use `color-scheme: light`, with no `prefers-color-scheme: dark` overrides, dark tokens or theme toggle. The kit README's Dark column is for reference only.
- **Personal details:** never publish a home address or phone number.
- **Legal pages:** don't publish anything the app doesn't actually do. Planned features are written as "when you use community features" and must match the app's backend plan (`docs/BACKEND.md` in the app repo).
- **Email:** app pages use support@happyrose.io, and company pages use hello@happyrose.io.
- **Commits:** the user makes all git commits. Don't commit or push unless they explicitly ask in that session.

## Site basics

- **Stack:** plain HTML and CSS, with no framework, build step or JavaScript. All styles are in `assets/css/site.css`: company tokens in `:root`, and each app's tokens under a body class (`.app-potato`). Fonts are self-hosted in `assets/fonts/` (SIL OFL).
- **Pages:** each page is a standalone `index.html` in its folder, with the header and footer copied into each one. When you change the header or footer, change every page.
  - `/` and `/404.html`
  - `/privacy/`, `/terms/`, `/support/`
  - `/apps/potato-tracker/` plus its `support/`, `privacy/` and `community-guidelines/`
  - When you add or remove a page, update `sitemap.xml`
- **Run locally:** `python3 -m http.server 8787` in this folder, then open http://localhost:8787. That server doesn't serve `404.html` for missing paths, so open `/404.html` directly.
- **Deploy:** GitHub Pages from `main` at the repo root, in the public repo `inakirosa/happyrose-site`. Pushing to `main` publishes within about a minute. The `CNAME` file holds `happyrose.io`, and `.nojekyll` turns off Jekyll.
- **Domain and DNS:** happyrose.io is registered on Cloudflare, and Cloudflare also runs its DNS:
  - A and AAAA records for GitHub Pages
  - `www` as a CNAME to inakirosa.github.io
  - the `_github-pages-challenge-inakirosa` TXT record for domain verification
  - All of them DNS only, not proxied
- **Email:** Cloudflare Email Routing forwards hello@ and support@ to the owner's inbox.

## Kit sync
Last synced: 2026-09-29
Last synced changelog entry: 2026-09-29: World map with every dish; the map isn't "hidden"
Last synced PotatoTracker commit: c8ef7d0, plus that entry's changes, which weren't committed in the app repo yet (CHANGELOG.md, README.md, copy.md, world-map-all-dishes.svg). Next session: once the app repo commits them, re-run the checks and confirm nothing differs.
