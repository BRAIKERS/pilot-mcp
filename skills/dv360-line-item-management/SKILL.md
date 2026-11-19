---
name: dv360-line-item-management
description: "Manages Display & Video 360 line items through the Pilot MCP server, with the user's explicit approval before every write: activate or pause, rename, set a fixed budget, assign creatives, duplicate a line item, edit its targeting, and change an insertion order's status. Each write is validated and simulated first, then applied and read back. Use when the user asks to change something in DV360 line items. Triggers: pause DV360 line item, change DV360 budget, assign creatives DV360. Déclencheurs : mettre en pause une ligne DV360, changer le budget d'une ligne DV360, ajouter des créations DV360, modifier le ciblage DV360."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Display & Video 360 advertiser attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "dv360_get_advertiser dv360_list_insertion_orders dv360_get_insertion_order dv360_list_line_items dv360_get_line_item dv360_list_assigned_targeting dv360_list_creatives dv360_validate_operation dv360_update_line_item dv360_set_line_item_budget dv360_assign_creative_to_line_item dv360_duplicate_line_item dv360_update_line_item_targeting dv360_update_insertion_order"
  pilot-mcp-read-only: "false"
---

# DV360 line item management

Apply the line item changes the user asks for in Display & Video 360, one at a time, each after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the DV360 advertiser.
- **Optional MCP servers:** a team messaging server to log the changes made. None is needed.
- **Writes:** the write tools need the paid Pilot MCP plan and a workspace role allowed to write. If a write tool is not served, say so and return the list of changes for a person to apply by hand in DV360.

## Tools

- `dv360_get_advertiser`: the advertiser's currency.
- `dv360_list_insertion_orders`: insertion orders.
- `dv360_get_insertion_order`: one insertion order, with its budget allocation.
- `dv360_list_line_items`: line items of an insertion order.
- `dv360_get_line_item`: one line item in full.
- `dv360_list_assigned_targeting`: the current targeting of a line item.
- `dv360_list_creatives`: creatives available.
- `dv360_validate_operation`: checks a payload before it is sent.
- `dv360_update_line_item`: changes name or status of a line item (write).
- `dv360_set_line_item_budget`: sets a fixed line item budget in micros (write).
- `dv360_assign_creative_to_line_item`: adds creatives to a line item (write).
- `dv360_duplicate_line_item`: copies a line item into the same insertion order (write).
- `dv360_update_line_item_targeting`: adds or removes targeting options on line items (write).
- `dv360_update_insertion_order`: changes name, status or objective of an insertion order (write).

## Confirmation

Every write changes live delivery and can move money. For each change:

1. Read the current state first with `dv360_get_line_item`, `dv360_get_insertion_order` or `dv360_list_assigned_targeting`.
2. Show the person the exact object (name, ID, advertiser), the current value and the proposed value, in the advertiser currency.
3. Run `dv360_validate_operation` when a payload is built, then the write tool with `dry_run: true`, and show the simulation.
4. Wait for an explicit yes for that write. A yes covers one write only, on one line item.
5. Send the write with `dry_run: false` (and `confirm: true` when the tool asks for it). A duplicated line item keeps a paused or draft state until activated by a separate, confirmed change.
6. Never archive or delete anything. Removing a targeting option is allowed only when the person names it; otherwise only add.
7. After the write, read the object again and show the before and after. If a write is refused or fails, stop, report the message, and do not attempt the next change.

## Steps

1. Call `dv360_get_advertiser` and confirm the advertiser; pass `advertiser_id` everywhere.
2. Find the objects with `dv360_list_insertion_orders` and `dv360_list_line_items`. If a name matches several, ask which one.
3. Turn the request into a numbered list of single changes and show it before any write.
4. Status or name: `dv360_update_line_item` (`ENTITY_STATUS_ACTIVE` or `ENTITY_STATUS_PAUSED`), or `dv360_update_insertion_order` for an insertion order.
5. Budget: `dv360_set_line_item_budget` with `budget_max_micros` (50 units = 50,000,000). It is refused when the insertion order allocates budget automatically: check with `dv360_get_insertion_order` first and say so.
6. Creatives: pick them with `dv360_list_creatives`, then `dv360_assign_creative_to_line_item` (merge by default; `replace` only on explicit request).
7. Targeting: read with `dv360_list_assigned_targeting`, then `dv360_update_line_item_targeting` with `create_requests` and, only when asked, `delete_requests`.
8. End with the log of changes made and not made.

## Output

1. **Plan**: the numbered list of changes, before any write.
2. **Log**: for each change, the object, the simulation, before, after and the time it was made.
3. **Not done**: changes refused, failed or left for a person, with the reason.

## Rules

- Show budgets both in micros and in readable currency.
- A partial failure on a bulk targeting edit is reported item by item: never resend what was applied.
- Never chain several writes on one yes.

## Examples

- "Pause the DV360 line item Prospecting CTV FR."
- "Fixe le budget de la ligne DV360 Retargeting à 5 000 euros."
- "Ajoute les trois nouvelles créations vidéo à la ligne DV360 YouTube Hiver."
