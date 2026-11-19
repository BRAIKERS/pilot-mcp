---
name: search-ads-360-performance-review
description: "Reviews Search Ads 360 performance in read-only mode through the Pilot MCP server: accounts reachable through the manager, campaigns and ad groups with clicks, impressions, cost, conversions and value over the period, and free-form SA360 queries to compare engines, devices or days, with the change against the previous period. Use when an agency or advertiser running several search engines through SA360 wants one consolidated read. Triggers: SA360 report, Search Ads 360 performance, cross-engine search review. Déclencheurs : rapport SA360, performance Search Ads 360, revue search multi-moteurs."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Search Ads 360 account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "search_ads_360_list_customers search_ads_360_list_campaigns search_ads_360_get_campaign search_ads_360_list_ad_groups search_ads_360_get_metrics search_ads_360_search_report search_ads_360_get_search_fields search_ads_360_validate_operation"
  pilot-mcp-read-only: "true"
---

# Search Ads 360 performance review

Give the user a consolidated Search Ads 360 read for the period asked: accounts, campaigns, ad groups, and the splits that matter. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Search Ads 360 account.
- **Optional MCP servers:** a spreadsheet server to export the tables. None is needed.
- **Read-only:** the SA360 Reporting API is read-only; nothing is changed.

## Tools

- `search_ads_360_list_customers`: the SA360 accounts reachable with the connection.
- `search_ads_360_list_campaigns`: campaigns of an account, with metrics for a period.
- `search_ads_360_get_campaign`: one campaign with its metrics.
- `search_ads_360_list_ad_groups`: ad groups, optionally for one campaign.
- `search_ads_360_get_metrics`: chosen metrics for an entity and a period.
- `search_ads_360_search_report`: a free-form SA360 query.
- `search_ads_360_get_search_fields`: which fields exist and can be selected or filtered.
- `search_ads_360_validate_operation`: checks a query before it is run.

## Steps

1. Call `search_ads_360_list_customers` and pick the account (ask if several). Use its ID without dashes as `customer_id`, and the manager ID as `login_customer_id` when access goes through a manager.
2. Fix the period with `during` (for example `LAST_30_DAYS`, `THIS_MONTH`, `LAST_MONTH`) and a comparison period of the same length.
3. Call `search_ads_360_get_metrics` at the customer level with `metrics` such as clicks, impressions, cost_micros, conversions and conversions_value, for both periods.
4. Call `search_ads_360_list_campaigns` with `during` for both periods; rank campaigns by cost and by conversions. Use `search_ads_360_get_campaign` for a closer look.
5. Call `search_ads_360_list_ad_groups` with `campaign_id` for the campaigns that weigh most.
6. For splits by engine account, device or day, write an SA360 query: check fields with `search_ads_360_get_search_fields`, validate it with `search_ads_360_validate_operation` (`kind: "query"`), then run `search_ads_360_search_report`. Follow `page_token` until every row is read.
7. Divide cost_micros by 1,000,000 and recompute ratios on the totals.

## Output

1. **Headline**: cost, clicks, conversions, value and cost per conversion, with the change.
2. **Campaigns**: a table ranked by cost.
3. **Ad groups**: the best and weakest of the main campaigns.
4. **Splits**: by engine, device or day, when asked.
5. **What to watch**: two or three points.

## Rules

- Name the period and the currency; never sum accounts in different currencies.
- A ratio with an empty denominator is not available, not zero.
- Show the SA360 query used for any custom split.

## Examples

- "Give me the SA360 report for last month."
- "Fais la revue Search Ads 360 des 30 derniers jours, par campagne."
- "Compare les conversions SA360 par appareil ce mois-ci."
