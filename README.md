# streetcat-web

Static site for **streetcatcreative.org** — the StreetCat Creative LLC landing page, plus per-app pages and privacy policies hosted at `/<app-slug>/`.

Vanilla HTML/CSS, no framework, no build step. Edit files, push, GitHub Pages serves them.

## Layout

```
/                       index.html       — LLC landing
/tikitapa/              index.html       — Tiki-Tapa app page
/tikitapa/privacy.html  — Tiki-Tapa privacy policy
/app-ads.txt            — AdMob authorization (REPLACE before launch — see below)
/styles.css             — shared CSS
/404.html               — GitHub Pages fallback for unmatched URLs
/CNAME                  — custom-domain marker for GitHub Pages
```

## One-time deploy

### 1. Push to GitHub

```bash
cd ~/Claude_Projects/streetcat-web
git init -b main
git add .
git commit -m "Initial site"
gh repo create streetcat-web --public --source=. --push
```

(Or create the repo via github.com UI and `git remote add origin ...`)

### 2. Enable GitHub Pages

Repo → **Settings** → **Pages** → Source: `Deploy from a branch` → Branch: `main` / root → Save.

GitHub shows a status banner; once it goes green you're live at `https://joebuckley06.github.io/streetcat-web/`.

### 3. Point streetcatcreative.org at it

Already done in code: the `CNAME` file in this repo tells Pages "serve this site at `streetcatcreative.org`."

Now configure DNS at **Hostinger**:

1. Sign in to **hPanel** → **Domains** → click `streetcatcreative.org` → **DNS / Nameservers** (or **Manage DNS records**).
2. **Delete** any existing `A`, `AAAA`, or `CNAME` records pointing at `@` or `www`.
3. **Add four `A` records** pointing the apex (`@`) at GitHub Pages:
   | Type | Host | Value           | TTL  |
   |------|------|-----------------|------|
   | A    | @    | 185.199.108.153 | 14400 |
   | A    | @    | 185.199.109.153 | 14400 |
   | A    | @    | 185.199.110.153 | 14400 |
   | A    | @    | 185.199.111.153 | 14400 |
4. **Add one `CNAME` record** for `www`:
   | Type  | Host | Value                       | TTL   |
   |-------|------|-----------------------------|-------|
   | CNAME | www  | joebuckley06.github.io.     | 14400 |

   (Trailing dot included.)
5. Save. DNS propagation usually takes 5–60 minutes; can be up to 24h.

### 4. Verify in GitHub

Back in repo **Settings** → **Pages** → under **Custom domain**, you should now see `streetcatcreative.org` (read from CNAME). Once DNS resolves, GitHub:

- Confirms ownership (green check)
- Auto-provisions an HTTPS certificate (Let's Encrypt; ~30 min)
- Then the **Enforce HTTPS** checkbox becomes available — tick it.

### 5. Smoke test

- `https://streetcatcreative.org/` → landing page
- `https://streetcatcreative.org/tikitapa/` → Tiki-Tapa page
- `https://streetcatcreative.org/tikitapa/privacy.html` → privacy policy
- `https://streetcatcreative.org/app-ads.txt` → the AdMob file (still a placeholder until step 6)

### 6. Paste your AdMob line into `app-ads.txt`

Open the AdMob console → **Apps** → **Tiki-Tapa** → **App settings** → scroll to **app-ads.txt**. Copy the single line they show (it looks like `google.com, pub-3883850593023808, DIRECT, f08c47fec0942fa0`).

Replace this file's contents with that line, commit, push. AdMob re-crawls within ~24h and the dashboard will flip to "Authorized."

### 7. App Store Connect

In App Store Connect → your app → **App Information**:
- **Marketing URL** → `https://streetcatcreative.org/tikitapa/`
- **Support URL** → `https://streetcatcreative.org/tikitapa/` (or a dedicated support page later)
- **Privacy Policy URL** → `https://streetcatcreative.org/tikitapa/privacy.html`

## Updating

Just edit files and `git push`. GitHub Pages republishes in ~30 seconds.

When the **Privacy Policy** changes, also update `PRIVACY_POLICY.md` in the `tikitapa` (game) repo so the policy text stays in sync between the source-of-truth Markdown and the published HTML.

## Future: subdomains per app

If you later want `tikitapa.streetcatcreative.org` instead of `streetcatcreative.org/tikitapa/`:

1. Create a second repo (e.g., `tikitapa-web`) with its own `CNAME` containing `tikitapa.streetcatcreative.org`.
2. At Hostinger, add a `CNAME` record: `tikitapa → joebuckley06.github.io.`
3. Move `/tikitapa/` content from this repo into the new one.
4. Add a redirect on the path here so old links still work.

For one app, the path-based layout is simpler. Revisit when you have a second app to publish.
