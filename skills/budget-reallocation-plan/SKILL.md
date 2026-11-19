---
name: budget-reallocation-plan
description: "Builds a budget reallocation plan through the Pilot MCP server: takes money from campaigns that spend without results or sit idle, and moves it to capped campaigns that convert well, on Google Ads, Meta Ads and TikTok Ads, with Google's budget simulations and Meta's minimum budgets as limits, for the same total and per currency. Use when the user asks how to split the budget, where to move money or how to spend the same budget better. Triggers: budget reallocation, move budget, rebalance spend, where to put more money. Déclencheurs : réallocation du budget, déplacer du budget, rééquilibrer la dépense, où mettre plus d'argent, répartition du budget."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with Google Ads, Meta Ads or TikTok Ads attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "pilot_budget_pacing pilot_diagnose_wasted_spend pilot_cross_platform_overview google_list_accounts google_list_budgets google_get_campaign_performance google_simulate_budget_change meta_list_accounts meta_list_adsets meta_get_account_insights meta_get_minimum_budgets tiktok_get_advertiser_info tiktok_get_campaign_performance_summary"
  pilot-mcp-read-only: "true"
---

# Budget reallocation plan

Propose where to take budget and where to put it, at constant total, with the evidence and the expected effect of each move. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace concerned.
- **Optional MCP servers:** a spreadsheet server to hand over the plan. None is needed.
- **Read-only:** never change a budget. The plan is for a person to apply, or for a skill that writes, after approval.

## Tools

- `pilot_budget_pacing`: capped and underused campaigns, and the idle daily budget.
- `pilot_diagnose_wasted_spend`: campaigns that spend without converting or above the CPA threshold.
- `pilot_cross_platform_overview`: CPA and ROAS per platform.
- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_list_budgets`: campaign budgets, their amounts in micros, and whether they are shared.
- `google_get_campaign_performance`: cost, conversions and value per campaign.
- `google_simulate_budget_change`: projected results of one campaign at other budget levels.
- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_list_adsets`: ad sets with their daily or lifetime budget, in the currency's minor unit.
- `meta_get_account_insights`: Meta results by campaign or ad set.
- `meta_get_minimum_budgets`: the lowest daily budget Meta accepts.
- `tiktok_get_advertiser_info`: the TikTok advertiser and its currency.
- `tiktok_get_campaign_performance_summary`: spend and conversions per TikTok campaign.

## Steps

1. Ask for the goal: more conversions at the same cost, a target CPA, or a target ROAS. Default: more conversions at constant total budget.
2. Call `pilot_budget_pacing` and `pilot_diagnose_wasted_spend` with `days: 30`. Sources of budget: idle budgets and campaigns flagged as waste. Destinations: capped campaigns with a CPA under the median or a ROAS above it.
3. Call `pilot_cross_platform_overview` with `days: 30` to compare platforms.
4. Google Ads: `google_list_budgets` (a shared budget moves several campaigns at once), `google_get_campaign_performance` for the figures, and `google_simulate_budget_change` on each capped destination to bound the gain.
5. Meta Ads: `meta_list_adsets` for the current budgets, `meta_get_account_insights` with `level: "adset"` for their results, and `meta_get_minimum_budgets` so that no source is cut below the minimum.
6. TikTok Ads: `tiktok_get_campaign_performance_summary` for the period.
7. Build the plan: each move gives a source, a destination, an amount per day in its currency, the reason and the expected effect. Keep the total unchanged per currency. Move no more than about 20% of a budget at a time, so that automated bidding can adapt.

## Output

1. **Summary**: amount moved per currency and the expected effect.
2. **Moves**: a table with source, destination, current and proposed daily budget, the evidence and the expected effect.
3. **Limits**: minimum budgets, shared budgets, campaigns too new or too small to judge.
4. **How to apply**: the order of the moves, and when to check the effect.

## Rules

- Never move money between two currencies as if they were one.
- Google simulations are Google's estimates: present them as such, with their range.
- Google amounts are in micros (divide by 1,000,000); Meta amounts in the currency's minor unit (divide by 100 for EUR or USD).
- Do not cut a campaign whose conversions are not collected: it is unmeasured, not wasteful.

## Examples

- "How should we reallocate this month's budget to get more conversions?"
- "Propose-moi un plan de réallocation du budget entre Google et Meta, à budget constant."
- "Où mettre plus d'argent, et d'où le prendre ?"
