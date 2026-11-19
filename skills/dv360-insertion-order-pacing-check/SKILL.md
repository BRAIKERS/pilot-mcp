---
name: dv360-insertion-order-pacing-check
description: "Checks Display & Video 360 insertion order pacing through the Pilot MCP server: budget segments and their dates, pacing setting, spend to date against the budget and the time elapsed, line item budgets and allocation, and the projected end-of-flight spend, then flags under- and over-delivery. Use when the user asks whether a DV360 flight will spend its budget, is ahead or behind, or when a flight ends soon. Triggers: DV360 pacing, insertion order underdelivery, DV360 budget on track. Déclencheurs : rythme DV360, sous-diffusion ordre d'insertion, budget DV360 dépensé, rythme de diffusion programmatique."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Display & Video 360 advertiser attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "dv360_get_advertiser dv360_list_insertion_orders dv360_get_insertion_order dv360_list_line_items dv360_get_line_item dv360_get_reporting dv360_get_report"
  pilot-mcp-read-only: "true"
---

# DV360 insertion order pacing check

Tell the user, insertion order by insertion order, whether the budget will be spent on time, and why not. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the DV360 advertiser.
- **Optional MCP servers:** a team messaging server to send the alert. None is needed.
- **Read-only:** never change a budget or a status. Proposed moves are for a person to apply.

## Tools

- `dv360_get_advertiser`: the advertiser's currency and time zone.
- `dv360_list_insertion_orders`: active insertion orders.
- `dv360_get_insertion_order`: budget segments with dates, pacing and allocation.
- `dv360_list_line_items`: line items of an insertion order.
- `dv360_get_line_item`: one line item with its budget and bidding.
- `dv360_get_reporting`: spend per insertion order and per day.
- `dv360_get_report`: fetches a report that was not ready in time.

## Steps

1. Call `dv360_get_advertiser` and `dv360_list_insertion_orders` with a `filter` on active status.
2. For each, call `dv360_get_insertion_order`: the current budget segment (amount, start, end), pacing period and type, budget unit (currency or impressions).
3. Call `dv360_get_reporting` with `group_bys` `FILTER_INSERTION_ORDER` and `FILTER_DATE`, `metrics` `METRIC_REVENUE_ADVERTISER` and `METRIC_IMPRESSIONS`, from the segment start to yesterday. Use `dv360_get_report` if the rows are not ready.
4. Compute per insertion order: spend to date, share of the budget spent, share of the flight elapsed, average daily spend over the last 7 days, and projected spend at the segment end at that rate.
5. Flag: behind (projection under 90 % of budget), ahead (projection over 105 %, or budget gone before the end), ending within 7 days with budget left.
6. For flagged insertion orders, call `dv360_list_line_items` and `dv360_get_line_item`: line items capped by their own budget, bids too low to win, flights that ended early, automatic allocation that starves some lines.

## Output

1. **Verdict**: how many insertion orders are on track, behind or ahead.
2. **Table**: insertion order, budget, spent, share of flight elapsed, projection, status.
3. **Causes**: for each flagged one, the likely cause at line item level.
4. **Proposed moves**: budget, bid or flight changes, for a person to apply.

## Rules

- State the days the projection uses and the advertiser time zone.
- Impression budgets and currency budgets are never mixed.
- A projection is an estimate at the recent rate: say so.

## Examples

- "Are our DV360 insertion orders on pace this month?"
- "Quels ordres d'insertion DV360 ne vont pas dépenser leur budget ?"
- "Will the Christmas flight on DV360 spend everything by the 24th?"
