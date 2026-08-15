# Normalized EBIT, Phase 1: STOP — and two defects found on the way

*2026-08-11. Branch `normalized-ebit` (forked from `mechanized`). Development
windows only. Nothing here touches the 2020-present holdout.*

## Verdict: the Phase-1 stop condition fired

Four independent solvency preconditions were designed (credit-analyst,
reuse-existing-gates, earnings-quality, minimal-parameters lenses), each
validated against the database, each attacked by a separate adversary hunting
verified counterexamples. **All four self-reported `separates = false`. All four
were refuted FATAL with disjoint counterexample sets.**

The controlling case, computed directly rather than inherited:

**Eastman Kodak (EKDKQ), 2005-11-14.** S&P 500 member that week. Price $22.25,
36.0% off its 52-week high, market cap $6,390M. Newest visible ART row filed
2005-11-09 with TTM EBIT **-$556M**, so the buy path skips it today
(`backtest_universe.py:478`). **`piotroski_f` = 7 and `trap_flags` = [] — it
clears the entire existing gate stack.** All four proposed preconditions admit
it on correct point-in-time inputs:

| lens | reading at 2005-11-14 | verdict |
|---|---|---|
| credit | net debt $1,066M / norm EBITDA $1,895M = 0.56x vs a 3.0x bar | ADMIT |
| reuse | Z_norm 2.39 vs TRAP_Z_MIN 1.8 | ADMIT |
| quality | TTM CFO +$642M, capex -$509M, FCF +$133M | ADMIT |
| minimal | retained earnings +$6,516M | ADMIT |

Forward total return **+23.1% at 1y, -60.7% at 3y, -77.5% at 5y.** Chapter 11
January 2012, equity cancelled, last trade $0.03.

Four of four, at the one point in this population where protection is actually
needed, five years ahead. No conjunction of the four repairs it, because none
of the four rejects.

## Why every design failed, structurally

The credit lens produced the generalizable finding. Of twelve admitted
zeros it traced, **six had LOW OR NEGATIVE net debt**:

- Bethlehem Steel — 1.96x, killed by a ~$6B pension deficit
- Winn-Dixie — 0.69x, killed by pre-ASC-842 operating leases
- W.R. Grace — 0.11x, killed by asbestos tort liability
- Kodak and Silicon Graphics — both at **net cash**

**The liability that kills a trough cyclical is usually not in debt or cash.**
It is in pensions, leases, tort, and obsolescence. No bar on debt and cash can
reach it, which is why every variant of "measure the balance sheet" failed the
same way.

The reuse lens produced the second-most useful result: `Z_norm` and `Z_raw`
agree on **90.0%** of scoreable weeks, and **84.4%** of its de-duplicated admits
are already admitted by a bare `altman_z(recs[-1], mc_now) >= 1.8` — existing
function, existing constant, zero new code. Normalization *degrades* it. Its
motivating diagnosis was also false: the funnel's `altman_z` reads the latest
ANNUAL EBIT, not the trough TTM EBIT that triggers the skip, so the existing leg
was never "charged with the trough" as assumed.

## My Phase-1 test set was invalid, and this is the methodological lesson

The eight-name label set in the plan was scored on **company outcomes** rather
than **trade outcomes**. Three of the four authors independently caught it:

| name | labelled | actual, from the labelled date |
|---|---|---|
| Q1 (Qwest) | "reject — permanent impairment" | **+257% 3y, +648% 5y**, acquired |
| VIAV (JDSU) | "reject — dot-com carcass" | **+85.6% 1y, +342% to date** |
| AMCC | "reject — never recovered" | **+69.9% 1y**, acquired 2017 |
| CHKAQ | reject | correct — the only real capital destruction |

**Only Chesapeake destroyed capital on a relevant horizon.** A rule "correctly"
rejecting Qwest in 2002 was refusing a triple. Any future Phase 1 must score on
trade outcomes or it will keep counting avoided winners as successes.

Eight names cannot validate a rule; they can only refute one. They refuted two.

## What the measurement showed before the designs ran

| | weeks | names |
|---|---|---|
| blind spot (EBIT<=0, 20%+ off high) | 14,412 | 273 |
| scoreable on normalized EBIT | 12,690 (88%) | 258 |
| ...own bottom 5% on normalized EBIT | 2,659 | **137** |
| ...own bottom 5% on EV/S (comparison) | 3,905 | 160 |
| cheap on both | 2,371 | 127 |

The normalized denominator drops VIAV, AMCC and Q1 **by construction** — the
band only takes a marker when the trailing median margin is positive, and they
never had one. CHKAQ survives at 50 weeks. Newly surfaced: NYT(56) and TGNA(46),
newspapers in 2008-09 — the archetypal secular decline, and one recovered while
the other did not.

## The trailing median is load-bearing for the WRONG reason

The plan and the first code comment both justified the per-marker trailing
median as point-in-time hygiene. That is wrong, and the error hides the failure
it claims to prevent.

**A constant median cancels algebraically.** `EV / (m x rev)` with the same `m`
in every marker is `EV / rev` times a constant, and a percentile is invariant to
a positive constant. Measured: rescaling every marker with the current median
reproduces the EV/S percentile on **54,758 of 54,758** scoreable weeks (100.00%).
A full-sample median would not leak the future — it would silently turn
normalized EV/EBIT into EV/Sales exactly.

The binding constraint is **non-degeneracy**, not PIT hygiene. The per-marker
median is the only thing making this a different measurement from `band_s`.

The leak that does NOT cancel is asymmetric: markers accumulated correctly while
the CURRENT reading is normalized from the whole margin history. That admits
5,879 weeks against 4,507 for the clean build. Invariant, now stated in the
code: the current reading must call `_median` on the same running accumulator,
never re-derive from `art`, `ary`, or a fresh load.

The accumulator itself was attacked by two independent adversaries and survived
both, verified by execution rather than reading — a truncation-invariance run
(grid ending 2010-12-31 vs 2019-12-31) produced byte-identical `no_mult` rows
for every week <= 2010 across 14 names, 1,453 rows, 0 mismatches. That test
fails on the full-sample-median variant, the asymmetric-median variant, and the
ARY-filtered-on-fye variant, and is worth committing as a regression test.

---

# TWO DEFECTS FOUND ON THE WAY, BOTH INDEPENDENT OF THIS BRANCH

## 1. LIVE: `normalized_target` pre-empts the GameStop fallback

The sell-side fix shipped for the GME hole is unreachable for a large class of
the names it was built for. Verified by reading the chain:

```
valuation.py:187      target_buy() returns None when latest_ebit <= 0
dashboard.py:303-307  ...so control falls through to normalized_target()
valuation.py:265-266  normalized_target builds margins from POSITIVE-EBIT records
                      only, and never requires current EBIT > 0 — mm * cur_rev
                      is perfectly well-defined at a loss
dashboard.py:326      row.sell_above = tb.sell_price        <- now populated
dashboard.py:390      if row.sell_above is None: ...        <- False
                      sell_line_no_ebit() IS NEVER CALLED
```

A company with a profitable history and currently negative EBIT — **GameStop's
exact profile** — receives a normalized sell line instead of the negative-EBIT
fallback. Reported measurement on the live store: GME at 2020-12-01 gets a
normalized sell line of **$7.36** where `sell_line_no_ebit` would have returned
**$86.88**.

Why this matters beyond the number: the fallback carries the two parameter-free
ratchets (record market cap AND price at an all-time high) that exist precisely
to stop it firing at a dislocation low. The normalized line has no such
protection. **Live can therefore emit a TRIM on a beaten-down negative-EBIT name
at the bottom** — the procyclical behaviour the ratchets were built to prevent.

The correct invariant is not "don't touch `cur_mult`" but **"no lane may
populate a sell line for a name whose trailing EBIT is <= 0 before the
negative-EBIT lane is consulted."**

NOT FIXED HERE — it is a live change and belongs on its own branch with its own
review.

## 2. BACKTEST: `_adj_shares` double-applies split factors on Sharadar data

`metrics._adj_shares` multiplies diluted shares by `split_factor`, documented on
the assumption that it receives EDGAR's as-originally-reported counts. True for
live. **False for the backtest**, which feeds it `sharadar.year_records`.

Sharadar's counts are already fully split-adjusted, verified directly:

```
AAPL FY2013 ARY diluted shares = 26,087M
     Apple reported ~929M;  929M x 7 (2014 split) x 4 (2020 split) = 26,012M
NVDA FY2013 ARY diluted shares = 24,998M
     NVDA reported ~625M;   625M x 4 x 10 = 25,000M
```

The factors do not cancel in the dilution ratio, because `split_factor` is
cumulative-since-that-fiscal-year: the older endpoint gets a larger factor than
the newer one, so `sh[-1]/sh[0]` is scaled by `F_now/F_then`. Forward splits
suppress real dilution; **reverse splits manufacture fake dilution**.

Measured over S&P 500 members with splits and 5 years of history at 2019-12-31 —
442 names, **18 (4%) have the net-dilution trap flag flipped**:

```
FALSE FLAG (reverse splits — distressed names):
   SVU    reads +622%  truth  +3%      JAVA1  reads +255%  truth -11%
   DYNIQ  reads +306%  truth -19%
MISSED (forward splits):
   BALL   reads  -38%  truth +24%      ISRG   reads  -65%  truth  +5%
   MNST   reads  -64%  truth  +8%      CE1    reads  -56%  truth +33%
```

**The false flags land on distressed reverse-split names — the funnel's own
target population.** The net-dilution flag was called "the signature of a rescue
financing" in the mega-cap work; on Sharadar data it is partly measuring split
history instead.

Bounded (4% of split-having names at one date) but real, in a load-bearing gate
present in every backtest run this campaign. NOT FIXED HERE — fixing it changes
every historical result and that is the user's call.

## Status

- **No precondition implemented. No buy-path change. Nothing fires.**
- `margins` / `band_n` accumulator KEPT as diagnostic, with its rationale
  corrected and the margin append moved out of the EV branch (an earnings-power
  series must not be conditioned on price data).
- `no_mult` now records the normalized percentile and median margin alongside
  the EV/S reading.
- If the lane is ever built, the surviving specification is the **null
  precondition**: set `pctl = npct` and fall through to the existing bar and
  gates entirely unchanged — no normalized Altman leg, no F>=7 relaxation, no
  cyclical flag, each of which was measured by its own adversary to cost more
  than it bought. It still needs Phase 2 and Phase 3-with-the-random-add-null,
  and it has a known live loss: **Kodak clears the existing gates too.**

"No precondition" is not "no risk". It is a recommendation that the risk be
priced by measurement rather than by a rule fitted to eight names — three of
whose labels were wrong.
