# sosial.app — official site + OAuth bridge

Standalone static site. No build step, no tracking, no cookies.
Lives in its OWN repo — never merge into the app repo (Pages would publicly
serve the app's source files as static text).

Pages: `index.html` (landing) · `privacy.html` (policy + data deletion, for
platform reviews) · `terms.html` (ToS, for stores + Meta) · `auth.html`
(OAuth bridge) · `404.html` · `logo.png` (Sosial emblem) · `CNAME`.

## 1. Create + push the repo (github.com, logged in as sosialapp — the new account)

New repository → name `sosial.app` → Public → no README/license (files are here).

```powershell
cd C:\Users\user\AppData\Local\Temp\opencode\sosial-site
git init
git add index.html privacy.html terms.html auth.html 404.html logo.png CNAME
git commit -m "Sosial official site + OAuth bridge"
git branch -M main
git remote add origin https://github.com/sosialapp/sosial.app.git
git push -u origin main
```

## 2. GitHub Pages (repo → Settings → Pages)

- Source: Deploy from branch → `main` → `/ (root)` → Save
- Custom domain: `sosial.app` → Save (validates against the CNAME file + DNS)
- Wait for the TLS certificate, then tick **Enforce HTTPS**
- Check: `https://sosial.app/` · `/privacy.html` · `/terms.html` · `/auth.html`

## 3. DNS (Namecheap → domain → Advanced DNS)

Keep the existing TXT (SPF) + any MX (PrivateEmail) records. Add:

| Type | Host | Value | TTL |
|---|---|---|---|
| A | @ | 185.199.108.153 | Automatic |
| A | @ | 185.199.109.153 | Automatic |
| A | @ | 185.199.110.153 | Automatic |
| A | @ | 185.199.111.153 | Automatic |
| CNAME | www | sosialapp.github.io. | Automatic |

(Trailing dot on the CNAME target matters in some panels.)
Propagation: minutes to ~24h. Verify: `nslookup sosial.app` shows the four
185.199.x.x addresses.

## 4. OAuth redirect migration (all 9 portals)

Add `https://sosial.app/auth.html` as an allowed redirect URI **alongside**
the existing `https://egateworldwide.github.io/Sosial/auth.html` — do NOT
delete the old one yet. Portals: Meta app dashboard (Facebook/IG/Threads
share the apps), TikTok sandbox app, X project, LinkedIn app (old + new),
Google Cloud credentials, Pinterest app, Mastodon is per-instance (n/a),
Bluesky is app-password (n/a).

Only after EVERY portal lists the new URI: flip `BRIDGE_URL` in
`src/utils/metaAuth.ts` to `https://sosial.app/auth.html`, bump BUILD_TAG,
reconnect one channel as a smoke test, then remove the old URI everywhere.

## 5. URLs to hand to reviewers

- Website: `https://sosial.app/`
- Privacy: `https://sosial.app/privacy.html`
- Terms: `https://sosial.app/terms.html`
- Data deletion: `https://sosial.app/privacy.html` (section 5)
- Support contact: `support@sosial.app` (SPF present; confirm the mailbox exists)

## Later: the real web app

When the Next.js app lands: deploy it to `app.sosial.app`, keep this static
site at the apex. Add one link here → the app. No changes needed to the
bridge or legal pages (same URLs reviewers already approved).
