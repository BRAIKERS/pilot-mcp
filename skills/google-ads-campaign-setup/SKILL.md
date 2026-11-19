---
name: google-ads-campaign-setup
description: "Sets up a new Google Ads Search campaign through the Pilot MCP server, after explicit approval of each write: drafts the full plan first (budget, campaign, locations, language, ad groups, keywords, responsive search ads), then creates each piece one at a time, everything paused, and reads the result back, so that a person reviews it before enabling. Use when the user asks to create or launch a Google Ads campaign. Triggers: create Google Ads campaign, new Search campaign, launch campaign, campaign setup. Déclencheurs : créer une campagne Google Ads, nouvelle campagne Search, lancer une campagne, mise en place de campagne."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Ads account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_get_account_info google_list_campaigns google_search_locations google_get_keyword_ideas google_create_budget google_create_campaign google_add_location_targeting google_add_campaign_targeting google_create_ad_group google_add_keyword google_create_responsive_search_ad google_list_ad_groups google_list_ads"
  pilot-mcp-read-only: "false"
---

# Google Ads campaign setup

Build a new Search campaign from an approved plan, piece by piece, everything paused, for a person to review and enable. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a document server to share the plan. None is needed.
- **Writes:** the write tools require the paid Pilot MCP plan and a workspace role that can write. If a write tool is not served, say so and return the plan as a list of settings for a person to build by hand in Google Ads.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_get_account_info`: account currency and time zone.
- `google_list_campaigns`: existing campaigns, to avoid duplicates and overlap.
- `google_search_locations`: location IDs by name.
- `google_get_keyword_ideas`: keyword ideas with volumes and bids.
- `google_create_budget`: creates a daily campaign budget (write).
- `google_create_campaign`: creates a campaign on that budget, paused by default (write).
- `google_add_location_targeting`: targets or excludes locations (write).
- `google_add_campaign_targeting`: adds a language or other criterion (write).
- `google_create_ad_group`: creates an ad group (write).
- `google_add_keyword`: adds a keyword to an ad group (write).
- `google_create_responsive_search_ad`: creates a responsive search ad (write).
- `google_list_ad_groups`: reads back the ad groups created.
- `google_list_ads`: reads back the ads created.

## Confirmation

Every write changes a live account. For each one:

1. Read the current state before writing: existing campaigns and budgets, so that nothing is duplicated.
2. Show the person the exact object to create (name, account and ID, settings), and that nothing of that name exists yet.
3. Wait for an explicit yes for this write. Approval of the whole plan does not replace a yes per write, unless the person says so for the plan.
4. Make one write at a time, in the order of the plan.
5. Create everything paused: the campaign with `status: "PAUSED"`, ad groups, keywords and ads with `status: "PAUSED"`. Never enable anything in this skill.
6. Never delete anything. If a piece is wrong, stop and tell the person.
7. After each write, read back and show what was created.
8. Stop at the first write that is refused or fails, report what exists so far, and do not try a workaround.

## Steps

1. Ask for the brief: product or service, landing page, market, language, daily budget, goal, bidding preference.
2. Call `google_list_accounts`, `google_get_account_info` and `google_list_campaigns`.
3. Draft the plan: budget amount, campaign name, locations from `google_search_locations`, language, two to five ad groups by theme, five to fifteen keywords each from `google_get_keyword_ideas` and the brief, one responsive search ad per ad group (at least 8 headlines of 30 characters at most and 3 descriptions of 90 at most). Show the plan and get it approved.
4. Build, one approved write at a time:
   1. `google_create_budget` with `name` and `amount_micros`.
   2. `google_create_campaign` with `name`, the new `budget_resource_name`, `channel_type: "SEARCH"`, `status: "PAUSED"`, and `maximize_conversions: true` if chosen.
   3. `google_add_location_targeting` with `location_ids`; `google_add_campaign_targeting` with `criterion_type: "LANGUAGE"`.
   4. `google_create_ad_group` per theme with `status: "PAUSED"`.
   5. `google_add_keyword` per keyword with `match_type` and `status: "PAUSED"`.
   6. `google_create_responsive_search_ad` per ad group with `headlines`, `descriptions`, `final_urls` and `status: "PAUSED"`.
5. Read back with `google_list_campaigns`, `google_list_ad_groups` and `google_list_ads`.

## Output

1. **Plan**: every setting, for approval.
2. **Built**: each object created, with its ID and status.
3. **Before enabling**: what a person should check (conversion goal, ad review status, budget), and that enabling starts spend.

## Rules

- Budget and bids are in micros: 30 per day is `30000000`.
- Never invent an offer, price or claim for the ads: use the brief and the landing page.
- Count characters exactly.
- A budget alone spends nothing; a paused campaign spends nothing.

## Examples

- "Create a Search campaign for our new product line, paused for review."
- "Monte une campagne Search pour la boutique de Lyon, 40 euros par jour, en pause."
- "Prépare et crée la campagne Google Ads de lancement, je l'activerai moi-même."
