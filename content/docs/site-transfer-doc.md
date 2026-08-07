---
title: "Site Transfer Doc"
updated: 2026-08-04
summary: "Full architecture, repo structure, and setup status — where things stand and what is left to do."
---

Reference doc covering everything set up so far: what exists, how it works, and what's still in progress. Written 2026-08-04.

---

## 1. What this is

A Hugo-based static site tracking your homelab/hardware projects — status, scope, deliverables, and a running log per project. Source of truth is Markdown files in a GitHub repo; the live site rebuilds automatically on every push.

- **Repo**: `https://github.com/Lilneenja/bench-log`
- **Live site**: `https://coilltean.net`
- **Local folder** (on your Windows machine): wherever you unzipped/cloned `bench-log`

---

## 2. How it's built (architecture)

- **Static site generator**: Hugo — reads Markdown files with YAML frontmatter, generates HTML
- **Content**: one `.md` file per project in `content/projects/`
- **Theme**: custom, not a downloaded theme — layouts live in `layouts/`, styling in `static/css/style.css`
- **Hosting**: GitHub Pages
- **Deploy**: GitHub Actions — every `git push` to `main` triggers `.github/workflows/hugo.yml`, which builds the site with Hugo and publishes it automatically. No manual build/upload step.
- **Domain**: `coilltean.net`, pointed at GitHub Pages via DNS A records, with a `static/CNAME` file in the repo telling GitHub which domain to serve

---

## 3. Repo structure

```
bench-log/
├── content/
│   ├── _index.md              ← homepage stub
│   └── projects/
│       ├── project-portfolio-website.md
│       ├── setup-vpn-to-home.md
│       ├── wifi-adapter-options.md
│       ├── finish-modding-the-wii.md
│       ├── fix-laptop.md
│       ├── service-database.md
│       └── arduino-wifi-connection.md
├── layouts/
│   ├── _default/baseof.html   ← base HTML wrapper
│   ├── index.html             ← homepage (project list/dashboard)
│   ├── partials/project-card.html
│   └── projects/single.html   ← individual project page
├── static/
│   ├── css/style.css
│   ├── CNAME                  ← custom domain config for GitHub Pages
│   └── admin/                 ← Decap CMS files (see section 6 — not finished)
│       ├── index.html
│       └── config.yml
├── archetypes/projects.md     ← template used by `hugo new`
├── .github/workflows/hugo.yml ← auto build + deploy
├── hugo.toml                  ← site config (baseURL, etc.)
└── README.md
```

---

## 4. Frontmatter fields (per project file)

Each project `.md` file starts with a block like this:

```yaml
---
title: "Setup VPN to Home"
date: 2026-07-20
status: "blocked"        # planned | active | blocked | done
priority: "medium"       # low | medium | high
started: 2026-07-20
target:
progress: 10              # 0-100, drives the progress bar
tags: [networking, homelab]
lastLog:
  date: "07/29"
  text: "Waiting on router firmware update before WireGuard port-forward will stick."
---
```

Below the `---` block is the Markdown body — Scope, Deliverables, Resources & Dependencies, Definition of Done, and a Log section with dated bullet points.

The `status` field drives the colored LED indicator and which group a project sorts into on the homepage. `lastLog` is just what shows as the one-line summary on the homepage card — update it whenever you add a new log entry so the dashboard reflects the latest state.

---

## 5. How to update the site today

**Option A — edit directly on GitHub.com (works from any device, including phone)**
1. Go to the file in the repo (e.g. `content/projects/setup-vpn-to-home.md`)
2. Click the pencil icon → edit → **Commit changes**
3. Site rebuilds automatically within about a minute

**Option B — edit locally and push via git**
```powershell
git add .
git commit -m "update project status"
git push
```

**Option C — Decap CMS admin panel** — set up but not finished yet, see below.

---

## 6. In progress: phone-friendly CMS (Decap CMS)

Goal: a form-based editor at `coilltean.net/admin` so you can update project status/notes from your phone without touching Markdown or git.

**Done:**
- `static/admin/index.html` and `static/admin/config.yml` added to the repo
- Config defines editable fields matching the frontmatter (status, priority, dates, progress, tags, log summary, body)

**Not done yet — remaining steps:**
1. Fork `github.com/SubhenduX/decap-cms-cloudflare-pages` to your GitHub account
2. Deploy that fork as a Cloudflare Pages project (free) — this hosts the small login-handshake server GitHub requires
3. Create a GitHub OAuth App (Settings → Developer settings → OAuth Apps), using the `.pages.dev` URL from step 2 as both the homepage and callback URL
4. Add the OAuth app's Client ID and Client Secret as environment variables (`GITHUB_CLIENT_ID`, `GITHUB_CLIENT_SECRET`) in the Cloudflare Pages project settings, then redeploy
5. Replace the placeholder `base_url` in `static/admin/config.yml` with the real `.pages.dev` URL, commit, push
6. Visit `coilltean.net/admin` — should show a "Login with GitHub" screen

This is the only piece of the setup that's not finished. Until it is, use Option A or B above.

---

## 7. Local tooling status

- **Hugo**: not yet installed on the Windows machine. Only needed for local preview (`hugo server`) or the `hugo new` shortcut for scaffolding new project files — not required to update the live site, since GitHub Actions does the building.
- To install: `winget install Hugo.Hugo.Extended` in PowerShell, then reopen PowerShell.
- **Git**: installed and working (was the earlier blocker — resolved via Git for Windows).

---

## 8. DNS records (for reference, at your domain registrar)

```
A     @     185.199.108.153
A     @     185.199.109.153
A     @     185.199.110.153
A     @     185.199.111.153
CNAME www   Lilneenja.github.io     (optional, only if www.coilltean.net should work)
```

Plus `static/CNAME` in the repo containing exactly `coilltean.net`.

GitHub Pages custom domain is also set in **Settings → Pages → Custom domain** on the repo, with "Enforce HTTPS" checked.

---

## 9. Known gotchas hit so far (for future reference)

- `theme = ""` in `hugo.toml` breaks the GitHub Actions build (Hugo tries to load a nonexistent module) — must be removed entirely, not left blank, since this site uses custom layouts, no theme.
- Node.js 20 deprecation warnings in Actions logs are harmless — GitHub auto-upgrades the runner.
- CRLF/LF line-ending warnings from git on Windows are cosmetic, not errors.
- Apex domains (`coilltean.net` with no subdomain) can't use a CNAME DNS record — hence the four A records instead.

---

## 10. Next steps (suggested order)

1. Finish the Decap CMS OAuth setup (section 6) if phone editing is still wanted
2. Optionally install Hugo locally for previewing before pushing
3. Fill in remaining "planned" projects (Service Database especially needs scope defined — currently a placeholder)
4. Longer term: once the VPN project is working, consider self-hosting the site on home hardware instead of GitHub Pages (not urgent — GitHub Pages is free and zero-maintenance)
