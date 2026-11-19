---
name: meta-ads-campaign-setup
description: "Sets up a new Meta Ads campaign through the Pilot MCP server, after explicit approval of each write: drafts the plan first (objective, budget, ad sets with locations, ages, interests or audiences, optimization goal and pixel, ads from existing creatives), checks the audience size and Meta's minimum budget, then creates the campaign, the ad sets and the ads one at a time, all paused, and reads them back. Use when the user asks to create or launch a Meta, Facebook or Instagram campaign. Triggers: create Meta campaign, launch Facebook ads, new Instagram campaign. Déclencheurs : créer une campagne Meta, lancer des pubs Facebook, nouvelle campagne Instagram, mise en place Meta."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Meta ad account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "meta_list_accounts meta_get_account meta_list_campaigns meta_list_pixels meta_search_targeting meta_estimate_reach meta_get_minimum_budgets meta_list_ad_creatives meta_create_campaign meta_create_adset meta_create_ad meta_get_campaign meta_get_adset meta_get_ad"
  pilot-mcp-read-only: "false"
---

# Meta Ads campaign setup

Build a new Meta campaign from an approved plan, piece by piece, everything paused, for a person to review and activate. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a document server to share the plan. None is needed.
- **Writes:** the write tools require the paid Pilot MCP plan and a workspace role that can write. If a write tool is not served, say so and return the plan as settings for a person to build in Meta Ads Manager.

## Tools

- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_get_account`: currency, time zone and status.
- `meta_list_campaigns`: existing campaigns, to avoid duplicates.
- `meta_list_pixels`: the pixels available for conversion optimization.
- `meta_search_targeting`: targeting options (interests, locations) by name.
- `meta_estimate_reach`: estimated audience of a targeting spec.
- `meta_get_minimum_budgets`: the lowest daily budget Meta accepts.
- `meta_list_ad_creatives`: existing creatives to use in the ads.
- `meta_create_campaign`: creates a campaign, paused by default (write).
- `meta_create_adset`: creates an ad set, paused by default (write).
- `meta_create_ad`: creates an ad, paused (write).
- `meta_get_campaign`: reads back the campaign created.
- `meta_get_adset`: reads back each ad set created.
- `meta_get_ad`: reads back each ad created.

## Confirmation

Every write changes a live account. For each one:

1. Read the current state before writing: existing campaigns, so that nothing is duplicated.
2. Show the person the exact object to create (name, account and ID, every setting).
3. Wait for an explicit yes for this write. Approval of the plan does not replace a yes per write, unless the person says so for the plan.
4. Make one write at a time, in the order of the plan.
5. Create everything paused: `status: "PAUSED"` on the campaign and ad sets; ads are created paused. Never activate anything in this skill.
6. Never delete anything. If a piece is wrong, stop and tell the person.
7. After each write, read the object back and show it.
8. Stop at the first write that is refused or fails, report what exists so far, and do not try a workaround.

## Steps

1. Ask for the brief: objective, product, landing page, market, budget, audience, creatives to use.
2. Call `meta_list_accounts`, `meta_get_account`, `meta_list_campaigns` and `meta_list_pixels`.
3. Build the targeting: `meta_search_targeting` with `type: "adinterest"` or `"adgeolocation"` and `q`, then `meta_estimate_reach` with the `targeting_spec`. Check the daily budget against `meta_get_minimum_budgets`.
4. Pick creatives with `meta_list_ad_creatives`. Creating a new creative is out of this skill.
5. Show the plan and get it approved: campaign name and objective, special ad categories if any, each ad set (name, budget in minor unit, targeting, optimization goal, pixel and event for conversions, schedule), each ad (name, creative).
6. Build, one approved write at a time:
   1. `meta_create_campaign` with `name`, `objective`, `status: "PAUSED"` and `special_ad_categories`.
   2. `meta_create_adset` per ad set with `campaign_id`, `name`, `daily_budget`, `targeting`, `optimization_goal`, `billing_event`, `status: "PAUSED"`, and `dsa_beneficiary` and `dsa_payor` for accounts that target the European Union.
   3. `meta_create_ad` per ad with `adset_id`, `name` and `creative`.
7. Read back with `meta_get_campaign`, `meta_get_adset` and `meta_get_ad`.

## Output

1. **Plan**: every setting, for approval.
2. **Built**: each object created, with its ID and paused status.
3. **Before activating**: what a person should check (pixel event, ad review, budget), and that activating starts spend.

## Rules

- Budgets are integers in the currency's minor unit: 30.00 EUR per day is `3000`.
- Audience sizes are Meta's estimates, given as bounds.
- Housing, employment, credit and social issue ads need their special ad category: ask when the subject suggests it.
- Never invent a claim or an offer for the ads.

## Examples

- "Create a Meta conversion campaign for our new collection, paused."
- "Monte une campagne Meta de prospection en France, 50 euros par jour, en pause."
- "Prépare la campagne Instagram de lancement avec les créas existantes, je l'activerai moi-même."
