---
name: magento-store-review
description: "Reviews a Magento store's sales in read-only mode through the Pilot MCP server: revenue, orders, average order value, discounts, shipping, taxes and refunds day by day, orders by state and status (pending, processing, shipped, canceled, refunded), and the best-selling products, with the change against the previous period. Use when the user asks how the Magento store sells, which products lead, or why orders are stuck or canceled. Triggers: Magento sales report, Magento best sellers, canceled orders Magento. Déclencheurs : ventes Magento, meilleures ventes Magento, commandes annulées Magento, bilan de la boutique Magento."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Magento store attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "magento_get_store magento_get_sales magento_get_orders_by_status magento_get_top_products"
  pilot-mcp-read-only: "true"
---

# Magento store review

Tell the user how the Magento store sells, which products carry it, and where orders get stuck or lost. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Magento store.
- **Optional MCP servers:** a spreadsheet server to export the tables. None is needed.
- **Read-only:** Magento is served read-only by Pilot; nothing is changed.

## Tools

- `magento_get_store`: the store's name, base currency, time zone and the shop's other stores.
- `magento_get_sales`: revenue, orders, average order value, cancellations, discounts, shipping, taxes and refunds, by day and in total.
- `magento_get_orders_by_status`: orders and amounts by state and status for orders created over a period.
- `magento_get_top_products`: best-selling products by revenue, with quantity and orders.

## Steps

1. Call `magento_get_store` for the currency and time zone.
2. Fix the period (the last 30 days by default) and the period before it, as `start_date` and `end_date`.
3. Call `magento_get_sales` for both periods: revenue, orders, average order value, guest share, discounts, refunds. Read the daily curve for peaks and gaps.
4. Call `magento_get_orders_by_status` for the period: orders still pending or in payment review, canceled and refunded shares. A rise in pending orders often points to a payment or stock problem.
5. Call `magento_get_top_products` with a `limit` of 20 to 50: concentration of revenue on a few products, and products that dropped against the previous period (call it for the previous period too).
6. Write the review.

## Output

1. **Headline**: revenue, orders and average order value, with the change.
2. **Daily trend**: peaks, gaps and their dates.
3. **Order health**: pending, canceled and refunded shares, and what they suggest.
4. **Products**: the top sellers, their share of revenue, and the biggest risers and fallers.
5. **What to watch**: two or three points.

## Rules

- Revenue includes taxes and shipping in `magento_get_sales`, while product revenue in `magento_get_top_products` excludes them: never compare the two directly.
- Days follow the store time zone; amounts are in the base currency.
- A ratio with an empty denominator is not available, not zero.

## Examples

- "How did the Magento store do last month?"
- "Quels produits font le chiffre d'affaires de la boutique Magento ?"
- "Pourquoi autant de commandes restent en attente cette semaine ?"
