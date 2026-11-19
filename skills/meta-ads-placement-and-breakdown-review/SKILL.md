---
name: meta-ads-placement-and-breakdown-review
description: "Breaks Meta Ads results down through the Pilot MCP server: by platform (Facebook, Instagram, Messenger, Audience Network), by placement (feed, stories, reels, right column), by device, by age and gender and by country, for the account, a campaign or an ad set, then shows where cost per result and ROAS differ and which placements or groups to keep, lower or exclude. Use when the user asks where Meta ads perform, Facebook against Instagram, reels against feed, or who converts. Triggers: Meta placements, Facebook versus Instagram, breakdown by placement, reels performance. Déclencheurs : placements Meta, Facebook contre Instagram, découpage par placement, performance des reels, par âge et sexe."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Meta ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "meta_list_accounts meta_list_adsets meta_get_account_insights meta_get_campaign_insights meta_get_adset_insights"
  pilot-mcp-read-only: "true"
---

# Meta Ads placement and breakdown review

Show where Meta ads perform and where they only cost: platforms, placements, devices, ages, genders and countries. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a chart server to draw the splits. None is needed.
- **Read-only:** never change placements or targeting. Proposals are for a person to apply.

## Tools

- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_list_adsets`: ad sets with their targeting, to see which use automatic placements.
- `meta_get_account_insights`: account or campaign results with `breakdowns`.
- `meta_get_campaign_insights`: one campaign's results with `breakdowns`.
- `meta_get_adset_insights`: one ad set's results with `breakdowns`.

## Steps

1. Call `meta_list_accounts` and pick the account. Set the period: `last_30d` by default.
2. Call `meta_get_account_insights` with `level: "campaign"` and in turn `breakdowns: ["publisher_platform"]`, `["publisher_platform", "platform_position"]`, `["device_platform"]`, `["age", "gender"]` and `["country"]`.
3. For a campaign or an ad set the user names, call `meta_get_campaign_insights` or `meta_get_adset_insights` with the same breakdowns.
4. Call `meta_list_adsets` to see which ad sets let Meta choose placements and which restrict them.
5. For each cut, compare share of spend with share of results, and cost per result or ROAS with the average of the campaign.
6. Read with care: with automatic placements, Meta moves spend to where results are cheapest at the margin, so a placement with a higher average cost may still help. Propose a test before an exclusion.

## Output

1. **Summary**: where results come from, in three lines.
2. **Platforms and placements**: a table with spend, results, cost per result, ROAS and share of spend.
3. **Devices, ages, genders, countries**: the same, one table per cut.
4. **Proposals**: placements or groups to test excluding or to give their own ad set, for a person to decide.

## Rules

- Meta does not return every breakdown with every metric: a missing figure is absent, not zero.
- Do not judge a group with a few results.
- Never sum amounts in different currencies; name the period.
- Some sectors cannot target by age or gender: say so before proposing an exclusion.

## Examples

- "Does Instagram or Facebook perform better for us?"
- "Les reels coûtent-ils plus cher que le fil d'actualité sur la campagne Prospection ?"
- "Qui achète sur Meta, par âge et par sexe ?"
