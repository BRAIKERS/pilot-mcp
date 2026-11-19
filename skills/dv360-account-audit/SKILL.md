---
name: dv360-account-audit
description: "Audits a Display & Video 360 advertiser in read-only mode through the Pilot MCP server: campaigns with goal and flight, insertion orders with budget, pacing and KPI, line items with type, budget, bidding and creatives, assigned targeting, creatives and their status, and delivery over the last 30 days from a Bid Manager report, then returns a prioritized findings report. Use when the user asks to audit or health-check a DV360 advertiser. Triggers: DV360 audit, Display & Video 360 review, programmatic account health check. Déclencheurs : audit DV360, revue Display & Video 360, bilan de santé programmatique, audit DV360 annonceur."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Display & Video 360 advertiser attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "dv360_get_advertiser dv360_list_campaigns dv360_get_campaign dv360_list_insertion_orders dv360_get_insertion_order dv360_list_line_items dv360_get_line_item dv360_list_assigned_targeting dv360_list_creatives dv360_get_reporting dv360_get_report pilot_data_freshness"
  pilot-mcp-read-only: "true"
---

# DV360 account audit

Audit one Display & Video 360 advertiser and hand back a short, prioritized list of findings, each backed by figures. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the DV360 advertiser.
- **Optional MCP servers:** a file storage server to save the report. None is needed.
- **Read-only:** never create, update, archive or delete anything. `dv360_get_reporting` creates a temporary report query and deletes it once read; it changes no campaign.

## Tools

- `dv360_get_advertiser`: the advertiser's configuration, currency and status.
- `dv360_list_campaigns`: campaigns with goal, status and flight dates.
- `dv360_get_campaign`: one campaign with its goal and frequency cap.
- `dv360_list_insertion_orders`: insertion orders, optionally per campaign.
- `dv360_get_insertion_order`: one insertion order with budget segments, pacing and optimization objective.
- `dv360_list_line_items`: line items, optionally per insertion order.
- `dv360_get_line_item`: one line item with type, budget, flight, bidding and creative IDs.
- `dv360_list_assigned_targeting`: the targeting of a line item for one targeting type.
- `dv360_list_creatives`: creatives with type, status and dimensions.
- `dv360_get_reporting`: a Bid Manager report in one call, rows included when ready within 40 seconds.
- `dv360_get_report`: fetches a report that was not ready in time.
- `pilot_data_freshness`: whether Pilot's collected DV360 data is current.

## Steps

1. Ask for the advertiser ID if several are linked; it goes as `advertiser_id` (and `account_id` for reports). Call `dv360_get_advertiser` for the currency.
2. Call `pilot_data_freshness` with `platform: "dv360"`.
3. Call `dv360_get_reporting` over the last 30 days with `group_bys` `FILTER_INSERTION_ORDER` and `FILTER_LINE_ITEM`, and `metrics` such as `METRIC_IMPRESSIONS`, `METRIC_CLICKS`, `METRIC_REVENUE_ADVERTISER` and the conversion metrics the advertiser uses. If it returns a `query_id` and `report_id` instead of rows, call `dv360_get_report` until DONE.
4. Read the structure: `dv360_list_campaigns`, `dv360_list_insertion_orders`, `dv360_list_line_items` with `filter` on active status. Flag active line items under ended insertion orders, flights already over, line items with no creative.
5. For the insertion orders that spend most, call `dv360_get_insertion_order`: budget segments that end before the campaign, pacing set to ahead with a small budget, KPI that does not match the campaign goal (`dv360_get_campaign`).
6. For the line items that spend most, call `dv360_get_line_item` and `dv360_list_assigned_targeting` for `TARGETING_TYPE_GEO_REGION`, `TARGETING_TYPE_DEVICE_TYPE` and brand safety types: missing brand safety, very broad or very narrow geography, frequency caps absent.
7. Call `dv360_list_creatives`: creatives disapproved or not serving, line items relying on a single creative.
8. Write the report, findings ranked by money at stake.

## Output

1. **Summary**: three lines on the advertiser's state and the top three priorities.
2. **Findings**: a table with the finding, the evidence (figure, period, currency), the stake and the suggested action.
3. **Structure**: campaigns, insertion orders and line items that need cleanup.
4. **Next steps**: actions in order, for a person to apply in DV360.

## Rules

- Micros are divided by 1,000,000; name the advertiser currency.
- A metric absent from the report is absent, not zero.
- Never sum advertisers in different currencies.

## Examples

- "Audit our DV360 advertiser."
- "Fais l'audit du compte Display & Video 360, en priorisant ce qui dépense le plus."
- "Nos ordres d'insertion DV360 sont-ils bien configurés ?"
