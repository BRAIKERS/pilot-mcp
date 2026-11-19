---
name: amazon-ads-performance-report
description: "Builds an Amazon Ads performance report through the Pilot MCP server from Amazon's own campaign reports: spend, impressions, clicks, purchases, sales, ACOS and ROAS for Sponsored Products, Sponsored Brands and Sponsored Display, by campaign and by day, with the change against the previous period. Use when the user wants Amazon Ads numbers for a week, a month or a sales event. Triggers: Amazon Ads report, Amazon ACOS this month, Amazon PPC performance. Déclencheurs : rapport Amazon Ads, ACOS du mois, performance PPC Amazon, ventes Amazon Ads."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with an Amazon Ads profile attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "amazon_ads_list_profiles amazon_ads_get_profile amazon_ads_create_report amazon_ads_get_report amazon_ads_sp_list_campaigns amazon_ads_sb_list_campaigns amazon_ads_sd_list_campaigns pilot_cross_platform_overview"
  pilot-mcp-read-only: "true"
---

# Amazon Ads performance report

Give the user a clear Amazon Ads report for the period asked, program by program and campaign by campaign. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Amazon Ads profile.
- **Optional MCP servers:** a spreadsheet server to export the tables. None is needed.
- **Read-only:** never change a campaign. Requesting a report changes nothing in the account.

## Tools

- `amazon_ads_list_profiles`: the profiles available, one per marketplace.
- `amazon_ads_get_profile`: the profile's marketplace, currency and time zone.
- `amazon_ads_create_report`: requests a campaign report for a program and a period.
- `amazon_ads_get_report`: polls the report and returns its rows.
- `amazon_ads_sp_list_campaigns`: Sponsored Products campaign names and states.
- `amazon_ads_sb_list_campaigns`: Sponsored Brands campaign names and states.
- `amazon_ads_sd_list_campaigns`: Sponsored Display campaign names and states.
- `pilot_cross_platform_overview`: Amazon Ads next to the other platforms, from Pilot's collected data.

## Steps

1. Call `amazon_ads_list_profiles` and pick the profile; call `amazon_ads_get_profile` for the currency and time zone.
2. Fix the period and a comparison period of the same length.
3. For each program in use, call `amazon_ads_create_report` twice (period and comparison): `SPONSORED_PRODUCTS` with `spCampaigns`, `SPONSORED_BRANDS` with `sbCampaigns`, `SPONSORED_DISPLAY` with `sdCampaigns`, `group_by: ["campaign"]`, and `time_unit: "DAILY"` when the trend matters. Ask for impressions, clicks, cost, and the purchases and sales columns of each program.
4. Poll every report with `amazon_ads_get_report` and `download: true` until COMPLETED; tell the user when it takes time.
5. Name and filter rows with `amazon_ads_sp_list_campaigns`, `amazon_ads_sb_list_campaigns` and `amazon_ads_sd_list_campaigns`.
6. Recompute ACOS, ROAS, click-through rate and cost per click on the totals.
7. Call `pilot_cross_platform_overview` over the same number of days to show the weight of Amazon Ads among the platforms.

## Output

1. **Headline**: spend, sales, ACOS and ROAS for the period, with the change.
2. **By program**: Sponsored Products, Brands and Display side by side.
3. **Campaigns**: a table ranked by spend with sales and ACOS.
4. **Trend**: the daily curve when requested.
5. **What to watch**: two or three points.

## Rules

- Name the period, the time zone, the currency and the attribution window of the sales columns.
- ACOS with zero sales is not available, not zero.
- Never sum profiles in different currencies.

## Examples

- "Give me last month's Amazon Ads report."
- "Fais le rapport Amazon Ads de la semaine du Prime Day."
- "Quel est notre ACOS par programme ce mois-ci ?"
