# Trade-Chain Analysis Contracts

Read this file when a claim needs generation attribution, activity or
comparability checks, systemic or economic analysis, semantic compliance
audits, market regime or news context, Insights evaluation, or completion
reporting. It extends the core skill and
inherits its selection, cutoff, linkage, and on-demand expansion rules.

## Contents

- [Generation Attribution Steps](#generation-attribution-steps)
- [Effective Generation Contract](#effective-generation-contract)
- [Activity And Comparability Contract](#activity-and-comparability-contract)
- [Economic And Systemic Analysis](#economic-and-systemic-analysis)
- [Chain And Reasoning Audit](#chain-and-reasoning-audit)
- [Market Regime And News](#market-regime-and-news)
- [Evaluate Insights](#evaluate-insights)
- [Recommendation And Completion](#recommendation-and-completion)

## Generation Attribution Steps

Use these steps when the claim compares prompt, settings, model, input, symbol,
or deployed-code generations. The core skill's matrix and since-change rules
still choose the windows.

1. For one exact requested `start`/`end` interval, `positions.episodes
   result_view=metrics` is enough. Start one full-selection `window_matrix`
   through `analysis.start` when canonical rolling, adaptive, disjoint, or
   multiple caller-supplied or generation windows are material. On that matrix,
   select `matrix_projection=summary` when only exact overall/profile/asset
   economics, coverage, conservation, and inherited management accounting are
   required; summary omits policy, generation, prompt, provider, and sequence
   groups. `matrix_projection` applies only to `window_matrix`.
2. For generation attribution on owned profiles, complete `runtime.provenance
   change_causality aggregate_metrics` over the entire requested interval and
   retain every prompt, model, input-definition, symbol, deployment, release,
   and runtime-code candidate boundary. For a public foreign profile, use only
   its body-free public projection; private prompt, settings, request, and
   runtime bodies are unavailable and must never be inferred.
3. For prompt consumption or prompt-performance attribution, complete one
   `decision.context result_view=prompt_lineage` read over the exact interval
   with `lineage_projection=generation_summary` and `execution_linkage=all`
   before assigning decisions or economics to a prompt cohort. Request
   `lineage_projection=decision_rows` only for a named unresolved per-decision
   lineage claim or when the literal question requires ordered prompt/runtime
   run boundaries that the generation summary does not expose.
4. When a literal input claim or unresolved input confounder can change the
   conclusion, run body-free `decision.context
   result_view=effective_input_catalog`, `content_view=audit`, and
   `execution_linkage=all`. Use it only as a discovery/confounder screen. Its
   aggregate counts do not locate time boundaries. If a material path has
   multiple definition or availability identities, request bounded
   `context_rows` for the exact relevant runs and discovered `input_paths` to
   map them over time. Otherwise skip the scan and mark input influence not
   evaluated. Treat unretained schema, type, units, scope, timeframe, and
   enablement as unavailable.
5. For a generation-performance claim, use decision lineage to establish
   ordered half-open run boundaries, then put every effective run intersecting
   the requested interval for every profile in the claim in one full-selection
   `window_matrix` started through `analysis.start` with
   `matrix_projection=compact`. Internal runs ending at the next boundary use
   `end_inclusive=false`; the terminal run ending at the inclusive analytical
   cutoff uses `end_inclusive=true`. Read the applicable profile cohort from
   each window. If the advertised interval shape cannot represent the complete
   run set, mark the join partial or blocked; do not approximate adjacent runs
   with overlapping inclusive metrics calls. Use selected position/event linkage
   when a campaign crosses a boundary. Never assign economics from
   first/last/count summaries alone. State simultaneous-change confounding and
   inherited-position coverage before causal attribution.

## Effective Generation Contract

- Treat settings receipts, Changelog rows, activations, deployments, commits,
  and releases as candidate boundaries, not generation proof.
- Establish the prompt cohort from configured prompt-bundle, prompt-assembly,
  and prompt-contract identities with exact decision-snapshot and rendered-
  delivery consistency confirmation. Keep rendered prompt identity out of the
  generation identity.
- Keep runtime-code identity independent. A deployment creates a prompt cohort
  only when retained prompt-bundle, prompt-assembly, prompt-contract, or
  rendering-contract identity changes; otherwise it creates only a runtime-code
  boundary.
- Treat model bindings, enabled input definitions/schema/enablement, symbol
  assignment, and material behavior settings as effective-time boundaries.
  Split the prompt cohort for them only when prompt assembly also changes.
- A changed model is not evidence that one model is intrinsically stronger.
  Compare the same prompt/runtime/input/symbol/regime cohort when available;
  otherwise label model-capacity attribution confounded.
- Build maximal half-open effective-time intervals where prompt cohort,
  runtime-code identity, model binding, enabled inputs, symbol assignment, and
  material behavior settings are stable. Let one prompt cohort span several
  time generations.
- Do not infer an input-definition boundary from aggregate catalog counts.
  Require exact decision-time mapping for every material boundary or label that
  axis unresolved.
- Attribute each decision to its active prompt/runtime pair. Keep fills with the
  originating decision and disclose campaigns that cross a boundary.
- When prompt and runtime change together, label causality confounded unless an
  overlapping or non-overlapping validation cohort isolates one axis.
- Activation `consumption_status` alone is not proof. Contradicted activation and
  exact snapshot evidence blocks activation-based attribution; exact snapshot
  identity may independently establish the actual cohort.
- Treat `use_my_averages` as UI-only, never a decision input, generation
  boundary, or performance explanation.

## Activity And Comparability Contract

For each profile and comparison interval:

- Establish recorded decision periods, decision counts, and material gaps.
  Distinguish successful decisions (including HOLD), failed attempts, confirmed
  stops, and unknown activity. Corroborate gaps with retained lifecycle,
  heartbeat, lease, or runtime evidence when available; a lack of logs or fills
  alone does not prove the bot was stopped. Neither first/last timestamps nor
  a current running status establishes continuous historical operation.
- Trace exposure entering, spanning, and leaving material gaps. Separate active
  management from positions carried without observed decisions. Exchange fills
  can occur without an active model cycle. Do not treat an unobserved interval
  as a deliberate HOLD or zero exposure, or a lack of realized closes as a lack
  of performance. Quantify unrealized changes only with retained valuation or
  market-path evidence; otherwise preserve their unavailability.
- Locate each profile's first proven consumption of a relevant change after a
  gap. Keep commit, deployment, settings activation, and consumption times
  separate. If several changes first appear together on resumption, report them
  as confounded rather than selecting one by proximity to a PnL turning point.
- Attribute entry, additions, intervening management, reductions, and exit to
  their actual prompt/runtime generations. A brief prompt change at exit does
  not earn credit for the whole campaign; a later restoration does not rewrite
  the exit's attribution. Separate when gains accumulated from when they were
  realized, retaining the campaign economics and cross-boundary uncertainty.
- State whether activity coverage, exposure, symbols, generations, and market
  conditions support the requested comparison. Preserve the requested calendar
  results, but do not use calendar PnL alone to rank prompts or changes across
  materially different participation. Report decision/exposure denominators
  alongside any normalized comparison; use active hours only when established,
  never by summing assumed cadence across gaps. Label unresolved attribution
  partial or blocked rather than presenting an unmatched profile as a control.

## Economic And Systemic Analysis

For every selected profile, required interval, and relevant generation:

- report retained net/gross PnL, fees, volume, completed campaigns, win rate,
  profit factor, average win/loss, drawdown, action frequency, and open versus
  completed outcomes;
- normalize by equity, exposure, or capital at risk only when retained evidence
  supports the denominator;
- stratify as material by instrument, direction, model, market regime, action
  sequence, and dynamically discovered inputs;
- separate decision/execution association from completed-campaign outcome;
- preserve exact user-supplied periods and validate material conclusions on a
  non-overlapping interval or generation when possible; and
- measure each proposed failure mechanism across the complete compact cohort,
  compare profitable counterexamples, and estimate collateral damage.

When the user supplies an asymmetric payoff objective, define its test before
examining outcomes. Use market evidence independent of bot PnL to classify the
material regimes, then report regime prevalence, conditional net PnL and
drawdown, exposure and action participation, loss velocity, contribution
concentration, upside capture, and bad-regime bleed. Compare those measurements
with the stated objective; never hardcode a preferred regime prevalence.

## Chain And Reasoning Audit

Trace chains as the core skill's **Reconstruct Complete Chains** section
describes.

For material actions capture timestamp, action, requested and filled size,
exposure, price, fees, position before/after, PnL, exact retained inputs, final
Reasoning, available Primary/Review traces, execution outcome, MFE/MAE, and
market path. Separate analysis error, timing, regime mismatch, exposure path,
duplicate evidence, scaling, reversal, exit management, execution quality, fee
bleed, and ordinary variance.

For semantic prompt compliance:

1. Derive the material instruction set from the exact effective prompt cohort.
   On owned profiles, take exact configured bundle references from prompt
   lineage and retrieve only the material bodies with `prompt_bundle_refs`,
   `content_view=verbatim`, and `execution_linkage=all` (prompt lineage rejects
   any other linkage), bounded to the smallest known run interval. Never infer
   instructions from hashes or fetch all per-decision contexts merely to recover
   one prompt body. Foreign private bodies remain unavailable.
2. Define the complete eligible denominator from compact metrics and economics.
3. Select and disclose a deterministic material audit cohort covering every
   material instruction, generation, action type, observed failure class,
   profitable/losing structure, and Primary/Review disagreement.
4. Classify each audited decision with sufficient retained evidence as
   `followed`, `violated`, or `indeterminate` and report
   `audited N / eligible M`, unavailable count, reasoning coverage, execution
   status, and economic association.

Never claim population-wide semantic compliance unless every eligible decision
was actually reviewed. If the user explicitly requires every eligible decision
and complete evidence is infeasible to inspect, mark the claim `blocked` rather
than silently sampling or using phrase matching or deterministic answer grading.

## Market Regime And News

- A regime claim requires cutoff-pinned completed candles with reconciled
  coverage and gaps. Period labels or PnL alone do not prove trend, range,
  volatility, reversal, breakout, or new-high/low regimes.
- Preserve exact canonical market symbols and prefixes.
- An explicit end returns only candles fully completed by the cutoff.
- Retrieve a current partial tail separately only when that live question is
  material. Label it outside the pinned historical cohort; never use it to
  backfill historical decisions or bypass the common cutoff.
- Use the smallest available completed timeframe for path shape,
  and use fills or executions for exact event timing. Candles do not prove
  intrabar ordering; state residual
  ambiguity.

Historical news and calendar rules live in the core skill.

## Evaluate Insights

Run this section only when the user asks whether Insights is accurate, complete,
performant, or fit for the workflow.

1. Evaluate accuracy first: exact selection/cutoff replay, schema/definition
   fidelity, included/excluded and zero-event populations, source coverage,
   count/economics conservation, cross-view agreement, typed missingness,
   deterministic hashes, and independently readable exact-row agreement.
2. Treat material mismatch or incomplete transport as an accuracy blocker.
3. Evaluate performance only after recording the accuracy verdict. Measure wall
   time and exposed authorization, queue, query, calculation, materialization,
   transfer, resume, and parse time plus calls, retries, timeouts, bytes, chunks,
   throughput, redundant work, and recovery behavior.
4. Separate tool limits from missing source retention and analytical uncertainty.
   Report a measured bottleneck to VTX with its evidence instead of guessing at
   a fix.

## Recommendation And Completion

When recommendations are requested or supported by a confirmed mechanism,
recommend the smallest existing control or prompt experiment that removes a
material loss cluster without disproportionately removing profitable structure.
State current/proposed state, mechanism, evidence, confidence, expected effect,
collateral risk, evaluation checkpoint, and what should remain unchanged.

For an effective-generation analysis, report the applicable dimensions below.
Mark unrequested dimensions `not evaluated` when needed to explain limits; they
do not create additional retrieval or completion requirements. Keep every
material requested claim subject to the complete/partial/blocked rule.

1. profiles, cutoff, intervals, complete compact economics, and activity/exposure
   comparability for performance comparisons;
2. prompt, runtime-code, and effective-time generation coverage;
3. prompt consumption and semantic-compliance coverage;
4. model/review lineage and reasoning coverage;
5. dynamic input definition, availability, value, and demonstrated use;
6. instrument, direction, regime, market path, and news context;
7. profitable/losing chains, execution, fees, and inherited exposure;
8. prompt-versus-code confounding and retention limits;
9. Insights accuracy/performance when requested; and
10. recommendation, collateral risk, checkpoint, and unchanged controls.

Lead with the verdict and separate measured fact, inference, and unavailable
evidence. Avoid false precision. A controlled observation window is preferable
to manufactured confidence.
