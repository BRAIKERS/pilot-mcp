---
name: google-ads-bidding-strategy-review
description: "Reviews Google Ads bidding through the Pilot MCP server: the bidding strategy and targets of each campaign, portfolio strategies, whether each campaign has enough conversions for automated bidding, the conversion goals it bids on, data exclusions and seasonality adjustments in place, and Google's bid simulations, then proposes strategy or target changes for a person to apply. Use when the user asks whether the bidding strategy is right, whether to move to target CPA or target ROAS, or why automated bidding behaves oddly. Triggers: bidding strategy review, target CPA, target ROAS, smart bidding check. Déclencheurs : stratégie d'enchères, CPA cible, ROAS cible, enchères automatiques, revue des enchères."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Ads account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_list_campaigns google_list_bidding_strategies google_run_query google_get_campaign_performance google_simulate_bid_change google_list_conversion_goals google_list_bidding_data_exclusions google_list_seasonality_adjustments"
  pilot-mcp-read-only: "true"
---

# Google Ads bidding strategy review

Check that each campaign bids the right way for its goal and its volume, and propose changes with their evidence. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** none.
- **Read-only:** never change a strategy, a target or a bid. Proposals are for a person to apply.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_list_campaigns`: campaigns with status and channel type.
- `google_list_bidding_strategies`: portfolio strategies and their targets.
- `google_run_query`: a read-only GAQL query, here for each campaign's bidding strategy type and targets.
- `google_get_campaign_performance`: cost, conversions and value per campaign.
- `google_simulate_bid_change`: projected clicks, impressions and cost at other bid levels.
- `google_list_conversion_goals`: which conversion goals are biddable, for the account and per campaign.
- `google_list_bidding_data_exclusions`: periods Smart Bidding is told to ignore.
- `google_list_seasonality_adjustments`: expected conversion rate changes declared to Smart Bidding.

## Steps

1. Call `google_list_accounts` and pick the account. Call `google_list_campaigns` for the active campaigns.
2. Call `google_run_query` with `SELECT campaign.name, campaign.bidding_strategy_type, campaign.bidding_strategy, campaign.target_cpa.target_cpa_micros, campaign.target_roas.target_roas, campaign.maximize_conversions.target_cpa_micros, campaign.maximize_conversion_value.target_roas FROM campaign WHERE campaign.status = 'ENABLED'`.
3. Call `google_list_bidding_strategies` for the portfolio strategies and the campaigns that share them.
4. Call `google_get_campaign_performance` over `LAST_30_DAYS` and compute each campaign's conversions, CPA and ROAS. Compare actual CPA or ROAS with the target.
5. Call `google_list_conversion_goals` for the account, then with `campaign_id` for campaigns that override it: a campaign bidding on a goal that is not the business goal is a finding.
6. Call `google_list_bidding_data_exclusions` and `google_list_seasonality_adjustments`: an exclusion or adjustment still active after its event distorts bidding.
7. For manual or enhanced CPC campaigns, call `google_simulate_bid_change` to show what other bid levels would bring.
8. Judge each campaign: a target strategy with very few conversions a month is starved; a target far from actual results either limits delivery or lets cost drift.

## Output

1. **Summary**: the main bidding issues.
2. **Per campaign**: a table with strategy, target, actual CPA or ROAS, monthly conversions and the verdict.
3. **Goals and signals**: biddable goals, exclusions and seasonality adjustments, and any doubt about them.
4. **Proposals**: strategy or target changes with the reason, for a person to apply step by step.

## Rules

- Targets are in micros for CPA (divide by 1,000,000) and in ratios for ROAS (3.5 means 350%).
- Do not judge a strategy changed less than about two weeks ago: it is still learning.
- Simulations are Google's estimates: present them as such.
- Propose target changes in small steps, about 10 to 20% at a time.

## Examples

- "Is our bidding strategy right for each campaign?"
- "Faut-il passer la campagne Search en CPA cible ?"
- "Le ROAS cible est-il trop haut par rapport à ce qu'on obtient ?"
