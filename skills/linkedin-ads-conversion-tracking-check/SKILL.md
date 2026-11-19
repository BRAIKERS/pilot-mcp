---
name: linkedin-ads-conversion-tracking-check
description: "Checks LinkedIn Ads conversion tracking through the Pilot MCP server: the Insight Tag, every conversion rule with its type, state, attribution type, windows and value, and the conversions each rule actually recorded, against the campaigns that optimize for website conversions. Use when LinkedIn conversions look wrong, after a site change, or before launching conversion campaigns. Triggers: LinkedIn Insight Tag check, LinkedIn conversions not tracking, LinkedIn conversion rules. Déclencheurs : Insight Tag LinkedIn, conversions LinkedIn, règles de conversion LinkedIn, suivi des conversions LinkedIn."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a LinkedIn ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "linkedin_linkedin_ads_list_accounts linkedin_linkedin_ads_manage_insight_tag linkedin_linkedin_ads_list_conversion_actions linkedin_linkedin_ads_get_conversion_action linkedin_linkedin_ads_analytics linkedin_linkedin_ads_list_campaigns linkedin_linkedin_ads_campaign_performance"
  pilot-mcp-read-only: "true"
---

# LinkedIn Ads conversion tracking check

Tell the user whether LinkedIn measures their conversions correctly, which rules count what, and which campaigns optimize on weak data. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the LinkedIn ad account.
- **Optional MCP servers:** an analytics or tag manager server to confirm the tag fires on the site. None is needed.
- **Read-only:** never create, update or disable a conversion rule. Fixes are for a person to apply.

## Tools

- `linkedin_linkedin_ads_list_accounts`: the ad accounts available.
- `linkedin_linkedin_ads_manage_insight_tag`: the Insight Tag of the account (read only despite its name).
- `linkedin_linkedin_ads_list_conversion_actions`: the conversion rules of the account.
- `linkedin_linkedin_ads_get_conversion_action`: one rule with type, state, attribution and value.
- `linkedin_linkedin_ads_analytics`: conversions per rule with `pivot: "CONVERSION"`.
- `linkedin_linkedin_ads_list_campaigns`: campaigns with their objective.
- `linkedin_linkedin_ads_campaign_performance`: conversions and leads per campaign.

## Steps

1. Call `linkedin_linkedin_ads_list_accounts` and pick the account.
2. Call `linkedin_linkedin_ads_manage_insight_tag`. No tag, or a tag with no recent activity, means website conversions cannot be counted.
3. Call `linkedin_linkedin_ads_list_conversion_actions`, then `linkedin_linkedin_ads_get_conversion_action` on each: name, type (LEAD, PURCHASE, SIGN_UP...), enabled or not, `attribution_type`, post-click and view-through windows, value.
4. Call `linkedin_linkedin_ads_analytics` over the last 30 days with `pivot: "CONVERSION"` and `externalWebsiteConversions` among the fields, to see which rules recorded conversions. An enabled rule with zero conversions over 30 days is suspect.
5. Call `linkedin_linkedin_ads_list_campaigns` and keep those whose objective is website conversions or lead generation. Call `linkedin_linkedin_ads_campaign_performance` to see their conversions and leads.
6. Flag: campaigns optimizing for conversions with no active rule, duplicate rules counting the same action, view-through windows that inflate results, purchase rules without a value.

## Output

1. **Verdict**: tracking reliable, partial or broken, in one sentence.
2. **Insight Tag**: state and what it means.
3. **Rules**: a table with name, type, enabled, attribution, windows, value and conversions recorded.
4. **Campaigns at risk**: conversion campaigns that rely on a missing or silent rule.
5. **Fixes**: in order, for a person to apply.

## Rules

- A rule with no conversions is not proof of a broken tag: low volume can explain it. Say which it is likely to be and why.
- Quote the attribution windows when you compare LinkedIn conversions with another source.
- A value LinkedIn did not return is absent, not zero.

## Examples

- "Are our LinkedIn conversions tracked correctly?"
- "Vérifie l'Insight Tag et les règles de conversion LinkedIn."
- "Pourquoi LinkedIn n'affiche aucune conversion depuis la refonte du site ?"
