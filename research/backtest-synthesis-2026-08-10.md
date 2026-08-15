# The backtest campaign, distilled: what we actually know

*2026-08-10. Synthesis of the full Sharadar campaign — ~400 configurations
across ten experiment batches, S&P 500 point-in-time (quarterly membership
snapshots, 15.6k delisted names in the store), as-reported fundamentals
filing-date indexed, 1998–2026. All artifacts in reports/ and
reports/scenarios/; every experiment regenerable from
scripts/backtest_universe.py flags recorded in the git log.*

---

## The holdout verdict (run last, reported first)

Design fixed before results were seen: rank 24 configurations (buy 5/10/15/20
× TRIM s95/s99/hold × annual-$1k-cap3 / monthly-full-month-cap3) on
**1998–2012 only**, then evaluate once on **2013–2026**. Metric: edge vs the
cadence-matched equal-weight benchmark within each window.

- **Spearman rank correlation between windows: ρ = 0.24.** Training rank
  barely predicts test rank.
- **The training winner (b5/s99/annual, +32.5% train edge) placed 16th of 24
  out of sample, with −4.3% test edge.** The implicit procedure of this whole
  campaign — pick the best backtest cell — fails the only honest test we ran
  of it.
- **12 of 24 configs had negative edge in 2013–2026.** In a dislocation-poor
  regime the mechanical funnel averages ≈ market parity (mean test edge
  ≈ −1%).
- Structure that *did* transfer, at the class level: every hold config landed
  near the bottom of the test window (1 of 8 positive); every monthly-funded
  TRIM config was positive (8 of 8, +1.6% to +11.7%); annual TRIM mixed
  (3 of 8 positive).

**Conclusion: point-optima are curve-fitting; class-level rules carry
signal.** Everything below is organized around that distinction.

---

## Tier 1 — ground truths (survived every variation we threw at them)

**1. The entry gates genuinely discriminate.** Median 5-year excess vs the
survivorship-free market, identical windows, full 22k-name universe
1998–2015: funnel buys **+3.9%**, trap-vetoed names **−10.9%**, naive
30%-dip buys **−23.9%** — monotone at every horizon, replicated within the
point-in-time S&P 500 (+0.8/−10.8/−8.0). Buying quality-gated deep
dislocations is better than buying dips, and the trap sweep correctly
identifies worse-performing names. This is entry-level, cash-free evidence,
independent of every portfolio knob.

**2. Mechanical thesis-break selling destroys value, always.** Every policy
containing trap-flag exits lost in every configuration tested ($373–434k vs
$579k hold in the flagship comparison). Mechanism understood: recession
filings make quality names look statistically broken, so mechanical
fundamentals-based exits sell the 2001/2009 lows. **Trap exits are
human-judgment territory, permanently.**

**3. If you sell mechanically at all, sell rarely and on valuation-plus-DCF.**
More selling was worse at every step (s85 < s90 < s95 < s99, all rows, all
grids), price-pattern exits (peak-dip, 5-day-peak) lost badly, and the
TRIM-class beat the hold-class in both holdout windows. Within the rare end
(s95 vs s99) the ordering is regime-dependent — s99 usually ≥ s95 but not
always — so the threshold inside [0.95, 0.99] is second-order. The two-leg
design (band percentile AND optimistic owner-earnings DCF) is what makes
rare TRIM safe on compounders; peak-price rules fail precisely because
compounders set price highs constantly while their multiples stay sane.

**4. Deployment capacity is first-order — as large as stock selection.**
Lot size relative to funding rate drove swings as big as any threshold:
quarter-month lots at cap 3 turned a winning strategy into a losing one
(−24.6% edge); fixed $1k lots created a capacity ceiling (multiple decays
9.5× → 5.4× as funding rises); lot ∝ funding is exactly scale-invariant
(arithmetic, verified). Too-small lots spread the same dollars over ~200
names and converge on the index. **Concentration is where the portfolio-level
edge lives** — per-episode entry quality (cohort medians) barely moved across
configs that differed by 2× in terminal value.

**5. The edge is crisis-concentrated.** The 2013–2026 window standalone
averages ≈ parity across configs; the crisis-rich windows produced nearly
all outperformance; the ratio curves hover near 1.0 until 2009 in most
plans. The funnel is a **dislocation harvester**: in calm regimes, expect
market-like results from the mechanical layer, and expect the payoff to
arrive in lumps after panics. A decade of ≈ parity is within the historical
range of configurations that end far ahead.

**6. Specific thresholds do not generalize.** Documented reversals: buy
depth flipped three times (b5 dominant in 1998–2015 grids under both funding
models → b15/b20 better on the full record → unstable in the holdout); s95
vs hold flipped between 1998–2015 and the full record; buy-depth ordering
inverted under monthly funding. Plus the holdout's ρ = 0.24. **Any specific
percentile we "found" is a fitted number.** The durable knowledge is the
direction of mechanisms, not point values.

**7. Benchmark engineering decides what results mean — and it's easy to get
wrong.** Three bugs/biases caught in-flight, each large enough to flip
conclusions: delisted names silently dropping out of the EW average
(survivorship re-introduced); benchmark legs funded annually while the funnel
was funded monthly (front-loading asymmetry — Adam caught this); the original
EDGAR backtest's universe being today's watchlist (survivor-selected by
construction, its own docstring said "results are a CEILING"). The honest
comparator is: same cashflows, same cadence, survivorship-free, and EW —
which itself beat SPY by ~$74k per $580k over 1998–2026 but *lost* to SPY
2016–2026 (cap-weight regime). Benchmark choice is a result-sized effect.

**8. Funding mechanics create their own artifacts.** Annual lumps produce
calendar-driven starvation (28% of weeks had an unfundable BUY; late-year by
arithmetic); monthly funding halves starved weeks and is ≈ free for
TRIM-class configs (and was the only consistently-positive class out of
sample) but hurts hold-class configs, whose only funding is contributions.
Recycled TRIM proceeds were the *majority* of deployment in rare-TRIM
configs ($1.39M deployed on $580k contributed), meaning sell policy and
funding smoothness are entangled — another reason class rules, not points.

**9. The data platform itself.** Point-in-time fundamentals floor is 1998
(nothing retail reaches 1995); delisted-company fundamentals are the
difference between honest and self-flattering results (15,638 of 21,960
tickers in the store are delisted); AR dimensions + filing dates prevent
restatement look-ahead; quarterly S&P membership snapshots prevent
index-membership look-ahead. All four were load-bearing at least once.

---

## Tier 2 — supported, operate with awareness of regime-dependence

- **Monthly funding + full-month lot + cap 3** is the best-supported plan
  class: 7/12 factorial cells over the full record, 8/8 positive out of
  sample. Reasonable default for future tests and for live-cadence thinking.
- **Entry depth is a capacity dial, not an alpha dial.** Deep (b5) suits slow
  funding relative to opportunity flow; shallower (b10–b20) suits faster
  funding. Choose it from the funding side, don't tune it from backtests.
- **TRIM line within [0.95, 0.99]: second-order.** Rarer is usually not
  worse. The guardrails that matter: two-leg test, full-position sells were
  what we simulated (live "trim a slice" is gentler than anything tested).
- **SPY-parking the reserve:** ≈ wash in crisis eras (bull carry cancelled by
  selling parked SPY low to fund crash fills), mildly positive in calm ones.
  Legitimate either way; philosophical choice about what dry powder is for.

## Refuted this campaign (do not resurrect without new evidence)

Peak-price sell rules (both variants) · band-percentile-only sells ·
mechanical trap sells in any mix · quarter-month lots at cap 3 · "s95 is
optimal" · "b5 is dominant" · "throttling, not starvation, binds" (my
inference; instrumentation said 28% of weeks starved) · "monthly funding is
worse" (single-cell artifact; class-level it's the best-supported) ·
comparing configs across cadences on raw finals (front-loading bias).

---

## What this means for the live system

1. **No config changes forced by this campaign.** The live b5/s95 sits inside
   the defensible class (deep entry suits the live system's slow real
   funding; s95 is within the second-order band). Its earlier justification
   ("global max of both grids") is dead — the holdout killed threshold
   optimality claims — but the setting survives on class logic.
2. **The division of labor is confirmed from both directions:** machines may
   enter (gates discriminate) and may rarely TRIM (safe); machines may never
   sell on deterioration (procyclical); Stage-2 judgment is the layer that
   must beat the trap-sweep's crisis false-positives — and since the
   mechanical funnel ≈ parity in calm regimes, **Stage-2 non-degradation is
   the whole game in calm markets, and crisis judgment is the whole upside.**
   The graveyard monitor + MECH shadow account remain the next build.
3. **Expectation-setting:** most years the funnel should roughly track the
   market; the compounding case rests on being present, funded, and
   un-vetoed in the two or three panics per generation.

## Standing limits

One 28-year path; no Stage-2 anywhere in the sims; full-liquidation TRIM;
no market impact; alphabetical rationing in cash-constrained weeks;
delisted names frozen at last trade; S&P 500 PIT universe only (by
decision); ~400 configs evaluated against the same history — every number
in this file is in-sample for the campaign as a whole except the single
holdout test, which is why the holdout outranks everything else here.
