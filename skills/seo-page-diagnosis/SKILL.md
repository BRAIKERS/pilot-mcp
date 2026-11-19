---
name: seo-page-diagnosis
description: "Diagnoses why one page underperforms in organic search through the Pilot MCP server: index state and canonical in Search Console, its queries, clicks, impressions and positions over time, an on-page audit (status, tags, load time, technical issues), the keywords it ranks for, and the live results for its main keyword. Use when a page lost traffic, never ranked, or ranks for the wrong queries. Triggers: why does this page not rank, page lost traffic, SEO page audit. Déclencheurs : pourquoi cette page ne se positionne pas, page qui perd du trafic, diagnostic SEO d'une page."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Search Console site and DataForSEO attached. Semrush is optional."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_search_console_list_sites google_search_console_get_url_inspection google_search_console_get_search_analytics dataforseo_onpage_instant_audit dataforseo_serp_google_organic semrush_get_api_units_status semrush_get_url_organic_keywords"
  pilot-mcp-read-only: "true"
---

# SEO page diagnosis

Find why one page does not get the organic traffic it should, and what to fix first. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on a workspace with a Search Console site and DataForSEO attached.
- **Optional MCP servers:** a CMS server to read the page source. None is needed. Semrush, when attached, adds the keywords the URL ranks for.
- **Read-only:** never change the page.

## Tools

- `google_search_console_list_sites`: the properties available.
- `google_search_console_get_url_inspection`: index state, Google canonical and last crawl of the page.
- `google_search_console_get_search_analytics`: queries, clicks, impressions and position of the page over time.
- `dataforseo_onpage_instant_audit`: an on-page check of the URL.
- `dataforseo_serp_google_organic`: the live Google results for the page's main keyword.
- `semrush_get_api_units_status`: Semrush units left, before any Semrush report.
- `semrush_get_url_organic_keywords`: the keywords the URL ranks for.

## Steps

1. Ask for the URL, its main keyword, the country and when the problem started.
2. Call `google_search_console_get_url_inspection`. If the page is not indexed, or Google chose another canonical, that is the diagnosis: explain it and stop the deeper checks unless the user wants them.
3. Call `google_search_console_get_search_analytics` with `dimensions: ["page", "date"]` over 6 months and keep the rows of the page (the tool has no page filter: raise `row_limit` and page with `start_row`). Then call it with `["page", "query"]` for the period before and after the drop: which queries were lost, and whether position, impressions or click-through rate moved.
4. Call `dataforseo_onpage_instant_audit` (with `enable_javascript` when the site renders in the browser): status code, title, meta description, headings, canonical, noindex, load time, issues.
5. Call `dataforseo_serp_google_organic` on the main keyword: who ranks now, what type of page, and which blocks took space.
6. When Semrush is attached, call `semrush_get_api_units_status`, then `semrush_get_url_organic_keywords` with a low `limit`.
7. Weigh the causes: indexing, technical, relevance (the page does not match the intent), competition (stronger pages arrived), or results page change (new blocks), and say which one the evidence supports.

## Output

1. **Diagnosis**: the most likely cause, in two sentences, with the evidence.
2. **Timeline**: clicks, impressions and position over time, with the date of the change.
3. **Lost queries**: before and after.
4. **On-page findings**: issues found by the audit.
5. **Fixes**: in order of expected effect.

## Rules

- Search Console data lags by a few days.
- Separate facts from hypotheses.
- Do not promise a recovery.

## Examples

- "Why did our pricing page lose organic traffic last month?"
- "Fais le diagnostic SEO de la page /guide-isolation."
- "Cette page n'a jamais été visible sur Google : pourquoi ?"
