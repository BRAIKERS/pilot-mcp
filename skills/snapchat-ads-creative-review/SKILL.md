---
name: snapchat-ads-creative-review
description: "Reviews Snapchat Ads creatives through the Pilot MCP server: creatives with their type, headline, call to action and destination, the media behind them, the ads that use each creative with their status and review state, and creatives reused across many ads. Use when the user asks which Snapchat creatives run, why an ad was rejected, or what to refresh. Triggers: Snapchat creatives review, Snap ad rejected, refresh Snapchat ads. Déclencheurs : créas Snapchat, publicité Snap refusée, renouveler les créas Snapchat, revue des créations Snap."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Snapchat ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "search snapchat_get_campaign snapchat_list_creatives snapchat_get_creative snapchat_list_media snapchat_get_media snapchat_list_ads snapchat_get_ad snapchat_list_ad_squads"
  pilot-mcp-read-only: "true"
---

# Snapchat Ads creative review

Tell the user which creatives run on Snapchat, which ads are blocked, and which creatives carry too many ads or need a refresh. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Snapchat ad account.
- **Optional MCP servers:** a file storage server holding the brand's creative assets. None is needed.
- **Read-only:** never upload media, create or update a creative, or change an ad.

## Tools

- `search`: finds campaigns of the workspace by name.
- `snapchat_get_campaign`: one campaign, including its ad account ID.
- `snapchat_list_creatives`: the creatives of the account.
- `snapchat_get_creative`: one creative with headline, brand name, call to action and media.
- `snapchat_list_media`: the media files of the account.
- `snapchat_get_media`: one media file with its type, duration and state.
- `snapchat_list_ads`: ads with their creative, status and review state.
- `snapchat_get_ad`: one ad in full, including any rejection reason.
- `snapchat_list_ad_squads`: ad squads, to place each ad in its context.

## Steps

1. Get the ad account ID from the user, or from `snapchat_get_campaign` on a campaign found with `search`.
2. Call `snapchat_list_ads` and `snapchat_list_ad_squads`. Keep the ads of active ad squads.
3. For ads that are rejected, pending or paused for review, call `snapchat_get_ad` and quote the reason Snapchat gives.
4. Call `snapchat_list_creatives`. Map each creative to the ads that use it. Flag a creative carried by many active ads: one weak creative then weighs on the whole account.
5. Call `snapchat_get_creative` on the creatives of active ads: headline, brand name, call to action, destination URL. Flag missing calls to action and destinations that differ from the campaign goal.
6. Call `snapchat_list_media`, then `snapchat_get_media` on media in error or with an unusual format or duration.
7. Ask the user for the date each creative went live if the data does not show it: a creative running for many weeks is a refresh candidate.

## Output

1. **Summary**: active creatives, blocked ads, and the main risk.
2. **Blocked ads**: a table with the ad, its ad squad, status and reason.
3. **Creatives**: a table with type, headline, call to action, destination and number of active ads.
4. **Refresh**: creatives to renew first, with the reason.
5. **Brief**: what the next creatives should fix, for the creative team.

## Rules

- Quote Snapchat's rejection reason as given; do not guess another one.
- Without results per ad, judge setup and reuse, not performance, and say so.
- A field Snapchat did not return is absent.

## Examples

- "Why was our Snapchat ad rejected?"
- "Quelles créas Snapchat tournent en ce moment, et lesquelles renouveler ?"
- "Fais la revue des créations Snap de la campagne de Noël."
