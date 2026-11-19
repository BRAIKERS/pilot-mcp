---
name: microsoft-ads-account-audit
description: "Audits a Microsoft Advertising (Bing Ads) account in read-only mode through the Pilot MCP server: campaigns and shared budgets, ad groups and ads, keywords and negatives, ad extensions, audiences, conversion goals and the spend of the last 30 days, then returns a prioritized findings report. Use when the user asks to audit, review or health-check a Microsoft Ads or Bing Ads account. Triggers: Microsoft Ads audit, Bing Ads audit, review my Microsoft Advertising account. Déclencheurs : audit Microsoft Ads, audit Bing Ads, auditer le compte Microsoft Advertising, bilan de santé Bing."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Microsoft Advertising account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "bing_ads_get_ad_accounts bing_ads_get_account_info bing_ads_list_campaigns bing_ads_list_budgets bing_ads_get_campaign_performance bing_ads_get_ad_group_performance bing_ads_list_ad_groups bing_ads_list_ads bing_ads_list_negative_keywords bing_ads_list_extensions bing_ads_list_conversion_goals bing_ads_list_audiences pilot_data_freshness pilot_diagnose_wasted_spend"
  pilot-mcp-read-only: "true"
---

# Microsoft Ads account audit

Audit one Microsoft Advertising account and hand back a short, prioritized list of findings, each backed by figures from the account. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a file storage server to save the report. None is needed.
- **Read-only:** never create, update, pause or delete anything. Every recommendation is written for a person to apply.

## Tools

- `bing_ads_get_ad_accounts`: the Microsoft Advertising accounts available, with their IDs.
- `bing_ads_get_account_info`: one account's settings, currency and time zone.
- `bing_ads_list_campaigns`: campaigns with type, status and budget.
- `bing_ads_list_budgets`: shared budgets.
- `bing_ads_get_campaign_performance`: impressions, clicks, cost and conversions per campaign.
- `bing_ads_get_ad_group_performance`: the same per ad group, for one campaign.
- `bing_ads_list_ad_groups`: ad groups of a campaign, with bid and status.
- `bing_ads_list_ads`: ads of an ad group, with status.
- `bing_ads_list_negative_keywords`: negatives of a campaign or ad group.
- `bing_ads_list_extensions`: sitelinks, callouts, snippets, call and other extensions.
- `bing_ads_list_conversion_goals`: conversion goals with type and status.
- `bing_ads_list_audiences`: remarketing lists of the account.
- `pilot_data_freshness`: whether Pilot's collected Microsoft Ads data is current.
- `pilot_diagnose_wasted_spend`: campaigns that spend without converting, from Pilot's collected data.

## Steps

1. Call `bing_ads_get_ad_accounts` and pick the account (ask if several). Use its ID as `target_account_id` everywhere. Call `bing_ads_get_account_info` for the currency.
2. Call `pilot_data_freshness` with `platform: "bing_ads"`; if Pilot holds no data, rely on the live tools only.
3. Call `bing_ads_get_campaign_performance` with `date_range: "LastThirtyDays"`. Rank campaigns by cost; note those with cost and no conversion. Call `pilot_diagnose_wasted_spend` with `platform: "bing_ads"` when Pilot holds data.
4. Call `bing_ads_list_campaigns` and `bing_ads_list_budgets`: paused campaigns still tied to a budget, shared budgets used by many campaigns.
5. For the three campaigns that cost most: `bing_ads_get_ad_group_performance`, `bing_ads_list_ad_groups`, then `bing_ads_list_ads` on their main ad groups (ad groups with fewer than two active ads, disapproved ads), and `bing_ads_list_negative_keywords` at campaign level (search campaigns with no negatives at all).
6. Call `bing_ads_list_extensions`: missing sitelinks or callouts on search campaigns.
7. Call `bing_ads_list_conversion_goals` (no active goal, or goals in error) and `bing_ads_list_audiences` (lists never used).
8. Write the report, findings ranked by money at stake.

## Output

1. **Summary**: three lines on the account's state and the top three priorities.
2. **Findings**: a table with the finding, the evidence (figure, period, currency), the stake and the suggested action.
3. **Tracking**: conversion goals and their state.
4. **Next steps**: actions in order, for a person to apply in Microsoft Advertising.

## Rules

- A ratio with an empty denominator is not available, not zero.
- Microsoft Ads search terms are not available through these tools: do not claim to have read them.
- Never sum amounts in different currencies.

## Examples

- "Audit our Microsoft Ads account."
- "Fais l'audit du compte Bing Ads, en priorisant ce qui coûte sans convertir."
- "Le compte Microsoft Advertising est-il bien configuré ?"
