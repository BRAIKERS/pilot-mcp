---
name: tiktok-ads-pixel-and-events-check
description: "Checks TikTok pixels and their events through the Pilot MCP server: which pixels exist, which events each one defines, how many events each pixel received day by day, whether the events that ad groups optimize for actually arrive, and whether conversions reported in campaigns match the pixel counts, then flags broken or missing events for a person or a developer to fix. Use when the user doubts TikTok conversions, after a site change, or before scaling a conversion campaign. Triggers: TikTok pixel check, TikTok events not firing, TikTok conversion tracking. Déclencheurs : vérifier le pixel TikTok, événements TikTok, suivi des conversions TikTok, pixel TikTok cassé."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a TikTok Ads advertiser attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "tiktok_get_advertiser_info tiktok_get_pixels tiktok_get_pixel_events tiktok_get_pixel_overview tiktok_get_ad_groups tiktok_get_daily_performance_trend"
  pilot-mcp-read-only: "true"
---

# TikTok Ads pixel and events check

Tell the user whether each TikTok pixel works, which events it receives, and whether the events used for optimization are there. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the advertiser.
- **Optional MCP servers:** none.
- **Read-only:** never create, rename or delete a pixel or an event. Fixes are for a person or a developer.

## Tools

- `tiktok_get_advertiser_info`: the advertiser attached.
- `tiktok_get_pixels`: the pixels of the advertiser, with their IDs and codes.
- `tiktok_get_pixel_events`: the events defined on a pixel, by pixel code.
- `tiktok_get_pixel_overview`: event statistics of one or more pixels over a period.
- `tiktok_get_ad_groups`: ad groups with their optimization goal and pixel.
- `tiktok_get_daily_performance_trend`: conversions reported per day.

## Steps

1. Call `tiktok_get_advertiser_info`, then `tiktok_get_pixels`.
2. For each pixel, call `tiktok_get_pixel_events` with its `pixel_code` to list the events it defines (for example `CompletePayment`, `SubmitForm`, `AddToCart`, `ViewContent`).
3. Call `tiktok_get_pixel_overview` with the `pixel_ids` and the last 14 days: note events with no count, days without events, and sudden drops.
4. Call `tiktok_get_ad_groups` and check that each conversion ad group optimizes on an event that arrives in volume.
5. Call `tiktok_get_daily_performance_trend` over the same 14 days and compare reported conversions with pixel counts. A large gap points to attribution windows, a duplicated event or a broken one.
6. Check the funnel: view, add to cart and purchase should decrease in that order; a purchase count above add to cart suggests a misfiring event.

## Output

1. **Verdict per pixel**: active or not, events defined, last activity.
2. **Events**: a table with event, daily counts over 14 days, and gaps.
3. **Optimization**: ad groups whose optimization event is missing or rare.
4. **Fixes**: in order, for a person or a developer.

## Rules

- A day with no event on a live site is a gap to report, never a zero result.
- Never ask for or display personal data sent with events.
- Name the dates and the time zone.

## Examples

- "Is our TikTok pixel tracking purchases?"
- "Les événements TikTok remontent-ils depuis la mise à jour du site ?"
- "Sur quel événement optimisent nos groupes d'annonces TikTok, et arrive-t-il ?"
