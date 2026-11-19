---
name: seo-keyword-research
description: "Runs SEO keyword research through the Pilot MCP server with DataForSEO or Semrush: expands seed keywords into ideas and related searches, adds monthly volume, cost per click, difficulty and search intent, collects the questions people ask, then groups keywords into topics and ranks them by opportunity. Use when the user plans content, a new section or a site, or wants to know what people search around a topic. Triggers: keyword research, find SEO keywords, search volume. Déclencheurs : recherche de mots-clés, trouver des mots-clés SEO, volume de recherche, idées de contenus SEO."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with DataForSEO or Semrush attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "dataforseo_keyword_ideas dataforseo_related_keywords dataforseo_keyword_search_volume dataforseo_keyword_difficulty dataforseo_search_intent semrush_get_api_units_status semrush_get_keyword_overview semrush_get_related_keywords semrush_get_keyword_questions"
  pilot-mcp-read-only: "true"
---

# SEO keyword research

Turn a topic and a few seed keywords into a ranked, grouped list of keywords worth targeting. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on a workspace with DataForSEO or Semrush attached. Use whichever is attached; DataForSEO first when both are.
- **Optional MCP servers:** a spreadsheet server to export the list. None is needed.
- **Read-only:** nothing is changed anywhere. Semrush reports consume the client's Semrush units.

## Tools

- `dataforseo_keyword_ideas`: keyword ideas from seed keywords, with volume, cost per click and difficulty.
- `dataforseo_related_keywords`: searches related in Google's graph, with a chosen depth.
- `dataforseo_keyword_search_volume`: monthly volume, cost per click and competition.
- `dataforseo_keyword_difficulty`: a difficulty score from 0 to 100.
- `dataforseo_search_intent`: the intent of each keyword.
- `semrush_get_api_units_status`: the Semrush units left, at no cost; call it before any Semrush report.
- `semrush_get_keyword_overview`: volume, cost per click and competition of one keyword.
- `semrush_get_related_keywords`: related keywords of a seed.
- `semrush_get_keyword_questions`: questions asked around a seed.

## Steps

1. Ask for the topic, three to five seed keywords, the country and the language. Pass them as `location_name` and `language_code` (DataForSEO) or `database` (Semrush, for example `fr` for France).
2. With DataForSEO: `dataforseo_keyword_ideas` on the seeds, `dataforseo_related_keywords` on the main seed with `depth` 1 or 2, then `dataforseo_keyword_search_volume`, `dataforseo_keyword_difficulty` and `dataforseo_search_intent` on the merged list.
3. With Semrush: call `semrush_get_api_units_status` first and tell the user what the planned reports will use. Then `semrush_get_keyword_overview` on each seed, `semrush_get_related_keywords` and `semrush_get_keyword_questions` with a low `limit` (20 to 50). Semrush bills per row: set `confirm_units` only after the user agrees to a large call.
4. Clean the list: remove duplicates, off-topic and brand terms of others unless asked, and keywords with no volume.
5. Group keywords into topics, one target page per topic, and pick the main keyword of each group.
6. Rank groups by opportunity: volume, difficulty the site can reach, and intent that matches the offer.

## Output

1. **Summary**: the topics with the most opportunity.
2. **Topic table**: topic, main keyword, volume, difficulty, intent, suggested page type.
3. **Keyword list**: every kept keyword with its topic and figures.
4. **Questions**: questions to answer in content or FAQs.
5. **Next steps**: pages to create or improve first.

## Rules

- Volumes and difficulty are estimates from the provider: name the provider and the country.
- A volume the provider did not return is absent, not zero.
- Do not promise rankings or traffic.

## Examples

- "Do keyword research for our new running shoes section in France."
- "Trouve des mots-clés SEO autour de la facturation électronique."
- "Quelles questions les gens posent-ils sur les pompes à chaleur ?"
