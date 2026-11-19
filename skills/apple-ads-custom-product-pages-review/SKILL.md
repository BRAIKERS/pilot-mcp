---
name: apple-ads-custom-product-pages-review
description: "Reviews the custom product pages used in Apple Ads through the Pilot MCP server: the app's App Store page and locales, its custom product pages and their localized content, the ads that use each page, their results against the default product page, and any rejection reason. Use when the user wants to know whether custom product pages beat the default page, or why an Apple Ads ad does not serve. Triggers: Apple Ads custom product pages, CPP performance, Apple ad rejected. Déclencheurs : pages produit personnalisées Apple Ads, CPP Apple, publicité Apple refusée, page produit App Store."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with an Apple Ads organization attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "apple_ads_apple_ads_list_campaigns apple_ads_apple_ads_get_app apple_ads_apple_ads_get_app_locale_details apple_ads_apple_ads_list_cpp apple_ads_apple_ads_get_cpp apple_ads_apple_ads_get_cpp_locale_details apple_ads_apple_ads_find_ads_org_wide apple_ads_apple_ads_report_ads_in_campaign apple_ads_apple_ads_find_product_page_reasons apple_ads_apple_ads_get_product_page_reason"
  pilot-mcp-read-only: "true"
---

# Apple Ads custom product pages review

Tell the user which product pages their Apple Ads use, how custom pages compare with the default page, and what blocks any of them. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Apple Ads organization.
- **Optional MCP servers:** none is needed.
- **Read-only:** never create, update or delete an ad, a creative or a page.

## Tools

- `apple_ads_apple_ads_list_campaigns`: campaigns with their app.
- `apple_ads_apple_ads_get_app`: the app's App Store details.
- `apple_ads_apple_ads_get_app_locale_details`: the app's localized App Store information.
- `apple_ads_apple_ads_list_cpp`: the custom product pages of the app.
- `apple_ads_apple_ads_get_cpp`: one custom product page.
- `apple_ads_apple_ads_get_cpp_locale_details`: the localized content of a custom page.
- `apple_ads_apple_ads_find_ads_org_wide`: ads across all campaigns, with the page each one uses.
- `apple_ads_apple_ads_report_ads_in_campaign`: results per ad of a campaign.
- `apple_ads_apple_ads_find_product_page_reasons`: rejection reasons of custom pages.
- `apple_ads_apple_ads_get_product_page_reason`: one rejection reason in detail.

## Steps

1. Call `apple_ads_apple_ads_list_campaigns` to find the apps (adamId) and campaigns.
2. Call `apple_ads_apple_ads_get_app` and `apple_ads_apple_ads_get_app_locale_details` to describe the default product page per locale.
3. Call `apple_ads_apple_ads_list_cpp`, then `apple_ads_apple_ads_get_cpp` and `apple_ads_apple_ads_get_cpp_locale_details` on each page: theme, locales covered, state.
4. Call `apple_ads_apple_ads_find_ads_org_wide` to map each ad to its page and ad group. Note custom pages built but used by no ad.
5. Call `apple_ads_apple_ads_report_ads_in_campaign` over the last 30 days for the campaigns that use custom pages. Compare tap-through rate, conversion rate and cost per install of each custom page with the default page in the same ad group.
6. Call `apple_ads_apple_ads_find_product_page_reasons`, then `apple_ads_apple_ads_get_product_page_reason` for any rejection: quote the reason.

## Output

1. **Summary**: whether custom pages beat the default page, and by how much.
2. **Pages**: a table with page, theme, locales, state, ads using it.
3. **Results**: per ad group, default page against custom pages, on the same period.
4. **Blocked**: rejected pages and the reason Apple gives.
5. **Next steps**: pages to extend, fix or retire, for a person to apply.

## Rules

- Compare pages within the same ad group and period only.
- Do not judge a page on a handful of installs: say the volume is too low.
- Never sum amounts in different currencies.

## Examples

- "Do our custom product pages convert better than the default page?"
- "Pourquoi cette publicité Apple Ads ne diffuse pas ?"
- "Quelles pages produit personnalisées ne sont utilisées par aucune annonce ?"
