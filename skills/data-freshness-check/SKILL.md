---
name: data-freshness-check
description: "Checks whether advertising and analytics data can be trusted today through the Pilot MCP server: how current Pilot's collected data is per platform, whether a gap comes from a failing collection or from an account that stopped delivering, and whether each platform still reports spend live, on Google Ads, Meta Ads, TikTok Ads and Google Analytics 4. Use before a report, when figures look stuck or missing, or when a dashboard shows zero. Triggers: is the data up to date, data freshness, missing data, figures look stuck. Déclencheurs : fraîcheur des données, données à jour, données manquantes, chiffres bloqués, trou de données."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with at least one platform attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "pilot_data_freshness pilot_cross_platform_overview google_list_accounts google_get_account_spend_today google_get_campaign_performance meta_list_accounts meta_check_account_issues meta_get_daily_spend_breakdown tiktok_get_advertiser_info tiktok_get_account_status tiktok_get_daily_performance_trend google_analytics_run_report"
  pilot-mcp-read-only: "true"
---

# Data freshness check

Tell the user, platform by platform, through which day the data is reliable, and who a gap is due to: Pilot's collection, or the account itself. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace concerned.
- **Optional MCP servers:** a team messaging server to warn the team. None is needed.
- **Read-only:** never reconnect, change or relaunch anything. Fixes are for a person to apply.

## Tools

- `pilot_data_freshness`: per platform, data covered through, last collection and its status, delay in days and the cause.
- `pilot_cross_platform_overview`: what Pilot has collected over the last days, per platform.
- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_get_account_spend_today`: spend today, read live from Google Ads, in micros.
- `google_get_campaign_performance`: daily cost per campaign, read live.
- `meta_list_accounts`: the Meta ad accounts and their status.
- `meta_check_account_issues`: a disabled account, a disable reason or a spend cap reached.
- `meta_get_daily_spend_breakdown`: daily Meta spend, read live.
- `tiktok_get_advertiser_info`: the TikTok advertiser attached.
- `tiktok_get_account_status`: restrictions or compliance issues on the TikTok account.
- `tiktok_get_daily_performance_trend`: daily TikTok spend, read live.
- `google_analytics_run_report`: daily sessions in GA4.

## Steps

1. Call `pilot_data_freshness` without `platform` to get every platform. Keep its cause for each one: up to date, late on Pilot's side, or an account that stopped delivering.
2. Call `pilot_cross_platform_overview` with `days: 7` to see which platforms have collected figures this week.
3. For each platform that is late or empty, read the platform live to split the two causes:
   - Google Ads: `google_list_accounts`, `google_get_account_spend_today`, then `google_get_campaign_performance` over `LAST_7_DAYS`.
   - Meta Ads: `meta_list_accounts`, `meta_check_account_issues`, then `meta_get_daily_spend_breakdown` with `date_preset: "last_7d"`.
   - TikTok Ads: `tiktok_get_advertiser_info`, `tiktok_get_account_status`, then `tiktok_get_daily_performance_trend` over the last seven days.
4. GA4: `google_analytics_run_report` with `dimensions: ["date"]`, `metrics: ["sessions"]` over the last seven days. A day at zero sessions on a site that has traffic is a tracking gap.
5. Conclude per platform: if the platform shows live spend that Pilot has not collected, the gap is on the collection side; if the platform shows no spend either, the account stopped delivering.

## Output

1. **Verdict**: a table with platform, data reliable through, cause, and what to do.
2. **Collection gaps**: platforms where the platform has data that Pilot has not collected, with the dates.
3. **Account issues**: accounts disabled, restricted, capped or with no delivery.
4. **What to tell the reader of a report**: which days to leave out until the data is complete.

## Rules

- A missing day is absent data, never a zero.
- Platforms often revise the last two or three days: say that recent figures can still move.
- Google amounts are in micros: divide by 1,000,000.
- Do not guess a cause: if the live read fails too, say both reads failed.

## Examples

- "Is our data up to date before I send the report?"
- "Les chiffres Meta n'ont pas bougé depuis deux jours : c'est la collecte ou le compte ?"
- "Vérifie la fraîcheur des données de toutes les régies."
