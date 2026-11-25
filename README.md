# SERP Scraping API Pricing, Explained: What 1,000 Google Searches Cost, Which ScraperAPI Plan Fits, and What Fields Come Back

ScraperAPI charges 25 credits for a Google or Bing search request, and that single number answers most of the questions people bring to a SERP scraping API. On the $49/month Hobby plan it works out to roughly $12.25 per 1,000 Google searches, which means the Hobby pool covers about 4,000 lookups a month. The free plan's 1,000 credits cover 40.

What follows is the plan ladder and what happens when your pool runs dry, the request parameters that quietly raise the per-search price, the JSON fields you actually get back, and the jobs where a different provider is the smarter buy.

> **Quick take:** ScraperAPI fits teams that want search results as one input in a wider scraping stack, billed on the same credit pool as the pages behind those results. Rank tracking at volume is the expensive use case; following the ranking URLs is the cheap one, at 1 credit per standard page. 👉 [See the current ScraperAPI plans and credit allowances](https://www.scraperapi.com/pricing?fp_ref=coupons)

## The plan ladder, and what happens when credits run out

Every plan draws from a monthly credit pool. When it empties, the account pauses until renewal unless you've enabled an overage mechanism, and which mechanism you get depends on the tier you're on.

| Plan | Monthly price | Auto-upgrade at zero | Pay-as-you-go | Get started |
| --- | --- | --- | --- | --- |
| Free | $0 | No | No | [Free plan details](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Hobby | $49 | Yes | No | [Hobby plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Startup | Tiered* | Yes | No | [Startup plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Business | Tiered* | Yes | No | [Business plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Scaling | Tiered* | Yes | Yes | [Scaling plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Professional | Tiered* | Yes | Yes | [Professional plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Advanced | Tiered* | No | Yes | [Advanced plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Enterprise | Custom | No | Yes | [Enterprise plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |

*Tier prices and credit allowances scale with the size of the pool and are listed on the pricing page.

Two details in that table matter more than the rest. Pay-as-you-go only switches on at Scaling, so on Hobby, Startup or Business the only way past your ceiling is auto-upgrade, which bumps you to the next tier at the next tier's price. Useful if your volume is climbing; unhelpful if one big crawl in the middle of the month doubles your bill. Advanced and Enterprise flip the arrangement: metered overage, no automatic climb.

The other detail is the free tier's shape. It gives you 1,000 credits and a hard cap of 5 concurrent connections. That's 40 Google searches, enough to check parsing quality on your own keyword set and not much else. If you need a real volume test, the 7-day trial hands out 5,000 credits, which is 200 Google searches at the SERP rate.

Every plan carries CAPTCHA and anti-bot handling, automatic retries, custom sessions and headers, desktop and mobile user agents, unlimited bandwidth and a 99.9% uptime guarantee.

## What one search request costs, down to the credit

ScraperAPI prices requests by domain, not by endpoint or by plan. The published rates:

| Request type | Credits per request |
| --- | --- |
| Standard page | 1 |
| Amazon, Walmart, eBay | 5 |
| Google and Bing, including all subdomains | 25 |
| LinkedIn | 30 |
| Bot-protection bypass (Cloudflare, Turnstile, DataDome, PerimeterX, Akamai) | +10 |

Parameters stack on top of the domain price:

- `premium=true`: +10 credits
- `render=true`: +10 credits
- `screenshot=true`: +10 credits
- `ultra_premium=true`: +30 credits
- `premium=true` with `render=true`: 25 credits total
- `ultra_premium=true` with `render=true`: 75 credits total

And these cost nothing extra: `country_code`, `device_type`, `output_format`, `autoparse`, `session_number`, `wait_for_selector`, `keep_headers`.

Run the arithmetic on a Google SERP and the economics get sharp. A plain search is 25 credits, so 1,000 searches is 25,000 credits. A search that also carries `render=true` is 35 credits, and the same 1,000 searches now cost about $17.15 at the Hobby per-credit rate instead of $12.25. Google rarely needs rendering; if you're copying a script from somewhere else, make sure the flag isn't still on.

You don't have to guess at any of this. ScraperAPI exposes an account-level cost endpoint that returns the credit price for a specific URL with a specific parameter set, a `max_cost` cap you can set per request so no single scrape exceeds a ceiling you choose, and an `sa-credit-cost` header on every response that tells you what that request actually consumed. During the trial, log those three things for a hundred real queries and you'll know your true monthly cost better than any table can tell you.

**The cheap half of the workflow.** The SERP costs 25 credits, but the ten pages that rank for it cost 1 credit each, or 5 if they happen to be Amazon. One keyword plus its full first page is 35 credits, of which the search is most of the bill. If you need the page content for embeddings or extraction, that ratio is the argument for keeping search and scraping on one account rather than bolting a second vendor onto the pipeline.

## The fields you actually get back

Structured JSON is available for four Google surfaces: Search, Shopping, News and Jobs. The schemas are specific enough to plan against without a trial.

**Organic and news results** come back under `articles`, with `source`, `thumbnail`, `title`, `description`, `date` and `link`.

**Ads** come back under `ads`, with `position`, `block_position` (top or bottom of the page), `title`, `link`, `displayed_link`, `tracking_link`, `description` and a `sitelinks` array holding each sitelink's title, link and description. That `tracking_link` field is what you'd use for ad intelligence work; without it you can see that a competitor bids, but not where the click actually lands.

**Jobs** come back under `jobs_results`, with `title`, `company_name`, `location`, `link`, `via`, `description` and `extensions` such as "Posted 5 days ago", "Work from home" or "Employment Type Full-time".

Beyond those arrays, the documented output covers the People Also Ask box, related searches and ranking positions, so the SERP features are parsed rather than left in raw HTML.

What the documentation does not promise is just as important. AI Overviews, the local pack and the knowledge panel aren't in the field list. Competitor pages now advertise AI Mode answers and map listings as headline features. If your product depends on AI Overview text or local pack data, do not assume ScraperAPI's parser returns it. Pull twenty queries in your own vertical during the trial and look at the raw response before you write the schema.

One more parameter worth knowing: `output_format` doesn't add credits. You can take JSON where it's supported and HTML everywhere else without paying a premium for the structured version.

## Localization and device targeting cost nothing extra

`country_code` and `device_type` are both on the free-parameter list, which matters more than it sounds. Rank tracking without geo-targeting is guesswork, because the SERP a searcher in Berlin sees is not the SERP you see from a Virginia datacenter. Passing a country code routes the request through that location's proxies so the result matches what a local user gets, and the same applies to mobile versus desktop layouts, which reorder results on plenty of commercial queries.

ScraperAPI's proxy network covers 50+ countries according to its SERP product page. That page is a couple of years old, so check the current country list against the markets you report on before you commit to it. For US, UK, DE, FR and the other major ad markets, coverage is a safe assumption. For a client in a smaller market, confirm it first.

## DataPipeline: the no-code path

If nobody on the team wants to write a scheduler, ScraperAPI's DataPipeline handles it. Create a project in the dashboard, pick the Google Search template, paste your keyword list, set geotargeting and a schedule, and run it. Data downloads in a few minutes. Each project accepts 10,000 URLs and there's no limit on the number of projects.

That's a good fit for an agency running recurring keyword pulls for a dozen clients, or for a content team that wants weekly SERP snapshots without a developer in the loop.

The boundary is flexibility. Templates give you the fields ScraperAPI decided to parse. Custom extraction rules, retries with your own logic, or hundreds of parallel requests need the API directly, where you control parameters and pacing.

## How ScraperAPI compares on the things that decide it

| Provider | Engines covered | Output formats | How you're billed | Free tier and guarantee |
| --- | --- | --- | --- | --- |
| ScraperAPI | Google and Bing priced at 25 credits; Google Search, Shopping, News and Jobs JSON endpoints | JSON on structured endpoints, HTML elsewhere | Credits by domain and parameters | 1,000 free credits, 5 concurrent; 7-day trial with 5,000 credits |
| Decodo | 6 engines including Google, Bing, Yahoo, DuckDuckGo and Baidu; 195+ locations | JSON, HTML, Markdown, XHR, plus CSV and PNG where supported | Per request, successful requests only | Free starter up to 10K recurring requests; 14-day money-back |
| Bright Data | Google, Bing, DuckDuckGo, Yandex, Baidu, Yahoo, Naver | JSON, Markdown, HTML | Per successful request; async billed on send only | 5K requests/month free; 99.9% uptime SLA |

Oxylabs sits in the same bracket, with HTML or structured JSON covering organic and paid results, and an unusually granular set of Google sources: autocomplete, carousels, featured snippets, Finance, related searches, Top Stories, Lens, local search, hotels and Trends.

Where a competitor is the better buy, honestly:

**You need 195-country localization.** Decodo and Bright Data both publish 195+ locations; ScraperAPI publishes 50+. If your reporting spans markets outside the major ad economies, that gap is the deciding factor.

**You need Markdown straight into an LLM.** Bright Data and Decodo both output Markdown natively, which saves a conversion step in a RAG pipeline. Bright Data's `parsed_light` mode, which returns only the top 10 organic results with no ads or knowledge panel, is also a reasonable trade if latency matters more than completeness.

**You need one key for search, scrape and crawl.** Decodo runs all three off a single API key, and ranking URLs can go straight into a crawl of up to 10K pages. With ScraperAPI you can do the same work, since the pages behind results cost 1 credit each, but you'd write the orchestration yourself.

**You need Baidu, Yandex or Naver.** Bright Data lists all three. ScraperAPI's cost table names Google and Bing only, so treat any other engine as unconfirmed until you test it.

Two claims deserve caution on the competitor side as well. Bright Data advertises full SERPs in under a second, but that's Fast SERP, which requires your account manager to enable it; the same product page elsewhere states response times under 5 seconds. And "pay only for successful requests" is a real billing difference worth reading the terms on, because ScraperAPI's credit documentation prices requests by domain and parameter without stating whether a blocked request still burns credits.

## Which tier to buy, by job

**Testing one endpoint or a one-off script:** the free plan. 40 Google searches is enough to inspect the JSON, confirm PAA and related searches show up for your queries, and see whether your target market is in the proxy network.

**Tracking up to about 130 keywords once a day:** Hobby. 130 keywords daily is roughly 4,000 searches a month, which is what $49 buys at 25 credits per SERP. 👉 [Start on the Hobby tier](https://www.scraperapi.com/pricing?fp_ref=coupons)

**Tracking 500 keywords daily:** that's 15,000 searches, or 375,000 credits, roughly $184 a month at the Hobby per-credit rate. Larger pools price credits lower, so the real number will be smaller, but you're past the entry tier either way. Turn on pay-as-you-go rather than auto-upgrade if your volume is spiky.

**Feeding a RAG pipeline or an agent:** budget 35 credits per keyword if you pull the top 10 pages along with the SERP, and skip the page fetches entirely when snippets are enough. Also check who handles the Markdown conversion: ScraperAPI returns HTML or JSON, so that step is yours.

**Agency work with recurring client reports:** DataPipeline, one project per client, scheduled weekly. Keep the API for anything that needs custom parsing.

Two things to confirm before you enter a card: the exact credit cost of your real target URL with your real parameters, which the dashboard's cost estimator returns, and whether failed or blocked requests consume credits, which the credit documentation doesn't spell out. Both are answerable inside the 7-day, 5,000-credit trial, and both change your monthly number more than the plan name does. 👉 [Compare the ScraperAPI tiers and current prices](https://www.scraperapi.com/pricing?fp_ref=coupons)
