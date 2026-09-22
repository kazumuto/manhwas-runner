# manhwas-runner

Scheduled automation runner for manhwas.site.

**This repo contains no application source code** — only the CI workflow
that runs the catalog refresh + Cloudflare deploy. All source lives in a
private repository (`manhwas-digital-comics`), checked out at runtime via
an encrypted secret.

Why public? Public repos get unlimited free GitHub Actions minutes; the
private source repo is capped at 2,000 min/month. See the workflow header
for the full story.

- Schedule: every 2 hours (cron `23 */2 * * *`)
- Runs: scrape MangaDex + MangaKatana → commit snapshot → publish to
  Cloudflare R2 → build + deploy to Cloudflare Pages
- Self-heals: re-dispatches once on transient failure

Additional workflows:

- `deploy-only.yml` — ships current main to Cloudflare Pages without
  scraping (2-hourly offset + on demand for urgent fixes)
- `health-check.yml` — daily live suite + R2/CDN freshness tripwire
- `publish-mirror.yml` — **disaster-recovery mirror**: builds the site and
  force-pushes the static output to `kazumuto/manhwas` `gh-pages`, served at
  https://mirror.manhwas.site (GitHub Pages; DNS-only CNAME in the
  manhwas.site Cloudflare zone). Every 3h at :38 (8 builds/day, under the
  Pages 10/day cap) + `workflow_dispatch` for immediate republish during an
  incident. Covers the full SEO surface (all prerendered pages, catalogs,
  sitemaps, robots, llms.txt); chapter reading (`/read/*`) and `/api/*`
  stay Cloudflare-only. Why: a copyright complaint can suspend the
  Cloudflare zone/Pages project — the mirror keeps the site reachable on a
  different provider with its own DMCA process. Runbook:
  migration_context.md §19.3 in the private repo.
