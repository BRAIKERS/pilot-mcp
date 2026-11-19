---
name: microsoft-ads-campaign-management
description: "Manages Microsoft Advertising (Bing Ads) campaigns, ad groups, keywords and shared budgets through the Pilot MCP server, with the user's explicit approval before every write: pause or resume, change a daily or shared budget, an ad group or keyword bid, rename, and create campaigns and ad groups in paused state. Reads the current state first and shows the before and after. Use when the user asks to change something in a Microsoft Ads account. Triggers: pause Bing campaign, change Microsoft Ads budget, Bing bid change. Déclencheurs : mettre en pause une campagne Bing, changer le budget Microsoft Ads, modifier une enchère Bing, créer une campagne Microsoft Ads."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Microsoft Advertising account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "bing_ads_get_ad_accounts bing_ads_list_campaigns bing_ads_get_campaign_by_id bing_ads_list_ad_groups bing_ads_get_ad_group_by_id bing_ads_list_keywords bing_ads_get_keyword_by_id bing_ads_list_budgets bing_ads_update_campaign bing_ads_update_ad_group bing_ads_update_keyword bing_ads_update_budget bing_ads_create_campaign bing_ads_create_ad_group"
  pilot-mcp-read-only: "false"
---

# Microsoft Ads campaign management

Apply the changes the user asks for in a Microsoft Advertising account, one at a time, each after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a team messaging server to log the changes made. None is needed.
- **Writes:** the write tools need the paid Pilot MCP plan and a workspace role allowed to write. If a write tool is not served, say so and return the list of changes for a person to apply by hand in Microsoft Advertising.

## Tools

- `bing_ads_get_ad_accounts`: the accounts available, with their IDs.
- `bing_ads_list_campaigns`: campaigns of the account.
- `bing_ads_get_campaign_by_id`: one campaign in full.
- `bing_ads_list_ad_groups`: ad groups of a campaign.
- `bing_ads_get_ad_group_by_id`: one ad group in full.
- `bing_ads_list_keywords`: keywords of an ad group.
- `bing_ads_get_keyword_by_id`: one keyword in full.
- `bing_ads_list_budgets`: shared budgets.
- `bing_ads_update_campaign`: changes name, status or daily budget of a campaign (write).
- `bing_ads_update_ad_group`: changes name, CPC bid or status of an ad group (write).
- `bing_ads_update_keyword`: changes bid or status of a keyword (write).
- `bing_ads_update_budget`: changes name or amount of a shared budget (write).
- `bing_ads_create_campaign`: creates a campaign (write).
- `bing_ads_create_ad_group`: creates an ad group in a campaign (write).

## Confirmation

Every write changes a live ad account and can move money. For each change:

1. Read the current state first with `bing_ads_get_campaign_by_id`, `bing_ads_get_ad_group_by_id`, `bing_ads_get_keyword_by_id` or `bing_ads_list_budgets`.
2. Show the person the exact object (name, ID, account), the current value and the proposed value, with the currency for any amount.
3. Wait for an explicit yes for that write. A yes covers one write only.
4. Make one write at a time.
5. Create every new campaign and ad group with `status: "Paused"`.
6. Never delete anything. If the person asks for a deletion, say that this skill pauses instead, and that a deletion must be asked for separately, naming the object.
7. After the write, read the object again and show the before and after.
8. If a write is refused or fails, stop, report the message, and do not attempt the next change.

## Steps

1. Call `bing_ads_get_ad_accounts` and confirm the account; use its ID as `target_account_id`.
2. Find the objects named by the user with the list tools. If a name matches several objects, ask which one.
3. Turn the request into a numbered list of single changes and show it before any write.
4. A campaign tied to a shared budget takes its budget change through `bing_ads_update_budget`: say that every campaign on that budget is affected, and name them.
5. Apply each change under the Confirmation rules.
6. For a new campaign: `bing_ads_create_campaign` with type, budget type, daily budget, languages and `status: "Paused"`, then `bing_ads_create_ad_group` with a CPC bid and `status: "Paused"`. Each is its own confirmed write.
7. End with the log of changes made and not made.

## Output

1. **Plan**: the numbered list of changes, before any write.
2. **Log**: for each change, the object, before, after and the time it was made.
3. **Not done**: changes refused, failed or left for a person, with the reason.

## Rules

- Amounts are in the account currency: name it.
- Never chain several writes on one yes.
- Never act on an object of another account than the one confirmed.

## Examples

- "Pause the Bing campaign Brand UK."
- "Passe le budget quotidien de la campagne Microsoft Ads Générique à 60 euros."
- "Baisse l'enchère du mot-clé chaussures running de 20 %."
