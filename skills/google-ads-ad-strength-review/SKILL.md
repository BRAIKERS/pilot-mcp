---
name: google-ads-ad-strength-review
description: "Reviews Google Ads responsive search ads through the Pilot MCP server: ad strength and policy status of each ad, number and variety of headlines and descriptions, ad groups with too few or weak ads, and how each ad performs, then proposes new headlines and descriptions for a person to approve. Use when the user asks to improve ad copy, why ad strength is poor, or which ads to rewrite. Triggers: ad strength review, responsive search ads, improve ad copy, RSA audit. Déclencheurs : force des annonces, annonces responsives, améliorer les textes, audit des annonces Search."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Ads account that runs Search campaigns."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_list_campaigns google_list_ad_groups google_list_ads google_get_ad_strength google_get_ad_performance google_get_keyword_performance"
  pilot-mcp-read-only: "true"
---

# Google Ads ad strength review

Find the responsive search ads that hold the account back, explain why, and draft better text. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a document server to share the drafts with the brand. None is needed.
- **Read-only:** never edit, pause or create an ad. Drafts are for a person to approve and apply.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_list_campaigns`: campaigns with their channel type.
- `google_list_ad_groups`: ad groups per campaign.
- `google_list_ads`: ads with type, status, headlines, descriptions and final URLs.
- `google_get_ad_strength`: ad strength and policy information of responsive search ads.
- `google_get_ad_performance`: impressions, clicks, CTR, cost and conversions per ad.
- `google_get_keyword_performance`: the keywords of each ad group, to check that ads speak to them.

## Steps

1. Call `google_list_accounts` and pick the account. Call `google_list_campaigns` and keep Search campaigns that spend.
2. Call `google_get_ad_strength` without `ad_id` to list every responsive search ad with its strength and policy status.
3. Call `google_list_ads` for their headlines, descriptions and final URLs, and `google_list_ad_groups` to group them.
4. Call `google_get_ad_performance` over `LAST_30_DAYS` to weigh each ad.
5. Call `google_get_keyword_performance` on the ad groups with weak ads, to see which searches the ads must answer.
6. For each weak ad, find the causes: fewer than about ten headlines, repeated wording, no keyword in headlines, too many pinned positions, no call to action, a policy limit.
7. Draft replacement headlines (30 characters at most) and descriptions (90 characters at most) that keep the brand's claims and use the ad group's main keywords.

## Output

1. **Summary**: share of ads rated poor or average, weighted by spend.
2. **Ads to fix**: a table with campaign, ad group, ad ID, strength, policy status, spend and the causes found.
3. **Drafts**: for each ad, the proposed headlines and descriptions with their character count.
4. **Ad groups short of ads**: those with a single ad or none approved.

## Rules

- Ad strength is Google's guide, not a result: an ad rated average that converts well is not rewritten first.
- Never invent a claim, a price or an offer: use only what the existing ads, the site or the user state.
- Count characters exactly; a headline over 30 characters is refused.
- Name the period of the performance figures.

## Examples

- "Which responsive search ads have poor ad strength, and how do we fix them?"
- "Propose-moi de nouveaux titres pour les annonces faibles de la campagne Search."
- "Quels groupes d'annonces n'ont qu'une seule annonce ?"
