---
name: seo-backlink-review
description: "Reviews a site's backlink profile through the Pilot MCP server with DataForSEO or Semrush: number of backlinks and referring domains, authority and spam signals, follow and nofollow split, the strongest referring domains, anchor text distribution and over-optimized anchors, compared with one or two competitors. Use when the user asks about links, domain authority, a link drop or toxic links, or wants link building ideas. Triggers: backlink audit, referring domains, anchor text review. Déclencheurs : audit des backlinks, domaines référents, profil de liens, ancres de liens, netlinking."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with DataForSEO or Semrush attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "dataforseo_backlinks_summary dataforseo_backlinks_referring_domains dataforseo_backlinks_anchors semrush_get_api_units_status semrush_get_backlinks_overview semrush_get_referring_domains semrush_get_backlink_anchors semrush_get_backlinks"
  pilot-mcp-read-only: "true"
---

# SEO backlink review

Tell the user how strong and how clean their link profile is, how it compares with competitors, and where links could come from. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on a workspace with DataForSEO or Semrush attached. DataForSEO first when both are.
- **Optional MCP servers:** a spreadsheet server to export the domain lists. None is needed.
- **Read-only:** nothing is changed. Semrush backlink reports are billed per row in Semrush units: keep limits low.

## Tools

- `dataforseo_backlinks_summary`: backlinks, referring domains, rank and spam score of a target.
- `dataforseo_backlinks_referring_domains`: referring domains with rank and backlink count.
- `dataforseo_backlinks_anchors`: anchor texts aggregated by anchor.
- `semrush_get_api_units_status`: Semrush units left; call it before any Semrush report.
- `semrush_get_backlinks_overview`: backlinks, referring domains, authority score and follow split.
- `semrush_get_referring_domains`: referring domains with authority score.
- `semrush_get_backlink_anchors`: anchors with backlinks and domains per anchor.
- `semrush_get_backlinks`: individual backlinks, for a short sample only.

## Steps

1. Ask for the domain and one or two competitors.
2. Overview: `dataforseo_backlinks_summary` for each, or `semrush_get_api_units_status` then `semrush_get_backlinks_overview` (flat cost per request).
3. Referring domains: `dataforseo_backlinks_referring_domains` or `semrush_get_referring_domains` with a `limit` of 50 to 100. Rank by authority; note the share of links from a handful of domains, and domains that look like spam networks.
4. Anchors: `dataforseo_backlinks_anchors` or `semrush_get_backlink_anchors`. A large share of exact commercial anchors is a risk; brand and URL anchors should lead.
5. When the user wants examples, `semrush_get_backlinks` with `limit` 20 at most.
6. Compare with competitors: referring domains they have and the site lacks are the first link building leads.
7. Set `confirm_units` on a Semrush call only after the user agrees to a large one.

## Output

1. **Summary**: profile strength, main risk, main opportunity.
2. **Benchmark**: a table with backlinks, referring domains, authority and follow share for the site and its competitors.
3. **Top referring domains**: the strongest, and any suspicious ones.
4. **Anchors**: distribution and over-optimized anchors.
5. **Link leads**: domains that link to competitors and not to the site.

## Rules

- Authority and spam scores are provider metrics, not Google's: say so.
- Name the provider for every figure; the two providers count differently.
- Do not advise disavowing links without clear evidence of spam.

## Examples

- "Review our backlink profile against our two main competitors."
- "Fais l'audit des liens entrants du site."
- "Nos ancres de liens sont-elles trop optimisées ?"
