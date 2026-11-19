---
name: google-ads-budget-and-status-management
description: "Changes Google Ads budgets and statuses through the Pilot MCP server, after explicit approval of each write: reads the campaign, its budget and its recent results, shows Google's budget simulation, then updates one daily budget, pauses or enables one campaign, ad group or ad, or changes its end date, one write at a time, and reads the result back. Use when the user asks to raise or lower a budget, pause or restart a campaign, or stop an ad. Triggers: change Google Ads budget, pause campaign, enable campaign, stop ad. Déclencheurs : changer le budget Google Ads, mettre en pause une campagne, relancer une campagne, arrêter une annonce."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Ads account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_list_campaigns google_list_budgets google_get_campaign_performance google_simulate_budget_change google_list_ad_groups google_list_ads google_update_budget google_update_campaign google_update_ad_group google_update_ad_status"
  pilot-mcp-read-only: "false"
---

# Google Ads budget and status management

Apply a budget or status change the user asked for, safely: read first, show the change, wait for a yes, write once, read back. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a team messaging server to log the change. None is needed.
- **Writes:** the write tools require the paid Pilot MCP plan and a workspace role that can write. If a write tool is not served, say so and return the list of changes for a person to apply by hand in Google Ads.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_list_campaigns`: campaigns with status, budget and dates.
- `google_list_budgets`: campaign budgets, amounts in micros, and whether they are shared.
- `google_get_campaign_performance`: recent cost and conversions of a campaign.
- `google_simulate_budget_change`: projected results at other budget levels.
- `google_list_ad_groups`: ad groups with status.
- `google_list_ads`: ads with status.
- `google_update_budget`: changes a campaign budget's daily amount (write).
- `google_update_campaign`: changes a campaign's status (ENABLED or PAUSED) or its dates (write).
- `google_update_ad_group`: changes an ad group's status (write).
- `google_update_ad_status`: changes an ad's status (write).

## Confirmation

Every write changes a live account and its spend. For each one:

1. Read the current state just before writing: the object, its status or budget, and its last 30 days.
2. Show the person the exact object (name, ID, account), the current value and the proposed value, with the daily amount in the account currency.
3. Wait for an explicit yes for this write. A yes for one write does not cover the next.
4. Make one write at a time.
5. Never remove a campaign, ad group or ad: pausing is the reversible way to stop. Removing is out of this skill.
6. After the write, read the object again and show the before and the after.
7. Stop at the first write that is refused or fails, report it, and do not try a workaround.

## Steps

1. Call `google_list_accounts` and pick the account. Find the object: `google_list_campaigns`, `google_list_ad_groups` with `campaign_id`, or `google_list_ads` with `ad_group_id`.
2. For a budget change: call `google_list_budgets` to find the budget behind the campaign. If the budget is shared, list every campaign it feeds before going further. Call `google_get_campaign_performance` over `LAST_30_DAYS` and `google_simulate_budget_change` with `campaign_id` to show the expected effect.
3. Present the change as in Confirmation and wait for the yes.
4. Write:
   - budget: `google_update_budget` with `budget_id` and `amount_micros` (amount times 1,000,000);
   - campaign: `google_update_campaign` with `status: "PAUSED"` or `"ENABLED"`, or `end_date`;
   - ad group: `google_update_ad_group` with `status`;
   - ad: `google_update_ad_status` with `ad_group_id`, `ad_id` and `status: "PAUSED"` or `"ENABLED"`.
5. Read back with the matching listing tool and show the result.

## Output

1. **Change proposed**: object, current value, new value, expected effect.
2. **Change done**: before and after, read back from Google Ads.
3. **Not done**: anything refused, as a change a person can apply by hand.

## Rules

- Amounts are in micros: 50 in the account currency is `50000000`.
- Raise a budget in steps of about 20% at most on automated bidding, unless the user insists.
- Enabling a campaign starts spend at once: say so before the yes.
- Simulations are Google's estimates.

## Examples

- "Raise the budget of the brand campaign to 80 a day."
- "Mets en pause la campagne Display Prospection."
- "Coupe l'annonce qui a le pire taux de clic dans le groupe Chaussures."
