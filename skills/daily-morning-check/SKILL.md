---
name: daily-morning-check
description: "Runs the morning check of the ad accounts through the Pilot MCP server: yesterday's spend and results on Google Ads, Meta Ads and TikTok Ads against the average of the six days before, spend so far today, the changes made to the accounts since yesterday and the Meta ads rejected or with issues, in a short list a media buyer reads before starting the day. Use when the user asks what happened yesterday, for a morning check or a start-of-day review. Triggers: morning check, what happened yesterday, yesterday's results, start of day review. Déclencheurs : point du matin, résultats d'hier, que s'est-il passé hier, revue du matin, point quotidien."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with Google Ads, Meta Ads or TikTok Ads attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "pilot_data_freshness google_list_accounts google_get_campaign_performance google_get_account_spend_today google_get_change_history meta_list_accounts meta_get_account_insights meta_get_ad_account_activities meta_get_ad_rejections tiktok_get_advertiser_info tiktok_get_daily_performance_trend"
  pilot-mcp-read-only: "true"
---

# Daily morning check

Tell the user, in a few lines, how yesterday went against a normal day, what was changed and what needs attention this morning. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace concerned.
- **Optional MCP servers:** a team messaging server to post the check. None is needed.
- **Read-only:** never pause, fix or relaunch anything. Actions are for a person.

## Tools

- `pilot_data_freshness`: whether yesterday is already collected.
- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_get_campaign_performance`: cost and conversions per campaign and day.
- `google_get_account_spend_today`: spend so far today, in micros.
- `google_get_change_history`: changes made to the Google Ads account, by whom and when.
- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_get_account_insights`: Meta results day by day with `time_increment: 1`.
- `meta_get_ad_account_activities`: who changed what on the Meta account.
- `meta_get_ad_rejections`: Meta ads disapproved or with issues.
- `tiktok_get_advertiser_info`: the TikTok advertiser attached.
- `tiktok_get_daily_performance_trend`: TikTok spend and results day by day.

## Steps

1. Call `pilot_data_freshness`. If yesterday is not collected yet for a platform, read that platform live and say so.
2. Google Ads: `google_list_accounts`, then `google_get_campaign_performance` with `date_range: "LAST_7_DAYS"`. Compare yesterday with the average of the six days before, per campaign and in total. Call `google_get_account_spend_today` for today so far, and `google_get_change_history` with `change_date_range: "YESTERDAY"`, then `TODAY`.
3. Meta Ads: `meta_list_accounts`, then `meta_get_account_insights` with `level: "campaign"`, `date_preset: "last_7d"` and `time_increment: 1`. Call `meta_get_ad_account_activities` with `since` set to yesterday morning, and `meta_get_ad_rejections`.
4. TikTok Ads, if attached: `tiktok_get_advertiser_info`, then `tiktok_get_daily_performance_trend` over the last seven days.
5. Flag a campaign when yesterday's spend or conversions differ from its six-day average by more than about 30%, and the campaign spent enough to matter.

## Output

1. **Yesterday in one line**: spend and conversions per platform, against a normal day.
2. **To look at**: campaigns with an unusual day, with yesterday's figure and the six-day average.
3. **Changes since yesterday**: who changed what, on which platform.
4. **Rejected ads**: Meta ads disapproved or with issues.
5. **Today so far**: Google Ads spend at the time of the check.

## Rules

- Keep it short: ten lines at most unless something is wrong.
- Days are in each account's time zone; say so if accounts differ.
- Yesterday's conversions can still rise during the day: present them as provisional.
- Never sum amounts in different currencies.
- Google amounts are in micros: divide by 1,000,000.

## Examples

- "Morning check: how did yesterday go?"
- "Fais-moi le point du matin sur Google, Meta et TikTok."
- "Qu'est-ce qui a changé sur les comptes depuis hier ?"
