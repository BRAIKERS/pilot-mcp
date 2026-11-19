---
name: google-ads-change-history-review
description: "Reviews the change history of a Google Ads account through the Pilot MCP server: what was changed in the last 30 days, by whom and when (campaigns, budgets, ad groups, ads, keywords, assets), then sets each change against the daily results to show which ones moved performance. Use when the user asks who changed what, what happened on a given day, or whether a change explains a jump or a drop. Triggers: change history, who changed what, recent changes Google Ads, did a change break something. Déclencheurs : historique des modifications, qui a changé quoi, changements récents Google Ads, une modification a-t-elle cassé quelque chose."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Ads account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_get_change_history google_get_campaign_performance google_get_account_summary google_list_campaigns google_list_ads"
  pilot-mcp-read-only: "true"
---

# Google Ads change history review

List the changes made to a Google Ads account and tie them to what happened to results afterwards. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a document server to keep a change log. None is needed.
- **Read-only:** never undo or redo a change. Rollbacks are for a person to apply.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_get_change_history`: change events with what was modified, when and by whom, within the last 30 days.
- `google_get_campaign_performance`: daily cost and conversions per campaign.
- `google_get_account_summary`: account totals for a period.
- `google_list_campaigns`: campaign names and status.
- `google_list_ads`: ad names, headlines and status, to name changed ads.

## Steps

1. Call `google_list_accounts` and pick the account.
2. Call `google_get_change_history` with `limit: 1000` and `change_date_range` set to the window asked (the last 30 days at most). Filter with `resource_type_filter` when the user asks about one kind of object (for example `CAMPAIGN_BUDGET`).
3. Group the changes by day, by person and by kind: budgets, bids and strategies, status, keywords, ads and assets, targeting.
4. Call `google_list_campaigns` and `google_list_ads` to put names on the IDs.
5. Call `google_get_campaign_performance` over the same window and, for each significant change, compare the campaign's daily cost and conversions in the days before and after.
6. Call `google_get_account_summary` for the window as a whole, to see the weight of each campaign.

## Output

1. **Summary**: how many changes, by whom, and the few that matter.
2. **Timeline**: a table with date, person, object, what changed (old and new value when given).
3. **Effects**: for each significant change, results before and after, and whether the change plausibly explains them.
4. **To review**: changes that look unintended or harmful, for a person to check.

## Rules

- Change history covers the last 30 days only: say so when the question goes further back.
- A change followed by a move in results is a lead, not a proof: say what else could explain it.
- Automated changes made by Google or by rules appear too: name the actor as given.
- Name the dates and the account time zone.

## Examples

- "Who changed what on the Google Ads account last week?"
- "Qu'est-ce qui a été modifié le jour où les conversions ont chuté ?"
- "Un changement de budget explique-t-il la hausse du CPA ?"
