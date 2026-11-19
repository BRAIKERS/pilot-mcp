---
name: wordpress-content-publishing
description: "Creates and updates WordPress posts and pages through the Pilot MCP server, with the user's explicit approval before every write: drafts first, categories, excerpt and slug set, images imported into the media library with alternative text, and publication or change of live content only on an explicit yes, since it is public at once. Reads the current content first and shows the before and after. Use when the user wants an article drafted into WordPress, a page updated, or a draft published. Triggers: publish on WordPress, create WordPress draft, update WordPress page. Déclencheurs : publier sur WordPress, créer un brouillon WordPress, mettre à jour une page WordPress."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a WordPress site attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "wordpress_get_site_info wordpress_list_content wordpress_get_content wordpress_list_categories wordpress_list_media wordpress_create_content wordpress_update_content wordpress_upload_media_from_url wordpress_update_media"
  pilot-mcp-read-only: "false"
---

# WordPress content publishing

Put the user's content into WordPress as drafts, and publish or change live content only after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the WordPress site.
- **Optional MCP servers:** a document server holding the text to publish. None is needed.
- **Writes:** the write tools need the paid Pilot MCP plan, a workspace role allowed to write, and a WordPress user allowed to edit. If a write tool is not served, say so and return the content ready to paste into WordPress by hand.

## Tools

- `wordpress_get_site_info`: the site and the connected user's roles.
- `wordpress_list_content`: existing posts and pages, to avoid duplicates.
- `wordpress_get_content`: one post or page in full.
- `wordpress_list_categories`: categories and tags with their IDs.
- `wordpress_list_media`: media already in the library.
- `wordpress_create_content`: creates a post or page, as a draft by default (write).
- `wordpress_update_content`: changes a post or page (write).
- `wordpress_upload_media_from_url`: imports an image from a public URL into the library (write).
- `wordpress_update_media`: changes the title, alternative text or caption of a media item (write).

## Confirmation

A published page is public at once, and an update to live content is public at once. For each write:

1. Read the current state first: `wordpress_list_content` with a full-text search to find duplicates, `wordpress_get_content` before any update.
2. Show the person the exact target (site, content type, title, ID, status), the current text or value and the proposed one.
3. Wait for an explicit yes for that write. A yes covers one write only.
4. Make one write at a time.
5. Create every post and page as a draft. Publishing, or changing content that is already published, is a separate write that needs its own yes, then `confirm: true`.
6. Never delete content or media. A removal must be asked for separately, naming the item.
7. After the write, read the content again and show the before and after, with the draft or public link.
8. If a write is refused or fails, stop, report the message, and do not attempt the next one.

## Steps

1. Call `wordpress_get_site_info` and confirm the site and the user's role.
2. Call `wordpress_list_content` with a full-text search on the title or topic: if a similar post exists, propose an update instead of a new post.
3. Call `wordpress_list_categories` to pick existing category IDs; do not invent new ones.
4. Images: reuse from `wordpress_list_media`, or import with `wordpress_upload_media_from_url` with a file name and alternative text; fix alternative text with `wordpress_update_media`.
5. Create the draft with `wordpress_create_content`: title, HTML content, excerpt, slug, categories, `status: "draft"`.
6. Updates: `wordpress_update_content` with only the fields that change. Content replaces the whole body: start from the text read with `wordpress_get_content`.
7. Publish only on an explicit yes: `wordpress_update_content` with `status: "publish"` and `confirm: true`.
8. End with the log of writes made and not made.

## Output

1. **Plan**: the writes to make, before any write.
2. **Log**: for each write, the target, before, after, status and link.
3. **Not done**: writes refused, failed or left for a person.

## Rules

- Never publish on the same yes that approved a draft.
- Keep the user's text as given; propose edits separately.
- Never chain several writes on one yes.

## Examples

- "Put this article into WordPress as a draft in the Guides category."
- "Mets à jour le titre et l'introduction de la page Tarifs, puis montre-moi l'avant et l'après."
- "Publie le brouillon « Bien choisir sa pompe à chaleur »."
