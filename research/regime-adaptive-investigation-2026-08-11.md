# Can the funnel be adapted per regime? — investigation, 2026-08-11

Branch: `regime-adaptive`. Question posed: the funnel is resoundingly
successful in CRISIS; can we build variants that work in BEAR, DECLINE, STALL
and BOOM?

**Answer: no, and the premise does not survive contact with the data.** The
regime-specific "bleeds" that motivated the question are an attribution
artifact. What the investigation did find is more useful, and it is not a
regime finding.

---

## The motivating metric is itself partly broken

Before anything else: the edge-by-regime decomposition — summed weekly
log-changes of the funnel/benchmark dollar ratio — is **contaminated by
deposit arithmetic**, and materially so. Both legs receive identical monthly
contributions while holding different amounts, so every deposit drags the
ratio toward 1 regardless of performance, and the drag lands in whichever
regime that week carries.

Calibrated with a synthetic leg given a head start and then earning EXACTLY
the EW weekly return with EXACTLY the same deposits — true performance
identical to the benchmark by construction:

| | total | CRISIS | BEAR | DECLINE | STALL | BOOM |
|---|---|---|---|---|---|---|
| funnel vs EW (the motivating table) | +6.5% | +17.2% | −6.4% | +5.9% | −10.0% | +1.9% |
| **zero-skill synthetic vs EW** | +4.1% | −4.9% | −1.5% | −3.0% | −4.7% | **+20.2%** |

Roughly **half of STALL's bleed is artifact**, and BOOM is substantially worse
than it appeared rather than better — the artifact concentrates in BOOM
because BOOM is 53% of weeks. (A first attempt at this control scaled the EW
leg by a constant, which scales its deposits too and wrongly gave the metric a
clean bill of health. The control has to hold deposits identical while letting
values differ.)

Consequence: **any regime attribution computed this way, including the one
used earlier for the parking analysis, carries this bias.** Differences
between two similar configs are less affected than levels, but the levels are
not trustworthy. Everything below that matters is instead measured on
per-signal forward excess returns against the index over identical windows,
which involve no cashflows at all and are free of this problem.

## The premise, and why it is an artifact

The motivating table was edge accrued by regime — the log-change in the
funnel/benchmark ratio summed within weeks of each label:

| regime | share of weeks | edge accrued |
|---|---|---|
| CRISIS | 15.2% | +22.8% |
| DECLINE | 9.3% | +4.8% |
| BOOM | 53.3% | +2.6% |
| BEAR | 4.1% | −6.3% |
| STALL | 18.1% | −8.9% |

That measures **when the book was marked up or down**, which is dominated by
positions bought earlier. It says almost nothing about whether signals fired
in a regime are worth acting on. Measured directly — median excess return over
the survivorship-free equal-weight index across the identical window, for
PASSED episodes stratified by the regime prevailing at entry:

| regime at entry | n | 1y | 3y | 5y |
|---|---|---|---|---|
| CRISIS | 195 | +4.3% | +5.4% | +11.0% |
| STALL | 104 | +3.4% | +3.2% | +6.3% |
| DECLINE | 95 | +1.1% | +5.6% | +2.3% |
| BOOM | 129 | +1.5% | +3.8% | −10.5% |
| BEAR | 38 | −1.1% | +0.1% | −11.8% |

**STALL — the supposed worst regime at −8.9% — is the second-best entry
regime at every horizon.** Its accrual figure is existing holdings being
marked down during choppy weeks, not bad buying. There is nothing to fix
there, and a "STALL variant" would have been solving a phantom.

## The buy rule is already regime-conditional

Signal density, PASSED episodes per 100 weeks:

| regime | weeks | signals | per 100wk | vs baseline |
|---|---|---|---|---|
| CRISIS | 160 | 195 | 121.9 | **2.43x** |
| DECLINE | 104 | 95 | 91.3 | 1.82x |
| BEAR | 42 | 38 | 90.5 | 1.80x |
| STALL | 208 | 105 | 50.5 | 1.01x |
| BOOM | 634 | 143 | 22.6 | **0.45x** |

A 5.4x density ratio between CRISIS and BOOM, achieved entirely by the
existing drawdown trigger and valuation percentile. Bolting an explicit regime
gate onto the buy rule would largely duplicate what the buy rule already does.

## Regime is mostly a proxy for depth

CRISIS entries have a median drawdown of **36.9%** against ~25% in every other
regime. Depth is itself a strong predictor, so the regime effect may be
depth wearing a costume. Testing regime *within* depth buckets (3y median
excess, PASSED):

| bucket | CRISIS | BEAR | DECLINE | STALL | BOOM |
|---|---|---|---|---|---|
| 20-30% | −11.1% (65) | +0.1% (25) | −1.7% (46) | −1.0% (69) | +3.8% (76) |
| 30-45% | +8.7% (62) | −1.4% (10) | +43.7% (30) | +16.4% (24) | +17.2% (33) |
| 45%+ | +14.6% (68) | — | −6.7% (16) | — | −10.1% (11) |

Inconsistent in sign and ordering; CRISIS is the *worst* regime in two of the
three buckets. Depth-match the entries and the regime advantage largely
disappears.

## What depth actually does, and what the gates do

Depth replicates across sub-periods at 1y (1998-2008: −1.5 / +4.3 / +9.5;
2009-2019: −0.7 / +3.3 / +14.1 across the three buckets). The cohort controls
separate the two effects cleanly:

- **Depth buys a bounce, regardless of quality.** At 1y, gate-VETOED deep
  names return +12.3% and gate-PASSED +12.4% — indistinguishable.
- **The gates make it durable.** At 3y, PASSED holds +13.8%/+7.6% while
  VETOED flattens to ~−1% and NAIVE dip-buying goes to −4.4%/−10.0%.

That is the clearest validation of the quality gates the campaign has
produced, and it is a *horizon* story: without gates you get a one-year
rebound and give it back.

## A correction I had to make mid-investigation

I initially concluded that the 20-30% drawdown band — 52% of all entries —
"contributes roughly nothing", from its ~zero medians. **That was wrong.** The
distributions are violently right-skewed and a portfolio compounds means:

| bucket | 3y median | 3y mean | 5y median | 5y mean | share of 3y contribution |
|---|---|---|---|---|---|
| 20-25% | +2.5% | **+6.7%** | −5.5% | **+7.8%** | 20% |
| 25-30% | −4.5% | **+2.7%** | −19.3% | **+13.2%** | 4% |
| 30-40% | +12.2% | +15.8% | +1.0% | +17.0% | 30% |
| 40-55% | +6.0% | +15.7% | +25.6% | +38.3% | 25% |
| 55%+ | +11.5% | +27.9% | +9.0% | +36.7% | 22% |

Every bucket contributes positively on a mean basis. The shallow band supplies
~24% of total mean-weighted excess at 3y. There is nothing to cut.

**Methodological consequence for the whole campaign:** our cohort statistics
are medians (the "passed +3.9% / vetoed −10.9% / naive −23.9%" ground truth).
That is the right statistic for *does the gate discriminate* and the wrong one
for *what should we own*. Any future decision about including or excluding a
class of entries must be made on means or on realised portfolio outcomes.

## Every knob turns out to be the same knob

Raising the drawdown trigger (parked, $200 lot, cap 12):

| dd trigger | 2000-19 vs EW | avg invested | 2010-19 vs EW | avg invested |
|---|---|---|---|---|
| 20% | **+18.0%** | 80.9% | +4.9% | 58.5% |
| 25% | +17.9% | 76.2% | +5.8% | 55.1% |
| 30% | +14.0% | 71.1% | +6.3% | 39.1% |
| 35% | +13.1% | 63.2% | **+6.7%** | 22.8% |

Monotonically worse in the crisis-rich window, monotonically better in the
crisis-poor one — because it is an exposure dial. That is now **three
different knobs** (lot size, monthly spend cap, entry depth) all resolving to
the same underlying mechanism: how much of the book sits in funnel picks
versus the parked index, and that trade has opposite signs by regime
(corr(exposure, edge) = +0.90 crisis-rich, −0.73 crisis-poor).

## The decisive test: mean excess by entry regime

Having learned that medians mislead here, the same question asked with means —
the statistic a portfolio compounds:

| regime at entry | 1y mean | 3y mean | 5y mean | share of 3y contribution |
|---|---|---|---|---|
| CRISIS | +10.8% | +12.1% | +24.7% | 38% |
| DECLINE | +4.5% | **+17.0%** | +26.7% | 25% |
| STALL | +4.2% | +11.2% | +26.7% | 18% |
| BEAR | +2.9% | +10.7% | +10.6% | 7% |
| BOOM | +1.6% | +6.4% | −1.3% | 12% |

**Every regime is positive at 1y and 3y.** BEAR, which on medians looked like
the one genuine defect (−1.1 / +0.1 / −11.8), has means of +2.9 / +10.7 /
+10.6. That reading is retracted too — third time tonight the median told the
opposite story from the mean.

Blocking any regime from buying (3y, mean-weighted):

| blocked | signals lost | excess lost | verdict |
|---|---|---|---|
| CRISIS | 36% | +38% | HURTS |
| BEAR | 7% | +7% | HURTS |
| DECLINE | 17% | +25% | HURTS |
| STALL | 18% | +18% | HURTS |
| BOOM | 22% | +12% | HURTS |

Unanimous. There is no regime whose entries should be refused.

**Robustness caveat.** Splitting by entry era, only CRISIS is stable:

| regime | 1y mean 98-08 / 09-19 | 3y mean 98-08 / 09-19 | n |
|---|---|---|---|
| CRISIS | +10.7% / +11.1% | +13.2% / +10.4% | 116 / 79 |
| DECLINE | +5.5% / +3.4% | +9.4% / +27.5% | 53 / 42 |
| STALL | +1.9% / +7.7% | +6.7% / +19.1% | 62 / 42 |
| BOOM | −0.2% / +3.9% | +2.0% / +13.2% | 73 / 56 |
| BEAR | +5.6% / −1.3% | −6.1% / +36.5% | 23 / 15 |

CRISIS holds its mean across both eras at both horizons. The other four swing
substantially and BEAR (n=23/15) swings wildest. So "all five regimes are
positive" is true pooled, but only the CRISIS estimate is reliably
established — which cuts against, not for, conditioning behaviour on the
other four.

**And the deepest reframe: CRISIS is not the best regime per signal.** At 3y a
DECLINE entry averages +17.0% against CRISIS's +12.1%. Crisis dominance is a
VOLUME effect — 2.43x signal density, 36% of all entries — amplified by the
exposure effect, not a signal-quality effect. "The edge is crisis-concentrated"
remains true of the portfolio and is false of the individual signal.

## Conclusions

1. **Do not build regime-specific strategy variants.** The regime-specific
   defects they would address do not exist; the buy rule is already
   implicitly regime-weighted 5.4x; and regime adds little once depth is
   controlled for.
2. **There is exactly one real regime-dependent quantity: exposure.** Every
   knob we have tried is a re-parameterisation of it. The parameter-free ways
   to manage it (parking, and the binary CRISIS exemption) are already built
   and measured.
3. **No regime should be blocked from buying.** Every regime's entries have a
   positive mean at 1y and 3y, and blocking any of them loses more excess than
   it saves in signals.
4. **Crisis dominance is volume, not per-signal quality.** DECLINE entries
   average MORE than CRISIS entries at 3y (+17.0% vs +12.1%). CRISIS matters
   because it produces 2.43x the signals and the book is more deployed then.
5. **The gates are validated more strongly than before**, and specifically as
   a *durability* mechanism: depth gives everyone a 1-year bounce, gates
   decide who keeps it at 3 years.
6. **Medians misled three separate times tonight** — on the STALL bleed, on
   the shallow-drawdown band, and on BEAR. Each time the mean reversed the
   conclusion. On distributions this skewed, any include/exclude decision must
   be made on means.

## Independent design review

A six-lens agent workflow (STALL bleed, BEAR vs CRISIS, exposure dial, entry
bar, sell side, literature survey) was run in parallel with the above, with
instructions that "the premise is an artifact, do nothing" was a fully
successful answer. **All six returned do-nothing, and all six introduced zero
new parameters.** They found several things this investigation missed:

- **The regime table's signs flip across configurations, not just windows.**
  Recomputed over all committed curves files: STALL is negative in 20/28
  configs for 2000-2019 but positive in 15/16 for 1998-2019; BOOM positive
  13/28 vs 1/16. Switching one config's lot from quarter-month to full-month
  turns STALL from −10.7% to +1.4%. The table is a function of the exposure
  dial, which is the lot-size inversion re-expressed in regime coordinates.
- **BEAR is not "an orderly decline with calm credit" — it is the shoulder of
  a panic.** Of 42 BEAR weeks in 2000-2019, only 7 have drawdown ≥ 20%; the
  other 35 are Sahm-rule BEARs. All 42 sit at stress bucket 2, median HY OAS
  7.22% with a maximum of 7.99% against the CRISIS cut of 8.00%. Median
  distance to the nearest CRISIS week is 5 weeks. The "market is still
  discriminating in BEAR" theory has no instance to attach to.
- **Throttling BEAR/DECLINE would throttle the on-ramp to the only regime that
  pays**: 76% of BEAR weeks and 69% of DECLINE weeks fall within the 26 weeks
  preceding a CRISIS entry.
- **The CRISIS label has almost no surface going forward**: 78 weeks in
  2000-02 and 62 in 2008-09, but only 7 in 2020 and zero in 2022. A mechanism
  whose entire authority lives in a 7-week window is not a mechanism — a
  serious practical objection to the crisis exemption.
- The deposit-contamination diagnosis above came from this review and was then
  verified independently here.

## The one real defect found — and why it does not pay to fix

The design review's headline claim was that under scarcity the funnel funds
names **alphabetically**, because the sweep runs `ORDER BY ticker`
(scripts/backtest_universe.py:781) and `portfolio()` serves signals in that
order until the reserve empties. Verified independently here, and the
mechanism is exactly as described:

- Mean weekly reserve: 2007 $108,538 → 2008 $57,102 → **2009 $1,405**, with
  **32 of 52 weeks in 2009 under $250** and a minimum of $0. The funnel spent
  everything in 2008 and was broke through the actual bottom.
- 2009: **65 names qualified, 32 were funded.** Mean alphabetical rank of the
  funded subset 0.368 against a 0.500 baseline, permutation **p = 0.0001**
  (20k draws). No other year shows it (2008 p=0.09, 2011 p=0.66, 2012 p=0.33).
- The unfunded half was the **deeper** half: median entry drawdown 0.489
  unfunded vs 0.328 funded.
- Present in the live path too: `autotrade.run_paper_routine` iterates
  `for r in buys:` in board order and drops the remainder into `no_cash`.

So the allocation rule in the highest-value weeks of the sample was an
accident of SQL. **But fixing it does not pay.** Implemented `--ration-order`
(ticker | deep | cheap | random) with a permutation null:

| order | final | vs EW | vs null |
|---|---|---|---|
| ticker (status quo) | $1,657,079 | +6.1% | +0.76 sd |
| deep (largest drawdown first) | $1,648,229 | +5.5% | **−3.22 sd** |
| cheap (lowest own percentile first) | $1,657,062 | +6.1% | +0.75 sd |
| 6 random nulls | — | +5.7% to +6.1% | mean +5.9%, sd 0.1% |

Total spread across every ordering: **0.6pp over 22 years**, and deepest-first
is the worst. The binding constraint is total capital, not its allocation —
reordering within a week creates no money, it only changes which names receive
the same dollars, and on average they are interchangeable enough that it
washes out. The review's own caveat proves to be the whole story: the 2009
unfunded cohort looked better because it was bottom-dated (Feb-Mar 2009), not
because it was deeper, and no ordering rule can buy anything with an empty
reserve.

Recommendation: the arbitrariness is worth removing on principle (an
unjustified rule should not be load-bearing), but `deep` is not the
replacement and no ordering change should be expected to earn anything. The
real lesson is the reserve profile — **the strategy ran out of money at the
bottom of the GFC**, which is a funding-plan finding, not an allocation one.

## Bounding the "save powder for the crash" idea — the thread is closed

The reserve profile above (broke at the GFC bottom) looked like the one live
lead. Bounded it with the most aggressive conservation a real-time signal can
express: `--buy-only-crisis` deploys **only** in weeks the regime reads CRISIS
and parks everything else. If that loses, no gentler rule can win.

| window | baseline vs EW | CRISIS-only vs EW | delta | avg invested |
|---|---|---|---|---|
| 1998-2019 | +6.1% | **−9.1%** | **−15.2pp** | 80% → 53% |
| 2000-2019 | +18.0% | **+9.7%** | **−8.3pp** | 81% → 63% |
| 2010-2019 | +4.9% | +11.4% | +6.5pp | 59% → **17%** |

**It loses badly in exactly the windows it was designed to help.** Skipping the
other 85% of weeks forfeits more than the extra powder is worth — which
follows directly from every regime having positive mean excess. And the
2010-2019 "win" is not one: at 17% average invested the book is 83% SPY, and
measured against SPY it is +1.3%. That is the exposure effect again, not
conservation working.

**Conclusion: running out of money at the bottom of the GFC is not a fixable
defect.** Deploying steadily through the 2008 decline was correct; hoarding
costs more than the bottom is worth, and the gradient runs the wrong way, so
no intermediate version rescues it. The only lever that escapes the fixed-pot
trade-off is adding OUTSIDE capital during a crisis, which is a personal
liquidity question rather than a strategy parameter.

## Not done / open

- The design workflow's proposals are recorded separately; nothing from it has
  been implemented.
- No live config change. No adoption of anything in this document.
- Everything here is training-side (1998-2019) per the holdout discipline.
- Episodes cluster in a handful of crises, so effective sample size is far
  below the raw n on every crisis-conditioned number here.
