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
  tokens, private prompts, or raw reasoning traces.
- Treat model reasoning as evidence, not truth. VTX supplies tools and
  calculations but does not grade, correct, or rewrite the final analysis.
- Never impose a population, row, byte, history, or analysis-duration cap.
  Transport pages and artifact chunks are flow control only.
- Do not retrieve irrelevant data merely because it is available. Start with
  the complete cohort that can change the requested decision, then expand when
  a broader population is material or explicitly requested.

## 1. Define The Question

Confirm or infer from the request:

- exact profiles or account population;
- one immutable cutoff and the requested time window;
- recent settings, prompt, model, scheduler, symbol, or variable changes that
  must define a new configuration generation; and
- the decision the user is trying to make.

Replay the exact selection and cutoff on every independent profile-scoped call.
Do not assume `scope=profile` selects a named profile.

## 2. Establish Performance And Generations

Read complete change provenance first when recent configuration changes matter.
Identify when each material change became effective and do not judge the new
generation using earlier results.

Call `positions.episodes` with `result_view=window_matrix` exactly once for the
selection and cutoff. Supply `adaptive_start` when the latest material change
is within seven days. Verify `source_load_count=1` and use the returned rolling,
adaptive, and disjoint windows without repeating them as separate calls.

Use the matrix to compare:

- net and gross PnL, fees, funding, volume, win rate, expectancy, drawdown, and
  completed campaigns;
- instruments, directions, settings generations, sessions, and action
  sequences; and
- recent performance against non-overlapping earlier evidence without blending
  old and new settings.

Do not call every fill a trade. A campaign is the strategy outcome; fills are
execution accounting.

## 3. Reconstruct Complete Chains

Retrieve `positions.episodes` with `result_view=event_detail` for the complete
requested window through `analysis.start` when the synchronous result may be
large. For an explicitly comprehensive full-history audit, make one
all-history unselected `event_detail` read and reuse it for every interval.

Inspect the manifest and consume every relevant artifact chunk exactly once.
Reconcile event, canonical-fill, funding, linked, ambiguous, unmatched, and
manifest counts before making completeness claims.

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

For entry, exit, fill, slippage, latency, or timing questions, start
`decision.context` with `execution_linkage=executed`. Expand to
`execution_linkage=all` when unexecuted decisions or HOLDs can materially change
the conclusion.

For a material campaign or complete reasoning cohort, use exact start/end,
`result_view=context_rows`, `content_view=audit`, and
`include_reasoning=true` through `analysis.start`, then consume the complete
artifact. Add only advertised exact `settings_paths` needed by the question;
never guess or flatten a settings path. Use `content_view=verbatim` only when
exact stored prompt, response, or full-context bytes are necessary.

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

Retrieve market history for detailed campaigns with the same cutoff. Preserve
canonical symbols, verify candle coverage, and use fills or executions for
event timing. Candles alone do not prove event order inside one interval.

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

Use `policy.replay` only for deterministic counterfactual gates and thresholds.
Never describe a replay as live performance or add independent replay gains
together. Test a combined recommendation with a combined replay.

Use Codex web research only as supplemental outside evidence. Use
`decision.context`, `market.news`, and `market.calendar` for the exact
VTX-managed context the bot received.

## 6. Recommend The Smallest Useful Experiment

Discover the writable settings schema before naming a control. Map each
supported mechanism to the least disruptive existing setting, prompt rule,
scheduler change, symbol-universe change, or agentic variable.

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
