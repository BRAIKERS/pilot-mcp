---
name: amazon-ads-account-audit
description: "Audits an Amazon Ads profile in read-only mode through the Pilot MCP server: Sponsored Products, Sponsored Brands and Sponsored Display campaigns with state and budget, ad groups, product ads, negatives, budget rules, and the spend, sales and ACOS of the last 30 days from Amazon reports, then returns a prioritized findings report. Use when the user asks to audit or health-check an Amazon Ads account or marketplace. Triggers: Amazon Ads audit, Amazon PPC review, Amazon advertising health check. Déclencheurs : audit Amazon Ads, audit PPC Amazon, revue du compte Amazon Ads, bilan de santé Amazon."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with an Amazon Ads profile attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "amazon_ads_list_profiles amazon_ads_get_profile amazon_ads_sp_list_campaigns amazon_ads_sb_list_campaigns amazon_ads_sd_list_campaigns amazon_ads_sp_list_ad_groups amazon_ads_sp_list_product_ads amazon_ads_sp_list_negative_keywords amazon_ads_sp_list_budget_rules amazon_ads_create_report amazon_ads_get_report pilot_data_freshness"
  pilot-mcp-read-only: "true"
---

# Amazon Ads account audit

Audit one Amazon Ads profile (one marketplace) and hand back a short, prioritized list of findings, each backed by figures. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Amazon Ads profile.
- **Optional MCP servers:** a file storage server to save the report. None is needed.
- **Read-only:** never create, update, pause or archive anything. `amazon_ads_create_report` only requests a report and changes nothing in the account.

## Tools

- `amazon_ads_list_profiles`: the profiles available, one per marketplace and account.
- `amazon_ads_get_profile`: one profile with its marketplace, currency and time zone.
- `amazon_ads_sp_list_campaigns`: Sponsored Products campaigns with state, targeting type and budget.
- `amazon_ads_sb_list_campaigns`: Sponsored Brands campaigns.
- `amazon_ads_sd_list_campaigns`: Sponsored Display campaigns.
- `amazon_ads_sp_list_ad_groups`: Sponsored Products ad groups with default bid.
- `amazon_ads_sp_list_product_ads`: the ASINs or SKUs advertised.
- `amazon_ads_sp_list_negative_keywords`: Sponsored Products negatives.
- `amazon_ads_sp_list_budget_rules`: budget rules (seasonal or performance increases).
- `amazon_ads_create_report`: requests a campaign report for a period (asynchronous).
- `amazon_ads_get_report`: polls a report and returns its rows when ready.
- `pilot_data_freshness`: whether Pilot's collected Amazon Ads data is current.

## Steps

1. Call `amazon_ads_list_profiles` and pick the profile (ask if several marketplaces). Pass it as `account_id` when the workspace has several. Call `amazon_ads_get_profile` for the currency.
2. Call `pilot_data_freshness` with `platform: "amazon_ads"`.
3. Request one report per program over the last 30 days with `amazon_ads_create_report`: `ad_product: "SPONSORED_PRODUCTS"` with `report_type_id: "spCampaigns"`, and the same for `SPONSORED_BRANDS` / `sbCampaigns` and `SPONSORED_DISPLAY` / `sdCampaigns`. Columns such as `campaignId`, `campaignName`, `impressions`, `clicks`, `cost`, and the sales and purchases columns of each program (for Sponsored Products, `sales7d` and `purchases7d`).
4. Poll each with `amazon_ads_get_report` and `download: true` until it is COMPLETED. Reports can take minutes: tell the user and keep polling.
5. Read the structure: `amazon_ads_sp_list_campaigns`, `amazon_ads_sb_list_campaigns`, `amazon_ads_sd_list_campaigns`. Flag enabled campaigns out of budget, archived campaigns that still matter, programs not used at all.
6. For the Sponsored Products campaigns that spend most: `amazon_ads_sp_list_ad_groups`, `amazon_ads_sp_list_product_ads` (ASINs advertised in many campaigns at once), `amazon_ads_sp_list_negative_keywords` (auto campaigns with no negatives).
7. Call `amazon_ads_sp_list_budget_rules`: rules that raise budgets on campaigns that lose money.
8. Compute ACOS (cost divided by sales) and ROAS per campaign where sales are above zero, then write the report.

## Output

1. **Summary**: three lines on the profile's state and the top three priorities.
2. **Findings**: a table with the finding, the evidence (figure, period, currency), the stake and the suggested action.
3. **Programs**: Sponsored Products, Brands and Display, spend, sales and ACOS each.
4. **Next steps**: actions in order, for a person to apply in Amazon Ads.

## Rules

- Each profile has its own currency: never sum two marketplaces in different currencies.
- ACOS with zero sales is not available, not zero.
- Say which attribution window the sales columns use.

## Examples

- "Audit our Amazon Ads account on amazon.fr."
- "Fais l'audit PPC Amazon, en commençant par les campagnes à ACOS élevé."
- "Où part le budget Amazon Ads sans ventes ?"
