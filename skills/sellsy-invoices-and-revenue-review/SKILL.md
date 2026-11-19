---
name: sellsy-invoices-and-revenue-review
description: "Reviews invoicing and collected revenue in Sellsy in read-only mode through the Pilot MCP server: invoices issued, paid and overdue over a period with amounts excluding and including tax, payments recorded, quotes turned into invoices, and the customers who owe the most or bring the most revenue. Use when the user asks how much was invoiced or collected, who pays late, or how revenue evolves. Triggers: Sellsy invoices, collected revenue, overdue invoices. Déclencheurs : factures Sellsy, chiffre d'affaires encaissé, factures en retard, relances clients."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Sellsy account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "sellsy_get_account sellsy_search_invoices sellsy_get_invoice sellsy_list_payments sellsy_search_estimates sellsy_get_company"
  pilot-mcp-read-only: "true"
---

# Sellsy invoices and revenue review

Tell the user what was invoiced and collected, what is overdue and from whom, and how revenue evolves. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Sellsy account.
- **Optional MCP servers:** an accounting server to reconcile with the books. None is needed.
- **Read-only:** never create, send or change an invoice, and never contact a customer.

## Tools

- `sellsy_get_account`: checks the connection.
- `sellsy_search_invoices`: invoices by date, status, customer or currency.
- `sellsy_get_invoice`: one invoice with its lines, amounts, status and due date.
- `sellsy_list_payments`: payments recorded.
- `sellsy_search_estimates`: quotes by date and status.
- `sellsy_get_company`: one customer's details.

## Steps

1. Call `sellsy_get_account`.
2. Fix the period and the period before it.
3. Call `sellsy_search_invoices` with a `date` range: total invoiced (excluding and including tax), then with `status: ["paid"]` for collected revenue, and `status: ["late"]` for overdue invoices. Page with the returned `pagination.offset`.
4. Call `sellsy_list_payments` to see the payments recorded over the period.
5. Call `sellsy_search_estimates` for the period to compare quoted volume with invoiced volume.
6. Rank customers by revenue and by overdue amount; call `sellsy_get_company` for the top debtors and `sellsy_get_invoice` on their oldest overdue invoices (due date, days late).
7. Compare with the previous period.

## Output

1. **Headline**: invoiced, collected and overdue amounts, with the change.
2. **Overdue**: customers, invoices, amounts and days late, oldest first.
3. **Top customers**: revenue share of the largest customers.
4. **Quotes to invoices**: quoted against invoiced over the period.
5. **What to watch**: two or three points.

## Rules

- Keep amounts excluding and including tax apart, and say which one each figure is.
- Never sum amounts in different currencies.
- Read every page before counting.

## Examples

- "How much did we invoice and collect last month in Sellsy?"
- "Quels clients ont des factures en retard ?"
- "Compare le chiffre d'affaires encaissé de ce trimestre et du précédent."
