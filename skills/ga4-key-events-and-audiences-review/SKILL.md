---
name: ga4-key-events-and-audiences-review
description: "Reviews Google Analytics 4 key events and audiences in read-only mode through the Pilot MCP server: each key event with its counting method and default value, how many times it fired and whether it is still collected, events collected that should be key events, events marked as key events that should not be, the audiences defined with their membership duration and filters, and the Google Ads accounts that may import them, then lists the changes for a person to make in GA4 Admin. Use when the user asks what GA4 counts as a conversion, whether key events are right, or which audiences exist. Triggers: GA4 key events review, GA4 conversions setup, GA4 audiences. Déclencheurs : revue des événements clés GA4, conversions GA4, audiences GA4, que compte GA4 comme conversion."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Analytics 4 property attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_analytics_get_account_summaries google_analytics_get_property google_analytics_list_key_events google_analytics_get_key_event google_analytics_run_report google_analytics_list_audiences google_analytics_get_audience google_analytics_list_google_ads_links"
  pilot-mcp-read-only: "true"
---

# GA4 key events and audiences review

Check that GA4 counts the right actions as key events, the right way, and that its audiences are usable, then list what to change. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the property.
- **Optional MCP servers:** none.
- **Read-only:** never create, change or delete a key event or an audience. The Pilot MCP server does not write to GA4: every change is for a person to make in GA4 Admin.

## Tools

- `google_analytics_get_account_summaries`: GA4 properties reachable.
- `google_analytics_get_property`: currency, for default values.
- `google_analytics_list_key_events`: key events with counting method and default value.
- `google_analytics_get_key_event`: one key event in detail.
- `google_analytics_run_report`: event counts and key events over time.
- `google_analytics_list_audiences`: audiences with membership duration and filters.
- `google_analytics_get_audience`: one audience in detail.
- `google_analytics_list_google_ads_links`: Google Ads accounts linked, which may import key events and audiences.

## Steps

1. Call `google_analytics_get_account_summaries`, pick the property, and call `google_analytics_get_property`.
2. Call `google_analytics_list_key_events`, and `google_analytics_get_key_event` for any that needs a closer look. Note each counting method: `ONCE_PER_EVENT` suits purchases, `ONCE_PER_SESSION` suits leads where repeats are duplicates.
3. Call `google_analytics_run_report` with `dimensions: ["eventName"]` and `metrics: ["eventCount", "keyEvents"]` over the last 28 days. Flag key events with no count (no longer collected, or renamed), and collected events that look like business goals but are not key events (`purchase`, `generate_lead`, `sign_up`, a form submit).
4. Call it again with `dimensions: ["date", "eventName"]` and a filter on the key events, to spot a key event that stopped on a given day.
5. Flag key events that inflate conversions: page views, scrolls or clicks marked as key events, or two key events for the same action.
6. Call `google_analytics_list_audiences`, then `google_analytics_get_audience` for those in use: membership duration, include and exclude clauses, and whether the definition still matches events that exist.
7. Call `google_analytics_list_google_ads_links`: a change to key events changes what linked Google Ads accounts can import.

## Output

1. **Key events**: a table with event, counting method, default value, count over 28 days and verdict.
2. **Missing or doubtful**: events to mark as key events, key events to unmark, duplicates.
3. **Audiences**: a table with name, duration, definition in plain words and verdict.
4. **Changes to make**: each with its reason, for a person to apply in GA4 Admin, and the effect on linked Google Ads accounts.

## Rules

- Event names are case-sensitive: quote them as returned.
- A key event change applies from that day onward: past data is not reprocessed.
- Name the period and the property currency.

## Examples

- "What does GA4 count as a conversion for us, and is it right?"
- "Nos événements clés GA4 sont-ils bien configurés ?"
- "Quelles audiences GA4 existent, et sont-elles encore utiles ?"
