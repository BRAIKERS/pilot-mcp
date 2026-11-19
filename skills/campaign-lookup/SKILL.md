---
name: campaign-lookup
description: "Finds a campaign by name across all connected ad platforms through the Pilot MCP server and returns its card: platform, account, status, objective or channel, budget and recent results, with its daily trend on Google Ads, Meta Ads or TikTok Ads. Use when the user names a campaign and wants to know where it runs, how it is doing or whether it is still live. Triggers: find campaign, how is campaign X doing, campaign status, look up campaign. Déclencheurs : trouver une campagne, où en est la campagne, état de la campagne, fiche de campagne, chercher une campagne."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with at least one ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "search fetch google_list_campaigns google_get_campaign_performance meta_get_campaign meta_get_campaign_insights tiktok_get_campaigns tiktok_get_campaign_performance_summary"
  pilot-mcp-read-only: "true"
---

# Campaign lookup

Find the campaign the user means, wherever it runs, and give its card in a few lines. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace concerned.
- **Optional MCP servers:** none.
- **Read-only:** never change the campaign. If the user wants a change, say which change a person should make.

## Tools

- `search`: campaigns whose name matches the query, those that delivered first, then those without delivery such as paused ones.
- `fetch`: one campaign found by `search`, by its namespaced ID, with its recent metrics.
- `google_list_campaigns`: Google Ads campaigns with status, channel type, budget and dates.
- `google_get_campaign_performance`: daily cost and conversions of one Google Ads campaign.
- `meta_get_campaign`: one Meta campaign with status, objective and budget.
- `meta_get_campaign_insights`: results of one Meta campaign over a preset.
- `tiktok_get_campaigns`: TikTok campaigns with status, objective and budget.
- `tiktok_get_campaign_performance_summary`: TikTok results per campaign between two dates.

## Steps

1. Call `search` with the words the user gave for the campaign name.
2. If several campaigns match, list them (name, platform, delivered or not) and ask which one, unless one matches exactly.
3. Call `fetch` with the chosen ID (for example a `campaign:` ID with the platform and the platform's own campaign ID).
4. Complete the card on its platform:
   - Google Ads: `google_list_campaigns` for status, channel type, budget and dates, then `google_get_campaign_performance` with `campaign_id` over `LAST_30_DAYS` for the daily trend.
   - Meta Ads: `meta_get_campaign` with `campaign_id`, then `meta_get_campaign_insights` with `date_preset: "last_30d"`.
   - TikTok Ads: `tiktok_get_campaigns` with `campaign_ids`, then `tiktok_get_campaign_performance_summary` over the last 30 days with `include_daily: true`.
5. Say whether the campaign delivered in the last days, and how its last seven days compare with the 30-day average.

## Output

1. **Card**: name, platform, account, ID, status, objective or channel type, budget, start and end dates.
2. **Results**: spend, conversions, CPA and ROAS over 30 days, and the last seven days against that average.
3. **State**: delivering, paused, ended, or limited, with the reason the platform gives.

## Rules

- Quote the name exactly as the platform returns it.
- If `search` finds nothing, say so and suggest a shorter query; never invent a campaign.
- Name the period and the currency of each figure.
- A metric the platform did not return is absent, not zero.

## Examples

- "How is the Black Friday campaign doing?"
- "Où tourne la campagne « Soldes hiver », et est-elle encore active ?"
- "Donne-moi la fiche de la campagne Prospection France."
