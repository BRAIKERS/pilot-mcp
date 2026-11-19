---
name: apple-ads-account-audit
description: "Audits an Apple Ads (App Store search ads) organization in read-only mode through the Pilot MCP server: campaigns by country with budget and status, ad groups with default bids and cost per acquisition goals, keywords and negatives, ads and their rejection reasons, and the spend, taps and installs of the last 30 days, then returns a prioritized findings report. Use when the user asks to audit or health-check Apple Ads or Apple Search Ads. Triggers: Apple Search Ads audit, Apple Ads review, App Store ads health check. Déclencheurs : audit Apple Ads, audit Apple Search Ads, revue du compte Apple Ads, publicités App Store."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with an Apple Ads organization attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "apple_ads_apple_ads_list_campaigns apple_ads_apple_ads_get_app apple_ads_apple_ads_report_campaigns apple_ads_apple_ads_list_adgroups apple_ads_apple_ads_report_adgroups apple_ads_apple_ads_list_targeting_keywords apple_ads_apple_ads_list_campaign_negative_keywords apple_ads_apple_ads_list_ads apple_ads_apple_ads_find_product_page_reasons pilot_data_freshness"
  pilot-mcp-read-only: "true"
---

# Apple Ads account audit

Audit one Apple Ads organization and hand back a short, prioritized list of findings, each backed by figures from the account. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Apple Ads organization.
- **Optional MCP servers:** a mobile measurement or analytics server to follow installs further. None is needed.
- **Read-only:** never create, update, pause or delete anything. Every recommendation is written for a person to apply.

## Tools

- `apple_ads_apple_ads_list_campaigns`: campaigns with app, countries, budget, status and serving state.
- `apple_ads_apple_ads_get_app`: the App Store details of the promoted app.
- `apple_ads_apple_ads_report_campaigns`: spend, impressions, taps and installs per campaign.
- `apple_ads_apple_ads_list_adgroups`: ad groups of a campaign with default bid, goal and search match.
- `apple_ads_apple_ads_report_adgroups`: results per ad group of a campaign.
- `apple_ads_apple_ads_list_targeting_keywords`: keywords of an ad group with match type and bid.
- `apple_ads_apple_ads_list_campaign_negative_keywords`: negatives of a campaign.
- `apple_ads_apple_ads_list_ads`: ads of an ad group, with status.
- `apple_ads_apple_ads_find_product_page_reasons`: rejection reasons of custom product pages used in ads.
- `pilot_data_freshness`: whether Pilot's collected Apple Ads data is current.

## Steps

1. Call `apple_ads_apple_ads_list_campaigns` with `limit: 1000`. Note each campaign's app (adamId), countries, daily budget, status and serving state. If several organizations are linked, ask which one and pass it as `account_id`.
2. Call `apple_ads_apple_ads_get_app` for each promoted app.
3. Call `pilot_data_freshness` with `platform: "apple_ads"`.
4. Call `apple_ads_apple_ads_report_campaigns` with `startTime` and `endTime` over the last 30 days and `granularity: "DAILY"` when the trend matters. Rank campaigns by spend; note spend with few installs.
5. For the campaigns that spend most, call `apple_ads_apple_ads_list_adgroups` and `apple_ads_apple_ads_report_adgroups`: search match left on in exact-match ad groups, default bids far above the cost per tap actually paid, goals never reached.
6. Read keywords and negatives: `apple_ads_apple_ads_list_targeting_keywords` on the main ad groups, `apple_ads_apple_ads_list_campaign_negative_keywords` per campaign. A discovery campaign whose winning terms are not excluded from it competes with the exact campaigns.
7. Call `apple_ads_apple_ads_list_ads` and `apple_ads_apple_ads_find_product_page_reasons`: ads stopped by a rejected custom product page.
8. Write the report, findings ranked by money at stake.

## Output

1. **Summary**: three lines on the account's state and the top three priorities.
2. **Findings**: a table with the finding, the evidence (figure, period, currency), the stake and the suggested action.
3. **Structure**: brand, category, competitor and discovery campaigns per country, and what is missing.
4. **Next steps**: actions in order, for a person to apply in Apple Ads.

## Rules

- Each campaign spends in its own currency: never sum different currencies.
- A ratio with an empty denominator is not available, not zero.
- Installs reported by Apple are Apple's attribution: say so when comparing with another source.

## Examples

- "Audit our Apple Search Ads account."
- "Fais l'audit de nos campagnes Apple Ads, pays par pays."
- "Où perd-on de l'argent sur Apple Ads ?"
