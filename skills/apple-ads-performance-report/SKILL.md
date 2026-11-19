---
name: apple-ads-performance-report
description: "Builds an Apple Ads performance report through the Pilot MCP server: spend, impressions, taps, installs, tap-through rate, conversion rate, cost per tap and cost per install by campaign, ad group and ad, by country or device, day by day or month by month, with the change against the previous period. Use when the user wants Apple Ads or Apple Search Ads numbers. Triggers: Apple Ads report, Apple Search Ads performance, cost per install Apple. Déclencheurs : rapport Apple Ads, performance Apple Search Ads, coût par installation Apple, installations App Store."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with an Apple Ads organization attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "apple_ads_apple_ads_list_campaigns apple_ads_apple_ads_get_app apple_ads_apple_ads_report_campaigns apple_ads_apple_ads_report_adgroups apple_ads_apple_ads_report_ads_in_campaign apple_ads_apple_ads_list_adgroups"
  pilot-mcp-read-only: "true"
---

# Apple Ads performance report

Give the user a clear Apple Ads report for the period asked: totals per currency, trend, campaigns, ad groups and ads. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Apple Ads organization.
- **Optional MCP servers:** a spreadsheet server to export the tables. None is needed.
- **Read-only:** never change a campaign, ad group, keyword or ad.

## Tools

- `apple_ads_apple_ads_list_campaigns`: campaign names, apps, countries and status.
- `apple_ads_apple_ads_get_app`: the name of each promoted app.
- `apple_ads_apple_ads_report_campaigns`: results per campaign, by day, week or month, optionally grouped by country or device.
- `apple_ads_apple_ads_report_adgroups`: results per ad group of a campaign.
- `apple_ads_apple_ads_report_ads_in_campaign`: results per ad (default or custom product page) of a campaign.
- `apple_ads_apple_ads_list_adgroups`: ad group names.

## Steps

1. Call `apple_ads_apple_ads_list_campaigns` and `apple_ads_apple_ads_get_app` to name campaigns and apps.
2. Fix the period and a comparison period of the same length, as `startTime` and `endTime` in YYYY-MM-DD.
3. Call `apple_ads_apple_ads_report_campaigns` for both periods. Use `granularity` DAILY, WEEKLY or MONTHLY for the trend, and `groupBy` with `countryOrRegion` or `deviceClass` when the user wants that split.
4. Group totals by currency: campaigns in different countries may spend in different currencies.
5. For the campaigns that weigh most, call `apple_ads_apple_ads_report_adgroups` (name rows with `apple_ads_apple_ads_list_adgroups`) and `apple_ads_apple_ads_report_ads_in_campaign` to compare the default product page with custom product pages.
6. Recompute tap-through rate, conversion rate, cost per tap and cost per install on the totals.

## Output

1. **Headline**: spend, taps, installs and cost per install, per currency, with the change.
2. **Trend**: a short table by day, week or month.
3. **Campaigns**: a table ranked by spend, with country, installs and cost per install.
4. **Ad groups and ads**: the best and weakest, and how custom product pages compare.
5. **What to watch**: two or three points.

## Rules

- Never sum amounts in different currencies.
- A ratio with an empty denominator is not available, not zero.
- Name the period and say that installs follow Apple's attribution.

## Examples

- "Give me the Apple Ads report for last month."
- "Fais le rapport Apple Search Ads de la semaine, par pays."
- "Quel est notre coût par installation sur Apple Ads ce trimestre ?"
