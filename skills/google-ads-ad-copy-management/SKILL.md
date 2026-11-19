---
name: google-ads-ad-copy-management
description: "Writes and applies Google Ads ad copy through the Pilot MCP server, after explicit approval of each write: reads the responsive search ads of an ad group with their ad strength and results, drafts new headlines and descriptions, then creates a new responsive search ad (paused) or replaces the text of an existing one, one write at a time, and reads the result back. Use when the user asks to rewrite ads, add an ad variant or refresh ad copy. Triggers: rewrite Google ads, new ad variant, update ad copy, responsive search ad text. Déclencheurs : réécrire les annonces Google, nouvelle variante d'annonce, mettre à jour les textes, annonce responsive."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Ads account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_list_ad_groups google_list_ads google_get_ad_strength google_get_ad_performance google_get_keyword_performance google_create_responsive_search_ad google_update_responsive_search_ad_text google_update_ad_status"
  pilot-mcp-read-only: "false"
---

# Google Ads ad copy management

Draft better responsive search ads from the evidence, then apply them one approved write at a time. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a document server to share drafts with the brand. None is needed.
- **Writes:** the write tools require the paid Pilot MCP plan and a workspace role that can write. If a write tool is not served, say so and return the drafts for a person to paste in Google Ads.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_list_ad_groups`: ad groups with their IDs.
- `google_list_ads`: ads with headlines, descriptions, final URLs and status.
- `google_get_ad_strength`: ad strength and policy status.
- `google_get_ad_performance`: results per ad.
- `google_get_keyword_performance`: the ad group's keywords and their results.
- `google_create_responsive_search_ad`: creates a responsive search ad (write).
- `google_update_responsive_search_ad_text`: replaces an ad's headlines, descriptions, URLs or paths (write).
- `google_update_ad_status`: pauses or enables an ad (write).

## Confirmation

Every write changes a live account. For each one:

1. Read the current ad just before writing: its headlines, descriptions, URLs, status and strength.
2. Show the person the exact ad (ad group, ad ID, account), the current text and the proposed text, side by side with character counts.
3. Wait for an explicit yes for this write. A yes for one ad does not cover the next.
4. Make one write at a time.
5. Create every new ad paused (`status: "PAUSED"`). Prefer a new paused variant to rewriting an ad that converts: rewriting sends it back to review and resets its history.
6. Never remove an ad. Pausing the old one is a separate write, after its own yes.
7. After the write, read the ad again with `google_list_ads` and show the before and the after.
8. Stop at the first write that is refused or fails, report it, and do not try a workaround.

## Steps

1. Call `google_list_accounts` and `google_list_ad_groups` to locate the ad group.
2. Call `google_list_ads` with `ad_group_id`, `google_get_ad_strength` for each responsive search ad, and `google_get_ad_performance` with `ad_group_id` over `LAST_30_DAYS`.
3. Call `google_get_keyword_performance` with `ad_group_id`: headlines should answer the searches that bring conversions.
4. Draft: 10 to 15 headlines of 30 characters at most, varied (keyword, benefit, proof, offer, call to action), and 4 descriptions of 90 characters at most. Keep only claims from the existing ads, the site or the user.
5. Choose the write with the person: a new variant with `google_create_responsive_search_ad` (`ad_group_id`, `headlines`, `descriptions`, `final_urls`, optional `path1` and `path2`, `status: "PAUSED"`), or a rewrite with `google_update_responsive_search_ad_text` (`ad_id` and the lists to replace).
6. When asked, pause the ad being replaced with `google_update_ad_status`.
7. Read back after each write.

## Output

1. **Current ads**: text, strength, results.
2. **Drafts**: headlines and descriptions with character counts.
3. **Writes done**: before and after for each.
4. **Not done**: refused or skipped writes, with the text to paste by hand.

## Rules

- Each list given to the rewrite tool replaces the whole current list.
- Count characters exactly; display paths are 15 characters at most.
- Never invent a price, an offer or a claim.
- A new ad goes through Google's policy review before serving.

## Examples

- "Rewrite the weak ads in the Running Shoes ad group."
- "Ajoute une nouvelle annonce responsive en pause dans le groupe Marque, avec ces arguments."
- "Remplace les descriptions de l'annonce 1234 par des versions plus courtes."
