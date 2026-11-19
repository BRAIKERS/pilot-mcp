---
name: google-business-profile-locations-benchmark
description: "Compares the Google Business Profile listings of a multi-location business through the Pilot MCP server: completeness of each listing (categories, hours, phone, website, description), and views, calls, website clicks, direction requests and bookings per location over the same period, then ranks locations and flags the ones that lag or have listing gaps. Use for chains, franchises and networks of stores or agencies. Triggers: compare store listings, multi-location Google Business Profile, location benchmark. Déclencheurs : comparer les fiches des magasins, réseau d'établissements Google, fiches multi-établissements."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Business Profile account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_business_list_business_accounts google_business_list_business_locations google_business_get_business_location google_business_get_business_performance google_business_get_business_search_keywords"
  pilot-mcp-read-only: "true"
---

# Google Business Profile locations benchmark

Rank the locations of a network on what their Google listings bring, and show which listings need work. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the Google Business Profile account.
- **Optional MCP servers:** a spreadsheet server to export the benchmark. None is needed.
- **Read-only:** never change a listing.

## Tools

- `google_business_list_business_accounts`: the Business Profile accounts available.
- `google_business_list_business_locations`: every listing of an account, with address, phone, category and hours.
- `google_business_get_business_location`: one listing in full.
- `google_business_get_business_performance`: daily metrics of one listing.
- `google_business_get_business_search_keywords`: the searches of one listing, by month.

## Steps

1. Call `google_business_list_business_accounts`, then `google_business_list_business_locations` for the account. Keep the locations in scope (a region, a brand, all).
2. For each, call `google_business_get_business_location` and score completeness: primary category consistent across the network, hours, special hours, phone, website pointing to the location page, description.
3. For each, call `google_business_get_business_performance` over the same 90 days. Compute views, calls, website clicks, direction requests and bookings, and actions per thousand views.
4. For the top and bottom locations, call `google_business_get_business_search_keywords` to compare brand and category searches.
5. Rank locations on actions, and set each one against the network median. A location far below the median with an incomplete listing is the first fix.

## Output

1. **Summary**: network totals, best and weakest locations.
2. **Benchmark table**: location, views, calls, website clicks, direction requests, actions per thousand views, completeness score.
3. **Listing gaps**: per location, what is missing or inconsistent.
4. **Next steps**: in order of locations.

## Rules

- Compare locations over the same period only.
- Local context differs (city size, opening date): say so before judging a location.
- A metric Google did not return is absent, not zero.

## Examples

- "Compare the Google listings of our 25 stores."
- "Quelles agences ont la fiche Google la moins performante ?"
- "Classe nos restaurants par appels et itinéraires sur Google."
