---
name: merchant-center-feed-audit
description: "Audits a Google Merchant Center account in read-only mode through the Pilot MCP server: account details and account-level issues, data sources (product feeds) with their type, schedule and last fetch, processed products and their approval by destination, and the share of products disapproved or limited, then returns a prioritized list of feed fixes. Use when Shopping or Performance Max campaigns under-deliver, after a feed change, or before a sales period. Triggers: Merchant Center audit, product feed audit, Google Shopping feed check. Déclencheurs : audit Merchant Center, audit du flux produits, flux Google Shopping, produits refusés."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Merchant Center account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "merchant_center_list_accounts merchant_center_get_account merchant_center_list_account_issues merchant_center_list_data_sources merchant_center_get_data_source merchant_center_list_products merchant_center_run_report"
  pilot-mcp-read-only: "true"
---

# Merchant Center feed audit

Tell the user whether their Merchant Center account and product feeds are healthy, and what to fix first. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Merchant Center account.
- **Optional MCP servers:** an e-commerce platform server to compare with the store's own catalog. None is needed.
- **Read-only:** never insert, update, fetch or delete anything. Fixes are for a person to apply.

## Tools

- `merchant_center_list_accounts`: the Merchant Center accounts available.
- `merchant_center_get_account`: one account's details.
- `merchant_center_list_account_issues`: account-level problems (policy, website, shipping, tax).
- `merchant_center_list_data_sources`: the product feeds and other data sources.
- `merchant_center_get_data_source`: one data source with its type, input and schedule.
- `merchant_center_list_products`: processed products with their status.
- `merchant_center_run_report`: a Merchant Query Language report, here product status counts.

## Steps

1. Call `merchant_center_list_accounts` and pick the account; pass it as `account` everywhere. Call `merchant_center_get_account`.
2. Call `merchant_center_list_account_issues`. An account suspension or a website verification issue blocks every product: put it at the top.
3. Call `merchant_center_list_data_sources`, then `merchant_center_get_data_source` on each: primary or supplemental, file, API or website input, fetch schedule, feed label and language. Flag primary feeds with no schedule, duplicated feeds for the same country and language, and supplemental feeds that nothing uses.
4. Call `merchant_center_run_report` with a query on `product_view` that returns the offer, its title and its aggregated status, and count products approved, limited, pending and disapproved per country. If a field is refused, list the products with `merchant_center_list_products` (page through with `page_token`) and count from their status.
5. Find the most frequent item-level issues from the same report or from a sample of products: missing GTIN, price or availability mismatch with the site, image problems, missing shipping.
6. Rank fixes by the number of products affected and by whether they block or only limit.

## Output

1. **Verdict**: healthy, degraded or blocked, in one sentence.
2. **Account issues**: each with its impact.
3. **Feeds**: a table with name, type, input, schedule, label and language.
4. **Products**: approved, limited, pending and disapproved, by country.
5. **Top issues**: issue, products affected, fix.

## Rules

- Quote Google's issue wording as given.
- A status Google did not return is unknown, not approved.
- Read every page of a list before counting.

## Examples

- "Audit our Merchant Center feed before Black Friday."
- "Pourquoi tant de produits sont refusés dans Merchant Center ?"
- "Fais le point sur les flux produits Google Shopping."
