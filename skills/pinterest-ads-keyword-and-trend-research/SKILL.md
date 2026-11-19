---
name: pinterest-ads-keyword-and-trend-research
description: "Researches Pinterest search demand through the Pilot MCP server: trending keywords by country, related and suggested terms, monthly search volume of candidate keywords, and how they compare with the keywords the account already targets. Use when the user plans Pinterest search campaigns, seasonal content or new keywords. Triggers: Pinterest keyword research, Pinterest trends, what people search on Pinterest. Déclencheurs : recherche de mots-clés Pinterest, tendances Pinterest, ce que les gens cherchent sur Pinterest, mots-clés Pinterest."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Pinterest ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "pinterest_ad_accounts_list pinterest_keywords_list pinterest_keywords_metrics pinterest_keywords_trending pinterest_terms_related pinterest_terms_suggested"
  pilot-mcp-read-only: "true"
---

# Pinterest Ads keyword and trend research

Help the user find what people search on Pinterest around their offer, what is rising, and which keywords to add or drop. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Pinterest ad account.
- **Optional MCP servers:** a spreadsheet server to export the keyword list. None is needed.
- **Read-only:** never create, update or delete a keyword. The list is for a person to apply.

## Tools

- `pinterest_ad_accounts_list`: the ad accounts available, with their IDs.
- `pinterest_keywords_list`: the keywords the account already targets.
- `pinterest_keywords_metrics`: search volume for up to 2,000 keywords.
- `pinterest_keywords_trending`: trending keywords for a region and a trend type.
- `pinterest_terms_related`: terms related to up to four seed terms.
- `pinterest_terms_suggested`: up to ten suggested completions of a seed term.

## Steps

1. Ask for the topic, the seed terms and the country. Pinterest trend regions are codes such as `US`, `GB`, `CA`, `AU`; if the user's country is not served, say so and offer the nearest one.
2. Call `pinterest_ad_accounts_list` and pick the account.
3. Call `pinterest_keywords_list` to know what is already targeted, with match types.
4. Expand the seeds: `pinterest_terms_related` with up to four seeds, and `pinterest_terms_suggested` for each main seed.
5. Call `pinterest_keywords_trending` with the `region` and a `trend_type` (for example growing or seasonal), filtered by `interests` when relevant, to find rising searches.
6. Call `pinterest_keywords_metrics` on the candidate list to get search volume.
7. Group the candidates by theme, mark those already targeted, and rank the rest by volume and fit with the offer.

## Output

1. **Summary**: the themes with the most demand and the ones rising now.
2. **Keyword table**: keyword, theme, volume, trend, already targeted or not.
3. **To add**: the best new keywords, grouped by theme, with a suggested match type.
4. **To review**: targeted keywords with little or no search volume.
5. **Seasonality**: when the main themes peak, when the trend data shows it.

## Rules

- Search volumes and trends are Pinterest's own figures: present them as estimates.
- A volume Pinterest did not return is absent, not zero.
- Do not promise traffic from a keyword.

## Examples

- "What are people searching on Pinterest around home decor right now?"
- "Trouve-moi des mots-clés Pinterest pour la gamme mariage."
- "Quels mots-clés Pinterest ciblés n'ont presque pas de recherches ?"
