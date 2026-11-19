---
name: pbs-media-manager-content-availability-review
description: "Reviews the PBS Media Manager catalog of a show in read-only mode through the Pilot MCP server: seasons, episodes, specials and collections, the full-length videos, clips and previews behind them, and their availability windows by audience (public, all members, station members), platform and country, then lists what is missing, expiring or not yet live. Use when a station or producer needs to know what viewers can watch, what expires soon, or what lacks a preview or captions. Triggers: PBS content availability, PBS episodes expiring, PBS Media Manager review. Déclencheurs : disponibilité des contenus PBS, épisodes PBS qui expirent, revue PBS Media Manager."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a PBS Media Manager key attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "pbs_media_manager_search_shows pbs_media_manager_get_show pbs_media_manager_list_seasons pbs_media_manager_list_episodes pbs_media_manager_list_specials pbs_media_manager_list_collections pbs_media_manager_list_assets pbs_media_manager_get_asset"
  pilot-mcp-read-only: "true"
---

# PBS Media Manager content availability review

Tell the user what each show offers on PBS platforms, for which audience and until when, and what is missing or about to expire. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the PBS Media Manager key.
- **Optional MCP servers:** a calendar server to plan promotion around availability dates. None is needed.
- **Read-only:** PBS Media Manager is served read-only by Pilot; nothing is changed.

## Tools

- `pbs_media_manager_search_shows`: finds shows by title.
- `pbs_media_manager_get_show`: one show with its latest clips and previews, seasons and recent specials.
- `pbs_media_manager_list_seasons`: the seasons of a show.
- `pbs_media_manager_list_episodes`: the episodes of a season, with premiere and encore dates.
- `pbs_media_manager_list_specials`: specials outside any season.
- `pbs_media_manager_list_collections`: curated collections of a show.
- `pbs_media_manager_list_assets`: videos, clips and previews with type, duration and availability by audience, platform and country.
- `pbs_media_manager_get_asset`: one asset with its files, captions, images and availability windows.

## Steps

1. Call `pbs_media_manager_search_shows` with the title the user gives, and confirm the show. Call `pbs_media_manager_get_show`.
2. Call `pbs_media_manager_list_seasons`, then `pbs_media_manager_list_episodes` for the seasons in scope (the latest by default), and `pbs_media_manager_list_specials`.
3. Call `pbs_media_manager_list_assets` with `show_id`, and with `available` set to the audience that matters (public, all_members, station_members), `platform_slug` and `country` when the user names them.
4. For each episode, find its full-length video and its preview. Call `pbs_media_manager_get_asset` on assets whose window ends soon, starts in the future, or lacks captions or images.
5. Call `pbs_media_manager_list_collections` to check that collections point to available content.
6. Classify: live now for the public; members only; expiring within 30 days; scheduled but not live; missing a full-length video, a preview or captions.

## Output

1. **Summary**: how many episodes are watchable, by whom, and the main gap.
2. **Availability table**: episode, type, audience, start, end, platforms.
3. **Expiring soon**: assets whose window ends within 30 days, with the date.
4. **Gaps**: episodes without full-length video, preview or captions.
5. **Next steps**: what the station or producer should check or request.

## Rules

- Dates are UTC: say so, and convert when the user gives a time zone.
- A field PBS did not return is absent, not empty by choice.
- Lists are paginated: read every page before counting.

## Examples

- "Which episodes of our show are available to the public right now?"
- "Quels épisodes PBS expirent dans le mois ?"
- "List the assets of this show that have no captions."
