---
name: linkedin-ads-audience-demographics-review
description: "Shows who LinkedIn Ads actually reach and convert through the Pilot MCP server: results by job function, seniority, industry, company size, country and company, set against the targeting criteria of each campaign. Use when the user wants to know whether LinkedIn ads reach the right buyers, which segments bring leads, or how to tighten B2B targeting. Triggers: LinkedIn audience breakdown, LinkedIn job function performance, who sees my LinkedIn ads. Déclencheurs : démographie LinkedIn, fonctions et séniorités LinkedIn, qui voit mes publicités LinkedIn, ciblage B2B LinkedIn."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a LinkedIn ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "linkedin_linkedin_ads_list_accounts linkedin_linkedin_ads_list_campaigns linkedin_linkedin_ads_get_campaign linkedin_linkedin_ads_analytics"
  pilot-mcp-read-only: "true"
---

# LinkedIn Ads audience demographics review

Tell the user which professional segments their LinkedIn ads reach, which ones bring leads, and whether the targeting matches the buyers they want. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the LinkedIn ad account.
- **Optional MCP servers:** a CRM server to compare with the profile of won customers. None is needed.
- **Read-only:** never change a campaign or its targeting.

## Tools

- `linkedin_linkedin_ads_list_accounts`: the ad accounts available, with currency.
- `linkedin_linkedin_ads_list_campaigns`: campaigns with objective and status.
- `linkedin_linkedin_ads_get_campaign`: one campaign with its targeting criteria.
- `linkedin_linkedin_ads_analytics`: results by one demographic pivot at a time.

## Steps

1. Ask who the ideal buyer is: functions, seniority, industries, company sizes, countries. If the user does not know, infer it from the targeting and say so.
2. Call `linkedin_linkedin_ads_list_accounts` and pick the account.
3. Call `linkedin_linkedin_ads_list_campaigns`, then `linkedin_linkedin_ads_get_campaign` on the active ones to read their targeting criteria.
4. Call `linkedin_linkedin_ads_analytics` over the last 90 days once per pivot: `MEMBER_JOB_FUNCTION`, `MEMBER_SENIORITY`, `MEMBER_INDUSTRY`, `MEMBER_COMPANY_SIZE`, `MEMBER_COUNTRY_V2`, and `COMPANY` when account-based marketing matters. Ask for `impressions`, `clicks`, `costInLocalCurrency`, `oneClickLeads` and `externalWebsiteConversions`. Filter with `campaign_ids` to look at one campaign.
5. For each pivot, rank segments by share of spend and by cost per lead. Compare the share of spend that goes to the ideal buyer with the share that goes elsewhere.
6. Values come back as URNs and LinkedIn rounds demographic data: translate what you can, and keep the URN when you cannot.

## Output

1. **Summary**: how much of the spend reaches the ideal buyer, in one or two sentences.
2. **Per dimension**: a table for function, seniority, industry, company size and country, with spend share, leads and cost per lead.
3. **Mismatches**: segments that take spend but bring nothing, and segments that convert but are barely targeted.
4. **Targeting suggestions**: exclusions or additions per campaign, for a person to apply.

## Rules

- LinkedIn demographic figures are approximate and hide small segments: say so.
- Do not judge a segment on a handful of leads.
- Never sum amounts in different currencies.

## Examples

- "Are our LinkedIn ads reaching decision makers?"
- "Quelles fonctions et quelles tailles d'entreprise génèrent nos leads LinkedIn ?"
- "Show LinkedIn results by seniority and industry for the last quarter."
