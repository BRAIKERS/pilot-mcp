---
name: tiktok-ads-placement-and-demographics-review
description: "Breaks TikTok Ads results down through the Pilot MCP server: by placement (TikTok, Pangle and other apps), by age group and gender, and by country or region, then shows where cost per conversion differs from the average and which placements, groups or places to keep, lower or exclude. Use when the user asks where TikTok ads perform, whether Pangle is worth it, or who converts on TikTok. Triggers: TikTok placements, TikTok demographics, Pangle performance, TikTok by country. Déclencheurs : placements TikTok, démographie TikTok, performance Pangle, TikTok par pays, qui convertit sur TikTok."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a TikTok Ads advertiser attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "tiktok_get_advertiser_info tiktok_get_placement_performance tiktok_get_age_gender_performance tiktok_get_geo_performance tiktok_get_ad_groups"
  pilot-mcp-read-only: "true"
---

# TikTok Ads placement and demographics review

Show where TikTok ads perform and where they only cost: placements, ages, genders and places. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the advertiser.
- **Optional MCP servers:** a chart server to draw the splits. None is needed.
- **Read-only:** never change placements or targeting. Proposals are for a person to apply.

## Tools

- `tiktok_get_advertiser_info`: the advertiser and its currency.
- `tiktok_get_placement_performance`: results by placement.
- `tiktok_get_age_gender_performance`: results by age group and gender.
- `tiktok_get_geo_performance`: results by country or region.
- `tiktok_get_ad_groups`: ad groups with their placement setting and targeting.

## Steps

1. Call `tiktok_get_advertiser_info`. Set the period: the last 30 days by default.
2. Call `tiktok_get_placement_performance`, `tiktok_get_age_gender_performance` and `tiktok_get_geo_performance` over the period.
3. Call `tiktok_get_ad_groups` to see which ad groups use automatic placement and which restrict it, and the ages and places they target.
4. For each cut, compare share of spend with share of conversions, and cost per conversion with the average.
5. Read with care: Pangle and other partner apps often bring cheap clicks and weak conversions; a placement chosen automatically may still help at the margin. Propose a test before an exclusion.

## Output

1. **Summary**: where conversions come from, in three lines.
2. **Placements**: a table with spend, clicks, conversions, cost per conversion and share of spend.
3. **Age and gender**: the same per group.
4. **Places**: the same per country or region.
5. **Proposals**: placements, groups or places to test excluding or to give their own ad group, for a person to decide.

## Rules

- Do not judge a group with a few conversions.
- Never sum amounts in different currencies; name the period.
- Some sectors cannot target by age or gender: say so before proposing an exclusion.
- A metric TikTok did not return is absent, not zero.

## Examples

- "Is Pangle worth it, or should we keep TikTok only?"
- "Qui convertit sur TikTok, par âge et par sexe ?"
- "Quels pays coûtent sans convertir sur TikTok ?"
