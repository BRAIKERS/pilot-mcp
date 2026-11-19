---
name: naming-convention-audit
description: "Audits the naming of campaigns, ad groups, ad sets and ads through the Pilot MCP server, on Google Ads, Meta Ads and TikTok Ads: infers or checks the naming convention, lists the names that break it, duplicates, default names left by the platforms, and labels in use, then proposes corrected names for a person to apply. Use when the user asks to check naming, before building reports that group campaigns by name, or when taking over an account. Triggers: naming convention audit, campaign names, inconsistent naming, rename campaigns. Déclencheurs : audit de nommage, convention de nommage, noms de campagnes, noms incohérents, renommer les campagnes."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with Google Ads, Meta Ads or TikTok Ads attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_list_campaigns google_list_ad_groups google_list_labels meta_list_accounts meta_list_campaigns meta_list_adsets meta_list_ads tiktok_get_advertiser_info tiktok_get_campaigns tiktok_get_ad_groups search"
  pilot-mcp-read-only: "true"
---

# Naming convention audit

Check that names follow one convention across platforms, so that reports can group campaigns reliably, and list the names to fix. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace concerned.
- **Optional MCP servers:** a spreadsheet server to hand over the renaming list. None is needed.
- **Read-only:** never rename anything. The renaming list is for a person to apply.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_list_campaigns`: Google Ads campaigns with name, status and channel type.
- `google_list_ad_groups`: ad groups with name, status and campaign.
- `google_list_labels`: labels defined in the Google Ads account.
- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_list_campaigns`: Meta campaigns with name, status and objective.
- `meta_list_adsets`: Meta ad sets with name and campaign.
- `meta_list_ads`: Meta ads with name, ad set and campaign.
- `tiktok_get_advertiser_info`: the TikTok advertiser attached.
- `tiktok_get_campaigns`: TikTok campaigns with name, status and objective.
- `tiktok_get_ad_groups`: TikTok ad groups with name and campaign.
- `search`: finds campaigns by a part of their name across platforms.

## Steps

1. Ask for the convention if the team has one (for example market, objective, channel, audience, date, separated by a fixed character). If not, infer it from the most common pattern among active campaigns and state it before judging.
2. Google Ads: `google_list_accounts`, then `google_list_campaigns` with `limit: 1000`, `google_list_ad_groups` with `limit: 1000`, and `google_list_labels`.
3. Meta Ads: `meta_list_accounts`, then `meta_list_campaigns`, `meta_list_adsets` and `meta_list_ads`, following `pages` while `suite` says rows remain.
4. TikTok Ads, if attached: `tiktok_get_advertiser_info`, then `tiktok_get_campaigns` and `tiktok_get_ad_groups`, page by page.
5. Check each name: number and order of parts, allowed values in each part, separator, case, accents, dates in one format, no default name (such as "New campaign" or a copy suffix), no duplicate among active objects.
6. Use `search` with a part of the convention (a market code, for example) to see whether campaigns sharing it are grouped as expected across platforms.
7. Propose a corrected name for each name that breaks the convention, keeping its meaning.

## Output

1. **Convention**: the pattern used for judging, given or inferred.
2. **Compliance**: share of compliant names per platform and level.
3. **To rename**: a table with platform, level, current name, ID, the issue and the proposed name.
4. **Duplicates and default names**: listed apart.
5. **Labels**: Google Ads labels in use, and whether they could carry what names carry today.

## Rules

- Judge active and paused objects; leave removed ones out.
- Never propose a name that changes what an object is: renaming only.
- Renaming an object does not reset its learning, but reports that filter on the old name break: say so.
- Quote names exactly as the platform returns them.

## Examples

- "Do our campaign names follow a consistent convention?"
- "Audite le nommage des campagnes Google, Meta et TikTok, et propose les noms corrigés."
- "Quelles campagnes ont encore un nom par défaut ?"
