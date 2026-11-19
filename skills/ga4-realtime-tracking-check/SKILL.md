---
name: ga4-realtime-tracking-check
description: "Checks in real time that Google Analytics 4 receives data through the Pilot MCP server: active users and events of the last 30 minutes by page, event, device and country, whether the key events and enhanced measurement events arrive, and whether the data streams expected are active, right after a site release, a tag change or a campaign launch. Use when the user asks whether tracking works right now, after a deployment, or why GA4 shows nothing today. Triggers: GA4 realtime check, is tracking working, tracking after release, realtime events. Déclencheurs : vérifier GA4 en temps réel, le suivi fonctionne-t-il, suivi après mise en ligne, événements en temps réel."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Analytics 4 property attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_analytics_get_account_summaries google_analytics_list_data_streams google_analytics_get_enhanced_measurement_settings google_analytics_list_key_events google_analytics_run_realtime_report"
  pilot-mcp-read-only: "true"
---

# GA4 realtime tracking check

Tell the user, now, whether GA4 receives visits and the events that matter, and what is missing. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the property.
- **Optional MCP servers:** a browser server to visit the site and trigger events during the check. None is needed.
- **Read-only:** never change anything in GA4.

## Tools

- `google_analytics_get_account_summaries`: GA4 properties reachable.
- `google_analytics_list_data_streams`: web and app streams with measurement IDs.
- `google_analytics_get_enhanced_measurement_settings`: which automatic events a web stream should send.
- `google_analytics_list_key_events`: the key events expected.
- `google_analytics_run_realtime_report`: activity of the last 30 minutes.

## Steps

1. Call `google_analytics_get_account_summaries` and pick the property.
2. Call `google_analytics_list_data_streams` and, for each web stream, `google_analytics_get_enhanced_measurement_settings`, to know which events to expect.
3. Call `google_analytics_list_key_events` for the key events to expect.
4. Call `google_analytics_run_realtime_report` with `metrics: ["activeUsers"]` and, in turn, `dimensions: ["unifiedScreenName"]`, `["eventName"]` (with `metrics: ["eventCount"]`), `["deviceCategory"]` and `["country"]`.
5. Compare what arrives with what is expected: no active user on a site that has traffic means the tag does not fire; page views without enhanced measurement events means those settings are off or blocked; key events missing may simply not have happened in 30 minutes.
6. If the user can, ask them to visit the site and trigger a key event (for example a test form), then run the event report again.

## Output

1. **Verdict**: data arriving or not, now.
2. **What arrives**: active users, pages, events, devices and countries of the last 30 minutes.
3. **What is missing**: expected events not seen, and whether their absence is worrying.
4. **Next checks**: what to verify on the site or in Tag Manager.

## Rules

- Realtime covers the last 30 minutes: absence of a rare event is not proof of a break.
- Realtime figures are not final and differ from standard reports: never use them for a period report.
- Name the time of the check and the property time zone.

## Examples

- "We just released the new site: is GA4 still tracking?"
- "GA4 reçoit-il des données en ce moment ?"
- "Les achats remontent-ils dans GA4 depuis la mise à jour du tag ?"
