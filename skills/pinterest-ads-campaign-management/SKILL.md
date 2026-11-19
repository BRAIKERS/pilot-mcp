---
name: pinterest-ads-campaign-management
description: "Manages Pinterest Ads campaigns, ad groups and ads through the Pilot MCP server, with the user's explicit approval before every write: pause or resume, change a budget or a bid, rename, and create new campaigns, ad groups or ads in paused state. Reads the current state first and shows the before and after of each change. Use when the user asks to change something in a Pinterest Ads account. Triggers: pause Pinterest campaign, change Pinterest budget, create Pinterest campaign. Déclencheurs : mettre en pause une campagne Pinterest, changer le budget Pinterest, créer une campagne Pinterest, modifier une enchère Pinterest."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Pinterest ad account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "pinterest_ad_accounts_list pinterest_campaigns_list pinterest_campaigns_get pinterest_ad_groups_list pinterest_ad_groups_get pinterest_ads_list pinterest_ads_get pinterest_ad_groups_bid_floor pinterest_campaigns_update pinterest_ad_groups_update pinterest_ads_update pinterest_campaigns_create pinterest_ad_groups_create"
  pilot-mcp-read-only: "false"
---

# Pinterest Ads campaign management

Apply the changes the user asks for in a Pinterest Ads account, one at a time, each after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Pinterest ad account.
- **Optional MCP servers:** a team messaging server to log the changes made. None is needed.
- **Writes:** the write tools need the paid Pilot MCP plan and a workspace role allowed to write. Archiving needs the administrator role. If a write tool is not served, say so and return the list of changes for a person to apply by hand in Pinterest Ads Manager.

## Tools

- `pinterest_ad_accounts_list`: the ad accounts available, with their IDs.
- `pinterest_campaigns_list`: campaigns with status, objective and spend caps.
- `pinterest_campaigns_get`: one campaign in full.
- `pinterest_ad_groups_list`: ad groups with status, budget, bid and targeting.
- `pinterest_ad_groups_get`: one ad group in full.
- `pinterest_ads_list`: ads with status and review state.
- `pinterest_ads_get`: one ad in full.
- `pinterest_ad_groups_bid_floor`: the minimum bid Pinterest accepts for a configuration.
- `pinterest_campaigns_update`: changes status, name or spend caps of campaigns (write).
- `pinterest_ad_groups_update`: changes status, name, budget, bid or schedule of ad groups (write).
- `pinterest_ads_update`: changes status or name of ads (write).
- `pinterest_campaigns_create`: creates campaigns (write).
- `pinterest_ad_groups_create`: creates ad groups in a campaign (write).

## Confirmation

Every write changes a live ad account and can move money. For each change:

1. Read the current state first with `pinterest_campaigns_get`, `pinterest_ad_groups_get` or `pinterest_ads_get`.
2. Show the person the exact object (name, ID, ad account), the current value and the proposed value, with the currency for any amount.
3. Wait for an explicit yes for that write. A yes covers one write only.
4. Make one write at a time, with one object in the array passed to the update tool.
5. Create every new campaign and ad group with status `PAUSED`.
6. Never archive or otherwise remove anything unless the person asks for it in so many words and names the object. Pinterest has no deletion for campaigns, ad groups and ads: archiving (status `ARCHIVED`) is final, so prefer `PAUSED`.
7. After the write, read the object again and show the before and after.
8. If a write is refused or fails, stop, report the message, and do not attempt the next change.

## Steps

1. Call `pinterest_ad_accounts_list` and confirm the account with the user. Use its ID as `ad_account_id`.
2. Find the objects named by the user with `pinterest_campaigns_list`, `pinterest_ad_groups_list` or `pinterest_ads_list`. If a name matches several objects, ask which one.
3. Turn the request into a numbered list of single changes and show it before any write.
4. For a bid change, call `pinterest_ad_groups_bid_floor` and refuse a bid under the floor.
5. Apply each change under the rules of the Confirmation section: status, name, budget or bid with `pinterest_ad_groups_update` or `pinterest_campaigns_update`, ad status with `pinterest_ads_update`.
6. For a new campaign, call `pinterest_campaigns_create` with status `PAUSED`, then `pinterest_ad_groups_create` in it with status `PAUSED`. Leave activation to a separate, confirmed change.
7. End with the log of changes made and changes not made.

## Output

1. **Plan**: the numbered list of changes, before any write.
2. **Log**: for each change, the object, before, after and the time it was made.
3. **Not done**: changes refused, failed or left for a person, with the reason.

## Rules

- Budget and bid fields are in micro units of the account currency: 1,000,000 micro units make one unit. Show both the micro value and the readable amount.
- Never act on an object in another ad account than the one confirmed.
- Never chain several writes on one yes.

## Examples

- "Pause the Pinterest campaign Summer Sale."
- "Passe le budget quotidien du groupe d'annonces Déco à 40 euros."
- "Crée une campagne Pinterest de trafic, en pause, pour la nouvelle collection."
