---
name: dv360-performance-report
description: "Builds a Display & Video 360 performance report through the Pilot MCP server from Bid Manager: impressions, clicks, spend, conversions and their costs by campaign, insertion order, line item, day or device type, including connected TV, with the change against the previous period. Use when the user wants DV360 or programmatic numbers for a week, a month or a flight. Triggers: DV360 report, programmatic performance, connected TV results. Déclencheurs : rapport DV360, performance programmatique, résultats TV connectée, rapport Display & Video 360."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Display & Video 360 advertiser attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "dv360_get_advertiser dv360_list_campaigns dv360_list_insertion_orders dv360_list_line_items dv360_get_reporting dv360_get_report"
  pilot-mcp-read-only: "true"
---

# DV360 performance report

Give the user a clear Display & Video 360 report for the period asked, from campaign down to line item. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the DV360 advertiser.
- **Optional MCP servers:** a spreadsheet server to export the tables. None is needed.
- **Read-only:** never change a campaign, insertion order or line item. Reports use a temporary query that the tool deletes.

## Tools

- `dv360_get_advertiser`: the advertiser's currency.
- `dv360_list_campaigns`: campaign names and goals.
- `dv360_list_insertion_orders`: insertion order names.
- `dv360_list_line_items`: line item names and types.
- `dv360_get_reporting`: a Bid Manager report in one call.
- `dv360_get_report`: fetches a report that was not ready in time.

## Steps

1. Call `dv360_get_advertiser` for the currency.
2. Fix the period and a comparison period of the same length, as a preset range or `start_date` and `end_date` in `data_range`.
3. Call `dv360_get_reporting` for each period with `group_bys` such as `FILTER_CAMPAIGN`, `FILTER_INSERTION_ORDER`, `FILTER_LINE_ITEM`, and `metrics` such as `METRIC_IMPRESSIONS`, `METRIC_CLICKS`, `METRIC_REVENUE_ADVERTISER` plus the conversion metrics used. Add `FILTER_DATE` for the daily trend.
4. When the user cares about screens, add `FILTER_DEVICE_TYPE`: the response then gives connected TV impressions and spend, with zero stated explicitly.
5. If a call returns `query_id` and `report_id` instead of rows, call `dv360_get_report` until DONE. If rows are truncated, say so.
6. Name rows with `dv360_list_campaigns`, `dv360_list_insertion_orders` and `dv360_list_line_items`.
7. Recompute click-through rate, cost per thousand impressions and cost per conversion on the totals.

## Output

1. **Headline**: impressions, spend, clicks, conversions and costs, with the change.
2. **Campaigns and insertion orders**: tables ranked by spend.
3. **Line items**: the best and weakest on the campaign's KPI.
4. **Screens**: split by device type, connected TV included, when asked.
5. **What to watch**: two or three points.

## Rules

- Name the period and the advertiser currency.
- A ratio with an empty denominator is not available, not zero.
- Say when the report was truncated.

## Examples

- "Give me last month's DV360 report by insertion order."
- "Fais le rapport DV360 de la semaine, avec la part de TV connectée."
- "Quelles lignes DV360 ont le meilleur coût par conversion ?"
