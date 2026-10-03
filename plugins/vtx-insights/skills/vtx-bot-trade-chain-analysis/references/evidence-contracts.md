# Trade-Chain Evidence Contracts

Read this file before any durable/raw expansion or when an exact accounting,
execution, policy, excursion, market, or replay claim requires its detailed
contract. The core skill remains the authority for scope and completion.

## Contents

- [Conditional Raw Expansion](#conditional-raw-expansion)
- [Durable Artifact Retrieval](#durable-artifact-retrieval)
  - [Code-Sandbox Hosts](#code-sandbox-hosts)
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
5. Acknowledge each verified batch's consecutive `{ordinal, content_hash}` rows
   through `artifact.resume`, and never reread a verified chunk. Call
   `artifact.resume` with an empty list only to recover after an interruption,
   then retry only the unconfirmed ordinals it reports.
6. Accept retrieval as complete only when resume reports complete and returned
   rows/counts reconcile.

A page, transport batch, or chunk is not an analytical sample. Missing or corrupt
chunks block claims that require the artifact. If advertised recovery cannot
restore integrity or the canonical job deadline expires, stop that retrieval
and mark the affected claim partial or blocked. Distinguish a still-pending job
from a terminal failure and retain its recovery handle privately. Do not keep
restarting the same scan or expand the analysis to compensate for unavailable
evidence.

### Code-Sandbox Hosts

If your host runs tool calls inside a fresh code sandbox per step that keeps
only values you explicitly persist (for example Codex code mode, where only
`store(key, value)` and `load(key)` survive between cells), a tool result held
in sandbox variables is gone when the step ends. Do each batch's retrieval
work in that one step:

1. Read the batch's chunks with `artifact.read`, then decode and verify each
   one's raw `byte_count` and `content_hash` with the helper below.
2. Persist the batch as one value with the host's persistence (Codex:
   `store()`): its text, `vtxChunk.joinText(verified)` for that batch's
   verified chunks, together with their `{ordinal, content_hash}` rows. Key it
   by the artifact handle and the batch's first ordinal, so another artifact
   never overwrites it and repeating the step overwrites that batch instead of
   appending it twice.
3. Only then acknowledge those rows through `artifact.resume`. The server stops
   serving acknowledged chunks, so acknowledging before the text is persisted
   loses it if the step fails. After an interruption, an empty `artifact.resume`
   recovers the checkpoint; acknowledge persisted but unacknowledged rows from
   the stored hashes instead of rereading them.
4. Once resume reports complete, walk the stored batches from ordinal 0: each
   next key is that handle with the previous batch's last ordinal plus one,
   until the manifest's `chunk_count` is reached. A missing key is a gap, so
   stop and recover instead of guessing. Concatenate the stored texts, parse
   them once, and persist the parsed document or the projection the analysis
   needs. Persist serializable values such as strings or plain objects, not
   byte arrays.
5. Output only small summaries such as ordinals, counts, hashes, and the resume
   state, never the chunk text.

Later steps load the persisted value instead of calling `artifact.read` again.
Never reread a verified chunk to recover sandbox state. Such sandboxes may lack
`TextEncoder`, `TextDecoder`, `atob`, `Buffer`, and `crypto`, so paste this
dependency-free helper into the step instead of writing decoding or hashing
code. `vtxChunk.verifyChunk(read)` takes one `artifact.read` return value (the
whole tool result or its `result` object) and returns
`{ordinal, content_hash, bytes}` or throws; acknowledge its `ordinal` and
`content_hash` once the decoded text is persisted. `vtxChunk.joinText(verified)`
joins one batch's verified chunks in ordinal order and decodes the UTF-8 text;
chunks never split a character, so batch texts concatenate exactly.

```js
const vtxChunk = (() => {
  const K = Uint32Array.from([
    0x428a2f98, 0x71374491, 0xb5c0fbcf, 0xe9b5dba5, 0x3956c25b, 0x59f111f1,
    0x923f82a4, 0xab1c5ed5, 0xd807aa98, 0x12835b01, 0x243185be, 0x550c7dc3,
    0x72be5d74, 0x80deb1fe, 0x9bdc06a7, 0xc19bf174, 0xe49b69c1, 0xefbe4786,
    0x0fc19dc6, 0x240ca1cc, 0x2de92c6f, 0x4a7484aa, 0x5cb0a9dc, 0x76f988da,
    0x983e5152, 0xa831c66d, 0xb00327c8, 0xbf597fc7, 0xc6e00bf3, 0xd5a79147,
    0x06ca6351, 0x14292967, 0x27b70a85, 0x2e1b2138, 0x4d2c6dfc, 0x53380d13,
    0x650a7354, 0x766a0abb, 0x81c2c92e, 0x92722c85, 0xa2bfe8a1, 0xa81a664b,
    0xc24b8b70, 0xc76c51a3, 0xd192e819, 0xd6990624, 0xf40e3585, 0x106aa070,
    0x19a4c116, 0x1e376c08, 0x2748774c, 0x34b0bcb5, 0x391c0cb3, 0x4ed8aa4a,
    0x5b9cca4f, 0x682e6ff3, 0x748f82ee, 0x78a5636f, 0x84c87814, 0x8cc70208,
    0x90befffa, 0xa4506ceb, 0xbef9a3f7, 0xc67178f2,
  ]);
  const H = Uint32Array.from([
    0x6a09e667, 0xbb67ae85, 0x3c6ef372, 0xa54ff53a,
    0x510e527f, 0x9b05688c, 0x1f83d9ab, 0x5be0cd19,
  ]);
  const B64 = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";
  const r = (x, n) => (x >>> n) | (x << (32 - n));

  // UTF-8 bytes of a JS string (what TextEncoder would return).
  function utf8Bytes(text) {
    const out = new Uint8Array(text.length * 3);
    let j = 0;
    for (const ch of text) {
      const c = ch.codePointAt(0);
      if (c < 0x80) out[j++] = c;
      else if (c < 0x800) {
        out[j++] = 0xc0 | (c >> 6); out[j++] = 0x80 | (c & 63);
      } else if (c < 0x10000) {
        out[j++] = 0xe0 | (c >> 12); out[j++] = 0x80 | ((c >> 6) & 63);
        out[j++] = 0x80 | (c & 63);
      } else {
        out[j++] = 0xf0 | (c >> 18); out[j++] = 0x80 | ((c >> 12) & 63);
        out[j++] = 0x80 | ((c >> 6) & 63); out[j++] = 0x80 | (c & 63);
      }
    }
    return out.slice(0, j);
  }

  // Raw bytes of standard base64 (what atob would return, as bytes).
  function base64Bytes(data) {
    const clean = data.replace(/[\s=]/g, "");
    const out = new Uint8Array((clean.length * 3) >> 2);
    let acc = 0, bits = 0, j = 0;
    for (const ch of clean) {
      const v = B64.indexOf(ch);
      if (v < 0) throw new Error("invalid base64 character");
      acc = ((acc << 6) | v) & 0xffff; bits += 6;
      if (bits >= 8) { bits -= 8; out[j++] = (acc >> bits) & 255; }
    }
    return out;
  }

  // Text of verified UTF-8 bytes (what TextDecoder would return).
  function utf8Text(bytes) {
    let text = "";
    const points = [];
    for (let i = 0; i < bytes.length;) {
      let c = bytes[i++];
      if (c >= 0xf0) {
        c = ((c & 7) << 18) | ((bytes[i++] & 63) << 12)
          | ((bytes[i++] & 63) << 6) | (bytes[i++] & 63);
      } else if (c >= 0xe0) {
        c = ((c & 15) << 12) | ((bytes[i++] & 63) << 6) | (bytes[i++] & 63);
      } else if (c >= 0xc0) c = ((c & 31) << 6) | (bytes[i++] & 63);
      points.push(c);
      if (points.length === 4096) {
        text += String.fromCodePoint(...points); points.length = 0;
      }
    }
    return text + String.fromCodePoint(...points);
  }

  // Lowercase SHA-256 hex digest of bytes.
  function sha256Hex(bytes) {
    const n = bytes.length, size = (((n + 8) >> 6) + 1) << 6;
    const m = new Uint8Array(size);
    m.set(bytes); m[n] = 0x80;
    const view = new DataView(m.buffer);
    view.setUint32(size - 8, Math.floor(n / 0x20000000));
    view.setUint32(size - 4, (n * 8) >>> 0);
    const h = H.slice(), w = new Uint32Array(64);
    for (let o = 0; o < size; o += 64) {
      for (let i = 0; i < 16; i++) w[i] = view.getUint32(o + i * 4);
      for (let i = 16; i < 64; i++) {
        const x = w[i - 15], y = w[i - 2];
        w[i] = w[i - 16] + w[i - 7] + (r(x, 7) ^ r(x, 18) ^ (x >>> 3))
          + (r(y, 17) ^ r(y, 19) ^ (y >>> 10));
      }
      let [a, b, c, d, e, f, g, k] = h;
      for (let i = 0; i < 64; i++) {
        const t1 = (k + (r(e, 6) ^ r(e, 11) ^ r(e, 25)) + ((e & f) ^ (~e & g))
          + K[i] + w[i]) | 0;
        const t2 = ((r(a, 2) ^ r(a, 13) ^ r(a, 22))
          + ((a & b) ^ (a & c) ^ (b & c))) | 0;
        k = g; g = f; f = e; e = (d + t1) | 0;
        d = c; c = b; b = a; a = (t1 + t2) | 0;
      }
      h[0] += a; h[1] += b; h[2] += c; h[3] += d;
      h[4] += e; h[5] += f; h[6] += g; h[7] += k;
    }
    return Array.from(h, (x) => x.toString(16).padStart(8, "0")).join("");
  }

  // Raw bytes of one artifact.read result, or an error if they do not verify.
  function verifyChunk(read) {
    const chunk = read && read.result && read.result.encoding ? read.result : read;
    const bytes = chunk.encoding === "base64"
      ? base64Bytes(chunk.base64_data)
      : chunk.encoding === "utf-8" ? utf8Bytes(chunk.text) : null;
    if (!bytes) throw new Error(`chunk ${chunk.ordinal}: unknown encoding`);
    const hash = sha256Hex(bytes);
    if (bytes.length !== chunk.byte_count || hash !== chunk.content_hash) {
      throw new Error(`chunk ${chunk.ordinal} failed byte_count/content_hash`);
    }
    return { ordinal: chunk.ordinal, content_hash: hash, bytes };
  }

  // Text of verified chunks joined in ordinal order.
  function joinText(verified) {
    const ordered = [...verified].sort((x, y) => x.ordinal - y.ordinal);
    const all = new Uint8Array(ordered.reduce((s, x) => s + x.bytes.length, 0));
    let at = 0;
    for (const x of ordered) { all.set(x.bytes, at); at += x.bytes.length; }
    return utf8Text(all);
  }

  return { utf8Bytes, base64Bytes, utf8Text, sha256Hex, verifyChunk, joinText };
})();
```

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
