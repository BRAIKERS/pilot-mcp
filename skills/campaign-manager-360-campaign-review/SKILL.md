---
name: campaign-manager-360-campaign-review
description: "Reviews Campaign Manager 360 campaigns in read-only mode through the Pilot MCP server: campaigns with dates and archived state, placements by site with status, pricing, size and dates, and the impressions, clicks, click-through rate and Floodlight conversions by campaign, placement, site or ad over the period. Use when an agency or advertiser wants to know how ad-served campaigns deliver across publishers, or which placements underperform. Triggers: CM360 campaign review, Campaign Manager placements, ad server report. Déclencheurs : revue Campaign Manager 360, emplacements CM360, rapport ad server, performance par site CM360."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Campaign Manager 360 advertiser attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "campaign_manager_360_get_advertiser campaign_manager_360_list_campaigns campaign_manager_360_list_placements campaign_manager_360_list_floodlight_activities campaign_manager_360_get_report_data"
  pilot-mcp-read-only: "true"
---

# Campaign Manager 360 campaign review

Tell the user how their Campaign Manager 360 campaigns deliver, placement by placement and site by site, and what needs attention. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the CM360 advertiser.
- **Optional MCP servers:** a spreadsheet server to export the tables. None is needed.
- **Read-only:** never change a campaign, placement or ad.

## Tools

- `campaign_manager_360_get_advertiser`: the advertiser, its status and Floodlight configuration.
- `campaign_manager_360_list_campaigns`: campaigns with dates and archived state.
- `campaign_manager_360_list_placements`: placements with campaign, site, status, pricing, dates and size.
- `campaign_manager_360_list_floodlight_activities`: the conversions CM360 counts.
- `campaign_manager_360_get_report_data`: a report returned as rows, with chosen dimensions and metrics.

## Steps

1. Call `campaign_manager_360_get_advertiser` with the advertiser ID linked to the workspace (in digits).
2. Call `campaign_manager_360_list_campaigns` with `archived: false`. Note campaigns whose end date has passed and campaigns starting soon.
3. Call `campaign_manager_360_list_placements` for the active campaigns: placements inactive while their campaign runs, placements whose dates end before the campaign, pricing types per site.
4. Call `campaign_manager_360_list_floodlight_activities` to name the conversions.
5. Call `campaign_manager_360_get_report_data` over the period with `dimensions` `campaign` and `placement` (and `site` or `ad` when useful), `metrics` `impressions`, `clicks`, `totalConversions`, `activityClickThroughConversions`, `activityViewThroughConversions`, and `totalConversionsRevenue` when sales are tracked. Raise `max_pages` or follow `page_token` until every row is read.
6. Rank placements by impressions and by conversions; flag placements with impressions but no clicks, and placements that deliver nothing although active.

## Output

1. **Summary**: active campaigns, delivery, and the main issue.
2. **Campaigns**: a table with dates, impressions, clicks, click-through rate and conversions.
3. **Placements**: the best and weakest, by site.
4. **Housekeeping**: inactive or expired placements in live campaigns.
5. **Next steps**: for the trafficker or the media team.

## Rules

- Keep click-through and view-through conversions apart.
- A ratio with an empty denominator is not available, not zero.
- Name the period and say which Floodlight activities are counted.

## Examples

- "How are our CM360 campaigns delivering by site?"
- "Quels emplacements Campaign Manager ne livrent rien ?"
- "Give me impressions and conversions by placement for last month."
