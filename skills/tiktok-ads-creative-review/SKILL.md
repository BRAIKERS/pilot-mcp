---
name: tiktok-ads-creative-review
description: "Reviews TikTok Ads creatives through the Pilot MCP server: best and worst ads on the goal's metric, video watch-through and hook rates, creatives that tire over time, Spark Ads against regular ads, the texts and calls to action used, and the videos in the library, then says which creatives to keep, refresh or stop, and what new ones to brief. Use when the user asks which TikTok videos work, about creative fatigue on TikTok, or what to brief next. Triggers: TikTok creative review, best TikTok ads, TikTok video performance, Spark Ads. Déclencheurs : revue des créas TikTok, meilleures vidéos TikTok, performance des vidéos, Spark Ads, fatigue des créas TikTok."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a TikTok Ads advertiser attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "tiktok_get_advertiser_info tiktok_get_creative_performance tiktok_get_creatives tiktok_get_basic_report tiktok_get_spark_ads tiktok_get_video_info tiktok_get_ads"
  pilot-mcp-read-only: "true"
---

# TikTok Ads creative review

Tell the user which TikTok creatives carry the results, which are tiring, and what to brief next. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the advertiser.
- **Optional MCP servers:** a document server to share the brief with the creative team. None is needed.
- **Read-only:** never disable, edit or create an ad. Recommendations are for a person to apply.

## Tools

- `tiktok_get_advertiser_info`: the advertiser and its currency.
- `tiktok_get_creative_performance`: results per creative or ad, to find top and bottom performers.
- `tiktok_get_creatives`: ads with their media, text and call to action.
- `tiktok_get_basic_report`: video metrics per ad and per day (plays, 2-second and 6-second views, watched to 25, 50, 75 and 100%).
- `tiktok_get_spark_ads`: organic posts authorized for Spark Ads.
- `tiktok_get_video_info`: length and details of library videos.
- `tiktok_get_ads`: ads with their status and ad group.

## Steps

1. Call `tiktok_get_advertiser_info`.
2. Call `tiktok_get_creative_performance` over the last 30 days. Set aside ads with too little spend or too few conversions to judge.
3. Call `tiktok_get_basic_report` with `data_level: "AUCTION_AD"`, `dimensions: ["ad_id"]` and video metrics (`video_play_actions`, `video_watched_2s`, `video_watched_6s`, `video_views_p25`, `video_views_p50`, `video_views_p75`, `video_views_p100`) with spend, clicks and conversions. The share of 2-second views among plays is the hook rate; the share of full views is the hold.
4. For the main ads, call `tiktok_get_basic_report` again with `dimensions: ["ad_id", "stat_time_day"]` and a filter on their IDs: falling CTR and rising cost per conversion over the weeks is fatigue.
5. Call `tiktok_get_creatives` for the best and worst ads to link figures to formats, texts and calls to action, and `tiktok_get_video_info` for video lengths.
6. Call `tiktok_get_spark_ads` and `tiktok_get_ads` to tell Spark Ads apart, and compare them with regular ads.
7. Draw the patterns: hooks, lengths, formats and messages that win, and the gaps.

## Output

1. **Summary**: the creatives that carry the results and the main risk.
2. **Keep**: best ads with their figures.
3. **Refresh**: tiring ads with the fatigue evidence.
4. **Stop or rework**: weak ads with their figures.
5. **Patterns**: what wins in hook, length, format and message.
6. **Brief**: three to five creative ideas for the team, drawn from the patterns.

## Rules

- Name the period, the metric and the currency.
- Never judge an ad set aside for low volume.
- A metric TikTok did not return is absent, not zero.

## Examples

- "Which TikTok videos work best, and which are tiring?"
- "Fais la revue des créas TikTok du mois et propose un brief."
- "Les Spark Ads font-elles mieux que nos publicités classiques ?"
