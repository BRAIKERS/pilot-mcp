---
name: google-ads-experiments-review
description: "Reviews Google Ads experiments through the Pilot MCP server: every A/B test with its type, dates and status, its control and treatment arms and their traffic split, and the results of each arm's campaign over the test period, then says whether a test has a readable winner, needs more time or should be stopped, for a person to decide. Use when the user asks how an experiment is going, whether to promote a test, or what tests ran. Triggers: Google Ads experiments, A/B test results, campaign experiment, should we promote the test. Déclencheurs : expériences Google Ads, résultats du test A/B, test de campagne, faut-il appliquer le test."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Ads account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_list_experiments google_list_experiment_arms google_list_campaigns google_get_campaign_performance"
  pilot-mcp-read-only: "true"
---

# Google Ads experiments review

Tell the user what each experiment tests, how its arms compare, and whether the result can be trusted yet. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a document server to keep a test log. None is needed.
- **Read-only:** never promote, end or change an experiment. The decision is for a person.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_list_experiments`: experiments with name, type, status and dates.
- `google_list_experiment_arms`: the control and treatment arms of one experiment, with their campaigns and traffic split.
- `google_list_campaigns`: campaign names and settings, to see what differs between arms.
- `google_get_campaign_performance`: daily cost, clicks, conversions and value of each arm's campaign.

## Steps

1. Call `google_list_accounts` and pick the account. Call `google_list_experiments`.
2. For each running or recently ended experiment, call `google_list_experiment_arms` with its `experiment_resource_name`: note the control campaign, the treatment campaign and the split.
3. Call `google_list_campaigns` to see what the treatment changes (bidding, budget, settings).
4. Call `google_get_campaign_performance` with `campaign_id` for each arm, from the experiment start date to its end or to yesterday.
5. Compare arms on the test's goal: conversions, CPA, ROAS or conversion value. Scale each arm by its traffic share before comparing volumes.
6. Judge readability: with fewer than about 30 conversions per arm, or less than two weeks of data, the test is not readable yet. A gap smaller than the day-to-day swing of the control is not a result.

## Output

1. **Experiments**: a table with name, what it tests, status, dates and split.
2. **Results**: per experiment, each arm's figures and the gap.
3. **Verdict**: winner readable, needs more time (with an estimate), or stop, and why.
4. **Next tests**: ideas the results suggest, for a person to decide.

## Rules

- Never call a winner on too few conversions; say how many are missing.
- The first days after a bidding change are learning: leave them out or say they are included.
- Name the dates and the currency.

## Examples

- "How is our Google Ads experiment going?"
- "Le test d'enchères a-t-il un gagnant ? Faut-il l'appliquer ?"
- "Quels tests ont tourné ce trimestre, et avec quel résultat ?"
