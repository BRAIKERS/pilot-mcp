---
name: ga4-ecommerce-review
description: "Reviews online store results in Google Analytics 4 through the Pilot MCP server: purchases, revenue, average order value and purchasers over the period against the period before, by channel and device, product performance from view to purchase, the shopping funnel from product view to purchase, and gaps in e-commerce tracking, then lists what to push and what to fix. Use when the user asks how the store sells, which products sell, or why revenue changed. Triggers: GA4 ecommerce review, online store performance, product performance, revenue by channel. Déclencheurs : revue e-commerce GA4, performance de la boutique, ventes par produit, chiffre d'affaires par canal."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Analytics 4 property that collects e-commerce events."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_analytics_get_account_summaries google_analytics_get_property google_analytics_list_key_events google_analytics_batch_run_reports google_analytics_run_report google_analytics_run_funnel_report"
  pilot-mcp-read-only: "true"
---

# GA4 e-commerce review

Show how the store sells, which channels and products carry revenue, where shoppers drop, and whether e-commerce tracking is complete. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the property.
- **Optional MCP servers:** a store or product catalog server to add margins and stock. None is needed.
- **Read-only:** never change anything in GA4.

## Tools

- `google_analytics_get_account_summaries`: GA4 properties reachable.
- `google_analytics_get_property`: currency and time zone.
- `google_analytics_list_key_events`: whether `purchase` is a key event.
- `google_analytics_batch_run_reports`: several e-commerce reports in one call.
- `google_analytics_run_report`: one report, here for products and tracking checks.
- `google_analytics_run_funnel_report`: the shopping funnel from product view to purchase.

## Steps

1. Call `google_analytics_get_account_summaries`, pick the property, and call `google_analytics_get_property` for the currency.
2. Call `google_analytics_list_key_events`.
3. Call `google_analytics_batch_run_reports` with the period and the period before:
   - totals: `metrics: ["ecommercePurchases", "purchaseRevenue", "averagePurchaseRevenue", "totalPurchasers"]`;
   - channels: `dimensions: ["sessionDefaultChannelGroup"]`, same metrics;
   - devices: `dimensions: ["deviceCategory"]`, same metrics.
4. Call `google_analytics_run_report` with `dimensions: ["itemName"]` and `metrics: ["itemsViewed", "itemsAddedToCart", "itemsPurchased", "itemRevenue"]`, `limit: 50`: products viewed often but rarely added, and products added but rarely bought.
5. Call `google_analytics_run_funnel_report` with steps on `view_item`, `add_to_cart`, `begin_checkout`, `add_payment_info` and `purchase`.
6. Tracking checks: `google_analytics_run_report` with `dimensions: ["eventName"]` and `metrics: ["eventCount"]` for these events, then with `dimensions: ["transactionId"]` and `metrics: ["ecommercePurchases"]`. A `(not set)` transaction ID, the same ID counted twice, or more purchases than checkouts point to broken tracking.

## Output

1. **Headline**: revenue, purchases and average order value, with the change.
2. **Channels and devices**: tables with revenue, purchases and change.
3. **Products**: best sellers, products that draw views but do not sell, products abandoned in cart.
4. **Funnel**: completion per step and the weakest step.
5. **Tracking**: missing or odd e-commerce events.
6. **Actions**: what to push and what to fix, for a person to decide.

## Rules

- Revenue is in the property currency: name it, and never mix it with ad platform values.
- GA4 revenue and the store's back office rarely match exactly: say so if the user compares them.
- A product with few views is not judged.
- Name the period.

## Examples

- "How did the store sell this month, by channel?"
- "Quels produits attirent des vues mais ne se vendent pas ?"
- "Pourquoi le panier moyen a baissé ?"
