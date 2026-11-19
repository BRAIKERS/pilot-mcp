---
name: ga4-property-audit
description: "Audits a Google Analytics 4 property in read-only mode through the Pilot MCP server: property settings (time zone, currency), data streams and enhanced measurement, key events and their counting, custom dimensions and metrics, Google Ads links, audiences, channel groups, and data quality signs such as unassigned traffic or not set values, then returns a prioritized list of fixes. Use when the user asks to audit or health-check GA4, or before trusting GA4 figures. Triggers: GA4 audit, Google Analytics audit, GA4 setup check. Déclencheurs : audit GA4, audit Google Analytics, vérifier la configuration GA4, bilan de santé GA4."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Analytics 4 property attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_analytics_get_account_summaries google_analytics_get_property google_analytics_list_data_streams google_analytics_get_enhanced_measurement_settings google_analytics_list_key_events google_analytics_list_custom_dimensions google_analytics_list_custom_metrics google_analytics_list_google_ads_links google_analytics_list_audiences google_analytics_list_channel_groups google_analytics_run_report"
  pilot-mcp-read-only: "true"
---

# GA4 property audit

Check that a GA4 property is set up to measure what the business needs, and list what to fix, most important first. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the property.
- **Optional MCP servers:** a document server to keep the audit. None is needed.
- **Read-only:** never create, change or delete anything in GA4. Fixes are for a person to apply.

## Tools

- `google_analytics_get_account_summaries`: GA4 accounts and properties reachable.
- `google_analytics_get_property`: time zone, currency, industry and service level.
- `google_analytics_list_data_streams`: web and app streams with measurement IDs.
- `google_analytics_get_enhanced_measurement_settings`: automatic events enabled on a web stream.
- `google_analytics_list_key_events`: key events and their counting method.
- `google_analytics_list_custom_dimensions`: custom dimensions with scope.
- `google_analytics_list_custom_metrics`: custom metrics with unit.
- `google_analytics_list_google_ads_links`: links to Google Ads accounts.
- `google_analytics_list_audiences`: audiences defined.
- `google_analytics_list_channel_groups`: custom channel groups.
- `google_analytics_run_report`: data quality checks on sessions and events.

## Steps

1. Call `google_analytics_get_account_summaries` and pick the property (ask if several). Use its ID as `propertyId`.
2. Call `google_analytics_get_property`: the time zone and currency must match the business and the ad accounts.
3. Call `google_analytics_list_data_streams`, then `google_analytics_get_enhanced_measurement_settings` for each web stream.
4. Call `google_analytics_list_key_events`: a store without `purchase` as a key event, or page views marked as key events, are findings. Note the counting method of each.
5. Call `google_analytics_list_custom_dimensions` and `google_analytics_list_custom_metrics`.
6. Call `google_analytics_list_google_ads_links`, `google_analytics_list_audiences` and `google_analytics_list_channel_groups`.
7. Data quality: `google_analytics_run_report` over the last 30 days with `dimensions: ["sessionDefaultChannelGroup"]` and `metrics: ["sessions", "keyEvents"]` (a large Unassigned share means broken campaign tags), then with `dimensions: ["eventName"]` and `metrics: ["eventCount"]` (duplicated or misspelled events), then with `dimensions: ["hostname"]` (traffic from hosts that are not the site).

## Output

1. **Summary**: whether GA4 can be trusted, and the top three fixes.
2. **Settings**: property, streams, enhanced measurement.
3. **Measurement**: key events, custom definitions, links.
4. **Data quality**: unassigned traffic, odd events, foreign hostnames.
5. **Fixes**: ranked by impact on reporting, for a person to apply.

## Rules

- Quote event names exactly as returned; GA4 names are case-sensitive.
- A share is computed on the property's totals for the same period.
- The GA4 admin change history is not read here: say so if the user asks who changed a setting.
- Name the period and the property time zone.

## Examples

- "Audit our GA4 property."
- "La configuration GA4 est-elle fiable pour piloter la pub ?"
- "Pourquoi autant de trafic « Unassigned » dans GA4 ?"
