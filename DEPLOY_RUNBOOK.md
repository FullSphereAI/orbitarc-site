# orbitarc.co Deploy Runbook — GitHub Pages
_July 10, 2026. DNS values verified against GitHub's official docs (docs.github.com, Pages custom domain guide) on July 10, 2026. Nothing here runs until Adam says approved._

## What ships

From `~/Claude/Artifacts/orbitarc-site/`: index.html, faq.html, fit.html, 404.html, styles.css, logo.jpg, favicon-32.png, apple-touch-icon.png, icon-512.png, og-image.jpg, CNAME, robots.txt, sitemap.xml. (This runbook file is harmless if uploaded.)

**Do NOT upload the `.claude` folder** (Claude Code settings, added July 10). If deploying via git, add a `.gitignore` containing `.claude/` first.

## Step 0 — Two things before deploy

**Intake mailbox: DONE.** `uplink@orbitarc.co` created in the M365 tenant (Adam confirmed July 10, 2026). Optional pre-launch sanity check: send it one test email and confirm it lands.

**Set the buy button.** In THREE files — `index.html`, `faq.html`, AND `fit.html` — one line near the top of each:

```
const BUY_URL = "REPLACE_AT_DEPLOY";
```

Replace with the destination (whatever satisfies the no-gumroad-visible rule at your call; the URL only lives behind the button, the text never shows it). Until replaced, buttons scroll to the pricing section instead of leaving the page — safe default.

## Step 1 — Create the repo (github.com, logged in as FullSphereAI)

1. New repository. Name it exactly: `fullsphereai.github.io` (user site) OR any name like `orbitarc-site` (project site). Either works with the custom domain. Recommended: `orbitarc-site`, keeps the account tidy.
2. Public. No README needed.

## Step 2 — Upload the site

Path A, no terminal: repo page → "uploading an existing file" link → drag every file from `~/Claude/Artifacts/orbitarc-site/` → Commit.

Path B, terminal:
```
cd ~/Claude/Artifacts/orbitarc-site
git init && git add -A && git commit -m "orbitarc.co v1"
git branch -M main
git remote add origin https://github.com/FullSphereAI/orbitarc-site.git
git push -u origin main
```

## Step 3 — Turn on Pages

Repo → Settings → Pages → Source: "Deploy from a branch" → Branch: `main`, folder `/ (root)` → Save. Site goes live at `fullsphereai.github.io/orbitarc-site/` within a minute or two.

## Step 4 — Custom domain

1. Still in Settings → Pages → Custom domain: enter `orbitarc.co` → Save. (The CNAME file in the repo already matches.)
2. Recommended first: verify the domain (GitHub → account Settings → Pages → Add verified domain) to block takeover attacks. GitHub gives a TXT record to add in GoDaddy.

## Step 5 — GoDaddy DNS (orbitarc.co)

DNS → Manage zones → orbitarc.co. Add:

| Type | Name | Value |
|---|---|---|
| A | @ | 185.199.108.153 |
| A | @ | 185.199.109.153 |
| A | @ | 185.199.110.153 |
| A | @ | 185.199.111.153 |
| CNAME | www | fullsphereai.github.io |

Remove any existing conflicting A record on @ (GoDaddy often parks one). Do NOT touch MX, TXT (SPF/DKIM/DMARC), or DANE records — email stays untouched, these are separate record types.

## Step 6 — HTTPS

Back in repo Settings → Pages: wait for the DNS check to pass (minutes to an hour, up to 24h worst case), then check **Enforce HTTPS**. GitHub auto-issues the Let's Encrypt cert. No purchase, no renewal, ever. If GoDaddy upsells an SSL cert anywhere in this flow, ignore it.

## Step 7 — Post-deploy checks

1. `https://orbitarc.co` loads with the padlock.
2. `https://www.orbitarc.co` redirects to the apex.
3. `dig orbitarc.co +noall +answer -t A` returns the four 185.199.x.153 addresses.
4. A wrong URL (orbitarc.co/nope) shows the branded 404.
5. Paste the URL into a LinkedIn/X draft — confirm the og-image card renders.
6. Buy button goes where it should, on all three pages.
7. Fit check (orbitarc.co/fit.html): complete the quiz once, confirm the boot demo plays and the CTA routes correctly.

## Rollback

Settings → Pages → Remove custom domain, or delete the repo. DNS records can sit harmlessly while the site is down, but per GitHub's guidance remove them if the site stays down long-term (takeover risk).
