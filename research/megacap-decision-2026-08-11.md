# Mega-caps: what was adopted, what was rejected, and why

*2026-08-11. Branch `regime-adaptive`. Development windows only — 1998-2019,
2000-2019, 2010-2019. Nothing here touches the 2020-present holdout.*

## The problem this was trying to solve

The funnel beats a survivorship-free equal-weight market in every window we
have. It beats SPY comfortably from 1998 (+25.8%) and from 2000 (+15.8%). It
**loses to SPY from 2010 (-4.6%)**, and the goal is to succeed regardless of
entry regime.

The earlier decomposition established that this gap is a *factor* problem, not
a *selection* problem: selection contributes +8.8% with a standard deviation of
4.7% and is positive in 8 of 8 sub-periods, while the factor leg has a mean of
+6.1% with a standard deviation of 9.2%. The funnel picks well and is exposed
to the wrong thing.

The wrong thing is specifically mega-cap. The funnel structurally cannot own it:
a company large enough to move a cap-weighted index almost never trades at the
5th percentile of its own EV/EBIT history, because its valuation band is
dominated by its own earlier growth era. So two levers were tested, both of
which reach that factor without rebuilding the strategy.

## Lever A — lower the Piotroski bar for mega-caps (ADOPTED)

`--mega-fscore 5`. The mega-cap basket is the synthetic point-in-time top-50
cap-weighted index built by `scripts/build_megacap_index.py`.

The prior argument came first and is the real basis for this. Piotroski built
and validated the F-score on small, thinly-covered value names — the population
where accounting-quality signals are most likely to be unpriced. Applying F>=7
to the most heavily analysed companies on earth is out of domain. The bar is
not wrong, it is *calibrated for someone else*.

That argues for recalibration, not abandonment. Both were tested, and the
difference between them is legible in a single name:

```
F test dropped entirely:   C   9 fills  2007-10 .. 2016-06   $290.38 -> $28.35
F bar lowered to 5:        C   1 fill   2016-06              $28.35
```

Dropping the test bought Citigroup at $290 (split-adjusted) in October 2007 and
kept buying it down. Citi is ~$80 in 2019: a permanent impairment, no bailout
upside, never recovered. A bar of 5 watched the balance sheet come apart and
refused. Bank of America is the same shape (14 fills 2008-01 to 2009-02 under
no test, 3 fills under F>=5) — though there the lower bar also gives up BAC's
$4.58 fills, which were the winner in that cohort. Net it is still ahead.

**Immunity has no limiting principle.** It asserts the score carries zero
information for this population; the Citi trace says otherwise. A lower bar is
bounded: the gate still does its job, only its threshold moves.

| window | baseline | F dropped | **F>=5** |
|---|---|---|---|
| 2010-2019 | — | +0.5% | **+0.3%** |
| 2000-2019 | — | not run | **+3.0%** |
| 1998-2019 | — | +3.3% | **+4.5%** |

Same sign in all three windows. The +4.5% vs +3.3% margin is *not* the case for
this — it is well within what a 22-year run can produce by chance. The case is
the prior argument plus the mechanism.

### The caveat that survives adoption

**49% of the mega-cap admissions under F>=5 fall in 2008-2009.** The effect is
broad in names (48 distinct, no single dominant contributor) but narrow in
time, and it is concentrated in the one episode where systemically important
financial institutions were rescued by policy choice. That happened once. The
counterfactual is not in the data.

F>=5 is the *more defensible* version of that trade — it is what refuses Citi —
but it is not an escape from the episode dependence. Read the full-window gain
as partly unearned.

## Lever B — park idle reserve in mega-cap (NOT ADOPTED, kept as an option)

`--park-mega FRAC`. Splits the parked reserve between SPY and the synthetic
mega-cap index. The idea is sound on its face: the funnel never buys this
factor, so idle cash is the natural place to hold it.

The response curve looked monotone and encouraging:

| parked mix | 2010-2019 | 2000-2019 |
|---|---|---|
| 25% mega | +0.6% | +0.0% |
| 50/50 | +1.2% | +0.3% |
| 75% mega | +1.8% | +0.5% |
| 100% mega | +2.3% | +0.3% |

**Then it was run on 1998-2019 for the first time, and the sign flipped.**

| window | park 50/50 vs baseline |
|---|---|
| 2010-2019 | +1.2% |
| 2000-2019 | +0.3% |
| **1998-2019** | **-0.4%** |

The only window where this looks good is the calm decade — which is precisely
the decade mega-cap had its unrepeatable run. Extend the window back to 1998
and the reserve is parked in Microsoft, Cisco, Intel and Lucent at peak dot-com
multiples and held through 2000-2002. It loses.

This is the same failure mode the campaign has hit repeatedly: a lever looks
good in one window because that window contains the thing the lever is a bet
on. Measuring mega-cap exposure over the mega-cap decade is not evidence.

Not adopted. The flag stays; the diversification argument for it (reserve
should not sit entirely in one factor) is coherent, but it is a risk argument
made *against* the return evidence, not supported by it.

## Lever C — loosen the buy line for mega-caps (REJECTED)

`--mega-buy-pctl`. Direction has a real argument — a mature giant essentially
never reaches the 5th percentile of its own history. The number does not.

| mega buy bar | 2010-2019 | 1998-2019 |
|---|---|---|
| b5 (unchanged) | +0.3% | +4.5% |
| b25 | **+2.1%** | +3.7% |
| b50 | +1.6% | **+4.8%** |
| b100 (no line) | +1.7% | +2.7% |

**The argmax moves between windows — 25 in one, 50 in the other — and the calm
window spread across b25/b50/b100 is 0.4pp.** That is noise, and picking from
it is exactly the threshold re-tuning the holdout discipline exists to stop.
No buy-line change adopted.

One genuine finding survives from this arm. Removing the valuation line
entirely (b100) is *worse* in the full window despite six points more average
exposure — 86% invested against b50's 83%, and 2.1pp less return. **This is the
first knob in the whole campaign where more exposure produced less return**,
i.e. the first evidence that the buy percentile carries selection content
rather than being the exposure dial in disguise. It holds in 1998-2019 only;
the calm decade has nothing genuinely distressed to discriminate against, which
is coherent rather than contradictory.

## Interaction

A and B were tested together on all three windows:

| window | A alone | B alone | additive | actual A+B | interaction |
|---|---|---|---|---|---|
| 2010-2019 | +0.3% | +1.2% | +1.5% | +1.4% | **-0.1%** |
| 2000-2019 | +3.0% | +0.3% | +3.3% | +3.2% | **-0.1%** |
| 1998-2019 | +4.5% | -0.4% | +4.1% | +4.1% | **-0.1%** |

Sub-additivity was expected — A admits more mega-caps, which spends reserve,
and B only pays on reserve — and it is real but negligible at -0.1% in every
window. The levers are independent.

## What none of this does

Every variant still trails SPY in the calm window. The best full configuration
tested (A+B) reaches **-3.3% vs SPY in 2010-2019**, against the baseline's
-4.6%; A alone reaches -4.3%.

**The mega-cap work closes at most a quarter of the entry-regime gap, and the
adopted half closes almost none of it in that window.** The 2010 entry problem
is not solved.

## Buy-side blind spot (found here, not addressed)

The negative-EBIT fallback built to close the GME hole is **sell-side only**.
It answers "is this egregiously expensive?" — EV/S percentile plus a record
market cap plus a price all-time high. The buy path still refuses any name with
no current multiple.

Instrumented and measured over 1998-2019:

| | name-weeks | names |
|---|---|---|
| 20%+ off high, EBIT<=0, no multiple | **14,412** | 273 |
| ...EV/S in own bottom 25% | 8,890 | 218 |
| ...EV/S in own bottom 5% | 3,905 | 160 |

For scale the funnel makes ~2,025 fills over the same window. The buy side is
skipping a population several times the size of what it acts on. An earlier
guess that this was negligible, on the grounds that F>=7 would veto these names
anyway, is **untested** — they exit the loop before any gate runs.

It is 14 names and 258 weeks for mega-caps specifically, so **it does not
interact with the F>=5 change.**

If a buy-side version is ever built it should use **normalized EBIT, not
EV/S**. The flagged population mixes real cyclical troughs (MU, DVN, LEN, NEM)
with terminal cases — CHKAQ, i.e. Chesapeake carrying its bankruptcy suffix,
is flagged 66 times as cheap on EV/S. Only a "profitable across most of the
last decade" precondition separates them, and that precondition is the buy-side
analogue of what the ratchets do on the sell side. Note also that the sell-side
fallback's safety comes from ratchets at an all-time *high*, which are hard to
fake; there is no mirror image, because a record *low* is exactly what terminal
decline looks like.

Separate piece of work, different population, not on this branch.

## Status

- `--mega-fscore 5` is **the settled mega-cap rule** for this strategy.
- `--park-mega` remains available and is **not part of the system**.
- No buy-line change.
- **Nothing is merged, and the live path has no mega-cap concept at all.**
  Implementing A live requires a live definition of the mega-cap basket, which
  does not exist yet. Settling on the rule is a decision about the strategy,
  not a change to what runs.

## Reproduce

```bash
python scripts/build_megacap_index.py
python scripts/backtest_universe.py --sp500 --trim-only --buy-pctl 0.05 \
  --sell-pctl 0.97 --inject 20000 --park --cadence monthly --lot-frac 0.01 \
  --cap 12 --mega-fscore 5 --start 1998-01-01 --end 2019-12-31
```

Baselines for comparison, after the report-clobbering fix: **1998-2019
$1,657,079 / 1933 fills; 2000-2019 $1,339,515 / 1690; 2010-2019 $389,112 /
528.** The unsuffixed report files in `reports/` had been overwritten in the
working tree by mega runs and were restored from git; the overwriting runs are
kept as `UNKNOWN-PROVENANCE-*` because their exact configuration can no longer
be established.
