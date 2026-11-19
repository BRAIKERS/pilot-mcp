---
name: pbs-media-manager-catalog-changes-review
description: "Reviews what changed in the PBS Media Manager catalog over the last 30 days through the Pilot MCP server: assets, shows, franchises, seasons, episodes, specials and collections created, updated or deleted, with the date and the fields changed, filtered by show, type or action, then explains what it means for viewers. Use when a station or producer wants to know what was published, changed or removed recently. Triggers: PBS changelog, what changed on PBS, new PBS episodes this week. Déclencheurs : journal des changements PBS, ce qui a changé sur PBS, nouveaux épisodes PBS, contenus PBS supprimés."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a PBS Media Manager key attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "pbs_media_manager_get_changelog pbs_media_manager_list_franchises pbs_media_manager_list_shows pbs_media_manager_search_shows pbs_media_manager_get_show pbs_media_manager_search_episodes pbs_media_manager_get_asset"
  pilot-mcp-read-only: "true"
---

# PBS Media Manager catalog changes review

Tell the user what was added, changed or removed in PBS Media Manager over a recent period, and what it changes for viewers. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the PBS Media Manager key.
- **Optional MCP servers:** a team messaging server to share the digest. None is needed.
- **Read-only:** PBS Media Manager is served read-only by Pilot; nothing is changed.

## Tools

- `pbs_media_manager_get_changelog`: creations, updates and deletions of the last 30 days, with date and changed fields.
- `pbs_media_manager_list_franchises`: franchises visible to the workspace key.
- `pbs_media_manager_list_shows`: shows, filterable by franchise or genre.
- `pbs_media_manager_search_shows`: finds a show by title.
- `pbs_media_manager_get_show`: one show with its seasons and latest clips.
- `pbs_media_manager_search_episodes`: finds episodes by title.
- `pbs_media_manager_get_asset`: one asset with its availability windows.

## Steps

1. Set the scope: the whole catalog visible to the key, a franchise (`pbs_media_manager_list_franchises`, then `pbs_media_manager_list_shows`), or one show (`pbs_media_manager_search_shows`).
2. Set the period with `since`, within the last 30 days (UTC). Older history is not available: say so if asked.
3. Call `pbs_media_manager_get_changelog` with `since`, and with `id` set to the show IDs and `with_assets: true` when the scope is a show, so asset activity is included. Filter by `type` or `action` when the user asks for one kind of change. Read every page.
4. Group the entries: new episodes and assets, updated availability windows, metadata edits (titles, descriptions, images), deletions.
5. For assets whose availability changed, call `pbs_media_manager_get_asset` to give the new window. For episodes named in the log, use `pbs_media_manager_search_episodes` or `pbs_media_manager_get_show` to give their context.
6. Flag what matters for viewers: content removed, windows shortened, new episodes without a preview.

## Output

1. **Summary**: the period, the scope and the three changes that matter most.
2. **New**: shows, seasons, episodes and assets created, with dates.
3. **Changed**: updates grouped by kind, with the fields changed.
4. **Removed**: deletions, with what they were.
5. **To check**: changes that may need action from the station or producer.

## Rules

- Dates are UTC: say so.
- The changelog only covers the last 30 days.
- Quote changed fields as given; do not infer an old value the log does not show.

## Examples

- "What changed in PBS Media Manager for our shows this week?"
- "Quels contenus PBS ont été supprimés ce mois-ci ?"
- "Liste les nouveaux épisodes publiés depuis lundi."
