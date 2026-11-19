---
name: tiktok-ads-budget-and-status-management
description: "Changes TikTok Ads budgets and statuses through the Pilot MCP server, after explicit approval of each write: reads the campaign or ad group, its budget mode, its budget and its recent results, then changes one campaign or ad group budget, disables or enables one campaign, ad group or ad, or moves an ad group's end date, one write at a time, and reads the result back. Use when the user asks to raise or lower a TikTok budget, pause or restart a campaign, or stop an ad. Triggers: change TikTok budget, pause TikTok campaign, enable TikTok ad group. Déclencheurs : changer le budget TikTok, mettre en pause une campagne TikTok, relancer un groupe d'annonces TikTok."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a TikTok Ads advertiser attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "tiktok_get_advertiser_info tiktok_get_campaigns tiktok_get_ad_groups tiktok_get_ads tiktok_get_campaign_performance_summary tiktok_get_adgroup_performance tiktok_update_campaign_budget tiktok_update_adgroup_budget tiktok_update_campaign_status tiktok_update_ad_group_status tiktok_update_ad_status tiktok_update_adgroup_schedule"
  pilot-mcp-read-only: "false"
---

# TikTok Ads budget and status management

Apply a budget or status change the user asked for, safely: read first, show the change, wait for a yes, write once, read back. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the advertiser.
- **Optional MCP servers:** a team messaging server to log the change. None is needed.
- **Writes:** the write tools require the paid Pilot MCP plan and a workspace role that can write. If a write tool is not served, say so and return the list of changes for a person to apply by hand in TikTok Ads Manager.

## Tools

- `tiktok_get_advertiser_info`: the advertiser, its currency and time zone.
- `tiktok_get_campaigns`: campaigns with status, budget mode and budget.
- `tiktok_get_ad_groups`: ad groups with status, budget and schedule.
- `tiktok_get_ads`: ads with status.
- `tiktok_get_campaign_performance_summary`: recent results per campaign.
- `tiktok_get_adgroup_performance`: recent results per ad group.
- `tiktok_update_campaign_budget`: changes a campaign's daily or total budget (write).
- `tiktok_update_adgroup_budget`: changes an ad group's budget (write).
- `tiktok_update_campaign_status`: enables or disables campaigns (write).
- `tiktok_update_ad_group_status`: enables or disables ad groups (write).
- `tiktok_update_ad_status`: enables or disables ads (write).
- `tiktok_update_adgroup_schedule`: changes an ad group's start or end time (write).

## Confirmation

Every write changes a live account and its spend. For each one:

1. Read the current state just before writing with `tiktok_get_campaigns`, `tiktok_get_ad_groups` or `tiktok_get_ads`.
2. Show the person the exact object (name, ID, advertiser), the current value and the proposed value, with amounts in the account currency.
3. Wait for an explicit yes for this write. A yes for one write does not cover the next.
4. Make one write at a time, on one object at a time, even though the status tools accept lists.
5. Never use the status `DELETE`: `DISABLE` is the reversible way to stop. Deleting is out of this skill.
6. After the write, read the object again and show the before and the after.
7. Stop at the first write that is refused or fails, report it, and do not try a workaround.

## Steps

1. Call `tiktok_get_advertiser_info`. Find the object with `tiktok_get_campaigns`, `tiktok_get_ad_groups` or `tiktok_get_ads`.
2. Find where the budget lives: on the campaign (with its budget mode, daily or total) or on each ad group.
3. Call `tiktok_get_campaign_performance_summary` or `tiktok_get_adgroup_performance` over the last seven days to show recent results.
4. Present the change as in Confirmation and wait for the yes.
5. Write:
   - campaign budget: `tiktok_update_campaign_budget` with `campaign_id`, `budget` in the local currency and `budget_mode` if it changes;
   - ad group budget: `tiktok_update_adgroup_budget` with `adgroup_id` and `budget`;
   - status: `tiktok_update_campaign_status` with `campaign_ids`, `tiktok_update_ad_group_status` with `adgroup_ids`, or `tiktok_update_ad_status` with `ad_ids`, each with a single ID and `operation_status: "DISABLE"` or `"ENABLE"`;
   - schedule: `tiktok_update_adgroup_schedule` with `adgroup_id` and `schedule_end_time`.
6. Read back and show the result.

## Output

1. **Change proposed**: object, current value, new value, recent results.
2. **Change done**: before and after, read back from TikTok.
3. **Not done**: anything refused, as a change a person can apply by hand.

## Rules

- TikTok budgets are in the local currency, not in cents.
- A large budget change can reset learning: say so before the yes.
- Enabling an object starts spend at once: say so before the yes.
- TikTok enforces a minimum budget: if a cut is refused for that reason, report the refusal as given.

## Examples

- "Raise the daily budget of the TikTok prospecting campaign to 100 euros."
- "Mets en pause le groupe d'annonces TikTok Lookalike France."
- "Coupe la vidéo TikTok qui a le pire coût par conversion."
