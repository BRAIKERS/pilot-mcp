---
name: tiktok-ads-performance-report
description: "Builds a TikTok Ads performance report through the Pilot MCP server: spend, impressions, clicks, conversions, cost per conversion and video metrics over the period against the period before, results by campaign and ad group, the day-by-day trend and the ads that carried the results. Use when the user asks for a TikTok Ads report or last period's TikTok results. Triggers: TikTok ads report, TikTok results, TikTok campaign performance. Déclencheurs : rapport TikTok Ads, résultats TikTok, performance des campagnes TikTok, bilan TikTok."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a TikTok Ads advertiser attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "tiktok_get_advertiser_info tiktok_get_campaign_performance_summary tiktok_get_adgroup_performance tiktok_get_daily_performance_trend tiktok_get_ad_performance tiktok_get_basic_report"
  pilot-mcp-read-only: "true"
---

# TikTok Ads performance report

Report what a TikTok advertiser delivered over a period, what changed, and which campaigns and ads explain it. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the advertiser.
- **Optional MCP servers:** a file storage or slide server to keep the report. None is needed.
- **Read-only:** never change a campaign, an ad group or an ad.

## Tools

- `tiktok_get_advertiser_info`: the advertiser, its currency and time zone.
- `tiktok_get_campaign_performance_summary`: spend, conversions, CTR and CPC per campaign between two dates.
- `tiktok_get_adgroup_performance`: results per ad group of a campaign.
- `tiktok_get_daily_performance_trend`: day-by-day results of the advertiser or by campaign.
- `tiktok_get_ad_performance`: results per ad, including video plays.
- `tiktok_get_basic_report`: a custom report with chosen dimensions and metrics.

## Steps

1. Call `tiktok_get_advertiser_info`; note the currency and time zone.
2. Set the period: the last 30 days by default, and the period of the same length just before.
3. Call `tiktok_get_campaign_performance_summary` for each period.
4. Call `tiktok_get_daily_performance_trend` over the current period for the trend, with `data_level: "AUCTION_CAMPAIGN"` when campaigns need to be told apart.
5. Call `tiktok_get_adgroup_performance` with `campaign_id` for the campaigns that spend most.
6. Call `tiktok_get_ad_performance` over the period to name the ads that carry the results.
7. For metrics the summary does not give (reach, frequency, video watched to 100%, purchase value), call `tiktok_get_basic_report` with `data_level: "AUCTION_CAMPAIGN"`, `dimensions: ["campaign_id"]` and the metrics needed. Value and ROAS depend on the events the pixel sends: if they come back empty, say so.
8. Recompute CTR, CPC, CPM and cost per conversion from the totals.

## Output

1. **Headline**: one sentence on the period.
2. **Account**: a table with spend, impressions, clicks, conversions, cost per conversion and CTR for both periods and the change.
3. **Campaigns and ad groups**: the same, ranked by spend.
4. **Trend**: the daily curve and any break in it.
5. **Best ads**: those that carried the results, with their video metrics.
6. **To watch**: three points for a person.

## Rules

- Name both periods with their dates and the currency.
- A ratio with an empty denominator is not available, not zero.
- A metric TikTok did not return is absent, not zero.
- TikTok's conversions follow its own attribution: say so when comparing with other platforms.

## Examples

- "Give me the TikTok Ads report for last month."
- "Fais le bilan TikTok des 30 derniers jours, campagne par campagne."
- "Quelles vidéos TikTok ont porté les conversions ?"
