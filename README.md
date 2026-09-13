# manhwas-runner

Scheduled automation runner for manhwas.site.

**This repo contains no application source code** — only the CI workflow
that runs the catalog refresh + Cloudflare deploy. All source lives in a
private repository (`manhwas-digital-comics`), checked out at runtime via
an encrypted secret.

Why public? Public repos get unlimited free GitHub Actions minutes; the
private source repo is capped at 2,000 min/month. See the workflow header
for the full story.

- Schedule: every 6 hours (cron `23 */6 * * *`)
- Runs: scrape MangaDex + MangaKatana → commit snapshot → publish to
  Cloudflare R2 → build + deploy to Cloudflare Pages
- Self-heals: re-dispatches once on transient failure
