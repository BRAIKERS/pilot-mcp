---
name: seo-competitor-gap-analysis
description: "Finds the organic keyword gap between a site and its competitors through the Pilot MCP server with DataForSEO or Semrush: who the organic competitors are, the keywords they rank for that the site does not, the keywords both rank for with who is ahead, and the topics to win first. Use when the user asks why a competitor gets more organic traffic, or which topics to cover to catch up. Triggers: SEO competitor gap, keyword gap, competitor keywords. Déclencheurs : écart de mots-clés, mots-clés des concurrents, analyse concurrentielle SEO, gap SEO."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with DataForSEO or Semrush attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "dataforseo_competitors_domain dataforseo_ranked_keywords dataforseo_domain_intersection semrush_get_api_units_status semrush_get_domain_overview semrush_get_organic_competitors semrush_get_domain_organic_keywords"
  pilot-mcp-read-only: "true"
---

# SEO competitor gap analysis

Show the user where competitors win organic search and the site does not, and which topics to go after first. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on a workspace with DataForSEO or Semrush attached. DataForSEO first when both are.
- **Optional MCP servers:** a spreadsheet server to export the gap list. None is needed.
- **Read-only:** nothing is changed. Semrush reports consume the client's Semrush units.

## Tools

- `dataforseo_competitors_domain`: organic competitors of a domain with overlap metrics.
- `dataforseo_ranked_keywords`: keywords a domain ranks for in the top 100, with position, volume and estimated traffic.
- `dataforseo_domain_intersection`: keywords two domains both rank for.
- `semrush_get_api_units_status`: Semrush units left, before any Semrush report.
- `semrush_get_domain_overview`: estimated organic traffic and keyword count of a domain.
- `semrush_get_organic_competitors`: organic competitors of a domain.
- `semrush_get_domain_organic_keywords`: keywords a domain ranks for.

## Steps

1. Ask for the site's domain (without protocol), the country, the language, and the competitors the user has in mind.
2. Find competitors: `dataforseo_competitors_domain` or, with Semrush, `semrush_get_api_units_status` then `semrush_get_organic_competitors` with `limit` 10. Keep three to five real business competitors; drop marketplaces, media and directories unless the user wants them.
3. Size each domain: `semrush_get_domain_overview`, or the totals of `dataforseo_ranked_keywords`.
4. Read rankings: `dataforseo_ranked_keywords` for the site and each competitor with a `limit` the user accepts, or `semrush_get_domain_organic_keywords` with a low `limit` (Semrush bills per row).
5. Shared keywords: `dataforseo_domain_intersection` between the site and each competitor, to see where the competitor is ahead.
6. Build the gap: keywords where competitors rank in the top 10 and the site is absent or beyond position 20. Group them by topic, add volume and estimated traffic, and mark the intent.
7. Rank topics by traffic at stake and by how close the site already is.

## Output

1. **Summary**: who leads organic search and the three topics to win first.
2. **Competitors**: a table with domain, ranked keywords, estimated traffic, overlap with the site.
3. **Gap by topic**: topic, keywords, volume, best competitor and its position, the site's position.
4. **Where the site is close**: shared keywords where a competitor is just ahead.
5. **Next steps**: pages to create or strengthen.

## Rules

- Traffic figures are provider estimates: name the provider and country.
- Query a domain in the database of its main market: a .fr site in the `fr` database.
- Do not promise rankings.

## Examples

- "What keywords do our competitors rank for that we don't?"
- "Fais l'analyse des écarts SEO avec nos trois concurrents principaux."
- "Sur quels sujets le concurrent X nous devance-t-il en référencement ?"
