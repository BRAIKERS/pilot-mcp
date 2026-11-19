---
name: openai-ads-conversion-tracking-check
description: "Checks OpenAI Ads conversion tracking through the Pilot MCP server: conversion pixels, the events they actually receive, the conversion event settings with their type and attribution window, the campaigns that bid on conversions, and the conversions attributed to them. Use when OpenAI Ads conversions look wrong, before switching a campaign to conversion bidding, or after a site change. Triggers: OpenAI Ads pixel check, ChatGPT ads conversions, OpenAI conversion events. Déclencheurs : pixel OpenAI Ads, conversions des publicités ChatGPT, suivi des conversions OpenAI Ads."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with an OpenAI Ads account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "openai_ads_openai_ads_get_ad_account openai_ads_openai_ads_list_conversion_pixels openai_ads_openai_ads_list_conversion_events openai_ads_openai_ads_list_conversion_event_settings openai_ads_openai_ads_list_campaigns openai_ads_openai_ads_conversion_insights"
  pilot-mcp-read-only: "true"
---

# OpenAI Ads conversion tracking check

Tell the user whether OpenAI Ads receives and attributes their conversions, and which campaigns bid on weak data. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the OpenAI Ads account.
- **Optional MCP servers:** an analytics or tag manager server to check the site side. None is needed.
- **Read-only:** never create a pixel or a conversion setting. Fixes are for a person to apply.

## Tools

- `openai_ads_openai_ads_get_ad_account`: account time zone and status.
- `openai_ads_openai_ads_list_conversion_pixels`: the web pixels of the account.
- `openai_ads_openai_ads_list_conversion_events`: the events a pixel has received.
- `openai_ads_openai_ads_list_conversion_event_settings`: what counts as a conversion and over which window.
- `openai_ads_openai_ads_list_campaigns`: campaigns, their bidding type and their conversion settings.
- `openai_ads_openai_ads_conversion_insights`: conversions attributed per campaign, with detail per event type.

## Steps

1. Call `openai_ads_openai_ads_get_ad_account` for the time zone.
2. Call `openai_ads_openai_ads_list_conversion_pixels`. No pixel means no conversion can be counted.
3. For each pixel, call `openai_ads_openai_ads_list_conversion_events` with its `pid`: does it receive events, which ones, and when was the last one. A pixel with no recent event is the first suspect, before any targeting question.
4. Call `openai_ads_openai_ads_list_conversion_event_settings`: event type, source pixels, attribution window. Flag settings tied to a pixel that receives nothing, and duplicated settings for the same event.
5. Call `openai_ads_openai_ads_list_campaigns`: campaigns with `bidding_type` conversions and their `conversion_event_setting_ids`.
6. Call `openai_ads_openai_ads_conversion_insights` over the last 30 days with `aggregation_level: "campaign"`, `include: ["attributed_events"]` and the windows of the settings, to see what each campaign actually records, by click and by view.

## Output

1. **Verdict**: tracking reliable, partial or broken, in one sentence.
2. **Pixels**: a table with pixel, events received, last event.
3. **Settings**: event type, window, source, campaigns using it.
4. **Campaigns at risk**: conversion bidding on a silent or missing setting.
5. **Fixes**: in order, for a person to apply.

## Rules

- No conversion is not proof of a broken pixel: low volume can explain it. Say which is likely and why.
- Keep click and view conversions apart.
- Name the attribution windows when comparing with another source.

## Examples

- "Is OpenAI Ads tracking our purchases?"
- "Vérifie le pixel et les conversions OpenAI Ads."
- "Peut-on passer la campagne ChatGPT en enchères à la conversion ?"
