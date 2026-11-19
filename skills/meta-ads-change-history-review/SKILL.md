---
name: meta-ads-change-history-review
description: "Reviews the change history of a Meta ad account through the Pilot MCP server: who changed what and when (budgets, bids, status, targeting, ads, audiences), by category and by person, then sets each significant change against the daily results to show which ones moved performance. Use when the user asks who changed what on Meta, what happened on a given day, or whether a change explains a drop. Triggers: Meta change history, who changed what Facebook ads, activity log Meta. Déclencheurs : historique des modifications Meta, qui a changé quoi sur Facebook Ads, journal d'activité Meta."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Meta ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "meta_list_accounts meta_get_ad_account_activities meta_get_activities meta_get_account_insights meta_list_campaigns meta_list_adsets"
  pilot-mcp-read-only: "true"
---

# Meta Ads change history review

List the changes made to a Meta ad account and tie them to what happened to results afterwards. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a document server to keep a change log. None is needed.
- **Read-only:** never undo or redo a change. Rollbacks are for a person.

## Tools

- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_get_ad_account_activities`: activity log with event type, time, object, actor and extra data, filtered by window and category.
- `meta_get_activities`: the raw activity events, page by page, for long windows.
- `meta_get_account_insights`: results per day with `time_increment: 1`.
- `meta_list_campaigns`: campaign names and status.
- `meta_list_adsets`: ad set names, budgets and status.

## Steps

1. Call `meta_list_accounts` and pick the account.
2. Call `meta_get_ad_account_activities` with `since` and `until` set to the window asked, `limit: 100` and `pages` high enough to cover it. Use `category` (`BUDGET`, `BID`, `STATUS`, `TARGETING`, `AD`) when the user asks about one kind of change.
3. If the log is long, call `meta_get_activities` with `pages` to fetch the rest.
4. Group the changes by day, by person and by category. Use `meta_list_campaigns` and `meta_list_adsets` to put names on objects when the log gives only IDs.
5. Call `meta_get_account_insights` with `level: "adset"`, `time_increment: 1` and `time_range` set to the window, and compare each changed object's daily results before and after.
6. A large budget change or a targeting edit sends an ad set back to learning: look for a dip in the days after it.

## Output

1. **Summary**: how many changes, by whom, and the few that matter.
2. **Timeline**: date, person, object, category, what changed (old and new value when given).
3. **Effects**: results before and after each significant change, and whether it plausibly explains them.
4. **To review**: changes that look unintended or harmful.

## Rules

- A change followed by a move in results is a lead, not a proof.
- Changes made by automated rules or by Meta appear with their actor: name it as given.
- Name the dates and the account time zone.

## Examples

- "Who changed what on our Meta account this week?"
- "Qu'est-ce qui a été modifié sur Meta le jour où le coût par achat a doublé ?"
- "Un changement de budget a-t-il relancé l'apprentissage ?"
