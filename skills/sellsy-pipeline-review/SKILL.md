---
name: sellsy-pipeline-review
description: "Reviews the Sellsy sales pipeline in read-only mode through the Pilot MCP server: pipelines and their steps, open opportunities by step with count and amount, deals due this month, won and lost deals over the period, quotes sent and their conversion, overdue deals and open CRM tasks. Use when the user asks how much is in the Sellsy pipeline, what will sign, or which deals are stuck. Triggers: Sellsy pipeline, Sellsy opportunities, deals to sign this month. Déclencheurs : pipeline Sellsy, opportunités Sellsy, affaires à signer ce mois-ci, devis Sellsy."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Sellsy account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "sellsy_get_account sellsy_list_pipelines sellsy_search_opportunities sellsy_get_opportunity sellsy_search_estimates sellsy_list_tasks"
  pilot-mcp-read-only: "true"
---

# Sellsy pipeline review

Tell the user how much is in the Sellsy pipeline, what should sign and when, and which deals need attention. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Sellsy account.
- **Optional MCP servers:** a team messaging server to send the review. None is needed.
- **Read-only:** never create, update or delete anything.

## Tools

- `sellsy_get_account`: checks the connection and names the Sellsy account.
- `sellsy_list_pipelines`: the pipelines and their steps.
- `sellsy_search_opportunities`: opportunities filtered by pipeline, step, status, creation or due date.
- `sellsy_get_opportunity`: one opportunity in full.
- `sellsy_search_estimates`: quotes by date and status.
- `sellsy_list_tasks`: CRM tasks.

## Steps

1. Call `sellsy_get_account` to check the connection.
2. Call `sellsy_list_pipelines` to name pipelines and steps in their order.
3. Call `sellsy_search_opportunities` for open deals per pipeline and step; page with `offset`, sending back the `pagination.offset` of the previous call as is. Sum count and amount per step.
4. Call it with a `due_date` range for this month and this quarter (deals expected to sign), and with `statuses` won and lost over the last period to compute the win rate.
5. Call `sellsy_search_estimates` over the same period: quotes sent and their status, to measure quote-to-order conversion.
6. Flag open deals past their due date, deals with no amount, and large deals stuck in an early step. For those, call `sellsy_get_opportunity`.
7. Call `sellsy_list_tasks` to show open tasks linked to the flagged deals.

## Output

1. **Summary**: open pipeline amount, expected this month and quarter, win rate.
2. **By step**: count and amount per pipeline step.
3. **Signing soon**: deals with company, amount, step and due date.
4. **Needs attention**: overdue, stalled or incomplete deals.
5. **Quotes**: sent, accepted, refused, pending.

## Rules

- Never sum amounts in different currencies.
- Read every page before counting.
- A field Sellsy did not return is absent, not zero.

## Examples

- "How much is in our Sellsy pipeline?"
- "Quelles affaires Sellsy doivent signer ce mois-ci ?"
- "Quel est notre taux de transformation des devis Sellsy ce trimestre ?"
