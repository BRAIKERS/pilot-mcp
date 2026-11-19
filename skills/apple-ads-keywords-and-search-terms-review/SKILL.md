---
name: apple-ads-keywords-and-search-terms-review
description: "Reviews Apple Ads keywords and search terms through the Pilot MCP server: keyword results per campaign and ad group, the App Store searches that triggered the ads, terms that spend without installs, winning terms not yet added as exact keywords, and the negatives already in place. Use when the user asks which App Store searches bring installs, what to add as keywords, or what to exclude. Triggers: Apple Ads search terms, Apple Search Ads keywords, App Store search queries. Déclencheurs : termes de recherche Apple Ads, mots-clés Apple Search Ads, recherches App Store, mots-clés négatifs Apple."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with an Apple Ads organization attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "apple_ads_apple_ads_list_campaigns apple_ads_apple_ads_list_adgroups apple_ads_apple_ads_report_keywords_in_campaign apple_ads_apple_ads_report_keywords_in_adgroup apple_ads_apple_ads_report_search_terms_in_campaign apple_ads_apple_ads_report_search_terms_in_adgroup apple_ads_apple_ads_list_targeting_keywords apple_ads_apple_ads_list_campaign_negative_keywords apple_ads_apple_ads_list_adgroup_negative_keywords"
  pilot-mcp-read-only: "true"
---

# Apple Ads keywords and search terms review

Tell the user which App Store searches and keywords bring installs at a good cost, which ones waste money, and what to add or exclude. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Apple Ads organization.
- **Optional MCP servers:** a spreadsheet server to export the lists. None is needed.
- **Read-only:** never add, change or delete a keyword or a negative. The lists are for a person to apply.

## Tools

- `apple_ads_apple_ads_list_campaigns`: campaigns with countries and status.
- `apple_ads_apple_ads_list_adgroups`: ad groups of a campaign, with search match on or off.
- `apple_ads_apple_ads_report_keywords_in_campaign`: keyword results across a campaign.
- `apple_ads_apple_ads_report_keywords_in_adgroup`: keyword results within one ad group.
- `apple_ads_apple_ads_report_search_terms_in_campaign`: search term results across a campaign.
- `apple_ads_apple_ads_report_search_terms_in_adgroup`: search term results within one ad group.
- `apple_ads_apple_ads_list_targeting_keywords`: current keywords with match type and bid.
- `apple_ads_apple_ads_list_campaign_negative_keywords`: campaign negatives.
- `apple_ads_apple_ads_list_adgroup_negative_keywords`: ad group negatives.

## Steps

1. Call `apple_ads_apple_ads_list_campaigns` and pick the campaigns to review (all active ones by default). Ask for the target cost per install if the user has one.
2. For each campaign, call `apple_ads_apple_ads_report_search_terms_in_campaign` over the last 30 days with `granularity: "MONTHLY"` or a single range. Apple hides terms with very low volume: say so.
3. Call `apple_ads_apple_ads_report_keywords_in_campaign` for the same period, then `apple_ads_apple_ads_report_keywords_in_adgroup` and `apple_ads_apple_ads_report_search_terms_in_adgroup` where one ad group needs a closer look.
4. Call `apple_ads_apple_ads_list_adgroups` (search match on or off), `apple_ads_apple_ads_list_targeting_keywords`, `apple_ads_apple_ads_list_campaign_negative_keywords` and `apple_ads_apple_ads_list_adgroup_negative_keywords` to know what is already in place.
5. Sort the search terms: winners (installs at or under the target cost) not yet exact keywords; losers (spend with no or few installs) not yet negatives; terms matched in several campaigns that compete with each other.
6. Sort keywords: bid far above the average cost per tap paid, keywords with impressions but almost no taps (low relevance), keywords with no impressions at all.

## Output

1. **Summary**: share of spend on winning, neutral and losing terms.
2. **Add as exact keywords**: term, campaign, installs, cost per install.
3. **Add as negatives**: term, where to exclude it, spend and installs.
4. **Keywords to review**: bid, relevance or delivery issues.
5. **Competition between campaigns**: terms served by several campaigns.

## Rules

- Never sum amounts of campaigns in different currencies.
- Do not judge a term on one or two installs: say the volume is too low.
- A cost per install with zero installs is not available, not zero.

## Examples

- "Which App Store searches bring us installs?"
- "Quels termes de recherche Apple Ads faut-il exclure ?"
- "Trouve les termes à ajouter en mots-clés exacts dans Apple Search Ads."
