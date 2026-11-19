---
name: microsoft-ads-keywords-and-negatives-review
description: "Reviews the keywords and negative keywords of a Microsoft Advertising (Bing Ads) account through the Pilot MCP server: keywords per ad group with match type, bid and status, campaign and ad group negatives, conflicts between them, and the ad groups that spend most, then lists what to add, pause or exclude. Use when the user asks about Microsoft Ads keywords, match types or negatives. Triggers: Bing keywords review, Microsoft Ads negatives, match types Bing. Déclencheurs : mots-clés Microsoft Ads, mots-clés négatifs Bing, types de correspondance Bing, revue des mots-clés Bing."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Microsoft Advertising account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "bing_ads_get_ad_accounts bing_ads_get_campaign_performance bing_ads_get_ad_group_performance bing_ads_list_campaigns bing_ads_list_ad_groups bing_ads_list_keywords bing_ads_get_keyword_by_id bing_ads_list_negative_keywords"
  pilot-mcp-read-only: "true"
---

# Microsoft Ads keywords and negatives review

Tell the user how keywords and negatives are set up in Microsoft Advertising, where they conflict or leak, and what to change. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a spreadsheet server to export the keyword lists. None is needed.
- **Read-only:** never add, change or delete a keyword or a negative. The lists are for a person to apply.

## Tools

- `bing_ads_get_ad_accounts`: the accounts available, with their IDs.
- `bing_ads_get_campaign_performance`: cost and conversions per campaign.
- `bing_ads_get_ad_group_performance`: cost and conversions per ad group of one campaign.
- `bing_ads_list_campaigns`: campaigns with type and status.
- `bing_ads_list_ad_groups`: ad groups of a campaign.
- `bing_ads_list_keywords`: keywords of an ad group with match type, bid and status.
- `bing_ads_get_keyword_by_id`: one keyword in full, including its editorial status.
- `bing_ads_list_negative_keywords`: negatives of a campaign or of an ad group.

## Steps

1. Call `bing_ads_get_ad_accounts` and pick the account; use its ID as `target_account_id`.
2. Call `bing_ads_get_campaign_performance` with `date_range: "LastThirtyDays"` and keep the search campaigns that cost most (`bing_ads_list_campaigns` gives the type).
3. For each of them, call `bing_ads_get_ad_group_performance` and `bing_ads_list_ad_groups`, then `bing_ads_list_keywords` on the ad groups that cost most.
4. Read the keyword set: share of Broad, Phrase and Exact; the same keyword in several ad groups (internal competition); paused or disapproved keywords (check with `bing_ads_get_keyword_by_id`); bids far apart for similar keywords.
5. Call `bing_ads_list_negative_keywords` with `entity_type: "Campaign"`, then `"AdGroup"` (with its `campaign_id`). Flag a negative that blocks an active keyword of the same campaign, and search campaigns with Broad keywords and no negatives.
6. Ad group results give the cost and conversions behind each keyword set; per-keyword results and search terms are not served by these tools: say so, and suggest checking the search terms report in Microsoft Advertising.

## Output

1. **Summary**: match type mix, main conflicts, main leaks.
2. **Conflicts**: negatives that block active keywords, duplicated keywords across ad groups.
3. **Keywords to review**: paused, disapproved or oddly bid keywords, with their ad group.
4. **Negatives to add**: themes to exclude, per campaign, for a person to apply.
5. **Data limits**: what these tools cannot show.

## Rules

- Do not invent search terms: they are not available here.
- Judge ad groups on their own cost and conversions over the period, and name the period.
- Never sum amounts in different currencies.

## Examples

- "Review our Bing Ads keywords and negatives."
- "Y a-t-il des mots-clés négatifs qui bloquent nos mots-clés Microsoft Ads ?"
- "Quels groupes d'annonces Bing ont trop de requête large ?"
