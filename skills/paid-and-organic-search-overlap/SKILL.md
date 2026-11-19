---
name: paid-and-organic-search-overlap
description: "Crosses Google Ads and Google Search Console through the Pilot MCP server: queries where the site pays for clicks while already ranking well organically, queries with good organic demand but no paid coverage, and paid search terms that convert but have no organic page, then proposes where to cut, keep or add spend and content. Use when the user asks whether they pay for clicks they would get anyway, or how paid and organic search should work together. Triggers: paid and organic overlap, SEO and SEA, brand bidding. Déclencheurs : SEO et SEA, chevauchement payant et naturel, payer pour sa marque, recouvrement Google Ads et référencement naturel."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Ads account and a Search Console site attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_get_search_terms_report google_get_keyword_performance google_search_console_list_sites google_search_console_get_search_analytics pilot_cross_platform_overview"
  pilot-mcp-read-only: "true"
---

# Paid and organic search overlap

Show the user where Google Ads and organic search overlap, where they leave gaps, and what to change on each side. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Google Ads account and the Search Console site.
- **Optional MCP servers:** a spreadsheet server to export the query lists. None is needed.
- **Read-only:** never change a campaign, keyword or page. Proposals are for a person to apply.

## Tools

- `google_list_accounts`: the Google Ads accounts available.
- `google_get_search_terms_report`: the searches that triggered ads, with cost and conversions.
- `google_get_keyword_performance`: keyword results, including search impression share.
- `google_search_console_list_sites`: the Search Console properties available.
- `google_search_console_get_search_analytics`: organic clicks, impressions and position by query.
- `pilot_cross_platform_overview`: paid spend next to organic traffic, with `include_organic: true`.

## Steps

1. Call `google_list_accounts` and pick the account; use its ID as `customer_id`. Call `google_search_console_list_sites` and pick the matching property.
2. Use the same period on both sides, the last 30 or 90 days.
3. Call `google_get_search_terms_report` with a high `limit` (up to 1000) and `google_get_keyword_performance` for impression share.
4. Call `google_search_console_get_search_analytics` with `dimensions: ["query"]` and a high `row_limit`.
5. Normalize queries (case, accents, plurals) and join the two lists.
6. Sort into four groups: both strong (paid query with organic position 1 to 3), paid only (no organic presence), organic only (good organic demand, no ad), and paid converting queries with organic position beyond 10 (content to build).
7. Call `pilot_cross_platform_overview` with `include_organic: true` over the same days to frame the totals.
8. For brand queries in the "both strong" group, do not conclude that ads are wasted: competitors may bid on the brand. Propose a controlled test rather than a cut.

## Output

1. **Summary**: paid spend on queries where the site already ranks in the top 3, and the main opportunity.
2. **Both strong**: query, paid cost and conversions, organic position and clicks, proposal (test a reduction, keep).
3. **Paid only**: converting queries with no organic page, as content ideas.
4. **Organic only**: queries worth testing in ads.
5. **Next steps**: for the paid team and the content team.

## Rules

- Search Console hides rare queries and Google Ads shows only part of search terms: say that the match is partial.
- Name the period and the currency.
- Never present a reduction of brand spend as a sure saving.

## Examples

- "Are we paying for clicks we would get organically anyway?"
- "Croise nos mots-clés Google Ads et nos positions SEO."
- "Sur quelles requêtes payantes devrait-on créer du contenu ?"
