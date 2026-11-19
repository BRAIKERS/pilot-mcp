---
name: ga4-traffic-report
description: "Builds a Google Analytics 4 traffic report through the Pilot MCP server: users, sessions, engagement and key events over the period against the period before and last year, by channel, source and medium, device and country, the day-by-day trend, and the annotations that explain breaks. Use when the user asks for a GA4 report, site traffic, or where visitors come from. Triggers: GA4 traffic report, website traffic, traffic sources, Google Analytics report. Déclencheurs : rapport de trafic GA4, trafic du site, sources de trafic, rapport Google Analytics."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Analytics 4 property attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_analytics_get_account_summaries google_analytics_get_property google_analytics_run_report google_analytics_batch_run_reports google_analytics_list_property_annotations"
  pilot-mcp-read-only: "true"
---

# GA4 traffic report

Report how much traffic the site got, from where, how engaged it was, and what changed. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the property.
- **Optional MCP servers:** a file storage or slide server to keep the report. None is needed.
- **Read-only:** never change anything in GA4.

## Tools

- `google_analytics_get_account_summaries`: GA4 properties reachable.
- `google_analytics_get_property`: time zone and currency.
- `google_analytics_run_report`: one report with dimensions and metrics over one or more date ranges.
- `google_analytics_batch_run_reports`: up to five reports in one call.
- `google_analytics_list_property_annotations`: dated notes on launches and changes.

## Steps

1. Call `google_analytics_get_account_summaries` and pick the property; call `google_analytics_get_property` for its time zone.
2. Set three ranges: the period (last 28 days by default), the period before, and the same period last year.
3. Call `google_analytics_batch_run_reports` with these reports, each with the three `dateRanges`:
   - totals: no dimension, `metrics: ["totalUsers", "newUsers", "sessions", "engagedSessions", "engagementRate", "averageSessionDuration", "keyEvents"]`;
   - channels: `dimensions: ["sessionDefaultChannelGroup"]`, `metrics: ["sessions", "engagementRate", "keyEvents"]`;
   - sources: `dimensions: ["sessionSource", "sessionMedium"]`, same metrics, `limit: 25`;
   - devices: `dimensions: ["deviceCategory"]`;
   - countries: `dimensions: ["country"]`, `limit: 15`.
4. Call `google_analytics_run_report` with `dimensions: ["date"]` and `metrics: ["sessions", "keyEvents"]` over the period for the daily trend.
5. Call `google_analytics_list_property_annotations` to explain breaks in the trend. This endpoint may not be available on every property: if it fails, say so and go on.
6. Explain each large change by the channels and sources that carry it.

## Output

1. **Headline**: one sentence on the period.
2. **Totals**: users, sessions, engagement rate and key events, against the period before and last year.
3. **Channels and sources**: tables with the change.
4. **Devices and countries**: shares and changes.
5. **Trend**: the daily curve and its breaks, with annotations.
6. **To watch**: three points for a person.

## Rules

- Name the date ranges and the property time zone.
- GA4 key events and ad platform conversions are not the same count: never mix them.
- A small difference in users between reports comes from GA4's estimation: do not explain it as a trend.
- Data of the last day or two can still change.

## Examples

- "Give me the GA4 traffic report for the last four weeks."
- "D'où vient le trafic du site ce mois-ci, comparé à l'an dernier ?"
- "Quels canaux ont fait bouger les sessions ?"
