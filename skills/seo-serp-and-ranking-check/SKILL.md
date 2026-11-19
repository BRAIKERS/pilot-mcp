---
name: seo-serp-and-ranking-check
description: "Checks where a site ranks on Google for chosen keywords through the Pilot MCP server: the live results page for each keyword (who ranks, which page of the site appears, featured snippet, people also ask and other blocks), the site's average position from Search Console, the keywords a URL ranks for, and the top 100 rankings of the domain. Use when the user asks where they rank, who outranks them, or which page Google shows. Triggers: ranking check, where do we rank, SERP check. Déclencheurs : vérifier nos positions, où se positionne-t-on, page de résultats Google, suivi de positions."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with DataForSEO attached. Search Console and Semrush are optional."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "dataforseo_serp_google_organic dataforseo_ranked_keywords google_search_console_list_sites google_search_console_get_search_analytics semrush_get_api_units_status semrush_get_url_organic_keywords"
  pilot-mcp-read-only: "true"
---

# SEO results page and ranking check

Tell the user where the site ranks for the keywords that matter, which page ranks, and who is ahead. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on a workspace with DataForSEO attached. Search Console and Semrush, when attached, add measured positions and URL detail.
- **Optional MCP servers:** a spreadsheet server to keep a ranking log. None is needed.
- **Read-only:** nothing is changed.

## Tools

- `dataforseo_serp_google_organic`: the live Google results for a keyword, location and language.
- `dataforseo_ranked_keywords`: the keywords a domain ranks for in the top 100.
- `google_search_console_list_sites`: the Search Console properties available.
- `google_search_console_get_search_analytics`: measured average position, clicks and impressions by query and page.
- `semrush_get_api_units_status`: Semrush units left, before any Semrush report.
- `semrush_get_url_organic_keywords`: the keywords one URL ranks for.

## Steps

1. Ask for the keywords (up to about twenty), the site's domain, the country and the language, and the device if it matters.
2. Call `dataforseo_serp_google_organic` for each keyword with `location_name`, `language_code` and `depth` 20 to 50: position of the site, ranking URL, the domains above it, and the blocks shown (featured snippet, people also ask, local pack, videos, shopping).
3. When Search Console is attached, call `google_search_console_get_search_analytics` over the last 28 days with `dimensions: ["query", "page"]` to compare with the measured average position and see clicks.
4. Call `dataforseo_ranked_keywords` on the domain to place these keywords among all its rankings.
5. For a page that should rank but does not, call `semrush_get_api_units_status`, then `semrush_get_url_organic_keywords` with a low `limit` to see what Google ranks it for instead.
6. Flag: a different page ranking than the one intended, two pages of the site alternating on one keyword, a block (snippet, local pack) that pushes organic results down.

## Output

1. **Summary**: how many keywords rank in the top 3, top 10 and beyond.
2. **Ranking table**: keyword, position, ranking URL, intended URL, top competitor, blocks on the page.
3. **Measured position**: Search Console average position and clicks, when attached.
4. **Issues**: wrong page, competing pages, blocks to target.
5. **Next steps**: page by page.

## Rules

- A live check is one snapshot for one location and device; Search Console gives an average over days: never mix them without saying so.
- Name the location, language and date of the check.
- Do not promise rankings.

## Examples

- "Where do we rank for our ten main keywords in France?"
- "Qui nous devance sur « logiciel de paie » et avec quelle page ?"
- "Quelle page de notre site Google affiche-t-il pour « devis toiture » ?"
