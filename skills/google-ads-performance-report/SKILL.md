---
name: google-ads-performance-report
description: "Builds a Google Ads performance report through the Pilot MCP server: account totals for the period against the period before, results per campaign, the ads, keywords and locations that carry spend and conversions, with CPA, ROAS and conversion rate recomputed on the totals. Use when the user asks for a Google Ads report, Google Ads results for a period or a campaign-by-campaign view. Triggers: Google Ads report, Google Ads results, campaign performance Google. Déclencheurs : rapport Google Ads, résultats Google Ads, performance des campagnes Google, bilan Google Ads."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Ads account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_get_account_info google_get_account_summary google_get_campaign_performance google_get_ad_performance google_get_keyword_performance google_get_location_performance"
  pilot-mcp-read-only: "true"
---

# Google Ads performance report

Report what a Google Ads account delivered over a period, what changed against the period before, and which campaigns, ads and keywords explain it. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a file storage or slide server to keep the report. None is needed.
- **Read-only:** never change anything in the account. Recommendations are for a person to apply.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace, with their IDs.
- `google_get_account_info`: account name, currency and time zone.
- `google_get_account_summary`: account totals between two dates or for a preset.
- `google_get_campaign_performance`: cost, clicks, conversions and value per campaign and day.
- `google_get_ad_performance`: the same metrics per ad, by cost.
- `google_get_keyword_performance`: keyword metrics with search impression share.
- `google_get_location_performance`: cost and results by location.

## Steps

1. Call `google_list_accounts` and pick the account (ask if several). Use its ID as `customer_id` everywhere.
2. Call `google_get_account_info` for the currency and the time zone.
3. Set the period: the last 30 days by default, or the period the user gives, and the period of the same length just before.
4. Call `google_get_account_summary` for each period, with `start_date` and `end_date`.
5. Call `google_get_campaign_performance` for each period with `limit: 1000`, and sum the days per campaign. If the row count equals the limit, shorten the period or filter with `campaign_id`.
6. Call `google_get_ad_performance` and `google_get_keyword_performance` on the current period to name the ads and keywords that carry the results, and `google_get_location_performance` for the places.
7. Recompute CTR, CPC, CPA, ROAS and conversion rate from the totals, never as averages of rows.

## Output

1. **Headline**: one sentence on the period.
2. **Account**: a table with impressions, clicks, cost, conversions, value, CPA and ROAS for both periods and the change.
3. **Campaigns**: the same per campaign, ranked by cost, with the largest changes marked.
4. **What carries the results**: top ads, keywords and locations.
5. **To watch**: three points for a person to act on.

## Rules

- Name both periods with their dates, and the currency.
- A ratio with an empty denominator is not available, not zero.
- Conversions of the last two or three days can still be added: say so when the period ends yesterday.
- Cost is returned in the account currency; amounts in micros are divided by 1,000,000.

## Examples

- "Give me the Google Ads report for the last 30 days."
- "Fais le rapport Google Ads du mois dernier, campagne par campagne."
- "Quels mots-clés et quelles annonces portent les conversions ce mois-ci ?"
