---
name: openai-ads-account-review
description: "Reviews an OpenAI Ads (ChatGPT ads) account in read-only mode through the Pilot MCP server: account status and brand review, spend limit windows, campaigns with bidding, budget and targeting, ad groups and ads with their review status and serving issues, conversion setup, and the spend and conversions of the last 30 days, then returns a prioritized findings report. Use when the user asks to audit or health-check an OpenAI Ads account. Triggers: OpenAI Ads audit, ChatGPT ads review, OpenAI ads account health. Déclencheurs : audit OpenAI Ads, revue des publicités ChatGPT, bilan du compte OpenAI Ads."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with an OpenAI Ads account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "openai_ads_openai_ads_get_ad_account openai_ads_openai_ads_list_spend_limit_windows openai_ads_openai_ads_list_campaigns openai_ads_openai_ads_list_ad_groups openai_ads_openai_ads_list_ads openai_ads_openai_ads_list_conversion_event_settings openai_ads_openai_ads_account_insights openai_ads_openai_ads_conversion_insights pilot_data_freshness"
  pilot-mcp-read-only: "true"
---

# OpenAI Ads account review

Review one OpenAI Ads account and hand back a short, prioritized list of findings, each backed by what the account shows. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the OpenAI Ads account.
- **Optional MCP servers:** a file storage server to save the report. None is needed.
- **Read-only:** never pause, activate, archive, create or update anything. Every recommendation is written for a person to apply.

## Tools

- `openai_ads_openai_ads_get_ad_account`: account ID, currency, time zone, status and brand review.
- `openai_ads_openai_ads_list_spend_limit_windows`: spend limit windows above campaign budgets.
- `openai_ads_openai_ads_list_campaigns`: campaigns with status, bidding type, budget in micros and targeting.
- `openai_ads_openai_ads_list_ad_groups`: ad groups with bidding config and context hints.
- `openai_ads_openai_ads_list_ads`: ads with creative and review status.
- `openai_ads_openai_ads_list_conversion_event_settings`: what counts as a conversion, and over which window.
- `openai_ads_openai_ads_account_insights`: impressions, clicks, spend, click-through rate, cost per click and per thousand impressions.
- `openai_ads_openai_ads_conversion_insights`: attributed conversions per campaign, ad group or ad.
- `pilot_data_freshness`: whether Pilot's collected OpenAI Ads data is current.

## Steps

1. Call `openai_ads_openai_ads_get_ad_account`. If the brand review is not approved, or an integrity review exists, put it at the top: nothing serves until the brand is approved.
2. Call `openai_ads_openai_ads_list_spend_limit_windows`: a window close to its limit stops every campaign.
3. Call `openai_ads_openai_ads_list_campaigns`, `openai_ads_openai_ads_list_ad_groups` and `openai_ads_openai_ads_list_ads`, each with `include: ["serving_issues"]`. List what blocks delivery: budget, schedule, targeting, review. Budgets are in micros: divide by 1,000,000.
4. Call `openai_ads_openai_ads_list_conversion_event_settings`: campaigns bidding on conversions need a setting that receives events.
5. Call `openai_ads_openai_ads_account_insights` with `aggregation_level: "campaign"`, `time_granularity: "none"` and `time_ranges` set to the last 30 days (`{ type: 'date_range', since, until }`).
6. Call `openai_ads_openai_ads_conversion_insights` with `aggregation_level: "campaign"` and the same single range.
7. Call `pilot_data_freshness` with `platform: "openai_ads"` when comparing with Pilot's reports.
8. Write the report, findings ranked by money at stake.

## Output

1. **Summary**: account and brand review status, top three priorities.
2. **Findings**: a table with the finding, the evidence, the stake and the suggested action.
3. **Delivery blockers**: per campaign, ad group or ad, the issue OpenAI reports.
4. **Next steps**: actions in order, for a person to apply.

## Rules

- An empty serving issues list is not proof that an object serves: check its status and its impressions.
- A ratio with an empty denominator is not available, not zero.
- Name the currency and the period.

## Examples

- "Audit our OpenAI Ads account."
- "Pourquoi nos publicités ChatGPT ne diffusent pas ?"
- "Fais le point sur le compte OpenAI Ads avant d'augmenter le budget."
