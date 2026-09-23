# Local wallet return calculations

Use the local-only network boundary in the parent skill for every call below.
Do not re-fetch valid evidence already acquired for the same wallet and period.
Start with the requested interval, not an unsolicited all-history scan.
Apply the parent skill's inclusive pagination, deduplication, progress checks,
and coverage rules to all time-ranged sources, including funding.

Before local reads, reuse the exact comparison's `roi_readiness`: compatible
start/end anchors, ledger/fills/funding coverage, wallet match, and specific
uncovered intervals. Separate cutoff-visible evidence from historical records
acquired later. A later local calculation does not change Insights' original
cutoff or verified status; no production import is part of this workflow.

## Sources

1. `userFillsByTime`: send `user`, inclusive `startTime`/`endTime`, and
   `aggregateByTime: true`. Paginate inclusively, deduplicate using stable fill
   identifiers and the full economic record, and sort by timestamp. Check the
   current documented retention ceiling: pagination cannot recover pruned fills.
   Keep main perps, named perps DEXs, and spot identifiable.
2. `userNonFundingLedgerUpdates`: use the same interval. Classify both incoming
   and outgoing transfers against the measured wallet/pool, including internal
   pool transfers. Preserve deposits, withdrawals, fees, and other adjustments
   separately. An incoming `send` is a cashflow even if its type is not `deposit`.
3. `userFunding`: fetch the same interval. Signed funding payments affect returns
   but are not in `closedPnl - fee`.
4. `clearinghouseState`: record its timestamp, DEX scope, account equity,
   unrealized PnL, withdrawable balance, notional, and margin used. Request a
   relevant named DEX explicitly when needed. A current state is context, not a
   historical end anchor. Reuse retained start/end equity snapshots for a past
   comparison and preserve their timestamp offsets and equity source contracts.
5. `portfolio`: inspect available periods and use `perpAllTime.pnlHistory` for
   the requested all-time curve when suitable. Retain `accountValueHistory` for
   capital/scope reconciliation, but do not plot its raw change as trading gains:
   deposits and transfers move it. Do not silently substitute another period.

## Calculations and reconciliation

Compute each fill's realized trading contribution as `closedPnl - fee`, including
opening fees; keep its fee currency. The documented `fee` already includes
`builderFee`, so do not subtract the builder fee again. Sum signed funding
separately. Do not sum unrelated fee currencies without a supported conversion.

For a requested realized return on contributed capital, the convention is:

```
capitalBasis = startingCapital + positiveDeposits
realizedTradingReturnPct = 100 * sum(closedPnl - fee) / capitalBasis
```

Label this a return on contributed capital; it is not a time-weighted return or
IRR. It does not time-weight deposits and does not shrink its denominator for
withdrawals. Use capital, not position margin, for these percentages. Preserve
zero/negative/unknown denominator as unavailable. For the wallet contribution
convention, `positiveDeposits` means incoming external capital; do not count
internal wallet transfers as fresh deposits or count the same capital again
when it cycles through spot and back to perps. Separately track flows across
the equity's pool boundary when reconciling marked PnL. If a pool-only capital
basis is requested, state that different convention and reconcile recycled
capital explicitly rather than silently inflating the denominator with each leg.

Prefer a directly observed starting-capital anchor. The reconstruction
`startingValue = endEquity - endUnrealized - cumulativeNetPnL - netCashFlows`
is valid only when every term covers the same account/pool and interval, and
`cumulativeNetPnL` includes all realized changes (funding and other non-fill
adjustments as applicable). Otherwise it is an estimate with an explicit
reconciliation residual. Current equity plus a historical subset of fills
cannot reconstruct historical starting capital. Starting unrealized PnL matters
for inherited positions: distinguish starting equity from starting realized
capital instead of silently treating them as identical.

For marked performance with compatible anchors:

```
markedPnL = endEquity - startEquity - netCapitalInflows
markedReturnOnContributedCapitalPct = 100 * markedPnL / capitalBasis
```

Insights `comparison.read` marked ROI uses observed starting equity plus
positive normalized deposits, with its configured denominator floor. Report that
actual denominator when comparing to Insights. A locally chosen starting realized
capital or pool-only basis is a different convention; do not silently equate it
to starting equity or compare percentages using unlike definitions. A zero-flow
assumption requires explicit user authorization and makes the result provisional.

Reconcile that result with fill PnL, fees, funding, change in unrealized PnL, and
other retained adjustments. Report a residual; do not label an unexplained
difference a deposit. Explicitly flag changed equity/pool coverage. Do not add
unrealized PnL again to a portfolio PnL series that already includes it.

For a flow-adjusted chart, subtract the starting `pnlHistory` value from later
values and divide by the chosen capital basis (or add the delta to that basis
for a synthetic dollar curve). This is not the literal account balance. Verify
series scope and reconcile sample deltas against anchors and known cashflows.
If named-DEX transfers create jumps or scope is unclear, label the curve partial
and avoid calling it flow-adjusted. Sampled portfolio points are not exact
requested-time anchors: disclose time offsets, and do not extrapolate or splice
different series silently. Keep all chart points within the declared cutoff.

## Presentation

If charts are requested, plot the cumulative realized fill contribution with
equal spacing per fill/aggregated fill (label the x-axis accordingly), and the
portfolio PnL-derived curve using actual timestamps. Show funding separately or
state explicitly when included. A one-fill interval supports only a single
realized step; do not manufacture a richer trading history.

Report the exact period, external cashflows, internal pool transfers, denominator,
realized return, marked return, source coverage and reconciliation residual. Keep
current state separate from the historical verdict. Comparisons across bots must
use the same return definition and equity boundary.
