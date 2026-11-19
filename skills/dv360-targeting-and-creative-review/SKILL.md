---
name: dv360-targeting-and-creative-review
description: "Reviews the targeting and creatives of Display & Video 360 line items through the Pilot MCP server: assigned targeting by type (geography, audiences, devices, brand safety, inventory, age and more), creatives assigned to each line item with their type, size and status, and delivery by line item and device, then flags gaps, overlaps and creative issues. Use when the user asks who DV360 line items target, whether brand safety is in place, or which creatives run. Triggers: DV360 targeting review, DV360 brand safety, DV360 creatives check. Déclencheurs : ciblage DV360, sécurité de marque DV360, créations DV360, revue des lignes DV360."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Display & Video 360 advertiser attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "dv360_list_line_items dv360_get_line_item dv360_list_assigned_targeting dv360_search_targeting_options dv360_list_creatives dv360_get_creative dv360_get_reporting dv360_get_report"
  pilot-mcp-read-only: "true"
---

# DV360 targeting and creative review

Tell the user who each DV360 line item targets, what protects the brand, which creatives run, and what to fix. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the DV360 advertiser.
- **Optional MCP servers:** none is needed.
- **Read-only:** never change targeting or creatives. Changes are for a person to apply.

## Tools

- `dv360_list_line_items`: line items of the advertiser or of an insertion order.
- `dv360_get_line_item`: one line item with its creative IDs.
- `dv360_list_assigned_targeting`: the targeting of a line item for one targeting type.
- `dv360_search_targeting_options`: resolves a region, point of interest or business chain by text.
- `dv360_list_creatives`: creatives with type, status and dimensions.
- `dv360_get_creative`: one creative with its assets.
- `dv360_get_reporting`: delivery by line item and device type.
- `dv360_get_report`: fetches a report that was not ready in time.

## Steps

1. Call `dv360_list_line_items` with a `filter` on active status, or for the insertion order the user names.
2. For each active line item, call `dv360_list_assigned_targeting` for the types that matter: `TARGETING_TYPE_GEO_REGION`, `TARGETING_TYPE_AUDIENCE_GROUP`, `TARGETING_TYPE_DEVICE_TYPE`, `TARGETING_TYPE_AGE_RANGE`, and brand safety and inventory types such as `TARGETING_TYPE_DIGITAL_CONTENT_LABEL_EXCLUSION`, `TARGETING_TYPE_SENSITIVE_CATEGORY_EXCLUSION` and `TARGETING_TYPE_INVENTORY_SOURCE`.
3. Use `dv360_search_targeting_options` to turn region names the user gives into IDs, and to name IDs found in the targeting when needed.
4. Compare line items of the same insertion order: overlapping audiences or geographies compete with each other; a line item with no brand safety exclusion is a risk.
5. Call `dv360_get_line_item` for creative IDs, then `dv360_list_creatives` and `dv360_get_creative`: creatives disapproved or paused, sizes missing for the inventory, video without a matching format, line items with one creative only.
6. Call `dv360_get_reporting` over the last 30 days with `group_bys` `FILTER_LINE_ITEM` and `FILTER_DEVICE_TYPE` to see where impressions land against the intended targeting.

## Output

1. **Summary**: targeting coverage, brand safety status, creative health.
2. **Targeting table**: line item, geography, audiences, devices, brand safety, inventory.
3. **Overlaps and gaps**: line items that compete, line items without protection.
4. **Creatives**: per line item, assigned creatives and their status.
5. **Fixes**: in order, for a person to apply.

## Rules

- Report targeting as DV360 returns it; do not assume a default the API does not show.
- A targeting type not read is unknown, not empty: say which types were read.
- Name the period of the delivery figures.

## Examples

- "Do all our DV360 line items have brand safety exclusions?"
- "Qui ciblent nos lignes DV360, et avec quelles créations ?"
- "Quelles lignes DV360 se marchent dessus sur la même audience ?"
