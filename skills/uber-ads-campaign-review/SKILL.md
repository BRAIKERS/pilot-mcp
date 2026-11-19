---
name: uber-ads-campaign-review
description: "Reviews Uber Ads (Uber Eats ads) in read-only mode through the Pilot MCP server: account type and the ad products it can buy, campaigns and ad groups with configured and effective status and their reasons, budgets, bids and targeted stores, and the impressions, clicks, spend, orders, sales and new customers of the period by campaign, ad group and store. Use when the user asks how their Uber Eats ads perform or why a campaign does not deliver. Triggers: Uber Eats ads review, Uber Ads performance, sponsored listing results. Déclencheurs : revue Uber Ads, publicités Uber Eats, performance des annonces Uber Eats, ventes par restaurant Uber."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with an Uber Ads account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "uber_ads_get_ad_account uber_ads_list_campaigns uber_ads_list_ad_groups uber_ads_list_ads uber_ads_list_stores uber_ads_get_statistics pilot_data_freshness"
  pilot-mcp-read-only: "true"
---

# Uber Ads campaign review

Tell the user how their Uber Ads campaigns deliver and sell, store by store, and what blocks or wastes budget. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Uber Ads account.
- **Optional MCP servers:** a file storage server to save the review. None is needed.
- **Read-only:** never pause, reactivate or change anything. Recommendations are for a person to apply.

## Tools

- `uber_ads_get_ad_account`: account name, currency, country, type and billing mode.
- `uber_ads_list_campaigns`: campaigns with configured and effective status, reasons, budget and dates.
- `uber_ads_list_ad_groups`: ad groups with ad product, budget, bid, schedule and targeted stores.
- `uber_ads_list_ads`: ads of an ad group with status and reasons.
- `uber_ads_list_stores`: the Uber Eats stores linked to the account.
- `uber_ads_get_statistics`: metrics by account, campaign, ad group or store, per day or over the period.
- `pilot_data_freshness`: whether Pilot's collected Uber Ads data is current.

## Steps

1. Call `uber_ads_get_ad_account`: the account type tells which ad products it can buy (Sponsored Listing, Sponsored Search, Sponsored Item, Sponsored Brands).
2. Call `uber_ads_list_campaigns`, then `uber_ads_list_ad_groups` with `campaign_id`. Compare configured and effective status: a campaign set active but not effectively delivering comes with Uber's reasons; quote them.
3. Call `uber_ads_list_stores` and map each store to the ad groups that target it. Flag stores targeted by no ad group, and ad groups whose stores are closed or missing.
4. For Sponsored Brands ad groups, call `uber_ads_list_ads` with `campaign_id` and `ad_group_id`.
5. Call `uber_ads_get_statistics` with `level: "CAMPAIGN"`, `granularity: "TOTAL"` over the period (92 days at most), then `level: "STORE"` for the store split and `level: "AD_GROUP"` for the campaigns that spend most.
6. Compute cost per order and return on ad spend (sales divided by spend) where orders and spend are above zero. Separate click-attributed and exposure-attributed orders, and show new customers apart.
7. Call `pilot_data_freshness` with `platform: "uber_ads"` when the user compares with Pilot's reports.

## Output

1. **Summary**: what delivers, what sells, the main blocker.
2. **Campaigns**: a table with configured and effective status, budget, spend, orders, sales and return on ad spend.
3. **Stores**: the best and weakest stores, and stores left without ads.
4. **Blocked**: campaigns, ad groups or ads not delivering, with Uber's reasons.
5. **Next steps**: changes for a person to apply.

## Rules

- Amounts are in units of the account currency: name it, and never sum stores in different currencies.
- A ratio with an empty denominator is not available, not zero.
- Name the attribution Uber uses for orders.

## Examples

- "How are our Uber Eats ads doing per restaurant?"
- "Pourquoi la campagne Uber Ads Centre-ville ne diffuse pas ?"
- "Quels restaurants vendent le plus grâce aux annonces Uber Eats ?"
