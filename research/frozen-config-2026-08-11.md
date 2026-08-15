# FROZEN CONFIGURATION — 2026-08-11

**This file is written BEFORE the reserve run, and that ordering is the point.**
Per `reports/holdout-discipline-2026-08-11.md` rule 2, the reserve period is
evaluated **once, against a frozen configuration, at the end of a development
cycle.** If the configuration is only recorded afterwards there is no way to
distinguish what was frozen from what was chosen having seen the result. Nothing
below may change on the basis of any number measured after 2019-12-31.

## The configuration

```bash
python scripts/backtest_universe.py --sp500 --trim-only \
  --buy-pctl 0.05 --sell-pctl 0.97 \
  --inject 20000 --park --cadence monthly --lot-frac 0.01 --cap 12 \
  --mega-fscore 5 \
  --start 2020-01-01 --end <today>
```

`--fscore` is deliberately absent: the harness default `QUALITY_F = 7` is the
global bar. No other flag is set — in particular **not** `--crisis-exempt`,
`--buy-only-crisis`, `--ration-order`, `--idio-threshold`, `--drop-frac`,
`--mega-buy-pctl`, `--mega-skip-trap`, `--park-mega`, `--month-cap`,
`--open-with`, `--recycle`, `--live-trim`, `--no-fb`, `--dd-trigger`.

## Provenance of every parameter

| parameter | value | chosen on | corrected-score status |
|---|---|---|---|
| `--buy-pctl` | 0.05 | **class logic, not numbers.** Entry depth is a capacity dial; the grid maximum was at b20 and was declined | n/a — never derived from a grid |
| `--sell-pctl` | 0.97 | column dominance 5/5 + saturation shape + interior-vs-boundary | **re-measured 2026-08-11**, holds 5/5; also re-measured in this funding regime, wins by 0.64pp |
| global F bar | 7 | monotone against loosening in 2 of 3 windows under both rationing orders | **re-measured**, 0 sign flips; dissent **resolved** as capital scale |
| `--mega-fscore` | 5 | prior domain argument + the Citi trace + a control ruling out generic loosening | **re-measured**, +0.4 / +2.7 / +5.8, same sign |
| `--cadence` | monthly | 8/8 positive out of sample vs annual's 3/8 | class-level; not numerical |
| `--lot-frac` | 0.01 | operational grounds — "most granularity without dilution, inside a region where returns do not discriminate — **not** because it wins" | class-level; not numerical |
| `--cap` | 12 | "cap above ~12 is inert" | class-level |
| `--park` | on | removes ~78% of the lot-size gradient, which was cash drag | class-level |

The three parameters chosen **on numbers** — s97, F>=7, mega F>=5 — have all
been re-measured on scores corrected for the split double-adjustment (080396d).
The rest were chosen on class-level reasoning that the numbers never drove,
which is the campaign's own top-line finding: point optima do not transfer
(holdout Spearman rho = 0.24), class rules do.

## Pre-flight checks, all passed

- **Data coverage over the reserve window.** S&P membership snapshots to
  2026-08-09 (102 since 2020); ART filings to 2026-08-07 (148,434 since 2020);
  prices to 2026-08-07 (11,478,143 rows across 11,578 tickers since 2020).
- **The configuration never touches `regime.py`.** `crisis_weeks` stays `None`
  unless `--crisis-exempt` or `--buy-only-crisis` is passed, and neither is.
  The 122-week HY OAS blackout (2021-04-09 -> 2023-08-04) sits inside the
  reserve window but **cannot affect this run**.
- **`--open-with` is inert at its default.** The committed 2000-2019 baseline
  reproduces byte-identically after the change.

## SUCCESS, DEFINED BEFORE THE RUN

Primary metric: **edge vs the cadence-matched, survivorship-free EW benchmark**
— same cashflows, same cadence, delisted names included.

| outcome | criterion |
|---|---|
| **Success** | edge **>= 0** and both mechanism checks hold |
| **Strong** | edge **>= +5%** with the edge visibly concentrated in 2020 entries |
| **Consistent, underpowered** | edge in **[-5%, 0)** and both mechanism checks hold |
| **FALSIFIED** | edge **< -5%**, *or* either mechanism check fails at any edge |

### Second criterion: the head-to-head against the incumbent

Added before the run, at Adam's proposal, and the more decision-relevant of the
two: **does the frozen configuration beat the thing the campaign set out to
improve on, over the same window?**

The comparator is **live as it stood on the morning of 2026-08-11**, before any
of the day's changes, expressed in harness flags:

```bash
python scripts/backtest_universe.py --sp500 --trim-only \
  --buy-pctl 0.05 --sell-pctl 0.95 --fscore 4 \
  --idio-threshold 0.10 --idio-basis cap \
  --inject 20000 --park --cadence monthly --lot-frac 0.01 --cap 12 \
  --start 2020-01-01 --end <today>
```

`--fscore 4` because live's only Piotroski veto was the `F <= 3` trap flag, so
its effective bar was 4. `--sell-pctl 0.95` because 0.97 shipped that same
morning (c3cfdf4). The idiosyncratic filter on, because it vetoed on the board
until that evening (a37cb67). No `--mega-fscore`, because live had no mega-cap
concept at all.

**THIS IS "LIVE-SHAPED", NOT LIVE, AND THE DISTINCTION MATTERS.** Live cannot be
run in the harness: it screens a curated 508-name watchlist rather than the S&P
500 point-in-time, enters on *any* of P/E, EV/EBIT or P/FCF rather than EV/EBIT
alone, builds bands from ~10-15 annual points rather than ~80 quarterly ones,
carries `rejected` memory the harness lacks, has a Stage-2 the harness cannot
represent, and uses a fixed $1,000 lot at cap 3 without parking.

The comparison is still valid, because **those divergences are common-mode** —
both legs run on the same universe, the same bands, the same absent Stage-2.
What differs between the two legs is exactly the set of choices this campaign
made. Neither leg is live; the *difference* is the campaign.

**PRE-COMMITTED, AND WORTH SAYING BEFORE WE SEE IT: the head-to-head does not
change which configuration is adopted.** If live-shaped wins, we do not switch
to it — selecting a configuration on reserve data is precisely what this
discipline forbids. What the comparison buys is a verdict on the campaign, not
a config choice.

It also does not replace the absolute criterion. A purely relative bar could
bless a configuration that beat the incumbent while losing money, so
`edge vs EW >= 0` stands on its own.

### Why the falsification line is -5%

Anchored, not picked. In the original holdout's crisis-free test window, across
all 24 configurations, **the worst edge any of them posted was -4.3%** — the
training winner failing to transfer, the single worst transfer in the campaign.
A result below -5% therefore means this configuration did worse than the worst
of 24 managed, in a window containing a crisis the other one lacked.

A tighter line was considered and rejected. The reference for *this class* is
better than the all-configs number: **monthly-funded TRIM configs went 8 of 8
positive, +1.6% to +11.7%**, in that same crisis-free window, so a negative
edge here would already be surprising. The line stays at -5% because **the
COVID crash was five weeks and V-shaped**, and the ladder fills one rung per
week at successively lower closes — a five-week crash caps the harvest at
roughly five rungs *by construction*, however good the signals were. The window
may under-deliver for reasons of crash SHAPE rather than strategy failure, and
the line has to leave room for that.

### Mechanism checks — these outrank the number

Either failing is falsification even if the return looks good, because a good
return with the mechanism dead means we were paid for something other than the
thesis.

1. **The harvester harvests.** Fills **per week** during Feb-Jun 2020 must
   exceed fills per week in the calm stretches. Measured per week, not in
   total, so a short crisis is not penalised for being short. If the funnel
   does not deploy into the one crisis in the window, the thesis is refuted
   regardless of terminal value.
2. **Selling does not turn procyclical.** No TRIM cluster at the March 2020
   bottom or early in the recovery. Every sell is listed with its date and the
   name's subsequent 1-year return.

### Explicitly NOT the criterion: SPY

2020-2026 was an extreme mega-cap-led regime, and
`reports/megacap-decision-2026-08-11.md` established that the funnel
structurally cannot own that factor — a company large enough to move a
cap-weighted index almost never trades at the 5th percentile of its own EV/EBIT
history. That record also decomposed the gap: **selection contributes +8.8%,
positive in 8 of 8 sub-periods; the factor leg is the variable one.** Scoring
against SPY here measures the factor, not the strategy. **Report it, never
score on it.**

### Excluded from scoring

Anything the negative-EBIT TRIM fallback does in 2020-21. It was designed
around GameStop's squeeze and validated on 2020-21 airline and cruise data —
in-sample by the holdout doc's own admission.

### Pre-committed response to a bad result

Record it in this file and stop. **No parameter changes, no re-run.** If the run
falsifies the configuration, the finding is that the mechanical layer does not
survive out of sample, which is worth more than a tuned number. Re-running
after seeing the result converts the reserve into training data permanently.

## What the result will NOT mean

Agreed in advance, so a good or bad number is read correctly.

1. **The negative-EBIT TRIM fallback is in-sample.** It was designed around
   GameStop's 2021 squeeze and validated on 2020-21 airline and cruise data —
   all inside the reserve. The holdout doc says so outright. Whatever it does
   in 2020-21 is not evidence.
2. **There is no Stage-2 in the simulation, and 2023-2026 is BOOM.** The
   synthesis holds that Stage-2 non-degradation is "the whole game in calm
   markets", so the calm two-thirds of this window measures a layer the harness
   cannot represent, and will read near parity by construction.
3. **It validates the harness's strategy, not the live system.** Live's
   `valuation_percentile = 0.05` means "cheapest reading ever" against ~10-15
   annual band points, where the harness's 0.05 means the cheapest few of ~80
   quarterly markers. Live's lot is fixed at `tranche_usd = 1000` rather than
   scaling with contributions, live's `LADDER_CAP = 3` against the harness's
   12, and live does not park idle cash. A good reserve result is evidence for
   this configuration, not for what is currently running.
4. **One path.** Plus every standing limit in
   `reports/backtest-synthesis-2026-08-10.md`: full-liquidation TRIM, no market
   impact, alphabetical rationing under constraint, delisted names frozen at
   last trade, S&P 500 PIT universe only.

## The rule that makes this worth anything

> **A disappointing reserve result IS the finding. Re-tuning and re-running
> converts the reserve into training data, permanently and irreversibly.**

If the run disappoints, the response is to record it here, not to adjust a
parameter and re-run. The forward live record remains the only genuinely
uncontaminated test.

## Status

**RUN 2026-08-11.** Everything above was written and committed (a0c0a3b) before
any reserve-window result existed. Everything below is the outcome.

---

# THE RESULT

Two legs, 2020-01-06 -> 2026-08-10, 345 weeks, $133,333 contributed:

    reports/backtest-universe-sp500-trimonly-b5s97-dca20k-park-mo-lf1-cap12-mf5-2020-2026-2026-08-11.md
    reports/backtest-universe-sp500-trimonly-b5s95-f4-idio10cap-dca20k-park-mo-lf1-cap12-2020-2026-2026-08-11.md

## Primary criterion: SUCCESS (+3.73%)

| | final | edge |
|---|---|---|
| Frozen configuration | $219,952 | — |
| EW market, survivorship-free | $212,048 | **+3.73%** |
| SPY — reported, never scored | $240,243 | -8.45% |

Clears the pre-registered bar of `>= 0`. The mechanical layer beat a
survivorship-free equal-weight market on identical cashflows, in a regime the
strategy openly predicts it will not shine in.

## Mechanism check 1: PASS, by a hair

| period | weeks | fills | per week |
|---|---|---|---|
| crisis window, as pre-registered (19 Feb - 30 Jun 2020) | 19 | 26 | **1.37** |
| everything else | 326 | 403 | 1.24 |
| March 2020 alone | 4.4 | 17 | **3.87** |

1.11x as written. **The pre-registered window was badly chosen** — running to
30 June averaged the crash together with the recovery that followed it. March
alone ran 3.1x the calm rate, which is the truer reading. The same measurement
over the GFC in a development run gives 7.5x. The mechanism fired; there was
very little for it to do.

## Mechanism check 2: PASS

Three sells in six and a half years, **none in 2020**. ILMN 2022-08-15 (-22.3%
over the next year, a good trim), CBRE 2024-07-29 (+32.6%, a year early), IPGP
2026-02-23 (delisted, n/a). Nothing fired at the March bottom or into the
recovery. On three events this is an anecdote, not a rate — and it means s97
was barely exercised.

## Head-to-head: LOST, -6.44pp

| leg | final | fills | avg invested | starved | vs EW |
|---|---|---|---|---|---|
| Frozen | $219,952 | 429 | 51.6% | 16 in 3 weeks | +3.73% |
| **Incumbent** | **$235,104** | 654 | 80.3% | 212 in 17 weeks | **+10.87%** |

## TWO EXPLANATIONS OFFERED, BOTH REFUTED

**1. "It is the exposure dial." REFUTED by direct measurement.** The claim was
that the strict bar simply deployed less capital. But SPY beat both legs, and
the frozen leg held **48% of its book parked in SPY** against the incumbent's
20% — more of the winning asset — and still finished lower. The gap cannot be
exposure; the frozen leg's selected names underperformed. This was asserted
before it was checked, and checking it reversed the answer.

**2. "It is the capacity regime." REFUTED as a cross-start predictor.** The
frozen leg was capital-ABUNDANT (16 starved signals in 3 weeks, half the book
idle), which is the regime where the `--open-with` experiment measured F>=7
going negative and loosening worth +9.26pp. Observed here: +6.44pp. The
magnitudes matched, so a prediction was registered — that the head-to-head gap
would track idle capital across entry dates, with a 2022 start best for the
frozen leg and a 2023 start worst.

**It failed.** Robustness check, reading rule fixed in advance (report all
starts, no cherry-picking, configuration unchanged regardless, and these are
NESTED views of one price path rather than four independent tests):

**UNITS, corrected 2026-08-11:** the head-to-head column is a relative
terminal-value shortfall, NOT a percentage-point difference of edges. -6.44
means 219952/235104-1; the edge difference is 3.73-10.87 = **-7.14pp**. Same for
-8.65 (edge diff -9.4pp) and -3.43 (edge diff -3.6pp).

| start | frozen | vs EW | fills | inv% | incumbent | vs EW | fills | terminal Δ | edge gap |
|---|---|---|---|---|---|---|---|---|---|
| 2020 | $219,952 | +3.7% | 429 | 51.6% | $235,104 | +10.9% | 654 | **-6.44%** | **-7.14pp** |
| 2021 | $164,542 | -1.1% | 400 | 66.3% | $180,120 | +8.3% | 570 | **-8.65%** | **-9.40pp** |
| 2022 | $134,055 | +1.4% | 339 | 75.4% | $138,814 | +5.0% | 475 | **-3.43%** | **-3.60pp** |
| 2023 | $104,324 | +2.2% | 243 | 56.6% | $104,236 | +2.1% | 372 | **+0.08%** | **+0.10pp** |

*Two distinct quantities, previously conflated under one "pp" heading:
**terminal Δ** is the relative shortfall in final value (219952/235104-1);
**edge gap** is the difference of the two vs-EW edges (3.73-10.87). Corrected
2026-08-11 — see reports/pre-integration-audit-2026-08-11.md.*

2023 was the frozen leg's BEST window and 2021 its worst — the opposite of the
prediction — and the gap does not track `inv%` at all. The capacity account
survives as a description of the F-bar's within-window behaviour, which was
measured directly, but it does NOT explain the head-to-head.

## What the four windows do show

- **The frozen configuration sits at roughly EW parity everywhere**: +3.7,
  -1.1, +1.4, +2.2. Exactly what the strategy predicts of itself in a
  dislocation-poor era. It never blows up.
- **The incumbent wins or ties in all four**, with its edge decaying
  monotonically as the window shortens: +10.9 -> +8.3 -> +5.0 -> +2.1.
- **By the 2023 start the two are indistinguishable** (+0.08pp). ~~All of the
  incumbent's advantage came from 2020-2022.~~ **REFUTED 2026-08-11 by a
  per-year decomposition of the same two report files: the frozen leg was AHEAD
  by $615 through January 2025, and the entire gap opened in calendar 2025
  (+$9,223) and Jan-Aug 2026 (+$6,544). The 2021-start gap ($15,578) EXCEEDS the
  2020-start gap ($15,152), so COVID contributed nothing or favoured the frozen
  leg. See reports/pre-integration-audit-2026-08-11.md.**
- **The incumbent takes 40-50% more fills in every window** (654/429, 570/400,
  475/339, 372/243). That ratio is the only thing stable across all four.

## A third explanation, POST-HOC AND UNTESTED

2020-21 was an indiscriminate recovery: nearly everything beaten down bounced
regardless of quality, so a quality screen was a pure cost. Marked clearly as
what it is — generated after the pre-registered explanation failed. It has a
precedent that cuts against it: the same junk-rally hypothesis was raised for
the 1998-99 window, TESTED, and rejected, because the extra fills turned out
not to be junk. Nobody has tested it here.

## What does NOT follow, and why the pre-commitment holds

The head-to-head is a true fact about two configurations on one nested price
path. It is **not** evidence that F>=4-plus-idio is a better design:

- **F>=4 was never designed.** It is a side effect of the `F <= 3` trap-flag
  threshold — what remains when no quality bar exists at all.
- **The incumbent carries the idiosyncratic filter**, measured twice as worse
  than deleting the same number of signals at random (-3.4pp, -5.3pp against a
  matched null, outside the seed ranges). It won WHILE carrying a component we
  have measured as harmful.
- **It has never been validated on anything.** It is not a rival hypothesis
  with evidence behind it; it is an accumulation that happened to be looser.
- **It is not even live** — no Stage-2, no curated watchlist, no `rejected`
  memory.

**THE CONFIGURATION DOES NOT CHANGE.** Switching to the incumbent on the
strength of this would be selecting a configuration on reserve data, which is
the single thing this discipline exists to prevent. That was pre-committed
above, before the numbers existed, precisely because it would be tempting
afterwards. It is tempting. It still does not happen.

---

# THE FINAL PRODUCT — and what each part's evidence actually is

Development closes here: **the frozen configuration above, plus a parked
reserve held 2/3 in growth mega-cap and 1/3 in the broad index.**

```bash
python scripts/backtest_universe.py --sp500 --trim-only \
  --buy-pctl 0.05 --sell-pctl 0.97 \
  --inject 20000 --park --cadence monthly --lot-frac 0.01 --cap 12 \
  --mega-fscore 5 \
  --park-mega 0.666667 --mega-index megacap-index-rev.json
```

Live implements the index third with **VOO**; SPY appears here only because it
is the one ETF in the Sharadar store.

## THE TWO HALVES HAVE DIFFERENT EVIDENTIAL STATUS, AND THIS MUST NOT BLUR

| component | development windows | 2020-2026 reserve |
|---|---|---|
| b5 / s97 / F>=7 / mega F>=5 | yes | **YES — tested once, as frozen** |
| the 2/3 growth reserve | yes (1998/2000/2010 -> 2019) | **NO. Never. Cannot be.** |

**The reserve run tested the configuration with a 100% SPY reserve.** The
growth-reserve work came afterwards, on development windows only, and the
holdout is spent — so this component has no out-of-sample validation and never
will. Anyone reading "tested" against the final product should read it as
"supported on 1998-2019 development windows" for the reserve half, and
"pre-registered and evaluated once out of sample" for the configuration half.

Those are not the same claim and the difference is the whole point of the
discipline.

## What the reserve half rests on

Positive in all three development windows at every fraction tested, monotone,
and surviving the 1998 cell at 100% that killed the blend version. Against
that: it is **linear in the growth share with no interior optimum**, so 2/3 is
a dial setting rather than a derived value; it measures a **factor premium over
that factor's best era** on a momentum-adjacent screen; and it costs **67 extra
starved signals** over 22 years — the reserve is worth less exactly when the
funnel spends hardest.

**The 2/3 setting is Adam's call on that dial.** 50% and 75% are equally
supported by this evidence, which has no opinion between them.

Full detail, including the cash sleeve built and rejected:
`reports/megacap-growth-reserve-2026-08-11.md`.

## Genuinely unresolved, and now on the record

1. **Why does the looser bar win 2020-2022 and tie thereafter?** Untested.
2. **F>=7's value is regime-conditional in a way we can describe but not
   predict.** It won two of three development windows and lost every reserve
   window it was compared in. The `--open-with` mechanism explains the
   within-window behaviour and failed to predict across entry dates.
3. **The reserve is spent.** No re-tuning, no re-running. The forward live
   record is now the only uncontaminated test remaining.
