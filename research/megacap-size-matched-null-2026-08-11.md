# The mega-cap lane against a size-matched null: partial, and the big number is the weak one

*2026-08-11. Development windows only. The control the lane never had.*

## Why the existing control was not enough

`fscore-bar-and-rationing-2026-08-11.md` argued that a bar of 5 applied to
mega-caps helps (+5.82/+2.75/+0.44) while the same bar applied to the WHOLE book
hurts (+11.1/-5.2/-8.8), and read that as ruling out generic loosening.

It does not, because the two arms are not volume-matched: global F>=5 multiplies
starved signals 6-21x (242 -> 5,212 in 1998-2019) where mega-only moves them
1.6-2.5x (242 -> 609). It separates "loosen for nearly everything" from "loosen
for a small subset" — never "mega-caps" from "any small subset".

## The null

`--random SEED` on the index builder draws TOP names at random from the SAME
eligible pool the cap ranking uses, at the SAME 114 snapshots. `--mega-basket`
(new, separate from `--mega-index`, which only picks the parking asset) points
the membership map at it. So the only thing that differs from the real basket is
WHICH names — not how many, not when, not whether they have usable data.

Both arms treat ~10% of the universe at any moment. Distinct-names-ever differs
(185 real vs ~885 random) because the top-50 is sticky and a random draw churns,
but the treated share of dislocation-weeks is matched, which is the quantity
that governs how much loosening happens.

## Result

| which 50 names | 1998-2019 | 2000-2019 | 2010-2019 |
|---|---|---|---|
| **REAL top-50 by market cap** | **+5.82%** | **+2.75%** | **+0.44%** |
| random seed 1 | +3.09% | -3.21% | -0.90% |
| random seed 2 | **+5.98%** | +0.46% | -2.15% |
| random seed 3 | +1.08% | -1.15% | -0.22% |
| null mean | +3.38% | -1.30% | -1.09% |
| null range | +1.08 .. +5.98 | -3.21 .. +0.46 | -2.15 .. -0.22 |

**VERDICT: PARTIAL.**

- **2000-2019 and 2010-2019: the real basket beats every null seed.** Random
  loosening HURTS on average (-1.30%, -1.09%) where mega loosening helps. Net of
  the null: **+4.05pp and +1.53pp**. This is the lane's real evidence.
- **1998-2019: the real basket sits INSIDE the null range, and seed 2 (+5.98%)
  BEATS it.** Net of the null mean, +2.44pp — but not separable from chance on
  three draws.

**The headline number is the un-special one.** +5.82% in 1998 was the largest
mega-cap result and the one most quoted; against the null, most of it is
"loosening a bar for some 50 names", not "loosening it for the LARGEST 50".
Where the measured effect was small (+2.75, +0.44) is exactly where it clears
the null cleanly.

## The treatment is if anything larger in the null arm

Extra fills over baseline, 1998-2019: real **+92**, random **+113 / +125 / +121**.
Random baskets admit MORE names, and still lose in two windows. So the mega
result in 2000/2010 is not explained by treatment volume — which strengthens
those two cells.

## What this does and does not change

**Does not refute the lane.** It was adopted on a prior argument (Piotroski
calibrated on small, thinly-covered names; out of domain on the most analysed
companies on earth) plus the Citigroup trace — dropping the F test entirely
bought C at $290 in Oct-2007 and kept buying to $28; a bar of 5 refused. The
return margin was explicitly discounted at adoption. None of that is touched.

**Does weaken the return evidence, and kills one claim outright:** "the control
rules out generic loosening" is now too strong in both directions. The right
statement is that mega-specificity shows in two of three windows, is absent in
the third, and that the third is where the biggest number came from.

**n = 3 seeds.** The quoted range is a range of three draws, not a confidence
interval. Six more seeds would cost minutes and would materially firm this up.

## Reproduce

```bash
python scripts/build_megacap_index.py --random 1
python scripts/backtest_universe.py --sp500 --trim-only --buy-pctl 0.05 \
  --sell-pctl 0.97 --inject 20000 --park --cadence monthly --lot-frac 0.01 \
  --cap 12 --mega-fscore 5 --mega-basket megacap-index-rand1.json \
  --start 1998-01-01 --end 2019-12-31
```
