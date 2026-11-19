---
name: merchant-center-local-inventory-review
description: "Reviews local and regional inventory in Google Merchant Center through the Pilot MCP server: for chosen products, the stores that carry them with local price, availability and quantity, the regions with their own price or availability, and the gaps between online data, stores and regions. Use when a retailer runs local inventory ads or free local listings, or uses regional pricing. Triggers: local inventory check, store availability Merchant Center, regional prices. Déclencheurs : inventaire local Merchant Center, disponibilité en magasin, prix régionaux, annonces d'inventaire local."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Merchant Center account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "merchant_center_list_accounts merchant_center_list_products merchant_center_get_product merchant_center_list_local_inventory merchant_center_list_regional_inventory"
  pilot-mcp-read-only: "true"
---

# Merchant Center local inventory review

Tell the user, product by product, what each store and region shows to shoppers, and where the data disagrees. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Merchant Center account.
- **Optional MCP servers:** a point of sale or inventory server to compare with real stock. None is needed.
- **Read-only:** never change an inventory. Fixes are for a person to apply.

## Tools

- `merchant_center_list_accounts`: the accounts available.
- `merchant_center_list_products`: processed products.
- `merchant_center_get_product`: one product's online price and availability.
- `merchant_center_list_local_inventory`: a product's inventory per store.
- `merchant_center_list_regional_inventory`: a product's inventory per region.

## Steps

1. Call `merchant_center_list_accounts` and pick the account.
2. Ask which products matter (best sellers, a promotion, a category). If the user has no list, page through `merchant_center_list_products` and take a sample.
3. For each product, call `merchant_center_get_product` for the online price and availability, then `merchant_center_list_local_inventory` and `merchant_center_list_regional_inventory`.
4. Flag: stores with no inventory entry for a product sold in store, out of stock in every store, local price far from the online price without a reason, sale price with an expired period, regions with a price but no availability.
5. Count stores covered per product and products covered per store.

## Output

1. **Summary**: coverage of stores and regions, and the main gap.
2. **Per product**: online price and availability, stores in stock, out of stock and missing, regional prices.
3. **Per store**: products missing or out of stock.
4. **Fixes**: in order, for a person to apply at the source.

## Rules

- Money values come in micros with a currency code: divide by 1,000,000 and keep the currency.
- An inventory entry absent from the list is missing, not out of stock.
- Read every page of a list before counting.

## Examples

- "Which stores show our best sellers as out of stock on Google?"
- "Vérifie l'inventaire local Merchant Center de nos 20 meilleures ventes."
- "Les prix régionaux sont-ils cohérents avec le prix en ligne ?"
