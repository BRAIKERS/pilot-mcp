---
name: amazon-ads-campaign-management
description: "Manages Amazon Ads campaigns through the Pilot MCP server, with the user's explicit approval before every write: pause or enable Sponsored Products, Sponsored Brands and Sponsored Display campaigns, change their daily budget or name, change ad group default bids, pause product ads, and create Sponsored Products campaigns in paused state. Each write is simulated first, then applied, then read back. Use when the user asks to change something in an Amazon Ads account. Triggers: pause Amazon campaign, change Amazon budget, Amazon default bid. Déclencheurs : mettre en pause une campagne Amazon, changer le budget Amazon Ads, enchère par défaut Amazon, créer une campagne Sponsored Products."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with an Amazon Ads profile attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "amazon_ads_list_profiles amazon_ads_sp_list_campaigns amazon_ads_sb_list_campaigns amazon_ads_sd_list_campaigns amazon_ads_sd_get_campaign amazon_ads_sp_list_ad_groups amazon_ads_sp_list_product_ads amazon_ads_validate_operation amazon_ads_sp_update_campaign amazon_ads_sb_update_campaign amazon_ads_sd_update_campaign amazon_ads_sp_update_ad_group amazon_ads_sp_update_product_ad amazon_ads_sp_create_campaign"
  pilot-mcp-read-only: "false"
---

# Amazon Ads campaign management

Apply the changes the user asks for in Amazon Ads, one at a time, each after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Amazon Ads profile.
- **Optional MCP servers:** a team messaging server to log the changes made. None is needed.
- **Writes:** the write tools need the paid Pilot MCP plan and a workspace role allowed to write. If a write tool is not served, say so and return the list of changes for a person to apply by hand in Amazon Ads.

## Tools

- `amazon_ads_list_profiles`: the profiles available.
- `amazon_ads_sp_list_campaigns`: Sponsored Products campaigns.
- `amazon_ads_sb_list_campaigns`: Sponsored Brands campaigns.
- `amazon_ads_sd_list_campaigns`: Sponsored Display campaigns.
- `amazon_ads_sd_get_campaign`: one Sponsored Display campaign.
- `amazon_ads_sp_list_ad_groups`: Sponsored Products ad groups.
- `amazon_ads_sp_list_product_ads`: Sponsored Products product ads.
- `amazon_ads_validate_operation`: checks a payload before it is sent; Amazon has no sandbox.
- `amazon_ads_sp_update_campaign`: changes state, budget or name of Sponsored Products campaigns (write).
- `amazon_ads_sb_update_campaign`: the same for Sponsored Brands (write).
- `amazon_ads_sd_update_campaign`: the same for Sponsored Display (write).
- `amazon_ads_sp_update_ad_group`: changes state, default bid or name of ad groups (write).
- `amazon_ads_sp_update_product_ad`: enables or pauses product ads (write).
- `amazon_ads_sp_create_campaign`: creates Sponsored Products campaigns (write).

## Confirmation

Every write changes a live account and can move money. For each change:

1. Read the current state first with the list tool of the program (filtered on the object), or `amazon_ads_sd_get_campaign`.
2. Show the person the exact object (name, ID, profile and marketplace), the current value and the proposed value, in the profile currency.
3. Run `amazon_ads_validate_operation`, then the write tool with `dry_run: true`, and show what Amazon would do.
4. Wait for an explicit yes for that write. A yes covers one write only, with one object in the array.
5. Send the write with `dry_run: false` (and `confirm: true` when the tool asks for it).
6. Create every new campaign with state `PAUSED`.
7. Never archive anything: archiving is final on Amazon. If the person asks for it, say that this skill pauses instead, and that an archive must be asked for separately, naming the object.
8. After the write, read the object again and show the before and after. If a write is refused or fails, stop, report Amazon's reason, and do not attempt the next change.

## Steps

1. Call `amazon_ads_list_profiles` and confirm the profile; pass it as `account_id` when the workspace has several.
2. Find the objects named by the user with the list tools. States are UPPERCASE for Sponsored Products and Brands (ENABLED, PAUSED), lowercase for Sponsored Display (enabled, paused).
3. Turn the request into a numbered list of single changes and show it before any write.
4. Apply each change under the Confirmation rules. A Sponsored Products budget is sent as `budget: { budget: <amount>, budgetType: "DAILY" }`.
5. For a new Sponsored Products campaign, call `amazon_ads_sp_create_campaign` with name, targeting type, daily budget, start date and state `PAUSED`. Ad groups, product ads and keywords come as later, separately confirmed writes.
6. End with the log of changes made and not made.

## Output

1. **Plan**: the numbered list of changes, before any write.
2. **Log**: for each change, the object, before, after and the time it was made.
3. **Not done**: changes refused, failed or left for a person, with the reason.

## Rules

- Amounts are in the profile currency: name it.
- If Amazon accepts some items of a batch and rejects others, never resend those already applied.
- Never chain several writes on one yes.

## Examples

- "Pause the Amazon campaign SP Auto Mugs."
- "Monte le budget quotidien de la campagne Sponsored Brands Marque à 80 euros."
- "Baisse l'enchère par défaut du groupe d'annonces Thé vert à 0,45 euro."
