# Seek Job Monitor (GitHub Actions)

Repository contains a serverless job monitor for Seek.com.au that runs on GitHub Actions.

Files added:
- `main.py` — the scraper, notifier and state manager.
- `requirements.txt` — Python dependencies.
- `.github/workflows/monitor.yml` — workflow to run every 30 minutes.
- `data/seen_jobs.json` — persisted state (initially empty).

Setup
1. Create a GitHub repository and push this project.
2. In the repository Settings -> Secrets and variables -> Actions add the following secrets/variables:
    - `TELEGRAM_TOKEN` — your bot token from @BotFather
    - `TELEGRAM_CHAT_ID` — your chat id
    - `SEARCH_KEYWORDS` — comma-separated list of search terms (e.g. `python developer,qa automation`)
    - (optional) `PROXY_URL` — e.g. `http://user:pass@host:port`
    - (optional) `SEARCH_LOCATION` — default location (overrides default `Auckland`)
3. Ensure Actions have Read and write permissions for contents (Settings -> Actions -> General).

Example GitHub Actions snippet (pass secrets into the job environment):

```yaml
env:
   TELEGRAM_TOKEN: ${{ secrets.TELEGRAM_TOKEN }}
   TELEGRAM_CHAT_ID: ${{ secrets.TELEGRAM_CHAT_ID }}
   SEARCH_KEYWORDS: ${{ secrets.SEARCH_KEYWORDS }}
   SEARCH_LOCATION: ${{ secrets.SEARCH_LOCATION }}

jobs:
   monitor:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v4
         - name: Set up Python
            uses: actions/setup-python@v4
            with:
               python-version: '3.11'
         - name: Install deps
            run: pip install -r requirements.txt
         - name: Run monitor
            run: python main.py
```

Notes: `SEARCH_KEYWORDS` can be comma-separated or newline-separated. The script also accepts a few common env aliases when running in Actions, for example `GITHUB_SEARCH_KEYWORDS` or `INPUT_SEARCH_KEYWORDS`.

Notes
- The scraper uses `cloudscraper` to bypass basic Cloudflare checks. If you encounter 403/429 frequently, consider using a residential proxy via `PROXY_URL` or a paid scraping API.
- This project is for educational purposes. Respect Seek's Terms of Service and robots.txt.
