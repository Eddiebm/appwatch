# AppWatch — Fleet Monitor

A zero-dependency uptime watchdog that lives in your Git repo. Monitor all your deployed apps from one place.

## Files

```
appwatch/
├── index.html              # The dashboard — deploy this or open locally
├── appwatch-config.json    # Your app registry — edit this
└── .github/
    └── workflows/
        └── appwatch.yml    # GitHub Actions scheduled pinger
```

## Quick Start

### 1. Edit the config
Open `appwatch-config.json` and add all your apps:
```json
{
  "apps": [
    { "name": "My App", "url": "https://myapp.pages.dev", "cat": "Cloudflare Pages" },
    { "name": "My API",  "url": "https://myapi.workers.dev", "cat": "API" }
  ]
}
```

### 2. Open the dashboard
- **Locally**: just open `index.html` in your browser
- **Deployed**: push to any static host (Cloudflare Pages, GitHub Pages, Netlify)
  - Set the root to the `appwatch/` folder
  - The dashboard is a single HTML file — no build step needed

### 3. Enable scheduled pinging via GitHub Actions
The `.github/workflows/appwatch.yml` runs every 30 minutes and:
- Pings every URL in `appwatch-config.json`
- Logs OK / SLOW / DOWN status to the Actions run log
- Adds a GitHub warning annotation for slow apps
- Optionally fails the run (triggering email from GitHub) if any app is down

To get email alerts when apps go down, uncomment the `exit 1` line in the workflow.

## Dashboard Features

- **Live ping** — click "Check All" or "Ping" per app to test now
- **Sparkline history** — last 20 checks shown as bars per app
- **Status badges** — Up / Down / Slow (>3s) / Unknown
- **Check log** — scrollable history of all pings with latency
- **Export config** — download your app list as JSON
- **Add/remove apps** — managed in the browser, persisted to localStorage

## Notes on CORS

The dashboard uses `mode: 'no-cors'` fetch calls (HEAD requests). This means:
- If a site is reachable and responds, it counts as **Up**
- If the fetch throws (unreachable, timeout, DNS failure), it counts as **Down**
- You won't see HTTP status codes — just reachability + latency

For more precise checks (actual HTTP status codes), use the GitHub Actions workflow, which runs curl with full visibility.

## Slow Threshold

Default slow threshold is **3000ms**. To change it, edit this line in `index.html`:
```js
const SLOW_THRESHOLD = 3000;
```
