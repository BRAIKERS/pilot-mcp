---
name: salesforce-lead-source-review
description: "Shows which acquisition channels bring leads that convert and revenue that closes, through the Pilot MCP server: Salesforce leads by source and status, won revenue by lead source, Salesforce campaigns with their recorded cost and results, set against the ad spend Pilot collected on each platform. Use when the user asks which channels bring real customers, not only leads, or whether paid channels pay off in the CRM. Triggers: lead source analysis, revenue by channel Salesforce, which ads bring customers. Déclencheurs : sources de leads, chiffre d'affaires par canal Salesforce, quels canaux rapportent des clients."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Salesforce org attached. Ad platforms are optional."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "salesforce_leads_by_source salesforce_revenue_by_source salesforce_list_campaigns salesforce_get_campaign pilot_cross_platform_overview"
  pilot-mcp-read-only: "true"
---

# Salesforce lead source review

Show the user, channel by channel, how many leads come in, how many convert, how much revenue closes, and what the channel cost. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Salesforce org.
- **Optional MCP servers:** none is needed. Ad platforms attached to the workspace add spend per channel.
- **Read-only:** never change a record.

## Tools

- `salesforce_leads_by_source`: leads by source and status since a date.
- `salesforce_revenue_by_source`: won revenue by lead source over a close date window.
- `salesforce_list_campaigns`: Salesforce campaigns with cost and results fields.
- `salesforce_get_campaign`: one campaign with its rollup fields.
- `pilot_cross_platform_overview`: ad spend and platform-reported conversions per platform.

## Steps

1. Fix the period, long enough for the sales cycle (a quarter or more for B2B).
2. Call `salesforce_leads_by_source` with `created_from`: leads per source and their status split (open, qualified, converted, unqualified). Compute the conversion rate per source.
3. Call `salesforce_revenue_by_source` with the close date window: won revenue per source. Call it again with `include_open: true` to show the pipeline each source feeds.
4. Call `salesforce_list_campaigns` with `is_active` as needed, and `salesforce_get_campaign` for the main ones: budgeted and actual cost, leads, opportunities and won amount recorded in Salesforce.
5. Call `pilot_cross_platform_overview` over the same number of days for ad spend per platform. Map platforms to lead sources only where the naming clearly matches; say where it does not.
6. Compute cost per lead, cost per converted lead and revenue per unit of spend per channel, in one currency only.
7. Flag sources with many leads but no revenue, sources left empty ("Other", blank), and paid channels whose leads Salesforce does not tag.

## Output

1. **Summary**: the channels that bring revenue, and the ones that bring only leads.
2. **Table by source**: leads, conversion rate, won revenue, open pipeline, spend, cost per converted lead.
3. **Salesforce campaigns**: cost and results as recorded in the CRM.
4. **Tracking gaps**: untagged or blank sources.
5. **Next steps**: budget and tracking changes for a person to decide.

## Rules

- Lead source is what the CRM recorded, usually first touch: say so.
- Never sum or divide amounts in different currencies.
- Leads created at the end of the period may not have closed yet: say so.

## Examples

- "Which channels bring leads that actually close in Salesforce?"
- "Quel chiffre d'affaires signé vient de LinkedIn et de Google Ads ?"
- "Compare le coût par lead converti par canal ce trimestre."
