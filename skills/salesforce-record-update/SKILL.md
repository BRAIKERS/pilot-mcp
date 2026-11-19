---
name: salesforce-record-update
description: "Updates or creates Salesforce companies (Account), opportunities and campaigns through the Pilot MCP server, with the user's explicit approval before every write: move an opportunity to another stage, change its amount or close date, record a campaign's actual cost, fix a company field, or create an opportunity or campaign. Reads the object's fields first, shows the before and after, and never touches people records. Use when the user asks to update the CRM. Triggers: update Salesforce opportunity, log campaign cost Salesforce, create Salesforce opportunity. Déclencheurs : mettre à jour une opportunité Salesforce, saisir le coût d'une campagne, créer une opportunité Salesforce."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Salesforce org attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "salesforce_describe_object salesforce_search salesforce_get_record salesforce_get_opportunity salesforce_get_account salesforce_get_campaign salesforce_list_opportunities salesforce_update_record salesforce_create_record"
  pilot-mcp-read-only: "false"
---

# Salesforce record update

Apply the CRM changes the user asks for in Salesforce, one record at a time, each after an explicit yes. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Salesforce org.
- **Optional MCP servers:** none is needed.
- **Writes:** the write tools need the paid Pilot MCP plan, a workspace role allowed to write, and a Salesforce user allowed to edit the object. They cover companies (Account), opportunities and campaigns only; contacts, leads and users are never created or changed here. If a write tool is not served, say so and return the changes for a person to make in Salesforce.

## Tools

- `salesforce_describe_object`: fields, types, picklist values and required fields of an object.
- `salesforce_search`: finds a record by name.
- `salesforce_get_record`: any record with chosen fields.
- `salesforce_get_opportunity`: one opportunity in full.
- `salesforce_get_account`: one company in full.
- `salesforce_get_campaign`: one campaign in full.
- `salesforce_list_opportunities`: opportunities, to find the right one.
- `salesforce_update_record`: changes fields of a company, opportunity or campaign (write).
- `salesforce_create_record`: creates a company, opportunity or campaign (write).

## Confirmation

The CRM is shared by the whole team, and reports and forecasts read from it. For each write:

1. Read the current record first with `salesforce_get_opportunity`, `salesforce_get_account`, `salesforce_get_campaign` or `salesforce_get_record`.
2. Show the person the exact record (object, name, ID, owner), each field's current value and proposed value.
3. Wait for an explicit yes for that write. A yes covers one record only.
4. Make one write at a time.
5. Never delete a record. A deletion must be asked for separately, naming the record.
6. After the write, read the record again and show the before and after.
7. If a write is refused (validation rule, permission, required field), stop, report Salesforce's message, and do not attempt the next one.

## Steps

1. Find the record with `salesforce_search`, or `salesforce_list_opportunities` filtered by account or stage. Confirm with the user when several match.
2. Call `salesforce_describe_object` for the object: exact API names, picklist values (for example the stages), required fields. Never send a value that is not in the picklist.
3. Turn the request into a numbered list of single-record changes and show it before any write.
4. Update with `salesforce_update_record`, passing only the fields that change by their API name (StageName, Amount, CloseDate, ActualCost and so on).
5. Create with `salesforce_create_record`, with every required field; check first with `salesforce_search` that the record does not already exist.
6. End with the log of writes made and not made.

## Output

1. **Plan**: the numbered list of changes, before any write.
2. **Log**: for each write, the record, before, after and the time it was made.
3. **Not done**: writes refused or left for a person, with Salesforce's message.

## Rules

- Amounts carry the record's currency: name it.
- Never chain several records on one yes.
- Never touch a person record (contact, lead, person account).

## Examples

- "Move the Acme renewal opportunity to Negotiation and set the close date to December 15."
- "Saisis le coût réel de la campagne Salon 2026 dans Salesforce : 12 000 euros."
- "Crée une opportunité pour Dupont SA, montant 25 000 euros, étape Qualification."
