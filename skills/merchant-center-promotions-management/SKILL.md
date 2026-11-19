---
name: merchant-center-promotions-management
description: "Creates and changes Google Merchant Center promotions through the Pilot MCP server, with the user's explicit approval before every write: new promotions with title, offer, discount, minimum purchase, products, dates and channel, and changes to an existing promotion's title, periods or discount. Each write is simulated first, then applied and read back. Use when the user wants to set up or adjust a promotion on Google Shopping. Triggers: create Merchant Center promotion, change promotion dates, Google Shopping promotion. Déclencheurs : créer une promotion Merchant Center, modifier les dates d'une promotion, promotion Google Shopping."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Merchant Center account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "merchant_center_list_accounts merchant_center_list_data_sources merchant_center_list_promotions merchant_center_get_promotion merchant_center_insert_promotion merchant_center_update_promotion"
  pilot-mcp-read-only: "false"
---

# Merchant Center promotions management

Set up or adjust the Merchant Center promotions the user asks for, one at a time, each after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Merchant Center account.
- **Optional MCP servers:** none is needed.
- **Writes:** the write tools need the paid Pilot MCP plan and a workspace role allowed to write. If a write tool is not served, say so and return the promotion details for a person to enter in Merchant Center.

## Tools

- `merchant_center_list_accounts`: the accounts available.
- `merchant_center_list_data_sources`: the data sources, to pick the promotion source.
- `merchant_center_list_promotions`: existing promotions, to avoid duplicates.
- `merchant_center_get_promotion`: one promotion in full.
- `merchant_center_insert_promotion`: creates or replaces a promotion (write).
- `merchant_center_update_promotion`: changes allowed attributes of an existing promotion (write).

## Confirmation

A promotion is shown to shoppers on Google once approved. For each write:

1. Read the current state first with `merchant_center_list_promotions` and `merchant_center_get_promotion`.
2. Show the person the exact promotion (account, ID, title), every field as it is now and as proposed: offer, discount, minimum purchase, products, validity and display periods, channel, country.
3. Run the write tool with `dry_run: true` and show the simulation.
4. Wait for an explicit yes for that write. A yes covers one promotion only.
5. Send the write with `dry_run: false` (and `confirm: true` when the tool asks for it).
6. Promotions cannot be deleted through the API: to stop one early, propose ending its validity period, as its own confirmed write.
7. After the write, read the promotion again and show the before and after. If a write is refused or fails, stop, report the message, and do not attempt the next one.

## Steps

1. Call `merchant_center_list_accounts` and confirm the account.
2. Call `merchant_center_list_data_sources` to find the promotion data source; `merchant_center_insert_promotion` requires it.
3. Call `merchant_center_list_promotions` to check that no promotion with the same ID or the same offer already exists.
4. Build the promotion: `promotionId`, content language, target country, redemption channel, offer type, long title, discount, dates. Leave time for Google's review before the start date, and say so.
5. Create it with `merchant_center_insert_promotion`, or change an existing one with `merchant_center_update_promotion` passing only the attributes to change (long title, periods, percent or amount off, minimum purchase, and the other attributes the tool allows).
6. Read it back and report its review status.

## Output

1. **Plan**: the promotions to create or change, field by field.
2. **Log**: for each write, the simulation, before, after and the time it was made.
3. **Not done**: writes refused, failed or left for a person.

## Rules

- Dates carry a time zone: confirm it with the user.
- The promotion must match the offer on the site: remind the user that Google checks it.
- Never chain several writes on one yes.

## Examples

- "Create a 20% off promotion on Merchant Center for the winter collection, from December 1 to 7."
- "Prolonge la promotion Livraison offerte jusqu'à dimanche."
- "Passe la remise de la promotion Rentrée de 10 à 15 %."
