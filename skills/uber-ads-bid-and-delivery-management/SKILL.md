---
name: uber-ads-bid-and-delivery-management
description: "Manages Uber Ads (Uber Eats ads) delivery, budgets and bids through the Pilot MCP server, with the user's explicit approval before every write: pause or reactivate campaigns, ad groups and ads, change a campaign or ad group budget (daily, weekly or total), and change the manual bid of an ad group. Reads the current state and recent results first, and shows the before and after. Use when the user asks to change something in Uber Ads. Triggers: pause Uber Eats ads, change Uber Ads budget, Uber Ads bid. Déclencheurs : mettre en pause les publicités Uber Eats, changer le budget Uber Ads, modifier l'enchère Uber Ads."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with an Uber Ads account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "uber_ads_get_ad_account uber_ads_list_campaigns uber_ads_list_ad_groups uber_ads_list_ads uber_ads_get_statistics uber_ads_update_campaign_status uber_ads_update_ad_group_status uber_ads_update_ad_status uber_ads_update_campaign_budget uber_ads_update_ad_group_budget uber_ads_update_ad_group_bid"
  pilot-mcp-read-only: "false"
---

# Uber Ads bid and delivery management

Apply the delivery, budget and bid changes the user asks for on Uber Ads, one at a time, each after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Uber Ads account.
- **Optional MCP servers:** a team messaging server to log the changes made. None is needed.
- **Writes:** the write tools need the paid Pilot MCP plan and a workspace role allowed to write. If a write tool is not served, say so and return the list of changes for a person to apply by hand in Uber Ads Manager.

## Tools

- `uber_ads_get_ad_account`: the account and its currency.
- `uber_ads_list_campaigns`: campaigns with status and budget.
- `uber_ads_list_ad_groups`: ad groups with status, budget, bid and bidding type.
- `uber_ads_list_ads`: ads of an ad group, with status.
- `uber_ads_get_statistics`: recent results, to ground each change.
- `uber_ads_update_campaign_status`: pauses or reactivates a campaign (write).
- `uber_ads_update_ad_group_status`: pauses or reactivates an ad group (write).
- `uber_ads_update_ad_status`: pauses or reactivates an ad (write).
- `uber_ads_update_campaign_budget`: changes a campaign budget and its unit (write).
- `uber_ads_update_ad_group_budget`: changes an ad group budget and its unit (write).
- `uber_ads_update_ad_group_bid`: changes the manual bid of an ad group (write).

## Confirmation

Every write changes live delivery and can move money. For each change:

1. Read the current state first with `uber_ads_list_campaigns`, `uber_ads_list_ad_groups` or `uber_ads_list_ads`, filtered on the object.
2. Show the person the exact object (name, ID, account), the current value and the proposed value, in the account currency and with the budget unit.
3. Wait for an explicit yes for that write. A yes covers one write only. Reactivating puts money back at stake: say so.
4. Make one write at a time.
5. This skill deletes nothing and never removes a budget: a budget is never set to zero.
6. After the write, read the object again and show the before and after.
7. If a write is refused or fails, stop, report the message, and do not attempt the next change.

## Steps

1. Call `uber_ads_get_ad_account` and confirm the account and currency.
2. Find the objects named by the user with the list tools; ad groups and ads need their `campaign_id`. If a name matches several objects, ask which one.
3. For a budget or bid change, call `uber_ads_get_statistics` with `level: "AD_GROUP"` or `"CAMPAIGN"` over the last 14 days to show spend, orders and sales next to the proposal.
4. A bid change is refused on ad groups with automatic or goal-based bidding: check the bidding type first and say so instead of trying.
5. Turn the request into a numbered list of single changes and show it before any write, then apply each one under the Confirmation rules.
6. End with the log of changes made and not made.

## Output

1. **Plan**: the numbered list of changes, before any write.
2. **Log**: for each change, the object, before, after and the time it was made.
3. **Not done**: changes refused, failed or left for a person, with the reason.

## Rules

- Amounts are in units of the account currency (12.5 means 12.50): name it, with the budget unit (daily, weekly or total).
- Never chain several writes on one yes.
- Never act on an object of another account than the one confirmed.

## Examples

- "Pause the Uber Eats ads of the Lyon store ad group."
- "Monte le budget hebdomadaire de la campagne Uber Ads Midi à 300 euros."
- "Baisse l'enchère du groupe d'annonces Uber Recherche à 0,80 euro."
