---
name: reddit-ads-campaign-management
description: "Manages Reddit Ads campaigns, ad groups and ads through the Pilot MCP server, with the user's explicit approval before every write: pause or reactivate, change a campaign or ad group budget, an ad group bid or a campaign spend cap, and create campaigns and ad groups, always paused. Reads the current state first and shows the before and after. Use when the user asks to change something in a Reddit Ads account. Triggers: pause Reddit campaign, change Reddit budget, Reddit bid change. Déclencheurs : mettre en pause une campagne Reddit, changer le budget Reddit, modifier une enchère Reddit, créer une campagne Reddit."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Reddit Ads account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "reddit_ads_get_ad_account reddit_ads_list_campaigns reddit_ads_list_ad_groups reddit_ads_list_ads reddit_ads_get_statistics reddit_ads_update_campaign_status reddit_ads_update_ad_group_status reddit_ads_update_ad_status reddit_ads_update_campaign_budget reddit_ads_update_ad_group_budget reddit_ads_update_ad_group_bid reddit_ads_update_campaign_spend_cap reddit_ads_create_campaign reddit_ads_create_ad_group"
  pilot-mcp-read-only: "false"
---

# Reddit Ads campaign management

Apply the changes the user asks for in a Reddit Ads account, one at a time, each after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Reddit Ads account.
- **Optional MCP servers:** a team messaging server to log the changes made. None is needed.
- **Writes:** the write tools need the paid Pilot MCP plan and a workspace role allowed to write. If a write tool is not served, say so and return the list of changes for a person to apply by hand in Reddit Ads.

## Tools

- `reddit_ads_get_ad_account`: the account, its currency and status.
- `reddit_ads_list_campaigns`: campaigns with status, campaign budget and spend cap.
- `reddit_ads_list_ad_groups`: ad groups with status, budget, bid and bid strategy.
- `reddit_ads_list_ads`: ads with status.
- `reddit_ads_get_statistics`: recent spend and conversions, to ground each change.
- `reddit_ads_update_campaign_status`: pauses or reactivates a campaign (write).
- `reddit_ads_update_ad_group_status`: pauses or reactivates an ad group (write).
- `reddit_ads_update_ad_status`: pauses or reactivates an ad (write).
- `reddit_ads_update_campaign_budget`: changes a campaign budget, when the budget lives on the campaign (write).
- `reddit_ads_update_ad_group_budget`: changes an ad group budget, when the campaign has no campaign budget (write).
- `reddit_ads_update_ad_group_bid`: changes an ad group bid, when its strategy has one (write).
- `reddit_ads_update_campaign_spend_cap`: changes a campaign's lifetime spend cap (write).
- `reddit_ads_create_campaign`: creates one campaign, always paused (write).
- `reddit_ads_create_ad_group`: creates one ad group in a campaign, always paused (write).

## Confirmation

Every write changes a live ad account and can move money. For each change:

1. Read the current state first with `reddit_ads_list_campaigns`, `reddit_ads_list_ad_groups` or `reddit_ads_list_ads`, filtered on the object's ID.
2. Show the person the exact object (name, ID, ad account), the current value and the proposed value, in the account currency.
3. Wait for an explicit yes for that write. A yes covers one write only. Reactivating puts money back at stake: say so.
4. Make one write at a time.
5. New campaigns and ad groups are created paused by Reddit through these tools; activation is a separate, confirmed change.
6. Never delete anything. If the person asks for a deletion, say that this skill pauses instead, and that a deletion must be asked for separately, naming the object.
7. After the write, read the object again and show the before and after.
8. If a write is refused or fails, stop, report the message, and do not attempt the next change.

## Steps

1. Call `reddit_ads_get_ad_account` and confirm the account and its currency.
2. Find the objects named by the user with the list tools. If a name matches several objects, ask which one.
3. When the change is about money, call `reddit_ads_get_statistics` with `level: "CAMPAIGN"` or `"AD_GROUP"` over the last 14 days to show recent spend and conversions next to the proposal.
4. Turn the request into a numbered list of single changes and show it before any write.
5. Pick the right budget tool: a campaign with a campaign budget takes `reddit_ads_update_campaign_budget`; otherwise the budget lives on the ad groups and takes `reddit_ads_update_ad_group_budget`. A bid change is refused for BIDLESS and MAXIMIZE_VOLUME ad groups.
6. Apply each change under the Confirmation rules.
7. End with the log of changes made and not made.

## Output

1. **Plan**: the numbered list of changes, before any write.
2. **Log**: for each change, the object, before, after and the time it was made.
3. **Not done**: changes refused, failed or left for a person, with the reason.

## Rules

- Amounts are in units of the account currency (12.5 means 12.50): name it.
- Never chain several writes on one yes.
- Never act on an object of another account than the one confirmed.

## Examples

- "Pause the Reddit ad group Gaming subreddits."
- "Baisse le budget de la campagne Reddit Lancement à 50 euros par jour."
- "Crée une campagne Reddit de trafic, en pause, avec un groupe d'annonces."
