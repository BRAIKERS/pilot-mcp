---
name: search-console-sitemap-management
description: "Submits or re-submits sitemaps to Google Search Console through the Pilot MCP server, with the user's explicit approval before every write: reads the sitemaps already known, checks that the new one belongs to the property, submits it, reads it back, and spot-checks a few of its URLs. Use after a migration, a new section or a sitemap URL change. Triggers: submit sitemap, resubmit sitemap Search Console, new sitemap. Déclencheurs : soumettre un sitemap, renvoyer le sitemap à Search Console, nouveau plan de site."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Search Console site attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_search_console_list_sites google_search_console_get_sitemaps google_search_console_get_sitemap google_search_console_submit_sitemap google_search_console_get_url_inspection"
  pilot-mcp-read-only: "false"
---

# Search Console sitemap management

Submit the sitemaps the user asks for to Google Search Console, one at a time, each after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Search Console site.
- **Optional MCP servers:** none is needed.
- **Writes:** the write tool needs the paid Pilot MCP plan and a workspace role allowed to write, and an owner or full-user permission on the property. If it is not served, say so and give the steps to submit the sitemap by hand in Search Console.

## Tools

- `google_search_console_list_sites`: the properties available, with permission level.
- `google_search_console_get_sitemaps`: sitemaps already known for the property.
- `google_search_console_get_sitemap`: one sitemap with its state and counts.
- `google_search_console_submit_sitemap`: submits or re-submits a sitemap (write).
- `google_search_console_get_url_inspection`: spot-checks URLs of the sitemap.

## Confirmation

A submission tells Google to read the sitemap again. For each sitemap:

1. Read the current state first with `google_search_console_get_sitemaps` and, if it is known, `google_search_console_get_sitemap`.
2. Show the person the exact property and sitemap URL, whether Google already knows it, and its last read date and errors.
3. Wait for an explicit yes for that submission. A yes covers one sitemap only.
4. Submit one sitemap at a time.
5. Never delete a sitemap. If the person asks to remove one, say that this skill does not remove, and that a deletion must be asked for separately, naming the sitemap.
6. After the submission, read the sitemap again and show the before and after.
7. If the submission is refused, stop, report the message, and do not submit the next one.

## Steps

1. Call `google_search_console_list_sites` and confirm the property and the permission level.
2. Check that the sitemap URL sits under the property (same domain, and same prefix for a URL-prefix property). Refuse a sitemap from another site.
3. Call `google_search_console_get_sitemaps` to see what is known; flag old sitemaps that still point to a former domain or structure, for the person to review.
4. Submit with `google_search_console_submit_sitemap` under the Confirmation rules.
5. Call `google_search_console_get_sitemap` again. Google may take hours or days to read it: say so if the counts are still empty.
6. Call `google_search_console_get_url_inspection` on two or three URLs of the sitemap to show their current state as a baseline.

## Output

1. **Plan**: the sitemaps to submit, with what Google knows of each.
2. **Log**: for each submission, before, after and the time it was made.
3. **Baseline**: the state of the spot-checked URLs.
4. **Not done**: anything refused or left for a person.

## Rules

- A submission does not force indexing: do not promise it.
- Never act on a property other than the one confirmed.
- Never chain several submissions on one yes.

## Examples

- "Submit our new sitemap to Search Console."
- "Renvoie le sitemap des produits à Google après la migration."
- "Google connaît-il notre sitemap blog ? Sinon, soumets-le."
