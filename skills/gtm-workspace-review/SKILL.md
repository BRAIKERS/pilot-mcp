---
name: gtm-workspace-review
description: "Reviews the pending changes of Google Tag Manager workspaces in read-only mode through the Pilot MCP server: lists the workspaces of a container, compares each workspace's tags, triggers and variables with the live version to show what was added, changed or removed, flags risky changes and workspaces left behind the latest version, and gives a checklist to test in preview before someone publishes. Use when the user asks what is waiting to be published in GTM, before a publication, or to tidy old workspaces. Triggers: GTM workspace review, pending changes Tag Manager, before publishing GTM. Déclencheurs : revue des espaces de travail GTM, changements en attente Tag Manager, avant de publier GTM."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Tag Manager account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_tag_manager_list_accounts google_tag_manager_list_containers google_tag_manager_list_workspaces google_tag_manager_get_workspace google_tag_manager_get_live_version google_tag_manager_get_latest_version google_tag_manager_list_versions google_tag_manager_list_tags google_tag_manager_list_triggers google_tag_manager_list_variables"
  pilot-mcp-read-only: "true"
---

# GTM workspace review

Show what is waiting in each Tag Manager workspace, what it would change once published, and what to test first. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Tag Manager account.
- **Optional MCP servers:** a browser server to run the preview checks. None is needed.
- **Read-only:** never change, sync, preview or publish a workspace. Publishing stays a person's decision in Tag Manager.

## Tools

- `google_tag_manager_list_accounts`: the Tag Manager accounts reachable.
- `google_tag_manager_list_containers`: containers of an account.
- `google_tag_manager_list_workspaces`: workspaces of a container.
- `google_tag_manager_get_workspace`: one workspace's name and description.
- `google_tag_manager_get_live_version`: the published version.
- `google_tag_manager_get_latest_version`: the latest version created, published or not.
- `google_tag_manager_list_versions`: versions with their dates.
- `google_tag_manager_list_tags`: tags of a workspace.
- `google_tag_manager_list_triggers`: triggers of a workspace.
- `google_tag_manager_list_variables`: variables of a workspace.

## Steps

1. Call `google_tag_manager_list_accounts` and `google_tag_manager_list_containers`, and pick the container.
2. Call `google_tag_manager_get_live_version`, `google_tag_manager_get_latest_version` and `google_tag_manager_list_versions`: a latest version newer than the live one was created but not published.
3. Call `google_tag_manager_list_workspaces`, then `google_tag_manager_get_workspace` for each.
4. For each workspace, call `google_tag_manager_list_tags`, `google_tag_manager_list_triggers` and `google_tag_manager_list_variables`, and compare each item with the live version by ID and fingerprint: added, changed (name, type, parameters, triggers, paused state) or removed.
5. Flag risky changes: a conversion tag whose trigger widens, a tag unpaused, a removed trigger still referenced, custom HTML added, a variable whose value source changed.
6. Flag stale workspaces: created long ago, never published, or built on an older version than the live one; they will meet conflicts at publication.
7. Write the test checklist: for each change, the page or action to reproduce in preview and what should fire.

## Output

1. **Container state**: live version and date, latest version, number of workspaces.
2. **Per workspace**: a table of added, changed and removed items, with old and new values.
3. **Risks**: changes that could break or inflate measurement.
4. **Stale workspaces**: to bring up to date or abandon, for a person to decide.
5. **Preview checklist**: what to test before publishing.

## Rules

- What runs on the site is the live version.
- Never say a workspace is safe to publish: say what was checked and what remains to test.
- Quote names and IDs exactly as returned.

## Examples

- "What is waiting to be published in our Tag Manager container?"
- "Revois l'espace de travail GTM avant que je le publie."
- "Quels espaces de travail GTM sont abandonnés ?"
