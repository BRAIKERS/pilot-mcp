---
name: wordpress-content-audit
description: "Audits the content of a WordPress site in read-only mode through the Pilot MCP server: posts and pages with status, dates and categories, thin or outdated content, missing excerpts, media without alternative text, categories left empty or overloaded, and, when Search Console is attached, which pages earn organic clicks and which earn none. Returns what to update, merge, keep or retire. Use when the user wants a content inventory, a blog cleanup or an SEO content audit. Triggers: WordPress content audit, blog cleanup, content inventory. Déclencheurs : audit de contenu WordPress, nettoyage du blog, inventaire des contenus, contenus à mettre à jour."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a WordPress site attached. Search Console and DataForSEO are optional."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "wordpress_get_site_info wordpress_list_content wordpress_get_content wordpress_list_categories wordpress_list_media wordpress_get_media google_search_console_list_sites google_search_console_get_search_analytics dataforseo_onpage_instant_audit"
  pilot-mcp-read-only: "true"
---

# WordPress content audit

Give the user an inventory of their WordPress content and a clear verdict on each piece: update, merge, keep or retire. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the WordPress site.
- **Optional MCP servers:** a spreadsheet server to export the inventory. None is needed. Search Console and DataForSEO, when attached to the workspace, add traffic and technical checks.
- **Read-only:** never create, update or delete content or media.

## Tools

- `wordpress_get_site_info`: the site's identity, API namespaces and the connected user's roles.
- `wordpress_list_content`: posts or pages with status, slug, link and dates.
- `wordpress_get_content`: one post or page in full.
- `wordpress_list_categories`: categories or tags with their post counts.
- `wordpress_list_media`: media in the library.
- `wordpress_get_media`: one media item with its alternative text and the content it belongs to.
- `google_search_console_list_sites`: the Search Console properties available.
- `google_search_console_get_search_analytics`: organic clicks and impressions by page.
- `dataforseo_onpage_instant_audit`: an on-page check of one URL.

## Steps

1. Call `wordpress_get_site_info`.
2. Call `wordpress_list_content` for `post` and for `page`, with `status: "publish,draft"`, `per_page: 100` and every page of results.
3. Call `wordpress_list_categories` for categories and for tags: empty ones, near-duplicates, and one category holding most posts.
4. When Search Console is attached, call `google_search_console_get_search_analytics` over 12 months with `dimensions: ["page"]` and join by URL: pages with clicks, pages with impressions but few clicks, pages with nothing.
5. Call `wordpress_get_content` on a sample: the most visited, the ones with no traffic, the oldest. Check length, headings, excerpt, date of last update, internal links, and outdated facts (years, prices, offers) to flag for review.
6. Call `wordpress_list_media` and `wordpress_get_media` on images used by key pages: missing alternative text.
7. Call `dataforseo_onpage_instant_audit` on a few key URLs when DataForSEO is attached.
8. Classify each piece: update (traffic potential, outdated), merge (several thin posts on one topic), keep, retire (no traffic, no purpose, with a redirect to a related page). Old drafts get their own list.

## Output

1. **Summary**: counts by verdict and the three biggest wins.
2. **Inventory**: a table with title, type, status, last update, clicks, verdict and reason.
3. **Merge groups**: posts to combine, with the page to keep.
4. **Taxonomy**: categories and tags to clean.
5. **Media**: images without alternative text on key pages.

## Rules

- Never recommend deleting a page that earns clicks or links without a redirect.
- Traffic comes from Search Console when attached; otherwise say that verdicts rest on content only.
- Flag dated facts; do not rewrite them.

## Examples

- "Audit our WordPress blog and tell me what to update or remove."
- "Fais l'inventaire des contenus du site WordPress."
- "Quels articles du blog n'apportent aucun trafic ?"
