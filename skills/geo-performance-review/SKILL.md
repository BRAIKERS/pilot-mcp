---
name: geo-performance-review
description: "Reviews advertising performance by country and region through the Pilot MCP server: spend, conversions, CPA and ROAS per location on Google Ads, Meta Ads and TikTok Ads, where people really were against the locations targeted, and the site traffic per country in Google Analytics 4, then lists the places to push, cut or exclude. Use when the user asks which countries or regions perform, where to expand, or whether ads show outside the target area. Triggers: geo performance, performance by country, location report, which regions work. Déclencheurs : performance par pays, performance géographique, quelles régions marchent, rapport par zone, ciblage géographique."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with Google Ads, Meta Ads or TikTok Ads attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_get_geographic_performance google_get_user_location_view google_list_campaign_location_targets meta_list_accounts meta_get_account_insights tiktok_get_advertiser_info tiktok_get_geo_performance google_analytics_run_report"
  pilot-mcp-read-only: "true"
---

# Geo performance review

Show which places bring results, which only cost, and whether ads reach people outside the intended area. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace concerned.
- **Optional MCP servers:** a map or chart server to draw the results. None is needed.
- **Read-only:** never change targeting or bids. Proposals are for a person to apply.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_get_geographic_performance`: cost and results by country and location type.
- `google_get_user_location_view`: results by where people physically were.
- `google_list_campaign_location_targets`: the locations each campaign targets or excludes.
- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_get_account_insights`: Meta results split by `country` or `region`.
- `tiktok_get_advertiser_info`: the TikTok advertiser and its currency.
- `tiktok_get_geo_performance`: TikTok results by country or region.
- `google_analytics_run_report`: sessions, key events and revenue by country in GA4.

## Steps

1. Set the period: the last 30 days by default, 90 days for small accounts.
2. Google Ads: `google_list_accounts`, then `google_get_geographic_performance` and `google_get_user_location_view` over the period, and `google_list_campaign_location_targets`. Compare where people were with what is targeted: spend from outside the target area points to a location option that also reaches people interested in the area.
3. Meta Ads: `meta_list_accounts`, then `meta_get_account_insights` with `level: "campaign"`, `date_preset: "last_30d"` and `breakdowns: ["country"]`; use `breakdowns: ["region"]` for a single-country account.
4. TikTok Ads, if attached: `tiktok_get_advertiser_info`, then `tiktok_get_geo_performance` over the period.
5. GA4: `google_analytics_run_report` with `dimensions: ["country"]` and `metrics: ["sessions", "keyEvents", "totalRevenue"]`.
6. Rank locations by spend, then compare their CPA or ROAS with the account average. Leave out places with too few conversions to judge.

## Output

1. **Summary**: the places that carry the results and those that only cost.
2. **By location**: a table with platform, location, spend, conversions, CPA, ROAS and share of spend.
3. **Outside the target**: spend from places not targeted, with the campaigns concerned.
4. **Proposals**: places to push, lower or exclude, for a person to apply.

## Rules

- Never sum amounts in different currencies.
- A location with a handful of conversions is not judged: say the volume is too low.
- Platforms do not cut locations the same way: compare countries across platforms, not regions.
- Name the period of each figure.

## Examples

- "Which countries perform best across our ad platforms?"
- "Quelles régions coûtent sans convertir sur Google Ads et Meta ?"
- "Nos annonces sortent-elles de la zone ciblée ?"
