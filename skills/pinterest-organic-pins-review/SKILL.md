---
name: pinterest-organic-pins-review
description: "Reviews organic Pinterest performance through the Pilot MCP server: profile, impressions, saves, outbound clicks and engagement over a period, top Pins and top video Pins, and how organic Pins compare with the Pins promoted in campaigns. Use when the user wants to know which Pins work without paid support, or which organic Pins deserve to be promoted. Triggers: Pinterest organic performance, top Pins, which Pins to promote. Déclencheurs : épingles organiques, meilleures épingles, performance Pinterest naturelle, quelles épingles sponsoriser."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Pinterest account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "pinterest_ad_accounts_list pinterest_user_account_get pinterest_user_account_analytics pinterest_user_account_top_pins pinterest_user_account_top_video_pins pinterest_campaigns_pin_analytics"
  pilot-mcp-read-only: "true"
---

# Pinterest organic Pins review

Tell the user how their organic Pins perform, which ones stand out, and which are worth promoting. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Pinterest account.
- **Optional MCP servers:** a social scheduling server to plan the next Pins. None is needed.
- **Read-only:** never create a Pin, follow an account or change a campaign.

## Tools

- `pinterest_ad_accounts_list`: the ad accounts available, with their IDs.
- `pinterest_user_account_get`: the profile, with its follower count.
- `pinterest_user_account_analytics`: organic impressions, saves, clicks and engagement over a date range.
- `pinterest_user_account_top_pins`: the best Pins of the last 90 days on a chosen metric.
- `pinterest_user_account_top_video_pins`: the best video Pins of the last 90 days.
- `pinterest_campaigns_pin_analytics`: Pin-level results inside campaigns.

## Steps

1. Call `pinterest_user_account_get` to confirm the profile.
2. Call `pinterest_user_account_analytics` for the period (last 30 days by default, YYYY-MM-DD) and the period before it, to show the trend of impressions, saves, outbound clicks and engagement.
3. Call `pinterest_user_account_top_pins` with `sort_by` on the metric that matches the goal (impressions, saves, outbound clicks or engagement), `num_of_pins: 25` and `created_in_last_n_days` up to 90.
4. Call `pinterest_user_account_top_video_pins` the same way when the account publishes video.
5. Call `pinterest_ad_accounts_list`, then `pinterest_campaigns_pin_analytics` for the same period, to see which Pins already run as ads and how they do.
6. Cross the two lists: strong organic Pins that are not promoted are the first candidates; promoted Pins that are weak organically deserve a second look.

## Output

1. **Trend**: organic impressions, saves, outbound clicks and engagement, with the change.
2. **Top Pins**: a table with the Pin, format, the chosen metric and its rank.
3. **Promote next**: strong organic Pins not yet promoted, with the reason.
4. **Review**: promoted Pins with weak organic signals.
5. **Content cues**: formats and themes that recur among the best Pins.

## Rules

- Top Pins only cover the last 90 days: say so.
- Organic metrics and ad metrics are counted differently: never add them together.
- Do not promise results from promoting a Pin.

## Examples

- "Which of our organic Pins should we promote?"
- "Fais le bilan des épingles Pinterest du dernier mois."
- "Quelles vidéos Pinterest marchent le mieux sans publicité ?"
