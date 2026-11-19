---
name: snapchat-ads-performance-report
description: "Builds a Snapchat Ads performance report through the Pilot MCP server: daily account spend from Snapchat, then campaign results (impressions, swipes, conversions) from the data Pilot has collected, with the change against the previous period. Use when the user wants Snapchat numbers, a weekly or monthly Snapchat report, or to know where the Snapchat budget goes. Triggers: Snapchat Ads report, Snapchat performance, Snap spend this month. Déclencheurs : rapport Snapchat Ads, performance Snapchat, dépense Snapchat du mois, résultats Snap."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Snapchat ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "search fetch snapchat_get_campaign snapchat_get_ad_account snapchat_get_ad_account_stats snapchat_list_campaigns pilot_cross_platform_overview pilot_data_freshness"
  pilot-mcp-read-only: "true"
---

# Snapchat Ads performance report

Give the user a Snapchat Ads report for the period asked: spend trend from Snapchat, and campaign results from what Pilot has collected. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Snapchat ad account.
- **Optional MCP servers:** a spreadsheet server to export the tables. None is needed.
- **Read-only:** never change a campaign, ad squad or ad.

## Tools

- `search`: finds campaigns of the workspace by name, with their Pilot IDs.
- `fetch`: one campaign as Pilot holds it, with its recent metrics.
- `snapchat_get_campaign`: one campaign in Snapchat, including its ad account ID.
- `snapchat_get_ad_account`: the account currency and time zone.
- `snapchat_get_ad_account_stats`: account spend by day or hour over a time range.
- `snapchat_list_campaigns`: campaigns with objective, status and budget.
- `pilot_cross_platform_overview`: totals per platform from Pilot's collected data, with recomputed ratios.
- `pilot_data_freshness`: how current Pilot's Snapchat data is.

## Steps

1. Get the ad account ID from the user, or from `snapchat_get_campaign` on a campaign found with `search`. Call `snapchat_get_ad_account` for the currency and time zone.
2. Fix the period and a comparison period of the same length.
3. Call `snapchat_get_ad_account_stats` for both periods with `granularity: "DAY"`. This gives spend only: Snapchat does not return impressions, swipes or conversions at the account level.
4. Call `pilot_data_freshness` with `platform: "snapchat"`. If Pilot holds current data, call `pilot_cross_platform_overview` with `platform: "snapchat"` and the number of days of the period, for impressions, swipes counted as clicks, conversions and ratios.
5. Call `snapchat_list_campaigns` to name the campaigns. For the campaigns that matter, call `search` with their name and `fetch` on the result to read their recent metrics.
6. If Pilot holds no Snapchat data, report spend only and say that results per campaign are not available through these tools.

## Output

1. **Headline**: spend for the period and its change, then results and cost per result when available.
2. **Daily spend**: a short table or a sentence on the shape of the curve.
3. **Campaigns**: a table with objective, status, budget, and results when Pilot holds them.
4. **Data limits**: what comes from Snapchat, what comes from Pilot, and how fresh it is.
5. **What to watch**: two or three points.

## Rules

- Divide micro amounts by 1,000,000 and name the currency.
- Days follow the account time zone: say which one.
- A result Pilot did not collect is absent, not zero.

## Examples

- "How did Snapchat do last month?"
- "Fais le rapport Snapchat de la semaine avec la dépense par jour."
- "Combien a-t-on dépensé sur Snap ce mois-ci, et pour quels résultats ?"
