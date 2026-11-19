---
name: pinterest-ads-audience-review
description: "Reviews Pinterest Ads audiences and targeting through the Pilot MCP server: custom, visitor, engagement and actalike audiences with their state, customer lists, audience insights, which ad groups use which audiences, and how age, gender, location and interest segments perform. Use when the user asks who their Pinterest ads reach, which audiences work, or how to widen or narrow targeting. Triggers: Pinterest audiences, Pinterest targeting review, actalike audience. Déclencheurs : audiences Pinterest, ciblage Pinterest, qui voit mes publicités Pinterest, audience similaire Pinterest."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Pinterest ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "pinterest_ad_accounts_list pinterest_audiences_list pinterest_audiences_get pinterest_customer_lists_list pinterest_audience_insights_audiences pinterest_audience_insights_get pinterest_ad_groups_list pinterest_ad_groups_targeting_analytics pinterest_ad_accounts_targeting_analytics pinterest_ad_groups_audience_sizing"
  pilot-mcp-read-only: "true"
---

# Pinterest Ads audience review

Tell the user which Pinterest audiences exist, which ones are used, which segments convert, and where targeting is too narrow or too wide. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Pinterest ad account.
- **Optional MCP servers:** a CRM server to compare customer lists with the customer base. None is needed.
- **Read-only:** never create, update or delete an audience or a customer list. Suggestions are for a person to apply.

## Tools

- `pinterest_ad_accounts_list`: the ad accounts available, with their IDs.
- `pinterest_audiences_list`: the audiences of the account, with type and size.
- `pinterest_audiences_get`: one audience in detail, including its rule and status.
- `pinterest_customer_lists_list`: uploaded customer lists.
- `pinterest_audience_insights_audiences`: the audiences eligible for insights.
- `pinterest_audience_insights_get`: demographics and interests of the account's audience.
- `pinterest_ad_groups_list`: ad groups and their targeting, with `translate_interests_to_names: true`.
- `pinterest_ad_groups_targeting_analytics`: ad group metrics by targeting segment.
- `pinterest_ad_accounts_targeting_analytics`: account metrics by age, gender, location, interest, keyword or audience.
- `pinterest_ad_groups_audience_sizing`: the estimated reach of a targeting spec.

## Steps

1. Call `pinterest_ad_accounts_list` and pick the account.
2. Call `pinterest_audiences_list` and `pinterest_customer_lists_list`. For audiences that look small, empty or not ready, call `pinterest_audiences_get`.
3. Call `pinterest_ad_groups_list` with `translate_interests_to_names: true` to map each audience and interest to the ad groups that use it. Flag audiences used nowhere and ad groups with no audience or interest at all.
4. Call `pinterest_ad_accounts_targeting_analytics` over the last 30 days with `targeting_types` such as `["AGE_BUCKET","GENDER","GEO","AUDIENCE"]` and columns including spend, clicks and conversions.
5. For the ad groups that spend most, call `pinterest_ad_groups_targeting_analytics` to see which segments carry their results.
6. Call `pinterest_audience_insights_audiences`, then `pinterest_audience_insights_get`, to describe who the account reaches.
7. When a change of targeting is on the table, call `pinterest_ad_groups_audience_sizing` with the proposed spec to show its estimated reach.

## Output

1. **Summary**: who the ads reach and who converts, in three lines.
2. **Audiences**: a table with name, type, size, status and the ad groups that use it.
3. **Segments**: the best and weakest segments, with spend, results and cost per result.
4. **Gaps**: unused audiences, ad groups without audience, audiences too small to deliver.
5. **Suggestions**: targeting changes with their estimated reach, for a person to apply.

## Rules

- Audience sizes and reach are Pinterest estimates: present them as such.
- Do not judge a segment on a handful of conversions.
- Never sum amounts in different currencies.

## Examples

- "Which Pinterest audiences actually convert?"
- "Fais le point sur les audiences et le ciblage Pinterest."
- "Quelle tranche d'âge coûte le moins cher par achat sur Pinterest ?"
