# Contributing to happyrose.io

This is the website for **Happy Rose LLC**, a Colorado limited liability company that makes and publishes mobile apps. It's live at **https://happyrose.io**.

The site exists for three reasons:
1. **Apple enrollment.** Apple needs a real, public website on the company's own domain before it converts the developer account from an individual to an organization. The legal name on the site must match exactly: **Happy Rose LLC**.
2. **Store URLs.** App Store Connect and Google Play need a marketing URL, a support URL and a privacy policy URL for each app.
3. **Home for our apps.** It's where people find our apps and learn about them.

If you're new here, whether you're a person or an AI session, read this file top to bottom once. Then use the checklists in [Making a change](#making-a-change) and [Adding a new app](#adding-a-new-app).

**Related files:**
- [CLAUDE.md](CLAUDE.md): the short rules AI sessions follow, including the Potato Tracker kit sync
- [docs/PLAN.md](docs/PLAN.md): the decisions log and TODO list
- [docs/DESIGN.md](docs/DESIGN.md): the company brand (colours, logo, type)

---

## The basics

**Stack:** plain HTML and CSS, with no framework, build step, JavaScript or package manager. What's in the repo is exactly what's served.

**Hosting:** GitHub Pages, served from the `main` branch at the repo root. The repo is `inakirosa/happyrose-site`, and it's public, so everything in it is public.

**Deploying:** pushing to `main` deploys. GitHub rebuilds and publishes within about a minute. To watch a deploy:
```
gh api repos/inakirosa/happyrose-site/pages/builds/latest --jq '{status, commit}'
```

**Domain:** happyrose.io is registered on Cloudflare, and Cloudflare also runs its DNS (details in [Domain, DNS and HTTPS](#domain-dns-and-https)).

**Email:** Cloudflare Email Routing forwards @happyrose.io addresses to the owner's inbox:
- **support@happyrose.io** is for app help. It appears on app pages and app policies.
- **hello@happyrose.io** is for everything else. It appears on company pages.

**Commits:** the owner makes all commits. AI sessions don't commit or push unless explicitly asked in that session.

### Running it locally

```
cd ~/Documents/happyrose-site
python3 -m http.server 8787
# open http://localhost:8787
```

Python's server doesn't serve `404.html` for missing paths the way GitHub Pages does. To see the 404 page, open http://localhost:8787/404.html directly.

### What's where

```
index.html                     Home: intro, app grid, values, contact
404.html                       "Well, that's a hot potato." (served by GitHub Pages for any missing path)
privacy/index.html             Company privacy notice (website + email), links to each app's policy
terms/index.html               Company-wide Terms of Use for all apps (includes Apple's required EULA terms)
support/index.html             Company support: app cards, common questions, both email addresses
apps/<app>/index.html          Each app's product page
apps/<app>/support/            Each app's FAQ and support email  ← App Store "Support URL"
apps/<app>/privacy/            Each app's privacy policy          ← App Store "Privacy Policy URL"
apps/<app>/community-guidelines/   Only for apps with user content (Apple guideline 1.2)
apps/<app>/img/                That app's icon, art and share image
assets/css/site.css            All styles: company tokens, then one token block per app
assets/fonts/                  Self-hosted Nunito + Fredoka (SIL OFL, licences included)
assets/img/                    Company logo (smiling rose) and share image
favicon.svg, apple-touch-icon.png
CNAME                          "happyrose.io". GitHub Pages needs it; don't delete it
.nojekyll                      Tells GitHub Pages not to run Jekyll
robots.txt, sitemap.xml        Update the sitemap when pages are added or removed
docs/                          PLAN.md and DESIGN.md (public, like everything else)
CLAUDE.md, CONTRIBUTING.md     How to work on the site
```

**Store URLs for Potato Tracker:**
- **Marketing:** https://happyrose.io/apps/potato-tracker/
- **Support:** https://happyrose.io/apps/potato-tracker/support/
- **Privacy policy:** https://happyrose.io/apps/potato-tracker/privacy/
- **Terms:** https://happyrose.io/terms/

---

## How pages are built

**Each page stands alone.** Each page is a complete `index.html` in its own folder, so URLs end in `/` (for example `/terms/`). There are no templates or includes, which means:
- The `<head>` (title, description, canonical URL, Open Graph and Twitter tags, theme colour, favicon), the header and the footer are copied into every page.
- When you change the header, the footer or a shared meta tag, change it on **every page**. `grep -rl "site-footer" --include=index.html .` lists them all, plus `404.html`.
- Use absolute paths starting with `/` (`/assets/css/site.css`). The 404 page is served at any URL, so relative paths would break there.

**The footer on every page** says "© 2026 Happy Rose LLC" and links to Privacy, Terms and Support. Don't remove it: it's part of what Apple checks.

**Never publish** a home address or phone number, anywhere, including meta tags and images.

### Design system

All colours and fonts are CSS variables in `assets/css/site.css`:

- **Company brand** (the `:root` block):
  - rose pink accent `#B33A5A`
  - cream page `#FFF9F6`
  - ink text `#3A2230`
  - Nunito for everything
  - the logo is a smiling rose (`assets/img/logo.svg`), a simple face on a rose shape
- **Each app keeps its own look on its own pages.** App pages put a class on `<body>` (for example `class="app-potato"`), and that class overrides the tokens: `--bg`, `--surface`, `--tint`, `--text`, `--muted`, `--accent`, `--on-accent`, `--line`, `--focus`, `--font-display` and `--display-weight`. The shared header and footer adopt the app's colours automatically.
- **Light only for now.** `color-scheme: light`, with no `prefers-color-scheme: dark` rules, dark tokens or toggle. Pages must look the same when the visitor's computer is in dark mode. The dark values in docs/DESIGN.md are kept for later.
- **Phones first.**
  - 16px side gutter.
  - Grids use `repeat(auto-fit, minmax(min(100%, 280px), 1fr))`, so nothing scrolls sideways.
  - If a grid child holds a scrolling row, give it `min-width: 0`, or it'll stretch the page.
  - Tables in `.doc` pages stack into cards below 600px. Each `<td>` needs a `data-label`.

**Reusable classes:**

| Class | Use |
|---|---|
| `.hero`, `.lede`, `.kicker` | Page intros |
| `.app-grid`, `.app-card`, `.app-card--empty` | App cards on home and support |
| `.panel`, `.note`, `.toc`, `.pill`, `.button` | General UI |
| `.doc` (with `.wrap.narrow`) | Long text: legal pages and FAQs. `<details>` becomes FAQ cards |
| `.table-wrap` + `<table>` in `.doc` | Tables that stack on phones |
| `.app-hero`, `.stage`, `.steps`, `.features`, `.feature`, `.wide`, `.joke`, `.final` | Product page sections (Potato Tracker uses them all) |
| `.support-app`, `.contact-grid` | Company support page |

---

## Where content comes from

### Potato Tracker: the website kit

The Potato Tracker pages don't invent anything. Their text and art come from a **website kit** in the app's repo:
`~/Documents/PotatoTracker/marketing/website-kit/`

| Kit file | What it's for |
|---|---|
| `CHANGELOG.md` | Newest first: what changed in the kit and which site pages it affects |
| `copy.md` | All page text, used **word for word** (hero, how it works, features, FAQ, meta tags) |
| `README.md` | Asset guide, the colour table (use the **Light** column) and a screenshot shot list |
| `privacy-facts.md` | Once added: the source of truth for the legal pages (SDKs, data, retention, deletion, age, price) |
| `icon/`, `illustrations/` | App icon, scenes, avatars, dishes and share card |

**Sync workflow** (also in CLAUDE.md, which records the last synced entry and commit):
1. Read CLAUDE.md's **Kit sync** section to see what was last applied.
2. Read newer `CHANGELOG.md` entries.
3. Check for unlogged or uncommitted kit changes:
   ```
   git -C ~/Documents/PotatoTracker log --oneline <last commit>..HEAD -- marketing/website-kit
   git -C ~/Documents/PotatoTracker status --short marketing/website-kit
   ```
4. Copy only the assets that changed (`cmp` first) into `apps/potato-tracker/img/`, and update the affected pages.
5. Update **Kit sync** in CLAUDE.md.

**Rules:**
- **The app repo is read-only** from here. Never edit, commit or regenerate anything in it. If the kit looks wrong, tell the owner.
- **Always write "potato points" in full,** never "points" or "pts" alone.
- **Don't reword approved lines** listed in `~/Documents/PotatoTracker/docs/APPROVED.md`.
- **Spell dish names the way they're spelled where they come from:** Chipsi mayai, Papa a la huancaína, Pierogi, etc.
- **Facts must match the kit.** Today: 55 dishes, 24 in the Log to start, 62 badges, 12 avatar categories, a 14 potato points weekly target, iPhone first with Android planned, a one-time purchase (the price isn't final, so it isn't shown), no ads, no subscriptions.

### Legal pages

- **Only claim what the app actually does.** Check its code (dependencies, permissions, network calls, what it stores) or its `privacy-facts.md`. Planned features are written as "when you use community features" and must match the app's backend plan (`docs/BACKEND.md` in the app repo).
- **Plain English.** Include an effective date, and change it whenever what the page says changes.
- **Lawyer notes and store answers stay out of this repo.** The notes for a lawyer, and the App Store privacy label and Google Play Data safety answers, live in `~/Documents/happyrose-legal-checklist.md`, outside the repo on purpose so they're never published. Update that file whenever a policy changes.
- **The terms are company-wide** and cover every app, including the terms Apple requires when an app uses its own licence. Only change them for a new kind of feature: payments, subscriptions, a new kind of user content.

---

## Making a change

1. Pull first: `git pull`. GitHub sometimes commits to `main` itself (see [Gotchas](#gotchas)).
2. If you're touching Potato Tracker pages, do the kit check above.
3. Edit the HTML and CSS. Keep the header, footer and `<head>` identical across pages.
4. Run it locally and check:
   - **Phone width:** 390px, with no sideways scrolling.
   - **Desktop:** around 1280px.
   - **Dark mode on your computer:** pages must stay light.
   - **Links:** run the [link check](#link-check).
5. The owner reviews, commits and pushes. Then confirm it's live:
   ```
   gh api repos/inakirosa/happyrose-site/pages/builds/latest --jq .status   # wait for "built"
   curl -s https://happyrose.io/<page>/ | grep -c "<some new text>"
   ```

### Link check

Run it from the site folder. It reports broken internal links, images and `#anchors`, plus any "points" that isn't "potato points":

```
python3 - <<'EOF'
import re, os, glob
bad = 0
for f in glob.glob('**/*.html', recursive=True):
    t = open(f).read()
    for u in re.findall(r'(?:href|src|srcset)="([^"]+)"', t):
        if u.startswith(('http', 'mailto')): continue
        if u.startswith('#'):
            if f'id="{u[1:]}"' not in t: print(f, 'missing anchor', u); bad += 1
            continue
        p = u.split('#')[0]; path = '.' + p + ('index.html' if p.endswith('/') else '')
        if not os.path.exists(path): print(f, '->', u); bad += 1
    for m in re.finditer(r'(\w+)\s+points?\b', re.sub('<[^>]+>', '', t)):
        if m.group(1).lower() != 'potato': print('check wording:', f, m.group(0))
print('broken:', bad)
EOF
```

### Screenshots and PNGs without extra tools

The Mac has no ImageMagick or Inkscape, but headless Chrome can do both jobs:

```
CHROME="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"
# Screenshot a page (add --blink-settings=preferredColorScheme=0 to test dark mode)
"$CHROME" --headless --disable-gpu --hide-scrollbars --screenshot=out.png --window-size=1280,900 http://localhost:8787/
# SVG → PNG (share images, apple-touch-icon): wrap it in a tiny HTML page at the exact size
echo "<html><body style='margin:0'><img src='file:///path/card.svg' width=1200 height=630></body></html>" > card.html
"$CHROME" --headless --disable-gpu --hide-scrollbars --force-device-scale-factor=1 --screenshot=card.png --window-size=1200,630 file://$PWD/card.html
```

- **Narrow widths:** headless Chrome won't lay out narrower than about 500px, so a 390px screenshot is misleading. To test phone width, load the page in a 390px-wide `<iframe>` inside a small wrapper HTML file.
- **Resizing a PNG:** `sips -z 180 180 in.png --out out.png` works.

**Image sizes:**
- Share images (`og:image`): 1200×630 PNG.
- Apple touch icon: 180×180 PNG.
- Big SVGs (the world map is about 450 KB) get `loading="lazy"`.

---

## Adding a new app

Use Potato Tracker as the template. Replace `<app>` with a short lowercase slug (for example `sock-counter`) and `<App Name>` with the real name.

**Before you start, gather:**
- the app's name, a one-line description and a tagline
- its icon (1024 px PNG, plus 512 and 180, and an SVG if there is one)
- a few illustrations or screenshots
- its colours and fonts, if it has its own look
- **the privacy facts** (inspect the app, or read its `privacy-facts.md`): every SDK, permission, piece of data stored or sent, third party, retention period, deletion method, age rule, price and purchase model, and ads or tracking (yes or no)
- whether users can see each other's content (if so, it needs community guidelines, plus report and block)

**Steps:**

1. **Folders:** copy the Potato Tracker structure:
   ```
   mkdir -p apps/<app>/{img,support,privacy}
   cp apps/potato-tracker/index.html apps/<app>/index.html
   cp apps/potato-tracker/support/index.html apps/<app>/support/index.html
   cp apps/potato-tracker/privacy/index.html apps/<app>/privacy/index.html
   # only if users see each other's content:
   mkdir -p apps/<app>/community-guidelines && cp apps/potato-tracker/community-guidelines/index.html apps/<app>/community-guidelines/
   ```
   Then rewrite **everything** app-specific in each copy:
   - the `<title>`, description, canonical URL and Open Graph/Twitter URLs and image
   - the icon links
   - the kicker, all body text and the app links panel near the bottom (`aria-label="Potato Tracker links"`)
2. **Assets:** put the icon (`app-icon-512.png`, `app-icon-180.png`, `app-icon.svg`), the art and a 1200×630 `share-card.png` in `apps/<app>/img/`.
3. **Look:**
   - If the app has its own look, add a `.app-<name> { … }` token block to `assets/css/site.css`, next to `.app-potato`, and put `class="app-<name>"` on `<body>` in all of the app's pages.
   - Load any new font with `@font-face` from `assets/fonts/`, including its licence file.
   - Light colours only.
   - Otherwise, leave the class off and the pages use the company brand.
4. **Product page:** hero (icon, name, tagline, a "Coming soon to the App Store" button with no `href` until launch), how it works, features, and quick facts (platforms, price model, ads). Use the app's own approved copy if it has a kit.
5. **Support page:** an FAQ covering the questions people will actually ask (at least: does it need an account, where is my data, how do I delete it), plus **support@happyrose.io** as plain text and a mailto button.
6. **Privacy policy:** start from Potato Tracker's and change every fact:
   - What stays on the phone, and what's collected today
   - Buying the app, and what planned features would collect (with a stacked table; every `<td>` needs a `data-label`)
   - Retention, deletion (in the app, and by email), children, rights, security, changes, contact
   - The effective date
7. **Company pages:**
   - `index.html`: add an `.app-card` to the `#apps` grid. Keep the dashed "More on the way" card last, or remove it once you have several apps.
   - `support/index.html`: add a `.support-app` block.
   - `privacy/index.html`: add the app to the list of app policies.
   - `terms/index.html`: usually no change. Update it if the app adds something new (subscriptions, in-app purchases, user-generated content of a new kind).
   - `sitemap.xml`: add every new URL.
8. **Legal checklist:** add a section for the app to `~/Documents/happyrose-legal-checklist.md` (outside the repo) with the App Store privacy label answers, the Google Play Data safety answers, and anything a lawyer should check.
9. **Kit sync:** if the app has a website kit, add a sync section for it to CLAUDE.md, the same way as Potato Tracker's. In the app's own repo, set up `privacy-facts.md` and a changelog (see "Keeping the app repos in step" below).
10. **Check and ship:**
    - run the link check
    - look at every new page at phone and desktop widths, with the computer in dark mode
    - update docs/PLAN.md
    - the owner commits and pushes
    - then paste the three URLs into App Store Connect and Google Play

**Launch day** (for any app): swap the "Coming soon" button for Apple's official "Download on the App Store" badge linking to the listing, following Apple's badge guidelines. Update "coming soon" wording on the product page, the home page card and the support page.

### Keeping the app repos in step

Each app repo should keep its website kit current on its own:
- **Legal facts:** when a change affects what the app collects, stores, sends, shares or deletes, the app session updates `marketing/website-kit/privacy-facts.md`. That includes SDKs, permissions, sign-in, push, backend fields, retention, account deletion, age gates, price, ads and moderation.
- **Changelog:** it adds a `CHANGELOG.md` entry naming the affected site pages.
- **Page facts:** when a user-visible fact the site quotes changes (counts, platforms, price), it updates `copy.md` and logs it.
- **Never the other way round:** the app repo never edits this site, and this site never edits the app repo.

---

## Domain, DNS and HTTPS

The current setup, and what we learned setting it up:

**Cloudflare DNS records** (all **DNS only**, grey cloud, never proxied/orange):

| Type | Name | Value |
|---|---|---|
| A | `@` | 185.199.108.153, 185.199.109.153, 185.199.110.153, 185.199.111.153 (four records) |
| AAAA | `@` | 2606:50c0:8000::153, 2606:50c0:8001::153, 2606:50c0:8002::153, 2606:50c0:8003::153 (four records) |
| CNAME | `www` | inakirosa.github.io |
| TXT | `_github-pages-challenge-inakirosa` | GitHub's domain-verification code |

- **DNS only is required.** GitHub has to see its own servers to issue and renew the HTTPS certificate. The orange proxy breaks that.
- **Bulk import:** Cloudflare can import records from a BIND zone file (DNS → Records → Import and Export), but its file picker only accepts **`.txt`** files. Add `; cf_tags=cf-proxied:false` to each line, and leave "Proxy imported DNS records" unchecked.
- **Domain verification** is done (github.com → Settings → Pages → Verified domains). It stops anyone else's GitHub Pages site from claiming happyrose.io. Keep the TXT record.
- **HTTPS:** the certificate covers happyrose.io and www.happyrose.io, and GitHub renews it automatically. Enforce HTTPS is on, so http:// and www redirect to https://happyrose.io.
  - **If a certificate never appears** after DNS is correct (GitHub normally takes 15–60 minutes), remove the custom domain and re-add it. That makes GitHub request one straight away:
    ```
    gh api -X PUT repos/inakirosa/happyrose-site/pages -F cname=null
    gh api -X PUT repos/inakirosa/happyrose-site/pages -f cname=happyrose.io
    gh api repos/inakirosa/happyrose-site/pages --jq .https_certificate
    ```
    This makes GitHub commit to `CNAME` itself, so pull afterwards.
  - Right after a certificate is issued, some HTTPS requests time out for a few minutes while it reaches all four GitHub servers. That's normal.
- **Testing DNS changes:** your Mac caches "no such domain" answers, so query a public resolver instead of trusting your browser:
  ```
  dig +short A happyrose.io @1.1.1.1
  curl -sI --resolve happyrose.io:443:185.199.108.153 https://happyrose.io/
  ```

**The domain's registrant contact** in Cloudflare should list **Happy Rose LLC** as the organization, to show Apple the domain belongs to the company.

---

## Gotchas

- **GitHub commits to `main` by itself** when the custom domain is changed in Pages settings (it rewrites `CNAME`, sometimes without a trailing newline). If a push is rejected, `git pull --rebase` and push again. Look at what came in first.
- **Don't delete `CNAME`**, or the site falls back to `inakirosa.github.io`.
- **Everything in the repo is public,** including `docs/`, CLAUDE.md and this file. Never commit secrets, personal contact details or lawyer notes.
- **`python3 -m http.server` doesn't do 404s** (see [Running it locally](#running-it-locally)).
- **Headless Chrome won't lay out below about 500px** (see [Screenshots and PNGs without extra tools](#screenshots-and-pngs-without-extra-tools)).
- **Grid children with scrolling content** need `min-width: 0`, or they push the page wider than the phone.
- **Company logo:** keep it a simple smiling rose. Earlier versions with a curl on top ("looks like hair"), petal lines on the face, or a plain circle ("looks like a lollipop") were rejected. The current one has rounded outer petals, a bud with a swirl, and the face on the front of the cup.
- **iCloud:** the site lives in `~/Documents`, which iCloud syncs. That's fine for a site with no build step. Only code-signed builds (like the iOS app) have problems there.
