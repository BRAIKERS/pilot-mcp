---
name: openai-ads-performance-report
description: "Builds an OpenAI Ads (ChatGPT ads) performance report through the Pilot MCP server: impressions, clicks, spend, click-through rate, cost per click and per thousand impressions by campaign, ad group and ad, by day or month, by country or device, with attributed conversions and the change against the previous period. Use when the user wants OpenAI Ads numbers for a week or a month. Triggers: OpenAI Ads report, ChatGPT ads performance, OpenAI ads results. Déclencheurs : rapport OpenAI Ads, performance des publicités ChatGPT, résultats OpenAI Ads du mois."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with an OpenAI Ads account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "openai_ads_openai_ads_get_ad_account openai_ads_openai_ads_list_campaigns openai_ads_openai_ads_account_insights openai_ads_openai_ads_campaign_insights openai_ads_openai_ads_ad_group_insights openai_ads_openai_ads_ad_insights openai_ads_openai_ads_conversion_insights"
  pilot-mcp-read-only: "true"
---

# OpenAI Ads performance report

Give the user a clear OpenAI Ads report for the period asked: delivery, cost and conversions, by campaign, ad group and ad. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the OpenAI Ads account.
- **Optional MCP servers:** a spreadsheet server to export the tables. None is needed.
- **Read-only:** never change a campaign, ad group or ad.

## Tools

- `openai_ads_openai_ads_get_ad_account`: currency and time zone.
- `openai_ads_openai_ads_list_campaigns`: campaign names, bidding type and status.
- `openai_ads_openai_ads_account_insights`: delivery metrics for the account, by campaign, ad group or ad rows.
- `openai_ads_openai_ads_campaign_insights`: delivery metrics for one campaign.
- `openai_ads_openai_ads_ad_group_insights`: delivery metrics for one ad group.
- `openai_ads_openai_ads_ad_insights`: delivery metrics for one ad.
- `openai_ads_openai_ads_conversion_insights`: attributed conversions and sales per campaign, ad group or ad.

## Steps

1. Call `openai_ads_openai_ads_get_ad_account` for the currency and time zone, and `openai_ads_openai_ads_list_campaigns` to name rows.
2. Fix the period and a comparison period of the same length. Each insights call takes ONE range in `time_ranges`: call once per period.
3. Call `openai_ads_openai_ads_account_insights` with `aggregation_level: "campaign"`, `time_granularity: "none"`, `fields` such as impressions, clicks, spend, ctr, cpc, cpm, and `sort` on spend. Repeat with `time_granularity: "daily"` for the trend, and with `segments` set to country or device when the user wants the split.
4. Drill down with `openai_ads_openai_ads_campaign_insights`, `openai_ads_openai_ads_ad_group_insights` and `openai_ads_openai_ads_ad_insights` for the objects that weigh most.
5. Call `openai_ads_openai_ads_conversion_insights` with `aggregation_level: "campaign"` (then `"ad_group"` or `"ad"`), the same range, and the attribution windows the account uses. Delivery insights carry no conversions: join the two by entity.
6. Recompute cost per conversion on the totals.

## Output

1. **Headline**: spend, clicks, conversions and cost per conversion, with the change.
2. **Trend**: a short table by day.
3. **Campaigns**: a table ranked by spend with clicks, click-through rate and conversions.
4. **Ad groups and ads**: the best and weakest.
5. **What to watch**: two or three points.

## Rules

- Click-through rate comes as a ratio (0.04 means 4 %): show it as a percentage.
- Name the period, the time zone, the attribution windows and the currency.
- A ratio with an empty denominator is not available, not zero.

## Examples

- "Give me last month's OpenAI Ads report."
- "Fais le rapport des publicités ChatGPT de la semaine, par campagne."
- "Combien de conversions OpenAI Ads ce mois-ci, et à quel coût ?"
