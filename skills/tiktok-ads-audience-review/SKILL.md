---
name: tiktok-ads-audience-review
description: "Reviews TikTok Ads audiences and targeting through the Pilot MCP server: custom and lookalike audiences with their state and expiry, the targeting of each active ad group, its estimated audience size, interest categories, and results by audience dimension (age, gender, country, platform), then flags audiences expired, too small or unused, and targeting worth widening or narrowing. Use when the user asks about TikTok audiences, targeting or lookalikes. Triggers: TikTok audiences, TikTok targeting review, TikTok lookalike. Déclencheurs : audiences TikTok, ciblage TikTok, audiences similaires TikTok, revue du ciblage TikTok."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a TikTok Ads advertiser attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "tiktok_get_advertiser_info tiktok_get_custom_audiences tiktok_get_custom_audience tiktok_get_lookalike_audiences tiktok_get_ad_groups tiktok_estimate_audience_size tiktok_get_audience_report tiktok_search_interest_categories"
  pilot-mcp-read-only: "true"
---

# TikTok Ads audience review

Show which audiences the advertiser has, how each ad group targets, how large each audience is, and which targeting works. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the advertiser.
- **Optional MCP servers:** none.
- **Read-only:** never create, change or delete an audience or a targeting. Proposals are for a person to apply.

## Tools

- `tiktok_get_advertiser_info`: the advertiser attached.
- `tiktok_get_custom_audiences`: custom audiences of the advertiser.
- `tiktok_get_custom_audience`: one audience with validity, expiry, sharing and rule.
- `tiktok_get_lookalike_audiences`: lookalike audiences.
- `tiktok_get_ad_groups`: ad groups with their targeting.
- `tiktok_estimate_audience_size`: estimated audience of a targeting.
- `tiktok_get_audience_report`: results by audience dimension (age, gender, country, platform).
- `tiktok_search_interest_categories`: interest categories for a keyword.

## Steps

1. Call `tiktok_get_advertiser_info`.
2. Call `tiktok_get_custom_audiences` and `tiktok_get_lookalike_audiences`. For audiences in use or that look broken, call `tiktok_get_custom_audience`: an expired, invalid or too small audience is a finding.
3. Call `tiktok_get_ad_groups` and read the targeting of active ad groups: locations, ages, gender, audiences included and excluded, interests.
4. For the ad groups that spend most, call `tiktok_estimate_audience_size` with their targeting, `objective_type` and `optimization_goal`.
5. Call `tiktok_get_audience_report` over the last 30 days with `dimensions` set to `["age", "gender"]`, then `["country_code"]`, and spend, conversion and cost metrics.
6. Use `tiktok_search_interest_categories` to suggest interests close to the best-performing ones.
7. Look for: prospecting ad groups that do not exclude customers, audiences that expired, ad groups so narrow that delivery stalls, lookalikes on a weak source.

## Output

1. **Audiences**: a table with name, type, state, expiry and the ad groups using it.
2. **Targeting by ad group**: who each active ad group reaches, its estimated size and its results.
3. **Results by dimension**: age, gender and country groups that convert or only cost.
4. **Proposals**: audiences to build, exclude, widen or refresh, for a person to apply.

## Rules

- Audience sizes are TikTok's estimates: present them as such.
- Customer files hold personal data: never ask for or display their content.
- Do not judge a group with a few conversions.
- Name the period and the currency.

## Examples

- "Review our TikTok audiences and targeting."
- "Nos audiences TikTok ont-elles expiré ?"
- "Quelles tranches d'âge convertissent sur TikTok ?"
