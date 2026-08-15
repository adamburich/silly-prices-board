# TRIM s99 grid — full record 1998–2026, S&P 500 point-in-time

*Generated 2026-08-10. 20 sweeps, $20k contributed each January, live $1k lots,
TRIM-only sells (band percentile AND optimistic owner-earnings DCF). Same
cashflows into every leg: EW members $4,106,872 · SPY $3,717,220 ·
BRK.B $3,586,572. Scenario artifacts kept in `reports/scenarios/` (gitignored).*

## Final value of $580,000 contributed

| buy pctl \ sell | s85 | s90 | s95 | **s99** | hold |
|---|---|---|---|---|---|
| bottom 5% | $3,445.7k | $3,781.9k | $4,211.4k | **$4,735.6k** | $4,614.5k |
| bottom 10% | $3,376.3k | $3,679.9k | $4,040.8k | **$4,451.1k** | $4,379.5k |
| bottom 15% | $3,612.3k | $4,179.0k | $4,617.6k | **$4,674.2k** | $4,446.9k |
| bottom 20% | $3,789.9k | $4,220.8k | $4,734.7k | **$4,872.1k** | $4,493.3k |

## Reading

1. **s99 wins all four rows.** The monotone rise s85 → s90 → s95 did not turn
   at 95; it continues to 99, which then edges *hold* by 1.2–2.9%. The peak of
   the inverted U sits at s99, and the top is broad (s99 ≈ hold) while the
   left side is a real slope (s85 is 20–27% below the peak).
2. **The live setting is on the wrong shoulder over the full record.**
   `sell_percentile = 0.95` gives up 1.2–12.5% to s99 depending on the row
   (biggest gap at b5, the live buy setting: $4.21M vs $4.74M).
3. **This contradicts the 1998–2015 grids**, where s95 beat hold in every row.
   Extending through the momentum decade reverses it. Second time the full
   record has disagreed with the crisis-era window (buy depth did the same),
   which is the strongest evidence yet that single-window threshold tuning
   does not generalise.
4. **What s99 actually means:** with 30–80 band points per name, the 99th
   percentile requires *every* prior observation to be at or below current —
   i.e. "the most expensive this company has ever been," not "top 1%". At
   N=40 even s98 is identical to s99; s97 is the first genuinely looser step.
   Early sim years (short bands) collapse all high thresholds together, so
   the s95-vs-s99 difference is almost entirely a 2010s–2020s effect.
5. **Sell counts (b5 row):** s85 111, s90 99, s95 74, s99 48, hold 0. Fewer,
   more extreme sells beat more numerous ones at every step.

## Starved-signal instrumentation (new)

Every scenario report now carries a starved-signal count — BUY-rated weeks
where the reserve held less than one lot. The b5 row at $20k/yr with $1k
lots: **4,120 starved signals across 415 weeks** (28% of all sim weeks) at
s95, rising to 4,234 at s99 (rarer TRIM → less recycled cash). This
overturns an earlier inference in this campaign that deployment throttling,
not cash starvation, was the binding constraint: the ladder is
cash-constrained more than a quarter of the time at these settings.

Note the confound it exposes: recycled proceeds were the *majority* of
deployment ($1.39M deployed vs $580k contributed in the b5/s95 run), and
recycling varies systematically across the sell axis (111 sells at s85 → 0
at hold). So configs differ not only in sell policy but in how smoothly
they are funded — an argument for moving the harness to monthly
contributions, where the calendar artifact disappears.

## Caveats

Single 28-year window; annual January funding imposes a seasonal purchasing-
power cycle that is not economically meaningful and is unevenly distributed
across the configs compared here; no Stage-2 judgment anywhere; the sim's
TRIM is a **full liquidation**, not a partial trim, so the live rule's
gentler behaviour sits somewhere between the tested s99 and hold.

## Open, not yet acted on

- Whether s99's lead survives monthly funding (portfolio-pass only).
- Where the peak really sits: s97 is the informative next point (s98 is
  arithmetically identical to s99 at typical band lengths).
- `sell_percentile` remains 0.95 in config.toml pending the monthly re-test.
