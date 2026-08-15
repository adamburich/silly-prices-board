# What the F>=7 gate is worth, and what queue order is worth

*2026-08-11. Branch `regime-adaptive`. Development windows only — 1998-2019,
2000-2019, 2010-2019. Nothing here touches the 2020-present holdout.*

## Why this was run

The mega-cap work settled on `--mega-fscore 5`: lower the Piotroski bar for the
top-50 basket, on the argument that Piotroski calibrated the score on small,
thinly-covered value names and it is out of domain on the largest companies in
the market.

That prompted an audit of how the harness compares to the live system, which
turned up a divergence nobody had noticed: **the harness enforces F>=7 as a
hard gate; live does not enforce it at all.** Live computes the score, reports
it in the "Gates failing" column, and vetoes only on the trap flag `F <= 3`
(`metrics.py:418`). Live's effective bar is 4. The harness has always modelled
a stricter rule than the thing it models.

So: what is the bar worth?

## The global bar

`--fscore N`, applied to every name.

| bar | 2010-2019 | 2000-2019 | 1998-2019 |
|---|---|---|---|
| F>=4 (live's effective bar) | **-8.7%** | **-9.5%** | +6.2% |
| F>=5 | -7.2% | -7.1% | +7.6% |
| F>=6 | -4.1% | -4.6% | +6.2% |
| F>=7 (harness default) | — | — | — |

**Two of three windows are cleanly monotone against loosening: every point off
the bar costs money.** The third disagrees, and disagrees hard.

### The outlier window is not the 1999 junk rally

The obvious explanation — 1998-2019 is the only window containing 1998-99, and
a loose bar buys beaten-down low-quality names before the largest low-quality
rally in modern market history — was tested and is **wrong**. Only 8% of the
extra fills (121 of 1,448) fall in 1998-99, and the names are Helmerich &
Payne, Loews, Equifax, Cincinnati Financial, VF, Hershey, Textron, Paccar,
Travelers. Not junk.

What the fill logs actually show is a **capital-scale** difference. Comparing
the same calendar years across the two windows:

| year | extra fills, 1998 start | extra fills, 2000 start |
|---|---|---|
| 2000 | 124 | 69 |
| 2002 | 134 | 101 |
| 2012 | 137 | 107 |

The 1998-start run buys more extra names in years *both* runs cover. Starting
two years earlier leaves the portfolio with more capital for the whole
remainder, so it can act on signals the 2000-start run must skip. The ladder is
capital-rationed — 1,750 starved signals across 45 weeks in the 1998 window —
so a looser bar is **additive** when capital is plentiful and **substitutive**
when it is scarce.

## Queue order

That mechanism predicted something testable. The rationing queue is served
`ORDER BY ticker` — alphabetical by accident of the SQL. Arbitrary with respect
to quality. At F>=7 that costs nothing because the pool is uniformly good; at a
looser bar, marginal names should displace good ones on nothing but their
initial.

`--ration-order quality` added (highest Piotroski first, deepest drawdown
breaking ties; `Signal.f` carries the score to the queue).

| | F>=7 ticker | F>=7 quality | F>=4 ticker | F>=4 quality |
|---|---|---|---|---|
| 2010-2019 | — | +0.2% | -8.7% | -8.6% |
| 2000-2019 | — | -0.5% | -9.5% | -6.0% |
| 1998-2019 | — | -0.1% | +6.2% | +8.1% |

**At F>=7 it is a no-op** (+0.2 / -0.5 / -0.1) — exactly as predicted, since
there is no spread to sort on. **At F>=4 it recovers part of the penalty**:
+4.0pp in 2000-19, +2.1pp in 1998-19, -0.2pp in 2010-19, each measured against
its own quality-order control.

So the displacement story is real but **partial**. It explains roughly a third
of the loosening penalty in the window where that penalty bites hardest, and
none of it in the calm window. It does not rescue a looser bar.

### The one clean selection measurement in the campaign

Fills and exposure barely move under the reorder: 815 -> 815, 2025 -> 2011,
2449 -> 2438; average invested 74.7% -> 75.1%, 88.7% -> 87.8%, 89.0% -> 88.6%.
**Same capital, same deployment, different names.**

Every other knob examined in this campaign moved exposure and had to be
controlled for — lot size, monthly cap, entry depth and the buy percentile all
resolve to "how much of the book sits in funnel picks vs the parked index".
This one does not. The +4.0pp in 2000-2019 is pure selection, uncontaminated.

## The global run is the control the mega-cap test never had

The most important result here is not about the global bar at all. Put the same
threshold of 5 against two different populations:

| | F>=5 for **mega-caps only** | F>=5 for **everything** |
|---|---|---|
| 2010-2019 | **+0.3%** | -7.2% |
| 2000-2019 | **+3.0%** | -7.1% |
| 1998-2019 | **+4.5%** | +7.6% |

Same threshold, same code path, same windows. Applied to 52 names it is
positive in all three; applied to the whole book it is negative in two.

**CAVEAT ADDED 2026-08-11:** no run artifact for a global `--fscore 5` exists
in `reports/` or in git history (such a run tags `-f5`), so the -8.8/-5.2/+11.1
column is unverifiable as it stands. The two arms are also not volume-matched
(global F>=5 drives starvation 715 -> 5,934 against mega-only's 715 -> 1,508),
so at best this separates "loosen for nearly everything" from "loosen for a
small subset" — not "mega-caps" from "any small subset". The size-matched null
was never run. Also: the harness applied the lower bar to 185 distinct names in
1998-2019, not 52 — membership is time-varying across 114 snapshots. See
reports/pre-integration-audit-2026-08-11.md.

**This rules out the obvious alternative explanation for the mega-cap result** —
that lowering F simply helps generically and mega-caps were incidental. If that
were true, loosening everywhere would have helped more, not hurt. It is also
precisely what the out-of-domain argument predicted *before* these runs: relax
the score where Piotroski did not calibrate it and it helps; relax it where he
did and it hurts.

That prediction was made in advance and held. It is the strongest evidence the
mega-cap rule has, and it is considerably better than the return margin that
originally motivated it.

## Interaction: does quality order undo the mega rule?

`--mega-fscore 5` deliberately puts names scoring 5 and 6 into the pool, so the
pool now *has* quality spread and the reorder is no longer a no-op. The concern
was that relaxed mega admissions would queue behind every F>=7 name and, with
~28% of signals starved, never get funded.

**They do get funded.** Mega gain, each against its own rationing-order baseline:

| | ticker order | quality order |
|---|---|---|
| 2010-2019 | +0.3% | +0.6% |
| 2000-2019 | +3.0% | +3.2% |
| 1998-2019 | +4.5% | +4.7% |

Fills identical (557/557, 2025/2025), mega zone-weeks identical (319 / 1446 /
1458). Starvation clusters in post-crash rushes when dozens of names fire at
once; a giant 20% off its high is not competing in that crowd. The two rules
are independent, not merely compatible.

## Decisions

- **F>=7 stays as the global bar.** Two of three windows monotone against
  loosening, under *both* rationing orders. The outlier window's disagreement
  is a capital-scale artifact, not evidence for a looser gate.
  **RESOLVED same day — the capital-scale claim, hedged here as "'traced' is
  generous", was confirmed by direct manipulation: giving the 2000 window the
  1998 run's opening balance flips the F-bar effect from -6.1pp to +10.3pp,
  against the 1998 window's +11.1pp. See
  reports/f-bar-capital-scale-2026-08-11.md. The F bar is a rationing device,
  and its value depends on how capital-constrained the funnel is.**
- **`--mega-fscore 5` stands**, and is better supported than before: the global
  run is a control that rules out generic loosening.
- **Quality rationing NOT adopted.** It is a no-op at the operating point
  (+0.2 / -0.5 / -0.1) and does not become meaningful under `--mega-fscore 5`
  either. The flag is kept because it is the control that makes any future
  F-bar argument interpretable; re-deriving it later costs more than carrying
  it.
- **Alphabetical rationing remains a real defect worth nothing.** Fourth
  independent key (`deep`, `cheap`, `random`, now `quality`) confirming commit
  a6ac48d.

## Harness/live divergence: still open, now with a sign

Live's effective F>=4 is the setting that loses 8.7% and 9.5% in two of three
windows. That is not proof live is wrong — live rations differently, screens a
curated watchlist rather than the S&P 500, tests three valuation metrics
instead of one, and runs on EDGAR/Yahoo rather than Sharadar — but it is the
first evidence on the divergence, and it does not favour live.

**The larger divergence found in the same audit is not the F gate.** Live
requires a name's drawdown to exceed its sector ETF's by >=10 percentage points
and rejects it as "sector-wide" otherwise (`screen.py:42`). The harness has no
sector comparison at all. That bites hardest exactly where the harness says the
money is — the edge is crisis-concentrated, and in a crisis the sector ETF is
also down 40%. **Live may be structurally screening out the entries the
backtest credits for most of the edge.** Unmeasured. It is the divergence most
likely to invalidate a conclusion currently being relied on, and it is testable:
build per-sector EW indices from the universe already swept and re-run with the
excess-drawdown requirement applied.

Smaller: live enters if **any** of P/E, EV/EBIT, P/FCF is at or below the
percentile; the harness tests EV/EBIT only (P/B for financials). Live is
materially more permissive on cheapness.

These are not one system running behind another. They are two strategies
sharing a vocabulary, and a rule-by-rule reconciliation should decide each
divergence on its merits rather than assuming the harness is truth.

## Reproduce

```bash
python scripts/backtest_universe.py --sp500 --trim-only --buy-pctl 0.05 \
  --sell-pctl 0.97 --inject 20000 --park --cadence monthly --lot-frac 0.01 \
  --cap 12 --fscore 4 --ration-order quality --start 1998-01-01 --end 2019-12-31
```

Baselines: 1998-2019 $1,657,079 / 1933 fills; 2000-2019 $1,339,515 / 1690;
2010-2019 $389,112 / 528.
