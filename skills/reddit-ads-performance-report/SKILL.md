---
name: reddit-ads-performance-report
description: "Builds a Reddit Ads performance report through the Pilot MCP server: account totals, daily trend, results by campaign, ad group and ad, with impressions, clicks, spend, key conversions by click and by view, purchase value and the change against the previous period. Use when the user wants Reddit numbers, a weekly or monthly Reddit report, or to know which ads convert. Triggers: Reddit Ads report, Reddit performance, Reddit conversions. Déclencheurs : rapport Reddit Ads, performance Reddit, conversions Reddit, résultats Reddit du mois."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Reddit Ads account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "reddit_ads_get_ad_account reddit_ads_get_statistics reddit_ads_list_campaigns reddit_ads_list_ad_groups reddit_ads_list_ads"
  pilot-mcp-read-only: "true"
---

# Reddit Ads performance report

Give the user a clear Reddit Ads report for the period asked: totals, trend, and which campaigns, ad groups and ads bring results. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Reddit Ads account.
- **Optional MCP servers:** a spreadsheet server to export the tables. None is needed.
- **Read-only:** never change a campaign, ad group or ad.

## Tools

- `reddit_ads_get_ad_account`: currency, time zone and attribution windows.
- `reddit_ads_get_statistics`: metrics by level (ACCOUNT, CAMPAIGN, AD_GROUP, AD), per day or in total.
- `reddit_ads_list_campaigns`: campaign names, objectives and status.
- `reddit_ads_list_ad_groups`: ad group names, budgets and bids.
- `reddit_ads_list_ads`: ad names, posts and destinations.

## Steps

1. Call `reddit_ads_get_ad_account` for the currency, time zone and attribution windows.
2. Fix the period and a comparison period of the same length. Each call covers 92 days at most: split longer ranges.
3. Call `reddit_ads_get_statistics` with `level: "ACCOUNT"` and `granularity: "TOTAL"` for both periods, then `granularity: "DAY"` for the trend.
4. Call it with `level: "CAMPAIGN"`, `granularity: "TOTAL"`, and name the rows with `reddit_ads_list_campaigns`.
5. Drill into the campaigns that matter: `level: "AD_GROUP"` with their IDs in `ids`, then `level: "AD"`. Name the rows with `reddit_ads_list_ad_groups` and `reddit_ads_list_ads`.
6. Keep click and view conversions apart, and show purchase value when the account tracks it.

## Output

1. **Headline**: spend, clicks, key conversions and cost per conversion, with the change against the comparison period.
2. **Trend**: a short table or sentence on the daily curve.
3. **Campaigns**: a table ranked by spend with clicks, conversions and cost per conversion.
4. **Ads**: the best and weakest, with their figures.
5. **What to watch**: two or three points.

## Rules

- Name the period, the time zone, the attribution windows and the currency.
- A ratio with an empty denominator is not available, not zero.
- Never add click and view conversions without saying so.

## Examples

- "Give me last week's Reddit Ads report."
- "Fais le rapport Reddit du mois avec les meilleures annonces."
- "Combien de conversions Reddit avons-nous eues ce trimestre, et à quel coût ?"
