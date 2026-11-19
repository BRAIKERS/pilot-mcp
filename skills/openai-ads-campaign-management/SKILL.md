---
name: openai-ads-campaign-management
description: "Manages OpenAI Ads (ChatGPT ads) campaigns, ad groups and ads through the Pilot MCP server, with the user's explicit approval before every write: pause or activate campaigns, ad groups and ads, change a campaign budget, schedule or targeting, and change an ad group's bidding or context hints. Reads the current state first and shows the before and after. Use when the user asks to change something in an OpenAI Ads account. Triggers: pause OpenAI ads campaign, change ChatGPT ads budget, OpenAI ads bid. Déclencheurs : mettre en pause une campagne OpenAI Ads, changer le budget des publicités ChatGPT, modifier l'enchère OpenAI Ads."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with an OpenAI Ads account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "openai_ads_openai_ads_get_ad_account openai_ads_openai_ads_list_campaigns openai_ads_openai_ads_get_campaign openai_ads_openai_ads_list_ad_groups openai_ads_openai_ads_get_ad_group openai_ads_openai_ads_list_ads openai_ads_openai_ads_pause_campaign openai_ads_openai_ads_activate_campaign openai_ads_openai_ads_pause_ad_group openai_ads_openai_ads_activate_ad_group openai_ads_openai_ads_pause_ad openai_ads_openai_ads_activate_ad openai_ads_openai_ads_update_campaign openai_ads_openai_ads_update_ad_group"
  pilot-mcp-read-only: "false"
---

# OpenAI Ads campaign management

Apply the changes the user asks for in OpenAI Ads, one at a time, each after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the OpenAI Ads account.
- **Optional MCP servers:** a team messaging server to log the changes made. None is needed.
- **Writes:** the write tools need the paid Pilot MCP plan and a workspace role allowed to write. If a write tool is not served, say so and return the list of changes for a person to apply by hand.

## Tools

- `openai_ads_openai_ads_get_ad_account`: the account, currency and status.
- `openai_ads_openai_ads_list_campaigns`: campaigns with status, budget and targeting.
- `openai_ads_openai_ads_get_campaign`: one campaign in full.
- `openai_ads_openai_ads_list_ad_groups`: ad groups of a campaign.
- `openai_ads_openai_ads_get_ad_group`: one ad group in full, with its bidding config.
- `openai_ads_openai_ads_list_ads`: ads of an ad group, with status.
- `openai_ads_openai_ads_pause_campaign`: pauses a campaign (write).
- `openai_ads_openai_ads_activate_campaign`: activates a campaign (write).
- `openai_ads_openai_ads_pause_ad_group`: pauses an ad group (write).
- `openai_ads_openai_ads_activate_ad_group`: activates an ad group (write).
- `openai_ads_openai_ads_pause_ad`: pauses an ad (write).
- `openai_ads_openai_ads_activate_ad`: activates an ad (write).
- `openai_ads_openai_ads_update_campaign`: changes name, budget, schedule or targeting of a campaign (write).
- `openai_ads_openai_ads_update_ad_group`: changes name, bidding config or context hints of an ad group (write).

## Confirmation

Every write changes a live account and can move money. For each change:

1. Read the current state first with `openai_ads_openai_ads_get_campaign`, `openai_ads_openai_ads_get_ad_group` or `openai_ads_openai_ads_list_ads`.
2. Show the person the exact object (name, ID, account), the current value and the proposed value, with the currency for any amount.
3. Wait for an explicit yes for that write. A yes covers one write only. Activating puts money at stake: say so.
4. Make one write at a time.
5. Never archive anything and never pause the whole account: this skill works on campaigns, ad groups and ads only. An archive must be asked for separately, naming the object.
6. After the write, read the object again and show the before and after.
7. If a write is refused or fails, stop, report the message, and do not attempt the next change.

## Steps

1. Call `openai_ads_openai_ads_get_ad_account` and confirm the account.
2. Find the objects named by the user with `openai_ads_openai_ads_list_campaigns`, `openai_ads_openai_ads_list_ad_groups` or `openai_ads_openai_ads_list_ads`. If a name matches several objects, ask which one.
3. Turn the request into a numbered list of single changes and show it before any write.
4. Status changes use the pause and activate tools of the right level.
5. A budget change goes through `openai_ads_openai_ads_update_campaign` with the full `budget` object, in micros (25 units = 25,000,000). A daily budget never becomes a lifetime budget again: warn before switching.
6. An ad group change goes through `openai_ads_openai_ads_update_ad_group`: pass the full `bidding_config`; `context_hints` replaces the whole list, so read the group, add or remove, then send the complete list.
7. End with the log of changes made and not made.

## Output

1. **Plan**: the numbered list of changes, before any write.
2. **Log**: for each change, the object, before, after and the time it was made.
3. **Not done**: changes refused, failed or left for a person, with the reason.

## Rules

- Show budgets both in micros and in readable currency.
- Never chain several writes on one yes.
- Never act on an object of another account than the one confirmed.

## Examples

- "Pause the OpenAI Ads campaign Spring launch."
- "Passe le budget quotidien de la campagne ChatGPT Marque à 100 euros."
- "Ajoute deux indications de contexte au groupe d'annonces OpenAI Ads Logiciel."
