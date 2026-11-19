---
name: seo-brand-mentions-review
description: "Finds where a brand is mentioned on the web through the Pilot MCP server with DataForSEO: mentions with their source, date and sentiment, the sites that mention the brand without linking to it, and the link profile they would add to, for digital PR and link building. Use when the user wants to know what the web says about the brand, track a launch, or find unlinked mentions to turn into links. Triggers: brand mentions, unlinked mentions, what the web says about us. Déclencheurs : mentions de marque, mentions sans lien, ce que le web dit de nous, relations presse en ligne."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with DataForSEO attached. Semrush is optional."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "dataforseo_content_search dataforseo_backlinks_summary dataforseo_backlinks_referring_domains semrush_get_api_units_status semrush_get_referring_domains"
  pilot-mcp-read-only: "true"
---

# SEO brand mentions review

Show the user where the brand is talked about, in what tone, and which mentions could become links. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on a workspace with DataForSEO attached.
- **Optional MCP servers:** a CRM or email server to track outreach. None is needed. Semrush, when attached, gives a second list of referring domains.
- **Read-only:** nothing is changed and nobody is contacted.

## Tools

- `dataforseo_content_search`: mentions of a term across the web, with sentiment and social data.
- `dataforseo_backlinks_summary`: the brand site's link profile.
- `dataforseo_backlinks_referring_domains`: the domains that already link to the site.
- `semrush_get_api_units_status`: Semrush units left, before any Semrush report.
- `semrush_get_referring_domains`: referring domains as Semrush sees them.

## Steps

1. Ask for the brand name, its variants and product names, and the site's domain.
2. Call `dataforseo_content_search` for each variant with a `limit` the user accepts. Keep source domain, URL, date, sentiment and context.
3. Remove the brand's own sites and social profiles, and mentions of a homonym.
4. Call `dataforseo_backlinks_summary` and `dataforseo_backlinks_referring_domains` on the domain. When Semrush is attached, call `semrush_get_api_units_status`, then `semrush_get_referring_domains` with a low `limit`.
5. Cross the two lists: mentioning domains that do not appear among referring domains are unlinked mentions, the easiest links to ask for.
6. Sort mentions by sentiment: negative ones need an answer before any link request.

## Output

1. **Summary**: volume of mentions, tone, and the number of unlinked mentions.
2. **Mentions**: a table with source, date, sentiment and context.
3. **Unlinked mentions**: domains to contact first, with their page.
4. **Negative mentions**: to handle before any outreach.

## Rules

- A domain absent from the referring domains list may still link through a page the provider has not crawled: present unlinked mentions as likely, not certain.
- Sentiment is the provider's automatic score: say so.
- Never contact anyone; the outreach list is for a person.

## Examples

- "Where is our brand mentioned online without a link?"
- "Que dit le web de notre nouvelle gamme depuis son lancement ?"
- "Trouve les articles qui citent la marque sans lien vers le site."
