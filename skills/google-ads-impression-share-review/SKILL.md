---
name: google-ads-impression-share-review
description: "Reviews Google Ads impression share through the Pilot MCP server: the share of eligible impressions each Search campaign won, the share lost to budget and the share lost to rank, top and absolute top of page rates, how they moved week by week, and the keywords that lose most visibility, then says whether to fix budget, bids or ad quality, or accept the loss. Use when the user asks why visibility fell, whether a campaign is held back by budget, or how much of the market the ads reach. Triggers: impression share, lost impression share, budget lost, rank lost. Déclencheurs : part d'impressions, part d'impressions perdue, perdue à cause du budget, perdue à cause du classement, visibilité Google Ads."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Ads account that runs Search campaigns."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_list_campaigns google_run_query google_get_campaign_performance google_get_keyword_performance"
  pilot-mcp-read-only: "true"
---

# Google Ads impression share review

Tell the user how much of the available search visibility each campaign wins, and whether what it loses comes from budget or from rank. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a spreadsheet server to track impression share over time. None is needed.
- **Read-only:** never change bids or budgets. Proposals are for a person to apply.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_list_campaigns`: campaigns with their channel type, to keep Search.
- `google_run_query`: a read-only GAQL query, here for impression share won and lost per campaign and week.
- `google_get_campaign_performance`: cost and conversions per campaign, to weigh each campaign.
- `google_get_keyword_performance`: impression share per keyword.

## Steps

1. Call `google_list_accounts` and pick the account. Call `google_list_campaigns` and keep the Search campaigns that spent in the period.
2. Call `google_run_query` with `SELECT campaign.name, metrics.search_impression_share, metrics.search_budget_lost_impression_share, metrics.search_rank_lost_impression_share, metrics.search_top_impression_share, metrics.search_absolute_top_impression_share, metrics.cost_micros FROM campaign WHERE segments.date DURING LAST_30_DAYS AND campaign.advertising_channel_type = 'SEARCH'`.
3. Run it again with `segments.week` added to the selected fields to see the trend week by week.
4. Call `google_get_campaign_performance` over `LAST_30_DAYS` to weigh each campaign by cost and conversions.
5. Call `google_get_keyword_performance` to find the keywords with the lowest impression share among those that convert.
6. Read the causes: impression share lost to budget means the budget runs out before the day ends; lost to rank means bids or ad quality are too low for the auctions. A campaign that loses to budget and converts well is a candidate for more budget; one that loses to rank needs better ads, landing pages or bids, at a cost.

## Output

1. **Summary**: the visibility won and lost, and the main cause.
2. **Per campaign**: a table with impression share, lost to budget, lost to rank, top and absolute top rates, cost and conversions.
3. **Trend**: week by week, for the campaigns that moved.
4. **Keywords**: converting keywords with low impression share.
5. **Options**: what a person could do (budget, bids, ad quality, or accept), with the cost of each.

## Rules

- Impression share covers Search campaigns here; it is a ratio and never adds up across campaigns.
- Google reports very low shares as a floor value (under 10%): present it as such.
- Competitors are not named: the Pilot MCP server does not read Google's auction insights, so who takes the lost impressions is not readable through the API.
- Name the period.

## Examples

- "Are our Search campaigns held back by budget or by rank?"
- "Pourquoi notre part d'impressions a baissé sur la campagne marque ?"
- "Quelle part de la visibilité disponible gagne-t-on sur Google ?"
