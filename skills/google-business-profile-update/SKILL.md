---
name: google-business-profile-update
description: "Updates a Google Business Profile listing through the Pilot MCP server, with the user's explicit approval before every write: description, phone numbers, website, regular hours and special hours for holidays or closures. Every change is public on Google Search and Maps as soon as it is applied, so each one is validated by Google first, shown to the user, applied only after a yes, and read back. Use when hours change, a holiday comes, the phone or website changes, or the description needs rewriting. Triggers: update Google listing hours, holiday hours Google, change Google Business phone. Déclencheurs : modifier les horaires de la fiche Google, horaires des jours fériés, changer le téléphone de la fiche Google."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Business Profile account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_business_list_business_accounts google_business_list_business_locations google_business_get_business_location google_business_update_business_description google_business_update_business_phones google_business_update_business_website google_business_update_business_regular_hours google_business_update_business_special_hours"
  pilot-mcp-read-only: "false"
---

# Google Business Profile update

Apply the listing changes the user asks for, one at a time, each after an explicit yes. Every change is public as soon as it is applied. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Google Business Profile account.
- **Optional MCP servers:** none is needed.
- **Writes:** the write tools need the paid Pilot MCP plan and a workspace role allowed to write. If a write tool is not served, say so and return the changes for a person to make in Google Business Profile.

## Tools

- `google_business_list_business_accounts`: the Business Profile accounts available.
- `google_business_list_business_locations`: the listings of an account.
- `google_business_get_business_location`: one listing in full, to read before and after.
- `google_business_update_business_description`: replaces the description (write).
- `google_business_update_business_phones`: sets the primary phone and up to two others (write).
- `google_business_update_business_website`: changes the website (write).
- `google_business_update_business_regular_hours`: replaces the regular hours (write).
- `google_business_update_business_special_hours`: replaces the special hours (write).

## Confirmation

The listing is public: customers see a change on Google Search and Maps as soon as it is applied. For each change:

1. Read the current listing first with `google_business_get_business_location`.
2. Show the person the exact listing (name, address, resource name), the current value and the proposed value, and remind them that the change will be public.
3. Call the update tool without `confirm` first: Google validates the change without applying it. Show the result.
4. Wait for an explicit yes for that change. A yes covers one change on one listing only.
5. Call the tool again with `confirm: true`.
6. Never remove information unless the person asks for it by name: an empty list of additional phones removes them, and replaced hours drop every period not sent.
7. After the write, read the listing again and show the before and after. If a change is refused, stop, report Google's message, and do not attempt the next one.

## Steps

1. Call `google_business_list_business_accounts` and `google_business_list_business_locations`; confirm the listing with the user. For a network, confirm each listing separately.
2. Turn the request into a numbered list of single changes and show it before any write.
3. Hours: `google_business_update_business_regular_hours` replaces the whole week, so send every opening period, not only the changed day. `google_business_update_business_special_hours` replaces every special period: keep the existing ones that still apply and add the new ones. Times are HH:MM from 00:00 to 24:00.
4. Phones: `google_business_update_business_phones` with `primary_phone` and the additional phones to keep, in international format.
5. Website: `google_business_update_business_website` with the full URL of the location page.
6. Description: `google_business_update_business_description` with a factual text in the business's voice, without links, prices or promotional claims Google forbids.
7. End with the log of changes made and not made.

## Output

1. **Plan**: the numbered list of changes, before any write.
2. **Log**: for each change, the listing, Google's validation, before, after and the time it was made.
3. **Not done**: changes refused or left for a person.

## Rules

- Never chain several changes on one yes.
- Never write anything the business cannot stand behind publicly.
- Google may review some changes before showing them: say so when the read-back still shows the old value.

## Examples

- "Set our holiday hours for December 24, 25 and 31 on Google."
- "Change le numéro de téléphone de la fiche Google de l'agence de Nantes."
- "Réécris la description de notre fiche Google et montre-la-moi avant de l'appliquer."
