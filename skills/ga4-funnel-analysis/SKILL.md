---
name: ga4-funnel-analysis
description: "Analyzes a conversion funnel in Google Analytics 4 through the Pilot MCP server: builds the funnel from the events the property actually collects, measures completion and drop-off at each step, splits it by device, channel or country, shows what users do after leaving a step, and compares two periods, then names the step that loses most and why. Use when the user asks where users drop off, why the checkout converts poorly, or for a funnel by device or channel. Triggers: GA4 funnel, drop-off analysis, checkout funnel, conversion funnel. Déclencheurs : entonnoir GA4, analyse des abandons, tunnel de commande, entonnoir de conversion, où les visiteurs décrochent."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Analytics 4 property attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_analytics_get_account_summaries google_analytics_get_metadata google_analytics_list_key_events google_analytics_run_report google_analytics_run_funnel_report google_analytics_check_compatibility"
  pilot-mcp-read-only: "true"
---

# GA4 funnel analysis

Find the step where users drop, for whom, and what they do instead. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the property.
- **Optional MCP servers:** a chart server to draw the funnel. None is needed.
- **Read-only:** never change anything in GA4.

## Tools

- `google_analytics_get_account_summaries`: GA4 properties reachable.
- `google_analytics_get_metadata`: dimensions and metrics valid for the property.
- `google_analytics_list_key_events`: key events, to anchor the last step.
- `google_analytics_run_report`: event counts, to build steps from events that exist.
- `google_analytics_run_funnel_report`: completion and drop-off per step, with optional breakdown and next action.
- `google_analytics_check_compatibility`: checks a breakdown dimension before using it.

## Steps

1. Call `google_analytics_get_account_summaries` and pick the property.
2. Ask for the funnel, or propose one: for a store, `session_start` or `view_item`, `add_to_cart`, `begin_checkout`, `add_payment_info`, `purchase`; for leads, landing page view, form start, form submit, the lead key event.
3. Call `google_analytics_run_report` with `dimensions: ["eventName"]` and `metrics: ["eventCount", "totalUsers"]` over the period, and `google_analytics_list_key_events`: keep only steps whose events exist, with their exact names.
4. Call `google_analytics_run_funnel_report` with `dateRanges` and a `funnel` whose `steps` each have a `name` and a `filterExpression` on the event name. Read completion and drop-off per step.
5. Call it again with `funnelBreakdown` on `deviceCategory`, then on `sessionDefaultChannelGroup`; check a breakdown with `google_analytics_check_compatibility` or `google_analytics_get_metadata` when in doubt.
6. Call it with `funnelNextAction` on `eventName` to see what users do after the step they leave.
7. Run the funnel on the period before to see whether a step got worse.

## Output

1. **Funnel**: users and completion rate per step, and drop-off between steps.
2. **Weakest step**: where most users are lost, with the figure.
3. **By segment**: the same by device and by channel, with the gaps.
4. **After leaving**: what users do instead at the weakest step.
5. **Change**: the funnel against the period before.
6. **Hypotheses and tests**: what could explain the drop, for a person to check on the site.

## Rules

- The funnel report is an alpha GA4 feature: if it fails, say so and fall back on event counts from `google_analytics_run_report`, as an approximation.
- Event names are case-sensitive: use them as returned.
- Name the period and say whether the funnel is open (users may enter at any step) or closed.
- A segment with few users is not judged.

## Examples

- "Where do users drop off in our checkout?"
- "Construis l'entonnoir d'achat GA4 par appareil."
- "Le tunnel de commande s'est-il dégradé ce mois-ci ?"
