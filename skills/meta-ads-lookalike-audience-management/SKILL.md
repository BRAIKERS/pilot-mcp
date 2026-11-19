---
name: meta-ads-lookalike-audience-management
description: "Builds and maintains Meta lookalike audiences through the Pilot MCP server, after explicit approval of each write: reads the custom audiences that can serve as sources with their size and state, proposes lookalikes by country and size, creates one lookalike at a time from an approved source, renames an audience or changes its retention, and reads the result back. Use when the user asks to create a lookalike audience, find people similar to customers, or tidy audience names. Triggers: create lookalike audience, similar audience Meta, lookalike from customers. Déclencheurs : créer une audience similaire, audience similaire Meta, lookalike à partir des clients."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Meta ad account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "meta_list_accounts meta_list_custom_audiences meta_get_custom_audience meta_list_adsets meta_create_lookalike_audience meta_update_custom_audience"
  pilot-mcp-read-only: "false"
---

# Meta Ads lookalike audience management

Create lookalike audiences from the right sources, and keep audience names and retention tidy, one approved write at a time. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** none.
- **Writes:** the write tools require the paid Pilot MCP plan and a workspace role that can write. If a write tool is not served, say so and return the audience settings for a person to create in Meta Ads Manager.

## Tools

- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_list_custom_audiences`: custom and lookalike audiences with subtype and size bounds.
- `meta_get_custom_audience`: one audience with its operation status.
- `meta_list_adsets`: ad sets and their targeting, to see which audiences are in use.
- `meta_create_lookalike_audience`: creates a lookalike from a source audience (write).
- `meta_update_custom_audience`: renames an audience, or changes its description or retention (write).

## Confirmation

Every write changes the account. For each one:

1. Read the current state just before writing: the source audience with `meta_get_custom_audience`, and existing lookalikes of that source.
2. Show the person the exact write: account, source audience (name, ID, size), country, ratio, name of the new audience; or, for an update, the current and proposed values.
3. Wait for an explicit yes for this write. A yes for one audience does not cover the next.
4. Make one write at a time.
5. A lookalike targets no one until an ad set uses it: creation changes no delivery. Never add it to an ad set in this skill.
6. Never delete an audience.
7. After the write, read the audience back and show it.
8. Stop at the first write that is refused or fails, report it, and do not try a workaround.

## Steps

1. Call `meta_list_accounts` and pick the account.
2. Call `meta_list_custom_audiences`. Good sources are buyers, high-value customers or leads, large enough for Meta to model (Meta asks for at least about 100 people in one country; a few thousand works better).
3. Call `meta_get_custom_audience` on each candidate source to check it is ready.
4. Call `meta_list_adsets` to see which lookalikes are already used, and avoid duplicates.
5. Propose: per source, a country and a ratio (0.01 for the closest 1%, up to 0.20), and a clear name such as source, country and ratio.
6. After approval: `meta_create_lookalike_audience` with `source_audience_id`, `name` and `lookalike_spec` holding `country` and `ratio`.
7. To tidy: `meta_update_custom_audience` with `audience_id` and `updates` holding `name`, `description` or `retention_days`.
8. Read back with `meta_get_custom_audience`. A new lookalike can take several hours to fill: say so.

## Output

1. **Sources**: candidate audiences with size, state and verdict.
2. **Proposed lookalikes**: source, country, ratio, name.
3. **Writes done**: each audience created or updated, with before and after.
4. **Not done**: refused or skipped writes.

## Rules

- Customer lists hold personal data: never ask for or display their members.
- Audience sizes are Meta's bounds.
- A smaller ratio is closer to the source and smaller; a larger ratio reaches more people and fewer like them.

## Examples

- "Create a 1% lookalike of our buyers in France."
- "Crée des audiences similaires à 1 %, 3 % et 5 % à partir des clients, pour la Belgique."
- "Renomme les audiences similaires selon notre convention."
