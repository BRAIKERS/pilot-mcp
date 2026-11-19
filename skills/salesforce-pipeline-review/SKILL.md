---
name: salesforce-pipeline-review
description: "Reviews the Salesforce sales pipeline in read-only mode through the Pilot MCP server: open opportunities by stage with count and amount, deals closing this month or quarter, won and lost deals over the period, deals past their close date or with no recent activity, and the biggest deals in play. Use when the user asks how much revenue is in play, what will close, or which deals are stuck. Triggers: Salesforce pipeline review, deals closing this quarter, stuck opportunities. Déclencheurs : revue du pipeline Salesforce, affaires qui signent ce trimestre, opportunités bloquées, prévisions de ventes."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Salesforce org attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "salesforce_get_org_limits salesforce_pipeline_summary salesforce_list_opportunities salesforce_get_opportunity salesforce_list_tasks salesforce_describe_object"
  pilot-mcp-read-only: "true"
---

# Salesforce pipeline review

Tell the user how much is in the pipeline, what should close and when, and which deals need attention. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Salesforce org.
- **Optional MCP servers:** a team messaging server to send the review to sales. None is needed.
- **Read-only:** never create, update or delete a record.

## Tools

- `salesforce_get_org_limits`: API calls left in the org; check before large lists.
- `salesforce_pipeline_summary`: opportunities by stage, with count and total amount.
- `salesforce_list_opportunities`: opportunities filtered by stage, open or closed, won, close date or account.
- `salesforce_get_opportunity`: one opportunity with all its fields.
- `salesforce_list_tasks`: activities logged against a record.
- `salesforce_describe_object`: the stages and fields the org uses on Opportunity.

## Steps

1. Call `salesforce_get_org_limits`; if the daily API budget is low, keep lists short and say so.
2. Call `salesforce_describe_object` with `object_type: "Opportunity"` to learn the stage values, their order and the amount and currency fields.
3. Call `salesforce_pipeline_summary` for open deals, and with `close_date_from` and `close_date_to` set to this month and this quarter.
4. Call `salesforce_list_opportunities` with `is_closed: false` and a close date window to list deals expected to close; and with `is_closed: true` over the last period to count won and lost deals and compute the win rate.
5. Flag deals whose close date has passed while still open, deals that have not moved stage for a long time, and large deals with a low probability.
6. For the biggest or most at-risk deals, call `salesforce_get_opportunity` and `salesforce_list_tasks` with `what_id` set to the opportunity: last activity, next step.

## Output

1. **Summary**: open pipeline amount, expected to close this month and quarter, win rate over the period.
2. **By stage**: count and amount per stage.
3. **Closing soon**: deals with owner, amount, stage, close date.
4. **Needs attention**: overdue, stalled or under-qualified deals, with the reason.
5. **Next steps**: for the sales team.

## Rules

- Never sum amounts in different currencies; multi-currency orgs are reported per currency.
- Probability-weighted figures are Salesforce's own: say so.
- A field the org does not fill is absent, not zero.

## Examples

- "How much is in our Salesforce pipeline this quarter?"
- "Quelles affaires Salesforce sont en retard sur leur date de signature ?"
- "Fais la revue du pipeline commercial avant la réunion de lundi."
