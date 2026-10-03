---
name: vtx-fleet-supervisor
description: Supervise the user's VTX bot fleet on demand or on a schedule with read-only VTX Insights evidence. Use when the user asks an agent to watch, monitor, check on, or supervise their bots, or schedules a recurring fleet check, to surface stopped or offline bots, Hyperliquid and model-provider errors, biggest winners and losers, large gains or losses, profit giveback, deep unrealized losses, stale open orders, and odd trades. Never changes settings, controls bots, or trades.
---

# VTX Fleet Supervisor

## Scope

Act as a read-only supervisor of the profiles the user owns. Report what needs
the user's attention; do not fix it. Never call a tool that changes settings,
profiles, connections, bot state, or orders, even when the connection would
allow it. If the user wants an action, name it and leave it to them or to a
separately authorized request.

Use the read-only VTX fleet monitor connection
(https://api.vtxmacro.com/insights/monitor/mcp) when it is connected; it only
ever has `insights:read`. Otherwise use the VTX Insights connection with only
`insights:read` requested. Setup and scheduling guidance is at
https://vtxmacro.com/insights. If a read fails or a profile is unavailable, say
so for that profile. Unavailable is never flat, healthy, or zero.

## Each check

Run these steps once per check. Reuse the first results instead of rediscovering.

1. **Runtime and population.** Call `control.bot.status` once with
   `{"selection":{"population":"all"}}`; its matched profiles are the complete
   owned population to supervise unless the user named a subset. Call
   `host.list` once.
   - Flag a bot whose Trader should be running (`desired_trader_running` or a
     desired running state) but is not `confirmed_live`. For Client Mode, use
     `waiting_state`, `lease_active`, and `owner_presence_fresh` to say whether
     the owner's browser or desktop app is closed or its heartbeat is stale, and
     use `host.list` to name which hosts are online.
   - Flag any `last_error`. A stopped bot the user intentionally stopped is not
     an alert.
2. **Errors.** For each owned bot that is running, should be running, or was
   flagged above, read `analysis.history` for the check window with
   `decisions=["ERROR","WARN"]`, following `next_cursor` while `has_more`. For a
   Client Mode bot with a runtime problem, also read `runtime.events.read`.
   Group repeated messages; separate exchange (Hyperliquid) errors from model
   provider errors and VTX runtime warnings.
3. **Open risk.** Call `account.snapshot` for the owned population. Report open
   positions with deep or fast-growing unrealized losses and any position that
   looks unexpected for the bot. Then read `trade.read` with
   `dataset="open_orders"` for each owned bot and report resting orders that
   look stale, orphaned (no matching position or running bot), or unexpected
   for the bot, with their age, side, size, and price.
4. **Results.** Call `analytics.query` with `dataset="performance"` for the
   window to rank the biggest winners and losers, and `position.excursions` with
   `result_view="summary"` for the same window to find profit giveback (peak
   unrealized profit versus what was kept) and the deepest adverse excursion.
   Use `analytics.query` `dataset="equity_history"` when a bot's or account's
   peak-to-now drop matters.
5. **Odd trades.** When results or excursions point at a specific bot, read its
   recent trades or `decisions.history` for that window to explain what looks
   unusual (size, direction flips, churn, entries against its own reasoning).
   Do not open full trade-chain analysis inside a scheduled check; suggest the
   `vtx-bot-trade-chain-analysis` skill instead.

## Judging what matters

Use judgment, not fixed thresholds: weigh each finding against the bot's own
recent behaviour, account size, and how fast it is changing. Apply any limits
the user set in their request exactly as given. A large loss on a tiny account,
a modest loss that is accelerating, or a winner that gave back most of its peak
can each matter more than a bigger static number.

## Window

Use the period since the previous check when the host remembers it; otherwise
use the schedule interval the user gave, or the last 24 hours. State the window
and the time of the check in the user's timezone: read it once with
`account.preferences.read`, and use UTC when it is automatic or unavailable.

## Report

- Lead with what needs attention, most urgent first: stopped or offline bots
  that should be running, active errors, deep unrealized losses, stale or
  orphaned open orders, then giveback, losers, winners, and odd trades.
- Name each profile by handle with the exact numbers behind every claim.
- When nothing needs attention, reply with exactly one all-clear line that names
  the window and the number of bots checked, plus one line per unavailable read
  if any. Do not list the checks that came back clean; scheduled runs must stay
  quiet.
- End with any profiles or reads that were unavailable.
