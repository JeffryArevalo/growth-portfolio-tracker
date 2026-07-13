# Growth Portfolio Tracker

A public, real-time view of my personal stock portfolio. Anyone can see my holdings,
live prices, performance vs the S&P 500, and my full buy/sell history â€” but only I
can record trades.

**Live site:** https://growth-portfolio-tracker.vercel.app

## How it works

```
Visitors â”€â”€> Vercel (Next.js, free Hobby tier â€” always on)
              â”œâ”€ /              Portfolio: KPIs, performance & allocation charts, holdings
              â”œâ”€ /activity      Every buy & sell, newest first, filterable
              â”œâ”€ /stock/SYMBOL  Live quote, price chart, key stats, news, my trades
              â””â”€ /api/*         Server routes: API keys stay server-side, responses
                                CDN-cached so free-tier rate limits are never hit

Admin (me) â”€â”€> /admin â”€â”€(GitHub token in my browser only)â”€â”€> commits to
               data/transactions.json in this repo â”€â”€> site updates in ~1 minute
```

- **Source of truth** is [`data/transactions.json`](data/transactions.json) â€” an
  append-only trade log committed to this repo. Holdings, average cost, and realized
  P/L are all derived from it. Git history doubles as an audit trail.
- **View-only by construction**: the public site has no write path. Writing requires a
  fine-grained GitHub personal access token that exists only in the owner's browser.
- **Live market data**: quotes from [Finnhub](https://finnhub.io) (cached 30s),
  daily price history from Yahoo Finance's public chart API (keyless, cached 6h),
  proxied through API routes so keys are never exposed to the client.
- **Performance chart** replays the trade log: actual portfolio value vs putting
  the same dollars into VOO on the same dates (dividends excluded on both sides).

## Tech stack

- Next.js 16 (App Router) + React 19 + TypeScript
- Recharts for charts
- Vercel Hobby for hosting (free, no sleeping)
- GitHub Contents API as the "database"

## Local development

```bash
git clone https://github.com/JeffryArevalo/growth-portfolio-tracker.git
cd growth-portfolio-tracker
npm install
cp .env.example .env.local   # then fill in your keys
npm run dev
```

`.env.local` (server-side only â€” do **not** prefix with NEXT_PUBLIC_):

```
FINNHUB_API_KEY=your_finnhub_key
```

Open http://localhost:3000

## Deployment (Vercel)

1. Import the repo in Vercel (already connected â€” every push to `main` deploys).
2. In **Project â†’ Settings â†’ Environment Variables**, add `FINNHUB_API_KEY`.
3. Redeploy. That's it â€” no other infrastructure.

## Recording trades (owner only)

1. Visit `/admin` on the live site.
2. One-time setup: create a **fine-grained PAT** at
   github.com/settings/personal-access-tokens/new scoped to *this repo only* with
   **Contents: Read and write**, and paste it on the setup screen. It is stored in
   your browser's localStorage and sent only to `api.github.com`.
3. Use the Buy/Sell form. Each trade becomes a commit
   (`trade: BUY 5 MSFT @ 430.10`) and the public site reflects it within a minute.

## Author

Jeffry Arevalo â€” https://github.com/JeffryArevalo


