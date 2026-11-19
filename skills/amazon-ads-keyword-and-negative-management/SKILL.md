---
name: amazon-ads-keyword-and-negative-management
description: "Manages Amazon Ads keywords, targets and negatives through the Pilot MCP server, with the user's explicit approval before every write: add Sponsored Products keywords with a match type and bid, change keyword and target bids, pause keywords, add negative keywords to Sponsored Products and Sponsored Brands, and adjust Sponsored Brands keyword bids. Each write is simulated first, then applied, then read back. Use when the user wants to act on Amazon keywords or exclusions. Triggers: add Amazon keywords, Amazon negative keywords, Amazon keyword bids. Déclencheurs : ajouter des mots-clés Amazon, mots-clés négatifs Amazon, enchères des mots-clés Amazon."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with an Amazon Ads profile attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "amazon_ads_list_profiles amazon_ads_sp_list_campaigns amazon_ads_sp_list_ad_groups amazon_ads_sp_list_keywords amazon_ads_sp_list_targets amazon_ads_sp_list_negative_keywords amazon_ads_sb_list_keywords amazon_ads_validate_operation amazon_ads_sp_create_keyword amazon_ads_sp_update_keyword amazon_ads_sp_update_target amazon_ads_sp_create_negative_keyword amazon_ads_sb_update_keyword amazon_ads_sb_create_negative_keyword"
  pilot-mcp-read-only: "false"
---

# Amazon Ads keyword and negative management

Add, adjust and exclude Amazon Ads keywords and targets as the user asks, one write at a time, each after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Amazon Ads profile.
- **Optional MCP servers:** none is needed.
- **Writes:** the write tools need the paid Pilot MCP plan and a workspace role allowed to write. If a write tool is not served, say so and return the lists for a person to apply by hand in Amazon Ads.

## Tools

- `amazon_ads_list_profiles`: the profiles available.
- `amazon_ads_sp_list_campaigns`: Sponsored Products campaigns.
- `amazon_ads_sp_list_ad_groups`: Sponsored Products ad groups.
- `amazon_ads_sp_list_keywords`: Sponsored Products keywords with match type, bid and state.
- `amazon_ads_sp_list_targets`: Sponsored Products targeting clauses.
- `amazon_ads_sp_list_negative_keywords`: Sponsored Products negatives.
- `amazon_ads_sb_list_keywords`: Sponsored Brands keywords.
- `amazon_ads_validate_operation`: checks a payload before it is sent.
- `amazon_ads_sp_create_keyword`: adds Sponsored Products keywords (write).
- `amazon_ads_sp_update_keyword`: changes bid or state of Sponsored Products keywords (write).
- `amazon_ads_sp_update_target`: changes bid or state of targeting clauses (write).
- `amazon_ads_sp_create_negative_keyword`: adds Sponsored Products negatives, NEGATIVE_EXACT or NEGATIVE_PHRASE (write).
- `amazon_ads_sb_update_keyword`: changes bid or state of Sponsored Brands keywords (write).
- `amazon_ads_sb_create_negative_keyword`: adds Sponsored Brands negatives (write).

## Confirmation

Every write changes which shopper searches the ads match and what they pay. For each change:

1. Read the current state first with the matching list tool, filtered on the ad group.
2. Show the person the exact target (campaign, ad group, IDs, profile), the current keywords or bids and the proposed ones, in the profile currency.
3. Run `amazon_ads_validate_operation`, then the write tool with `dry_run: true`, and show what Amazon would do.
4. Wait for an explicit yes for that write. A yes covers one write only; one batch for one ad group counts as one write.
5. Send the write with `dry_run: false` (and `confirm: true` when the tool asks for it). Add new keywords in state `PAUSED` when the person wants to review them first.
6. Never archive a keyword, target or negative: pause instead. An archive must be asked for separately, naming the item.
7. After the write, read the list again and show the before and after. If a write is refused or fails, stop, report Amazon's reason, and do not attempt the next change.

## Steps

1. Call `amazon_ads_list_profiles` and confirm the profile.
2. Find the campaign and ad group with `amazon_ads_sp_list_campaigns` and `amazon_ads_sp_list_ad_groups`.
3. Read what exists: `amazon_ads_sp_list_keywords`, `amazon_ads_sp_list_targets`, `amazon_ads_sp_list_negative_keywords`, and `amazon_ads_sb_list_keywords` for Sponsored Brands. Drop duplicates and any negative that would block an active keyword of the same campaign, and say why.
4. Add keywords with `amazon_ads_sp_create_keyword` (campaignId, adGroupId, keywordText, matchType EXACT, PHRASE or BROAD, bid, state).
5. Change bids or states with `amazon_ads_sp_update_keyword`, `amazon_ads_sp_update_target` or `amazon_ads_sb_update_keyword`.
6. Add negatives with `amazon_ads_sp_create_negative_keyword` or `amazon_ads_sb_create_negative_keyword`. When a keyword moves into a manual exact campaign, propose excluding it from the auto campaign as a separate write.
7. End with the log of changes made and not made.

## Output

1. **Plan**: keywords to add, bids to change, negatives to add, per ad group.
2. **Log**: for each write, the target, before, after and the time it was made.
3. **Not done**: items dropped, refused or failed, with the reason.

## Rules

- Bids are in the profile currency: name it.
- If Amazon accepts some items of a batch and rejects others, never resend those already applied.
- Never chain several writes on one yes.

## Examples

- "Add these exact keywords to the Amazon ad group Green tea."
- "Ajoute « gratuit » et « occasion » en négatifs exacts sur la campagne Amazon Auto."
- "Baisse de 10 % les enchères des mots-clés Sponsored Brands de la campagne Marque."
