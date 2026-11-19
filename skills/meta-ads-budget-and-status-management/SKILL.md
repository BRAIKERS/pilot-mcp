---
name: meta-ads-budget-and-status-management
description: "Changes Meta Ads budgets and statuses through the Pilot MCP server, after explicit approval of each write: reads the campaign or ad set, its budget, its results and Meta's minimum budget, then changes one daily or lifetime budget, pauses or activates one campaign, ad set or ad, or moves an end date, one write at a time, and reads the result back. Use when the user asks to raise or lower a Meta budget, pause or restart a campaign, or stop an ad. Triggers: change Meta budget, pause Facebook campaign, activate ad set, stop Meta ad. Déclencheurs : changer le budget Meta, mettre en pause une campagne Facebook, activer un ensemble de publicités, arrêter une publicité Meta."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Meta ad account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "meta_list_accounts meta_list_campaigns meta_list_adsets meta_list_ads meta_get_campaign meta_get_adset meta_get_account_insights meta_get_minimum_budgets meta_update_campaign meta_update_adset meta_update_ad"
  pilot-mcp-read-only: "false"
---

# Meta Ads budget and status management

Apply a budget or status change the user asked for, safely: read first, show the change, wait for a yes, write once, read back. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a team messaging server to log the change. None is needed.
- **Writes:** the write tools require the paid Pilot MCP plan and a workspace role that can write. If a write tool is not served, say so and return the list of changes for a person to apply by hand in Meta Ads Manager.

## Tools

- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_list_campaigns`: campaigns with status, objective and budget.
- `meta_list_adsets`: ad sets with status and budget.
- `meta_list_ads`: ads with status.
- `meta_get_campaign`: one campaign with its budget, to read before and after.
- `meta_get_adset`: one ad set with its budget, to read before and after.
- `meta_get_account_insights`: recent results of the object.
- `meta_get_minimum_budgets`: the lowest daily budget Meta accepts.
- `meta_update_campaign`: changes a campaign's status, budget, spend cap or stop time (write).
- `meta_update_adset`: changes an ad set's status, budget, bid or end time (write).
- `meta_update_ad`: changes an ad's status (write).

## Confirmation

Every write changes a live account and its spend. For each one:

1. Read the current state just before writing with `meta_get_campaign`, `meta_get_adset` or `meta_list_ads`.
2. Show the person the exact object (name, ID, account), the current value and the proposed value, with amounts in the account currency.
3. Wait for an explicit yes for this write. A yes for one write does not cover the next.
4. Make one write at a time.
5. Never delete or archive anything: `PAUSED` is the reversible way to stop. The statuses `ARCHIVED` and `DELETED` are out of this skill.
6. After the write, read the object again and show the before and the after; the tool also reports when Meta did not apply a value.
7. Stop at the first write that is refused or fails, report it, and do not try a workaround.

## Steps

1. Call `meta_list_accounts` and pick the account. Find the object with `meta_list_campaigns`, `meta_list_adsets` or `meta_list_ads`.
2. Find where the budget lives: on the campaign (campaign budget) or on each ad set. Changing the wrong level does nothing or is refused.
3. Call `meta_get_account_insights` at the object's level with `date_preset: "last_7d"` to show its recent results, and `meta_get_minimum_budgets` before any budget cut.
4. Present the change as in Confirmation and wait for the yes.
5. Write:
   - campaign: `meta_update_campaign` with `campaign_id` and `updates` holding `status`, `daily_budget`, `lifetime_budget`, `spend_cap` or `stop_time`;
   - ad set: `meta_update_adset` with `adset_id` and `updates` holding `status`, `daily_budget`, `lifetime_budget` or `end_time`;
   - ad: `meta_update_ad` with `ad_id` and `updates: { "status": "PAUSED" }` or `"ACTIVE"`.
6. Read back and show the result.

## Output

1. **Change proposed**: object, current value, new value, recent results.
2. **Change done**: before and after, read back from Meta.
3. **Not done**: anything refused, as a change a person can apply by hand.

## Rules

- Budgets are integers in the currency's minor unit: 50.00 EUR per day is `5000`.
- A budget change of more than about 20% can send an ad set back to learning: say so before the yes.
- Activating an object starts spend at once: say so before the yes.
- Never cut a budget below Meta's minimum.

## Examples

- "Raise the daily budget of the Retargeting ad set to 60 euros."
- "Mets en pause la campagne Meta Notoriété hiver."
- "Coupe la publicité qui a le pire coût par achat dans l'ensemble Lookalike."
