---
name: account-concentration-review
description: "Reviews how concentrated advertising spend is through the Pilot MCP server: how many campaigns carry 80% of the spend, how steady those campaigns are day by day, whether they are capped by their budget, and how much impression share the top Google Ads campaigns lose, then rates the dependency risk. Use when the user asks whether the account depends on too few campaigns, what happens if a key campaign breaks, or how diversified spend is. Triggers: spend concentration, dependency risk, top campaigns share. Déclencheurs : concentration de la dépense, dépendance à quelques campagnes, risque de concentration, part des campagnes principales."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with Google Ads or Meta Ads attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "pilot_account_concentration pilot_cross_platform_overview pilot_budget_pacing google_list_accounts google_get_campaign_performance google_run_query meta_list_accounts meta_get_daily_spend_breakdown search fetch"
  pilot-mcp-read-only: "true"
---

# Account concentration review

Tell the user whether results rest on a handful of campaigns, how fragile those campaigns are, and what would spread the risk. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace concerned.
- **Optional MCP servers:** a document server to keep the review. None is needed.
- **Read-only:** never change a campaign or a budget. Suggestions are for a person to apply.

## Tools

- `pilot_account_concentration`: the number of campaigns carrying 80% of the spend and the top campaigns with their share.
- `pilot_cross_platform_overview`: spend and results per platform, to see the split between platforms.
- `pilot_budget_pacing`: whether the top campaigns are capped by their budget.
- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_get_campaign_performance`: daily cost and conversions of a campaign.
- `google_run_query`: a read-only GAQL query, here for the impression share the top campaigns win and lose.
- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_get_daily_spend_breakdown`: daily Meta spend by campaign.
- `search`: finds a campaign by name across platforms.
- `fetch`: a campaign found by `search`, with its recent metrics.

## Steps

1. Call `pilot_account_concentration` with `days: 30` and `limit: 10`, then once per platform (`platform: "google"`, `platform: "meta"`) to see the concentration inside each platform.
2. Call `pilot_cross_platform_overview` with `days: 30` to see how spend splits between platforms.
3. For each campaign in the top list, use `search` with its name and `fetch` with the returned ID to read its recent metrics and its platform.
4. Google Ads top campaigns: `google_get_campaign_performance` with `campaign_id` over `LAST_30_DAYS` to see how steady cost and conversions are day by day; `google_run_query` with `SELECT campaign.name, metrics.search_impression_share, metrics.search_budget_lost_impression_share, metrics.search_rank_lost_impression_share FROM campaign WHERE segments.date DURING LAST_30_DAYS` to see whether they lose visibility to budget or to rank.
5. Meta top campaigns: `meta_get_daily_spend_breakdown` with `level: "campaign"` and `date_preset: "last_30d"`.
6. Call `pilot_budget_pacing` with `days: 30`: a top campaign that is capped cannot absorb more budget.
7. Rate the risk: high when one or two campaigns carry 80% of the spend, or when a top campaign is unsteady or loses a growing share of impressions to rank.

## Output

1. **Concentration**: the number of campaigns carrying 80% of the spend, overall and per platform.
2. **Top campaigns**: a table with name, platform, share of spend, conversions, daily steadiness and budget status.
3. **Fragility**: what would happen to results if the top campaign stopped, on the last 30 days' figures.
4. **Risk level and moves**: low, medium or high, with the moves that would spread it, for a person to decide.

## Rules

- Amounts are summed as collected, without currency conversion: if several currencies coexist, read the concentration per currency.
- A brand search campaign that carries most conversions is a normal pattern: say so rather than calling it a risk.
- Impression share is a ratio: never add it across campaigns. Competitors are not named through the API.
- Name the period of each figure.

## Examples

- "Does this account depend on too few campaigns?"
- "Combien de campagnes portent 80 % de la dépense, et sont-elles fragiles ?"
- "Que se passe-t-il si la campagne principale s'arrête ?"
