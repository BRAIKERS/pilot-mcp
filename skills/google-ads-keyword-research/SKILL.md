---
name: google-ads-keyword-research
description: "Researches new keywords for Google Ads through the Pilot MCP server: ideas from Keyword Planner seeded by keywords or a website, with monthly searches, competition and top of page bids, set against the keywords already in the account and the search terms that already convert, then grouped into themes ready for ad groups. Use when the user asks for keyword ideas, to expand a campaign, or to prepare a new Search campaign. Triggers: keyword research, keyword ideas, keyword planner, expand keywords. Déclencheurs : recherche de mots-clés, idées de mots-clés, planificateur de mots-clés, élargir les mots-clés."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Ads account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_get_keyword_ideas google_search_locations google_list_keywords google_get_search_terms_report google_list_keyword_plans"
  pilot-mcp-read-only: "true"
---

# Google Ads keyword research

Find keywords worth adding, with their volume and cost, without repeating what the account already has. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a spreadsheet server to hand over the keyword list. None is needed.
- **Read-only:** never add a keyword or create a plan. The list is for a person, or for the keyword management skill after approval.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_get_keyword_ideas`: Keyword Planner ideas with average and monthly searches, competition and top of page bids.
- `google_search_locations`: location IDs by name, for the market studied.
- `google_list_keywords`: the keywords already in the account.
- `google_get_search_terms_report`: search terms that already triggered ads, with their conversions.
- `google_list_keyword_plans`: keyword plans already saved in the account.

## Steps

1. Ask for the seeds: a few keywords, a page URL, or both, and the market (country, language).
2. Call `google_search_locations` for the market's location ID, and pick the language ID (1002 for French, 1000 for English).
3. Call `google_get_keyword_ideas` with `keyword_texts`, `url`, `language_id`, `location_ids` and `limit: 200`.
4. Call `google_list_keywords` with `limit: 1000` and remove ideas the account already has.
5. Call `google_get_search_terms_report` over `LAST_90_DAYS` with `limit: 1000`: terms that converted but are not keywords are the strongest candidates.
6. Call `google_list_keyword_plans` to reuse a plan already prepared, if any.
7. Group the remaining ideas into themes of close meaning, each a candidate ad group, and suggest a match type: exact for proven terms, phrase for themes.
8. Flag ideas that do not match the offer, competitor brands, and terms likely to bring the wrong intent (jobs, free, definitions).

## Output

1. **Summary**: number of ideas kept and their total monthly searches.
2. **Themes**: per theme, the keywords with average monthly searches, competition, top of page bid range and suggested match type.
3. **Proven terms**: converting search terms not yet keywords.
4. **Left out**: ideas excluded and why.

## Rules

- Keyword Planner volumes are rounded estimates: present them as such.
- Bids are in micros in the raw answer: divide by 1,000,000, and name the currency.
- Never add a competitor's brand as a keyword without the user's explicit choice.

## Examples

- "Find new keyword ideas for our running shoes campaign."
- "Trouve des mots-clés à ajouter à partir de la page produit, pour la France."
- "Quels termes de recherche convertissent sans être des mots-clés ?"
