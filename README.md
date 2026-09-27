# Rena Farm — Public Website

Static HTML/CSS/JS website for [Rena Farm](https://renafarm.co.ke), Kajiado Central, Kenya.

## Stack

| Layer | Technology |
|---|---|
| Frontend | Static HTML, CSS, Vanilla JS |
| Backend / Database | Supabase (PostgreSQL + Auth + Storage) |
| Deployment | Vimexx FTP via GitHub Actions (auto-deploy from `deploy` branch) |
| Media | Supabase Storage (`website-media` bucket) |

## Project Structure

```
/
├── css/styles.css          # Global stylesheet
├── js/
│   ├── layout.js           # Shared navbar + footer injected on every page
│   └── supabase-client.js  # All Supabase query helpers
├── img/                    # Locally committed images (tracked by git)
│   ├── hay/
│   ├── pellets/
│   ├── silage/
│   └── doper-rams/
├── .github/workflows/      # CI checks + deploy automation
│   ├── ci.yml              # Runs on every push + PR (image/link/security checks)
│   ├── deploy-gate.yml     # Required gate for PRs targeting `deploy`
│   └── deploy-ftp.yml      # FTP upload to Vimexx on push to `deploy`
├── *.html                  # One file per page
└── Media/                  # Raw/original media (NOT tracked by git)
```

## Pages

| File | Route | Description |
|---|---|---|
| index.html | / | Homepage |
| about.html | /about | About & team |
| products.html | /products | Product listings (dynamic) |
| livestock.html | /livestock | Livestock breeds |
| fodder.html | /fodder | Fodder & feeds |
| gallery.html | /gallery | Photo gallery (dynamic) |
| events.html | /events | Events & EXPOs (dynamic) |
| how-to-buy.html | /how-to-buy | Purchasing process |
| location.html | /location | Farm location & directions |
| contact.html | /contact | Contact form + enquiry |
| enquire.html | /enquire | Product-specific enquiry form |
| portal.html | /portal | Client portal (requires login) |
| login.html | /login | Client login |
| register.html | /register | Client registration |

## Environment Variables

The Supabase anon key is a **public** key — safe to expose in frontend code.
See `.env.example` for the variables used.

For GitHub Actions deployment, the following secrets must be set under
Settings → Secrets and variables → Actions:

| Secret | Purpose |
|---|---|
| `FTP_HOST` | Vimexx FTP hostname |
| `FTP_USERNAME` | Vimexx FTP username |
| `FTP_PASSWORD` | Vimexx FTP password |

## Development

No build step required. Open any `.html` file in a browser, or use a local server:

```bash
npx serve .
```

## Deployment

Merging a PR into `deploy` triggers GitHub Actions (`deploy-ftp.yml`) which uploads
all site files to Vimexx via FTP into `public_html/`. The live site is at
[renafarm.co.ke](https://renafarm.co.ke).

**Never push directly to `deploy` or `test`.** Always use a feature/fix branch and
open a PR targeting `test` first. Once staging looks good, open a second PR from
`test` → `deploy`. The `deploy` branch requires owner approval before merging.

See `DEPLOYMENTS.md` for the full step-by-step workflow and non-negotiable rules.

## Branch Strategy

| Branch | Purpose | Who can push directly |
|---|---|---|
| `deploy` | Production — triggers FTP deploy to renafarm.co.ke | Nobody — PRs only |
| `test` | Staging — all features land here first | Nobody — PRs only |
| `feature/*` | New features | Assigned developer |
| `fix/*` | Bug fixes | Assigned developer |
| `hotfix/*` | Critical production fixes only | Senior developer only |

## CI

GitHub Actions runs checks on every push and PR:

- **`ci.yml`** — runs on all branches: syntax-checks JS, validates image references, checks
  internal links, guards against committed `.env` files.
- **`deploy-gate.yml`** — required status check for PRs targeting `deploy`; owner approval
  also required before merge.
- **`deploy-ftp.yml`** — triggered on push to `deploy`; uploads site to Vimexx via FTP.
