---
name: ga4-custom-definitions-review
description: "Reviews Google Analytics 4 custom dimensions and metrics in read-only mode through the Pilot MCP server: each custom definition with its parameter, scope and unit, whether it still receives values, definitions that duplicate each other or hold personal data, the room left per scope, and the definitions the team would need for its reports, then lists the changes for a person to make in GA4 Admin. Use when the user asks which custom dimensions exist, why a custom dimension shows not set, or what to register for a report. Triggers: GA4 custom dimensions, GA4 custom metrics, custom definitions review. Déclencheurs : dimensions personnalisées GA4, métriques personnalisées GA4, revue des définitions personnalisées."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Analytics 4 property attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_analytics_get_account_summaries google_analytics_list_custom_dimensions google_analytics_list_custom_metrics google_analytics_get_custom_dimensions_and_metrics google_analytics_get_custom_dimension google_analytics_get_custom_metric google_analytics_run_report"
  pilot-mcp-read-only: "true"
---

# GA4 custom definitions review

Check that the custom dimensions and metrics of a GA4 property are filled, useful and safe, and list what to add or fix. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the property.
- **Optional MCP servers:** none.
- **Read-only:** never create, change or archive a definition. The Pilot MCP server does not write to GA4: every change is for a person to make in GA4 Admin.

## Tools

- `google_analytics_get_account_summaries`: GA4 properties reachable.
- `google_analytics_list_custom_dimensions`: custom dimensions with parameter, scope and description.
- `google_analytics_list_custom_metrics`: custom metrics with parameter, scope and unit.
- `google_analytics_get_custom_dimensions_and_metrics`: the custom fields as the reporting API names them.
- `google_analytics_get_custom_dimension`: one dimension in detail.
- `google_analytics_get_custom_metric`: one metric in detail.
- `google_analytics_run_report`: whether each custom field receives values.

## Steps

1. Call `google_analytics_get_account_summaries` and pick the property.
2. Call `google_analytics_list_custom_dimensions` and `google_analytics_list_custom_metrics`, and `google_analytics_get_custom_dimensions_and_metrics` for their API names (for example `customEvent:content_type`).
3. For each custom dimension, call `google_analytics_run_report` over the last 28 days with that API name as dimension and `metrics: ["eventCount"]`. A dimension that returns only `(not set)` no longer receives its parameter, or never did.
4. For each custom metric, run a report with the metric over the same period: a total of zero means the parameter is not sent.
5. Use `google_analytics_get_custom_dimension` or `google_analytics_get_custom_metric` to check the scope and description of doubtful ones.
6. Flag: duplicates (two definitions for the same idea), wrong scope (a user property registered at event scope), parameters that look like personal data (email, phone, name), and how many slots remain per scope.
7. Ask what the team wants to report on, and propose the definitions it needs, with parameter, scope and unit.

## Output

1. **Definitions**: a table with display name, parameter, scope, unit, values in 28 days and verdict.
2. **Issues**: empty, duplicated, wrongly scoped or risky definitions.
3. **Proposed**: definitions to add, with their reason.
4. **Changes to make**: for a person to apply in GA4 Admin, with a note for the developer when a parameter must be sent first.

## Rules

- A parameter name cannot change after registration: check its spelling with the developer before anyone registers it.
- A new definition fills from creation onward; past events are not reprocessed.
- Never recommend registering personal data.

## Examples

- "Which GA4 custom dimensions do we have, and do they work?"
- "Pourquoi la dimension personnalisée « type de client » affiche-t-elle (not set) ?"
- "Quelles définitions personnalisées faut-il créer pour le rapport par offre ?"
