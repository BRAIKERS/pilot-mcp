---
name: wasted-spend-review
description: "Finds advertising money that brings nothing through the Pilot MCP server, on Google Ads, Meta Ads and TikTok Ads: campaigns that spend without converting, campaigns far above the median cost per acquisition, then the search terms, locations and ads that carry the waste, with the amount at stake. Use when the user asks where money is wasted, what to cut or how to lower the cost per acquisition. Triggers: wasted spend, what should we cut, spend without conversions, money leaks. Déclencheurs : dépense gaspillée, budget perdu, campagnes sans conversion, où couper, fuites de budget."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with Google Ads, Meta Ads or TikTok Ads attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "pilot_data_freshness pilot_diagnose_wasted_spend google_list_accounts google_get_campaign_performance google_get_search_terms_report google_get_location_performance google_get_ad_performance meta_list_accounts meta_get_account_insights meta_get_worst_performing_ads tiktok_get_advertiser_info tiktok_get_campaign_performance_summary tiktok_get_ad_performance"
  pilot-mcp-read-only: "true"
---

# Wasted spend review

Show where money is spent without results, how much is at stake, and what a person could cut or rework first. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace concerned.
- **Optional MCP servers:** a spreadsheet server to keep the list of cuts, a team messaging server to share it. None is needed.
- **Read-only:** never pause, exclude or lower anything. The list of cuts is for a person to apply.

## Tools

- `pilot_data_freshness`: whether conversions are collected, so that a missing conversion is not read as waste.
- `pilot_diagnose_wasted_spend`: campaigns that spend without converting, or whose CPA is above a threshold.
- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_get_campaign_performance`: cost and conversions per campaign and day.
- `google_get_search_terms_report`: the queries that triggered the ads, by cost.
- `google_get_location_performance`: cost and results by location.
- `google_get_ad_performance`: cost and conversions per ad.
- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_get_account_insights`: Meta results by campaign, ad set or ad.
- `meta_get_worst_performing_ads`: the weakest Meta ads on a metric.
- `tiktok_get_advertiser_info`: the TikTok advertiser and its currency.
- `tiktok_get_campaign_performance_summary`: spend and conversions per TikTok campaign.
- `tiktok_get_ad_performance`: spend and conversions per TikTok ad.

## Steps

1. Set the period: the last 30 days by default. Ask for a target CPA if the user has one.
2. Call `pilot_data_freshness`. On a platform whose conversions are not collected, do not call anything waste.
3. Call `pilot_diagnose_wasted_spend` with `days: 30`, and `max_cpa` if the user gave a target. Otherwise keep the default threshold, twice the median CPA, and say so.
4. Google Ads, for each flagged campaign: `google_get_search_terms_report` with its `campaign_id` to find the costly queries without conversions, `google_get_location_performance` to find costly places, `google_get_ad_performance` to find costly ads. Use `google_get_campaign_performance` to check that the waste is steady over the days, not one bad day.
5. Meta Ads: `meta_get_account_insights` with `level: "adset"` and `date_preset: "last_30d"` on the flagged campaigns, then `meta_get_worst_performing_ads` with `metric: "spend"` to see where spend goes with little return.
6. TikTok Ads: `tiktok_get_campaign_performance_summary` for the period, then `tiktok_get_ad_performance` on the campaigns that spend without conversions.
7. Rank the findings by amount spent without result, per currency.

## Output

1. **Amount at stake**: spend without conversions and spend above the CPA threshold, per platform and currency.
2. **Waste list**: a table with platform, campaign, what carries the waste (query, place, ad, ad set), spend, conversions and the suggested action.
3. **Not judged**: campaigns with too little volume, or whose conversions are not collected.
4. **Cuts in order**: the first five actions for a person to apply.

## Rules

- A campaign whose conversions are not collected is not waste: it is unmeasured.
- Do not call a small campaign wasteful on a few days of data; say the volume is too low.
- A conversion may come late: be careful with the last two or three days.
- Name the period and the currency; never sum two currencies.
- An upper-funnel campaign (reach, video views) is judged on its own goal, not on conversions.

## Examples

- "Where are we wasting money across Google, Meta and TikTok?"
- "Quelles campagnes dépensent sans convertir depuis un mois ?"
- "Trouve-moi les fuites de budget, avec le montant en jeu."
