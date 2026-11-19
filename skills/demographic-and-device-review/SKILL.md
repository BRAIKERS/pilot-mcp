---
name: demographic-and-device-review
description: "Reviews advertising results by age, gender and device through the Pilot MCP server: Google Ads age and gender views and device segments, Meta Ads and TikTok Ads breakdowns, and the same cuts in Google Analytics 4, then shows which groups and devices convert, which only cost, and what bid or targeting change to test. Use when the user asks who converts, how mobile compares with desktop, or which age groups to target. Triggers: demographics report, age and gender performance, mobile versus desktop, device performance. Déclencheurs : performance par âge et sexe, démographie, mobile contre ordinateur, performance par appareil, qui convertit."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with Google Ads, Meta Ads or TikTok Ads attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_run_query google_list_campaign_bid_modifiers meta_list_accounts meta_get_account_insights tiktok_get_advertiser_info tiktok_get_age_gender_performance google_analytics_run_report"
  pilot-mcp-read-only: "true"
---

# Demographic and device review

Show which age groups, genders and devices bring results on each platform, and which ones only cost. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace concerned.
- **Optional MCP servers:** a chart server to draw the splits. None is needed.
- **Read-only:** never change bids or targeting. Proposals are for a person to apply.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_run_query`: a read-only GAQL query, here on the age, gender and campaign views of Google Ads.
- `google_list_campaign_bid_modifiers`: device bid adjustments already set on campaigns.
- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_get_account_insights`: Meta results split by `age`, `gender` or `device_platform`.
- `tiktok_get_advertiser_info`: the TikTok advertiser and its currency.
- `tiktok_get_age_gender_performance`: TikTok results by age group and gender.
- `google_analytics_run_report`: GA4 sessions and key events by device, age and gender.

## Steps

1. Set the period: the last 30 days by default.
2. Google Ads: `google_list_accounts`, then three `google_run_query` calls:
   - age: `SELECT ad_group_criterion.age_range.type, metrics.cost_micros, metrics.conversions, metrics.conversions_value FROM age_range_view WHERE segments.date DURING LAST_30_DAYS`
   - gender: the same fields with `ad_group_criterion.gender.type` from `gender_view`
   - device: `SELECT segments.device, metrics.cost_micros, metrics.conversions, metrics.conversions_value FROM campaign WHERE segments.date DURING LAST_30_DAYS`
   Then `google_list_campaign_bid_modifiers` to see the device adjustments in place.
3. Meta Ads: `meta_list_accounts`, then `meta_get_account_insights` with `date_preset: "last_30d"` and `breakdowns: ["age", "gender"]`, then with `breakdowns: ["device_platform"]`.
4. TikTok Ads, if attached: `tiktok_get_advertiser_info`, then `tiktok_get_age_gender_performance` over the period.
5. GA4: `google_analytics_run_report` with `dimensions: ["deviceCategory"]`, then `["userAgeBracket", "userGender"]`, and `metrics: ["sessions", "keyEvents"]`. Age and gender exist in GA4 only when Google signals is on and above its thresholds.
6. For each cut, compare each group's share of spend with its share of conversions, and its CPA or ROAS with the average.

## Output

1. **Summary**: who converts and on which device, in three lines.
2. **By age and gender**: a table per platform with spend, conversions, CPA and ROAS per group.
3. **By device**: the same for mobile, desktop and tablet, with the adjustments already set.
4. **To test**: bid adjustments or exclusions worth testing, for a person to decide.

## Rules

- Google amounts are in micros: divide by 1,000,000.
- An "undetermined" or "unknown" group is reported as such, never spread over the others.
- A group with few conversions is not judged.
- Never sum amounts in different currencies; name the period.
- Some sectors cannot target by age or gender: say so before proposing an exclusion.

## Examples

- "Which age groups and devices convert best?"
- "Compare mobile et ordinateur sur Google Ads, Meta et TikTok."
- "Qui achète vraiment, par âge et par sexe ?"
