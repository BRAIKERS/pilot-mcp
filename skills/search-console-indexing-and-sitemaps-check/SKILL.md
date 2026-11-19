---
name: search-console-indexing-and-sitemaps-check
description: "Checks how Google indexes a site through the Pilot MCP server: sitemaps submitted with their errors, warnings and indexed counts, URL inspection of key pages (indexed or not, canonical chosen by Google, crawl date, robots and fetch state), pages that earn impressions against pages that should, and an on-page audit of pages left out. Use when pages are missing from Google, after a migration or a redesign, or when Search Console shows indexing drops. Triggers: indexing check, pages not indexed, sitemap errors. Déclencheurs : contrôle d'indexation, pages non indexées, erreurs de sitemap, indexation après migration."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Search Console site attached. The on-page audit needs DataForSEO attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_search_console_list_sites google_search_console_get_sitemaps google_search_console_get_sitemap google_search_console_get_url_inspection google_search_console_get_search_analytics dataforseo_onpage_instant_audit"
  pilot-mcp-read-only: "true"
---

# Search Console indexing and sitemaps check

Tell the user which of their important pages Google indexes, which it leaves out, and why. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Search Console site.
- **Optional MCP servers:** a crawler or CMS server to list every URL of the site. None is needed.
- **Read-only:** never submit or delete a sitemap. Fixes are for a person to apply.

## Tools

- `google_search_console_list_sites`: the Search Console properties available, with permission level.
- `google_search_console_get_sitemaps`: sitemaps submitted, with last download, errors and warnings.
- `google_search_console_get_sitemap`: one sitemap in detail, with submitted and indexed counts per content type.
- `google_search_console_get_url_inspection`: the index status of one URL as Google sees it.
- `google_search_console_get_search_analytics`: clicks and impressions by page.
- `dataforseo_onpage_instant_audit`: an on-page check of one URL (status code, tags, load time, issues).

## Steps

1. Call `google_search_console_list_sites` and pick the property (domain or URL prefix). Use it as `site_url`.
2. Call `google_search_console_get_sitemaps`, then `google_search_console_get_sitemap` on each: last read date, errors, warnings, submitted against indexed counts. A sitemap not read for weeks, or with many more submitted than indexed URLs, is the first lead.
3. Ask for the key pages (home, categories, best sellers, landing pages), or take the pages with the most impressions from `google_search_console_get_search_analytics` with `dimensions: ["page"]` over the last 90 days, plus the pages the user expects but cannot find.
4. Call `google_search_console_get_url_inspection` on each key page: indexed or not, coverage state, canonical declared against canonical chosen by Google, last crawl, robots.txt and fetch state, mobile usability when given. One call per URL: keep the list to the pages that matter.
5. For pages not indexed or with a different canonical, call `dataforseo_onpage_instant_audit` when DataForSEO is attached: status code, noindex, canonical tag, duplicate title, thin content, load time.
6. Group the causes: blocked (robots, noindex), duplicate (canonical elsewhere), discovered but not crawled, crawled but not indexed, errors (4xx, 5xx, redirects).

## Output

1. **Verdict**: share of key pages indexed, and the main cause of the rest.
2. **Sitemaps**: a table with sitemap, last read, submitted, indexed, errors.
3. **Pages**: a table with URL, index state, Google canonical, last crawl, cause.
4. **Fixes**: grouped by cause, in order of pages affected.

## Rules

- Search Console data lags by a few days: say so for recent changes.
- Quote Google's coverage state as given.
- Inspection reflects Google's last crawl, not the live page: say when the audit and the inspection disagree.

## Examples

- "Why are some of our product pages not on Google?"
- "Vérifie l'indexation et les sitemaps après la migration du site."
- "Quelles pages clés Google ignore-t-il, et pourquoi ?"
