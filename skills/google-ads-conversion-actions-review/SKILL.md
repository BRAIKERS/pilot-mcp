---
name: google-ads-conversion-actions-review
description: "Reviews the conversion setup of a Google Ads account through the Pilot MCP server: every conversion action with its status, category, counting, lookback windows, value and attribution, which goals are biddable for the account and per campaign, value rules, and the conversions each action actually records, then flags double counting, dead actions and goals that mislead automated bidding. Use when the user doubts Google Ads conversion figures or before changing a bidding target. Triggers: conversion actions review, Google Ads conversions setup, primary and secondary conversions, double counting. Déclencheurs : revue des actions de conversion, configuration des conversions Google Ads, conversions principales et secondaires, double comptage."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Ads account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_list_conversion_actions_detailed google_list_conversion_goals google_list_conversion_value_rules google_run_query google_get_campaign_performance google_list_campaigns"
  pilot-mcp-read-only: "true"
---

# Google Ads conversion actions review

Check that the Google Ads account counts the right conversions, once, with the right value, and that bidding optimizes toward the business goal. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** none. For a cross-platform check, the conversion-tracking-check skill compares Google Ads with Meta and GA4.
- **Read-only:** never create, change or remove a conversion action or goal. Fixes are for a person to apply.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_list_conversion_actions_detailed`: conversion actions with status, category, counting type, lookback windows, value settings and attribution model.
- `google_list_conversion_goals`: conversion goals (category and origin) and whether each is biddable.
- `google_list_conversion_value_rules`: rules that change conversion values.
- `google_run_query`: a read-only GAQL query, here for conversions per conversion action.
- `google_get_campaign_performance`: conversions and value per campaign.
- `google_list_campaigns`: campaigns, to check goal overrides.

## Steps

1. Call `google_list_accounts` and pick the account.
2. Call `google_list_conversion_actions_detailed`. For each action, note status, category, primary or secondary, counting (one or every), click and view lookback windows, default value and whether transaction values are used.
3. Call `google_run_query` with `SELECT segments.conversion_action_name, segments.conversion_action, metrics.all_conversions, metrics.all_conversions_value FROM campaign WHERE segments.date DURING LAST_30_DAYS`, summed per action to see what each action records.
4. Call `google_list_conversion_goals` for the account, then with `campaign_id` for the main campaigns from `google_list_campaigns`, to see which goals each campaign bids on.
5. Call `google_list_conversion_value_rules`.
6. Call `google_get_campaign_performance` over `LAST_30_DAYS` to compare the Conversions column with all conversions.
7. Flag: two primary actions for the same event (a tag and a GA4 import of the same purchase), purchases counted every time instead of once per click for leads, primary actions with no conversion in 30 days, micro-actions (page views, clicks) set as primary, value rules that inflate values, a campaign bidding on a goal unrelated to its purpose.

## Output

1. **Summary**: whether the Conversions column can be trusted.
2. **Actions**: a table with name, category, primary or secondary, counting, windows, value, conversions in 30 days and the verdict.
3. **Bidding goals**: biddable goals for the account and overrides per campaign.
4. **Issues and fixes**: ranked by effect on bidding, for a person to apply.

## Rules

- Primary actions feed the Conversions column and bidding; secondary ones only appear in all conversions.
- An action with no conversion may be new or broken: check its creation and tracking status before calling it dead.
- Never sum conversion values in different currencies.
- Name the period.

## Examples

- "Can we trust the conversions in Google Ads?"
- "Vérifie les actions de conversion : doublons, conversions mortes, mauvaises valeurs."
- "Sur quelles conversions la campagne Performance Max enchérit-elle ?"
