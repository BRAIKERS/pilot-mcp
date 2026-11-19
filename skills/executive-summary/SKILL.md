---
name: executive-summary
description: "Writes a short executive summary of advertising through the Pilot MCP server: total spend and results across the connected platforms over the last 30 days, the change against the 30 days before, how concentrated spend is, money spent without results, budget status and organic traffic, in a few lines a founder or a director can read in one minute. Use when the user asks for a summary for management, a board update or the key figures only. Triggers: executive summary, board update, key figures, one-minute summary. Déclencheurs : synthèse pour la direction, résumé exécutif, chiffres clés, point pour le comité, synthèse en une minute."
compatibility: "Requires the Pilot MCP server (https://app.pilot.braike.com/mcp) connected in the client and authorized on a workspace with at least one ad account attached."
metadata:
  pilot-mcp-server: "https://app.pilot.braike.com/mcp"
  pilot-mcp-tools: "pilot_data_freshness pilot_cross_platform_overview pilot_account_concentration pilot_budget_pacing pilot_diagnose_wasted_spend google_analytics_run_report"
  pilot-mcp-read-only: "true"
---

# Executive summary

Give management the state of advertising in a few lines: what was spent, what it brought, what changed and what needs a decision. Answer in the user's language.

## Requirements

- **Required MCP server:** Pilot MCP (`https://app.pilot.braike.com/mcp`), authorized on the workspace concerned.
- **Optional MCP servers:** a team messaging server or an email server to send the summary. None is needed.
- **Read-only:** never change anything in an ad account. The decisions listed are for people to take.

## Tools

- `pilot_data_freshness`: whether the data behind the summary is current.
- `pilot_cross_platform_overview`: spend, conversions, value, CPA and ROAS per platform and in total, recomputed on the totals; with `include_organic: true`, Analytics and Search Console too.
- `pilot_account_concentration`: how many campaigns carry 80% of the spend.
- `pilot_budget_pacing`: capped and idle budgets.
- `pilot_diagnose_wasted_spend`: campaigns that spend without converting or far above the median CPA.
- `google_analytics_run_report`: sessions and key events from GA4, when a property is attached.

## Steps

1. Call `pilot_data_freshness`. A stale platform is named in the first line of the summary.
2. Call `pilot_cross_platform_overview` with `days: 30` and `include_organic: true`, then with `days: 60`. The 30 days before are the 60-day totals minus the 30-day totals, for additive figures only (spend, clicks, conversions, value); recompute the ratios from those differences.
3. If the overview refuses a money total because several currencies coexist, keep one line per currency.
4. Call `pilot_diagnose_wasted_spend` with `days: 30` and add up the spend of the campaigns it flags, per currency.
5. Call `pilot_account_concentration` with `days: 30` and `pilot_budget_pacing` with `days: 30`.
6. If a GA4 property is attached and the overview did not include it, call `google_analytics_run_report` with `dateRanges` for the two periods and `metrics: ["sessions", "keyEvents"]`.
7. Write the summary: no more than ten lines, figures first, one decision per line at the end.

## Output

1. **In one sentence**: the state of advertising this month.
2. **Key figures**: spend, conversions, value, CPA and ROAS, last 30 days and change against the 30 days before.
3. **Risks**: spend without results, concentration on a few campaigns, budgets capped or idle, stale data.
4. **Decisions to take**: at most three, each with the figure behind it.

## Rules

- Keep it short: a director reads it in one minute. Details go in an appendix only if asked.
- Name the period and the currency of every amount.
- Never sum amounts in different currencies.
- Conversions are summed across platforms only because the overview does it: say that each platform counts them its own way.
- A figure not collected is absent, not zero.

## Examples

- "Give me a one-minute summary for the board."
- "Fais-moi une synthèse pour la direction : dépense, résultats, risques."
- "Quels sont les chiffres clés de la pub ce mois-ci, en dix lignes ?"
