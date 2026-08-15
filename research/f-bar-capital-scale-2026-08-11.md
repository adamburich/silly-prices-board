# The F-bar window disagreement is capital, not calendar — resolved

*2026-08-11. Branch `mechanized`. Development windows only — nothing here
touches the 2020-present reserve.*

## The problem this closes

`reports/fscore-bar-and-rationing-2026-08-11.md` adopted F>=7 on two of three
windows and recorded the third as dissenting. On corrected scores the dissent
got **wider**, not narrower:

| loosening the bar | F>=4 | F>=5 | F>=6 |
|---|---|---|---|
| 2010-2019 | -10.2 | -8.8 | -4.5 |
| 2000-2019 | -7.0 | -5.2 | -1.9 |
| **1998-2019** | **+8.3** | **+11.1** | **+9.1** |

Two windows say tighten. One says loosen by 11 points — and it is the *longest*
window with the *most* crisis content, which matters because this strategy's
entire thesis is crisis harvesting. It was the only evidence against the single
most consequential gate in the configuration.

The explanation on file was capital scale: a looser bar is *additive* when
capital is plentiful and *substitutive* when scarce, and a 1998 start reaches
the shared calendar years holding more capital than a 2000 start does. That
diagnosis rested on one diagnostic over fill distributions, and the original
write-up said so — "'traced' is generous."

## The test

`--open-with` (new): a balance the account already holds at week 0 in inject
mode. **Every benchmark leg receives the same dollars at the same instant** via
`leg_values()`, and `contributed_for()` counts it, so the edge stays a
same-cashflow comparison rather than the funnel being handed free capital.
Default 0.0; verified byte-identical output on the committed 2000-2019 baseline
($1,345,039 / 1640 fills) before any result below was trusted.

The 1998-start run held **$53,557** entering 2000. Give that to the 2000-start
run and hold everything else — calendar, universe, crises, gates — fixed.

**Prediction, written before the runs:** if capital scale is the mechanism, the
F>=5-minus-F>=7 delta in the 2000 window climbs from -5.2pp toward positive. If
it stays flat, capital scale is refuted.

## Result

2000-2019, adopted funding config, varying only the opening balance:

| head start | F>=7 edge | F>=5 edge | **F5 - F7** | starved at F>=7 |
|---|---|---|---|---|
| $0 | +18.44% | +12.31% | **-6.13pp** | 715 |
| **$53,557** | +10.19% | +20.45% | **+10.26pp** | 153 |
| $150,000 | -2.63% | +6.63% | **+9.26pp** | 0 |

*(1998-start window, for reference: **+11.1pp**)*

Handing the 2000 window exactly the capital the 1998 run held entering 2000
**flips the sign and lands within 0.9pp of the 1998 value** — +10.26 against
+11.1. Same calendar, same crises, same universe. Only capital changed.

That is the mechanism reproduced on demand, not merely a consistent story.

## Why, mechanically

The starvation column carries it. At $0 the funnel is starved 715 times at
F>=7; loosening to F>=5 drives that to 5,934, so the extra names mostly cannot
be funded and instead **displace** better ones through the alphabetical
rationing queue. Substitutive, and it costs money. At a $150k head start F>=7
starves **zero** times — there is spare capacity, the extra names are **added**
rather than substituted, and loosening pays.

**So the F bar is not really a quality parameter. It is a rationing device** —
and a good one, measurably better than sorting by ticker (which is what
`ORDER BY ticker` in the queue amounts to). Its value is a function of how
capital-constrained the funnel is. Three windows disagreed because they held
different amounts of capital, not because they contained different truths.

This also explains, in passing, why the fourth independent rationing key
(`quality`) was a no-op at F>=7 and recovered +4.0pp at F>=4: at a strict bar
the pool is uniformly good so there is nothing to sort, and the bar has already
done the rationing.

## Falling out of it

At a $150k head start **F>=7 goes negative against its own benchmark**
(-2.63%). With abundant capital and a strict bar the money cannot find enough
qualifying names and sits parked. That is the capacity ceiling the campaign
previously found from the other direction, with fixed $1k lots.

## What this settles, and what it does not

**Settles:** the 1998 window is not evidence against F>=7. It is a measurement
of a different capacity regime. F>=7 stands for the adopted configuration,
which is squarely in the constrained regime — 715 / 242 / 178 starved signals
at F>=7 across the three windows from a $0 start.

**Does not settle:** which regime the LIVE system is in. That is a question
about live's funding rate against its signal rate, and the harness cannot
answer it. Note the relevant divergence: live's lot is fixed at
`tranche_usd = 1000` rather than scaling with contributions, so the exact
scale-invariance of lot-proportional funding does not apply to it.

## Reproduce

```bash
python scripts/backtest_universe.py --sp500 --trim-only --buy-pctl 0.05 \
  --sell-pctl 0.97 --inject 20000 --park --cadence monthly --lot-frac 0.01 \
  --cap 12 --open-with 53557 [--fscore 5] --start 2000-01-01 --end 2019-12-31
```

`--open-with` is a DIAGNOSTIC, not a strategy knob. It is not part of the
frozen configuration (reports/frozen-config-2026-08-11.md).
