---
name: merchant-center-product-issues-review
description: "Reviews Google Merchant Center product issues through the Pilot MCP server: products disapproved or limited per destination and country, item-level issues with their severity, attribute and documentation, the full product data behind each issue, and the issues that affect the best-selling or most-viewed products first. Use when the user asks why products are disapproved, which products are not eligible for Shopping, or how to fix a specific product. Triggers: disapproved products, Merchant Center product issues, why is this product not showing. Déclencheurs : produits refusés, problèmes produits Merchant Center, pourquoi ce produit ne diffuse pas."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Merchant Center account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "merchant_center_list_accounts merchant_center_list_account_issues merchant_center_list_products merchant_center_get_product merchant_center_get_product_status merchant_center_list_product_issues merchant_center_run_report"
  pilot-mcp-read-only: "true"
---

# Merchant Center product issues review

Tell the user which products cannot serve or serve less, why, and how to fix each kind of issue. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Merchant Center account.
- **Optional MCP servers:** an e-commerce platform server to fix the source data. None is needed.
- **Read-only:** never change a product. Fixes are for a person to apply, at the source or in the feed.

## Tools

- `merchant_center_list_accounts`: the accounts available.
- `merchant_center_list_account_issues`: account-level problems, which take precedence.
- `merchant_center_list_products`: processed products.
- `merchant_center_get_product`: one product's attributes and status.
- `merchant_center_get_product_status`: one product's status per destination and its issues summary.
- `merchant_center_list_product_issues`: one product's item-level issues.
- `merchant_center_run_report`: product status and performance through Merchant Query Language.

## Steps

1. Call `merchant_center_list_accounts` and pick the account. Call `merchant_center_list_account_issues` first: an account-level issue explains product-level ones.
2. Call `merchant_center_run_report` on `product_view` to list products with their status and issues; when the user cares about revenue, call it on `product_performance_view` over the last 30 days for clicks and impressions per offer, to rank issues by what they cost.
3. If the report does not give issue detail, page through `merchant_center_list_products` and, for disapproved or limited products, call `merchant_center_get_product_status` and `merchant_center_list_product_issues`. Product names follow `contentLanguage~feedLabel~offerId`, for example `fr~FR~SKU123`.
4. Group issues by code and attribute: how many products, which destinations (Shopping ads, free listings), which countries, blocking or limiting.
5. For each group, open one example with `merchant_center_get_product` and show the attribute value that triggers the issue.
6. Write the fix per group: correct the source data, add a supplemental feed rule, or fix the site (price, availability, structured data).

## Output

1. **Summary**: products affected, blocking against limiting, the top three issues.
2. **Issue groups**: issue, attribute, products, destinations, countries, example, fix.
3. **Priority products**: the most viewed or best-selling products with an issue.
4. **Next steps**: in order.

## Rules

- Quote Google's issue wording and documentation link as given.
- Separate disapproved (cannot serve) from limited (serves less).
- Read every page of a list before counting.

## Examples

- "Why are 300 of our products disapproved in Merchant Center?"
- "Pourquoi le produit SKU123 n'apparaît pas dans Shopping ?"
- "Quels problèmes produits touchent nos meilleures ventes ?"
