# Trade-Chain Evidence Contracts

Read this file before any durable/raw expansion or when an exact accounting,
execution, policy, excursion, market, or replay claim requires its detailed
contract. The core skill remains the authority for scope and completion.

## Contents

- [Conditional Raw Expansion](#conditional-raw-expansion)
- [Durable Artifact Retrieval](#durable-artifact-retrieval)
- [Position Ledger Reconciliation](#position-ledger-reconciliation)
- [Decision Context And Reasoning](#decision-context-and-reasoning)
- [Dynamic Inputs And Prompt Evidence](#dynamic-inputs-and-prompt-evidence)
- [Execution Evidence](#execution-evidence)
- [Policy And Replay Evidence](#policy-and-replay-evidence)
- [Excursion And Market Evidence](#excursion-and-market-evidence)

## Conditional Raw Expansion

Raw event or reasoning transfer is not required merely because a compact summary
exists. Start an expansion only after naming the unresolved row-level claim and
the compact evidence that could not answer it.

- Start `positions.episodes result_view=event_detail` through `analysis.start`,
  scoped as the core skill's **Reconstruct Complete Chains** section describes.
- Use one raw ledger for the selected interval. Do not repeat it per window,
  generation, symbol, or illustration.
- Bound reasoning `context_rows` to the exact interval and available symbol
  selector for the material campaign/cohort, with the core skill's
  `execution_linkage` rule.
- A broad exact-interval all-linkage reasoning artifact is justified only for an
  explicit population-wide reasoning or exhaustive semantic-compliance claim.
- `content_view=audit` is the default. Use `verbatim` only for indispensable
  exact prompt, response, or full-context bytes, and never expose private bodies
  in user-facing output.

## Durable Artifact Retrieval

A compact result may still scan a large complete population. When the supplied
selection, interval, or a prior complete count makes the scan known-large, start
it directly through `analysis.start` without a synchronous probe. This applies
to broad provenance, prompt-lineage, input-catalog, and model-outcome scans as
well as raw expansions. Compact is a payload shape, not a duration promise.

Choose the access mode with the first artifact call and do not mix modes for one
handle:

- `artifact.query path=[]` chooses indexed query mode for compact paths.
- After path discovery, batch indexed paths with `artifact.query_many` as step 4
  of the core skill's **Platform Aggregate Observability Route** describes; that
  batching rule applies to every query-mode artifact, not only platform scans.
- If a bounded `artifact.query_many` batch exceeds the synchronous response
  window, submit that exact batch through `analysis.start`; consume its separate
  result artifact in complete-chunk mode and keep the source handle in query
  mode.
- `artifact.manifest` chooses complete-chunk mode for a required full artifact.
- If a targeted query needs durable execution, set `result_projection=bounded`
  for navigation or exact-if-it-fits evidence. Use `exact` only when complete
  selected bytes are material; omission preserves the legacy durable-exact
  behavior. A nonzero `structure_offset` durable handoff requires bounded. Exact
  or omitted requires `structure_offset=0` as a new complete-value request.

For complete-chunk mode:

1. Record the handle, selection, cutoff, interval, arguments, manifest counts,
   byte count, and content hash.
2. Read chunks in ascending non-overlapping batches no larger than the returned
   parallel-read maximum.
3. For every `artifact.read`, branch on `encoding`: encode `text` as UTF-8 bytes
   for `encoding=utf-8`, or decode `base64_data` for `encoding=base64`.
4. Verify raw `byte_count` and `content_hash` before acknowledging the ordinal.
5. Acknowledge consecutive `{ordinal, content_hash}` rows through
   `artifact.resume`. After interruption, recover the confirmed checkpoint and
   retry only unconfirmed ordinals.
6. Accept retrieval as complete only when resume reports complete and returned
   rows/counts reconcile.

A page, transport batch, or chunk is not an analytical sample. Missing or corrupt
chunks block claims that require the artifact. If advertised recovery cannot
restore integrity or the canonical job deadline expires, stop that retrieval
and mark the affected claim partial or blocked. Distinguish a still-pending job
from a terminal failure and retain its recovery handle privately. Do not keep
restarting the same scan or expand the analysis to compensate for unavailable
evidence.

## Position Ledger Reconciliation

When event detail is material, reconcile:

- reported event count to returned decision-event rows;
- observed canonical fill count to returned canonical fill rows;
- linked, linked-outside-interval, correlated, ambiguous, conflicted, and
  unmatched fill counts to observed fills;
- raw funding count and per-currency conservation;
- exact-economics fill count and linkage/funding reason distributions; and
- artifact manifest counts/bytes and deterministic source-content hash.

Only direct persisted linkage or unique venue identity enters exact economics.
Keep correlated, ambiguous, conflicted, and unmatched rows enumerable but out of
exact campaign economics. Funding remains unattributed unless a unique exact
half-open position interval proves attribution. Zero retained funding does not
prove zero exchange funding.

## Decision Context And Reasoning

Start compact, with `execution_linkage=all` on every view below (the default
`linked` drops HOLDs and other decisions without an execution record, and prompt lineage rejects it):

- `model_reasoning_outcome_metrics` for complete provider/model, fallback,
  retry/failure/parse/refusal, Primary/Review/final action, review-change,
  reasoning-availability, execution-linkage, and event-economics groups. Its
  invocation fingerprint evidence is aggregate cardinality only and exact
  invocation fingerprint values are intentionally omitted. Use `prompt_lineage`
  for the distinct canonical configured/delivered prompt and runtime identities;
- `prompt_lineage` with `lineage_projection=generation_summary` for body-free
  configured/delivered prompt consistency and provider populations; use
  `decision_rows` for a named unresolved per-decision lineage claim or when an
  exact generation-performance question requires ordered run boundaries;
- for semantic review, use configured `bundle_refs` returned by prompt
  lineage as exact `prompt_bundle_refs` in a smallest-run
  `content_view=verbatim` prompt-lineage request. This retrieves only material
  instruction bodies; do not infer instructions from hashes or transfer every
  per-decision context. Prompts are public, so this works for public foreign
  profiles too;
- body-free `effective_input_catalog audit` for dynamic input path, definition,
  availability, template, and hash/byte discovery without decision rows or
  values; and
- `exposure_metrics` for complete server-computed retained-limit arithmetic.

When exact reasoning is material, start the exact `context_rows` request through
`analysis.start` without a synchronous probe if `include_reasoning=true`,
`include_candle_coverage=true`, or `content_view=verbatim`. Replay the same
selection, start/end, symbols, linkage, settings paths, and input paths. Consume
the selected artifact completely.

For material decisions inspect final Reasoning, retained Primary/Review traces,
exact decision-time settings and inputs, position/account state, linked
execution/block/absence, and market information available at that boundary. A
null trace means unavailable or not retained, not that no reasoning occurred.

## Dynamic Inputs And Prompt Evidence

- Discover inputs from retained manifests, templates, definitions, and context;
  do not assume a fixed built-in catalog or infer semantics from a field name.
- Use `effective_input_catalog` only when an input claim or confounder is
  material. It is an aggregate discovery screen, not a temporal map. Multiple
  material definition or availability identities require bounded
  `context_rows` over the exact runs and returned `input_paths`; otherwise label
  the timing unresolved.
- Preserve every explicitly retained definition/version, encoding/type, units,
  scope, timeframe, availability, enablement/enforcement state, safe value/hash,
  and source. When the catalog lacks a semantic dimension, mark it unavailable
  and use owner-scoped provenance only for any recoverable historical
  definition; a generic identity is not a semantic definition.
- Do not collapse differently scoped/timed values or backfill current values.
- Treat user-defined inputs as first-class evidence and prove actual prompt
  visibility/use before attributing behavior.
- A configured prompt hash, rendered prompt hash, and prompt-assembly identity
  are distinct. Dynamic per-cycle values do not create prompt generations.
- Prompt activation, storage, delivery, apparent comprehension, semantic
  compliance, execution, and economic outcome are separate claims.

## Execution Evidence

Use `execution.quality summary` before `detail_rows`. Inspect complete
per-profile and exact-symbol fill, close, fee/PnL, execution-category, audit, and
timing aggregates. Treat unknown PnL, fees, or chronology as unavailable.

Prospective attempt states are prepared, send-started, acknowledged, failed,
retry, and outcome-unknown. A unique acknowledged attempt may use directly
linked canonical fills and retained midpoint/mark evidence. Do not attach later
retry fills or infer missing legacy attempt, BBO, spread, slippage, lifecycle, or
pure exchange latency from nearest timestamps. Decision-to-fill duration is
retained end-to-end time, not pure exchange latency.

## Policy And Replay Evidence

Use `policy.evaluate summary` before decision rows. Preserve exact asset,
timeframe, rule, evidence mode, machine-rule state, comparator, threshold,
effect, compliance, execution status, and incomplete reason. A natural-language
restriction remains `not_machine_evaluable`; completed-candle count alone does
not prove compliance or violation.

Keep observed retained, observed repair, recomputed counterfactual, and
unavailable evidence distinct. Treat replay fields as binding:

- If `causal_claim_allowed=false`, do not say the scenario would or would not
  have changed the run.
- Report only `known_immediate_change`, typed incompleteness, and downstream
  `unobserved_after_divergence`.
- `first_divergence=null` is not proof of no effect.
- Do not add independent scenario improvements. Use a canonical combined
  scenario when advertised or report them separately.

## Excursion And Market Evidence

Use `position.excursions summary` before `episode_rows`. Keep
actual-position-path and closing-event groups separate. Reconcile eligible
counts, exact/possible extrema, candle coverage/gaps, boundary/flip uncertainty,
and the before-funding realized-PnL-minus-fees label. Judge realized outcomes by
`economics_status` and extrema by `path_status`; `status` is their union. The
view is not an account equity curve or margin simulator.

Use cutoff-pinned fully completed `market.history` candles only for an explicit
market-path/regime claim or unresolved excursion path. Preserve canonical market
symbols/prefixes and reconcile gaps before MFE, MAE, forward-return, breakout,
reversal, or new-high/low claims. Candles do not prove event ordering inside an
interval.

Open-position, marked-equity, and claim-label accounting rules live in the core
skill's **Establish Performance And Generations** and **Claim Labels** sections.
