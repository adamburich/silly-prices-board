# Sell threshold 0.95 → 0.97 — decision record, 2026-08-11

Evidence: reports/backtest-threshold-surface-2026-08-11.html (chart),
scripts/chart_threshold_surface.py, and the 15 sensitivity runs
`backtest-universe-sp500-trimonly-b*s*-dca20k-mo-lm100-1998-2019-*`.
Pre-registration: reports/threshold-sensitivity-prereg-2026-08-11.md.
Holdout frame: reports/holdout-discipline-2026-08-11.md.

## What was run

15 cells — buy ∈ {3,5,10,15,20} × sell ∈ {95,97,99} — over **1998-2019**
(training side of the holdout cut), monthly $20k/yr, full-month lots, cap 3,
TRIM-only, unparked, S&P 500 point-in-time. Only the two percentiles varied.
Metric: finish vs a benchmark receiving identical monthly cashflows.

**All 15 cells finished positive** (+9.0% to +29.1%, mean +20.2%, sd 6.0%).
No cliff anywhere: the strategy does not depend on having guessed a threshold.
That was the pre-registered question and it is answered.

## Why this is not a move to the winning cell

The pre-registration forbade relocating to the best cell, and that rule was
honoured. The best cell was **b20/s99 at +29.1% — declined.** The buy axis is
unchanged at b5.

What changed is based on a **column** effect, not a cell:

| sell | mean vs benchmark | trims (22y) | beats s95 at |
|---|---|---|---|
| s95 | +14.0% | 29-37 | — |
| s97 | +23.6% | 26-34 | 5 of 5 buy depths |
| s99 | +23.2% | 19-25 | 5 of 5 buy depths |

Three properties make this a calibration rather than a discovery:

1. **Column dominance, not a peak.** s97 beats s95 at every buy depth, by
   7-14 points each time. **(Re-measured later the same day — the dominance
   holds 5/5, but "7-14 points" is specific to THIS funding regime; in the
   configuration actually adopted it is 0.6 points. See the two appended
   sections.)**
2. **A saturation curve, not a maximum.** 95→97 is worth ~9 points; 97→99 is
   worth nothing. Saturation is mechanistically interpretable; peaks are not.
3. **It agrees with a prior we already hold.** "Mechanical TRIM must be rare —
   frequent selling clips compounders" is an established class finding from the
   whole campaign. This grid does not propose a new rule; it says **0.95 was
   not rare enough to satisfy the rule we had already adopted.**

## Why 0.97 and not 0.99

s97 and s99 are indistinguishable on this evidence: means +23.6% vs +23.2%,
splitting 3-2 across the five buy depths. Choosing between them on this data
would be reading noise.

The tiebreaker is external and structural: **s97 is interior, s99 is on a
boundary.** s99's upper neighbour is the hold regime (never sell), which the
earlier holdout showed is genuinely bad — every hold-class config sank while
all 8 monthly-TRIM configs stayed positive. s97's neighbours are validated good
on both sides, which also makes it **interpolation between two
out-of-sample-validated points rather than extrapolation** (the 8/8 result
covered s95 and s99).

## The buy axis: deliberately unchanged

The surface tilts toward shallower entry (b20 +22.0% vs b3 +9.0% at s95), but
fill counts tilt with it — 375 at b3, 443 at b5, 536 at b20. Shallower entry is
buying **more deployment, not better selection**. Entry depth is a capacity
dial (an established class finding), so the correct response is to fix capacity
with lot size, cap and parking — not to relax the entry bar. b5 stays.

Second reason: **b20/s99 topping a training window is the exact pattern that
already failed.** It led the earlier full-record ranking and then landed
mid-pack out of sample, while b10/s99 — seventh of eight in training — won the
test window. Within-class rank correlation was −0.14.

## Status and the test

s97 has **never been run on any window touching 2020-2026**. Adam's point, and
it is the right one: that blindness makes the reserve run a genuine test on
this axis instead of a re-confirmation of something already peeked at. Narrow
but real — clean on the *sell-threshold* dimension only; the negative-EBIT
fallback, the gates and the buy depth remain in-sample.

Known limits: one training window, no out-of-sample check, and the whole grid
sits on the training side of the cut. This establishes the neighbourhood is
safe; it does **not** establish that the gradient is real.

## Rejected options, recorded so they are not re-litigated

- **b20/s99 (+29.1%, the maximum).** Declined — pre-registration forbids
  relocation to the winner, and this specific configuration has already failed
  to transfer once.
- **b20 at any sell level.** Declined — the buy gradient is deployment
  capacity, not selection quality.
- **s99.** Not wrong, and equally supported by the data; declined only on
  interior-robustness grounds.

---

# APPENDED 2026-08-11: re-measured on corrected Piotroski scores

Every cell above was computed with the split double-adjustment defect live
(commit 080396d fixed it). `no_dilution` is one of Piotroski's nine criteria, so
**every F-score in every cell of this grid was wrong**, and the gate is F>=7
throughout. All 15 cells re-run.

**The decision stands. Nothing reordered.**

| sell | mean BEFORE | mean AFTER |
|---|---|---|
| s95 | +14.0% | +18.9% |
| **s97** | **+23.6%** | **+28.3%** |
| s99 | +23.2% | +26.9% |

- **Column dominance:** s97 beats s95 at **5 of 5** buy depths, unchanged.
- **Saturation, not a peak:** 95->97 is worth +9.4pp, 97->99 is worth -1.4pp.
  Sharper than before, where 97->99 was roughly flat (-0.4pp).
- **Pre-registered question ("all cells positive, no cliff"):** still 15 of 15,
  range tightened upward to +15.4% .. +31.7% (was +9.0% .. +29.1%).
- s97 now beats s99 at 4 of 5 depths (was 3-2, called "reading noise" above).

Every cell improved — 14 up, one down 0.45pp, mean +20.2% -> +24.7%. That is a
**uniform level shift, not a reordering**, which is the good case: the defect
was a roughly constant drag rather than a distortion of the comparison. It does
mean every absolute figure quoted above understates by ~4-5pp.

The argmax moved from b20/s99 to b20/s97. Still on the buy axis that was
declined, still declined for the same reason. **That the maximum now sits at
the adopted sell level is a coincidence and must not be cited as support.**

# APPENDED 2026-08-11: the grid was run in the WRONG FUNDING REGIME

Found while rebuilding the command. This grid is `dca20k-mo-lm100` — unparked,
full-month lots, cap 3. The adopted configuration is
`dca20k-park-mo-lf1-cap12` — **parked, 1%-of-annual lots, cap 12**. The sell
threshold was chosen in a funding regime that is not the one it governs, and
parking alone removes ~78% of the lot-size gradient
(reports/lot-size-exposure-finding-2026-08-11.md), so the two regimes do not
behave alike.

Re-run at b5 in the adopted regime, 1998-2019:

| sell | funnel | edge vs EW | sells | end reserve |
|---|---|---|---|---|
| s95 | $1,638,132 | +4.84% | 69 | $599,522 |
| **s97** | **$1,648,017** | **+5.48%** | 58 | $538,423 |
| s99 | $1,605,811 | +2.78% | 38 | $393,614 |

**s97 still wins — but by 0.64pp over s95, not 9.9pp.** On one 22-year path
that is noise. The column-dominance argument was measured at roughly 15x the
effect size it has in the configuration being run. This does not overturn s97;
it confirms the campaign's own Tier-2 claim that the TRIM line inside
[0.95, 0.99] is second-order (reports/backtest-synthesis-2026-08-10.md).

**One thing genuinely new, and it favours the choice made.** s99 was declined on
purely structural grounds — interior beats boundary — because s97 and s99 were
"indistinguishable on this evidence." In the adopted regime they are no longer
indistinguishable: s99 trails by 2.70pp. The tiebreaker taken on faith now has
support.

Mechanism visible in the reserve column: more selling parks more reserve
($599k at s95 against $394k at s99), so in a parked config the sell rule is
partly an exposure dial between funnel picks and SPY — the sixth knob in this
campaign to turn out to be the exposure dial in disguise. But s99 holds the
MOST in picks and performs WORST, which cuts against a pure exposure reading
and suggests the trims themselves carry value. One path; hold that loosely.

**Not done:** the other 14 cells in the adopted regime. Only the b5 column was
re-run, because b5 is fixed and the buy axis was never in question.
