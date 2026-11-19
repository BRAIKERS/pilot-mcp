---
name: gtm-container-audit
description: "Audits a Google Tag Manager container in read-only mode through the Pilot MCP server: the live version and when it was published, every tag with its type, firing and blocking triggers and paused state, triggers and variables that nothing uses, tags that fire on all pages without need, duplicated measurement tags, custom HTML tags, folders and naming, then returns a prioritized cleanup and risk list. Use when the user asks to audit or clean up Tag Manager, after taking over a site, or when tracking behaves oddly. Triggers: GTM audit, Tag Manager audit, container cleanup, tag audit. Déclencheurs : audit Tag Manager, audit GTM, nettoyage du conteneur, revue des balises."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Tag Manager account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_tag_manager_list_accounts google_tag_manager_list_containers google_tag_manager_get_container google_tag_manager_get_live_version google_tag_manager_list_versions google_tag_manager_list_workspaces google_tag_manager_list_tags google_tag_manager_list_triggers google_tag_manager_list_variables google_tag_manager_list_built_in_variables google_tag_manager_list_folders google_tag_manager_list_templates"
  pilot-mcp-read-only: "true"
---

# GTM container audit

Tell the user what the Tag Manager container really runs, what is dead weight, and what puts measurement or the site at risk. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Tag Manager account.
- **Optional MCP servers:** a document server to keep the audit. None is needed.
- **Read-only:** never create, change, pause, delete or publish anything in Tag Manager. The cleanup list is for a person to apply.

## Tools

- `google_tag_manager_list_accounts`: the Tag Manager accounts reachable.
- `google_tag_manager_list_containers`: containers of an account.
- `google_tag_manager_get_container`: one container's public ID, usage context and domains.
- `google_tag_manager_get_live_version`: the published version, with its tags, triggers and variables.
- `google_tag_manager_list_versions`: the versions of the container, to date the last publication.
- `google_tag_manager_list_workspaces`: workspaces with changes not yet published.
- `google_tag_manager_list_tags`: tags of a workspace.
- `google_tag_manager_list_triggers`: triggers of a workspace.
- `google_tag_manager_list_variables`: custom variables of a workspace.
- `google_tag_manager_list_built_in_variables`: built-in variables enabled.
- `google_tag_manager_list_folders`: folders of a workspace.
- `google_tag_manager_list_templates`: custom templates of a workspace.

## Steps

1. Call `google_tag_manager_list_accounts`, then `google_tag_manager_list_containers` with `accountId`, and pick the container (ask if several). Call `google_tag_manager_get_container`.
2. Call `google_tag_manager_get_live_version`: this is what the site runs. Call `google_tag_manager_list_versions` to see when it was published and how often the container changes.
3. Call `google_tag_manager_list_workspaces`. For the default workspace, call `google_tag_manager_list_tags`, `google_tag_manager_list_triggers`, `google_tag_manager_list_variables`, `google_tag_manager_list_built_in_variables`, `google_tag_manager_list_folders` and `google_tag_manager_list_templates`.
4. Build the map: for each tag, its type (`gaawe` GA4 event, `googtag` Google tag, `awct` Google Ads conversion, `html` custom HTML, and so on), its firing and blocking triggers, and whether it is paused.
5. Flag:
   - triggers and variables that no tag or trigger uses;
   - paused tags kept for months;
   - two tags sending the same event to the same destination (double counting);
   - conversion tags firing on all pages instead of a confirmation step;
   - custom HTML tags (security and speed risk), and what they load;
   - tags without consent conditions where consent mode is expected;
   - names that do not say platform, type and event, and items outside folders.
6. Compare the live version with the workspace: changes waiting unpublished are listed apart.

## Output

1. **Summary**: container, live version and date, and the top three risks.
2. **Map**: a table of tags with type, triggers, paused state and folder.
3. **Risks**: double counting, broad conversion triggers, custom HTML, consent gaps.
4. **Cleanup**: unused triggers and variables, old paused tags, naming fixes.
5. **Pending**: workspace changes not yet published.
6. **Next steps**: in order, for a person to apply and publish after testing in preview.

## Rules

- What runs on the site is the live version, not the workspace.
- Quote names and IDs exactly as returned.
- Never recommend deleting a tag without saying what it measures and who uses that data.

## Examples

- "Audit our Tag Manager container."
- "Fais l'audit GTM : balises en double, déclencheurs inutiles, HTML personnalisé."
- "Quelles balises tournent vraiment sur le site ?"
