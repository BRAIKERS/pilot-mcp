---
name: google-ads-shopping-review
description: "Reviews Google Shopping campaigns through the Pilot MCP server: the Merchant Center link, results per product, products that spend without selling and best sellers without enough reach, the product group structure and its bids, and the impression share Shopping campaigns lose to budget or rank, then proposes bid, structure and exclusion changes for a person to apply. Use when the user asks how Shopping performs, which products to push or cut, or why Shopping spend rose. Triggers: Google Shopping review, Shopping campaigns, product performance, product groups. Déclencheurs : revue Google Shopping, campagnes Shopping, performance par produit, groupes de produits."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Ads account that runs Shopping campaigns."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_list_merchant_center_links google_list_campaigns google_get_campaign_performance google_get_shopping_performance google_list_product_groups google_run_query"
  pilot-mcp-read-only: "true"
---

# Google Ads Shopping review

Show which products earn their spend in Shopping, which waste it, and how the campaign structure and bids should change. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a spreadsheet server to hand over the product list. None is needed.
- **Read-only:** never change bids, product groups or campaigns. Proposals are for a person to apply.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_list_merchant_center_links`: the Merchant Center accounts linked to Google Ads.
- `google_list_campaigns`: campaigns with their channel type, to keep Shopping.
- `google_get_campaign_performance`: cost, conversions and value per campaign.
- `google_get_shopping_performance`: product-level metrics, by cost.
- `google_list_product_groups`: product groups (listing groups) and their bids.
- `google_run_query`: a read-only GAQL query, here for the impression share of Shopping campaigns.

## Steps

1. Call `google_list_accounts` and pick the account. Call `google_list_merchant_center_links`: without an active link, Shopping cannot serve.
2. Call `google_list_campaigns` and keep Shopping campaigns; call `google_get_campaign_performance` over `LAST_30_DAYS` for their totals.
3. Call `google_get_shopping_performance` with `limit: 1000` over the last 30 days (`start_date`, `end_date`). If the count equals the limit, filter with `campaign_id`.
4. Sort products: those that spend without conversions, those with a ROAS well under the account average, and best sellers with a strong ROAS but little spend.
5. Call `google_list_product_groups` to see how products are split and bid on: a single "All products" group gives every product the same bid.
6. Call `google_run_query` with `SELECT campaign.name, metrics.search_impression_share, metrics.search_budget_lost_impression_share, metrics.search_rank_lost_impression_share FROM campaign WHERE segments.date DURING LAST_30_DAYS AND campaign.advertising_channel_type = 'SHOPPING'` to see whether Shopping loses visibility to budget or to rank.
7. Propose changes: split product groups by brand, category or margin, lower bids or exclude products that waste, raise bids on best sellers that are held back.

## Output

1. **Summary**: Shopping results and the main lever.
2. **Products to cut**: product, spend, conversions, ROAS.
3. **Products to push**: product, ROAS, spend share.
4. **Structure**: the product group tree and its issues.
5. **Visibility**: impression share won and lost to budget or rank per Shopping campaign.
6. **Changes**: for a person to apply, in order.

## Rules

- A product with a few clicks is not judged: say the volume is too low.
- Product-level figures depend on the feed: a product missing from the report may be disapproved in Merchant Center; say so rather than guess.
- Google amounts are in micros: divide by 1,000,000.
- Name the period and the currency.

## Examples

- "Which products waste money in Google Shopping?"
- "Quels produits Shopping faut-il pousser, et lesquels exclure ?"
- "Notre structure de groupes de produits est-elle bonne ?"
