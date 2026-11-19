---
name: amazon-ads-sponsored-products-review
description: "Reviews Sponsored Products on Amazon through the Pilot MCP server: auto and manual campaigns, ad groups and default bids, advertised ASINs and SKUs, keywords with match type and bid, product and category targets, negative keywords, budget rules, and campaign spend, sales and ACOS. Use when the user asks how their Sponsored Products are built, which campaigns to scale or trim, or how keywords and targets are organized. Triggers: Sponsored Products review, Amazon keywords and targets, Amazon auto campaigns. Déclencheurs : Sponsored Products, mots-clés Amazon, campagnes automatiques Amazon, ciblage produit Amazon."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with an Amazon Ads profile attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "amazon_ads_list_profiles amazon_ads_sp_list_campaigns amazon_ads_sp_list_ad_groups amazon_ads_sp_list_product_ads amazon_ads_sp_list_keywords amazon_ads_sp_list_targets amazon_ads_sp_list_negative_keywords amazon_ads_sp_list_budget_rules amazon_ads_create_report amazon_ads_get_report"
  pilot-mcp-read-only: "true"
---

# Amazon Ads Sponsored Products review

Tell the user how their Sponsored Products are organized, which campaigns earn and which lose money, and how keywords, targets and negatives should change. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Amazon Ads profile.
- **Optional MCP servers:** a spreadsheet server to export the lists. None is needed.
- **Read-only:** never create, update or archive anything. Requesting a report changes nothing in the account.

## Tools

- `amazon_ads_list_profiles`: the profiles available.
- `amazon_ads_sp_list_campaigns`: campaigns with targeting type (auto or manual), state and budget.
- `amazon_ads_sp_list_ad_groups`: ad groups with default bid.
- `amazon_ads_sp_list_product_ads`: ASINs and SKUs per ad group.
- `amazon_ads_sp_list_keywords`: keywords with match type, bid and state.
- `amazon_ads_sp_list_targets`: product, category and auto targeting clauses with bid.
- `amazon_ads_sp_list_negative_keywords`: negatives per campaign or ad group.
- `amazon_ads_sp_list_budget_rules`: budget rules.
- `amazon_ads_create_report`: requests a Sponsored Products campaign report.
- `amazon_ads_get_report`: polls the report and returns its rows.

## Steps

1. Call `amazon_ads_list_profiles` and pick the profile.
2. Call `amazon_ads_create_report` with `ad_product: "SPONSORED_PRODUCTS"`, `report_type_id: "spCampaigns"`, the last 30 days, and columns `campaignId`, `campaignName`, `impressions`, `clicks`, `cost`, `purchases7d`, `sales7d`. Poll with `amazon_ads_get_report` and `download: true`.
3. Call `amazon_ads_sp_list_campaigns` (`state_filter` ENABLED) and join it to the report: ACOS and ROAS per campaign, split auto and manual.
4. For the campaigns that spend most, call `amazon_ads_sp_list_ad_groups`, `amazon_ads_sp_list_product_ads`, `amazon_ads_sp_list_keywords` and `amazon_ads_sp_list_targets` with `ad_group_id_filter`.
5. Read the structure: the same ASIN in many ad groups, ad groups mixing unrelated products, manual campaigns with only broad keywords, keywords with bids far from the ad group default, paused targets that still matter.
6. Call `amazon_ads_sp_list_negative_keywords`: auto campaigns without negatives, and exact keywords of manual campaigns not excluded from the auto campaign that found them.
7. Call `amazon_ads_sp_list_budget_rules` and check which campaigns they touch.
8. Per-keyword and search term results are not served by these tools: judge keywords on their setup and their campaign's results, and say so.

## Output

1. **Summary**: share of spend on profitable, break-even and losing campaigns.
2. **Campaigns**: a table with type, budget, spend, sales, ACOS.
3. **Structure issues**: duplicates, mixed ad groups, match type gaps.
4. **Negatives**: missing exclusions, per campaign.
5. **Next steps**: budgets, bids, keywords and negatives to change, for a person to apply.

## Rules

- ACOS with zero sales is not available, not zero.
- Never sum amounts of profiles in different currencies.
- Do not claim keyword-level results: they are not available here.

## Examples

- "Review our Sponsored Products campaigns."
- "Nos campagnes automatiques Amazon ont-elles des mots-clés négatifs ?"
- "Quelles campagnes Sponsored Products faut-il couper ou pousser ?"
