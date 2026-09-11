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

## Where it lives

| | |
|---|---|
| Production | https://markhomepage.com and https://www.markhomepage.com |
| Fallback | https://csc381.pumpkin-ai-v2.workers.dev |
| Cloudflare Worker | `csc381` (assets only, no script) |
| Git | `Kyyota-Wang/cs381`, branch `main` |
| First deploy | Sep 10, 2026, version `5740ecc0-40ac-4da3-be07-66daa61244bf` |

Same pattern as INDHive's `DEPLOY.md`, minus the API: `wrangler.jsonc` points
`assets.directory` at `public/`, both hostnames are `custom_domain: true` (wrangler
creates the DNS records and certificate itself), and `workers_dev: true` keeps the
fallback address alive.

## Deploy

wrangler is already authorised on this machine by OAuth (check with `npx wrangler whoami`;
re-run `npx wrangler login` if it has lapsed). Then:

```bash
cd projects/csc381-site
npm install                 # first time only
npx wrangler deploy
```

The output must list all three addresses. If workers.dev is missing, `workers_dev: true`
has been dropped from `wrangler.jsonc`. Note the `Current Version ID` line; that is the
rollback point (`npx wrangler rollback <id>`).

Verify:

```bash
for u in https://markhomepage.com/ https://markhomepage.com/visualization/ https://markhomepage.com/nothing; do curl -s -o /dev/null -w "$u %{http_code}
" "$u"; done
```

Expect 200, 200, 404.

## Adding Module 2, 3, 4

Copy `visualization/` to a new folder, swap `data.js` and the chart code, and turn
the matching card in `index.html` from `module soon` into `module live` with an `href`.
Keep the colour tokens in `site.css`; the categorical slots were validated with the
dataviz palette checker (light and dark) and should not be re-picked by eye.
