# csc381-site — interactive modules for markhomepage.com

A static site: no server, no build step. Everything under `public/` is what gets
deployed. Cloudflare Pages serves it as-is.

```
public/
  index.html                 the four-module landing page
  assets/site.css            shared chrome (WCU purple/gold) + validated chart palette
  assets/plotly.min.js       plotly.js 2.35.2, vendored so the classroom does not depend on a CDN
  visualization/index.html   Module 1 — the Titanic dashboard
  visualization/data.js      891 rows, generated from Lectures/Week03/wk_3_code/data/titanic_raw.csv
build_data.py                regenerates visualization/data.js (run with Miniconda's python)
```

## Preview locally

```bash
cd projects/csc381-site/public && python -m http.server 8080
```
then open http://localhost:8080/ . (Opening `index.html` by double-click also works.)

## Deploy to Cloudflare Pages (first time, ~5 minutes)

Option A — drag and drop, no tooling:
1. Cloudflare dashboard → **Workers & Pages → Create → Pages → Upload assets**.
2. Project name `csc381` (this gives you `csc381.pages.dev`).
3. Drag the **`public`** folder in. Done; the `.pages.dev` URL is live immediately.

Option B — command line (repeatable, what you want once it becomes a habit):
```bash
npx wrangler login
npx wrangler pages project create csc381 --production-branch main
npx wrangler pages deploy public --project-name csc381
```
Every later update is just the last line again.

## Put it on markhomepage.com

`markhomepage.com` currently has no DNS record (the name does not resolve), so
either of these works:

- **Root domain**: in the Pages project → **Custom domains → Set up a custom domain**
  → `markhomepage.com`. Cloudflare adds the DNS record itself because the zone is
  already on Cloudflare. Also add `www.markhomepage.com` if you want that to work.
- **Subdomain** (if you later want a personal homepage at the root):
  `csc381.markhomepage.com` the same way. The site uses relative links only, so it
  works at either address unchanged.

Module URLs are then `markhomepage.com/` (index) and `markhomepage.com/visualization/`.
Filters are encoded in the URL hash, so a link like
`markhomepage.com/visualization/#cls=3&sex=male` opens the dashboard already filtered —
useful for a slide.

## Adding Module 2, 3, 4

Copy `visualization/` to a new folder, swap `data.js` and the chart code, and turn
the matching card in `index.html` from `module soon` into `module live` with an `href`.
Keep the colour tokens in `site.css`; the categorical slots were validated with the
dataviz palette checker (light and dark) and should not be re-picked by eye.
