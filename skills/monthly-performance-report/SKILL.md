---
name: monthly-performance-report
description: "Builds the monthly advertising report through the Pilot MCP server: spend, results and ratios of the last calendar month on Google Ads, Meta Ads and TikTok Ads, the change against the month before, the campaigns that moved most, budget status and the site traffic seen in Google Analytics 4. Use when the user asks for a monthly report, a month-end recap or last month's results. Triggers: monthly report, month-end recap, how did last month go. Déclencheurs : rapport mensuel, bilan du mois, rapport de fin de mois, résultats du mois dernier."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with at least one ad account attached. Google Analytics 4 and TikTok Ads are optional."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "pilot_data_freshness pilot_cross_platform_overview pilot_budget_pacing google_list_accounts google_get_account_summary google_get_campaign_performance meta_list_accounts meta_get_account_insights tiktok_get_advertiser_info tiktok_get_campaign_performance_summary google_analytics_run_report"
  pilot-mcp-read-only: "true"
---

# Monthly performance report

Write the report of the last calendar month across the connected ad platforms, compared with the month before, with the few campaigns that explain the change. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace concerned.
- **Optional MCP servers:** a file storage server to save the report, a team messaging server to send it, a slide or document server to lay it out. None is needed.
- **Read-only:** never change a campaign, a budget or a setting. Every recommendation is for a person to apply.

## Tools

- `pilot_data_freshness`: whether the collected data covers the whole month, per platform.
- `pilot_cross_platform_overview`: consolidated spend and results over the last days, per platform and in total.
- `pilot_budget_pacing`: capped and idle budgets.
- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_get_account_summary`: account totals between two dates.
- `google_get_campaign_performance`: cost, clicks, conversions and value per campaign and day.
- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_get_account_insights`: Meta results for an explicit window, by campaign with `level: "campaign"`.
- `tiktok_get_advertiser_info`: the TikTok advertiser, its currency and time zone.
- `tiktok_get_campaign_performance_summary`: spend, conversions, CTR and CPC per TikTok campaign between two dates.
- `google_analytics_run_report`: sessions, users and key events from GA4.

## Steps

1. Set the two months: the last full calendar month (M) and the one before (M-1), with their first and last days. Use the dates the user gives if any.
2. Call `pilot_data_freshness`. If a platform is not covered through the last day of M, say so at the top and keep its figures apart.
3. Google Ads: `google_list_accounts`, then `google_get_account_summary` with `start_date` and `end_date` for M, then for M-1. Call `google_get_campaign_performance` on M with `limit: 1000` and sum the days per campaign; do the same for M-1 to rank the campaigns that moved most.
4. Meta Ads: `meta_list_accounts`, then `meta_get_account_insights` with `level: "campaign"` and `time_range` set to M, then to M-1.
5. TikTok Ads, if attached: `tiktok_get_advertiser_info`, then `tiktok_get_campaign_performance_summary` with the dates of M, then of M-1.
6. Call `pilot_budget_pacing` with `days` equal to the length of M for the budget picture. `pilot_cross_platform_overview` counts back from today: use it as a check on totals only when M ends yesterday.
7. Call `google_analytics_run_report` with two `dateRanges` (M and M-1), `dimensions: ["sessionDefaultChannelGroup"]` and `metrics: ["sessions", "totalUsers", "keyEvents"]`.
8. Write the report. Explain each large change by the two or three campaigns that carry it.

## Output

1. **Headline**: one sentence on the month.
2. **By platform**: a table with spend, conversions, value, CPA and ROAS for M and M-1 and the change, one row per platform and currency.
3. **What moved**: the campaigns behind the largest changes, with their figures.
4. **Budgets**: capped and idle budgets at month end.
5. **Site traffic**: sessions and key events by channel, M against M-1.
6. **Next month**: three points to act on, for a person to decide.

## Rules

- Name the exact dates of both months and the currency of each amount.
- Never sum amounts in different currencies: give one total per currency.
- A ratio with an empty denominator is not available, not zero.
- Each platform counts conversions its own way: never add Google Ads, Meta and TikTok conversions into one figure without saying so.
- Data the platform did not return is absent, not zero.

## Examples

- "Give me the monthly report for last month."
- "Fais le bilan du mois d'octobre sur Google Ads, Meta et TikTok, comparé à septembre."
- "Prépare le rapport mensuel du client avec le trafic GA4."
