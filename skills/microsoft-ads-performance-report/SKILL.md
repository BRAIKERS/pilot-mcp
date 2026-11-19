---
name: microsoft-ads-performance-report
description: "Builds a Microsoft Advertising (Bing Ads) performance report through the Pilot MCP server: account totals, results by campaign and ad group with impressions, clicks, cost, conversions and cost per conversion, the change against the previous period, and the share of Microsoft Ads in total paid search. Use when the user wants Microsoft Ads or Bing numbers for a week or a month. Triggers: Microsoft Ads report, Bing Ads performance, Bing results this month. Déclencheurs : rapport Microsoft Ads, performance Bing Ads, résultats Bing du mois, rapport Microsoft Advertising."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Microsoft Advertising account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "bing_ads_get_ad_accounts bing_ads_get_account_info bing_ads_get_campaign_performance bing_ads_get_ad_group_performance bing_ads_list_campaigns pilot_cross_platform_overview"
  pilot-mcp-read-only: "true"
---

# Microsoft Ads performance report

Give the user a clear Microsoft Advertising report for the period asked: totals, campaigns, ad groups and how Microsoft Ads compares with the other platforms. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a spreadsheet server to export the tables. None is needed.
- **Read-only:** never change a campaign, ad group, ad or keyword.

## Tools

- `bing_ads_get_ad_accounts`: the accounts available, with their IDs.
- `bing_ads_get_account_info`: currency and time zone.
- `bing_ads_get_campaign_performance`: impressions, clicks, cost and conversions per campaign.
- `bing_ads_get_ad_group_performance`: the same per ad group of one campaign.
- `bing_ads_list_campaigns`: campaign names, types and status.
- `pilot_cross_platform_overview`: spend and results per platform from Pilot's collected data.

## Steps

1. Call `bing_ads_get_ad_accounts` and pick the account; call `bing_ads_get_account_info` for the currency.
2. Pick the period among the values the tools accept: `LastSevenDays`, `LastThirtyDays`, `LastMonth`, `ThisMonth`, `Yesterday`, `Today`. For a comparison, use `LastMonth` against `ThisMonth` pro rata, or say plainly that no earlier period is available through these tools.
3. Call `bing_ads_get_campaign_performance` with the chosen `date_range`. Sum the account totals from the campaign rows and recompute ratios on the totals.
4. Call `bing_ads_list_campaigns` to name campaigns and their type.
5. For the campaigns that weigh most, call `bing_ads_get_ad_group_performance` to show which ad groups carry results.
6. Call `pilot_cross_platform_overview` over the same number of days to place Microsoft Ads among the other platforms (share of spend and of conversions). It refuses a total across currencies: keep it that way.

## Output

1. **Headline**: cost, clicks, conversions and cost per conversion for the period.
2. **Campaigns**: a table ranked by cost with clicks, conversions and cost per conversion.
3. **Ad groups**: the best and weakest of the main campaigns.
4. **Place in the mix**: share of Microsoft Ads in spend and conversions.
5. **What to watch**: two or three points.

## Rules

- Name the period and the currency.
- A ratio with an empty denominator is not available, not zero.
- Recompute ratios on totals; never average them.

## Examples

- "Give me last month's Microsoft Ads report."
- "Fais le rapport Bing Ads des 30 derniers jours, par campagne."
- "Quelle part de nos conversions vient de Microsoft Ads ?"
