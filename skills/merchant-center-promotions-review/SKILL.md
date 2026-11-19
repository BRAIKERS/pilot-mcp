---
name: merchant-center-promotions-review
description: "Reviews Google Merchant Center promotions in read-only mode through the Pilot MCP server: active, scheduled and expired promotions with their title, offer type, discount, minimum purchase, dates of validity and display, channel and approval status, and the data sources that carry them. Use before a sales period, when a promotion does not show on Google, or to list what is live. Triggers: Merchant Center promotions, Google Shopping promotions, promotion not showing. Déclencheurs : promotions Merchant Center, promotions Google Shopping, promotion qui ne s'affiche pas."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Merchant Center account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "merchant_center_list_accounts merchant_center_list_promotions merchant_center_get_promotion merchant_center_list_data_sources merchant_center_list_account_issues"
  pilot-mcp-read-only: "true"
---

# Merchant Center promotions review

Tell the user which Merchant Center promotions are live, scheduled or blocked, and what to fix. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Merchant Center account.
- **Optional MCP servers:** a calendar server to check the promotion calendar. None is needed.
- **Read-only:** never create or change a promotion.

## Tools

- `merchant_center_list_accounts`: the accounts available.
- `merchant_center_list_promotions`: the promotions of the account.
- `merchant_center_get_promotion`: one promotion in full, with its status.
- `merchant_center_list_data_sources`: the data sources, including promotion sources.
- `merchant_center_list_account_issues`: account issues that can block promotions.

## Steps

1. Call `merchant_center_list_accounts` and pick the account.
2. Call `merchant_center_list_account_issues`: promotions need an account in good standing.
3. Call `merchant_center_list_promotions` (every page), then `merchant_center_get_promotion` on each one that is live or scheduled.
4. For each: title, offer type (generic or with code), percent or amount off, minimum purchase, products concerned, validity period, display period, channel (online or in store), country, and approval status with any disapproval reason.
5. Classify: live and approved, scheduled, pending review, disapproved, expired.
6. Flag: a disapproved promotion with its reason, a display period that starts after the validity period, a promotion about to expire, overlapping promotions on the same products, and promotions that go live before Google has time to review them.
7. Call `merchant_center_list_data_sources` to say where promotions come from (feed or API).

## Output

1. **Summary**: live, scheduled, pending and disapproved counts.
2. **Promotions**: a table with title, offer, dates, channel, country and status.
3. **Issues**: disapprovals with Google's reason, date problems, overlaps.
4. **Next steps**: for a person to apply.

## Rules

- Quote Google's disapproval reason as given.
- Dates are given with a time zone: name it.
- A status Google did not return is unknown, not approved.

## Examples

- "Which Merchant Center promotions are live right now?"
- "Pourquoi notre promotion de Noël ne s'affiche pas sur Google ?"
- "Liste les promotions prévues pour le Black Friday et leur état."
