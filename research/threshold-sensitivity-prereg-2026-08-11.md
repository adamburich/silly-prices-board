# Threshold sensitivity check — pre-registration

Written **before** the runs, 2026-08-11. This is a fragility test, not a
threshold hunt. See reports/holdout-discipline-2026-08-11.md.

## Question

Is the live operating point (b5 / s95) sitting on a **plateau** — a region
where nearby thresholds work about as well — or on a **peak**, where the
strategy depends on having picked one specific number?

This is NOT "which threshold is best." That question is unanswerable: the
holdout returned Spearman ρ=0.24 overall and **−0.14 within the monthly-TRIM
class**, i.e. training rank carried no information about test rank, and three
windows crowned three different winners.

## Design

- Window: **1998-01-01 → 2019-12-31** (training side of the holdout cut).
- Grid: buy percentile ∈ {0.03, 0.05, 0.10, 0.15, 0.20} × sell percentile ∈
  {0.95, 0.97, 0.99} = 15 cells. b3 is included specifically to probe
  immediately *below* the operating point. s85/s90 stay retired (rarity is
  already an established class finding; the sell-side cliff is known to sit
  below 0.95).

  **Amendment, same day, before any result was observed:** s97 added at
  Adam's request. It fills the gap between s95 and s99 and tests his standing
  hypothesis that rarity is *not* a smooth dial — that s99 should behave more
  like never-selling than like s95. If the sell axis is monotonic and gentle
  across 95→97→99, rarity is a plateau; if s99 diverges toward the hold-class
  result, the sell threshold is a real choice rather than an arbitrary one
  inside a safe range. No results had been read when this amendment was made;
  the s95 column was still running.
- Held constant: monthly $20k/yr, full-month lot, cap 3, TRIM-only sells,
  S&P 500 point-in-time.
- **Unparked**, deliberately. Parking adds a market-beta component that would
  compress the differences between cells and bias the result toward "flat" —
  the conclusion I am predisposed to. Unparked is the most sensitive read and
  therefore the most likely to expose a cliff.
- Metric: final funnel ÷ final cadence-matched EW − 1.

## Decision rule (binding)

The only permitted outcomes are **STAY** or **DISTRUST**. Relocating to the
best-performing cell is forbidden regardless of what the grid shows — that is
precisely the move the holdout invalidated.

- **PLATEAU → STAY.** If ≥12 of 15 cells are positive vs matched EW and
  b5/s95 falls inside the central range of the cell distribution, keep
  b5/s95 unchanged. A flat region means the choice does not drive the result,
  so there is no reason to move and moving would be noise-chasing.
- **CLIFF → DISTRUST.** If fewer than 9 of 15 cells are positive, or if
  b5/s95's immediate neighbours (b3/s95, b10/s95, b5/s97) diverge sharply
  from it, the design depends on a number we picked — that is evidence
  against the strategy, to be recorded as such, not an invitation to move.
- **SUSPICIOUS.** If b5/s95 is the maximum cell by a wide margin, treat that
  as a warning that the earlier grid work leaked into the choice, not as
  vindication.

## What this cannot establish

That b5/s95 is optimal. Nothing can, with this data. The strongest available
claim is that the neighbourhood is viable — which the holdout already
supports independently: all 8 monthly-TRIM configurations (b5-b20 × s95/s99)
finished positive out of sample, while every hold-class config sank.
