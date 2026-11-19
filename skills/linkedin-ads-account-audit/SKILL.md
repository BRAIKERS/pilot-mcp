---
name: linkedin-ads-account-audit
description: "Audits a LinkedIn Ads account in read-only mode through the Pilot MCP server: campaign groups and campaigns with their objective, budget, bidding and targeting, creatives with their serving and review status, conversion rules, the Insight Tag, and spend against leads and conversions, then returns a prioritized findings report. Use when the user asks to audit, review or health-check a LinkedIn Ads account. Triggers: LinkedIn Ads audit, review my LinkedIn campaigns, B2B ads health check. Déclencheurs : audit LinkedIn Ads, auditer le compte LinkedIn, bilan de santé LinkedIn, revue des campagnes LinkedIn."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a LinkedIn ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "linkedin_linkedin_ads_list_accounts linkedin_linkedin_ads_account_performance linkedin_linkedin_ads_campaign_performance linkedin_linkedin_ads_list_campaign_groups linkedin_linkedin_ads_list_campaigns linkedin_linkedin_ads_get_campaign linkedin_linkedin_ads_list_creatives linkedin_linkedin_ads_list_conversion_actions linkedin_linkedin_ads_manage_insight_tag pilot_data_freshness pilot_diagnose_wasted_spend"
  pilot-mcp-read-only: "true"
---

# LinkedIn Ads account audit

Audit one LinkedIn Ads account and hand back a short, prioritized list of findings, each backed by figures from the account. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the LinkedIn ad account.
- **Optional MCP servers:** a CRM server to check what LinkedIn leads became. None is needed.
- **Read-only:** never create, update, pause, archive or delete anything. Every recommendation is written for a person to apply.

## Tools

- `linkedin_linkedin_ads_list_accounts`: the ad accounts available, with currency and status.
- `linkedin_linkedin_ads_account_performance`: account totals for a date range.
- `linkedin_linkedin_ads_campaign_performance`: impressions, clicks, spend, leads and conversions per campaign.
- `linkedin_linkedin_ads_list_campaign_groups`: campaign groups with status, schedule and total budget.
- `linkedin_linkedin_ads_list_campaigns`: campaigns with objective, budget, bidding and status.
- `linkedin_linkedin_ads_get_campaign`: one campaign in full, including its targeting criteria.
- `linkedin_linkedin_ads_list_creatives`: creatives with intended status, serving state and review status.
- `linkedin_linkedin_ads_list_conversion_actions`: the conversion rules of the account.
- `linkedin_linkedin_ads_manage_insight_tag`: the Insight Tag of the account (read only).
- `pilot_data_freshness`: whether Pilot's collected LinkedIn data is current.
- `pilot_diagnose_wasted_spend`: campaigns that spend without converting, from Pilot's collected data.

## Steps

1. Call `linkedin_linkedin_ads_list_accounts` and pick the account (ask if several). Use its ID as `account_id`.
2. Call `pilot_data_freshness` with `platform: "linkedin"`; if Pilot holds no data, rely on the live LinkedIn tools only.
3. Set the period: the last 30 days by default, YYYY-MM-DD.
4. Call `linkedin_linkedin_ads_account_performance`, then `linkedin_linkedin_ads_campaign_performance`. Rank campaigns by spend; note those with spend and no lead or conversion.
5. Call `pilot_diagnose_wasted_spend` with `platform: "linkedin"` when Pilot holds data.
6. Read the structure: `linkedin_linkedin_ads_list_campaign_groups` and `linkedin_linkedin_ads_list_campaigns`. Remember that a LinkedIn campaign group is what Pilot calls a campaign, and a LinkedIn campaign is what Pilot calls an ad group.
7. For the campaigns that spend most, call `linkedin_linkedin_ads_get_campaign`: objective, cost type, bid, daily budget, audience expansion, and targeting criteria (too narrow, too broad, overlapping with another campaign).
8. Call `linkedin_linkedin_ads_list_creatives`: campaigns with a single active creative, creatives rejected or not serving.
9. Call `linkedin_linkedin_ads_list_conversion_actions` and `linkedin_linkedin_ads_manage_insight_tag`: website conversion campaigns need an active tag and enabled rules.
10. Write the report, findings ranked by money at stake.

## Output

1. **Summary**: three lines on the account's state and the top three priorities.
2. **Findings**: a table with the finding, the evidence (figure, period, currency), the stake and the suggested action.
3. **Tracking**: Insight Tag, conversion rules and their attribution windows.
4. **Next steps**: actions in order, for a person to apply in Campaign Manager.

## Rules

- A ratio with an empty denominator is not available, not zero.
- B2B volumes are small: do not judge a campaign on a handful of leads.
- Never sum amounts in different currencies.

## Examples

- "Audit our LinkedIn Ads account."
- "Fais l'audit du compte LinkedIn Ads, en priorisant ce qui coûte sans générer de leads."
- "Nos campagnes LinkedIn sont-elles bien ciblées et bien mesurées ?"
