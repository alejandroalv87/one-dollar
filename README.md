# Portfolio Dashboard (no login)

A private, TIKR-style portfolio dashboard — **no login, no email, no secrets to
manage beyond one data key.** Import your holdings from a Google Sheets export,
see live market value and gain/loss, and click any holding to drill into live
financials, the next earnings date, institutional ownership, and insider
transactions.

Built with Next.js (App Router). The market-data API key lives **server-side
only** and is never exposed to the browser.

## How "private" works without a login

- Your **holdings are stored only in your own browser** (localStorage). They are
  never uploaded anywhere, so anyone who opens the URL just sees an empty
  dashboard — not your positions. Your holdings follow the browser/device you
  imported them on.
- The only shared surface is the stock-lookup, which uses **your** market-data
  key server-side. If you'd rather not let anyone hit it, keep the URL private,
  or ask me to add back a light gate.

## Setup

1. **Get a market-data key** from Financial Modeling Prep (FMP):
   https://site.financialmodelingprep.com/developer/docs
   (Free tier covers quotes/profiles; financial statements, 13F ownership and
   insider endpoints generally need a paid plan — the app shows "not available"
   where your plan doesn't cover an endpoint.)

2. **Configure:**
   ```bash
   cp .env.example .env.local
   ```
   Set `FMP_API_KEY` (and optionally `DATA_CACHE_TTL`). That's all.

3. **Run locally:**
   ```bash
   npm install
   npm run dev          # http://localhost:3000
   ```
   Open it, click **Import holdings**, paste your sheet (File → Download → CSV).

## Deploy

### Netlify / Vercel
Push this folder to a Git repo, import it, and add **one** environment variable
(`FMP_API_KEY`) under the project's environment settings. Deploy. (On Netlify the
Next.js plugin is auto-detected; remember that env-var changes only take effect
after a redeploy.)

### Any Node host / Docker
```bash
npm install && npm run build && npm run start   # serves on $PORT (default 3000)
```
A `Dockerfile` is included.

## Importing from Google Sheets

**File → Download → CSV**, then paste or upload it in the **Import holdings**
panel. A `Symbol`/`Ticker` column is required; `Shares` and `Cost basis`
(per-share average) are optional but needed for gain/loss math. Example in
`holdings.sample.csv`.

## Project layout

```
app/
  page.js                    Portfolio dashboard + holdings import
  stock/[symbol]/page.js     Stock detail (overview/financials/ownership/insiders)
  api/data/*                 quote / profile / financials / earnings / ownership / insiders
lib/
  fmp.js                     Financial Modeling Prep client + cache
  holdings.js                CSV parsing + browser persistence
  format.js                  Number/date formatting
components/AppShell.js       Sidebar + ticker search
```

## Want the login back later?

The email-2FA version (with allowlist, one-time codes, and route protection) is a
drop-in superset of this one. Ask and I'll hand it back.
