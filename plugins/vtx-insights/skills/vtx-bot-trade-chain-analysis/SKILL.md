---
name: vtx-bot-trade-chain-analysis
description: Reconstruct complete VTX bot trade chains to explain wins, losses, overtrading, low-liquidity entries, execution problems, settings-generation effects, and systemic failure modes. Use when the user asks why one or many bots are performing well or poorly, requests a deep bot review, wants recent settings judged only after they became effective, or needs evidence-backed keep, tune, pause, or optimization recommendations.
---

# VTX Bot Trade-Chain Analysis

## Outcome

Explain how settings and decision-time evidence became executions, fills,
positions, exits, fees, funding, and PnL. Distinguish repeatable bot problems
from adverse variance and recommend the smallest useful changes without
diagnosing from aggregate PnL alone.

## Boundaries

- Keep the analysis read-only unless the user separately authorizes an exact
  settings, runtime, profile, connection, or trading action.
- Resolve the requested population explicitly: owned profiles, named public
  handles or wallets, or the whole public platform.
- Use profile names in the answer. Do not expose internal IDs, credentials,
  tokens, or unauthorized private data. When an authorized owner explicitly
  requests exact retained prompts, responses, or reasoning—or those bytes are
  materially necessary—use the verbatim tool contract and state the scope.
- Treat model reasoning as evidence, not truth. VTX supplies tools and
  calculations but does not grade, correct, or rewrite the final analysis.
- Never impose a population, row, byte, history, or analysis-duration cap.
  Transport pages and artifact chunks are flow control only.
- Do not retrieve irrelevant data merely because it is available. Start with
  the complete cohort that can change the requested decision, then expand when
  a broader population is material or explicitly requested.
- Keep a completed-call ledger keyed by capability, selection, cutoff, view,
  and interval. Reuse returned evidence and never issue an identical call
  twice.

- Judge cutoff drift from the explicit requested end, the result cutoff or
  effective end, and data-bearing business-row timestamps. A later job or
  artifact `data_cutoff`, `as_of`, `created_at`, authorization, invocation, or
  materialization timestamp is not business-data drift. When every business
  bound and row remains at or before the requested end, use the artifact; do
  not discard or narrow it because processing finished later.

## 1. Define The Question

Confirm or infer from the request:

- exact profiles or account population;
- one immutable cutoff and the requested time window;
- recent settings, prompt, model, scheduler, symbol, or variable changes that
  must define a new configuration generation; and
- the decision the user is trying to make.

Exact supplied handles are already a resolved selection. Pass them directly in
`selection`. When a route in this skill fully specifies the needed calls, do
not call `profiles.discover`, `help.search`, `list_mcp_resources`,
`read_mcp_resource`, a capability catalog, or any other Help/schema-discovery
surface before starting that route.

Replay the exact selection and cutoff on every independent profile-scoped call.
Do not assume `scope=profile` selects a named profile.

## 2. Establish Performance And Generations

Read complete change provenance first when recent configuration changes matter.
Identify when each material change became effective and do not judge the new
generation using earlier results.

For a literal “since configuration or prompt change” review, start one
`trade_chain.since_change` request through `analysis.start` with the exact
selection and cutoff, then consume its one immutable body-free artifact. Use its
exact retained boundary, effective-consumption prompt lineage, compact campaign
economics, provider reliability, coverage, and conservation as the core
evidence. Make separate calls below only for a named unresolved claim. If any
selected profile lacks an exact boundary, preserve the typed missingness and do
not infer an adaptive fallback.

Call `positions.episodes` with `result_view=window_matrix` and
`matrix_projection=compact` exactly once for the full selection and cutoff.
Use `matrix_projection=full` only when a literal setting or input-cohort claim
requires the high-cardinality detail omitted by compact. When recent changes define the requested cohort,
resolve each selected profile's latest relevant material change and keep
qualifying timestamps within seven days. If exactly one qualifying timestamp
remains, use it as `adaptive_start`; if multiple remain, use their latest
timestamp (`max(timestamps)`) as the one shared `adaptive_start`. This includes
a single selected profile and a multi-profile selection where only one profile
changed recently. Never split the matrix by profile or change timestamp. If no
qualifying change remains, omit `adaptive_start` so the config-owned fallback
applies. For performance questions without change attribution, do not retrieve
change provenance solely to choose a window and omit `adaptive_start`. Verify
`source_load_count=1` and use the returned rolling, adaptive, and disjoint
windows without repeating them as separate calls.

When the user supplies exact custom periods, include them in the same matrix as
timezone-aware `custom_intervals` with explicit `end_inclusive` values. Verify
`window_count=12+custom_count` and one source load; use the one frozen cutoff
for a "to now" interval instead of issuing separate reads.

Use the matrix to compare:

- net and gross PnL, fees, funding, volume, win rate, expectancy, drawdown, and
  completed campaigns;
- instruments, directions, settings generations, sessions, and action
  sequences; and
- recent performance against non-overlapping earlier evidence without blending
  old and new settings.

Do not call every fill a trade. A campaign is the strategy outcome; fills are
execution accounting.

### Platform Aggregate Observability Route

When the question is an aggregate whole-platform asset, behavior-generation,
source-coverage, campaign, or execution-cost question:

1. Choose one explicit cutoff and call `analysis.start` directly for one
   `positions.episodes result_view=window_matrix` request with
   `matrix_projection=compact` and
   `selection={"population":"whole_platform"}`. Do not make a synchronous
   probe, discover a ranked profile subset, or start a second position-episode
   source job.
2. Before parsing result data, persist the source handle, cutoff, scope,
   custom intervals, and planned query paths in the completed-call ledger.
3. Make `artifact.query` with `path=[]` the first artifact access. This commits
   the handle to query mode; never call `artifact.manifest` or download the full
   raw artifact for this aggregate question. Discover returned keys before
   querying child paths.
4. Retrieve only the complete compact matrix paths needed for the answer: window
   metadata, cohort and per-profile stream coverage, per-profile asset coverage,
   and window-local campaign metrics by profile/asset/generation. Preserve every
   returned zero-event profile and conservation row; targeted retrieval is
   transport selection, not analytical sampling.
   After the paths are disclosed, use `artifact.query_many` to retrieve several
   small indexed paths in one round trip. Keep each returned path's exact versus
   structure projection and continuation separate. Use individual
   `artifact.query` for legacy unindexed artifacts or durable exact retrieval.
5. When execution cost is part of the question, call
   `execution.quality result_view=summary` separately with the same explicit
   cutoff and `selection={"population":"whole_platform"}`. This is the compact
   execution source; it is not a child path in the position-episode matrix
   artifact. Record and reconcile both calls without downloading execution
   detail rows.
6. If a targeted query itself becomes durable, keep the matrix source handle in
   query mode and treat the query-result handle as a separate artifact. Record
   it before parsing and follow its advertised manifest/resume contract without
   changing the original handle's retrieval mode.

Do not retrieve an all-history event ledger, raw decision reasoning, or complete
artifact for this route unless a separate causal question genuinely requires
exact chains. If it does, start that expansion as a separate, cutoff-identical
request and state the unresolved claim first.

For fleet-wide premature-close, same-candle reversal, or unchanged-evidence
questions, use `decision.context result_view=adjacent_transition_audit` with an
exact start and end before requesting raw reasoning. It returns the complete
selected non-HOLD transition population with the preceding same-profile/symbol
typed action, body-free candle-identity delta, reasoning availability, and
cutoff-safe execution outcome.

### Settings-Change Trade Review Route

When the user asks to review trades since settings changed:

1. Read current settings with `settings.read view=current_bot_settings` and
   `result_view=enabled_only`, then
   complete `runtime.provenance change_causality aggregate_metrics`—through
   `analysis.start` for a long or all-history read—and call the one
   full-selection `window_matrix` with `matrix_projection=compact`.
2. Use the matrix's returned adaptive-window start as the exact `start` for
   interval-scoped `policy.evaluate summary`, `decision.context
   exposure_metrics`, `position.excursions summary`, and any compact decision
   context. Replay the same selection and cutoff.
3. Complete those compact reads before starting ledger or reasoning expansion.
4. Start the one complete all-history `event_detail` ledger with no `start`.
5. Wait for an exact user-supplied counterfactual scenario or threshold
   follow-up before calling `policy.replay`; never invent default replay inputs.

When a same-thread follow-up explicitly supplies a warning/critical threshold
pair after Max Drawdown, the six-hour window, selection, start, and cutoff were
established, inherit only that context and run exactly one
`policy.replay result_view=comparison` with `enforcement=hard`. Do this even
when the pair matches current settings: replay evaluates deterministic
enforcement, not configuration novelty. Do not infer a threshold pair from
settings or replay a policy/window-only question.

For this route, make one synchronous `decision.context exposure_metrics`
attempt with `execution_linkage=all`. If it times out before returning an
artifact, recover that same exact request once through `analysis.start`; this
is one logical exposure request, not an executed-then-all expansion. That
general expansion rule applies only to `context_rows`, and a completed exposure
request must never be repeated. Treat
`policy.evaluate summary` as complete unless the user's literal question
explicitly asks for a per-decision enforcement audit; a nonzero or unavailable
summary value alone does not justify `decision_rows`. After the ledger, make at
most one optional `decision.context context_rows` expansion for the general
settings-change review, and consume it completely before starting another job.
Before starting it, name the exact unresolved claim in the working analysis.
If the compact evidence and ledger already answer every material claim, stop
without a reasoning expansion.

Use enabled path/catalog, behavior hash, and prompt hash metadata for generation
comparison. Do not treat disabled, unset, inherited, or UI-only values as active;
request the complete settings view only when an exact raw value or prompt body
can materially change the claim.

For this route, call `execution.quality` only when the user explicitly asks
about execution, fills, fees, failures, or latency, or when compact ledger
evidence leaves a material execution question unresolved. Call
`market.history` only for an explicit candle or market-path question, or when
the excursion summary leaves material path ambiguity. Expand complete decision
reasoning only after compact evidence identifies a material unresolved claim.

### Campaign-Loss And Systemic-Problem Route

When the user asks why a bot lost and whether the problem is systemic, without
asking for settings-change attribution:

1. Resolve the exact requested profiles, one cutoff, and any investigation
   start already supplied by the user, host, or existing thread. If no explicit
   handle was supplied, discover the intended public comparison population
   once. Never invent an investigation start from an unrelated settings change.
2. Call the one full-selection `window_matrix` with `matrix_projection=compact`
   and `adaptive_start` omitted.
   Do not call change provenance merely because this is a performance question.
   If no investigation start was already established, use the returned
   `standard_windows.all.complete_metrics.time_coverage.first_event_at` as the
   exact investigation start. If it is null, report that no retained campaign
   exists instead of issuing a schema-invalid excursion request.
3. Call direct `execution.quality summary` with
   `decision_target_settings=omit` and direct `position.excursions summary`,
   using the exact established investigation start, selection, and cutoff.
   Execution summary is material here because it separates execution failure
   from strategy failure even when the user did not mention fills explicitly.
4. Complete the matrix and both summaries before starting and completely
   consuming the one all-history `event_detail` ledger with no `start`.
5. Only after those compact reads and the ledger, start one complete
   `decision.context context_rows` reasoning audit with the same established
   investigation start, `execution_linkage=all`,
   `content_view=audit`, and `include_reasoning=true`, and consume it completely.

Use the ledger to compare losing campaigns with profitable counterexamples and
explicit zero-event profiles before calling the behavior systemic. Do not add
runtime provenance unless the literal question asks for change attribution.

### Gain-Giveback And Exit-Timing Route

When the user asks whether one campaign gave back gains or exited late:

1. Resolve the exact requested profiles, campaign symbol, campaign start, and
   one cutoff from the user, host, existing thread, or retained matrix. Do not
   rediscover an exact profile selection that is already established.
2. Complete one full-selection `window_matrix` with `matrix_projection=compact`
   and `adaptive_start` omitted,
   then call direct `position.excursions summary` and direct
   `execution.quality summary` with `decision_target_settings=omit` for the
   exact campaign start, symbol, selection, and cutoff.
3. Complete the matrix and both summaries before starting any durable
   expansion. Then consume the complete all-history `event_detail` ledger with
   no `start`, the exact campaign's `position.excursions episode_rows`, and
   cutoff-pinned fully completed `market.history` candles.
4. After the compact excursion evidence, start exactly one
   `decision.context context_rows` audit for the campaign through
   `analysis.start` with `execution_linkage=all`, `content_view=audit`,
   `include_reasoning=true`, and `include_candle_coverage=false`; consume it
   completely. Do not make an `executed` precursor or repeat the reasoning
   request.

For this route, the matrix and both summaries must complete before any durable
call starts. Make one all-linkage reasoning request and no executed precursor.

For a claim that the campaign gave back gains, use only the exact
`actual_position_path.confirmed_profit_giveback_usd` aggregate. Its eligible
rows require confirmed positive MFE. Do not count legacy `giveback_from_mfe`
as profitable campaigns: it is a generic confirmed peak-to-exit delta that can
include a never-profitable loss that merely worsened. Keep confirmed and
possible MFE/MAE, candle gaps, boundary uncertainty, and exit efficiency
explicit. Decision-to-fill elapsed
time can separate decision delay from end-to-end execution delay, but it is not
pure exchange latency and unavailable pre-trade spread or slippage stays
unavailable.

### Execution-Quality And Cost Route

When the user asks whether a specific loss was caused by fills, fees, failures,
or latency and the exact profiles, loss symbols, investigation start, and cutoff
are already established:

1. Reuse the exact supplied selection, loss symbols, investigation start, and
   cutoff. Do not call `profiles.discover`, broaden the cohort, or replace the
   established interval with a settings-change boundary.
2. Call exactly one full-selection `window_matrix` with
   `matrix_projection=compact` and `adaptive_start` omitted. Call direct
   `execution.quality summary` with
   `decision_target_settings=omit`, the exact selection, investigation start,
   and cutoff, but no `symbols` filter. Call direct `decision.context
   context_rows` for the exact loss symbols with `execution_linkage=executed`,
   `content_view=audit`, `include_reasoning=false`, and
   `include_candle_coverage=false`, replaying the same selection, start, and
   cutoff.
3. Complete the matrix, execution summary, and compact decision context before
   starting exactly one `execution.quality detail_rows` request through
   `analysis.start`, again with `decision_target_settings=omit`, the same
   selection, start, and cutoff, and no `symbols` filter. Consume the complete
   artifact.
4. Do not retrieve an event ledger or decision reasoning unless a separate
   causal question makes that evidence material.

Inspect the relevant exact-symbol groups and rows returned by each complete
`execution.quality` population. Loss symbols narrow only the
`decision.context` request; they are not accepted execution-quality arguments.

For this route, decision-to-fill timing is only retained end-to-end elapsed
time, not pure exchange latency. When no pre-trade BBO or reference price was
retained, spread and slippage are unavailable, not zero.

### Guardrail Effect And Max-Drawdown Replay Route

When the user asks whether a guardrail blocked or warned and supplies the exact
Max Drawdown 1.5/2.5 over six hours comparison, with an exact selection,
investigation start, and cutoff already established:

1. Reuse the exact selection, investigation start, and cutoff. Do not call
   `profiles.discover`, Help, or schema discovery.
2. Call exactly one `settings.read current_bot_settings`, then direct
   `policy.evaluate summary` with the same selection, start, and cutoff.
3. Complete both compact calls before starting exactly one `policy.evaluate
   decision_rows` request through `analysis.start` with the same selection,
   start, and cutoff. Consume its complete artifact exactly once.
4. Only after the decision rows are complete, call exactly one direct
   `policy.replay result_view=comparison` with the same selection, start, and
   cutoff and the JSON object scenario whose `scenario_version` value is the
   string `"2"`:
   `{"scenario_version":"2","scenario_type":"max_drawdown","window_hours":6,"warning_pct":1.5,"critical_pct":2.5,"enforcement":"hard"}`.

Keep observed retained, observed repair, unavailable, and recomputed replay
evidence separate. A prompt restriction is not a deterministic block, and a
completed-candle count proves nothing without an active applicable
machine-readable rule.

### Exposure, Scaling, And Re-Entry Route

When the user asks whether a bot overtraded, added, or re-entered without new
evidence and the exact selection, investigation start, and cutoff are already
established:

1. Reuse the exact supplied selection, established start, and cutoff. Do not
   call `profiles.discover`, Help, schema discovery, or `market.history` unless
   a separate explicit candle or market-path question makes it material.
2. Call exactly one full-selection `window_matrix` with
   `matrix_projection=compact` and `adaptive_start` omitted. Call direct
   `decision.context exposure_metrics` with
   `execution_linkage=all`, `content_view=audit`, `include_reasoning=false`,
   and `include_candle_coverage=false`, plus direct `policy.evaluate summary`,
   replaying the same selection, start, and cutoff on both interval calls.
3. Complete the matrix, exposure metrics, and policy summary before starting
   any durable expansion. Then start and completely consume, one at a time:
   the one all-history `event_detail` ledger with no `start`; exactly one
   relevant `decision.context context_rows` request with
   `execution_linkage=all`, `content_view=audit`, `include_reasoning=true`, and
   `include_candle_coverage=false`; and exactly one `policy.evaluate
   decision_rows` request with the established start. Replay the exact
   selection and cutoff on every call and consume each artifact exactly once.

Use policy evidence, not decision-context absence, to distinguish a warning,
deterministic block, or unavailable enforcement evidence. Every compact call
must be complete before each durable expansion starts, and one durable artifact
must be fully consumed before starting the next.

## 3. Reconstruct Complete Chains

The platform aggregate route also skips this generic section unless a separate
causal question makes a ledger material.

Skip this generic section for the dedicated execution-quality and guardrail
routes above unless a separate causal question makes a ledger material. Reuse
every completed execution-quality call and never repeat it.

Outside the dedicated execution-quality route above, only when the user
explicitly asks about fills, fees, execution failures, or
end-to-end timing, or complete ledger evidence leaves such a material question
unresolved, start
`execution.quality result_view=summary` synchronously with
`decision_target_settings=omit`. Start
`execution.quality result_view=detail_rows` through `analysis.start` only when
complete raw execution rows or target-versus-executed evidence are material.

Use the summary's complete per-profile and exact-symbol fill, closing-outcome,
fee/PnL, execution/audit-category, and timing aggregates to identify the cohort
that can change the answer. Treat unavailable PnL, fees, and chronology as
unknown, not zero, and do not relabel decision-to-fill elapsed time as pure
exchange latency.

Use prospective attempt rows to distinguish prepared, send-started,
acknowledged, failed, retry, and outcome-unknown dispatches in Server and Client
Mode. Keep intent audits separate, retain legacy absence as unavailable, and do
not infer BBO, spread, or latency from nearest timestamps.

Retrieve `positions.episodes` with `result_view=event_detail` for the complete
requested window through `analysis.start` when the synchronous result may be
large. For an explicitly comprehensive full-history audit, make one
all-history unselected `event_detail` read and reuse it for every interval.

Select complete-chunk retrieval for this ledger: make `artifact.manifest` the
first access to its handle. That manifest call commits the handle to complete
mode, so never call `artifact.query` for it. Consume every advertised artifact
chunk exactly once in bounded batches, acknowledging consecutive `{ordinal,
content_hash}` rows through `artifact.resume`. Resume only from its durable
confirmed checkpoint and stop when it reports complete. Reconcile event,
canonical-fill, funding, linked,
ambiguous, unmatched, and manifest counts before making completeness claims.

For every `artifact.read`, branch on the returned `encoding`: encode `text` as
UTF-8 bytes when `encoding=utf-8`, or decode `base64_data` when
`encoding=base64`. Verify raw `byte_count` and `content_hash` before
acknowledging the chunk.

Trace each material campaign without gaps:

```text
settings generation -> analysis -> decision -> execution or block
-> canonical fill -> position change -> later decisions and HOLDs
-> reduction or exit -> realized result
```

Start from executed campaigns. Select losing and winning contrasts, both sides
of material change boundaries, and every suspected failure class. Selection
may reduce illustrations in the answer, never the complete cohort used for a
systemic claim.

## 4. Inspect Decisions And Market Context

Outside the dedicated gain-giveback/exit-timing and
exposure/scaling/re-entry routes above, start `decision.context` for entry,
exit, fill, slippage, latency, or timing questions with
`execution_linkage=executed`. Expand to `execution_linkage=all` when unexecuted
decisions or HOLDs can materially change the conclusion.

After compact evidence identifies a material unresolved reasoning claim, use
exact start/end for the relevant campaign or complete cohort,
`result_view=context_rows`, `content_view=audit`, and
`include_reasoning=true` through `analysis.start`, then consume the complete
artifact. Add only advertised exact `settings_paths` needed by the question;
never guess or flatten a settings path. Use `content_view=verbatim` only when
exact stored prompt, response, or full-context bytes are necessary.

For model/provider reliability questions, first use `decision.context
result_view=model_reasoning_outcome_metrics` over the complete cohort. Compare
requested/actual primary and review lineage, fallback/retry/failure/parse/
refusal state, review changes, final action, execution linkage, and reasoning
availability. Keep `legacy_unknown` separate and expand exact text only for a
compact cohort that can change the conclusion.

Inspect:

- decision-time effective settings and dynamically defined variables;
- final reasoning and available Primary/Review reasoning;
- position, account, and market state available at that moment;
- linked execution, block, fill, or absence of execution; and
- whether repeated evidence, thesis drift, review behavior, or stale context
  affected the chain.

Use `decision.context result_view=exposure_metrics` for complete max-exposure
and retained trading-limit arithmetic. Use a live `account.snapshot` for current
positions; unavailable does not mean flat.

Retrieve market history only for an explicit candle or market-path question, or
when excursion evidence leaves material path ambiguity. Do not call it merely
to validate an excursion summary. Preserve canonical symbols, verify candle
coverage, and use fills or executions for event timing. Candles alone do not
prove event order inside one interval.

## 5. Test The Mechanism

Derive hypotheses from complete chains, then measure each across the relevant
cohort and profitable counterexamples. Separate:

- signal or reasoning error;
- low liquidity, market-session, symbol, or regime mismatch;
- overtrading, rapid re-entry, scaling, reversal, or correlated exposure;
- sizing, leverage, stop, take-profit, and exit management;
- spread, slippage, fees, funding, latency, or rejected execution;
- runtime, provider, or stale-data failure; and
- ordinary adverse variance.

Retrieve `policy.evaluate result_view=summary` synchronously before making a
guardrail claim. Start the same request with `result_view=decision_rows` through
`analysis.start` only when a per-decision enforcement claim is material.

The summary keeps every selected profile explicit and separates evidence mode,
machine-rule state, effect, compliance, bounded execution category, incomplete
reason, and completed-candle evidence. Keep prompt restrictions, observed deterministic
blocks, observed repair, unavailable evidence, and recomputed replay evidence
distinct.

Use `policy.replay` only for deterministic counterfactual gates and thresholds.
Wait for the user to supply the exact scenario or threshold, including its
window and enforcement when applicable; never invent or preemptively replay
default values before that question is asked.
Never describe a replay as live performance or add independent replay gains
together. For Max Drawdown, require each selected profile, its trusted current
wallet-assignment history counts, retained/recomputed status, and any
pre-window divergence that makes later events unobserved. Same-decision
request-local repair must remain distinct from retained and unavailable
evidence and must not write history. Test a combined recommendation with a
combined replay.

Treat replay causal fields as binding. If the overall comparison or relevant
profile has `causal_claim_allowed=false`, do not say the scenario would or
would not have changed the run. Report only `known_immediate_change` for the
evaluable recorded population, disclose the typed incompleteness, and state
that `first_divergence=null` is not proof of no effect.

Use `position.excursions result_view=summary` synchronously first. Start the
same request with `result_view=episode_rows` through `analysis.start` only when
exact campaign rows are material.

Use the summary for complete per-profile and exact ledger-market/market/basis
campaign coverage and extrema. Keep actual-position-path and closing-event
economics separate. Keep population completeness separate from exact-economics
completeness; quarantined or non-exact fills remain enumerable even when
excluded from exact campaign economics.

Use Codex web research only as supplemental outside evidence. Use
`decision.context`, `market.news`, and `market.calendar` for the exact
VTX-managed context the bot received.

## 6. Recommend The Smallest Useful Experiment

Use current settings and advertised evidence to recommend the smallest useful
experiment. Do not search Help or the writable schema merely to name a
read-only recommendation. Discover the writable schema only when the user
explicitly asks for a concrete settings change; then map each supported
mechanism to the least disruptive existing setting, prompt rule, scheduler
change, symbol-universe change, or agentic variable.

For every recommendation, state:

- current and proposed behavior;
- mechanism and supporting chains;
- confidence and evidence limitations;
- profitable behavior that must be preserved;
- collateral risk; and
- a prospective checkpoint measured only after the change becomes effective.

Use `High confidence` only when detailed chains, a material cohort, profitable
counterexamples, and validation agree. Use `Do not change yet` when the sample
or generation is too new.

## Claim Labels

- Distinguish historical account/position snapshot retention from parseability
  and coverage. Say no snapshots were retained only when evidence proves zero
  retained snapshots; otherwise say complete parseable historical snapshot
  coverage is unavailable.

- When pre-trade BBO/reference price, bid-ask spread, realized slippage, or pure
  exchange latency is unavailable, do not exclude execution quality as a cause
  or call it non-primary solely from that incomplete evidence. If retained fees,
  failures, rejections, fill outcomes, or other execution evidence positively
  identifies a contribution or cause, report it precisely. Otherwise state that
  retained evidence did not identify execution as the cause, and name the
  unavailable dimensions.

- `Executed` is an execution fact. Do not call an entry, add, exit, or reversal
  `valid`, `compliant`, or `correct` unless the relevant policy and decision-time
  evidence establish that judgment.
- Decision units describe the instruction. Say the model `issued an N-unit`
  instruction unless canonical executions and fills prove the exact filled
  quantity and resulting position change.
- Preserve every aggregate's operator and denominator. Label MAE/MFE totals as
  sums across their eligible campaigns; never present a total as one campaign's
  maximum or excursion.
- Compare blocked instructions only with the complete ledger's one-row-per-
  decision counts and `recorded_executed` state. Do not use policy-summary
  execution-action counts for that denominator; one flip decision can produce
  multiple execution actions.
- For a multi-action chain, reconcile stated net PnL as eligible realized PnL
  minus every included fee. If exact economics are incomplete, label the net
  unavailable instead of dropping a fee.
- When reporting a fee share, name whether its denominator is realized PnL
  before fees, net PnL after fees, or notional. Never label net loss as gross.
- Attribute observed blocks to their exact retained status and reason category.
  A block count shared across policy rows is not proof that the named policy
  caused those blocks.

## Output

Lead with the verdict. Include:

1. population, cutoff, coverage, and configuration generations;
2. a compact interval performance table;
3. representative winning and losing chains;
4. systemic mechanisms ranked by economic impact;
5. execution and market-context findings;
6. profitable behavior worth preserving;
7. a keep, tune, pause, or observe recommendation matrix; and
8. limitations and the evidence that would change the verdict.

Avoid false precision. A model mistake is possible; missing, misleading,
incomplete, unauthorized, or incorrect VTX tool evidence is the connector issue
to report.
