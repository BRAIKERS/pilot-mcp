---
name: linkedin-ads-performance-report
description: "Builds a LinkedIn Ads performance report through the Pilot MCP server: account totals, monthly or daily trend, results by campaign group, campaign and creative, with spend, clicks, leads, website conversions, cost per lead and the change against the previous period. Use when the user wants LinkedIn numbers, a monthly LinkedIn report, or to know which campaigns bring leads. Triggers: LinkedIn Ads report, LinkedIn cost per lead, LinkedIn performance this month. Déclencheurs : rapport LinkedIn Ads, coût par lead LinkedIn, performance LinkedIn du mois, résultats LinkedIn."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a LinkedIn ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "linkedin_linkedin_ads_list_accounts linkedin_linkedin_ads_account_performance linkedin_linkedin_ads_campaign_performance linkedin_linkedin_ads_analytics linkedin_linkedin_ads_list_campaign_groups linkedin_linkedin_ads_list_campaigns linkedin_linkedin_ads_list_creatives"
  pilot-mcp-read-only: "true"
---

# LinkedIn Ads performance report

Give the user a clear LinkedIn Ads report for the period asked: totals, trend, which campaigns and creatives bring leads, and at what cost. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the LinkedIn ad account.
- **Optional MCP servers:** a spreadsheet server to export the tables, a CRM server to follow leads further. None is needed.
- **Read-only:** never change a campaign or a creative.

## Tools

- `linkedin_linkedin_ads_list_accounts`: the ad accounts available, with currency.
- `linkedin_linkedin_ads_account_performance`: account totals, overall, daily or monthly.
- `linkedin_linkedin_ads_campaign_performance`: metrics per campaign.
- `linkedin_linkedin_ads_analytics`: one pivot at a time (campaign group, creative, conversion...) with chosen fields.
- `linkedin_linkedin_ads_list_campaign_groups`: campaign group names and status.
- `linkedin_linkedin_ads_list_campaigns`: campaign names, objectives and groups.
- `linkedin_linkedin_ads_list_creatives`: creatives with their campaign and status.

## Steps

1. Call `linkedin_linkedin_ads_list_accounts` and pick the account. Note its currency.
2. Fix the period and a comparison period of the same length, in YYYY-MM-DD.
3. Call `linkedin_linkedin_ads_account_performance` for both periods with `time_granularity: "ALL"`, then with `"DAILY"` or `"MONTHLY"` for the trend.
4. Call `linkedin_linkedin_ads_campaign_performance` for both periods. Name the rows with `linkedin_linkedin_ads_list_campaigns` and `linkedin_linkedin_ads_list_campaign_groups`.
5. Call `linkedin_linkedin_ads_analytics` with `pivot: "CAMPAIGN_GROUP"` to roll results up by group, and with `pivot: "CREATIVE"` to rank creatives. Ask for fields such as `costInLocalCurrency`, `impressions`, `clicks`, `landingPageClicks`, `oneClickLeads` and `externalWebsiteConversions`.
6. Name creatives with `linkedin_linkedin_ads_list_creatives`.
7. Compute cost per lead and cost per conversion only where the count is above zero.

## Output

1. **Headline**: spend, leads, conversions, cost per lead, with the change against the comparison period.
2. **Trend**: a short table by day or month.
3. **Campaign groups and campaigns**: tables ranked by spend, with leads and cost per lead.
4. **Creatives**: the best and weakest, with their figures.
5. **What to watch**: two or three points.

## Rules

- Name the period and the currency of every amount.
- A cost per lead with zero leads is not available, not zero.
- Remember the vocabulary: a LinkedIn campaign group is a campaign in Pilot, a LinkedIn campaign is an ad group.

## Examples

- "Give me the LinkedIn Ads report for last month."
- "Fais le rapport LinkedIn du trimestre, avec le coût par lead par campagne."
- "Quelles publicités LinkedIn génèrent le plus de leads ?"
