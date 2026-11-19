---
name: apple-ads-keyword-management
description: "Manages Apple Ads keywords and negatives through the Pilot MCP server, with the user's explicit approval before every write: add exact or broad targeting keywords with a bid, change keyword bids or pause keywords, and add negative keywords to a campaign or an ad group, starting from the search terms report. Reads the current lists first and shows the before and after. Use when the user wants to act on Apple Ads keywords or search terms. Triggers: add Apple Ads keywords, Apple Search Ads negatives, change keyword bids Apple. Déclencheurs : ajouter des mots-clés Apple Ads, mots-clés négatifs Apple Search Ads, enchères des mots-clés Apple."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with an Apple Ads organization attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "apple_ads_apple_ads_list_campaigns apple_ads_apple_ads_list_adgroups apple_ads_apple_ads_list_targeting_keywords apple_ads_apple_ads_get_targeting_keyword apple_ads_apple_ads_list_campaign_negative_keywords apple_ads_apple_ads_list_adgroup_negative_keywords apple_ads_apple_ads_report_search_terms_in_adgroup apple_ads_apple_ads_create_targeting_keywords apple_ads_apple_ads_update_targeting_keywords apple_ads_apple_ads_create_campaign_negative_keywords apple_ads_apple_ads_create_adgroup_negative_keywords"
  pilot-mcp-read-only: "false"
---

# Apple Ads keyword management

Add, adjust and exclude Apple Ads keywords as the user asks, one write at a time, each after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Apple Ads organization.
- **Optional MCP servers:** none is needed.
- **Writes:** the write tools need the paid Pilot MCP plan and a workspace role allowed to write. If a write tool is not served, say so and return the lists for a person to apply by hand in Apple Ads.

## Tools

- `apple_ads_apple_ads_list_campaigns`: campaigns of the organization.
- `apple_ads_apple_ads_list_adgroups`: ad groups of a campaign.
- `apple_ads_apple_ads_list_targeting_keywords`: current keywords with match type, bid and status.
- `apple_ads_apple_ads_get_targeting_keyword`: one keyword in full.
- `apple_ads_apple_ads_list_campaign_negative_keywords`: campaign negatives.
- `apple_ads_apple_ads_list_adgroup_negative_keywords`: ad group negatives.
- `apple_ads_apple_ads_report_search_terms_in_adgroup`: the searches that triggered an ad group.
- `apple_ads_apple_ads_create_targeting_keywords`: adds keywords to an ad group (write).
- `apple_ads_apple_ads_update_targeting_keywords`: changes bids or status of keywords (write).
- `apple_ads_apple_ads_create_campaign_negative_keywords`: adds campaign negatives (write).
- `apple_ads_apple_ads_create_adgroup_negative_keywords`: adds ad group negatives (write).

## Confirmation

Every write changes which App Store searches the ads match and what they pay. For each change:

1. Read the current state first with `apple_ads_apple_ads_list_targeting_keywords` or the negative keyword lists.
2. Show the person the exact target (campaign, ad group, IDs), the current keywords or bids and the proposed ones, with the currency for bids.
3. Wait for an explicit yes for that write. A yes covers one write only; one batch for one ad group counts as one write.
4. Make one write at a time.
5. Add new keywords paused when the person wants to review them before they serve.
6. Never delete a keyword or a negative: pause instead. A deletion must be asked for separately, naming the keyword.
7. After the write, read the list again and show the before and after.
8. If a write is refused or fails, stop, report the message, and do not attempt the next change.

## Steps

1. Call `apple_ads_apple_ads_list_campaigns` and `apple_ads_apple_ads_list_adgroups` to confirm the targets.
2. When the user starts from search terms, call `apple_ads_apple_ads_report_search_terms_in_adgroup` over the last 30 days and propose winners as exact keywords and losers as negatives.
3. Read what exists: `apple_ads_apple_ads_list_targeting_keywords`, `apple_ads_apple_ads_list_campaign_negative_keywords`, `apple_ads_apple_ads_list_adgroup_negative_keywords`. Drop duplicates and any negative that would block an active keyword, and say why.
4. Add keywords with `apple_ads_apple_ads_create_targeting_keywords` (text, match type EXACT or BROAD, bid amount and currency).
5. Change bids or pause with `apple_ads_apple_ads_update_targeting_keywords`; check one keyword with `apple_ads_apple_ads_get_targeting_keyword` before and after.
6. Add negatives with `apple_ads_apple_ads_create_campaign_negative_keywords` (to keep a term out of a whole campaign, such as a discovery campaign) or `apple_ads_apple_ads_create_adgroup_negative_keywords`.
7. End with the log of changes made and not made.

## Output

1. **Plan**: keywords to add, bids to change, negatives to add, per ad group.
2. **Log**: for each write, the target, before, after and the time it was made.
3. **Not done**: items dropped, refused or failed, with the reason.

## Rules

- Bids are in the campaign currency: name it.
- When a term moves into an exact campaign, propose excluding it from the discovery campaign as a separate write.
- Never chain several writes on one yes.

## Examples

- "Add the best search terms as exact keywords in the Apple Ads Brand ad group."
- "Exclus ces recherches de la campagne Apple Découverte."
- "Baisse de 15 % l'enchère des mots-clés Apple qui coûtent trop par installation."
