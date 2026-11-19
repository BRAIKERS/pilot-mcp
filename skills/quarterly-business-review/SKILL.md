---
name: quarterly-business-review
description: "Prepares a quarterly business review through the Pilot MCP server: the quarter's spend, results and ratios on Google Ads, Meta Ads and TikTok Ads against the quarter before and the same quarter last year, the campaigns that drove the change, how concentrated spend is, the experiments run, and the site traffic seen in Google Analytics 4, ending with the plan for the next quarter. Use when the user asks for a QBR, a quarterly review or a quarter-end deck. Triggers: quarterly business review, QBR, quarter review, quarter over quarter. Déclencheurs : revue trimestrielle, bilan du trimestre, QBR, comparaison trimestre sur trimestre."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with at least one ad account attached. Google Analytics 4 and TikTok Ads are optional."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "pilot_data_freshness pilot_account_concentration google_list_accounts google_get_account_summary google_get_campaign_performance google_list_experiments meta_list_accounts meta_get_account_insights tiktok_get_advertiser_info tiktok_get_campaign_performance_summary google_analytics_run_report"
  pilot-mcp-read-only: "true"
---

# Quarterly business review

Build the material for a quarterly review: what the quarter delivered, against the quarter before and last year, why, and what to do next quarter. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace concerned.
- **Optional MCP servers:** a slide or document server to lay out the review. None is needed.
- **Read-only:** never change an account. The plan is for people to decide.

## Tools

- `pilot_data_freshness`: whether the whole quarter is collected.
- `pilot_account_concentration`: how many campaigns carried 80% of the spend.
- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_get_account_summary`: Google Ads totals between two dates.
- `google_get_campaign_performance`: cost, conversions and value per campaign and day.
- `google_list_experiments`: the Google Ads experiments run.
- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_get_account_insights`: Meta results for an explicit window, by campaign.
- `tiktok_get_advertiser_info`: the TikTok advertiser and its currency.
- `tiktok_get_campaign_performance_summary`: TikTok results per campaign between two dates.
- `google_analytics_run_report`: sessions, key events and revenue by channel in GA4.

## Steps

1. Set three windows: the last full quarter (Q), the quarter before (Q-1) and the same quarter last year (Q-4). Use the user's fiscal quarters if given.
2. Call `pilot_data_freshness`. A window that platforms no longer return in full is reported as incomplete.
3. Google Ads: `google_get_account_summary` with `start_date` and `end_date` for each window. Call `google_get_campaign_performance` on Q with `limit: 1000`; if the row count reaches the limit, read the quarter month by month. Call `google_list_experiments` to list the tests of the quarter.
4. Meta Ads: `meta_get_account_insights` with `level: "account"` and `time_range` for each window, then `level: "campaign"` on Q.
5. TikTok Ads, if attached: `tiktok_get_advertiser_info`, then `tiktok_get_campaign_performance_summary` for Q and Q-1.
6. Call `pilot_account_concentration` with `days: 90`.
7. GA4: `google_analytics_run_report` with `dateRanges` for Q and Q-4, `dimensions: ["sessionDefaultChannelGroup"]` and `metrics: ["sessions", "keyEvents", "totalRevenue"]`.
8. Explain the changes by campaigns, tests and seasonality; end with three to five priorities for next quarter.

## Output

1. **The quarter in three lines**.
2. **Results**: a table per platform and currency with spend, conversions, value, CPA and ROAS for Q, Q-1 and Q-4.
3. **Drivers**: the campaigns and tests behind the changes.
4. **Risks**: concentration, stale data, falling channels.
5. **Next quarter**: priorities, each tied to a figure.

## Rules

- Name the dates of each window and the currency of each amount; never sum two currencies.
- Compare a quarter with the same quarter last year before calling a change a trend: seasonality explains a lot.
- A window the platform no longer returns in full is incomplete, not low.
- Platform conversions and GA4 key events are kept apart.

## Examples

- "Prepare our QBR for the last quarter."
- "Fais la revue trimestrielle : T3 contre T2 et contre T3 de l'an dernier."
- "Quels ont été les moteurs du trimestre, et que faire au prochain ?"
