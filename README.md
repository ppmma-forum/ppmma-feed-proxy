# PPMMA Feed Proxy

A tiny GitHub Actions workflow that fetches RSS feeds from publishers that
block Cloudflare Workers' IP range (notably Seattle Times) and re-serves
them via `raw.githubusercontent.com`. Our news-stand worker polls the raw
URLs instead of the original sources.

## How it works

`.github/workflows/fetch-feeds.yml` runs every 30 min on GitHub's
runners (Azure IPs that publishers don't block). For each entry in the
`FEEDS` array it:

1. `curl`s the source URL with a friendly user-agent
2. Writes the response to `feeds/<slug>.xml`
3. Commits + pushes only if the file content changed

## Adding a new feed

Edit `.github/workflows/fetch-feeds.yml`, append a line in the `FEEDS=( )`
block:

```bash
"slug-name|https://source.example.com/rss/"
```

…then commit. The next scheduled run picks it up.

## Public raw URLs

Once the repo is public and the first action has run, each feed is available at:

```
https://raw.githubusercontent.com/<owner>/<repo>/main/feeds/<slug>.xml
```

That's the URL the news-stand worker polls.

## Manual run

Actions tab → "Fetch feeds" → "Run workflow" — useful to verify your setup
without waiting for the next 30-minute tick.

## Currently proxied

- `seattle-times` — Seattle Times homepage feed (`/feed/`)
