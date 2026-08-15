# Holdout discipline — adopted 2026-08-11

**Develop on 1998-01-01 → 2019-12-31. Reserve 2020-01-01 → present.**

## What this is, and what it isn't

It is not a pristine statistical holdout. Every window in the Sharadar record
has already been run — the lump-sum tests, the negative-EBIT fallback
acceptance runs, the original train/test split (which already spent 2013-2026
as its test window), and the threshold grids. Nothing in 1998-2026 is
untouched.

It is **this version's holdout**: a discipline binding from today forward. Its
value is prospective, not retrospective. Work done before 2026-08-11 is
in-sample by default, including the negative-EBIT TRIM fallback, which was
explicitly designed around GameStop's 2021 squeeze and validated on 2020-21
airline and cruise-line data — all inside the reserve. That rule is in-sample
under any cut we could have chosen; this frame makes the status explicit
rather than implicit.

## Why 2020, and not the more recent cut

A holdout without a crisis has no power for this strategy, and that is
measured, not assumed. The original holdout's test window (2013-2026)
contained no genuine crisis and returned near-parity for *every* configuration
(mean edge ≈ −1%) — it could not distinguish a good configuration from a bad
one, because the entire edge mechanism is crisis-harvesting and there was
nothing to harvest.

By the regime classifier (sillyprices/regime.py), 2022-2026 contains **zero
CRISIS episodes**, and 2023 onward is BOOM. A 2022 cut would therefore buy a
reserve that can only answer "does it avoid hurting itself in calm markets."

> **CORRECTION, same day.** The original text here justified that with "HY OAS
> peaked near 6%" in 2022. **The committed artifact cannot support that
> sentence and I should not have written it.** `reports/regime-history.csv`
> has no HY OAS value for 122 contiguous weeks, **2021-04-09 → 2023-08-04** —
> the seed archive (data/regime/hy_oas.csv) ends 2021-03-19 and the FRED tail
> only resumes 2023-08-11, because FRED truncated the series to a rolling
> three-year window in April 2026. **Every 2022 label was produced VIX-only.**
> No substitute source exists: the GitHub archive's last commit for that file
> is 2021-03-23, and every Wayback capture of the FRED CSV post-dates the
> truncation.
>
> The conclusion very likely still holds — HY OAS did peak around 6% in 2022,
> which lands in stress bucket 2 ("high"), below the bucket-3 CRISIS cut of
> 8% — so 2022 would not label CRISIS even with the credit leg lit. But that
> is external knowledge, not a measurement, and the distinction matters
> because the reserve run inherits these labels. `regime --history --csv` now
> emits a `stress_basis` column marking each row `vix+credit` or `VIX-ONLY`
> so the degradation is self-declaring rather than hidden behind a blank cell.

2020-2026 answers considerably more:

| condition | what it can falsify |
|---|---|
| COVID crash + V-recovery (2020) | the edge mechanism fires at all; sell rules do not turn procyclical in a recovery |
| meme mania (2021) | the negative-EBIT fallback's target behaviour |
| 2022 bear — long, shallow, no credit crisis | a different stress shape than a panic |
| 2023-2026 boom | non-degradation when nothing qualifies |

Training retains 22 years and five crises (LTCM 1998, dot-com 2000-03,
GFC 2008-09, euro 2011, credit/energy 2016). Split ≈ 77/23. The boundary sits
immediately before the COVID crash rather than splitting the episode.

## Operating rules

1. No configuration choice — threshold, lot size, cap, cadence, parking, sell
   rule, gate — may be informed by any result measured after 2019-12-31.
2. Reserve-period runs happen **once**, against a frozen configuration, at the
   end of a development cycle.
3. A disappointing reserve result **is the finding**. Re-tuning and re-running
   converts the reserve into training data, permanently and irreversibly.
4. Any backtest run whose window crosses the cut must say so in its filename
   and must not be cited in a configuration argument.

## The real holdout

Forward time. Every reserve carved out of 1998-2026 is a degraded proxy for
the only genuinely uncontaminated test: a frozen configuration tracked live
across a full cycle containing at least one crisis. Hold both — the backtest
reserve for development discipline, the live record as the actual verdict.

See also: reports/backtest-synthesis-2026-08-10.md (the ρ=0.24 finding that
motivates all of this).
