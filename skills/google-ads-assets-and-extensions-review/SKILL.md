---
name: google-ads-assets-and-extensions-review
description: "Reviews the assets and extensions of a Google Ads account through the Pilot MCP server: sitelinks, callouts, structured snippets, calls, images and other assets linked to each campaign, campaigns that miss the common ones, assets paused or disapproved, and how assets are used across campaigns, then drafts the missing texts for a person to approve. Use when the user asks about sitelinks, callouts, extensions or assets, or why ads look smaller than competitors'. Triggers: ad extensions review, sitelinks, callouts, Google Ads assets. Déclencheurs : extensions d'annonce, liens annexes, accroches, extraits structurés, éléments Google Ads."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Ads account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_list_campaigns google_list_campaign_assets google_list_assets google_list_extensions google_get_campaign_performance"
  pilot-mcp-read-only: "true"
---

# Google Ads assets and extensions review

Check that each campaign carries the assets that make its ads larger and more useful, and draft what is missing. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a document server to share the drafts. None is needed.
- **Read-only:** never create, link, pause or unlink an asset. Drafts are for a person to approve and apply.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_list_campaigns`: campaigns with status and channel type.
- `google_list_campaign_assets`: assets linked to each campaign, with field type and status.
- `google_list_assets`: the account's assets (images, text, videos, callouts, sitelinks).
- `google_list_extensions`: extensions by type (sitelink, call, callout, and so on).
- `google_get_campaign_performance`: cost per campaign, to rank where gaps cost most.

## Steps

1. Call `google_list_accounts` and pick the account. Call `google_list_campaigns` and keep active Search and Performance Max campaigns.
2. Call `google_list_campaign_assets` with `limit: 1000` to see, per campaign, which field types are linked and their status.
3. Call `google_list_extensions` per type (`SITELINK`, `CALLOUT`, `STRUCTURED_SNIPPET`, `CALL`) and `google_list_assets` for images and logos.
4. Call `google_get_campaign_performance` over `LAST_30_DAYS` to rank campaigns by spend.
5. Flag per campaign: fewer than four sitelinks, fewer than four callouts, no structured snippet, no image or logo where the campaign type allows them, a phone number missing for a business that takes calls, assets paused or not approved, sitelinks pointing to the same page as the ad.
6. Draft the missing texts from the site and existing ads: sitelink text 25 characters at most with two description lines of 35, callouts 25 characters at most, structured snippet values (at least three) under a standard header.

## Output

1. **Summary**: how complete the assets are, weighted by spend.
2. **Per campaign**: a table with sitelinks, callouts, snippets, call, images, logo, and the gaps.
3. **Assets with issues**: paused, disapproved or pointing to wrong pages.
4. **Drafts**: proposed texts with their character counts and target URLs.

## Rules

- Never invent an offer, a price, a phone number or a page: take them from the site, the existing ads or the user.
- Count characters exactly.
- Account-level assets apply to campaigns without their own: say so when a campaign seems empty.
- Google never deletes an asset; unlinking only removes it from a campaign.

## Examples

- "Which campaigns miss sitelinks or callouts?"
- "Fais la revue des extensions Google Ads et propose les accroches manquantes."
- "Nos annonces ont-elles assez d'éléments par rapport aux concurrents ?"
