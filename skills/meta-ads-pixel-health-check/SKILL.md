---
name: meta-ads-pixel-health-check
description: "Checks the health of Meta pixels through the Pilot MCP server: whether each pixel still fires, which events it receives day by day and from which source (browser or server), its approximate match rate and automatic matching settings, and whether the events that ad sets optimize for actually arrive, then flags gaps and fixes for a person to apply. Use when the user doubts Meta conversions, after a site change, or before scaling a conversion campaign. Triggers: Meta pixel check, pixel not firing, Conversions API, event match quality. Déclencheurs : vérifier le pixel Meta, pixel qui ne remonte plus, API Conversions, qualité de correspondance des événements."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Meta ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "meta_list_accounts meta_list_pixels meta_get_pixel meta_get_pixel_stats meta_list_adsets meta_get_account_insights"
  pilot-mcp-read-only: "true"
---

# Meta Ads pixel health check

Tell the user whether each Meta pixel works, which events it receives and from where, and whether the events used for optimization are there. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** none. For a cross-platform comparison, the conversion-tracking-check skill sets Meta against Google Ads and GA4.
- **Read-only:** never create or change a pixel or an event. Fixes are for a person or a developer.

## Tools

- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_list_pixels`: pixels of the account; `champs` gives last fired time, availability, approximate match rate, first-party cookie status and automatic matching.
- `meta_get_pixel`: one pixel's details and owner account.
- `meta_get_pixel_stats`: hourly event counts of a pixel, by event, by source or by other aggregations.
- `meta_list_adsets`: ad sets with their optimization goal through `champs`.
- `meta_get_account_insights`: conversions reported by Meta per day.

## Steps

1. Call `meta_list_accounts` and pick the account.
2. Call `meta_list_pixels` with `champs: ["last_fired_time", "is_unavailable", "match_rate_approx", "first_party_cookie_status", "automatic_matching_fields", "enable_automatic_matching"]`. A pixel that has not fired for more than a day on a live site is broken.
3. For each pixel in use, call `meta_get_pixel` for its details.
4. Call `meta_get_pixel_stats` with `start_time` and `end_time` over the last 14 days and `aggregation: "event"`: list events and their daily counts, and spot days with none.
5. Call `meta_get_pixel_stats` again with `aggregation: "event_source"`: events only from the browser mean no server-side sending (Conversions API), which loses part of the conversions.
6. Call `meta_list_adsets` with `champs: ["optimization_goal", "effective_status"]`: check that each conversion ad set optimizes on an event the pixel receives in volume.
7. Call `meta_get_account_insights` with `date_preset: "last_14d"` and `time_increment: 1`: compare reported purchases or leads with the pixel's event counts. A large gap points to attribution windows or a broken event.

## Output

1. **Verdict per pixel**: firing or not, last event, match rate, server-side sending or not.
2. **Events**: a table with event, daily count over 14 days, sources, and gaps.
3. **Optimization**: ad sets whose optimization event is missing or rare.
4. **Fixes**: in order, for a person or a developer.

## Rules

- Meta returns hourly slices only for hours with events: an hour missing is zero events, a whole day missing is a gap to report.
- Match rate is approximate: present it as such.
- Never ask for or display personal data sent with events.
- Name the dates and the time zone.

## Examples

- "Is our Meta pixel still working?"
- "Le pixel reçoit-il bien les achats, et par l'API Conversions ?"
- "Depuis la refonte du site, les conversions Meta ont chuté : le pixel est-il cassé ?"
