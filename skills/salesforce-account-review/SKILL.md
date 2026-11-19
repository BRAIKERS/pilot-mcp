---
name: salesforce-account-review
description: "Prepares a view of one customer account from Salesforce through the Pilot MCP server: company details, open and closed opportunities with amounts and stages, recent activities and open tasks, and support cases with their status and priority. Use before a client meeting, a renewal or an escalation, or when an account manager takes over an account. Triggers: Salesforce account review, customer account summary, prepare client meeting Salesforce. Déclencheurs : fiche client Salesforce, synthèse d'un compte client, préparer un rendez-vous client, historique d'un compte."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Salesforce org attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "salesforce_search salesforce_list_accounts salesforce_get_account salesforce_list_opportunities salesforce_list_tasks salesforce_list_cases"
  pilot-mcp-read-only: "true"
---

# Salesforce account review

Give the user a one-page view of one customer account: who they are, what they bought or may buy, what was done lately, and what is open. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Salesforce org.
- **Optional MCP servers:** a calendar or email server to add recent meetings and exchanges. None is needed.
- **Read-only:** never change a record.

## Tools

- `salesforce_search`: finds the account by name across objects.
- `salesforce_list_accounts`: lists accounts, when the name is uncertain.
- `salesforce_get_account`: the account with all its fields.
- `salesforce_list_opportunities`: the account's opportunities, open and closed.
- `salesforce_list_tasks`: activities and tasks logged against the account.
- `salesforce_list_cases`: support cases of the account.

## Steps

1. Call `salesforce_search` with the account name (objects Account), or `salesforce_list_accounts` if needed. Confirm the right account with the user when several match.
2. Call `salesforce_get_account`: industry, size, owner, address, type, any custom fields that matter.
3. Call `salesforce_list_opportunities` with `account_id`: open deals with stage, amount and close date; won and lost deals with dates and amounts.
4. Call `salesforce_list_tasks` with `what_id` set to the account: the last activities and the open tasks with their due dates.
5. Call `salesforce_list_cases` with `account_id`: open cases, priority, age; recent closed cases.
6. Read the signals: renewal or close date coming, open high-priority cases, no activity for a long time, a lost deal and why.

## Output

1. **Account**: identity, owner, relationship length.
2. **Business**: won revenue, open pipeline, next close dates.
3. **Recent activity**: last exchanges and open tasks.
4. **Support**: open cases and their priority.
5. **Talking points**: three points to raise, and risks.

## Rules

- Never sum amounts in different currencies.
- Report personal data only as far as the meeting needs it.
- A field the org does not fill is absent.

## Examples

- "Prepare a summary of the Acme account before tomorrow's meeting."
- "Fais la fiche du client Dupont SA depuis Salesforce."
- "Qu'est-ce qui est ouvert chez ce client : affaires, tâches, tickets ?"
