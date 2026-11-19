---
name: channel-mix-review
description: "Reviews the marketing channel mix through the Pilot MCP server: how spend and results split between Google Ads, Meta Ads and TikTok Ads, between campaign types (Search, Shopping, Performance Max, video) and between placements (Facebook, Instagram, Audience Network), set against the organic and direct traffic seen in Google Analytics 4. Use when the user asks whether the mix is right, which channel to grow, or how dependent the business is on paid traffic. Triggers: channel mix, media mix, split between platforms, paid versus organic. Déclencheurs : mix de canaux, mix média, répartition entre régies, payant contre organique, quel canal développer."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with at least one ad account attached. Google Analytics 4 is recommended."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "pilot_cross_platform_overview pilot_account_concentration google_list_accounts google_list_campaigns google_get_campaign_performance meta_list_accounts meta_get_account_insights tiktok_get_advertiser_info tiktok_get_campaign_performance_summary google_analytics_run_report"
  pilot-mcp-read-only: "true"
---

# Channel mix review

Show how money and results split between channels, where the return differs, and what shift is worth testing. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace concerned.
- **Optional MCP servers:** a chart or spreadsheet server to draw the mix. None is needed.
- **Read-only:** never change a campaign or a budget. Shifts are proposals for a person.

## Tools

- `pilot_cross_platform_overview`: spend and results per platform, and organic traffic with `include_organic: true`.
- `pilot_account_concentration`: how many campaigns carry most of the spend.
- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_list_campaigns`: campaigns with their channel type (Search, Shopping, Performance Max, Display, Video).
- `google_get_campaign_performance`: cost, conversions and value per campaign.
- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_get_account_insights`: Meta results, split by placement with `breakdowns: ["publisher_platform"]`.
- `tiktok_get_advertiser_info`: the TikTok advertiser and its currency.
- `tiktok_get_campaign_performance_summary`: spend and conversions per TikTok campaign.
- `google_analytics_run_report`: sessions, key events and revenue by channel group in GA4.

## Steps

1. Set the period: the last 90 days by default, long enough to smooth weekly swings.
2. Call `pilot_cross_platform_overview` with `days: 90` and `include_organic: true`.
3. Google Ads: `google_list_campaigns` to get each campaign's channel type, then `google_get_campaign_performance` over the period (with `start_date` and `end_date`, `limit: 1000`), summed per channel type.
4. Meta Ads: `meta_get_account_insights` with `level: "account"`, the period in `time_range` and `breakdowns: ["publisher_platform"]`.
5. TikTok Ads, if attached: `tiktok_get_advertiser_info`, then `tiktok_get_campaign_performance_summary` over the period.
6. GA4: `google_analytics_run_report` with `dimensions: ["sessionDefaultChannelGroup"]` and `metrics: ["sessions", "keyEvents", "totalRevenue"]` over the period.
7. Call `pilot_account_concentration` with `days: 90` to see whether a channel rests on one campaign.
8. Compare each channel's share of spend with its share of conversions and value. A channel whose share of results is well above its share of spend is a candidate to grow, if its campaigns are not already capped.

## Output

1. **Mix today**: a table with channel, spend, share of spend, conversions, share of conversions, CPA and ROAS.
2. **Paid and organic**: the weight of paid channels in sessions and key events, against organic, direct and referral.
3. **Imbalances**: channels whose return differs most from their weight.
4. **Shifts to test**: at most three, each with the evidence, for a person to decide.

## Rules

- Platform conversions and GA4 key events do not count the same thing: never mix them in one column.
- Never sum amounts in different currencies.
- Name the period and the attribution each figure comes from.
- A channel that feeds others (video, upper funnel) is not judged on last-click conversions alone: say so.

## Examples

- "Is our channel mix right, or are we too dependent on Google?"
- "Comment se répartissent la dépense et les résultats entre Google, Meta et TikTok ?"
- "Quelle part de nos ventes vient du payant contre l'organique ?"
