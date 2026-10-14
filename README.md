# How to Scrape France Travail Job Postings, No Login, No API Key

> **Run it now, no code: [francetravail-jobs-scraper on the Apify Store](https://apify.com/bibian/francetravail-jobs-scraper)** &nbsp;|&nbsp; $1.20 per 1,000 job postings &nbsp;|&nbsp; no browser, no API key

France Travail (formerly Pôle emploi) is the French government's official job board. Its own keyword search is blocked to crawlers by `robots.txt`, and its partner API requires an OAuth app registration. This guide shows how to get its postings as clean structured data anyway, by profession and département, with a free Apify Actor, from the console or from code.

Searches this page answers: france travail scraper, pole emploi scraper, french job offers api, offres emploi france scraper.

## What you get from the France Travail scraper

- Job postings by profession (métier) and French département, with employer, location and contract type.
- A stable `id` and canonical `url` per posting, straight from France Travail's own listing pages.
- A default run that rotates across a wide spread of métiers and départements nationwide, no input required.
- Clean text with any named contact's e-mail or phone number removed.

Export the dataset as JSON, CSV or Excel, or read it through the Apify API. A real sample (five results, personal data removed) is in [`sample-output.json`](sample-output.json).

## Output fields

| Field | What it is |
|---|---|
| `id`, `url` | France Travail posting id and public link |
| `title` | Job title |
| `companyName` | Hiring company as displayed |
| `departmentCode`, `city` | Where the job is based |
| `metier` | France Travail's own profession category for this listing page |
| `contractType` | Contract type and working time, for example `CDI, Temps plein` |
| `datePosted` | Posting age as shown by France Travail (relative text) |
| `description` | Card preview text, may end with "..." when France Travail truncates it |

## Use cases

- **Cover a profession nationwide.** Set `keywords` to a métier (for example `cuisinier`) and leave `location` empty to collect postings across every département.
- **Monitor hiring in one département.** Set `location` to a French département or region and leave `keywords` empty.
- **Build a daily job alert.** Schedule a run every morning and send the new postings to Slack, e-mail or a Google Sheet.
- **Feed a job board or a regional labour-market dataset.** Structured `departmentCode`/`city`/`contractType` fields, ready to aggregate.

## Input

| Field | What it does |
|---|---|
| `keywords` | Job title or profession, for example `cuisinier`. Empty means every métier. |
| `location` | French département or region name, for example `calvados`. Empty means every département. |
| `maxItems` | The run stops once this many postings are saved (default 50). |

Example input:

```json
{
  "keywords": "cuisinier",
  "location": "calvados",
  "maxItems": 50
}
```

## Three ways to use it

### 1. Apify console, no code

1. Open [the Actor page](https://apify.com/bibian/francetravail-jobs-scraper) and click **Try for free** (a free Apify account is enough).
2. Fill in the input form (profession, département and the maximum number of results) and click **Start**.
3. When the run finishes, open the **Output** tab, check the table, then **Export** as CSV, Excel or JSON.
4. For a daily alert, open **Schedules**, create a daily schedule for the Actor and, optionally, add an integration that sends new results to Slack, e-mail or Google Sheets.

### 2. Apify API with JavaScript

```bash
npm install apify-client
export APIFY_TOKEN="your token from Apify console, Settings, Integrations"
node examples/run.js
```

```js
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: process.env.APIFY_TOKEN });

const run = await client.actor('bibian/francetravail-jobs-scraper').call({
    "keywords": "cuisinier",
    "location": "calvados",
    "maxItems": 50
});

const { items } = await client.dataset(run.defaultDatasetId).listItems();
for (const item of items) {
    console.log(`${item.title} | ${item.companyName} | ${item.city}`);
}
```

The complete file is [`examples/run.js`](examples/run.js) (ES module, Node 18 or later).

### 3. Apify API with Python

```bash
pip install apify-client
export APIFY_TOKEN="your token from Apify console, Settings, Integrations"
python examples/run.py
```

```python
import os
from apify_client import ApifyClient

client = ApifyClient(os.environ["APIFY_TOKEN"])

run = client.actor("bibian/francetravail-jobs-scraper").call(run_input={
    "keywords": "cuisinier",
    "location": "calvados",
    "maxItems": 50,
})

for item in client.dataset(run.default_dataset_id).iterate_items():
    print(f"{item['title']} | {item.get('companyName')} | {item.get('city')}")
```

The complete file is [`examples/run.py`](examples/run.py). Never put your token in the code: read it from the `APIFY_TOKEN` environment variable as above.

## Pricing

Pay per result: **$1.20 per 1,000 job postings** (that is $0.0012 per job posting), plus a tiny start fee. You pay only for the results saved to the dataset. Apify's free plan includes monthly credits, enough to try it on a few hundred results. Prices are those shown on the [Store page](https://apify.com/bibian/francetravail-jobs-scraper) when this guide was written; the Store page is the reference.

## Why this one

- **No login, no browser, no API key.** France Travail's listing pages are plain server-rendered HTML; the official partner API requires an OAuth app registration, this Actor does not.
- **Covers the whole country by profession or département**, not a single city search box.
- **Structured fields**, not a single block of text to re-parse: département code, city and contract type come out already separated.

## Limits and honest notes

- France Travail's own keyword search (`motsCles=`) is blocked to crawlers by its `robots.txt`; this Actor reads the site's SEO listing pages (one per profession × département) instead, so `keywords` and `location` match France Travail's own category names, not free text.
- Each profession × département page lists around 20 postings and has no further pagination available to crawlers; broad coverage comes from rotating across many pages, not from paging one search deeper.
- `description` is the card's own preview text and may end with "..." when France Travail truncates it; the full detail panel on the site is filled in by a script this Actor does not run.
- The dataset contains job postings only. Any named contact's e-mail or phone number found in a posting's text is dropped from `description`.
- This is an unofficial tool, not affiliated with France Travail or the French government. Check the site's terms and the data protection rules that apply to your use.

## FAQ

**Does it need a browser or a proxy?**  
No. The Actor reads France Travail's own listing pages directly.

**Does it work without `keywords` or `location`?**  
Yes. Leave both empty for a productive default run spread across varied professions and départements.

**Can I get the data in Excel or Google Sheets?**  
Yes. Export the dataset as CSV, Excel or JSON from the Apify console, or connect the Apify Google Sheets integration.

## Try it

**[Open francetravail-jobs-scraper on the Apify Store](https://apify.com/bibian/francetravail-jobs-scraper)**

The code in `examples/` is released under the MIT license (see [LICENSE](LICENSE)). The data belongs to its original publishers.
