---
name: apple-ads-campaign-management
description: "Manages Apple Ads campaigns, ad groups and ads through the Pilot MCP server, with the user's explicit approval before every write: pause or enable, change a daily budget, a default bid or a cost per acquisition goal, rename, and create campaigns and ad groups in paused state. Reads the current state first and shows the before and after. Use when the user asks to change something in an Apple Ads or Apple Search Ads account. Triggers: pause Apple Ads campaign, change Apple Search Ads budget, Apple Ads bid change. Déclencheurs : mettre en pause une campagne Apple Ads, changer le budget Apple Search Ads, modifier une enchère Apple Ads, créer une campagne Apple."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with an Apple Ads organization attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "apple_ads_apple_ads_list_campaigns apple_ads_apple_ads_get_campaign apple_ads_apple_ads_list_adgroups apple_ads_apple_ads_get_adgroup apple_ads_apple_ads_list_ads apple_ads_apple_ads_get_ad apple_ads_apple_ads_report_campaigns apple_ads_apple_ads_update_campaign apple_ads_apple_ads_update_adgroup apple_ads_apple_ads_update_ad apple_ads_apple_ads_create_campaign apple_ads_apple_ads_create_adgroup"
  pilot-mcp-read-only: "false"
---

# Apple Ads campaign management

Apply the changes the user asks for in Apple Ads, one at a time, each after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Apple Ads organization.
- **Optional MCP servers:** a team messaging server to log the changes made. None is needed.
- **Writes:** the write tools need the paid Pilot MCP plan and a workspace role allowed to write. If a write tool is not served, say so and return the list of changes for a person to apply by hand in Apple Ads.

## Tools

- `apple_ads_apple_ads_list_campaigns`: campaigns of the organization.
- `apple_ads_apple_ads_get_campaign`: one campaign in full.
- `apple_ads_apple_ads_list_adgroups`: ad groups of a campaign.
- `apple_ads_apple_ads_get_adgroup`: one ad group in full.
- `apple_ads_apple_ads_list_ads`: ads of an ad group.
- `apple_ads_apple_ads_get_ad`: one ad in full.
- `apple_ads_apple_ads_report_campaigns`: recent results, to ground each change.
- `apple_ads_apple_ads_update_campaign`: changes a campaign: status, daily budget, name (write).
- `apple_ads_apple_ads_update_adgroup`: changes an ad group: status, default bid, goal, name (write).
- `apple_ads_apple_ads_update_ad`: changes an ad: status, name (write).
- `apple_ads_apple_ads_create_campaign`: creates a campaign (write).
- `apple_ads_apple_ads_create_adgroup`: creates an ad group in a campaign (write).

## Confirmation

Every write changes a live account and can move money. For each change:

1. Read the current state first with `apple_ads_apple_ads_get_campaign`, `apple_ads_apple_ads_get_adgroup` or `apple_ads_apple_ads_get_ad`.
2. Show the person the exact object (name, ID, organization), the current value and the proposed value, with the currency for any amount.
3. Wait for an explicit yes for that write. A yes covers one write only.
4. Make one write at a time, sending only the fields that change.
5. Create every new campaign and ad group with status `PAUSED`.
6. Never delete anything. If the person asks for a deletion, say that this skill pauses instead, and that a deletion must be asked for separately, naming the object.
7. After the write, read the object again and show the before and after.
8. If a write is refused or fails, stop, report the message, and do not attempt the next change.

## Steps

1. Call `apple_ads_apple_ads_list_campaigns` and confirm the organization and the campaigns concerned. Pass `account_id` when several organizations are linked.
2. Find ad groups and ads with `apple_ads_apple_ads_list_adgroups` and `apple_ads_apple_ads_list_ads`. If a name matches several objects, ask which one.
3. For a budget or bid change, call `apple_ads_apple_ads_report_campaigns` over the last 14 days to show recent spend, installs and cost per install next to the proposal.
4. Turn the request into a numbered list of single changes and show it before any write.
5. Apply each change under the Confirmation rules. Amounts are objects with an amount and the campaign currency.
6. For a new campaign: `apple_ads_apple_ads_create_campaign` (app adamId, countries, daily budget, status `PAUSED`), then `apple_ads_apple_ads_create_adgroup` (default bid, search match choice, status `PAUSED`). Each is its own confirmed write.
7. End with the log of changes made and not made.

## Output

1. **Plan**: the numbered list of changes, before any write.
2. **Log**: for each change, the object, before, after and the time it was made.
3. **Not done**: changes refused, failed or left for a person, with the reason.

## Rules

- Each campaign has its own currency: name it with every amount.
- Never chain several writes on one yes.
- Never act on an object of another organization than the one confirmed.

## Examples

- "Pause the Apple Ads campaign Discovery FR."
- "Passe le budget quotidien de la campagne Apple Marque US à 200 dollars."
- "Crée une campagne Apple Ads Concurrents pour l'Allemagne, en pause."
