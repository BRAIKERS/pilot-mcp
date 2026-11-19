---
name: merchant-center-product-performance-report
description: "Builds a product-level report from Google Merchant Center through the Pilot MCP server: impressions, clicks, click-through rate and conversions per product, brand or category across Shopping ads and free listings, products with impressions but no clicks, products with no impressions at all, and the change against the previous period. Use when the user asks which products get seen and clicked on Google, or which products never show. Triggers: product performance Google Shopping, Merchant Center report, products with no impressions. Déclencheurs : performance des produits Google Shopping, rapport Merchant Center, produits sans impressions."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Merchant Center account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "merchant_center_list_accounts merchant_center_run_report merchant_center_list_products merchant_center_get_product"
  pilot-mcp-read-only: "true"
---

# Merchant Center product performance report

Show the user which products Google shows and gets clicked, which ones it ignores, and what to look at. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Merchant Center account.
- **Optional MCP servers:** a spreadsheet server to export the product tables. None is needed.
- **Read-only:** never change a product.

## Tools

- `merchant_center_list_accounts`: the accounts available.
- `merchant_center_run_report`: Merchant Query Language reports on product performance and product status.
- `merchant_center_list_products`: processed products, to find those absent from the performance report.
- `merchant_center_get_product`: one product's attributes, to explain an outlier.

## Steps

1. Call `merchant_center_list_accounts` and pick the account.
2. Fix the period and a comparison period of the same length.
3. Call `merchant_center_run_report` on `product_performance_view` with offer, title, brand, clicks, impressions and click-through rate, filtered on the dates, for both periods. Split by marketing method (ads against free listings) when the user asks. Page through with `page_token`.
4. Rank products by impressions and by clicks; group by brand or category when the report gives them.
5. Call `merchant_center_list_products` and compare with the report: approved products with zero impressions over the period are the ones Google never shows.
6. For outliers (many impressions and almost no clicks), call `merchant_center_get_product`: title, image, price and availability often explain it.
7. Compute click-through rate on totals, and the change for the top products.

## Output

1. **Headline**: impressions, clicks and click-through rate, with the change.
2. **Top products**: by clicks, with impressions and click-through rate.
3. **Seen but not clicked**: products with many impressions and a low click-through rate, with the likely reason.
4. **Never shown**: approved products with no impressions.
5. **By brand or category**: when available.

## Rules

- Merchant Center counts its own clicks and conversions; they differ from Google Ads and analytics figures: say so.
- A ratio with an empty denominator is not available, not zero.
- Name the period, and show the query used.

## Examples

- "Which of our products get the most clicks on Google Shopping?"
- "Quels produits Google n'affiche jamais ?"
- "Fais le rapport produits Merchant Center du mois, par marque."
