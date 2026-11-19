---
name: meta-ads-account-audit
description: "Audits a Meta ad account in read-only mode through the Pilot MCP server: account status and spend cap, campaign and ad set structure, learning phase, optimization goals, results by campaign, rejected ads, pixels, audiences, automated rules and recent changes, then returns a prioritized findings report. Use when the user asks to audit, review or health-check a Meta, Facebook or Instagram ad account. Triggers: Meta ads audit, Facebook ads account review, account health check Meta. Déclencheurs : audit Meta Ads, audit du compte Facebook Ads, bilan de santé du compte Meta, revue de compte Meta."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Meta ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "meta_list_accounts meta_get_account meta_check_account_issues meta_get_account_performance_summary meta_list_campaigns meta_list_adsets meta_get_account_insights meta_get_ad_rejections meta_list_pixels meta_list_custom_audiences meta_list_automated_rules meta_get_ad_account_activities"
  pilot-mcp-read-only: "true"
---

# Meta Ads account audit

Audit one Meta ad account and hand back a short, prioritized list of findings, each backed by figures from the account. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a file storage server to save the report, a team messaging server to share it. None is needed.
- **Read-only:** never call a tool that creates, updates, pauses or deletes anything. Every recommendation is for a person to apply.

## Tools

- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_get_account`: name, status, currency, time zone and spend cap.
- `meta_check_account_issues`: inactive account, disable reason, spend cap nearly used.
- `meta_get_account_performance_summary`: key figures of the account over a preset.
- `meta_list_campaigns`: campaigns with status, objective and budget.
- `meta_list_adsets`: ad sets with budget and targeting; `champs` adds effective status, learning stage and optimization goal.
- `meta_get_account_insights`: results by campaign, ad set or ad.
- `meta_get_ad_rejections`: ads disapproved or with issues.
- `meta_list_pixels`: pixels, with their last activity through `champs`.
- `meta_list_custom_audiences`: custom audiences and their size.
- `meta_list_automated_rules`: automated rules in place.
- `meta_get_ad_account_activities`: who changed what recently.

## Steps

1. Call `meta_list_accounts` and pick the account (ask if several). Use its ID as `account_id`.
2. Call `meta_get_account` and `meta_check_account_issues`: a restricted account or a spend cap reached comes first.
3. Call `meta_get_account_performance_summary` with `date_preset: "last_30d"`.
4. Call `meta_list_campaigns` and `meta_list_adsets` with `champs: ["effective_status", "learning_stage_info", "optimization_goal"]`. Look for: many small ad sets splitting the budget, ad sets stuck in learning, an optimization goal that does not match the business goal (link clicks for a store, for example), campaigns that compete for the same audience.
5. Call `meta_get_account_insights` with `level: "campaign"` and `date_preset: "last_30d"`, then `level: "adset"` for the campaigns that spend most.
6. Call `meta_get_ad_rejections`.
7. Call `meta_list_pixels` with `champs: ["last_fired_time", "is_unavailable"]`, and `meta_list_custom_audiences`.
8. Call `meta_list_automated_rules` and `meta_get_ad_account_activities` to see what changes the account on its own and who touched it lately.
9. Write the report, findings ranked by money at stake.

## Output

1. **Summary**: three lines on the account's state and the top three priorities.
2. **Findings**: a table with finding, evidence (figure and period), stake and suggested action.
3. **Tracking**: pixel activity and what the account optimizes for.
4. **Next steps**: the actions in order, for a person to apply in Meta Ads Manager.

## Rules

- Meta budgets are in the currency's minor unit: divide by 100 for EUR or USD.
- A conversion Meta did not report is absent, not zero; a ratio on an empty denominator is not available.
- Do not judge an ad set with too little volume to leave learning; say so.
- Name the period and the currency.

## Examples

- "Audit our Meta ad account."
- "Fais l'audit du compte Facebook Ads du client, priorisé par enjeu."
- "Quels sont les trois chantiers prioritaires sur le compte Meta ?"
