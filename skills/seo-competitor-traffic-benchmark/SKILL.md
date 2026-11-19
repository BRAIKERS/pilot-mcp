---
name: seo-competitor-traffic-benchmark
description: "Benchmarks a site's estimated web traffic against competitors through the Pilot MCP server with Semrush Traffic Analytics: visits, unique visitors, pages per visit, visit duration and bounce rate, traffic sources (direct, search, referral, social, paid), the most visited pages of each competitor, and the organic and paid traffic estimates. Use when the user wants to size competitors, see where their traffic comes from, or find their entry pages. Triggers: competitor traffic, traffic benchmark, where competitors get traffic. Déclencheurs : trafic des concurrents, comparatif d'audience, sources de trafic des concurrents."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with Semrush attached, with access to Traffic Analytics."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "semrush_get_api_units_status semrush_get_traffic_summary semrush_get_traffic_sources semrush_get_traffic_top_pages semrush_get_domain_overview"
  pilot-mcp-read-only: "true"
---

# SEO competitor traffic benchmark

Compare the site's estimated traffic with its competitors', show where each one's visitors come from and which pages bring them in. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on a workspace with Semrush attached.
- **Optional MCP servers:** an analytics server to compare the estimate with the site's own measured traffic. None is needed.
- **Read-only:** nothing is changed. Reports consume the client's Semrush units.

## Tools

- `semrush_get_api_units_status`: Semrush units left; call it first, it is free.
- `semrush_get_traffic_summary`: visits, unique visitors, pages per visit, duration and bounce rate for several domains at once.
- `semrush_get_traffic_sources`: the share of direct, referral, search, social and paid traffic of a domain.
- `semrush_get_traffic_top_pages`: the most visited pages of a domain.
- `semrush_get_domain_overview`: estimated organic and paid search traffic and keyword counts.

## Steps

1. Ask for the site and up to five competitors (domains without protocol) and the country.
2. Call `semrush_get_api_units_status` and say what the planned reports will use.
3. Call `semrush_get_traffic_summary` once with all domains in `targets` and the `country`.
4. Call `semrush_get_traffic_sources` for each domain to compare the channel mix.
5. Call `semrush_get_traffic_top_pages` for each competitor with a small `limit` to find their entry pages: guides, tools, category pages, offers.
6. Call `semrush_get_domain_overview` for each domain, with the `database` of the country, to split search traffic between organic and paid.
7. Read the gaps: a competitor much stronger on organic search, a channel the site does not use, entry pages with no equivalent on the site.

## Output

1. **Summary**: where the site stands and the main gap.
2. **Benchmark table**: domain, visits, unique visitors, pages per visit, duration, bounce rate.
3. **Channel mix**: share of each source per domain.
4. **Competitor entry pages**: the pages that bring them visitors, and whether the site has an equivalent.
5. **Next steps**: what to build or test.

## Rules

- These are third-party estimates, not measured traffic: say so, and do not compare them with the site's analytics as if they were the same measure.
- Name the country and the period Semrush uses.
- A figure Semrush did not return is absent, not zero.

## Examples

- "How does our traffic compare with our three main competitors?"
- "D'où vient le trafic de nos concurrents ?"
- "Quelles pages attirent le plus de visiteurs chez le concurrent X ?"
