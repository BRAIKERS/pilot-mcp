---
name: google-business-profile-review
description: "Reviews a Google Business Profile listing in read-only mode through the Pilot MCP server: name, address, phone, website, categories, regular and special hours, description, then views on Search and Maps, calls, website clicks, direction requests and bookings day by day, and the searches through which customers found the business. Use when the user asks how a local business shows on Google, whether the listing is complete, or what it brings. Triggers: Google Business Profile review, Google My Business performance, local listing check. Déclencheurs : fiche Google Business Profile, fiche Google My Business, fiche établissement Google, appels et itinéraires."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Business Profile account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_business_list_business_accounts google_business_list_business_locations google_business_get_business_location google_business_get_business_performance google_business_get_business_search_keywords"
  pilot-mcp-read-only: "true"
---

# Google Business Profile review

Tell the user whether their Google listing is complete and accurate, and what it brings in calls, visits and clicks. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Google Business Profile account.
- **Optional MCP servers:** none is needed.
- **Read-only:** never change the listing. Changes are public and are for a person to approve.

## Tools

- `google_business_list_business_accounts`: the Business Profile accounts available.
- `google_business_list_business_locations`: the listings of an account.
- `google_business_get_business_location`: one listing in full.
- `google_business_get_business_performance`: daily views on Search and Maps, calls, website clicks, direction requests and bookings.
- `google_business_get_business_search_keywords`: the searches through which customers found the listing, by month.

## Steps

1. Call `google_business_list_business_accounts`, then `google_business_list_business_locations` with the `account_name`. Ask which listing if there are several.
2. Call `google_business_get_business_location`. Check: primary and additional categories, address, phone in international format, website, regular hours, special hours for coming holidays, description present and accurate, attributes when returned.
3. Call `google_business_get_business_performance` over the last 90 days and the 90 days before, with the default metrics. Sum views on Search and Maps, calls, website clicks and direction requests; show the weekly trend.
4. Call `google_business_get_business_search_keywords` over the last six months (`start_month`, `end_month` as YYYY-MM): share of brand searches against category searches ("restaurant near me", "plumber Lyon").
5. Read the results: more category searches mean the listing is found by new customers; a drop in views with stable searches often follows a change of category or hours.

## Output

1. **Summary**: completeness of the listing and what it brings.
2. **Listing check**: each field, its value and what is missing or wrong.
3. **Performance**: views, calls, website clicks, direction requests and bookings, with the change.
4. **Searches**: top searches, brand against category.
5. **Next steps**: changes for a person to approve.

## Rules

- Google rounds search keyword volumes and hides small ones: say so.
- A metric Google did not return is absent, not zero.
- Never suggest fake reviews, keyword stuffing in the business name, or anything against Google's guidelines.

## Examples

- "How is our Google Business Profile doing?"
- "Notre fiche Google est-elle complète ?"
- "Combien d'appels et d'itinéraires la fiche a-t-elle générés ce trimestre ?"
