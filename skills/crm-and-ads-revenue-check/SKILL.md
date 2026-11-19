---
name: crm-and-ads-revenue-check
description: "Sets the revenue recorded in the CRM against advertising spend through the Pilot MCP server: won revenue and leads by source in Salesforce, signed deals and collected invoices in Sellsy, and ad spend with platform-reported conversions per ad platform, then shows the gap between what ad platforms claim and what sales closed, and the cost per customer by channel. Use when the user asks whether ads bring real revenue, or why platform conversions and CRM results disagree. Triggers: CRM vs ads revenue, ad spend to closed revenue, offline conversions gap. Déclencheurs : CRM et publicité, chiffre d'affaires signé et dépense publicitaire, écart entre régies et CRM."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with Salesforce or Sellsy and at least one ad platform attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "salesforce_leads_by_source salesforce_revenue_by_source salesforce_list_campaigns sellsy_search_opportunities sellsy_search_invoices pilot_cross_platform_overview pilot_data_freshness"
  pilot-mcp-read-only: "true"
---

# CRM and ads revenue check

Show the user what advertising cost, what the platforms claim it brought, and what sales actually closed in the CRM. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on a workspace with Salesforce or Sellsy, and ad platforms, attached.
- **Optional MCP servers:** none is needed.
- **Read-only:** nothing is changed.

## Tools

- `salesforce_leads_by_source`: Salesforce leads by source and status.
- `salesforce_revenue_by_source`: Salesforce won revenue by lead source.
- `salesforce_list_campaigns`: Salesforce campaigns with recorded cost and results.
- `sellsy_search_opportunities`: Sellsy deals, won ones with `statuses: ["won"]`.
- `sellsy_search_invoices`: Sellsy invoices, paid ones for collected revenue.
- `pilot_cross_platform_overview`: ad spend, conversions and conversion value per platform.
- `pilot_data_freshness`: whether Pilot's ad data is current.

## Steps

1. Fix a period long enough for the sales cycle, and say that leads from its last weeks may not have closed yet.
2. Call `pilot_data_freshness`, then `pilot_cross_platform_overview` with the number of days: spend, conversions and conversion value per platform.
3. Salesforce: `salesforce_leads_by_source` with `created_from`, `salesforce_revenue_by_source` with the close date window, and `salesforce_list_campaigns` for costs recorded in the CRM.
4. Sellsy: `sellsy_search_opportunities` with `statuses: ["won"]` over the period for signed amounts, and `sellsy_search_invoices` with `status: ["paid"]` for collected revenue. Sellsy deals carry no lead source by default: say so, and use whatever source field or tag the account uses when the user names it.
5. Map platforms to CRM sources only where names clearly match; list the sources that cannot be mapped.
6. Compute per channel, in one currency only: spend, platform-reported conversions, CRM leads, won deals, won revenue, cost per won deal, revenue per unit of spend.
7. Show the gap: platform-reported conversions or value against CRM leads and won revenue, and explain the usual causes (attribution windows, view-through, duplicated leads, untagged sources, long sales cycle).

## Output

1. **Verdict**: does advertising bring closed revenue, channel by channel, in two sentences.
2. **Table by channel**: spend, platform conversions, CRM leads, won deals, won revenue, cost per won deal.
3. **Gap**: what platforms claim against what the CRM shows, and why.
4. **Tracking fixes**: source tagging, offline conversion import, naming.
5. **Next steps**: for a person to decide.

## Rules

- Never sum or divide amounts in different currencies.
- CRM lead source is usually first touch; platform conversions follow each platform's attribution: say so.
- Do not move budget on this check alone; present it as evidence for a decision.

## Examples

- "Do our ads bring revenue that actually closes in the CRM?"
- "Compare les conversions déclarées par Google Ads et Meta avec les affaires signées dans Salesforce."
- "Quel est notre coût par client signé, canal par canal ?"
