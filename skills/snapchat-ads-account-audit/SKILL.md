---
name: snapchat-ads-account-audit
description: "Audits a Snapchat Ads account in read-only mode through the Pilot MCP server: account settings, campaigns, ad squads and ads with their status, budgets and bids, creatives, pixels, audiences and daily spend, then returns a prioritized findings report. Use when the user asks to audit, review or health-check a Snapchat Ads account. Triggers: Snapchat Ads audit, review my Snapchat account, Snap ads health check. Déclencheurs : audit Snapchat Ads, auditer le compte Snapchat, bilan de santé Snapchat, revue du compte Snap."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Snapchat ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "search snapchat_get_campaign snapchat_get_ad_account snapchat_get_ad_account_stats snapchat_list_campaigns snapchat_list_ad_squads snapchat_list_ads snapchat_list_creatives snapchat_list_pixels snapchat_list_audiences pilot_data_freshness pilot_diagnose_wasted_spend"
  pilot-mcp-read-only: "true"
---

# Snapchat Ads account audit

Audit one Snapchat Ads account and hand back a short, prioritized list of findings, each backed by what the account shows. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Snapchat ad account.
- **Optional MCP servers:** a file storage server to save the report, a team messaging server to share it. None is needed.
- **Read-only:** never create, update, pause or delete anything. Every recommendation is written for a person to apply.

## Tools

- `search`: finds campaigns of the workspace by name, with IDs such as `campaign:snapchat:<id>`.
- `snapchat_get_campaign`: one campaign in full, including its ad account ID.
- `snapchat_get_ad_account`: the account settings, currency and status.
- `snapchat_get_ad_account_stats`: the account spend by day over a time range.
- `snapchat_list_campaigns`: campaigns with status, objective, budget and dates.
- `snapchat_list_ad_squads`: ad squads with status, optimization goal, bid, budget and targeting.
- `snapchat_list_ads`: ads with status and review state.
- `snapchat_list_creatives`: creatives of the account.
- `snapchat_list_pixels`: pixels of the account.
- `snapchat_list_audiences`: audience segments and their state.
- `pilot_data_freshness`: whether Pilot's collected data for this platform is current.
- `pilot_diagnose_wasted_spend`: campaigns that spend without converting, from Pilot's collected data.

## Steps

1. Get the ad account ID. If the user does not give it, call `search` with a campaign name, then `snapchat_get_campaign` on one result: the campaign carries its `ad_account_id`.
2. Call `snapchat_get_ad_account` for the currency, time zone and status. A suspended or unpaid account explains more than any setting.
3. Call `pilot_data_freshness` with `platform: "snapchat"`. If Pilot holds no Snapchat data, say so: results per campaign will be missing from the audit.
4. Call `snapchat_get_ad_account_stats` for the last 30 days with `granularity: "DAY"` and ISO 8601 `start_time` and `end_time`. Snapchat returns only spend at this level: note spend gaps and spikes.
5. Read the structure: `snapchat_list_campaigns`, `snapchat_list_ad_squads`, `snapchat_list_ads`. Flag active campaigns with no active ad squad, ad squads with no active ad, rejected ads, ended campaigns still active, and budgets or bids in micro units that look out of scale.
6. Read the setup: `snapchat_list_pixels` (is there a pixel, and is it used by conversion-optimized ad squads), `snapchat_list_audiences` (segments too small or expired), `snapchat_list_creatives` (ads sharing one tired creative).
7. When Pilot holds Snapchat data, call `pilot_diagnose_wasted_spend` with `platform: "snapchat"`.
8. Write the report, findings ranked by money at stake.

## Output

1. **Summary**: three lines on the account's state and the top three priorities.
2. **Findings**: a table with the finding, the evidence, the stake and the suggested action.
3. **Measurement**: pixel and conversion setup, and what is missing.
4. **Next steps**: actions in order, for a person to apply in Snapchat Ads Manager.

## Rules

- Snapchat amounts are in micro units of the account currency: divide by 1,000,000 and name the currency.
- Results per campaign that Pilot did not collect are absent, not zero.
- Never sum amounts in different currencies.

## Examples

- "Audit our Snapchat Ads account."
- "Fais l'audit du compte Snapchat Ads, en commençant par ce qui coûte le plus."
- "Le compte Snap est-il bien configuré pour mesurer les ventes ?"
