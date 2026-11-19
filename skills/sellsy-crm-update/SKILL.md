---
name: sellsy-crm-update
description: "Updates the Sellsy CRM through the Pilot MCP server, with the user's explicit approval before every write: move an opportunity to another step, change its status, amount, probability or due date, create an opportunity in the right pipeline and step, create a company or a contact, and create a follow-up task. Each write is simulated first, then applied and read back. Use when the user asks to update Sellsy after a call, a meeting or a signed deal. Triggers: update Sellsy opportunity, create Sellsy deal, Sellsy follow-up task. Déclencheurs : mettre à jour une opportunité Sellsy, créer une affaire Sellsy, créer une tâche de relance Sellsy."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Sellsy account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "sellsy_get_account sellsy_search_all sellsy_get_company sellsy_list_pipelines sellsy_search_opportunities sellsy_get_opportunity sellsy_update_opportunity sellsy_create_opportunity sellsy_create_company sellsy_create_contact sellsy_create_task"
  pilot-mcp-read-only: "false"
---

# Sellsy CRM update

Apply the CRM changes the user asks for in Sellsy, one at a time, each after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Sellsy account.
- **Optional MCP servers:** none is needed.
- **Writes:** the write tools need the paid Pilot MCP plan and a workspace role allowed to write. If a write tool is not served, say so and return the changes for a person to make in Sellsy.

## Tools

- `sellsy_get_account`: checks the connection.
- `sellsy_search_all`: finds a company, contact or document by name.
- `sellsy_get_company`: one company in full.
- `sellsy_list_pipelines`: pipelines and steps, required to create or move an opportunity.
- `sellsy_search_opportunities`: opportunities, to find the right one or avoid duplicates.
- `sellsy_get_opportunity`: one opportunity in full.
- `sellsy_update_opportunity`: changes step, status, amount, probability, due date or note (write).
- `sellsy_create_opportunity`: creates an opportunity attached to a company or an individual (write).
- `sellsy_create_company`: creates a company (write).
- `sellsy_create_contact`: creates a contact (write).
- `sellsy_create_task`: creates a task attached to a company or an opportunity (write).

## Confirmation

The CRM is shared by the team and feeds its forecasts. For each write:

1. Read the current state first with `sellsy_get_opportunity` or `sellsy_get_company`, and search for duplicates with `sellsy_search_all` before any creation.
2. Show the person the exact object (type, name, ID, company), each field's current value and proposed value.
3. Run the write tool with `dry_run: true` (the default) and show the simulation.
4. Wait for an explicit yes for that write. A yes covers one object only.
5. Send the write with `dry_run: false` and `confirm: true`.
6. Never delete anything: Sellsy keeps no trash for these objects. A deletion must be asked for separately, naming the object.
7. After the write, read the object again and show the before and after. If a write is refused or fails, stop, report the message, and do not attempt the next one.

## Steps

1. Call `sellsy_get_account`.
2. Find the objects with `sellsy_search_all` or `sellsy_search_opportunities`. Confirm with the user when several match.
3. Call `sellsy_list_pipelines` whenever a step is involved: `pipeline` and `step` take identifiers, not names.
4. Turn the request into a numbered list of single writes and show it before any write.
5. Move or update a deal with `sellsy_update_opportunity`. Create a deal with `sellsy_create_opportunity` (`related` set to exactly one company or individual). Create a company with `sellsy_create_company` or a contact with `sellsy_create_contact` only when the search found none.
6. Follow-ups: `sellsy_create_task` with a due date, a label and the related object.
7. End with the log of writes made and not made.

## Output

1. **Plan**: the numbered list of writes, before any write.
2. **Log**: for each write, the object, the simulation, before, after and the time it was made.
3. **Not done**: writes refused or left for a person.

## Rules

- Amounts carry the account currency: name it.
- Never chain several writes on one yes.
- Create people records only with the data the user gave.

## Examples

- "Move the Dupont deal to Negotiation in Sellsy and set the amount to 18,000 euros."
- "Crée une affaire Sellsy pour Martin SARL, étape Découverte, échéance fin janvier."
- "Ajoute une tâche de relance chez Durand pour vendredi."
