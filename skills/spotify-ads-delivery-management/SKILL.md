---
name: spotify-ads-delivery-management
description: "Manages Spotify Ads delivery through the Pilot MCP server, with the user's explicit approval before every write: pause or reactivate a campaign, switch an ad set or an ad off or back on, change an ad set budget or bid, and move an ad set end date. Reads the current state and recent results first, and shows the before and after. Use when the user asks to change budgets, bids, dates or delivery on Spotify Ads. Triggers: pause Spotify campaign, change Spotify ad set budget, Spotify ads bid. Déclencheurs : mettre en pause une campagne Spotify, changer le budget Spotify Ads, modifier l'enchère Spotify, prolonger un ensemble d'annonces Spotify."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Spotify Ads account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "spotify_ads_get_ad_account spotify_ads_list_campaigns spotify_ads_list_adsets spotify_ads_list_ads spotify_ads_get_statistics spotify_ads_update_campaign_status spotify_ads_update_ad_set_delivery spotify_ads_update_ad_delivery spotify_ads_update_ad_set_budget spotify_ads_update_ad_set_bid spotify_ads_update_ad_set_end_time"
  pilot-mcp-read-only: "false"
---

# Spotify Ads delivery management

Apply the delivery, budget, bid and date changes the user asks for on Spotify Ads, one at a time, each after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Spotify Ads account.
- **Optional MCP servers:** a team messaging server to log the changes made. None is needed.
- **Writes:** the write tools need the paid Pilot MCP plan and a workspace role allowed to write. If a write tool is not served, say so and return the list of changes for a person to apply by hand in Spotify Ads Manager.

## Tools

- `spotify_ads_get_ad_account`: the account and its currency.
- `spotify_ads_list_campaigns`: campaigns with status.
- `spotify_ads_list_adsets`: ad sets with budget and its type, bid, dates and delivery.
- `spotify_ads_list_ads`: ads with status and delivery switch.
- `spotify_ads_get_statistics`: recent results, to ground each change.
- `spotify_ads_update_campaign_status`: pauses or reactivates a campaign (write).
- `spotify_ads_update_ad_set_delivery`: switches an ad set off or on (write).
- `spotify_ads_update_ad_delivery`: switches an ad off or on (write).
- `spotify_ads_update_ad_set_budget`: changes an ad set budget, keeping its type unless told otherwise (write).
- `spotify_ads_update_ad_set_bid`: changes an ad set bid per thousand impressions (write).
- `spotify_ads_update_ad_set_end_time`: moves an ad set end date (write).

## Confirmation

Every write changes live delivery and can move money. For each change:

1. Read the current state first with `spotify_ads_list_campaigns`, `spotify_ads_list_adsets` or `spotify_ads_list_ads`, filtered on the object.
2. Show the person the exact object (name, ID, account), the current value and the proposed value, in the account currency.
3. Wait for an explicit yes for that write. A yes covers one write only. Reactivating or switching back on puts money back at stake: say so.
4. Make one write at a time.
5. This skill deletes nothing; it only pauses, switches off, or adjusts.
6. After the write, read the object again and show the before and after.
7. If a write is refused or fails, stop, report the message, and do not attempt the next change.

## Steps

1. Call `spotify_ads_get_ad_account` and confirm the account and currency.
2. Find the objects named by the user with the list tools. If a name matches several objects, ask which one.
3. For a budget or bid change, call `spotify_ads_get_statistics` with `level: "AD_SET"` and the ad set ID in `ids` over the last 14 days, to show recent spend, impressions and completion next to the proposal.
4. Turn the request into a numbered list of single changes and show it before any write.
5. Apply each change under the Confirmation rules. End dates are ISO 8601 in UTC (YYYY-MM-DDTHH:MM:SSZ): convert from the user's time zone and show both.
6. End with the log of changes made and not made.

## Output

1. **Plan**: the numbered list of changes, before any write.
2. **Log**: for each change, the object, before, after and the time it was made.
3. **Not done**: changes refused, failed or left for a person, with the reason.

## Rules

- Amounts are in units of the account currency (12.5 means 12.50): name it.
- Never chain several writes on one yes.
- Never act on an object of another account than the one confirmed.

## Examples

- "Switch off the Spotify ad Summer 30s."
- "Prolonge l'ensemble d'annonces Spotify Podcast jusqu'au 31 décembre."
- "Monte le budget de l'ensemble Spotify Audio 25-34 à 500 euros."
