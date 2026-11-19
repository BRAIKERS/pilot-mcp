---
name: ga4-annotations-review
description: "Reads the Google Analytics 4 timeline in read-only mode through the Pilot MCP server: lists the annotations in place, finds the breaks in daily sessions and key events that no annotation explains, and drafts the annotations a person should add for the launches, promotions, site releases and tracking changes the user confirms. Use when the user asks why a curve moved on a given day, wants to document past events in GA4, or prepares a report that needs context. Triggers: GA4 annotations, unexplained traffic break, document launches in Analytics. Déclencheurs : annotations GA4, cassure de trafic inexpliquée, consigner les lancements dans Analytics."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Analytics 4 property attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_analytics_get_account_summaries google_analytics_get_property google_analytics_list_property_annotations google_analytics_get_annotation google_analytics_run_report"
  pilot-mcp-read-only: "true"
---

# GA4 annotations review

Explain the breaks in the GA4 timeline with the annotations in place, and draft the ones missing. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the property.
- **Optional MCP servers:** a calendar or project server listing launches and releases. None is needed.
- **Read-only:** never create, change or delete an annotation. The Pilot MCP server does not write to GA4: the drafts are for a person to add in GA4. The annotations endpoint may require the Analytics Admin API alpha: if it fails, say so and work from the daily figures alone.

## Tools

- `google_analytics_get_account_summaries`: GA4 properties reachable.
- `google_analytics_get_property`: time zone of the property.
- `google_analytics_list_property_annotations`: annotations in place.
- `google_analytics_get_annotation`: one annotation in detail.
- `google_analytics_run_report`: daily sessions and key events, to find breaks.

## Steps

1. Call `google_analytics_get_account_summaries`, pick the property, and call `google_analytics_get_property` for its time zone.
2. Call `google_analytics_list_property_annotations`, and `google_analytics_get_annotation` for any whose title is unclear.
3. Call `google_analytics_run_report` with `dimensions: ["date"]` and `metrics: ["sessions", "keyEvents"]` over the last 90 days. Mark days where a figure moves far from the days around it, or a level that shifts and stays.
4. For each break, call `google_analytics_run_report` with `dimensions: ["date", "sessionDefaultChannelGroup"]` around that date, to see which channel moved.
5. Match breaks with annotations. For those left, ask the user what happened, or take the events they list. Never present a guess as a fact.
6. Draft each missing annotation: date, short title, description with the facts, and a color by kind if the team uses a code.

## Output

1. **Annotations in place**: date and title.
2. **Breaks**: date, what moved, which channel, and the annotation that explains it, if any.
3. **Unexplained breaks**: to ask about.
4. **Drafts**: annotations to add, ready to copy into GA4.

## Rules

- Only facts the user confirms become annotations.
- Dates are in the property time zone.
- A one-day spike on a small site may be noise: say so before calling it a break.

## Examples

- "Which breaks in our GA4 traffic have no explanation?"
- "Pourquoi les sessions ont chuté le 3 novembre ?"
- "Prépare les annotations GA4 à ajouter pour le trimestre."
