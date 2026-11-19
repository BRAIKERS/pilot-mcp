---
name: pinterest-ads-catalog-and-feed-review
description: "Reviews the Pinterest catalogs behind shopping ads through the Pilot MCP server: catalogs and feeds, last processing results and their errors, product groups and how many products they hold, sample items, and the shopping campaigns that depend on them. Use when shopping or catalog ads on Pinterest under-deliver, after a feed change, or before a sales period. Triggers: Pinterest catalog check, Pinterest feed errors, Pinterest shopping ads. Déclencheurs : catalogue Pinterest, flux produits Pinterest, erreurs du flux Pinterest, annonces shopping Pinterest."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Pinterest ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "pinterest_ad_accounts_list pinterest_catalogs_list pinterest_catalog_feeds_list pinterest_catalog_feeds_get pinterest_catalog_feeds_processing_results pinterest_catalog_product_groups_list pinterest_catalog_product_groups_product_counts pinterest_catalog_items_get pinterest_campaigns_list pinterest_campaigns_analytics"
  pilot-mcp-read-only: "true"
---

# Pinterest Ads catalog and feed review

Tell the user whether the Pinterest catalog that feeds shopping ads is healthy, what breaks, and which campaigns are affected. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Pinterest ad account.
- **Optional MCP servers:** an e-commerce platform server to compare with the store's own product data. None is needed.
- **Read-only:** never create, update, re-ingest or delete a catalog, feed, item or product group. Fixes are for a person to apply.

## Tools

- `pinterest_ad_accounts_list`: the ad accounts available, with their IDs.
- `pinterest_catalogs_list`: the catalogs the connection can read.
- `pinterest_catalog_feeds_list`: the feeds of a catalog.
- `pinterest_catalog_feeds_get`: one feed's URL, format, currency, country and schedule.
- `pinterest_catalog_feeds_processing_results`: the last processing runs, item counts and errors.
- `pinterest_catalog_product_groups_list`: product groups of a catalog or feed.
- `pinterest_catalog_product_groups_product_counts`: how many products a group holds.
- `pinterest_catalog_items_get`: given items, as Pinterest holds them, for a country and language.
- `pinterest_campaigns_list`: campaigns, to find the shopping ones.
- `pinterest_campaigns_analytics`: spend and results of those campaigns.

## Steps

1. Call `pinterest_ad_accounts_list` and pick the account. Pass its ID as `ad_account_id` to the catalog tools.
2. Call `pinterest_catalogs_list`, then `pinterest_catalog_feeds_list` for each catalog.
3. For each feed, call `pinterest_catalog_feeds_get` (status, location, `default_currency`, `default_country`, schedule) and `pinterest_catalog_feeds_processing_results`. Compare the latest run with the previous one: items ingested, items dropped, errors and warnings by type.
4. Call `pinterest_catalog_product_groups_list`, then `pinterest_catalog_product_groups_product_counts` for each group. A group with zero products cannot serve any ad.
5. When the processing results name failing items, call `pinterest_catalog_items_get` with their IDs, `country` and `language` to show what Pinterest holds.
6. Call `pinterest_campaigns_list` to find catalog sales campaigns, then `pinterest_campaigns_analytics` over the last 14 days to see whether their delivery dropped when the feed broke.

## Output

1. **Verdict**: healthy, degraded or broken, in one sentence.
2. **Feeds**: a table per feed with the last run date, items ingested, items in error and the main error types.
3. **Product groups**: groups that are empty or much smaller than expected.
4. **Impact**: shopping campaigns whose spend or results moved with the feed.
5. **Fixes**: the changes to make in the feed or the store, for a person to apply.

## Rules

- Quote the date of each processing run: a feed can look fine while its last run is weeks old.
- An error count Pinterest did not return is absent, not zero.
- Never sum amounts in different currencies.

## Examples

- "Why are my Pinterest shopping ads not delivering?"
- "Vérifie le flux produits Pinterest avant le Black Friday."
- "Combien de produits sont rejetés dans le catalogue Pinterest ?"
