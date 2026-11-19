---
name: spotify-ads-campaign-review
description: "Reviews Spotify Ads campaigns in read-only mode through the Pilot MCP server: account status, campaigns, ad sets with budget, bid, dates, audio or video format and music or podcast placement, ads with their delivery switch and review status, creative assets, and the impressions, listens, completion rates, reach, frequency, clicks and conversions of the period. Use when the user asks how Spotify audio or video ads perform, or to health-check a Spotify Ads account. Triggers: Spotify Ads review, Spotify audio ads performance, podcast ads results. Déclencheurs : revue Spotify Ads, publicités audio Spotify, performance des annonces podcast, écoutes complètes Spotify."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Spotify Ads account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "spotify_ads_get_ad_account spotify_ads_list_campaigns spotify_ads_list_adsets spotify_ads_list_ads spotify_ads_list_assets spotify_ads_get_statistics pilot_data_freshness"
  pilot-mcp-read-only: "true"
---

# Spotify Ads campaign review

Tell the user how their Spotify campaigns are set up and how they perform, and what to change. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Spotify Ads account.
- **Optional MCP servers:** a file storage server to save the review. None is needed.
- **Read-only:** never pause, restore or change anything. Recommendations are for a person to apply.

## Tools

- `spotify_ads_get_ad_account`: account name, currency, country and status.
- `spotify_ads_list_campaigns`: campaigns with status and objective.
- `spotify_ads_list_adsets`: ad sets with budget, bid, dates, format, placements and targeting.
- `spotify_ads_list_ads`: ads with status, delivery switch, tagline, call to action and asset IDs.
- `spotify_ads_list_assets`: the audio, video and image assets of given IDs.
- `spotify_ads_get_statistics`: metrics by account, campaign, ad set or ad, per day or over the period.
- `pilot_data_freshness`: whether Pilot's collected Spotify data is current.

## Steps

1. Call `spotify_ads_get_ad_account` for the currency and status.
2. Call `spotify_ads_list_campaigns`, `spotify_ads_list_adsets` and `spotify_ads_list_ads`. Flag ads rejected or pending approval, ads switched off, ad sets ended or about to end, ad sets with no active ad.
3. Call `spotify_ads_get_statistics` with `level: "CAMPAIGN"`, `granularity: "LIFETIME"` over the period (90 days at most, UTC days), then `level: "AD_SET"` and `level: "AD"` for the objects that spend most.
4. Read the audio and video funnel: starts, quartiles, completed listens or views, then clicks and conversions. Completion rate is the main quality signal for audio; low completion on one ad points to its creative.
5. Call `spotify_ads_list_assets` with the asset IDs of the best and weakest ads, to compare duration and format.
6. Reach and frequency are not additive: never sum them across ad sets.
7. Call `pilot_data_freshness` with `platform: "spotify_ads"` when the user compares with Pilot's reports.

## Output

1. **Summary**: account status, what works, the main issue.
2. **Campaigns and ad sets**: a table with format, placement, budget, spend, impressions, completion rate and cost per thousand impressions.
3. **Ads**: the best and weakest, with completion and click figures, and their asset duration.
4. **Blocked**: rejected, pending or switched-off ads.
5. **Next steps**: changes for a person to apply.

## Rules

- Amounts are in units of the account currency: name it.
- A ratio with an empty denominator is not available, not zero.
- Do not add reach or frequency across rows.

## Examples

- "How are our Spotify audio ads doing?"
- "Fais la revue des campagnes Spotify Ads du dernier mois."
- "Quelles annonces podcast sont le plus écoutées jusqu'au bout ?"
