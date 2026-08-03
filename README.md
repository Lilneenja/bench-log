# bench log

A Hugo site for tracking homelab/hardware projects: status, scope, deliverables,
and a running log per project. Deploys automatically to GitHub Pages.

## Preview locally

```
hugo server
```

Then open http://localhost:1313

## Add a new project

```
hugo new projects/name-of-project.md
```

This uses `archetypes/projects.md` to prefill the frontmatter (status, priority,
dates, tags) and section headers (Scope, Deliverables, Resources, Definition of
Done, Log). Fill it in, then just add new bullet points under `## Log` each time
you touch the project.

Frontmatter fields:

| Field      | Values                                  |
|------------|------------------------------------------|
| `status`   | `planned` / `active` / `blocked` / `done` |
| `priority` | `low` / `medium` / `high`                 |
| `started`  | date, or leave blank                      |
| `target`   | date, or leave blank                      |
| `progress` | 0–100, only used for active/blocked cards |
| `lastLog`  | `date` + `text` shown on the home page card |

## Deploy to GitHub Pages (one-time setup)

1. Create a new GitHub repo (e.g. `bench-log`) and push this folder to it.
2. In the repo: **Settings → Pages → Source → GitHub Actions**.
3. Edit `hugo.toml` — set `baseURL` to your actual Pages URL,
   e.g. `https://yourusername.github.io/bench-log/`.
4. Push to `main`. The workflow in `.github/workflows/hugo.yml` builds the
   site and deploys it automatically — check the **Actions** tab for progress.
5. Site will be live at the URL from step 3 a minute or two later.

Every time you `git push` after that (e.g. after adding a new project or log
entry), the site rebuilds and redeploys automatically — no manual steps.

## Later: move to self-hosting

When your home-server / VPN projects are further along, you can serve the
same `public/` output with Caddy or nginx on your own hardware instead —
nothing about the content or structure needs to change, just where the
built files are served from.
