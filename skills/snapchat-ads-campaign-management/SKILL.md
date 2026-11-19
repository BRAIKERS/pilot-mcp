---
name: snapchat-ads-campaign-management
description: "Manages Snapchat Ads campaigns, ad squads and ads through the Pilot MCP server, with the user's explicit approval before every write: pause or resume, change a daily budget, a spend cap, a bid or an end date, rename, and create campaigns, ad squads and ads in paused state. Reads the current state first and shows the before and after. Use when the user asks to change something in a Snapchat Ads account. Triggers: pause Snapchat campaign, change Snapchat budget, create Snap ad. Déclencheurs : mettre en pause une campagne Snapchat, changer le budget Snapchat, créer une publicité Snap, modifier une enchère Snapchat."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Snapchat ad account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "search snapchat_get_campaign snapchat_list_campaigns snapchat_list_ad_squads snapchat_get_ad_squad snapchat_list_ads snapchat_get_ad snapchat_update_campaign snapchat_update_ad_squad snapchat_update_ad snapchat_create_campaign snapchat_create_ad_squad snapchat_create_ad"
  pilot-mcp-read-only: "false"
---

# Snapchat Ads campaign management

Apply the changes the user asks for in a Snapchat Ads account, one at a time, each after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Snapchat ad account.
- **Optional MCP servers:** a team messaging server to log the changes made. None is needed.
- **Writes:** the write tools need the paid Pilot MCP plan and a workspace role allowed to write. If a write tool is not served, say so and return the list of changes for a person to apply by hand in Snapchat Ads Manager.

## Tools

- `search`: finds campaigns of the workspace by name.
- `snapchat_get_campaign`: one campaign in full, including its ad account ID.
- `snapchat_list_campaigns`: campaigns of an ad account.
- `snapchat_list_ad_squads`: ad squads of an ad account.
- `snapchat_get_ad_squad`: one ad squad in full.
- `snapchat_list_ads`: ads of an ad account.
- `snapchat_get_ad`: one ad in full.
- `snapchat_update_campaign`: changes name, status, daily budget, end time or lifetime spend cap (write).
- `snapchat_update_ad_squad`: changes name, status, bid, daily budget, end time or targeting (write).
- `snapchat_update_ad`: changes name, status or creative of an ad (write).
- `snapchat_create_campaign`: creates a campaign (write).
- `snapchat_create_ad_squad`: creates an ad squad in a campaign, paused by default (write).
- `snapchat_create_ad`: creates an ad in an ad squad from an existing creative, paused by default (write).

## Confirmation

Every write changes a live ad account and can move money. For each change:

1. Read the current state first with `snapchat_get_campaign`, `snapchat_get_ad_squad` or `snapchat_get_ad`.
2. Show the person the exact object (name, ID, ad account), the current value and the proposed value, with the currency for any amount.
3. Wait for an explicit yes for that write. A yes covers one write only.
4. Make one write at a time.
5. Create every new campaign, ad squad and ad with status `PAUSED`, passing it explicitly.
6. Never delete anything. If the person asks for a deletion, say that this skill pauses instead, and that a deletion must be asked for separately, naming the object.
7. After the write, read the object again and show the before and after.
8. If a write is refused or fails, stop, report the message, and do not attempt the next change.

## Steps

1. Get the ad account ID from the user, or from `snapchat_get_campaign` on a campaign found with `search`. Confirm it with the user.
2. Find the objects named by the user with `snapchat_list_campaigns`, `snapchat_list_ad_squads` or `snapchat_list_ads`. If a name matches several objects, ask which one.
3. Turn the request into a numbered list of single changes and show it before any write.
4. Apply each change under the Confirmation rules: campaign status, budget, end time or spend cap with `snapchat_update_campaign`; ad squad status, bid, budget or targeting with `snapchat_update_ad_squad`; ad status or creative with `snapchat_update_ad`. Snapchat replaces the whole object: Pilot reads it and sends it back with only the requested fields changed.
5. For a new setup: `snapchat_create_campaign` with `status: "PAUSED"`, then `snapchat_create_ad_squad`, then `snapchat_create_ad` with an existing `creative_id`. Each step is its own confirmed write. Leave activation to a separate, confirmed change.
6. End with the log of changes made and not made.

## Output

1. **Plan**: the numbered list of changes, before any write.
2. **Log**: for each change, the object, before, after and the time it was made.
3. **Not done**: changes refused, failed or left for a person, with the reason.

## Rules

- Budgets, bids and spend caps are in micro units of the account currency: 1,000,000 micro units make one unit. Show both values.
- Times are ISO 8601 with a time zone: confirm the time zone with the user.
- Never chain several writes on one yes.

## Examples

- "Pause the Snapchat ad squad Retargeting 18-24."
- "Monte le budget quotidien de la campagne Snap Rentrée à 80 euros."
- "Crée une campagne Snapchat en pause avec un groupe d'annonces et la créa Hiver."
