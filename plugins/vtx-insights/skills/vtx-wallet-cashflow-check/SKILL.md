---
name: vtx-wallet-cashflow-check
description: Check a selected VTX bot wallet's cashflows, fills, and capital-based returns using local Hyperliquid reads and the official explorer. Use to explain equity changes or close ROI evidence gaps; never originate exchange requests from the VTX VPS.
---

# VTX Wallet Cashflow Check

Use this for a requested wallet cashflow or return check, especially when retained
Insights data has an uncovered interval. For a requested performance calculation
or chart, also read [Return calculations](references/returns.md).

## Scope and source

- Reuse the user's exact bot, wallet, time interval, and analytical cutoff.
  A request to check one bot does not authorize a fleet scan.
- Resolve a supplied bot handle with `profiles.discover` through VTX Insights
  when its wallet is not already available in the current evidence. Preserve
  ownership labels. Do not request settings or trading permissions for this read.
- Read only. Do not connect a wallet, sign, trade, register a watchlist, change
  settings, or persist corrections to production records.
- Originate exchange and explorer requests from the user's local machine/network,
  never the VTX VPS, a production container, an SSH tunnel through the VPS, or an
  Insights server-side refresh. Confirm execution location and proxy routing
  before the first request. Browser/search tools may use hosted egress: do not
  assume they use the user's IP. If local execution is unavailable, report that
  limitation rather than moving requests to the VPS.
- Use bounded reads, cache the acquired evidence, and stop on throttling. No
  background polling or automatic retry loop. Check current request contracts
  in the [official API documentation](https://hyperliquid.gitbook.io/hyperliquid-docs/for-developers/api/info-endpoint).

## Retained readiness and historical recovery

For an Insights ROI comparison, reuse `comparison.read view=roi_readiness` for
the exact selection and interval before fetching local evidence. If not yet
available, request that view through `analysis.start`. Preserve every profile,
anchor timestamp/offset, wallet match, ledger/fill/funding status and reason,
required/covered bounds, and uncovered prefix/suffix durations. Unknown durations
are unavailable, not zero. Readiness does not refresh exchange data or compute
returns; complete coverage alone does not validate transfer classification.

A historical event fetched after the analytical cutoff is later-acquired
recovery, not evidence retained at that cutoff. Record acquisition time and
coverage, and present any independently recomputed return separately from
Insights' original unavailable result. Do not invent an Insights upload/import
capability or write local recovery into production. Recover the full interval
needed by the retained equity anchors, including their disclosed offsets; a
small visible tail cannot certify the earlier gap.

## Local ledger read

POST `https://api.hyperliquid.xyz/info` from the local process with
`{"type":"userNonFundingLedgerUpdates","user":"<wallet>","startTime":<start_ms>,"endTime":<end_ms>}`.
Use the actual account address, not its agent/signing wallet. Preserve successful
response bytes and acquisition time privately. This source includes incoming
transfers that may be absent from the address's authored explorer transactions.

Enumerate the interval with the documented time pagination. Continue from the
last returned timestamp inclusively, deduplicate stable event identities, and
sort ascending. Do not deduplicate ledger events by hash alone: multiple events
can share a hash. Detect a repeated full page or a saturated timestamp that makes
no progress; report incomplete coverage instead of skipping that millisecond.
Respect documented retention and page limits; reaching the tail does not recover
history already outside retention. Preserve unknown event types for inspection.
Classify ledger `delta` fields and reconcile material transfers with explorer
details when needed. Do not use the explorer list as the sole inbound ledger.

## Explorer source

- The official page is
  `https://app.hyperliquid.xyz/explorer/address/<wallet>`.
  Its address-history request is a read-only POST to
  `https://rpc.hyperliquid.xyz/explorer` with
  `{"type":"userDetails","user":"<wallet>"}`.
  This is still a public Hyperliquid endpoint, separate from `/info`.
- A request to use the official explorer permits its ordinary history lookup;
  it does not authorize background polling. If the user prohibits
  all public Hyperliquid endpoints including explorer RPC, explain the conflict
  before a network read. Never describe explorer RPC as independent of Hyperliquid.

## Bounded explorer inspection

Use an isolated browser with request interception, or inspect the page and its
exact read-only history response. Start with one wallet-history request and
reuse the response. Do not poll. Stop on throttling or an access challenge.

When inspecting the explorer, install network rules **before navigation**:
block HTTP requests to `/info`, block WebSocket connections, disable service
workers, and allow only the explorer request and necessary static page assets.
HTTP interception alone does not block WebSockets. The main application shell
can request unrelated account/market data even on its explorer page. Do not
treat an Offline banner caused by blocked background calls as proof that a
successful explorer history response failed.

The observed `userDetails` response contains `type` and `txs`. Each transaction
contains `time` (Unix milliseconds), `user`, `action`, `block`, `hash`, and
`error`. Check the live response shape; do not silently interpret a changed
schema. Inspect all returned rows in the requested interval, not just the first
visible table page. Keep the source response, acquisition time, earliest/latest
returned timestamps, and any row limit as evidence. An observed 300-row result
is not an all-history guarantee. Do not invent pagination parameters; follow
the explorer's current supported pagination only when needed and available.

For an ambiguous action, open its transaction detail through the official
explorer. Preserve the identifier in private evidence without exposing unrelated
wallet identifiers. Failed actions (`error` present and non-null) are attempts,
not completed cashflows. A transaction time alone does not establish the exact
equity snapshot at which its accounting effect appeared.

## Classify against the equity boundary

For every successful transfer, record timestamp, amount, asset, source wallet
and pool, destination wallet and pool, and evidence reference. Use exact decimal
amounts. Do not treat every token amount as USD.

For deposits, withdrawals, explorer `sendAsset`, and ledger `delta.type=send`,
normalize direction against the measured wallet. For sends, inspect sender (`user`),
`destination`, `sourceDex`, `destinationDex`, `token`,
`amount`, and `fromSubAccount`. Compare wallet addresses case-insensitively;
retain any subaccount distinction. In the observed explorer contract, `spot`
names spot, an empty DEX string names main perpetuals, and a named DEX such as
`xyz` names that perpetuals venue. Verify unfamiliar pool identifiers rather
than guessing. An empty DEX string is not a missing destination wallet.

Distinguish:

- External deposits/withdrawals: capital crossing the measured wallet boundary.
- Internal transfers: capital moving between pools in the same wallet.
- Trading P&L, fees, and funding: performance income/costs, not external capital.

Internal transfers net to zero only when **both pools are inside the equity
measure**. A transfer into main perpetuals can be a capital inflow for a
main-perps-only return even though no money entered the wallet. Get the actual
equity source contract from retained Insights evidence before adjusting ROI.
Do not add both legs of an internal transfer to external cashflow totals.

## Coverage and result

An address's transaction history can contain actions signed by that address
without establishing all transfers received from other addresses or bridge
events. Oldest returned time preceding the start proves date reach for returned
actions, not incoming-transfer completeness. No visible deposit is not proof of
zero deposits. Likewise, an empty table after blocked/failed requests is missing
data, not a zero-cashflow result.

Report the selected bot and interval, a compact transfer table, net movement
across each relevant pool boundary, and what remains unverified. Use the user's
timezone (EDT for VTX production reports). Keep wallet addresses private unless
the user asks for them; link the official explorer generically when needed.

Separate observed transfers, verified-zero coverage, and unknown coverage.
Only certify complete cashflows when the source covers incoming and outgoing
events for the exact equity boundary and full interval. If the user authorizes
a zero-external-cashflow assumption, a provisional ROI may proceed with that
assumption clearly stated; observed transfers must still be accounted for at
the correct pool boundary. Never overwrite Insights' verified ROI status with
this provisional result. Request only the remaining amounts/timestamps or
coverage confirmation that the user can supply.
