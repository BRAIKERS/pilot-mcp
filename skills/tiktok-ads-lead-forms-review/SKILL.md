---
name: tiktok-ads-lead-forms-review
description: "Reviews TikTok lead generation through the Pilot MCP server: the Instant Forms of the advertiser, their questions and status, submissions and conversion rate per form, the lead generation campaigns and ad groups that use them, and their cost per lead, then says which forms and campaigns to keep, simplify or stop. Use when the user asks about TikTok leads, Instant Forms or cost per lead on TikTok. Triggers: TikTok lead forms, TikTok instant forms, TikTok leads, cost per lead TikTok. Déclencheurs : formulaires TikTok, formulaires instantanés TikTok, prospects TikTok, coût par prospect TikTok."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a TikTok Ads advertiser that runs lead generation."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "tiktok_get_advertiser_info tiktok_get_lead_forms tiktok_get_lead_form_details tiktok_get_lead_form_stats tiktok_get_campaigns tiktok_get_campaign_performance_summary tiktok_get_adgroup_performance"
  pilot-mcp-read-only: "true"
---

# TikTok Ads lead forms review

Tell the user which TikTok forms and campaigns bring leads at a good cost, and what to change in the forms. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the advertiser.
- **Optional MCP servers:** a CRM server to check lead quality downstream. None is needed.
- **Read-only:** never edit, publish or disable a form or a campaign. Proposals are for a person to apply.

## Tools

- `tiktok_get_advertiser_info`: the advertiser and its currency.
- `tiktok_get_lead_forms`: Instant Forms, draft or published.
- `tiktok_get_lead_form_details`: one form's questions and settings.
- `tiktok_get_lead_form_stats`: submissions and conversion rate per form.
- `tiktok_get_campaigns`: campaigns, to keep lead generation ones.
- `tiktok_get_campaign_performance_summary`: spend and conversions per campaign.
- `tiktok_get_adgroup_performance`: results per ad group.

## Steps

1. Call `tiktok_get_advertiser_info`.
2. Call `tiktok_get_lead_forms`, then `tiktok_get_lead_form_details` with the `page_id` of each published form.
3. Call `tiktok_get_lead_form_stats` with the `page_ids` over the last 30 days.
4. Call `tiktok_get_campaigns` with `objective_type: "LEAD_GENERATION"`, then `tiktok_get_campaign_performance_summary` over the same period, and `tiktok_get_adgroup_performance` for each lead campaign.
5. Compute cost per lead per campaign and ad group. Compare each form's conversion rate with the others.
6. Look at the forms: number of questions, required fields, a privacy policy link, a clear thank-you step. A long form lowers volume; a very short one can lower quality.

## Output

1. **Summary**: leads, cost per lead and the main lever.
2. **Forms**: a table with name, status, number of questions, submissions and conversion rate.
3. **Campaigns and ad groups**: spend, leads and cost per lead.
4. **Proposals**: forms to simplify or enrich, campaigns to keep or stop, for a person to decide.

## Rules

- Lead counts say nothing about quality: say so, and suggest checking the CRM.
- Never ask for or display the content of leads.
- Name the period and the currency.

## Examples

- "How are our TikTok lead forms doing?"
- "Quel formulaire TikTok a le meilleur taux de conversion ?"
- "Quel est notre coût par prospect sur TikTok, par campagne ?"
