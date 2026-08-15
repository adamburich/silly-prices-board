# Ladder cap sweep, fixed lots — 2026-08-09

Companion to the [hybrid sweep](backtest-two-tranche-2026-08-09.md), which tied lot size to the cap and so conflated purchase count with equity fraction. Here every lot is the same fixed size (1 unit ≈ the live $1k paper lot) and the cap only truncates the only-lower fill sequence — so every cap is a prefix of the same fills, evaluated on identical episodes.

**Matched lump** = the same number of dollars the ladder actually deployed, all bought at tranche 1. Beating it means the *schedule* added value for those dollars; this control neutralises the mechanical cost-basis flattery of any average-down rule (Hayley). Returns are fixed-horizon (3y from T1) on deployed capital — never IRR, per METHODOLOGY.

## Dot-com bust — 752 episodes

| Cap | Avg lots filled | Return on deployed (median) | p10 | $P&L per episode (median) | p10 | Beats matched lump |
|---|---|---|---|---|---|---|
| 1 | 1.00 | +21.7% | -46.2% | +0.22 | -0.46 | — |
| 2 | 1.85 | +25.7% | -43.9% | +0.45 | -0.85 | 85% |
| 3 | 2.47 | +29.3% | -41.4% | +0.58 | -1.14 | 85% |
| 4 | 2.98 | +31.0% | -38.6% | +0.66 | -1.35 | 85% |
| 6 | 3.72 | +32.9% | -35.5% | +0.79 | -1.60 | 85% |
| uncapped | 4.92 | +37.6% | -28.7% | +1.00 | -1.36 | 85% |

### Marginal value of the k-th lot

Each row: only episodes where a k-th fill happened. Depth is the fill price vs tranche 1; return is that single lot to horizon.

| Lot # | Episodes reaching it | Median fill week | Median depth vs T1 | Median lot return | Mean lot return | Lots ending negative |
|---|---|---|---|---|---|---|
| 1 | 752 (100%) | 0 | +0% | +21.7% | +35.8% | 31% |
| 2 | 639 (85%) | 1 | -5% | +25.1% | +39.1% | 31% |
| 3 | 470 (62%) | 5 | -10% | +23.0% | +38.5% | 33% |
| 4 | 381 (51%) | 10 | -15% | +22.9% | +34.4% | 34% |
| 5 | 310 (41%) | 14 | -20% | +26.3% | +38.2% | 32% |
| 6 | 244 (32%) | 16 | -26% | +29.5% | +36.6% | 34% |
| 7 | 202 (27%) | 20 | -31% | +28.4% | +40.9% | 35% |
| 8 | 168 (22%) | 21 | -38% | +25.9% | +44.1% | 36% |

## Global financial crisis — 745 episodes

| Cap | Avg lots filled | Return on deployed (median) | p10 | $P&L per episode (median) | p10 | Beats matched lump |
|---|---|---|---|---|---|---|
| 1 | 1.00 | +23.9% | -25.7% | +0.24 | -0.26 | — |
| 2 | 1.86 | +26.2% | -23.9% | +0.44 | -0.48 | 86% |
| 3 | 2.61 | +28.4% | -22.1% | +0.59 | -0.65 | 86% |
| 4 | 3.23 | +29.5% | -20.0% | +0.75 | -0.79 | 86% |
| 6 | 4.22 | +33.4% | -15.5% | +0.99 | -0.91 | 86% |
| uncapped | 6.02 | +40.5% | -3.7% | +1.44 | -0.25 | 86% |

### Marginal value of the k-th lot

Each row: only episodes where a k-th fill happened. Depth is the fill price vs tranche 1; return is that single lot to horizon.

| Lot # | Episodes reaching it | Median fill week | Median depth vs T1 | Median lot return | Mean lot return | Lots ending negative |
|---|---|---|---|---|---|---|
| 1 | 745 (100%) | 0 | +0% | +23.9% | +45.2% | 26% |
| 2 | 639 (86%) | 2 | -4% | +25.1% | +49.2% | 25% |
| 3 | 562 (75%) | 4 | -8% | +28.4% | +58.8% | 24% |
| 4 | 464 (62%) | 7 | -14% | +29.9% | +66.6% | 25% |
| 5 | 402 (54%) | 9 | -19% | +29.8% | +72.4% | 25% |
| 6 | 330 (44%) | 13 | -23% | +31.4% | +76.9% | 23% |
| 7 | 267 (36%) | 16 | -27% | +30.9% | +85.9% | 23% |
| 8 | 216 (29%) | 19 | -31% | +29.7% | +76.0% | 24% |

## Tested regime (control) — 1533 episodes

| Cap | Avg lots filled | Return on deployed (median) | p10 | $P&L per episode (median) | p10 | Beats matched lump |
|---|---|---|---|---|---|---|
| 1 | 1.00 | +25.5% | -18.1% | +0.26 | -0.18 | — |
| 2 | 1.83 | +27.5% | -16.9% | +0.47 | -0.33 | 83% |
| 3 | 2.53 | +29.2% | -15.0% | +0.61 | -0.42 | 83% |
| 4 | 3.12 | +30.5% | -13.5% | +0.76 | -0.48 | 83% |
| 6 | 4.00 | +32.7% | -11.4% | +0.98 | -0.52 | 83% |
| uncapped | 5.25 | +35.6% | -6.5% | +1.23 | -0.30 | 83% |

### Marginal value of the k-th lot

Each row: only episodes where a k-th fill happened. Depth is the fill price vs tranche 1; return is that single lot to horizon.

| Lot # | Episodes reaching it | Median fill week | Median depth vs T1 | Median lot return | Mean lot return | Lots ending negative |
|---|---|---|---|---|---|---|
| 1 | 1533 (100%) | 0 | +0% | +25.5% | +47.9% | 23% |
| 2 | 1276 (83%) | 1 | -3% | +24.9% | +47.8% | 23% |
| 3 | 1074 (70%) | 5 | -6% | +26.4% | +50.1% | 22% |
| 4 | 898 (59%) | 8 | -9% | +27.3% | +51.5% | 21% |
| 5 | 747 (49%) | 13 | -13% | +27.7% | +49.2% | 21% |
| 6 | 607 (40%) | 16 | -16% | +28.0% | +51.4% | 20% |
| 7 | 493 (32%) | 19 | -19% | +29.1% | +54.6% | 19% |
| 8 | 377 (25%) | 22 | -22% | +29.9% | +56.7% | 19% |

## Reading the result

**1. Cap 3 is not a performance optimum — the hybrid sweep's "cap 3 wins" was the
lot-size effect, not the count effect.** With lot size held fixed, both median
return on deployed capital and median dollar P&L rise monotonically with the cap
in every window. Nothing in per-lot quality argues for stopping at 3.

**2. The per-lot decay predicted by the adverse-selection argument does not appear
in this sample.** Lots 4–8 fill at −15% to −38% below tranche 1 and return as
well per dollar as lots 1–3 (GFC medians: lot 1 +23.9%, lot 6 +31.4%; share
ending negative flat at ~23–26% across all k). Conditional on a k-th lower close
occurring, buying it was fine — in a universe of eventual survivors.

**3. That qualifier is the whole argument, because the one number favouring
"uncapped" is the one number survivorship manufactures.** Uncapped's paradoxical
p10 dollar improvement (GFC: −0.25 vs cap 6's −0.91) comes from the deepest fills
in surviving catastrophes rescuing the episode — BAC bought at −80% and V-ing
back. Every name in this universe that fell 90% recovered *by construction of
today's index membership*. Lehman, WaMu, Enron and WorldCom — the names where
only-lower fires all the way to zero — are exactly the names not in the data, and
they would have been uncapped's largest positions. Within-sample, averaging down
to the bottom looks retroactively brilliant; that is the classic averaging-down
illusion, and the dot-com window (where the V-effect is weaker) already shows the
pattern breaking: uncapped p10 −1.36 is no better than cap 4's −1.35.

**4. What the cap actually controls is dollar exposure to the tail you cannot
measure.** The p10 dollar loss grows near-linearly with the cap (dot-com: −0.46 →
−1.60 lots from cap 1 to cap 6), and the fill-count distribution is long-tailed:
median 4–5 fills, but **p99 is 18–20 and the maximum 22–24**. Uncapped at $1k
lots means the worst grinds take $18–24k in a single name — with the size ranking
across names inverted, largest where the decline was longest.

**5. One designed metric failed: the matched-lump column is tautological.** An
only-lower ladder's every fill after the first is below tranche 1 by
construction, so it beats same-dollars-at-T1 whenever ≥2 lots fill. The constant
85/86/83% across caps is just the share of episodes with a second fill. It still
confirms the schedule dominates its own matched lump, but it cannot discriminate
between caps.

### Where this leaves the cap

The cap is a **risk-budget dial, not a return parameter**. In-sample, anything
from 3 to 6 is defensible — higher caps buy more median P&L at a near-linear cost
in measurable tail and full exposure to the unmeasurable one. Uncapped is not
justifiable on this evidence: its case rests entirely on the survivorship-poisoned
number. The principled sizing is top-down: cap × lot size = the maximum you are
willing to lose in one name when the diagnosis is wrong, and the Stage-4
position-size cap (~5% at cost) should bind before the ladder does. The number
that would genuinely settle cap 3 vs cap 6 is the zero-tail frequency — which is
the delisted-names data the vendor purchase provides.

## Honest limits

- Same harness limits as the stress test: price-criterion trigger only, SPY benchmark, weekly closes, survivorship with the near-death survivors captured.
- 'Still a buy' is proxied by the Stage-1 price criterion; a live ladder also halts on trap markers or a verdict flip, which none of these numbers credit.
- Return on deployed capital is cost-weighted; the matched-lump column is the control for that. Dollar P&L columns are in lot units (multiply by the live lot size).
- Ladder eligibility runs 52 weeks from T1; outcomes measured 3 years from T1.