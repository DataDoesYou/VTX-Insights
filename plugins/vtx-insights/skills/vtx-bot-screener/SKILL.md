---
name: vtx-bot-screener
description: Run the existing VTX Screener through Insights MCP with the calling assistant evaluating canonical prompts. Use for current symbol screening, discovery, or profile and fleet symbol recommendations that combine present tradability with historical ROI and supporting P&L. Keep screening read-only; route separately authorized changes through VTX Insights.
---

# VTX Bot Screener

## Scope and methodology

Use the existing Screener's forward-looking tradability rubric, context,
per-symbol passes, and aggregation. Do not invent a substitute ranking formula.
The calling assistant evaluates the returned prompts; do not dispatch configured
models or describe the result as a model comparison.

Keep this workflow read-only: no bot control, orders, assignments, saved settings,
code changes, or deployments. Normal market-data read-through caching is part of
context retrieval. Route an already authorized settings change through the bundled
`$vtx-insights-analysis` skill and its separately authorized settings capabilities.
This screener skill does not apply recommendations or grant mutation authority.

Use the connected VTX Insights MCP server and its current public capability
contracts. Setup and permission guidance is at https://vtxmacro.com/insights;
request only `insights:read` for this workflow. If the connector or an owned
profile's context is unavailable, report that gap. No repository checkout,
operator access, or direct exchange credentials are required.

## Resolve the requested scan

- Discover the current MCP capability schemas before calling
  `screener.candidates`, `screener.context`, and `screener.rank`.
- For named profiles, call `profiles.discover` once, confirm each selector's
  `owned_by_caller` label, and preserve the exact resolved selection. Public
  profiles can supply public historical evidence; their private account context
  is not available for profile-scoped screening.
- General screening uses canonical defaults unchanged. Screening for an exact
  owned profile uses that profile's effective Screener settings and trading
  context. Explicit user symbols and overrides apply only to this scan.
- Preserve the requested profiles and symbols. When symbols are omitted, use the
  existing Screener discovery filters and volume ordering. Retain current symbols
  added as comparison baselines and identify unresolved incumbents.
- For fleets, group profiles only after proving equivalent prompt-affecting
  settings and inputs: prompts, variables, indicators, universe and filters,
  timeframes, lookback, cadence, and relevant calendar/news settings. Account or
  position inputs can require separate profile contexts. Preserve each bot's
  baseline and selection even when market evaluations can be reused.
- Report effective settings and candidate coverage. If profile configuration
  cannot resolve, mark that scope blocked; do not silently select another profile,
  model, default configuration, or smaller candidate list.

## Evaluate actual inputs

For every effective pass, retrieve and read each candidate's complete rendered
system and user prompts with the same profile, symbol selection, and overrides.
Honor the returned evaluation timing without treating the interval between
scheduled runs as a delay between passes. Use the canonical
rubric to judge remaining opportunity, entry structure, and risk/reward. Evaluate
with the assistant's judgment rather than a programmed score heuristic. Record
one actual integer tradability score and reason per usable symbol/pass.

Check the data inside each prompt before scoring:

- Verify the latest completed candle timestamps against the evaluation time and
  each timeframe's duration, respecting the applicable trading session and
  canonical freshness policy. A recent response or live candle is insufficient.
- Check continuity and internal gaps across the configured lookback, as well as
  coverage of the requested timeframes and derived statistics. A freshly repaired
  tail does not prove that the historical window is usable.
- Inspect unavailable markers and source warnings. `context_complete` alone does
  not establish freshness or continuity. Treat stale, incomplete, failed, or
  unavailable inputs that prevent a sound evaluation as unscored with a reason,
  not as zero or evidence of poor symbol performance.

Pass the complete candidate list, effective pass count, and actual evaluations to
`screener.rank`. Preserve individual reasons, mean, sample spread, and missing
passes. Do not copy one evaluation across passes or invent scores to complete a
matrix. Disclose that repeated evaluations by the same assistant are not
independent model tests. Reuse completed valid evidence; recover failed transport
through its supported mechanism without rerunning successful work unnecessarily.
For slow reads, use `analysis.start` and poll the same `analysis.status` handle.
For complete artifact retrieval, read `artifact.manifest`, consume every chunk,
verify `byte_count` and `content_hash`, and acknowledge through `artifact.resume`.
Decode `text` as UTF-8 when `encoding=utf-8` or `base64_data` when
`encoding=base64`. Do not silently shrink the candidate population after a
transport limit.

## Add historical evidence for fleet recommendations

Current screening alone is insufficient to recommend fleet reallocations.
Start with the leaderboard's precomputed seven-day ROI and supporting P&L unless
the user requests another period or exact attribution. Read the complete relevant
leaderboard population through an available public capability or the existing
leaderboard endpoint, including every page. Preserve performance availability
and financial snapshot timestamps; unavailable numeric placeholders are not
zero returns. Existing snapshots are sufficient for ordinary recommendations;
disclose their age without demanding live freshness or rebuilding trade history.

If the available MCP result contains fill-dollar aggregates rather than
precomputed ROI, use the public read-only endpoint:
`https://api.vtxmacro.com/leaderboard?timeframe=7d&sort_by=roi&order=desc&running_only=false&my_bots_only=false&page=1&limit=100`.
Follow the returned pagination until the complete public population is read;
do not use only the first page or filter to running bots. Change `timeframe`
only for the user's requested supported period. Preserve the endpoint's
availability and financial timestamp fields. This reads VTX's stored leaderboard
and does not request new Hyperliquid history. Report its public eligibility
limits; the public leaderboard is not every registered VTX account.

Honor the requested historical population independently of the fleet being
allocated. A request for all VTX native Hyperliquid symbols requires the whole
platform's native-symbol population, including incumbents and symbols below the
current score cutoff; an owner's bots or the shortlisted candidates are not a
substitute. Report missing symbols and source coverage explicitly.

Use leaderboard ROI as the primary historical comparison, with dollar P&L as
supporting evidence. Associate profiles with symbols using existing assignments
or retained trading evidence and state which association was used. A bot's ROI
is account performance: label multi-symbol and changed-assignment histories as
mixed evidence rather than claiming exact symbol attribution. This approximate
comparison is sufficient for an allocation proposal; missing symbol-level
capital attribution must not block use of valid leaderboard returns.

Compare median bot ROI, positive-return breadth, and sample counts for each
represented symbol, alongside notable winners and losses. Do not sum percentages
or rank symbols by dollar gains alone. A bot may support multiple symbol groups,
but those groups overlap and must not be added into portfolio totals. Keep
symbols without usable historical evidence unproven, not losing.

Use the bundled `$vtx-bot-trade-chain-analysis` skill
only for a material unresolved attribution question or a user-requested deeper
audit. Reuse completed evidence. Do not launch full-history matrices, rebuild
equity/cashflow returns, require historical margin, or create a new return
calculation merely to make an ordinary screener recommendation. If exact returns
are requested, name their denominator and distinguish account equity, margin,
and entry-notional returns, with costs and coverage explicit.

Check whether model, prompt generation, sizing, symbol assignment, or unequal
market periods explain the apparent advantage. Distinguish measured profitability
from a promising current score. A new symbol with no fleet history is unproven;
a stale or missing historical sample is not a losing record.

## Recommend a concrete allocation

Give a before/after mapping by bot name, the evidence for each proposed change,
and the resulting coverage per symbol. Preserve each bot's venue/dex pool unless
the user explicitly requests a cross-pool plan. Apply the user's coverage and
allocation constraints, such as a requested minimum number of bots per symbol;
do not turn an example constraint into a universal default.
When the user requires protecting held symbols, read the owned profiles' current
`account.snapshot` before proposing removals. Retain symbols with open positions;
an unavailable position read does not prove that a bot is flat. Adding an eligible
symbol alongside a held one is an option only within the user's allocation limits.

Favor changes supported by both usable current inputs and relevant economics.
Do not replace a profitable incumbent solely because its current tradability
score falls below the new-entry cutoff; weigh comparable historical ROI and its
coverage against the case for replacement.
For unproven candidates, propose a limited, measurable trial appropriate to the
user's fleet and constraints, with a review checkpoint, success criteria, and
material tradeoffs, without applying it. Keeping assignments unchanged
is a valid recommendation when evidence is weak or incumbents remain preferable.

Separate current tradability, historical performance, proposed allocation, and
unresolved coverage. Use profile names and clearly labelled timestamps, keep private prompt
and trace bodies out of the report, and never imply recommendations were applied.
