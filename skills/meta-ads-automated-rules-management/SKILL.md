---
name: meta-ads-automated-rules-management
description: "Reviews and manages Meta automated rules through the Pilot MCP server: lists the rules in place with their conditions, actions and schedule, reads what each rule did recently, flags rules that conflict or act on stale conditions, then, after explicit approval of each write, creates a new rule (notification only first) or updates or turns off an existing one, and reads it back. Use when the user asks about Meta automated rules, wants a rule that pauses costly ad sets or alerts on spend, or wants to check what rules did. Triggers: Meta automated rules, Facebook ads rules, auto pause rule. Déclencheurs : règles automatiques Meta, règles Facebook Ads, règle de mise en pause automatique, alerte de dépense Meta."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with a Meta ad account attached. Writes require the paid Pilot MCP plan."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "meta_list_accounts meta_list_automated_rules meta_get_automated_rule meta_get_rule_history meta_list_campaigns meta_list_adsets meta_create_automated_rule meta_update_automated_rule"
  pilot-mcp-read-only: "false"
---

# Meta Ads automated rules management

Show what the account's automated rules do, and add or change a rule safely when asked. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace that holds the account.
- **Optional MCP servers:** none.
- **Writes:** the write tools require the paid Pilot MCP plan and a workspace role that can write. If a write tool is not served, say so and return the rule settings for a person to enter in Meta Ads Manager.

## Tools

- `meta_list_accounts`: the Meta ad accounts attached to the workspace.
- `meta_list_automated_rules`: rules with name, status and evaluation spec.
- `meta_get_automated_rule`: one rule with its evaluation, execution and schedule specs.
- `meta_get_rule_history`: what a rule did recently.
- `meta_list_campaigns`: campaigns, to name a rule's scope.
- `meta_list_adsets`: ad sets, to name a rule's scope.
- `meta_create_automated_rule`: creates a rule (write).
- `meta_update_automated_rule`: changes a rule's name, status or specs (write).

## Confirmation

Every write changes how the account behaves on its own. For each one:

1. Read the current state just before writing: the rule with `meta_get_automated_rule`, or the list of rules for a creation.
2. Show the person the exact rule (name, ID, account), its current settings and the proposed ones, in plain words: on which objects, under which condition, which action, how often.
3. Wait for an explicit yes for this write. A yes for one rule does not cover the next.
4. Make one write at a time.
5. Create every new rule in notification mode first (`execution_spec: { execution_type: "NOTIFICATION" }`), so that it alerts without acting; switching it to an action that pauses or changes budgets is a separate write after its own yes.
6. Never delete a rule. To stop a rule, turn it off with `meta_update_automated_rule`, after a yes.
7. After the write, read the rule again and show the before and the after.
8. Stop at the first write that is refused or fails, report it, and do not try a workaround.

## Steps

1. Call `meta_list_accounts` and pick the account.
2. Call `meta_list_automated_rules`, then `meta_get_automated_rule` for each active rule, and `meta_get_rule_history` to see what it did.
3. Use `meta_list_campaigns` and `meta_list_adsets` to name the objects each rule acts on.
4. Flag: two rules acting on the same objects in opposite directions, rules on campaigns that no longer run, rules that pause on too short a window (one day of data), rules that never fired.
5. For a new rule, draft it with the person: objects (filters on entity type and IDs or names), condition (for example cost per result above a threshold over the last 3 days with a minimum spend), action, schedule.
6. After approval: `meta_create_automated_rule` with `name`, `evaluation_spec`, `execution_spec` and `schedule_spec` for a scheduled rule; or `meta_update_automated_rule` with `rule_id` and the changed fields.
7. Read back with `meta_get_automated_rule`.

## Output

1. **Rules in place**: a table with name, status, objects, condition, action, schedule and last actions.
2. **Issues**: conflicts, stale scopes, risky conditions.
3. **Writes done**: before and after for each.
4. **Not done**: refused or skipped writes, as settings for a person.

## Rules

- Amounts in rule conditions follow Meta's units for the field: check the unit before writing a threshold.
- A rule that pauses on a short window reacts to noise: propose at least three days and a minimum spend.
- Name the account time zone, which drives the schedule.

## Examples

- "What do our Meta automated rules do?"
- "Crée une règle qui m'alerte quand un ensemble dépasse 40 euros de coût par achat sur trois jours."
- "Désactive la règle qui coupe les ensembles le week-end."
