---
name: gtm-tag-and-trigger-setup
description: "Builds Google Tag Manager tags, triggers and variables through the Pilot MCP server, after explicit approval of each write: reads the container and its existing items, drafts the tracking plan, then creates in a dedicated workspace the variables, triggers and tags needed (for example a GA4 event or a Google Ads conversion on a form submit), tags paused, one write at a time, reads each back and gives a preview link. It never publishes: a person tests and publishes. Use when the user asks to add a tag, track an event or set up a conversion in GTM. Triggers: create GTM tag, add trigger Tag Manager, track event with GTM. Déclencheurs : créer une balise GTM, ajouter un déclencheur Tag Manager, suivre un événement avec GTM."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Tag Manager account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_tag_manager_list_accounts google_tag_manager_list_containers google_tag_manager_list_workspaces google_tag_manager_list_tags google_tag_manager_list_triggers google_tag_manager_list_variables google_tag_manager_create_workspace google_tag_manager_create_variable google_tag_manager_create_trigger google_tag_manager_create_tag google_tag_manager_get_tag google_tag_manager_get_trigger google_tag_manager_quick_preview_workspace"
  pilot-mcp-read-only: "false"
---

# GTM tag and trigger setup

Add the tags, triggers and variables a tracking need calls for, in a separate workspace, paused, for a person to test and publish. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Tag Manager account.
- **Optional MCP servers:** a browser server to test in preview. None is needed.
- **Writes:** the write tools require the paid Pilot MCP plan and a workspace role that can write. If a write tool is not served, say so and return the tracking plan for a person to build in Tag Manager. This skill never publishes a version.

## Tools

- `google_tag_manager_list_accounts`: the Tag Manager accounts reachable.
- `google_tag_manager_list_containers`: containers of an account.
- `google_tag_manager_list_workspaces`: workspaces of a container.
- `google_tag_manager_list_tags`: tags of a workspace, to avoid duplicates.
- `google_tag_manager_list_triggers`: triggers of a workspace, to reuse them.
- `google_tag_manager_list_variables`: variables of a workspace, to reuse them.
- `google_tag_manager_create_workspace`: creates a workspace for the change (write).
- `google_tag_manager_create_variable`: creates a variable, for example a data layer value (write).
- `google_tag_manager_create_trigger`: creates a trigger (write).
- `google_tag_manager_create_tag`: creates a tag, paused (write).
- `google_tag_manager_get_tag`: reads back a tag created.
- `google_tag_manager_get_trigger`: reads back a trigger created.
- `google_tag_manager_quick_preview_workspace`: generates a preview link, without publishing (write).

## Confirmation

Every write changes the container that the site loads once someone publishes. For each one:

1. Read the current state just before writing: the workspace's tags, triggers and variables, so that nothing is duplicated.
2. Show the person the exact item to create: account, container and workspace IDs, name, type, parameters and triggers.
3. Wait for an explicit yes for this write. Approval of the plan does not replace a yes per write, unless the person says so for the plan.
4. Make one write at a time, in a workspace dedicated to the change.
5. Create every tag paused (`paused: true`). Never unpause, publish or create a version in this skill.
6. Never delete or revert anything.
7. After each write, read the item back and show it.
8. Stop at the first write that is refused or fails, report what exists so far, and do not try a workaround.

## Steps

1. Ask for the need: what action to track, on which pages, sent where (GA4 event name and parameters, Google Ads conversion ID and label), and how the site exposes it (data layer event, form, click, page).
2. Call `google_tag_manager_list_accounts`, `google_tag_manager_list_containers` and `google_tag_manager_list_workspaces`. Read the default workspace with `google_tag_manager_list_tags`, `google_tag_manager_list_triggers` and `google_tag_manager_list_variables`: reuse a trigger or variable that exists, and check that no tag already sends the same event.
3. Draft the plan: variables (for example `v` data layer variables for the value and transaction ID), the trigger (for example `customEvent` on the data layer event, or `formSubmission` with a filter on the page), and the tag (`gaawe` for a GA4 event with its measurement ID or Google tag, `awct` for a Google Ads conversion). Get it approved.
4. After the yes, create the workspace with `google_tag_manager_create_workspace` and a name that says the change.
5. Create, one approved write at a time: `google_tag_manager_create_variable`, then `google_tag_manager_create_trigger`, then `google_tag_manager_create_tag` with `firingTriggerId` and `paused: true`.
6. Read back with `google_tag_manager_get_trigger` and `google_tag_manager_get_tag`.
7. After a yes, call `google_tag_manager_quick_preview_workspace` and give the link with the test steps: unpause in the workspace, preview, perform the action, check the tag fires once and the event arrives in GA4 or Google Ads.

## Output

1. **Plan**: variables, trigger and tag, with every setting.
2. **Built**: each item created, with its ID, in the named workspace.
3. **To test and publish**: preview link, test steps, and the reminder that a person publishes.
4. **Not done**: refused or skipped writes.

## Rules

- Never send personal data (email, phone, name) in tag parameters.
- A conversion tag fires on the confirmation step only, never on all pages.
- Respect the site's consent settings: say so when the container uses consent mode.

## Examples

- "Add a GA4 event tag for form submissions, paused, in a new workspace."
- "Crée dans GTM la conversion Google Ads sur la page de confirmation, en pause, sans publier."
- "Prépare les balises pour suivre les clics sur le bouton d'appel."
