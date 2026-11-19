---
name: magento-revenue-and-ad-spend-check
description: "Sets Magento store revenue against advertising spend through the Pilot MCP server: store revenue and orders day by day, ad spend and platform-reported conversion value across the connected ad platforms, the ratio of revenue to ad spend, and the gap between what platforms claim and what the store sold, with the best-selling products. Use when the user asks whether advertising pays for itself, or why platform ROAS and store revenue disagree. Triggers: revenue vs ad spend, blended ROAS Magento, ads and store sales. Déclencheurs : chiffre d'affaires et dépense publicitaire, ROAS global Magento, publicité rentable boutique."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Magento store and at least one ad platform attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "magento_get_store magento_get_sales magento_get_top_products pilot_cross_platform_overview pilot_data_freshness"
  pilot-mcp-read-only: "true"
---

# Magento revenue and ad spend check

Show the user what the store sold against what advertising cost, and how far the platforms' own figures are from the store's. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Magento store and the ad platforms.
- **Optional MCP servers:** an analytics server for channel attribution. None is needed.
- **Read-only:** nothing is changed.

## Tools

- `magento_get_store`: base currency and time zone of the store.
- `magento_get_sales`: store revenue and orders by day and in total.
- `magento_get_top_products`: the products that make the revenue.
- `pilot_cross_platform_overview`: ad spend, conversions and conversion value per platform, recomputed on totals.
- `pilot_data_freshness`: whether Pilot's ad data is current.

## Steps

1. Call `magento_get_store` for the currency and time zone.
2. Call `pilot_data_freshness`: a platform behind on collection makes spend look low.
3. Fix the period (the last 30 days by default) and call `magento_get_sales` for it and for the period before.
4. Call `pilot_cross_platform_overview` with the same number of days. It refuses a money total when currencies differ: if the store and the platforms do not share a currency, compare per currency and say that no blended ratio is possible.
5. Compute, in one currency only: store revenue divided by total ad spend, ad spend as a share of revenue, and the sum of platform-reported conversion value against store revenue. Platforms each claim the sales they touched, so their sum often exceeds the store's revenue: show the gap and explain it.
6. Read the daily curves side by side: spend peaks that bring no revenue peak, revenue peaks without spend.
7. Call `magento_get_top_products` to say which products the revenue rests on.

## Output

1. **Verdict**: revenue against ad spend for the period, with the change.
2. **Table**: store revenue, orders, ad spend per platform, platform-reported value, ratios.
3. **Gap**: platform-reported value against store revenue, and why they differ.
4. **Daily view**: spend and revenue side by side, notable days.
5. **What to watch**: two or three points.

## Rules

- Never sum or divide amounts in different currencies.
- Store revenue includes taxes and shipping: say so when comparing with platform values.
- Do not attribute store revenue to a platform from this comparison alone.

## Examples

- "Does our advertising pay for itself against Magento sales?"
- "Compare le chiffre d'affaires Magento et la dépense publicitaire du mois."
- "Pourquoi la somme des ROAS des régies dépasse nos ventes réelles ?"
