---
name: meta-ads-performance-report
description: "Builds a Meta Ads performance report through the Pilot MCP server: key figures of the account over the period against the period before, results by campaign side by side, the ads that carried the results, and the change week by week, with spend, results, cost per result, ROAS, reach and frequency. Use when the user asks for a Meta, Facebook or Instagram ads report or last period's Meta results. Triggers: Meta ads report, Facebook ads results, Instagram ads performance. Déclencheurs : rapport Meta Ads, résultats Facebook Ads, performance Instagram, bilan Meta."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Meta ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "meta_list_accounts meta_get_account_performance_summary meta_compare_periods_performance meta_get_account_insights meta_compare_campaigns_performance meta_get_top_performing_ads"
  pilot-mcp-read-only: "true"
---

# Meta Ads performance report

Report what a Meta ad account delivered over a period, what changed, and which campaigns and ads explain it. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a file storage or slide server to keep the report. None is needed.
- **Read-only:** never change a campaign, an ad set or an ad.

## Tools

- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_get_account_performance_summary`: spend, impressions, clicks, CTR, CPC, CPM, reach, conversions, purchase value and ROAS over a preset.
- `meta_compare_periods_performance`: account insights over two presets.
- `meta_get_account_insights`: results by campaign, ad set or ad, for a preset or an explicit window, split by days with `time_increment`.
- `meta_compare_campaigns_performance`: chosen campaigns side by side.
- `meta_get_top_performing_ads`: the best ads on a metric.

## Steps

1. Call `meta_list_accounts` and pick the account. Note its currency.
2. Set the period: the last 30 days by default.
3. Call `meta_get_account_performance_summary` with `date_preset: "last_30d"`.
4. For the period before, call `meta_get_account_insights` with `level: "account"` and `time_range` set to the 30 days before. `meta_compare_periods_performance` reads presets as Meta defines them (`last_14d` contains `last_7d`), so use it only for overlapping views.
5. Call `meta_get_account_insights` with `level: "campaign"` for both windows, and rank campaigns by spend.
6. Call `meta_compare_campaigns_performance` with the top `campaign_ids` and the metrics that match the goal (spend, results, cost per result, ROAS).
7. Call `meta_get_account_insights` with `level: "account"`, `date_preset: "last_30d"` and `time_increment: 7` for the weekly trend.
8. Call `meta_get_top_performing_ads` with the goal's metric over `last_30d`.

## Output

1. **Headline**: one sentence on the period.
2. **Account**: a table with spend, results, cost per result, ROAS, reach and frequency for both windows and the change.
3. **Campaigns**: the same per campaign.
4. **Weekly trend**: four weeks, to show direction.
5. **Best ads**: those that carried the results.
6. **To watch**: three points for a person.

## Rules

- Meta omits an action type with no event: a missing conversion count is zero only if the campaign optimizes for that event and delivered; otherwise say it is absent.
- Never average ROAS across campaigns: recompute it from value and spend.
- Name the windows with their dates, the attribution setting and the currency.

## Examples

- "Give me the Meta Ads report for the last 30 days."
- "Fais le bilan Facebook et Instagram du mois, campagne par campagne."
- "Quelles campagnes Meta ont porté les ventes ce mois-ci ?"
