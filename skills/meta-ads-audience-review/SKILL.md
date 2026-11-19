---
name: meta-ads-audience-review
description: "Reviews Meta Ads audiences and targeting through the Pilot MCP server: custom and lookalike audiences with their size and state, saved audiences, the targeting of each active ad set, its estimated audience size, and how ad sets with different audiences perform, then flags audiences too small, too broad, overlapping or not ready. Use when the user asks about Meta audiences, targeting, lookalikes or retargeting. Triggers: Meta audiences review, Facebook targeting, lookalike audiences, retargeting audiences. Déclencheurs : audiences Meta, ciblage Facebook, audiences similaires, audiences de reciblage, revue du ciblage."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Meta ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "meta_list_accounts meta_list_custom_audiences meta_get_custom_audience meta_list_saved_audiences meta_list_adsets meta_get_adset meta_get_delivery_estimate meta_get_account_insights"
  pilot-mcp-read-only: "true"
---

# Meta Ads audience review

Show which audiences the account has, how each ad set targets, how big each audience is, and which targeting works. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** none.
- **Read-only:** never create, change or delete an audience or a targeting. Proposals are for a person to apply.

## Tools

- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_list_custom_audiences`: custom and lookalike audiences, with subtype and approximate size bounds.
- `meta_get_custom_audience`: one audience with its operation status.
- `meta_list_saved_audiences`: saved audiences and their targeting.
- `meta_list_adsets`: ad sets with their targeting.
- `meta_get_adset`: one ad set's budget and full targeting.
- `meta_get_delivery_estimate`: estimated monthly audience of an existing ad set.
- `meta_get_account_insights`: results by ad set.

## Steps

1. Call `meta_list_accounts` and pick the account.
2. Call `meta_list_custom_audiences`. For each audience in use or that looks broken, call `meta_get_custom_audience` and read its operation status: an audience that is not ready, too small or no longer filling is a finding.
3. Call `meta_list_saved_audiences`.
4. Call `meta_list_adsets` and keep active ad sets; call `meta_get_adset` for those that spend most to read their full targeting (locations, ages, custom audiences included and excluded, interests, Advantage+ audience).
5. Call `meta_get_delivery_estimate` with `adset_id` for each of them.
6. Call `meta_get_account_insights` with `level: "adset"` and `date_preset: "last_30d"`.
7. Look for: prospecting ad sets that do not exclude customers or recent site visitors, two ad sets targeting the same people, retargeting audiences too small to deliver, lookalikes built on a weak source, targeting so narrow that frequency climbs.

## Output

1. **Audiences**: a table with name, type, size bounds, state, and the ad sets using it.
2. **Targeting by ad set**: who each active ad set reaches, its estimated size and its results.
3. **Issues**: overlaps, missing exclusions, audiences too small or not ready.
4. **Proposals**: audiences to build, exclude or widen, for a person to apply.

## Rules

- Audience sizes are Meta's bounds: give both bounds, never a single exact number.
- Customer lists hold personal data: never ask for or display their members.
- Do not judge an ad set with too little volume.
- Name the period and the currency.

## Examples

- "Review our Meta audiences and targeting."
- "Nos audiences de reciblage Meta sont-elles assez grandes ?"
- "Les ensembles de prospection excluent-ils bien les clients ?"
