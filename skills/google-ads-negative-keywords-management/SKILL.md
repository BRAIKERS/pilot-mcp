---
name: google-ads-negative-keywords-management
description: "Manages Google Ads negative keywords through the Pilot MCP server, after explicit approval of each write: reads the search terms that spend without converting and the negatives already in place (campaign, ad group, shared lists and account level), proposes the negatives to add with their match type and level, then adds them one at a time, creates or attaches a shared negative list when asked, and reads the result back. Use when the user wants to add, organize or clean negative keywords. Triggers: add negative keywords, negative keyword list, exclude search terms. Déclencheurs : ajouter des mots-clés négatifs, liste de mots-clés négatifs, exclure des termes de recherche, gérer les négatifs."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Google Ads account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "google_list_accounts google_list_campaigns google_get_search_terms_report google_list_negative_keywords google_list_shared_sets google_list_shared_criteria google_list_customer_negative_criteria google_add_negative_keyword google_create_shared_set google_add_shared_criterion google_attach_shared_set_to_campaign google_add_customer_negative_criterion google_delete_shared_criterion"
  pilot-mcp-read-only: "false"
---

# Google Ads negative keywords management

Stop ads from showing on searches that waste money, by adding the right negatives at the right level, one approved write at a time. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** a spreadsheet server to keep the list of negatives. None is needed.
- **Writes:** the write tools require the paid Pilot MCP plan and a workspace role that can write; removing a negative needs the administrator role. If a write tool is not served, say so and return the list of changes for a person to apply by hand in Google Ads.

## Tools

- `google_list_accounts`: the Google Ads accounts attached to the workspace.
- `google_list_campaigns`: campaigns with their IDs.
- `google_get_search_terms_report`: the searches that triggered ads, with cost and conversions.
- `google_list_negative_keywords`: negatives set on campaigns and ad groups.
- `google_list_shared_sets`: shared negative keyword lists.
- `google_list_shared_criteria`: the negatives inside one shared list.
- `google_list_customer_negative_criteria`: account-level exclusions.
- `google_add_negative_keyword`: adds one negative to a campaign or an ad group (write).
- `google_create_shared_set`: creates a shared negative keyword list (write).
- `google_add_shared_criterion`: adds a negative to a shared list (write).
- `google_attach_shared_set_to_campaign`: attaches a shared list to a campaign (write).
- `google_add_customer_negative_criterion`: adds an account-level negative keyword or placement (write).
- `google_delete_shared_criterion`: removes one negative from a shared list, only on express request (write).

## Confirmation

Every write changes a live account. For each one:

1. Read the current state just before writing: the negatives already in place at that level.
2. Show the person the exact change: account and ID, level (campaign, ad group, shared list, account), the object's name and ID, the negative text and match type, what is there now and what will be there after.
3. Wait for an explicit yes for this write. A yes for one write does not cover the next one; a list may be approved as a whole only if the person says so for that list.
4. Make one write at a time.
5. Never remove a negative unless the person asks for it in words that name the negative and its list.
6. After the write, read the level again and show the before and the after.
7. Stop at the first write that is refused or fails, report it, and do not try a workaround.

## Steps

1. Call `google_list_accounts` and pick the account; call `google_list_campaigns`.
2. Call `google_get_search_terms_report` over `LAST_30_DAYS` with `limit: 1000`. Keep terms with spend and no conversion, and terms with a clearly wrong intent.
3. Read what is excluded already: `google_list_negative_keywords`, `google_list_shared_sets` then `google_list_shared_criteria` for each list, and `google_list_customer_negative_criteria`.
4. Propose each negative with its level: a term wrong for the whole business goes to a shared list or the account; a term wrong for one campaign goes to that campaign. Choose the match type: exact for one query, phrase for a family of queries. Check that the negative does not block a converting term or a keyword of the account.
5. After approval, write: `google_add_negative_keyword` with `level`, `campaign_id` or `ad_group_id`, `text` and `match_type`; or `google_add_shared_criterion` on an existing list; or, when asked, `google_create_shared_set` with `set_type: "NEGATIVE_KEYWORDS"`, then `google_attach_shared_set_to_campaign`; or `google_add_customer_negative_criterion` with `type: "KEYWORD"`.
6. Read back after each write and show the result.

## Output

1. **Proposed negatives**: a table with term, match type, level, target, spend saved over 30 days and the reason.
2. **Writes done**: each one with before and after.
3. **Not done**: writes refused or skipped, as a list a person can apply by hand.

## Rules

- A negative blocks every search that matches it: check converting terms before adding a phrase negative.
- Negative keywords do not match close variants the way positive keywords do: add plural or misspelled forms separately when needed.
- Never sum the spend saved across currencies.

## Examples

- "Add negatives for the search terms that wasted money this month."
- "Crée une liste de mots-clés négatifs partagée et applique-la aux campagnes Search."
- "Exclus « gratuit » et « emploi » sur tout le compte."
