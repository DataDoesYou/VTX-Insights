---
name: vtx-insights-analysis
description: Analyze, optimize, manage fleet profiles and write-only connections, automate, and trade VTX with separately authorized Insights tools. Use for bot reviews, public or whole-platform comparisons, settings improvements, profile provisioning, provider/exchange connection changes, Server Mode Trader or Assistant control, explicitly requested trading operations on profiles the user owns, policy replay, stored reasoning, and VTX product questions.
---

# VTX Insights

Use VTX Insights as the VTX trading-data and action source. The user chooses analysis, settings, profile-management, write-only connection, automation-control, and trading permissions separately.

- Resolve the requested population explicitly: the user's profiles, named public handles or wallets, or the whole public VTX platform.
- Inspect returned coverage, provenance, as-of times, warnings, and artifact manifests before making completeness claims.
- For a large population, a potentially long computation, or any synchronous response timeout, call `analysis.start` with the intended read capability and its unchanged arguments. Poll `analysis.status` until it returns the immutable artifact, then inspect that artifact. Do not narrow or sample the user's request merely to fit one request window.
- For a deep bot performance diagnosis, use the bundled `$vtx-bot-trade-chain-analysis` skill. It reconstructs settings generations, decisions, executions, fills, positions, exits, fees, funding, PnL, and market context instead of diagnosing from aggregate PnL. Its value-first workflow never caps a requested population and does not retrieve unrelated HOLD decisions when they cannot change the answer.
- For a complete reasoning audit, call broad raw `decision.context` with exact `start`/`end`, `execution_linkage=all`, `result_view=context_rows`, `content_view=audit`, and `include_reasoning=true`, then consume every artifact chunk. This retains unexecuted HOLDs, exact final reasoning, and retained Primary/Review Reasoning Traces without generated episode or decision IDs or raw prompt/model-response bytes. Use only advertised exact `settings_paths` needed by the question, and never guess or flatten a path. Use `content_view=verbatim` only when exact raw prompt, model-response, or full-context bytes are required.
- For max-exposure or retained trading-limit questions over large decision populations, use `decision.context` with `result_view=exposure_metrics`; use `context_rows` when individual decision context is required.
- Preserve the distinction between observed history and a replayed counterfactual. Never describe a replay as a live result.
- Use the host's native web, file, terminal, and calculation tools when they materially improve the answer.
- Use your own judgment for tools, calculations, recommendations, and the final answer. VTX does not grade, rewrite, or verify it.
- If a result is delivered as an artifact, inspect the manifest and read or query all relevant chunks rather than inferring from the inline sample.
- Use `help.search` and `help.open` only for VTX product behavior. Do not substitute help text for trading or market evidence.
- Before changing settings, inspect the current configuration, identify the exact owned profile, and use a stable idempotency key for the approved change.
- Use profile lifecycle tools for fleet provisioning. Cloning carries settings only; connect provider or exchange access separately with the connection permission.
- Treat every provider or exchange value as write-only. Never ask Insights to read, export, recover, echo, or place the value in prose, files, screenshots, or artifacts; rely on returned configured counts and validation status.
- Before starting or stopping Trader or Assistant automation, inspect the current persisted bot status and report the resulting state.
- Perform a trading action only when the user's request authorizes it and only for owned profiles. Use the narrow single-profile tool for one operation and `trade.batch` for an approved heterogeneous fleet plan. Report every returned exchange and post-action state honestly; never infer success from submission alone.
- Require the tool's explicit confirmation literal before closing every position. Use stable idempotency keys for individual actions and the complete ordered batch.

Public VTX trading data is available for analysis. Billing and account-security management remain in VTX.

For setup, grant management, troubleshooting, and the current host compatibility matrix, open https://vtxmacro.com/insights.
