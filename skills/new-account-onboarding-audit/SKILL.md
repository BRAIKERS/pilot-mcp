---
name: new-account-onboarding-audit
description: "Takes stock of a newly connected workspace through the Pilot MCP server: which Google Ads, Meta Ads, TikTok Ads and Google Analytics 4 accounts are attached, their currency, time zone and status, whether data is being collected, which conversions, pixels and key events exist, and whether GA4 is linked to Google Ads, then lists what is missing before work can start. Use when a new client or a new account is connected, at the start of an engagement, or for a setup check. Triggers: onboarding audit, new client setup, first look at the account. Déclencheurs : audit d'arrivée, nouveau client, état des lieux, premier regard sur le compte, vérification de la configuration."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on the workspace of the new client."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "pilot_data_freshness google_list_accounts google_get_account_info google_list_campaigns google_list_conversion_actions_detailed meta_list_accounts meta_check_account_issues meta_list_pixels tiktok_get_advertiser_info tiktok_get_pixels google_analytics_get_account_summaries google_analytics_list_key_events google_analytics_list_google_ads_links"
  pilot-mcp-read-only: "true"
---

# New account onboarding audit

Give the team a clear inventory of a new workspace and the list of what must be fixed or asked for before optimizing anything. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace of the new client.
- **Optional MCP servers:** a project management server to open the setup tasks, a document server to keep the inventory. None is needed.
- **Read-only:** never create, link or change anything during onboarding. The to-do list is for a person.

## Tools

- `pilot_data_freshness`: whether Pilot already collects each platform, and through which day.
- `google_list_accounts`: the Google Ads accounts reachable, manager accounts and sub-accounts.
- `google_get_account_info`: name, currency, time zone, type and the last 30 days' figures of one account.
- `google_list_campaigns`: campaigns with their status and channel type.
- `google_list_conversion_actions_detailed`: conversion actions with their status, counting and attribution.
- `meta_list_accounts`: the Meta ad accounts, their status and currency.
- `meta_check_account_issues`: disabled account, disable reason, spend cap.
- `meta_list_pixels`: the pixels of a Meta ad account.
- `tiktok_get_advertiser_info`: the TikTok advertiser, its currency and time zone.
- `tiktok_get_pixels`: the TikTok pixels of the advertiser.
- `google_analytics_get_account_summaries`: GA4 accounts and properties reachable.
- `google_analytics_list_key_events`: the key events of a GA4 property.
- `google_analytics_list_google_ads_links`: links between the GA4 property and Google Ads.

## Steps

1. Call `pilot_data_freshness`. A platform without collected data is either not connected or connected too recently: note which.
2. Google Ads: `google_list_accounts` (with `manager_customer_id` and `include_nested: true` when a manager account is listed), then for each client account `google_get_account_info`, `google_list_campaigns` and `google_list_conversion_actions_detailed`.
3. Meta Ads: `meta_list_accounts`, then for each account `meta_check_account_issues` and `meta_list_pixels` with `champs: ["last_fired_time", "is_unavailable"]` to see whether each pixel still fires.
4. TikTok Ads, if attached: `tiktok_get_advertiser_info`, then `tiktok_get_pixels`.
5. GA4: `google_analytics_get_account_summaries`, then for the property in use `google_analytics_list_key_events` and `google_analytics_list_google_ads_links`.
6. Cross-check: same currency and time zone across platforms, at least one primary conversion per ad account, a pixel that fired recently, GA4 linked to each Google Ads account that spends.

## Output

1. **Inventory**: a table with platform, account name and ID, currency, time zone, status, campaigns active, data collected through.
2. **Measurement**: conversions, pixels and key events found, with their state.
3. **Gaps**: what is missing or broken, ranked by impact on reporting.
4. **To ask the client**: accesses, accounts or information still needed.
5. **First tasks**: the setup steps in order, for a person to do.

## Rules

- Report what the tools return; an account not listed is not attached, not empty.
- Different currencies across accounts are normal for a multi-country client: flag them so that no one sums them later.
- Do not judge performance yet: onboarding is an inventory, not an audit.

## Examples

- "We just connected a new client: what do we have and what is missing?"
- "Fais l'état des lieux du nouvel espace : comptes, conversions, pixels, GA4."
- "Le compte est-il prêt pour qu'on commence à optimiser ?"
