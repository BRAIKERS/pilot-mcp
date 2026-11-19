---
name: pinterest-ads-account-audit
description: "Audits a Pinterest Ads account in read-only mode through the Pilot MCP server: account settings, campaign and ad group structure, spend without conversions, conversion tags, audiences, catalog feeds and keywords, then returns a prioritized findings report. Use when the user asks to audit, review or health-check a Pinterest Ads account. Triggers: Pinterest Ads audit, review my Pinterest account, Pinterest account health check. Déclencheurs : audit Pinterest Ads, auditer le compte Pinterest, bilan de santé Pinterest, revue du compte Pinterest."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Pinterest ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "pinterest_ad_accounts_list pinterest_ad_accounts_get pinterest_campaigns_list pinterest_ad_groups_list pinterest_ads_list pinterest_campaigns_analytics pinterest_ad_groups_analytics pinterest_conversion_tags_list pinterest_audiences_list pinterest_catalog_feeds_list pinterest_keywords_list pilot_data_freshness pilot_diagnose_wasted_spend"
  pilot-mcp-read-only: "true"
---

# Pinterest Ads account audit

Audit one Pinterest Ads account and hand back a short, prioritized list of findings, each backed by figures from the account. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Pinterest ad account.
- **Optional MCP servers:** a file storage server to save the report, a team messaging server to share it. None is needed.
- **Read-only:** never create, update, pause, archive or delete anything. Every recommendation is written for a person to apply.

## Tools

- `pinterest_ad_accounts_list`: the ad accounts the connection can read, with their IDs.
- `pinterest_ad_accounts_get`: one account's settings, including its currency and country.
- `pinterest_campaigns_list`: campaigns, status, objective and budget.
- `pinterest_ad_groups_list`: ad groups, status, bid, budget and targeting.
- `pinterest_ads_list`: ads with their status and review state.
- `pinterest_campaigns_analytics`: spend, impressions, clicks and conversions per campaign.
- `pinterest_ad_groups_analytics`: the same metrics per ad group.
- `pinterest_conversion_tags_list`: the conversion tags of the account.
- `pinterest_audiences_list`: custom, visitor, engagement and actalike audiences.
- `pinterest_catalog_feeds_list`: the catalog feeds behind shopping campaigns.
- `pinterest_keywords_list`: keywords of the account, by campaign or ad group.
- `pilot_data_freshness`: whether Pilot's collected data for this platform is current.
- `pilot_diagnose_wasted_spend`: campaigns that spend without converting, from Pilot's collected data.

## Steps

1. Call `pinterest_ad_accounts_list`. If several accounts are listed, ask which one to audit unless the user named it. Use its ID as `ad_account_id` everywhere.
2. Call `pinterest_ad_accounts_get` to read the currency. Pinterest metric names say DOLLAR whatever the account currency: label every amount with the account currency.
3. Call `pilot_data_freshness` with `platform: "pinterest"`. If Pilot has no data for Pinterest, say so and rely on the live Pinterest tools only.
4. Set the period: the last 30 days by default, as `start_date` and `end_date` in YYYY-MM-DD.
5. Read the structure: `pinterest_campaigns_list`, `pinterest_ad_groups_list`, `pinterest_ads_list`. Note paused objects that still hold budget, ad groups without active ads, and rejected ads.
6. Read the results: `pinterest_campaigns_analytics` and `pinterest_ad_groups_analytics` with `columns` such as `["SPEND_IN_DOLLAR","IMPRESSION_1","CLICK_1","CTR","TOTAL_CONVERSIONS"]`. Micro amounts are divided by 1,000,000.
7. Call `pilot_diagnose_wasted_spend` with `platform: "pinterest"` when Pilot holds data; otherwise compute spend without conversions from step 6.
8. Read the setup: `pinterest_conversion_tags_list` (a tag that never fires leaves every conversion column empty), `pinterest_audiences_list`, `pinterest_catalog_feeds_list` for shopping campaigns, `pinterest_keywords_list` for search placements.
9. Write the report, findings ranked by money at stake.

## Output

1. **Summary**: three lines on the account's state and the top three priorities.
2. **Findings**: a table with the finding, the evidence (figure, period, currency), the stake and the suggested action.
3. **Tracking**: what the conversion tags count, and any doubt about it.
4. **Next steps**: actions in order, for a person to apply in Pinterest Ads Manager.

## Rules

- A ratio with an empty denominator is not available, not zero.
- Do not judge an ad group on a handful of conversions: say the volume is too low.
- A metric Pinterest did not return is absent, not zero.
- Never sum amounts in different currencies.

## Examples

- "Audit our Pinterest Ads account for the last 30 days."
- "Fais l'audit du compte Pinterest Ads de la boutique."
- "Qu'est-ce qui coûte le plus sans rien rapporter sur Pinterest ?"
