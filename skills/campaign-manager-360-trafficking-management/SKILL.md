---
name: campaign-manager-360-trafficking-management
description: "Applies trafficking changes in Campaign Manager 360 through the Pilot MCP server, with the user's explicit approval before every write: activate or pause an ad, move an ad's end of delivery, activate or deactivate a placement, and rename a campaign, move its end date or change its comment. Each tool reads the object before and after. Use when a trafficker or media team asks to switch ads or placements on or off, or to extend a flight. Triggers: pause CM360 ad, extend CM360 campaign, deactivate placement. Déclencheurs : mettre en pause une annonce CM360, prolonger une campagne Campaign Manager, désactiver un emplacement CM360."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Campaign Manager 360 advertiser attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "campaign_manager_360_get_advertiser campaign_manager_360_list_campaigns campaign_manager_360_list_placements campaign_manager_360_get_report_data campaign_manager_360_update_ad_status campaign_manager_360_update_ad_end_time campaign_manager_360_update_placement_status campaign_manager_360_update_campaign"
  pilot-mcp-read-only: "false"
---

# Campaign Manager 360 trafficking management

Apply the trafficking changes the user asks for in Campaign Manager 360, one at a time, each after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the CM360 advertiser.
- **Optional MCP servers:** a team messaging server to notify the publishers or the media team. None is needed.
- **Writes:** the write tools need the paid Pilot MCP plan and a workspace role allowed to write. If a write tool is not served, say so and return the list of changes for a trafficker to apply by hand in Campaign Manager 360.

## Tools

- `campaign_manager_360_get_advertiser`: the advertiser linked to the workspace.
- `campaign_manager_360_list_campaigns`: campaigns with dates and archived state.
- `campaign_manager_360_list_placements`: placements with status and dates.
- `campaign_manager_360_get_report_data`: finds ads by name and ID (dimensions `ad` and `adId`) and shows their recent delivery.
- `campaign_manager_360_update_ad_status`: activates or pauses an ad (write).
- `campaign_manager_360_update_ad_end_time`: moves an ad's end of delivery (write).
- `campaign_manager_360_update_placement_status`: activates or deactivates a placement (write).
- `campaign_manager_360_update_campaign`: changes a campaign's name, end date or comment (write).

## Confirmation

Every write changes what serves on publisher sites. For each change:

1. Read the current state first: `campaign_manager_360_list_campaigns` or `campaign_manager_360_list_placements` filtered on the object, or `campaign_manager_360_get_report_data` for an ad.
2. Show the person the exact object (name, ID, advertiser), the current value and the proposed value.
3. Wait for an explicit yes for that write. A yes covers one write only.
4. Make one write at a time.
5. Never archive anything: archived objects cannot be reopened, and these tools refuse them anyway.
6. Each tool reads the object before and after: show the before and after it returns.
7. If a write is refused or fails, stop, report the message, and do not attempt the next change.

## Steps

1. Call `campaign_manager_360_get_advertiser` and confirm the advertiser (ID in digits).
2. Find the objects: `campaign_manager_360_list_campaigns` with `search_string`, `campaign_manager_360_list_placements` with `campaign_ids`, and ads with `campaign_manager_360_get_report_data` (`dimensions` `campaign`, `ad`, `adId`, `metrics` `impressions`) over the last 7 days.
3. Turn the request into a numbered list of single changes and show it before any write.
4. Ads: `campaign_manager_360_update_ad_status` with `active` true or false; `campaign_manager_360_update_ad_end_time` with an RFC 3339 instant after the ad's start (for example 2026-12-31T23:59:00Z).
5. Placements: `campaign_manager_360_update_placement_status` with `PLACEMENT_STATUS_ACTIVE` or `PLACEMENT_STATUS_INACTIVE`.
6. Campaigns: `campaign_manager_360_update_campaign` with a new `end_date` (YYYY-MM-DD, not before the start), `name` (unique for the advertiser) or `comment`. When a campaign is extended, remind the user that ads and placements keep their own end dates.
7. End with the log of changes made and not made.

## Output

1. **Plan**: the numbered list of changes, before any write.
2. **Log**: for each change, the object, before, after and the time it was made.
3. **Not done**: changes refused, failed or left for a trafficker, with the reason.

## Rules

- Times are UTC in RFC 3339: convert from the user's time zone and show both.
- Never chain several writes on one yes.
- Never act on an advertiser other than the one confirmed.

## Examples

- "Pause the CM360 ad Black Friday 300x250."
- "Prolonge la campagne Campaign Manager Hiver jusqu'au 15 janvier."
- "Désactive l'emplacement CM360 du site Exemple Actu."
