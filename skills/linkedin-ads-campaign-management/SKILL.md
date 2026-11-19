---
name: linkedin-ads-campaign-management
description: "Manages LinkedIn Ads campaign groups, campaigns and creatives through the Pilot MCP server, with the user's explicit approval before every write: pause, resume or archive, change a daily or total budget, rename, and create campaign groups, campaigns and sponsored posts as drafts. Reads the current state first and shows the before and after. Use when the user asks to change something in a LinkedIn Ads account. Triggers: pause LinkedIn campaign, change LinkedIn budget, create LinkedIn campaign. Déclencheurs : mettre en pause une campagne LinkedIn, changer le budget LinkedIn, créer une campagne LinkedIn, sponsoriser un post LinkedIn."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a LinkedIn ad account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "linkedin_linkedin_ads_list_accounts linkedin_linkedin_ads_list_campaign_groups linkedin_linkedin_ads_get_campaign_group linkedin_linkedin_ads_list_campaigns linkedin_linkedin_ads_get_campaign linkedin_linkedin_ads_list_creatives linkedin_linkedin_ads_get_creative linkedin_linkedin_ads_update_campaign linkedin_linkedin_ads_update_campaign_group linkedin_linkedin_ads_update_creative_status linkedin_linkedin_ads_create_campaign_group linkedin_linkedin_ads_create_campaign linkedin_linkedin_ads_create_creative"
  pilot-mcp-read-only: "false"
---

# LinkedIn Ads campaign management

Apply the changes the user asks for in a LinkedIn Ads account, one at a time, each after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the LinkedIn ad account.
- **Optional MCP servers:** a team messaging server to log the changes made. None is needed.
- **Writes:** the write tools need the paid Pilot MCP plan and a workspace role allowed to write. If a write tool is not served, say so and return the list of changes for a person to apply by hand in Campaign Manager.

## Tools

- `linkedin_linkedin_ads_list_accounts`: the ad accounts available, with currency.
- `linkedin_linkedin_ads_list_campaign_groups`: campaign groups and their status.
- `linkedin_linkedin_ads_get_campaign_group`: one campaign group with schedule and total budget.
- `linkedin_linkedin_ads_list_campaigns`: campaigns, optionally within a group.
- `linkedin_linkedin_ads_get_campaign`: one campaign with budget, bidding and targeting.
- `linkedin_linkedin_ads_list_creatives`: creatives with their status.
- `linkedin_linkedin_ads_get_creative`: one creative with status and review state.
- `linkedin_linkedin_ads_update_campaign`: changes status, daily or total budget, or name of a campaign (write).
- `linkedin_linkedin_ads_update_campaign_group`: changes status, total budget or name of a group (write).
- `linkedin_linkedin_ads_update_creative_status`: sets a creative ACTIVE, PAUSED, ARCHIVED or CANCELED (write).
- `linkedin_linkedin_ads_create_campaign_group`: creates a campaign group as a draft (write).
- `linkedin_linkedin_ads_create_campaign`: creates a campaign as a draft in a group (write).
- `linkedin_linkedin_ads_create_creative`: sponsors an existing organization post, as a draft or paused (write).

## Confirmation

Every write changes a live ad account and can move money. For each change:

1. Read the current state first with `linkedin_linkedin_ads_get_campaign_group`, `linkedin_linkedin_ads_get_campaign` or `linkedin_linkedin_ads_get_creative`.
2. Show the person the exact object (name, ID, ad account), the current value and the proposed value, with the currency for any amount.
3. Wait for an explicit yes for that write. A yes covers one write only.
4. Make one write at a time.
5. New campaign groups and campaigns are always created as drafts; create creatives with `intended_status: "DRAFT"` or `"PAUSED"`. Activation is a separate, confirmed change.
6. Never archive or cancel anything unless the person asks for it in so many words and names the object: archiving stops the object for good in practice. Prefer pausing.
7. After the write, read the object again and show the before and after.
8. If a write is refused or fails, stop, report the message, and do not attempt the next change.

## Steps

1. Call `linkedin_linkedin_ads_list_accounts` and confirm the account. Use its ID as `account_id`.
2. Find the objects named by the user with `linkedin_linkedin_ads_list_campaign_groups`, `linkedin_linkedin_ads_list_campaigns` or `linkedin_linkedin_ads_list_creatives`. If a name matches several objects, ask which one.
3. Turn the request into a numbered list of single changes and show it before any write.
4. Apply status, budget and name changes under the Confirmation rules with `linkedin_linkedin_ads_update_campaign` or `linkedin_linkedin_ads_update_campaign_group`; creative status with `linkedin_linkedin_ads_update_creative_status`. A creative under review cannot be paused.
5. For a new setup: `linkedin_linkedin_ads_create_campaign_group`, then `linkedin_linkedin_ads_create_campaign` with objective, cost type, daily budget, locale, targeting criteria (geo URNs) and `political_intent`, then `linkedin_linkedin_ads_create_creative` from an organization post URN. Each step is its own confirmed write.
6. End with the log of changes made and not made.

## Output

1. **Plan**: the numbered list of changes, before any write.
2. **Log**: for each change, the object, before, after and the time it was made.
3. **Not done**: changes refused, failed or left for a person, with the reason.

## Rules

- Budgets are in the account currency: name it.
- Vocabulary: a LinkedIn campaign group is a campaign in Pilot, a LinkedIn campaign is an ad group.
- Never chain several writes on one yes.

## Examples

- "Pause the LinkedIn campaign Webinar retargeting."
- "Passe le budget quotidien de la campagne LinkedIn Décideurs IT à 150 euros."
- "Sponsorise notre dernier post LinkedIn dans une nouvelle campagne, en brouillon."
