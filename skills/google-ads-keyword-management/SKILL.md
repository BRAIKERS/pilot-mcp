---
name: google-ads-keyword-management
description: "Manages Google Ads keywords through the Pilot MCP server, after explicit approval of each write: reads keywords with their results, the search terms that convert and Keyword Planner ideas, then adds a keyword (paused by default) with its match type, pauses or enables a keyword, or changes its CPC bid or final URL, one write at a time, and reads the result back. Use when the user wants to add, pause or re-bid keywords. Triggers: add keywords, pause keyword, change keyword bid, manage keywords Google Ads. Déclencheurs : ajouter des mots-clés, mettre en pause un mot-clé, changer l'enchère d'un mot-clé, gérer les mots-clés Google Ads."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Ads account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_list_campaigns google_list_ad_groups google_list_keywords google_get_keyword_performance google_get_search_terms_report google_get_keyword_ideas google_add_keyword google_update_keyword"
  pilot-mcp-read-only: "false"
---

# Google Ads keyword management

Add, pause or re-bid keywords that the evidence supports, one approved write at a time. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a spreadsheet server to keep the change list. None is needed.
- **Writes:** the write tools require the paid Pilot MCP plan and a workspace role that can write. If a write tool is not served, say so and return the list of changes for a person to apply by hand in Google Ads.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_list_campaigns`: campaigns with their IDs.
- `google_list_ad_groups`: ad groups with their IDs.
- `google_list_keywords`: keywords of an ad group, with criterion IDs.
- `google_get_keyword_performance`: keyword metrics with impression share.
- `google_get_search_terms_report`: searches that triggered ads, with conversions.
- `google_get_keyword_ideas`: Keyword Planner ideas with volumes and bids.
- `google_add_keyword`: adds one keyword to an ad group (write).
- `google_update_keyword`: changes a keyword's status, CPC bid or final URLs (write).

## Confirmation

Every write changes a live account. For each one:

1. Read the current state just before writing: the ad group's keywords, or the keyword's status and bid.
2. Show the person the exact change: account, campaign, ad group (name and ID), keyword text and match type, current value and proposed value.
3. Wait for an explicit yes for this write. A yes for one write does not cover the next.
4. Make one write at a time.
5. Create every new keyword paused (`status: "PAUSED"`) unless the person asks for it enabled.
6. Never remove a keyword: pausing is the reversible way. Removing is out of this skill.
7. After the write, read the ad group's keywords again and show the before and the after.
8. Stop at the first write that is refused or fails, report it, and do not try a workaround.

## Steps

1. Call `google_list_accounts`, `google_list_campaigns` and `google_list_ad_groups` to locate the ad group.
2. Call `google_list_keywords` with `ad_group_id`, and `google_get_keyword_performance` over `LAST_30_DAYS` for their results.
3. To add: take candidates from `google_get_search_terms_report` (terms that convert and are not keywords) or from `google_get_keyword_ideas`. Check each is not already in the ad group or elsewhere in the account.
4. To pause: keep keywords that spent without conversions over a long enough period, and that are not the only way the ad group gets traffic.
5. To re-bid: on manual CPC only, propose a new bid from the keyword's cost per conversion and the target.
6. After approval, write: `google_add_keyword` with `ad_group_id`, `text`, `match_type` and `status: "PAUSED"`; or `google_update_keyword` with `ad_group_id`, `criterion_id` and `status`, `cpc_bid_micros` or `final_urls`.
7. Read back with `google_list_keywords` after each write.

## Output

1. **Proposed changes**: a table with ad group, keyword, match type, action, evidence and current and new value.
2. **Changes done**: before and after for each.
3. **Not done**: refused or skipped changes, as a list for a person.

## Rules

- Bids are in micros: 1.20 in the account currency is `1200000`.
- A CPC bid has no effect under automated bidding: say so instead of changing it.
- Keyword text is at most 80 characters.
- A paused keyword added today serves only once a person enables it: say so.

## Examples

- "Add the converting search terms as exact keywords in their ad groups."
- "Mets en pause les mots-clés qui ont dépensé sans convertir depuis trois mois."
- "Baisse l'enchère du mot-clé « chaussure running » à 0,80."
