---
name: google-ads-recommendations-triage
description: "Triages Google's own Google Ads recommendations through the Pilot MCP server: reads every open recommendation and its projected impact, sets each one against the account's goals and figures, and sorts them into worth applying, worth testing and to dismiss, with the optimization score for context. Use when the user asks which Google recommendations to accept, whether the optimization score matters, or to clean up the recommendations tab. Triggers: Google Ads recommendations, optimization score, which recommendations to apply. Déclencheurs : recommandations Google Ads, score d'optimisation, quelles recommandations appliquer, tri des recommandations."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Ads account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_get_optimization_score google_get_recommendations google_get_recommendation_impact google_list_campaigns google_get_campaign_performance"
  pilot-mcp-read-only: "true"
---

# Google Ads recommendations triage

Sort Google's recommendations by what they would really do for this account, so that a person applies the useful ones and dismisses the rest. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** none.
- **Read-only:** never apply or dismiss a recommendation. The triage is for a person to act on.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_get_optimization_score`: the account optimization score, between 0 and 1.
- `google_get_recommendations`: open recommendations by type (keyword, budget, bid, ad, and so on).
- `google_get_recommendation_impact`: the projected impact of one recommendation.
- `google_list_campaigns`: campaigns, to name those concerned.
- `google_get_campaign_performance`: cost, conversions and value per campaign.

## Steps

1. Call `google_list_accounts` and pick the account.
2. Call `google_get_optimization_score`. Present it as Google's measure of how many of its suggestions are applied, not as a measure of results.
3. Call `google_get_recommendations` with `limit: 1000`.
4. For each recommendation on a campaign that spends, call `google_get_recommendation_impact` with its `recommendation_resource_name`.
5. Call `google_list_campaigns` and `google_get_campaign_performance` over `LAST_30_DAYS` to set each recommendation against the campaign's actual results.
6. Sort:
   - **Apply**: fixes with no spend risk (missing assets, ad strength, broken settings) and budget raises on capped campaigns that convert below the target cost.
   - **Test**: bidding strategy changes, broad match, new keywords, through an experiment or on a small scope.
   - **Dismiss**: suggestions that raise spend without evidence of return, broaden targeting against the brief, or add automatically created assets the brand did not approve.

## Output

1. **Score**: the optimization score and what it means.
2. **Triage**: a table with recommendation type, campaign, projected impact, verdict (apply, test, dismiss) and the reason.
3. **First moves**: the three recommendations worth acting on first.

## Rules

- Keep Google's projections and your verdict apart: projections are Google's estimates.
- A recommendation to raise a budget is judged on the campaign's cost per conversion, not on the extra clicks.
- Never recommend auto-applying recommendations.
- Name the period of the figures used.

## Examples

- "Which Google Ads recommendations should we accept?"
- "Trie les recommandations Google : à appliquer, à tester, à rejeter."
- "Notre score d'optimisation est bas : est-ce grave ?"
