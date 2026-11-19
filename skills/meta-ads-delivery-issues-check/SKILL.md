---
name: meta-ads-delivery-issues-check
description: "Finds why Meta ads do not deliver or under-deliver through the Pilot MCP server: account disabled or capped, ads rejected or with issues, ad sets stuck in learning or limited, budgets under Meta's minimum, audiences too small, spend that stopped on a given day, and the recent changes that could explain it, then lists the fixes for a person to apply. Use when the user says Meta ads are not spending, not delivering, stuck in learning or rejected. Triggers: Meta ads not delivering, ads not spending, learning limited, ad rejected. Déclencheurs : les pubs Meta ne diffusent pas, ne dépensent pas, apprentissage limité, publicité refusée, problème de diffusion."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Meta ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "meta_list_accounts meta_check_account_issues meta_get_ad_rejections meta_list_adsets meta_list_ads meta_get_daily_spend_breakdown meta_get_minimum_budgets meta_get_delivery_estimate meta_get_ad_account_activities"
  pilot-mcp-read-only: "true"
---

# Meta Ads delivery issues check

Find what stops or slows delivery on a Meta ad account, from the account level down to the ad, and say how to fix it. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a team messaging server to alert the team. None is needed.
- **Read-only:** never edit, resubmit or relaunch anything. Fixes are for a person to apply.

## Tools

- `meta_list_accounts`: the Meta ad accounts and their status.
- `meta_check_account_issues`: inactive account, disable reason, spend cap, funding source.
- `meta_get_ad_rejections`: ads disapproved or with issues, with the details.
- `meta_list_adsets`: ad sets with budget and targeting; `champs` adds effective status, learning stage and Meta's recommendations.
- `meta_list_ads`: ads with their effective status.
- `meta_get_daily_spend_breakdown`: spend per day, to date the stop.
- `meta_get_minimum_budgets`: the lowest daily budget Meta accepts.
- `meta_get_delivery_estimate`: the estimated audience of an ad set.
- `meta_get_ad_account_activities`: changes made around the date of the stop.

## Steps

1. Call `meta_list_accounts` and pick the account. Call `meta_check_account_issues`: an inactive account, a disable reason, a spend cap used or a funding problem stops everything.
2. Call `meta_get_daily_spend_breakdown` with `date_preset: "last_30d"` and `level: "adset"` to see when and where spend stopped or fell.
3. Call `meta_get_ad_rejections` for disapproved ads and ads with issues.
4. Call `meta_list_adsets` with `champs: ["effective_status", "learning_stage_info", "recommendations"]`: note ad sets in learning limited, ad sets without active ads, ad sets past their end date.
5. Call `meta_list_ads` to find ad sets whose ads are all paused, rejected or in review.
6. Call `meta_get_minimum_budgets` and compare with each ad set's daily budget (minor unit).
7. Call `meta_get_delivery_estimate` with `adset_id` for ad sets that barely spend: a tiny audience explains low delivery.
8. Call `meta_get_ad_account_activities` with `since` a few days before the drop to find the change that matches it.

## Output

1. **Cause**: the most likely reason delivery stopped or slowed, with the evidence.
2. **By level**: account, campaign, ad set and ad issues found.
3. **Timeline**: spend per day around the drop, and changes made then.
4. **Fixes**: in order, for a person to apply in Meta Ads Manager.

## Rules

- Quote Meta's rejection reasons as given; never guess a policy.
- Budgets are in the currency's minor unit: divide by 100 for EUR or USD.
- Audience sizes are Meta's estimates, given as bounds.
- Name the dates and the account time zone.

## Examples

- "Our Meta ads stopped spending yesterday: why?"
- "Pourquoi cet ensemble de publicités reste en apprentissage limité ?"
- "Quelles publicités Meta sont refusées, et pour quelle raison ?"
