---
name: search-console-query-opportunities
description: "Finds quick SEO wins in Google Search Console through the Pilot MCP server: queries ranking just off the first positions (about 4 to 20) with real impressions, queries with a click-through rate far below what their position should bring, and the page that ranks for each, then checks the live results page and the search intent to say what to change. Use when the user wants SEO opportunities, pages to optimize first, or titles to rewrite. Triggers: SEO quick wins, striking distance keywords, low CTR queries. Déclencheurs : opportunités SEO, mots-clés presque en première page, requêtes à faible taux de clic, pages à optimiser."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Search Console site attached. The results page and intent checks need DataForSEO attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_search_console_list_sites google_search_console_get_search_analytics dataforseo_serp_google_organic dataforseo_search_intent dataforseo_keyword_search_volume"
  pilot-mcp-read-only: "true"
---

# Search Console query opportunities

Find the queries where a small change can bring more organic clicks, and say what to change on which page. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Search Console site.
- **Optional MCP servers:** a CMS server to apply the title and content changes. None is needed.
- **Read-only:** never change a page. The list is for a person to apply.

## Tools

- `google_search_console_list_sites`: the properties available.
- `google_search_console_get_search_analytics`: clicks, impressions, click-through rate and position by query and page.
- `dataforseo_serp_google_organic`: the live Google results for a query (titles, URLs, featured snippets, questions).
- `dataforseo_search_intent`: the intent of each query (informational, navigational, commercial, transactional).
- `dataforseo_keyword_search_volume`: monthly search volume of the queries.

## Steps

1. Call `google_search_console_list_sites` and pick the property.
2. Call `google_search_console_get_search_analytics` over the last 90 days with `dimensions: ["query", "page"]` and a high `row_limit`; page through with `start_row` when needed.
3. Keep two lists. Striking distance: average position between about 4 and 20 with enough impressions to matter. Low click-through: position 1 to 5 with a click-through rate well below the site's own rate at that position.
4. Remove brand queries if the user wants non-brand only, and merge near-duplicate queries.
5. When DataForSEO is attached, call `dataforseo_search_intent` and `dataforseo_keyword_search_volume` on the shortlist (pass `location_name` and `language_code`), then `dataforseo_serp_google_organic` on the top ten: who ranks above, which formats Google shows (featured snippet, questions, videos, shopping), and whether the ranking page matches the intent.
6. For each opportunity, write the change: new title or meta description, a section answering the question Google shows, internal links to the page, or a different page better matched to the intent.

## Output

1. **Summary**: number of opportunities and the clicks at stake, as an order of magnitude.
2. **Striking distance**: a table with query, page, position, impressions, volume, intent and the change.
3. **Low click-through**: a table with query, page, position, click-through rate and the title or snippet fix.
4. **Wrong page**: queries where another page should rank.

## Rules

- Positions are averages over the period: say so.
- Do not promise rankings or clicks; give the stake as an estimate.
- Search Console hides rare queries: say that totals by query are below site totals.

## Examples

- "Find our best SEO quick wins in Search Console."
- "Quelles requêtes sont presque en première page ?"
- "Quels titres réécrire pour gagner des clics sans changer de position ?"
