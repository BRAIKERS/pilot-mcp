---
name: seo-content-brief
description: "Writes an SEO content brief for one target keyword through the Pilot MCP server: reads the live Google results (top pages, featured snippet, people also ask), the search intent, related searches and questions with their volume, and the pages the site already has on the topic, then outlines the page to write. Use before writing or rewriting a blog post, guide or landing page. Triggers: SEO content brief, outline for a blog post, what should this page cover. Déclencheurs : brief SEO, plan d'article SEO, brief de contenu, que doit couvrir cette page."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with DataForSEO attached. Semrush and WordPress are optional."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "dataforseo_serp_google_organic dataforseo_search_intent dataforseo_related_keywords dataforseo_keyword_search_volume semrush_get_api_units_status semrush_get_keyword_questions wordpress_list_content"
  pilot-mcp-read-only: "true"
---

# SEO content brief

Give the writer a brief for one page: what the searcher wants, what the top results cover, what to add, and how to structure it. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on a workspace with DataForSEO attached.
- **Optional MCP servers:** a document server to save the brief. None is needed. Semrush and WordPress, when attached to the workspace, add questions and existing pages.
- **Read-only:** never publish or change content. The brief is for a writer.

## Tools

- `dataforseo_serp_google_organic`: the live Google results for the keyword.
- `dataforseo_search_intent`: the intent of the keyword and its variants.
- `dataforseo_related_keywords`: related searches to cover.
- `dataforseo_keyword_search_volume`: volume of the main keyword and variants.
- `semrush_get_api_units_status`: Semrush units left, before any Semrush report.
- `semrush_get_keyword_questions`: questions asked around the keyword.
- `wordpress_list_content`: pages and posts the site already has on the topic.

## Steps

1. Ask for the target keyword, the country, the language and the page to create or rewrite.
2. Call `dataforseo_serp_google_organic` with `depth` 10 to 20: titles and URLs of the top results, featured snippet, people also ask, other blocks (videos, shopping, local). Note the dominant page type (guide, list, product page, tool).
3. Call `dataforseo_search_intent` on the keyword; if the intent does not match the page the user plans, say so first.
4. Call `dataforseo_related_keywords` with `depth` 1 and `dataforseo_keyword_search_volume` on the main keyword and variants.
5. When Semrush is attached, call `semrush_get_api_units_status`, then `semrush_get_keyword_questions` with a low `limit`.
6. When WordPress is attached, call `wordpress_list_content` with a full-text search on the keyword: an existing page may need a rewrite rather than a new page, and two pages on the same topic would compete.
7. Write the brief.

## Output

1. **Target**: keyword, variants, volume, intent, page type to produce.
2. **What ranks**: the top results and what they share.
3. **Outline**: suggested title, meta description, H1, H2 and H3 sections, each with what it must answer.
4. **Questions**: to answer in the page or in an FAQ.
5. **Gaps to fill**: what the top results miss and the page can add.
6. **Internal links**: existing pages to link from and to.

## Rules

- Never copy text from competing pages: describe what they cover.
- Volumes are provider estimates: name the provider and country.
- Do not promise a ranking.

## Examples

- "Write an SEO brief for the keyword best CRM for small business."
- "Fais le brief SEO de l'article sur l'isolation des combles."
- "Que doit couvrir notre page sur la facture électronique pour se positionner ?"
