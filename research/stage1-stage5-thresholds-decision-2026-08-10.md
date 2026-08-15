# Decision: buy percentile 0.20 → 0.05, TRIM sell line 0.85 → 0.95

*2026-08-10. Decided by Adam after the Sharadar backtest campaign; applied to
config.toml ([screen].valuation_percentile, [valuation].buy_percentile,
[valuation].sell_percentile) and METHODOLOGY.md Stage 1.*

## Evidence

16-cell sensitivity grid (`scripts/backtest_universe.py --sp500`, point-in-time
S&P 500 members 1998–2015, $100k in 100 × $1k ladder lots, TRIM-only sells =
band percentile + optimistic owner-earnings DCF, both legs required):

| buy pctl \ sell | TRIM s85 | TRIM s90 | TRIM s95 | hold |
|---|---|---|---|---|
| bottom 5% | $646.2k | $678.2k | **$740.4k** | $621.0k |
| bottom 10% | $564.8k | $589.6k | $659.9k | $617.1k |
| bottom 15% | $551.1k | $573.9k | $650.8k | $604.1k |
| bottom 20% | $565.6k | $576.6k | $671.6k | $578.8k |

Benchmarks, same window: survivorship-free equal-weight members $395.4k,
BRK.B $428.0k, SPY $289.1k.

- **b5 wins its column at every sell setting** (4/4) — the deep-entry
  advantage is robust across the sell dimension, not a one-cell fluke.
- **s95 wins its row at every buy setting** (4/4) — rare TRIM beats both
  tighter TRIM and never-selling everywhere.
- Every cell beats every benchmark; parameters modulate +10.0%…+11.8% CAGR.

Supporting sell-side attribution (same harness): mechanical thesis-break
(trap-flag) sells destroy value in every combination tested ($373–434k) —
they fire on recession filings and sell the 2001/2009 lows. They remain
HUMAN-ONLY. TRIM at 0.85 was nearly free (−2% vs hold, 48 sells/18y);
at 0.95 it becomes additive (24 sells/18y scale).

## What changes operationally

- The zone and the board's buy targets demand the **cheapest ~5%** of a
  name's own decade, not the cheapest ~20%. BUY chips, zone entries, and
  paper-routine ladder fills will be **much rarer** — the backtest cut
  fills from ~370 to ~260 and concentrated them in fewer names. Quiet
  boards are the design working, not a data problem.
- The Stage-5 history-rich review leg fires on the richest ~5% instead of
  ~15%; TRIM flags (already rare) get rarer. TRIM remains a review flag +
  human decision — no auto-sells were added anywhere.
- Drawdown trigger (20%), quality gates, trap disqualifiers, ladder
  mechanics (weekly / only-lower / cap 3): all unchanged.

## Caveats accepted with the decision

- b5 is the deepest row tested (no b2.5/b7.5 neighbors); single 18-year
  window; portfolio gains are allocation-driven (concentration + TRIM
  recycling), not better per-episode picks; sequential testing preceded
  the grid. The grid's cross-dimension dominance is the counterweight.
- Rollback is one knob per line in config.toml; the prior values (0.20 /
  0.85) ran the live system 2026-07-24 → 2026-08-10.
