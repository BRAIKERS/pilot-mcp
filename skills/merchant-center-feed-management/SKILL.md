---
name: merchant-center-feed-management
description: "Fixes product data in Google Merchant Center through the Pilot MCP server, with the user's explicit approval before every write: correct product attributes (title, price, availability, identifiers), add or update a product, update local or regional inventory, rename or adjust a data source, and trigger a feed fetch. Each payload is validated and simulated first, then applied and read back. Use when the user wants to fix disapproved products or feed data directly in Merchant Center. Triggers: fix Merchant Center product, update product feed, refresh feed. Déclencheurs : corriger un produit Merchant Center, modifier le flux produits, relancer le flux."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Merchant Center account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "merchant_center_list_accounts merchant_center_list_data_sources merchant_center_get_data_source merchant_center_get_product merchant_center_list_product_issues merchant_center_validate_product_payload merchant_center_update_product merchant_center_insert_product merchant_center_update_local_inventory merchant_center_update_regional_inventory merchant_center_update_data_source merchant_center_fetch_data_source"
  pilot-mcp-read-only: "false"
---

# Merchant Center feed management

Apply the product and feed fixes the user asks for in Merchant Center, one at a time, each after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Merchant Center account.
- **Optional MCP servers:** an e-commerce platform server, to fix the source data as well. None is needed.
- **Writes:** the write tools need the paid Pilot MCP plan and a workspace role allowed to write. If a write tool is not served, say so and return the list of fixes for a person to apply in Merchant Center or at the source.

## Tools

- `merchant_center_list_accounts`: the accounts available.
- `merchant_center_list_data_sources`: the feeds of the account.
- `merchant_center_get_data_source`: one feed in detail.
- `merchant_center_get_product`: one product's current attributes and status.
- `merchant_center_list_product_issues`: one product's issues.
- `merchant_center_validate_product_payload`: checks a product payload before it is sent.
- `merchant_center_update_product`: changes attributes of a product input (write).
- `merchant_center_insert_product`: creates or replaces a product input (write).
- `merchant_center_update_local_inventory`: sets a product's price and availability for one store (write).
- `merchant_center_update_regional_inventory`: sets a product's price and availability for one region (write).
- `merchant_center_update_data_source`: changes fields of a feed (write).
- `merchant_center_fetch_data_source`: asks Google to download a file feed now (write).

## Confirmation

Product data is what shoppers see in ads and free listings. For each write:

1. Read the current state first with `merchant_center_get_product` (and `merchant_center_list_product_issues`) or `merchant_center_get_data_source`.
2. Show the person the exact object (account, feed, product name `language~label~offerId`), the current value and the proposed value.
3. Validate product payloads with `merchant_center_validate_product_payload`, then run the write tool with `dry_run: true` and show the simulation.
4. Wait for an explicit yes for that write. A yes covers one write, on one product or one feed.
5. Send the write with `dry_run: false` (and `confirm: true` when the tool asks for it).
6. Never delete a product, an inventory or a feed. A deletion must be asked for separately, naming the object.
7. After the write, read the object again and show the before and after; the processed product can take a few minutes to update. If a write is refused or fails, stop, report the message, and do not attempt the next one.

## Steps

1. Call `merchant_center_list_accounts` and confirm the account.
2. Call `merchant_center_list_data_sources`: a product input belongs to a data source, which `merchant_center_update_product` and `merchant_center_insert_product` require.
3. Warn when the feed is a file or a platform sync: the next fetch will overwrite a fix made here unless the source is fixed too.
4. Turn the request into a numbered list of single writes and show it before any write.
5. Attribute fixes: `merchant_center_update_product` with only the attributes to change. Full replacement or a new product: `merchant_center_insert_product`.
6. Store or region data: `merchant_center_update_local_inventory` with `store_code`, or `merchant_center_update_regional_inventory` with `region`. Prices are in micros with a currency code.
7. Feed changes: `merchant_center_update_data_source` with an `update_mask`; refresh a file feed with `merchant_center_fetch_data_source`.
8. End with the log of writes made and not made.

## Output

1. **Plan**: the numbered list of writes, before any write.
2. **Log**: for each write, the object, the simulation, before, after and the time it was made.
3. **Not done**: writes refused, failed or left for the source.

## Rules

- Show prices both in micros and in readable currency.
- Never chain several writes on one yes.
- Never act on an account other than the one confirmed.

## Examples

- "Fix the price of SKU123 in Merchant Center to 49.90 euros."
- "Passe le produit fr~FR~TSHIRT-01 en rupture de stock."
- "Relance le téléchargement du flux principal Merchant Center."
