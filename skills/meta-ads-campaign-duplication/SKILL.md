---
name: meta-ads-campaign-duplication
description: "Duplicates Meta Ads campaigns, ad sets or ads through the Pilot MCP server, after explicit approval of each write: reads the source and its results, copies it paused (with its ad sets and ads when asked), then adjusts the copy's name, budget, targeting or schedule, one write at a time, and reads it back, to test a new audience, market or budget without touching what works. Use when the user asks to copy, duplicate or clone a Meta campaign, ad set or ad. Triggers: duplicate Meta campaign, copy ad set, clone Facebook campaign. Déclencheurs : dupliquer une campagne Meta, copier un ensemble de publicités, cloner une campagne Facebook."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Meta ad account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "meta_list_accounts meta_list_campaigns meta_list_adsets meta_get_campaign meta_get_adset meta_get_account_insights meta_copy_campaign meta_copy_adset meta_copy_ad meta_update_adset"
  pilot-mcp-read-only: "false"
---

# Meta Ads campaign duplication

Copy a Meta campaign, ad set or ad to test a variation, leaving the original untouched, one approved write at a time. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** none.
- **Writes:** the write tools require the paid Pilot MCP plan and a workspace role that can write. If a write tool is not served, say so and return the steps for a person to duplicate in Meta Ads Manager.

## Tools

- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_list_campaigns`: campaigns, to find the source.
- `meta_list_adsets`: ad sets, to find the source and read back copies.
- `meta_get_campaign`: one campaign's settings.
- `meta_get_adset`: one ad set's budget and targeting.
- `meta_get_account_insights`: the source's recent results.
- `meta_copy_campaign`: copies a campaign with its ad sets and ads, paused by default (write).
- `meta_copy_adset`: copies an ad set with its ads, into the same or another campaign, paused by default (write).
- `meta_copy_ad`: copies an ad, optionally into another ad set, paused by default (write).
- `meta_update_adset`: adjusts the copy's name, budget, targeting or schedule (write).

## Confirmation

Every write changes a live account. For each one:

1. Read the source just before writing with `meta_get_campaign` or `meta_get_adset`.
2. Show the person the exact source (name, ID, account), what will be copied, where, and the settings of the copy.
3. Wait for an explicit yes for this write. A yes for the copy does not cover the adjustments that follow.
4. Make one write at a time.
5. Create every copy paused (`status: "PAUSED"`). Never activate the copy or touch the source in this skill.
6. Never delete anything, the source or a copy.
7. After each write, read the copy back and show its settings next to the source's.
8. Stop at the first write that is refused or fails, report what exists, and do not try a workaround.

## Steps

1. Call `meta_list_accounts` and pick the account. Find the source with `meta_list_campaigns` or `meta_list_adsets`.
2. Read it with `meta_get_campaign` or `meta_get_adset`, and its results with `meta_get_account_insights` at its level over `last_30d`.
3. Agree on the test with the person: what changes in the copy (audience, country, budget, schedule, placement), and how long it runs.
4. Copy:
   - campaign: `meta_copy_campaign` with `campaign_id`, `name` (prefix of the copied names), `deep_copy: true` and `status: "PAUSED"`;
   - ad set: `meta_copy_adset` with `adset_id`, optional `campaign_id`, `deep_copy: true` and `status: "PAUSED"`;
   - ad: `meta_copy_ad` with `ad_id`, optional `adset_id` and `status: "PAUSED"`.
5. Find the copy with `meta_list_adsets`, then adjust it with `meta_update_adset` and `updates` holding `name`, `daily_budget`, `targeting` or `end_time`, each after its yes.
6. Read back with `meta_get_adset`.

## Output

1. **Source**: settings and recent results.
2. **Copy**: ID, paused status and settings, next to the source's.
3. **Test plan**: what is tested, for how long, and on which metric to judge it.
4. **Not done**: refused or skipped writes.

## Rules

- A copy starts its own learning: it does not inherit the source's history.
- Two ad sets targeting the same people compete in the same auctions: say so when the copy only changes the budget.
- Budgets are integers in the currency's minor unit.

## Examples

- "Duplicate our best ad set to test it in Belgium."
- "Copie la campagne Black Friday en pause pour la relancer à Noël."
- "Duplique la meilleure publicité dans l'ensemble Lookalike."
