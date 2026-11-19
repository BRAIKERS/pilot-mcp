---
name: pinterest-ads-performance-report
description: "Builds a Pinterest Ads performance report through the Pilot MCP server: account totals, trend by day or week, results by campaign, ad group and ad, and a breakdown by age, gender, location or interest, for any period. Use when the user wants Pinterest numbers, a weekly or monthly Pinterest report, or to know which campaigns and Pins perform. Triggers: Pinterest Ads report, Pinterest performance, best Pinterest ads. Déclencheurs : rapport Pinterest Ads, performance Pinterest, résultats Pinterest du mois, meilleures épingles sponsorisées."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Pinterest ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "pinterest_ad_accounts_list pinterest_ad_accounts_get pinterest_resources_delivery_metrics pinterest_ad_accounts_analytics pinterest_campaigns_list pinterest_campaigns_analytics pinterest_ad_groups_analytics pinterest_ads_analytics pinterest_campaigns_targeting_analytics"
  pilot-mcp-read-only: "true"
---

# Pinterest Ads performance report

Give the user a clear Pinterest Ads report for the period asked: totals, trend, what works and what does not. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Pinterest ad account.
- **Optional MCP servers:** a spreadsheet server to export the tables, a team messaging server to send the report. None is needed.
- **Read-only:** never change a campaign, ad group or ad.

## Tools

- `pinterest_ad_accounts_list`: the ad accounts available, with their IDs.
- `pinterest_ad_accounts_get`: the account currency and settings.
- `pinterest_resources_delivery_metrics`: the metric columns Pinterest accepts, with their definitions.
- `pinterest_ad_accounts_analytics`: account totals, by day, week or month with `granularity`.
- `pinterest_campaigns_list`: campaign names, objectives and status.
- `pinterest_campaigns_analytics`: metrics per campaign.
- `pinterest_ad_groups_analytics`: metrics per ad group.
- `pinterest_ads_analytics`: metrics per ad.
- `pinterest_campaigns_targeting_analytics`: metrics broken down by age, gender, location, interest, keyword or audience.

## Steps

1. Call `pinterest_ad_accounts_list` and pick the account (ask if several). Call `pinterest_ad_accounts_get` for the currency.
2. Fix the period with the user (last 7 days, last month, a custom range) and a comparison period of the same length. Dates are YYYY-MM-DD.
3. If unsure which columns exist, call `pinterest_resources_delivery_metrics`. A common set: `SPEND_IN_DOLLAR`, `IMPRESSION_1`, `CLICK_1`, `CTR`, `CPC_IN_MICRO_DOLLAR`, `TOTAL_CONVERSIONS`, `TOTAL_CHECKOUT`, `TOTAL_CHECKOUT_VALUE_IN_MICRO_DOLLAR`. Divide micro amounts by 1,000,000.
4. Call `pinterest_ad_accounts_analytics` for both periods with `granularity: "TOTAL"`, then with `"DAY"` or `"WEEK"` for the trend.
5. Call `pinterest_campaigns_list`, then `pinterest_campaigns_analytics` to rank campaigns by spend and by result.
6. Drill down where it matters: `pinterest_ad_groups_analytics` for the top campaigns, `pinterest_ads_analytics` to find the best and weakest ads.
7. Call `pinterest_campaigns_targeting_analytics` with `targeting_types` such as `["AGE_BUCKET","GENDER","GEO"]` to show who converts.
8. Keep the attribution windows Pinterest used (`click_window_days`, `view_window_days`) the same for both periods, and state them.

## Output

1. **Headline**: spend, results and cost per result for the period, with the change against the comparison period.
2. **Trend**: a short table by day or week.
3. **Campaigns**: a table ranked by spend, with results and cost per result.
4. **Ads**: the three best and the three weakest, with their figures.
5. **Audience**: the segments that convert best and worst.
6. **What to watch**: two or three points for the next period.

## Rules

- Name the period, the attribution windows and the currency of every amount.
- A ratio with an empty denominator is not available, not zero.
- Compare periods of the same length only.

## Examples

- "Give me last month's Pinterest Ads report."
- "Fais le rapport Pinterest de la semaine, comparé à la semaine précédente."
- "Quelles épingles sponsorisées rapportent le plus de ventes ?"
