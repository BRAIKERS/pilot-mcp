---
name: microsoft-ads-negatives-and-extensions-management
description: "Adds negative keywords and manages ad extensions in Microsoft Advertising (Bing Ads) through the Pilot MCP server, with the user's explicit approval before every write: add Exact or Phrase negatives to a campaign or ad group, create sitelinks and callouts, attach them to the account, a campaign or an ad group, and edit their text. Reads the current lists first and shows the before and after. Use when the user wants to exclude queries or improve ad assets on Microsoft Ads. Triggers: add Bing negative keywords, Microsoft Ads sitelinks, Bing callouts. Déclencheurs : ajouter des mots-clés négatifs Bing, liens annexes Microsoft Ads, accroches Bing, extensions Microsoft Ads."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Microsoft Advertising account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "bing_ads_get_ad_accounts bing_ads_list_campaigns bing_ads_list_ad_groups bing_ads_list_keywords bing_ads_list_negative_keywords bing_ads_list_extensions bing_ads_list_extension_associations bing_ads_add_negative_keywords bing_ads_create_sitelink_extension bing_ads_create_callout_extension bing_ads_update_sitelink_extension bing_ads_update_callout_extension bing_ads_set_extension_association"
  pilot-mcp-read-only: "false"
---

# Microsoft Ads negatives and extensions management

Add the negatives and the ad extensions the user asks for in Microsoft Advertising, one write at a time, each after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** none is needed.
- **Writes:** the write tools need the paid Pilot MCP plan and a workspace role allowed to write. If a write tool is not served, say so and return the list of changes for a person to apply by hand in Microsoft Advertising.

## Tools

- `bing_ads_get_ad_accounts`: the accounts available, with their IDs.
- `bing_ads_list_campaigns`: campaigns of the account.
- `bing_ads_list_ad_groups`: ad groups of a campaign.
- `bing_ads_list_keywords`: active keywords, to avoid blocking them.
- `bing_ads_list_negative_keywords`: current negatives of a campaign or ad group.
- `bing_ads_list_extensions`: extensions of the account by type.
- `bing_ads_list_extension_associations`: where extensions are attached.
- `bing_ads_add_negative_keywords`: adds 1 to 100 Exact or Phrase negatives (write).
- `bing_ads_create_sitelink_extension`: creates a sitelink (write).
- `bing_ads_create_callout_extension`: creates a callout of 25 characters at most (write).
- `bing_ads_update_sitelink_extension`: replaces a sitelink's text, URLs and descriptions (write).
- `bing_ads_update_callout_extension`: changes a callout's text (write).
- `bing_ads_set_extension_association`: attaches an extension to the account, a campaign or an ad group (write).

## Confirmation

Every write changes what live ads show or which searches they match. For each change:

1. Read the current state first: `bing_ads_list_negative_keywords` for negatives, `bing_ads_list_extensions` and `bing_ads_list_extension_associations` for extensions.
2. Show the person the exact target (campaign or ad group name and ID, account), the current list or text and the proposed one.
3. Wait for an explicit yes for that write. A yes covers one write only; one batch of negatives for one campaign counts as one write.
4. Make one write at a time.
5. Never delete or detach anything: this skill only adds and edits. A removal must be asked for separately, naming the object.
6. After the write, read the list or the extension again and show the before and after.
7. If a write is refused or fails, stop, report the message, and do not attempt the next change.

## Steps

1. Call `bing_ads_get_ad_accounts` and confirm the account; use its ID as `target_account_id`.
2. For negatives: find the campaign or ad group, call `bing_ads_list_negative_keywords` and `bing_ads_list_keywords`. Drop any proposed negative that already exists or that would block an active keyword, and say why. Then add the rest with `bing_ads_add_negative_keywords` (`entity_type` Campaign or AdGroup, match type Exact or Phrase).
3. For extensions: call `bing_ads_list_extensions` and `bing_ads_list_extension_associations` to see what exists and where. Reuse an existing extension rather than creating a duplicate.
4. Create a sitelink with `bing_ads_create_sitelink_extension` (link text 35 characters at most, 25 with descriptions, both description lines or none) or a callout with `bing_ads_create_callout_extension`. Then attach it with `bing_ads_set_extension_association`, as a separate confirmed write.
5. Edit an existing one with `bing_ads_update_sitelink_extension` (description lines not given are removed) or `bing_ads_update_callout_extension`.
6. End with the log of changes made and not made.

## Output

1. **Plan**: the numbered list of changes, before any write.
2. **Log**: for each change, the target, before, after and the time it was made.
3. **Not done**: changes dropped, refused or failed, with the reason.

## Rules

- Respect the character limits before sending; never cut a text without showing the result.
- Microsoft cannot change a negative keyword: a wrong one must be removed separately.
- Never chain several writes on one yes.

## Examples

- "Add these negatives to the Bing campaign Generic: free, cheap, jobs."
- "Ajoute quatre liens annexes à la campagne Microsoft Ads Marque."
- "Crée une accroche Bing « Livraison offerte » et rattache-la au compte."
