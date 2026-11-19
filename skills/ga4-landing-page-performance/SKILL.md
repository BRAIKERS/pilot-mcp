---
name: ga4-landing-page-performance
description: "Reviews landing pages in Google Analytics 4 through the Pilot MCP server: sessions, engagement rate, key events, key event rate and revenue per landing page, split by channel and by device, the pages that receive paid traffic but convert poorly, and how each page changed against the period before, then lists the pages to fix first. Use when the user asks which landing pages work, where paid traffic lands, or which pages to improve. Triggers: landing page performance, GA4 landing pages, best landing pages, page conversion rate. Déclencheurs : performance des pages d'atterrissage, pages de destination GA4, meilleures pages, taux de conversion par page."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Analytics 4 property attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_analytics_get_account_summaries google_analytics_get_property google_analytics_run_report google_analytics_run_pivot_report google_analytics_check_compatibility"
  pilot-mcp-read-only: "true"
---

# GA4 landing page performance

Show which landing pages turn visits into results, which waste traffic, and which to fix first. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the property.
- **Optional MCP servers:** a browser or screenshot server to look at the pages. None is needed.
- **Read-only:** never change anything in GA4 or on the site.

## Tools

- `google_analytics_get_account_summaries`: GA4 properties reachable.
- `google_analytics_get_property`: time zone and currency.
- `google_analytics_run_report`: metrics per landing page, channel and period.
- `google_analytics_run_pivot_report`: landing pages against device or channel in one table.
- `google_analytics_check_compatibility`: checks a dimension and metric combination before running it.

## Steps

1. Call `google_analytics_get_account_summaries` and pick the property; call `google_analytics_get_property` for the currency.
2. Call `google_analytics_run_report` over the period and the period before with `dimensions: ["landingPage"]`, `metrics: ["sessions", "engagementRate", "keyEvents", "sessionKeyEventRate", "totalRevenue"]`, ordered by sessions, `limit: 50`.
3. Call it again with `dimensions: ["landingPage", "sessionDefaultChannelGroup"]` and a `dimensionFilter` on the paid channels (Paid Search, Paid Social, Paid Shopping, Display) to see where paid traffic lands.
4. Call `google_analytics_run_pivot_report` with `dimensions: ["landingPage", "deviceCategory"]`, `metrics: ["sessions", "sessionKeyEventRate"]` and a pivot on `deviceCategory`: a page that converts on desktop but not on mobile is a mobile issue.
5. Check unusual combinations with `google_analytics_check_compatibility` before running them.
6. Rank pages by lost opportunity: sessions times the gap between the page's key event rate and the site average, for pages with enough sessions.

## Output

1. **Summary**: the pages that carry results and those that leak.
2. **Pages**: a table with sessions, engagement rate, key events, key event rate, revenue and change.
3. **Paid traffic**: where each paid channel lands and how those pages convert.
4. **Mobile and desktop**: pages with a large gap between devices.
5. **To fix first**: pages ranked by lost opportunity, with what to check on each.

## Rules

- `(not set)` landing pages are sessions GA4 could not tie to a page: report their share, do not rank them.
- A page with few sessions is not judged.
- Name the period, the currency and the key events counted.

## Examples

- "Which landing pages convert best, and which waste paid traffic?"
- "Sur quelles pages atterrit le trafic Google Ads, et convertissent-elles ?"
- "Quelles pages convertissent sur ordinateur mais pas sur mobile ?"
