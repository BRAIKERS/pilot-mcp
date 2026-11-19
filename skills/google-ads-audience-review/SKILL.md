---
name: google-ads-audience-review
description: "Reviews Google Ads audiences through the Pilot MCP server: remarketing and customer match lists and their size and state, custom audiences, the audiences targeted, observed or excluded in each ad group, and how each audience performs, then proposes audiences to add, exclude or adjust for a person to apply. Use when the user asks about remarketing, audiences, customer lists or observation audiences in Google Ads. Triggers: Google Ads audiences, remarketing lists, customer match, audience performance. Déclencheurs : audiences Google Ads, listes de remarketing, ciblage par liste de clients, performance des audiences."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Ads account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_list_user_lists google_list_audiences google_list_ad_groups google_list_ad_group_audience_criteria google_run_query google_list_remarketing_actions"
  pilot-mcp-read-only: "true"
---

# Google Ads audience review

Show which audiences exist, where they are used, how they perform, and what to change. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** none.
- **Read-only:** never add, remove or change an audience or a list. Proposals are for a person to apply.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_list_user_lists`: remarketing, customer match and rule-based lists.
- `google_list_audiences`: the account's audience resources.
- `google_list_ad_groups`: ad groups per campaign.
- `google_list_ad_group_audience_criteria`: audiences targeted or excluded in one ad group.
- `google_run_query`: a read-only GAQL query, here for results per audience.
- `google_list_remarketing_actions`: the remarketing tags that feed lists.

## Steps

1. Call `google_list_accounts` and pick the account.
2. Call `google_list_user_lists` and note each list's type, size and state. A list too small to serve, or no longer filling, is a finding.
3. Call `google_list_audiences` and `google_list_remarketing_actions`: a remarketing list with no tag behind it stops growing.
4. Call `google_list_ad_groups` for active ad groups, then `google_list_ad_group_audience_criteria` for those that spend most.
5. Call `google_run_query` with `SELECT campaign.name, ad_group.name, ad_group_criterion.display_name, ad_group_criterion.negative, metrics.cost_micros, metrics.conversions, metrics.conversions_value FROM ad_group_audience_view WHERE segments.date DURING LAST_30_DAYS`.
6. Compare each audience's CPA or ROAS with its ad group: an observed audience that converts much better is a bid adjustment candidate; recent customers not excluded from acquisition campaigns is a waste candidate.

## Output

1. **Lists**: a table with name, type, size, state, and whether it still fills.
2. **Usage**: audiences per ad group, targeted, observed or excluded.
3. **Performance**: audiences that convert better or worse than their ad group.
4. **Proposals**: audiences to add, exclude or adjust, for a person to apply.

## Rules

- Google needs a minimum list size to serve; a small list is reported as too small, not as failing.
- Customer match lists hold personal data: never ask for or display their members.
- Google amounts are in micros: divide by 1,000,000.
- Do not judge an audience on a few conversions.

## Examples

- "Which audiences do we use in Google Ads, and do they work?"
- "Nos listes de remarketing se remplissent-elles encore ?"
- "Faut-il exclure les clients récents des campagnes d'acquisition ?"
