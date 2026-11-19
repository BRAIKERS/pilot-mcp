---
name: snapchat-ads-pixel-and-audience-review
description: "Reviews Snapchat Ads measurement and targeting through the Pilot MCP server: Snap pixels and their state, which ad squads optimize for conversions, audience segments with their size and retention, the targeting of each ad squad and its estimated reach. Use when Snapchat conversions look wrong, before a conversion campaign, or to check who the ads target. Triggers: Snap pixel check, Snapchat audiences, Snapchat targeting review. Déclencheurs : pixel Snapchat, audiences Snapchat, ciblage Snapchat, vérifier le suivi Snap."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Snapchat ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "search snapchat_get_campaign snapchat_get_ad_account snapchat_list_pixels snapchat_get_pixel snapchat_list_audiences snapchat_get_audience snapchat_list_ad_squads snapchat_get_ad_squad snapchat_get_audience_size"
  pilot-mcp-read-only: "true"
---

# Snapchat Ads pixel and audience review

Tell the user whether Snapchat can measure conversions on their site and whether ad squads target the right people. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Snapchat ad account.
- **Optional MCP servers:** a tag manager server to check that the pixel is installed on the site. None is needed.
- **Read-only:** never create a pixel or an audience, and never change an ad squad.

## Tools

- `search`: finds campaigns of the workspace by name.
- `snapchat_get_campaign`: one campaign, including its ad account ID.
- `snapchat_get_ad_account`: the account settings.
- `snapchat_list_pixels`: the pixels of the account.
- `snapchat_get_pixel`: one pixel in detail, including its status.
- `snapchat_list_audiences`: audience segments with source, size and retention.
- `snapchat_get_audience`: one segment in detail.
- `snapchat_list_ad_squads`: ad squads with optimization goal and targeting.
- `snapchat_get_ad_squad`: one ad squad in full.
- `snapchat_get_audience_size`: the estimated reach of a targeting spec.

## Steps

1. Get the ad account ID from the user, or from `snapchat_get_campaign` on a campaign found with `search`. Call `snapchat_get_ad_account`.
2. Call `snapchat_list_pixels`, then `snapchat_get_pixel` on each. Note the status and anything that suggests the pixel is not firing.
3. Call `snapchat_list_ad_squads`. List the ad squads that optimize for a conversion goal: they depend on the pixel. An ad squad optimizing for purchases with no working pixel is the first finding.
4. Call `snapchat_list_audiences`, then `snapchat_get_audience` on segments that look small, pending or near the end of their retention.
5. For each active ad squad, read its targeting (with `snapchat_get_ad_squad` when the list is not detailed enough): geography, age, gender, interests, segments included and excluded.
6. For ad squads whose targeting looks too narrow or too wide, call `snapchat_get_audience_size` with their `targeting_spec` and the ad squad fields (type, placement, optimization goal) to show the estimated reach.

## Output

1. **Measurement**: pixels, their state, and the ad squads that rely on them.
2. **Audiences**: a table with segment, source, size, retention and the ad squads that use it.
3. **Targeting**: per active ad squad, who it targets and its estimated reach.
4. **Issues**: missing or silent pixel, segments too small to deliver, overlapping ad squads.
5. **Fixes**: changes for a person to apply.

## Rules

- Audience sizes are Snapchat estimates given as ranges: show the range.
- A pixel state Snapchat did not return is unknown, not healthy.
- Do not conclude that the pixel works from its existence alone.

## Examples

- "Is the Snap pixel set up right for our conversion campaigns?"
- "Fais le point sur les audiences et le ciblage Snapchat."
- "Quelle est la taille estimée de l'audience de nos groupes d'annonces Snap ?"
