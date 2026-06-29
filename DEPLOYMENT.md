# Deploying K Square Architects to GitHub Pages

This site is a static, single-page website (`index.html` + `assets/`). It deploys to
**GitHub Pages** automatically via GitHub Actions, and is served at the custom domain
**`k-squarearchitects.com`**.

Everything in the repo is already prepared:

- `.github/workflows/deploy.yml` — the Actions workflow that builds and deploys on every push to `main`.
- `CNAME` — tells GitHub Pages to serve the site at `k-squarearchitects.com`.
- `.gitignore` — keeps local-only files (the screen recording, `Logo/`, `refrances/`, `Main_Tabs.txt`) out of the published repo.

---

## Step 1 — Create the GitHub repository

1. Go to <https://github.com/new>.
2. Name it (e.g. `ksquare-architects`). Public is recommended (Pages on private repos requires a paid plan).
3. **Do not** add a README, `.gitignore`, or license — this repo already has commits.
4. Click **Create repository**.

## Step 2 — Push your local repo

The local repository is already initialized and committed on the `main` branch. In this
project folder, run:

```bash
git remote add origin https://github.com/<your-username>/<your-repo>.git
git push -u origin main
```

> Replace `<your-username>` and `<your-repo>` with your actual values.

If you prefer the GitHub CLI and it's authenticated (`gh auth login`):

```bash
gh repo create <your-repo> --public --source=. --remote=origin --push
```

## Step 3 — Enable GitHub Pages (GitHub Actions source)

1. In the repo on GitHub, open **Settings → Pages**.
2. Under **Build and deployment → Source**, choose **GitHub Actions**.

   (The included workflow uses the official Pages actions, so no branch selection is needed.)
3. The workflow runs automatically on the push from Step 2. Watch it in the **Actions** tab.
   When it finishes green, the site is live at `https://<your-username>.github.io/<your-repo>/`.

## Step 4 — Point your domain's DNS at GitHub

You own **`k-squarearchitects.com`**. Log in to your domain registrar's DNS settings and add
the records below. This sets up the **apex** domain plus the **www** alias.

### Apex domain (`k-squarearchitects.com`) — A and AAAA records

Create four **A** records (host `@`) pointing to GitHub's Pages IPs:

| Type | Host | Value           |
|------|------|-----------------|
| A    | @    | 185.199.108.153 |
| A    | @    | 185.199.109.153 |
| A    | @    | 185.199.110.153 |
| A    | @    | 185.199.111.153 |

(Recommended) also add four **AAAA** records for IPv6:

| Type | Host | Value                  |
|------|------|------------------------|
| AAAA | @    | 2606:50c0:8000::153    |
| AAAA | @    | 2606:50c0:8001::153    |
| AAAA | @    | 2606:50c0:8002::153    |
| AAAA | @    | 2606:50c0:8003::153    |

### www alias (`www.k-squarearchitects.com`) — CNAME record

| Type  | Host | Value                      |
|-------|------|----------------------------|
| CNAME | www  | `<your-username>.github.io` |

> Note the trailing `.github.io` — point to your user/org GitHub Pages host, **not** the repo URL.

DNS changes can take anywhere from a few minutes to 24–48 hours to propagate.

## Step 5 — Set the custom domain in GitHub Pages

1. Back in **Settings → Pages → Custom domain**, confirm it shows `k-squarearchitects.com`
   (the `CNAME` file in the repo sets this automatically; if it's blank, type it and click **Save**).
2. GitHub runs a DNS check. Once it passes (✓), tick **Enforce HTTPS**.
   - The certificate can take up to ~24 hours to issue after DNS resolves. If "Enforce HTTPS"
     is greyed out, wait and check back.

## Step 6 — Verify

- Visit `https://k-squarearchitects.com` — the site should load over HTTPS.
- Visit `https://www.k-squarearchitects.com` — it should redirect to the apex domain.
- Hard-refresh (Ctrl/Cmd + Shift + R) if you see a stale page.

---

## Updating the site later

Any change you push to `main` redeploys automatically:

```bash
git add -A
git commit -m "Update site content"
git push
```

Watch the **Actions** tab for the deploy to finish, then refresh the live site.

---

## Troubleshooting

| Symptom | Likely cause / fix |
|---|---|
| 404 at the github.io URL | Pages source not set to **GitHub Actions** (Step 3), or the workflow hasn't run yet. |
| Custom domain "DNS check failed" | DNS records not added or still propagating. Re-check Step 4; wait and retry. |
| "Enforce HTTPS" greyed out | Certificate still issuing. Wait up to 24h after DNS resolves. |
| Images/styles broken | Asset paths are relative (`assets/...`), which is correct — verify the `assets/` folder was pushed (`git ls-files`). |
| `CNAME` keeps getting removed | Don't delete the `CNAME` file from the repo; the workflow publishes the repo root including it. |
| Domain shows old/parked page | Remove any conflicting registrar URL-forwarding or default A records before adding GitHub's. |

## What is NOT published

These local files are intentionally excluded via `.gitignore` and never reach GitHub:

- `Recording 2026-06-29 000252.mp4` (and any `*.mp4`)
- `refrances/`
- `Logo/`
- `Main_Tabs.txt`

If you ever need one of these in the repo, edit `.gitignore` to un-exclude it.
