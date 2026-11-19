---
name: openai-ads-serving-issues-check
description: "Finds why OpenAI Ads (ChatGPT ads) do not serve through the Pilot MCP server: brand and integrity reviews, spend limit windows, campaign, ad group and ad serving issues, bids flagged too low, ads rejected or in review, landing page crawl proof and ad previews. Use when an OpenAI Ads campaign has no or few impressions, or an ad stays in review. Triggers: OpenAI ads not serving, ChatGPT ad rejected, bid too low OpenAI. Déclencheurs : publicités ChatGPT qui ne diffusent pas, annonce OpenAI refusée, enchère trop basse OpenAI Ads."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with an OpenAI Ads account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "openai_ads_openai_ads_get_ad_account openai_ads_openai_ads_list_spend_limit_windows openai_ads_openai_ads_get_campaign openai_ads_openai_ads_get_ad_group openai_ads_openai_ads_list_ads openai_ads_openai_ads_get_ad openai_ads_openai_ads_get_landing_page_crawl openai_ads_openai_ads_preview_ad openai_ads_openai_ads_campaign_insights"
  pilot-mcp-read-only: "true"
---

# OpenAI Ads serving issues check

Find what stops an OpenAI Ads campaign from serving, from the account down to each ad, and say what to fix. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the OpenAI Ads account.
- **Optional MCP servers:** none is needed.
- **Read-only:** never activate, pause or change anything. Fixes are for a person to apply.

## Tools

- `openai_ads_openai_ads_get_ad_account`: account status, brand review and integrity review.
- `openai_ads_openai_ads_list_spend_limit_windows`: spend limit windows that can stop delivery.
- `openai_ads_openai_ads_get_campaign`: one campaign with its serving issues.
- `openai_ads_openai_ads_get_ad_group`: one ad group with serving issues and the bid too low flag.
- `openai_ads_openai_ads_list_ads`: ads of an ad group with review status and serving issues.
- `openai_ads_openai_ads_get_ad`: one ad with its review detail.
- `openai_ads_openai_ads_get_landing_page_crawl`: whether the crawler reached the landing page of the ad.
- `openai_ads_openai_ads_preview_ad`: a preview of the ad, to show rather than describe it.
- `openai_ads_openai_ads_campaign_insights`: impressions and spend, to confirm the symptom.

## Steps

1. Call `openai_ads_openai_ads_campaign_insights` over the last 7 days with `time_granularity: "daily"` to confirm the symptom: no impressions, or a sudden stop.
2. Account level: `openai_ads_openai_ads_get_ad_account` (brand review not approved, integrity review open) and `openai_ads_openai_ads_list_spend_limit_windows` (window reached or expired).
3. Campaign level: `openai_ads_openai_ads_get_campaign` with `include: ["serving_issues"]`: budget, schedule, targeting.
4. Ad group level: `openai_ads_openai_ads_get_ad_group` with `include: ["serving_issues", "bid_too_low"]`. A bid flagged too low is advice from OpenAI, not a hard block: say so.
5. Ad level: `openai_ads_openai_ads_list_ads` with `include: ["serving_issues"]`, then `openai_ads_openai_ads_get_ad` on ads rejected or in review, and quote the review reason.
6. For rejected or suspicious ads, call `openai_ads_openai_ads_get_landing_page_crawl`: a landing page the crawler could not capture often explains a rejection. Call `openai_ads_openai_ads_preview_ad` to show the ad.
7. Stop at the first level that blocks everything below it, but list the issues found at lower levels too.

## Output

1. **Cause**: the blocking issue, at which level, in one sentence.
2. **Chain**: account, campaign, ad group, ad, with the state of each.
3. **Other issues**: problems that will block next once the first is fixed.
4. **Fixes**: in order, for a person to apply.

## Rules

- An empty serving issues list is not proof that an object serves.
- Quote OpenAI's review reasons as given; do not guess another one.
- Name the period of the delivery figures.

## Examples

- "Why is our OpenAI Ads campaign not getting impressions?"
- "Notre annonce ChatGPT est refusée : pourquoi ?"
- "Qu'est-ce qui bloque la diffusion de la campagne OpenAI Ads Lancement ?"
