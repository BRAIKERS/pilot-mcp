---
name: search-ads-360-conversion-actions-check
description: "Checks Search Ads 360 conversion actions through the Pilot MCP server: the Floodlight and other conversion actions of an account with their status, category and counting, and the conversions and value each one recorded by campaign over the period, then flags silent, duplicated or misused actions. Use when SA360 conversions look wrong or before changing bidding goals. Triggers: SA360 conversion actions, Search Ads 360 Floodlight, SA360 tracking check. Déclencheurs : actions de conversion SA360, Floodlight Search Ads 360, suivi des conversions SA360."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Search Ads 360 account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "search_ads_360_list_customers search_ads_360_list_conversion_actions search_ads_360_get_search_fields search_ads_360_validate_operation search_ads_360_search_report"
  pilot-mcp-read-only: "true"
---

# Search Ads 360 conversion actions check

Tell the user which SA360 conversion actions count what, which ones recorded conversions, and what looks wrong. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Search Ads 360 account.
- **Optional MCP servers:** a tag manager server to check the tags on the site. None is needed.
- **Read-only:** nothing is changed; the SA360 Reporting API is read-only.

## Tools

- `search_ads_360_list_customers`: the SA360 accounts reachable.
- `search_ads_360_list_conversion_actions`: conversion actions with their settings.
- `search_ads_360_get_search_fields`: which conversion fields and segments exist.
- `search_ads_360_validate_operation`: checks a query before it is run.
- `search_ads_360_search_report`: a free-form SA360 query, here conversions per action and per campaign.

## Steps

1. Call `search_ads_360_list_customers` and pick the account; pass `customer_id` without dashes, and `login_customer_id` when access goes through a manager.
2. Call `search_ads_360_list_conversion_actions`: name, type, status, category, counting and whether each action is included in the conversions column used for bidding.
3. Check with `search_ads_360_get_search_fields` which segment carries the conversion action in metric queries, then build a query that returns conversions and conversion value per conversion action and per campaign over `LAST_30_DAYS`.
4. Validate it with `search_ads_360_validate_operation` (`kind: "query"`), then run `search_ads_360_search_report` and read every page.
5. Flag: enabled actions with no conversion in 30 days; actions that count every conversion where one per click is intended; two actions that seem to count the same event; purchase actions with no value; secondary actions included in the bidding column.

## Output

1. **Verdict**: tracking reliable, partial or broken, in one sentence.
2. **Actions**: a table with name, type, status, counting, in the bidding column or not, conversions and value.
3. **Issues**: with the evidence.
4. **Fixes**: in order, for the person who manages tracking.
5. **Query used**: the SA360 query, so the check can be rerun.

## Rules

- No conversion is not proof of a broken tag: low volume can explain it.
- Name the period and the currency of conversion values.
- A field SA360 did not return is absent, not zero.

## Examples

- "Which SA360 conversion actions feed our bidding?"
- "Vérifie les actions de conversion Search Ads 360 du compte."
- "Pourquoi les conversions SA360 ont doublé depuis le 3 ?"
