---
name: reddit-ads-account-review
description: "Reviews a Reddit Ads account in read-only mode through the Pilot MCP server: account status and suspension reason, attribution windows, campaigns, ad groups and ads with configured and applied status, budgets, bids, spend caps and rejected ads, and the spend and conversions of the last 30 days, then returns a prioritized findings report. Use when the user asks to audit or health-check a Reddit Ads account. Triggers: Reddit Ads audit, review my Reddit account, Reddit ads rejected. Déclencheurs : audit Reddit Ads, revue du compte Reddit, publicités Reddit refusées, bilan de santé Reddit."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Reddit Ads account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "reddit_ads_get_ad_account reddit_ads_list_campaigns reddit_ads_list_ad_groups reddit_ads_list_ads reddit_ads_get_statistics pilot_data_freshness"
  pilot-mcp-read-only: "true"
---

# Reddit Ads account review

Review one Reddit Ads account and hand back a short, prioritized list of findings, each backed by what the account shows. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Reddit Ads account.
- **Optional MCP servers:** a file storage server to save the report. None is needed.
- **Read-only:** never pause, change, create or delete anything. Every recommendation is written for a person to apply.

## Tools

- `reddit_ads_get_ad_account`: account name, currency, time zone, status, suspension reason and attribution windows.
- `reddit_ads_list_campaigns`: campaigns with configured and applied status, objective, campaign budget, spend cap and dates.
- `reddit_ads_list_ad_groups`: ad groups with status, budget, bid and its type, dates and targeting.
- `reddit_ads_list_ads`: ads with status, rejection reason, destination URL and the post that carries them.
- `reddit_ads_get_statistics`: impressions, clicks, spend and conversions by level, per day or in total, 92 days at most.
- `pilot_data_freshness`: whether Pilot's collected Reddit data is current.

## Steps

1. Call `reddit_ads_get_ad_account`. If the status is not VALID, put it and the suspension reason at the top: nothing else matters until it is fixed. Note the currency, the time zone and the attribution windows.
2. Call `pilot_data_freshness` with `platform: "reddit_ads"` and say if Pilot's copy is behind.
3. Call `reddit_ads_list_campaigns`, `reddit_ads_list_ad_groups` and `reddit_ads_list_ads`. Check `reponse.tronque` and read further with `max_items` when it is true.
4. Flag: ads whose applied status is REJECTED (quote the reason), objects configured ACTIVE but not applied as active, active campaigns with no active ad group, ad groups with no active ad, campaigns near their spend cap, end dates already past.
5. Call `reddit_ads_get_statistics` with `level: "CAMPAIGN"`, `granularity: "TOTAL"` and the last 30 days, then with `level: "AD_GROUP"` for the campaigns that spend most. Note spend without key conversions.
6. Write the report, findings ranked by money at stake.

## Output

1. **Summary**: account status and the top three priorities.
2. **Findings**: a table with the finding, the evidence, the stake and the suggested action.
3. **Blocked ads**: rejected or pending ads, with Reddit's reason.
4. **Next steps**: actions in order, for a person to apply in Reddit Ads.

## Rules

- Amounts are in units of the account currency: name it.
- Days follow the account time zone.
- A conversion count Reddit did not return is absent, not zero.

## Examples

- "Audit our Reddit Ads account."
- "Pourquoi nos publicités Reddit ne diffusent plus ?"
- "Fais le point sur le compte Reddit Ads avant la campagne de lancement."
