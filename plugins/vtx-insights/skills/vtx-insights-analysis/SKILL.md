---
name: vtx-insights-analysis
description: >-
  Use VTX Insights for general VTX analysis and explicitly authorized operations:
  discover profiles, compare public or owned populations, retrieve complete evidence,
  manage settings and profiles, configure write-only connections, control bots, and
  place requested trades. Route deep causal bot-performance reviews,
  settings-generation comparisons, execution diagnoses, and win/loss investigations
  to the bundled $vtx-bot-trade-chain-analysis skill.
---

# VTX Insights

Use VTX Insights as the VTX data and action source. Treat this skill as the general router and safety contract; do not recreate the specialist trade-chain workflow here.

## Route The Request

- Use `$vtx-bot-trade-chain-analysis` for questions about why bots win or lose, whether a problem is systemic, how settings generations changed outcomes, execution quality, overtrading, exits, or evidence-backed bot improvements.
- Use the live capability registry for ordinary VTX questions, public or owned-profile comparisons, settings and profile work, connection management, bot control, and explicitly requested trading actions. Do not guess capability names or input fields.
- Resolve the requested population explicitly: owned profiles, exact public handles or wallets, or the whole public platform. Preserve one explicit time window and cutoff across related reads.
- Use `help.search` and `help.open` only for VTX product behavior. Do not substitute Help for trading evidence.

## Preserve Complete Evidence

- Inspect coverage, provenance, freshness, warnings, and unavailable fields before making claims. Unknown is not zero, and replay is not observed performance.
- For large or slow reads, call `analysis.start` with the original capability and unchanged arguments, poll `analysis.status`, and consume its immutable artifact. Never shrink or sample the requested population to fit one response window.
- Choose one artifact mode. For targeted values, begin with `artifact.query path=[]`, discover exact paths, and use `artifact.query_many` for several small indexed descendants. If that same bounded batch exceeds the response window, submit it unchanged through `analysis.start` and consume the separate result artifact. For complete retrieval, begin with `artifact.manifest`, read every advertised chunk, and acknowledge consecutive hashes through `artifact.resume`. Never mix query and complete modes on one handle.
- For every `artifact.read`, use `text` as UTF-8 bytes when `encoding=utf-8`, or decode `base64_data` when `encoding=base64`; verify `byte_count` and `content_hash` before acknowledgement.
- When exact retained reasoning is material, use `decision.context` with exact start and end, `execution_linkage=all`, `result_view=context_rows`, `content_view=audit`, and `include_reasoning=true`. Request only advertised exact `settings_paths`; never guess or flatten a path. Use `content_view=verbatim` only for exact raw prompt, model-response, or full-context bytes.

## Authorize And Verify Actions

- Request analysis, settings, bot-control, profile-management, connection-management, and trading permissions separately. Actions are limited to profiles the user owns.
- Before a consequential action, resolve the exact profile and current state, preview when supported, explain the effect, obtain the required approval, and use a stable idempotency key. Verify the receipt against effective readback or observed runtime/exchange state.
- Keep settings reads and fleet mutations compact and exact. For an exact owned subset, call `settings.read` with `selection={"population":"owned_profiles","handles":["@Profile"]}` and optional canonical `settings_paths`. Preview and apply fleet changes with `selection={"population":"selected","profiles":["@Profile"]}`, shared `updates`, and only the necessary `per_profile_updates`; explicitly request `result_view=compact` for a large apply. A projected `stored_only` read preserves the full stored-document `settings_version` but omits the full settings body and complete-replacement evidence, so use it for a fenced patch/readback rather than constructing `update_mode=complete`.
- Treat provider and exchange credentials as write-only. Never request, recover, echo, log, or place them in prose, files, screenshots, or artifacts.
- Report desired and confirmed bot state separately. A Client Mode start may remain `waiting_for_owner`; it is not running until current ownership and heartbeat evidence confirm it.
- Trade only when the user explicitly requests it. Use the narrow single-profile capability for one action and `trade.batch` for an approved heterogeneous fleet plan. Require the capability's exact confirmation literal for closing every position, and report each exchange result without inferring success from submission.

Use your own judgment for tools, calculations, recommendations, and the final answer. VTX does not grade or rewrite model conclusions. Billing and account-security management remain in VTX.

For setup, permission management, host compatibility, and the current capability registry, open https://vtxmacro.com/insights.
