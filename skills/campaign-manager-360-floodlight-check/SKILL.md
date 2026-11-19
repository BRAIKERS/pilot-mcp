---
name: campaign-manager-360-floodlight-check
description: "Checks Campaign Manager 360 Floodlight conversion tracking through the Pilot MCP server: the advertiser's Floodlight configuration (own or shared), every activity with its type (counter or sale), counting method and status, and the conversions each activity recorded by click and by view over the period, then flags silent, duplicated or misconfigured activities. Use when CM360 conversions look wrong or before a campaign launch. Triggers: Floodlight check, CM360 conversions tracking, Floodlight activities audit. Déclencheurs : vérification Floodlight, suivi des conversions CM360, activités Floodlight, balises Floodlight."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Campaign Manager 360 advertiser attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "campaign_manager_360_get_advertiser campaign_manager_360_list_floodlight_activities campaign_manager_360_list_campaigns campaign_manager_360_get_report_data"
  pilot-mcp-read-only: "true"
---

# Campaign Manager 360 Floodlight check

Tell the user whether Floodlight counts their conversions correctly, activity by activity, and what to fix. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the CM360 advertiser.
- **Optional MCP servers:** a tag manager server to check the tags on the site. None is needed.
- **Read-only:** never change an activity, a campaign or an ad.

## Tools

- `campaign_manager_360_get_advertiser`: the advertiser and the Floodlight configuration it uses, its own or a shared one.
- `campaign_manager_360_list_floodlight_activities`: activities with group, type, counting method and status.
- `campaign_manager_360_list_campaigns`: active campaigns, to know what relies on the activities.
- `campaign_manager_360_get_report_data`: conversions per activity and per day.

## Steps

1. Call `campaign_manager_360_get_advertiser`. If the Floodlight configuration belongs to another advertiser, say so: activities are shared and changes affect both.
2. Call `campaign_manager_360_list_floodlight_activities`. Note type (COUNTER or SALE), counting method (for example standard, unique or per session for counters; transactions or items sold for sales), and status.
3. Call `campaign_manager_360_get_report_data` over the last 30 days with `dimensions` `activity` and `date`, and `metrics` `totalConversions`, `activityClickThroughConversions`, `activityViewThroughConversions` and `totalConversionsRevenue`.
4. Flag: active activities with no conversion in 30 days; activities that stopped on a given day (a site release often explains it); sale activities with conversions but no revenue; two activities that seem to count the same action; counters set to count every event where one per user was intended.
5. Call `campaign_manager_360_list_campaigns` to list the live campaigns that depend on the flagged activities.

## Output

1. **Verdict**: tracking reliable, partial or broken, in one sentence.
2. **Activities**: a table with name, type, counting method, status, conversions and revenue.
3. **Issues**: silent, stopped, duplicated or misconfigured activities, with the evidence.
4. **Impact**: live campaigns affected.
5. **Fixes**: in order, for the person who manages the tags.

## Rules

- No conversion is not proof of a broken tag: low volume can explain it. Say which is likely and why.
- Keep click-through and view-through conversions apart.
- Name the period.

## Examples

- "Are our Floodlight activities counting correctly?"
- "Pourquoi l'activité Floodlight Achat ne remonte plus rien depuis mardi ?"
- "Liste les activités Floodlight qui n'ont rien compté ce mois-ci."
