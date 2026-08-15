# Live's idiosyncratic filter is worse than deleting signals at random

*2026-08-11. Branch `mechanized`. Development windows only — 1998-2019,
2000-2019, 2010-2019. Nothing here touches the 2020-present holdout.*

## What was tested and why

Live requires a name's 52-week drawdown to exceed its **sector benchmark's** by
10 percentage points, and rejects it as "sector-wide" otherwise
(`screen.py:42`). The harness has never had a sector comparison at all — the
sector field is used only to decide whether a name is a financial.

Of the live-only rules catalogued in the harness/live audit, this was judged the
most worth having. It encodes the strategy's actual thesis: *"the market is
wrong about this company"* is a different claim from *"the market repriced this
whole sector"*, and only the first is a mispricing you can expect to close.

It was also set up as a **falsification test for the central claim**. The edge
here is crisis-concentrated, and in a crisis the sector is down too. If
requiring idiosyncratic dislocation erased the edge, then much of what we have
been calling selection would be "bought beta at the bottom" — implementable
with SPY and a calendar, and worth far less than we think.

## Construction

Two sector-benchmark bases were built, because the choice turned out to matter.

**Cap-weighted (`--idio-basis cap`, the faithful one).**
`scripts/build_sector_indices.py` builds point-in-time cap-weighted indices per
Sharadar sector from S&P 500 members, following
`build_megacap_index.py` including both of its corrections — weight by shares
outstanding rather than filed market cap (a stale marketcap trims winners and
tops up losers, a systematic anti-momentum drag), and mark to market **before**
rebalancing (the other order discards the rebalance week's return). Basis is
`close`, split-adjusted and NOT dividend-adjusted, because it is compared
against the name's own price drawdown; shares are derived as
marketcap / close-at-filing on that same series.

**Equal-weighted (`--idio-basis ew`).** Legs chained from weekly equal-weight
price returns over the swept universe, accumulated during the sweep.

Sector ETFs were not an option: XLE/XLF/XLK and friends mostly begin 1998-12,
which would gut the 1998 window.

Chained weekly returns rather than px/p0 in both cases: sector membership turns
over substantially across 22 years, and a start-fixed basket drifts into being a
different sector than the one a 2015 name is compared against.

## Results

| 1998-2019 | vs baseline | vs EW | vs SPY | dropped | avg invested |
|---|---|---|---|---|---|
| no filter | — | +6.1% | +25.8% | — | 80% |
| **idio 10pp, cap-weighted** | **-6.3%** | **-0.6%** | +17.9% | 30% | 68% |
| idio 10pp, equal-weighted | -5.0% | +0.8% | +19.5% | 26% | 73% |
| **random null, 3 seeds** | **-2.1%** (-2.6..-1.6) | ~+3.9% | ~+23.2% | 26% | 78% |

| 2000-2019 | vs baseline | vs EW | vs SPY | dropped | avg invested |
|---|---|---|---|---|---|
| no filter | — | +18.0% | +15.8% | — | 81% |
| **idio 10pp, cap-weighted** | **-3.8%** | +13.4% | +11.3% | 30% | 76% |
| idio 10pp, equal-weighted | -2.7% | +14.8% | +12.7% | 27% | 78% |
| **random null, 3 seeds** | **+0.0%** (-0.8..+1.3) | ~+18.0% | ~+15.8% | 27% | 79% |

2010-2019: -0.5% cap / +0.1% EW, 10% dropped. No null run — nothing to explain.

## The null is the whole finding

Any filter that removes signals also removes deployment, and deployment alone
is worth a great deal in a crisis-rich window — five separate levers in this
campaign have turned out to be the exposure dial in disguise. So the rule has to
be compared against **random removal at the same rate** (`--drop-frac`), not
against no filter at all.

**The rule loses to random by 4.2pp in 1998-2019 and 3.8pp in 2000-2019, both
outside the seed ranges.** It is not a throttle. It is a throttle that
preferentially removes the entries that pay.

The mechanism is in the exposure column: a 30% signal cut costs **12 points** of
average deployment (80% -> 68%); a 26% *random* cut costs 2. The rule's removals
cluster — it strips out crisis weeks, when many names fire at once and capital
actually gets put to work. That is inherent to what it does: in a crash the
sector is down too, so nothing clears a 10pp excess bar precisely when the
funnel's best opportunities exist.

In the long window it takes the edge over equal-weight **negative** (-0.6%):
with live's rule applied, the strategy loses to a survivorship-free EW market.

## The falsification did NOT fire

Random removal of a quarter of the signals costs -2.1% and +0.0%. **The edge
survives arbitrary thinning almost intact**, so it does not depend on any
particular subset and is not "bought beta at the bottom." That is a real defence
of the central claim, and it was the outcome I would have bet against.

## A prediction that was wrong, in sign

Going in, the expectation was that cap-weighting would reject FEWER names than
EW, on the reasoning that equal-weight falls harder in a crash (the small
members fall furthest), so EW sector drawdowns run deeper and excess drawdown
runs smaller.

**Cap-weighting rejected MORE** — 30% vs 26-27% — and did more damage.

The premise is true of broad small-cap-led selloffs and false for both crashes
in this data. The dot-com unwind and the GFC were **epicentre-at-the-top**
events: Cisco, Intel, Lucent and Microsoft were the inflated part of Technology;
Citigroup, AIG and Bank of America were the inflated part of Financials.
Cap-weighted sector indices therefore fell *further* than equal-weighted ones
exactly when it matters. The caveat offered as protection for the rule was
backwards.

## Before this becomes a live change

**The harness models this as a hard veto; live applies it as a demotion.** A
sector-wide name still appears in the screen table labelled `no (sector-wide)`
— it is excluded from the zone list that directs attention, not deleted. Live's
version is therefore less severe than what was measured here, and the remedy may
be as small as changing how those names are surfaced rather than removing the
rule.

Other gaps between test and reality:
- The harness runs the S&P 500 point-in-time; live runs a curated watchlist.
- Live's benchmarks are real sector ETFs with different constituents and
  classification than these synthetic indices.
- Sharadar sector labels are current, not point-in-time (affects the benchmark,
  not the strategy).
- The `(unclassified)` sector has no members, so unclassified names get a sector
  drawdown of zero and pass unconditionally — a small leak toward
  under-filtering.

## Status

- `--idio-threshold` / `--idio-basis` / `--drop-frac` added, all default OFF.
- **NOT adopted in the harness.** The rule is worse than random.
- Whether live should keep it is open, pending the demotion-vs-veto distinction
  above. It is the most damaging thing measured in this campaign, and it is
  running with real money.

## Reproduce

```bash
python scripts/build_sector_indices.py
python scripts/backtest_universe.py --sp500 --trim-only --buy-pctl 0.05 \
  --sell-pctl 0.97 --inject 20000 --park --cadence monthly --lot-frac 0.01 \
  --cap 12 --idio-threshold 0.10 --idio-basis cap \
  --start 1998-01-01 --end 2019-12-31
# the null it must beat:
python scripts/backtest_universe.py ... --drop-frac 0.30 --drop-seed 1
```

Baselines: 1998-2019 $1,657,079 / 1933 fills; 2000-2019 $1,339,515 / 1690;
2010-2019 $389,112 / 528.
