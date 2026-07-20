---
name: vtx-insights-analysis
description: Analyze, optimize, automate, and trade VTX with separately authorized Insights tools. Use for bot reviews, public or whole-platform comparisons, settings improvements, Server Mode control, explicitly requested orders on profiles the user owns, policy replay, stored reasoning, and VTX product questions.
---

# VTX Insights

Use VTX Insights as the VTX trading-data and action source. The user chooses analysis, settings, automation-control, and trading permissions separately.

- Resolve the requested population explicitly: the user's profiles, named public handles or wallets, or the whole public VTX platform.
- Inspect returned coverage, provenance, as-of times, warnings, and artifact manifests before making completeness claims.
- For a large population, a potentially long computation, or any synchronous response timeout, call `analysis.start` with the intended read capability and its unchanged arguments. Poll `analysis.status` until it returns the immutable artifact, then inspect that artifact. Do not narrow or sample the user's request merely to fit one request window.
- For max-exposure or retained trading-limit questions over large decision populations, use `decision.context` with `result_view=exposure_metrics`; use `context_rows` when individual decision context is required.
- Preserve the distinction between observed history and a replayed counterfactual. Never describe a replay as a live result.
- Use the host's native web, file, terminal, and calculation tools when they materially improve the answer.
- Use your own judgment for tools, calculations, recommendations, and the final answer. VTX does not grade, rewrite, or verify it.
- If a result is delivered as an artifact, inspect the manifest and read or query all relevant chunks rather than inferring from the inline sample.
- Use `help.search` and `help.open` only for VTX product behavior. Do not substitute help text for trading or market evidence.
- Before changing settings, inspect the current configuration, identify the exact owned profile, and use a stable idempotency key for the approved change.
- Before starting or stopping automation, inspect the current persisted bot status and report the resulting state.
- Place or cancel an order only when the user's request is explicit, only for an owned profile, and report the returned exchange and post-action state honestly.

Public VTX trading data is available for analysis. Billing and account-security management remain in VTX.

For setup, grant management, troubleshooting, and the current host compatibility matrix, open https://vtxmacro.com/insights.
