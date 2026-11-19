---
name: google-ads-performance-max-review
description: "Reviews Performance Max campaigns in Google Ads through the Pilot MCP server: results per campaign and asset group, the assets of each asset group (headlines, descriptions, images, videos, logos) and what is missing, the audience and search theme signals, the products sold when a Merchant Center feed is linked, and the conversion goals the campaigns bid on, then lists what to add, replace or split. Use when the user asks how Performance Max is doing or how to improve it. Triggers: Performance Max review, PMax audit, asset groups, PMax results. Déclencheurs : revue Performance Max, audit PMax, groupes d'éléments, résultats Performance Max."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Ads account that runs Performance Max campaigns."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_list_campaigns google_get_campaign_performance google_list_asset_groups google_list_asset_group_assets google_run_query google_get_shopping_performance google_list_merchant_center_links google_list_conversion_goals"
  pilot-mcp-read-only: "true"
---

# Google Ads Performance Max review

Tell the user how each Performance Max campaign and asset group performs, whether it has what it needs, and what to change. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a file storage server to collect new assets. None is needed.
- **Read-only:** never add, pause or remove an asset, a signal or an asset group. Changes are for a person to apply.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_list_campaigns`: campaigns with their channel type, to keep Performance Max.
- `google_get_campaign_performance`: cost, conversions and value per campaign and day.
- `google_list_asset_groups`: the asset groups of a Performance Max campaign.
- `google_list_asset_group_assets`: the assets linked to one asset group.
- `google_run_query`: a read-only GAQL query, here for results per asset group and for signals.
- `google_get_shopping_performance`: product-level results, for campaigns fed by Merchant Center.
- `google_list_merchant_center_links`: the Merchant Center accounts linked.
- `google_list_conversion_goals`: the goals each campaign bids on.

## Steps

1. Call `google_list_accounts`, then `google_list_campaigns` and keep campaigns whose channel type is Performance Max.
2. Call `google_get_campaign_performance` over `LAST_30_DAYS` with `campaign_id` for each one, and compute CPA and ROAS.
3. Call `google_list_asset_groups` with `campaign_id`, then `google_run_query` with `SELECT asset_group.name, asset_group.status, metrics.cost_micros, metrics.conversions, metrics.conversions_value FROM asset_group WHERE segments.date DURING LAST_30_DAYS` for results per asset group.
4. For each asset group, call `google_list_asset_group_assets` and count assets by field type. Flag missing types: too few headlines or descriptions, no long headline, no landscape or square image, no logo, no video (Google then builds one).
5. Call `google_run_query` with `SELECT asset_group.name, asset_group_signal.audience.audience, asset_group_signal.search_theme.text FROM asset_group_signal` to see the signals.
6. If the campaign sells products: `google_list_merchant_center_links`, then `google_get_shopping_performance` with `campaign_id` to see which products take the spend.
7. Call `google_list_conversion_goals` with `campaign_id`: a Performance Max campaign bidding on a weak goal finds cheap, useless conversions.

## Output

1. **Summary**: each campaign's results and the main lever.
2. **Asset groups**: a table with name, status, spend, conversions, CPA or ROAS, and missing assets.
3. **Signals**: audiences and search themes per asset group, and gaps.
4. **Products**: products that take spend without selling, when a feed is linked.
5. **Changes**: assets to add or replace, groups to split by product line or audience, goals to fix, for a person to apply.

## Rules

- Performance Max reports little by channel: never claim a split by network that the tools did not return.
- Do not judge an asset group with too few conversions; say the volume is too low.
- Google amounts are in micros: divide by 1,000,000.
- Name the period.

## Examples

- "How are our Performance Max campaigns doing, asset group by asset group?"
- "Quels éléments manquent dans les groupes d'éléments Performance Max ?"
- "Quels produits consomment le budget PMax sans vendre ?"
