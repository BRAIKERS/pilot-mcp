---
name: tiktok-ads-account-audit
description: "Audits a TikTok Ads account in read-only mode through the Pilot MCP server: advertiser status and restrictions, balance, campaign and ad group structure, results by campaign and ad group, pixels, audiences and identities, then returns a prioritized findings report. Use when the user asks to audit, review or health-check a TikTok Ads account. Triggers: TikTok ads audit, review TikTok account, TikTok account health check. Déclencheurs : audit TikTok Ads, audit du compte TikTok, bilan de santé TikTok, revue de compte TikTok."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a TikTok Ads advertiser attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "tiktok_get_advertiser_info tiktok_get_account_status tiktok_get_advertiser_balance tiktok_get_campaigns tiktok_get_ad_groups tiktok_get_campaign_performance_summary tiktok_get_adgroup_performance tiktok_get_pixels tiktok_get_custom_audiences tiktok_get_identities"
  pilot-mcp-read-only: "true"
---

# TikTok Ads account audit

Audit one TikTok Ads advertiser and hand back a short, prioritized list of findings, each backed by figures from the account. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the advertiser.
- **Optional MCP servers:** a file storage server to save the report. None is needed.
- **Read-only:** never call a tool that creates, updates, disables or deletes anything. Every recommendation is for a person to apply.

## Tools

- `tiktok_get_advertiser_info`: the advertiser, its currency and time zone.
- `tiktok_get_account_status`: restrictions, punishments and compliance issues.
- `tiktok_get_advertiser_balance`: the account balance.
- `tiktok_get_campaigns`: campaigns with status, objective and budget.
- `tiktok_get_ad_groups`: ad groups with status, placements, budget and optimization goal.
- `tiktok_get_campaign_performance_summary`: spend, conversions, CTR and CPC per campaign.
- `tiktok_get_adgroup_performance`: results per ad group.
- `tiktok_get_pixels`: the advertiser's pixels.
- `tiktok_get_custom_audiences`: custom audiences.
- `tiktok_get_identities`: the identities ads run under.

## Steps

1. Call `tiktok_get_advertiser_info` to identify the advertiser. Use its ID as `advertiser_id`.
2. Call `tiktok_get_account_status` and `tiktok_get_advertiser_balance`: a restriction or an empty balance comes first.
3. Call `tiktok_get_campaigns` and `tiktok_get_ad_groups` with `page_size: 100`, page by page.
4. Call `tiktok_get_campaign_performance_summary` over the last 30 days, then `tiktok_get_adgroup_performance` with `campaign_id` for the campaigns that spend most.
5. Call `tiktok_get_pixels`, `tiktok_get_custom_audiences` and `tiktok_get_identities`.
6. Look for: budget split across many small ad groups, ad groups optimizing on clicks for a sales goal, conversion campaigns without a pixel, audiences unused or expired, ad groups that spend without conversions.
7. Write the report, findings ranked by money at stake.

## Output

1. **Summary**: three lines on the account's state and the top three priorities.
2. **Findings**: a table with finding, evidence (figure and period), stake and suggested action.
3. **Tracking**: pixels and what the conversion ad groups optimize for.
4. **Next steps**: actions in order, for a person to apply in TikTok Ads Manager.

## Rules

- Automated rules and TikTok's ad group diagnoses are not read here: say so if the user asks about them.
- A ratio with an empty denominator is not available, not zero.
- Do not judge an ad group with too little volume.
- Name the period and the currency.

## Examples

- "Audit our TikTok Ads account."
- "Fais l'audit du compte TikTok du client, priorisé par enjeu."
- "Le compte TikTok a-t-il des restrictions ou des problèmes de configuration ?"
