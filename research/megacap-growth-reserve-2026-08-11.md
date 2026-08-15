# The reserve should hold what the funnel cannot own — growth, not blend

*2026-08-11, after the reserve run. Development windows only (1998/2000/2010 ->
2019). See the contamination note below: this work followed the holdout, and
that is recorded rather than glossed.*

## The question, and the conflation that hid it

`--park-mega` was tested and rejected earlier today: the response curve looked
monotone across 25/50/75/100% in two windows, then **flipped sign to -0.4% the
first time it met 1998-2019**, because the parked reserve was holding Microsoft,
Cisco, Intel and Lucent at peak dot-com multiples through 2000-2002.

Adam then asked why MGK had been dismissed, on the grounds that **its growth
tilt is the offset the system needs**. Re-reading the record, I had conflated
two different jobs an index does here:

- **The acquisition list** (Lever A, adopted): which names get the lower
  Piotroski bar. MGK is genuinely wrong for this — it screens out Berkshire,
  the banks and the majors, which are the only mega-caps that ever reach the
  5th percentile of their own valuation history.
- **The parked reserve** (Lever B): what idle money holds. Here MGK's logic is
  the right one, and it is the decision record's own argument: *"the funnel
  never buys this factor, so idle cash is the natural place to hold it."*

**Lever B had only ever been tested with a BLEND proxy**, which
`build_megacap_index.py` flags about itself: *"this is mega-cap BLEND, not
mega-cap GROWTH... it understates the growth tilt in both directions."* The
rejected version was never the asset the argument was about.

## A bug that would have shipped as a finding

The style screen selects the top half of the cap-selected basket, but the
existing "require a full basket of TOP names" guard ran **after** the screen —
so every screened basket (25 of 50 by construction) was rejected and the index
held its SPY stand-in for its entire life.

It was caught only because **both growth variants returned identical values**
(12.670). Had the numbers been merely plausible rather than identical, two
"growth" indices that were actually SPY would have been tested and reported.
The guard now applies to the cap selection, with a separate minimum on what
survives the screen. Default (`--growth none`) verified to rebuild the
committed blend index byte-identically.

## Two growth proxies, and only one works

| proxy | definition | 2000 basket | result |
|---|---|---|---|
| `rev` | top half by trailing 3y revenue CAGR | CSCO, DELL, EMC, **Sun**, **Lucent**, MSFT, ORCL, QCOM | **works** |
| `ps` | top half by price-to-sales (richest) | AIG, AMGN, BMY, CSCO, **KO, LLY, MRK** | fails |

`ps` is negative in two of three windows. It is a poor style proxy because
price-to-sales keys on margin structure as much as growth, so it drags in
defensives — its 2000 basket is half pharma. **That reasoning was produced
after seeing which proxy won, and one degree of freedom is spent on it.**

## The result: 12 cells, all positive, monotone

`--park-mega F --mega-index megacap-index-rev.json`, against the adopted
configuration (b5/s97/F>=7/mega F>=5):

| reserve in growth | 1998-2019 | 2000-2019 | 2010-2019 |
|---|---|---|---|
| 25% | +0.48% | +1.01% | +1.34% |
| 50% | +1.07% | +1.50% | +2.86% |
| 75% | +1.66% | +2.20% | +4.44% |
| **100%** | **+2.12%** | **+2.92%** | **+6.03%** |

**The decisive cell is 1998-2019 at 100%** — maximum exposure to Sun, Lucent,
Cisco and EMC held straight through the dot-com unwind, with no SPY to dilute
it — and it holds sign at +2.12%. That is the exact cell that killed the blend
version. The growth version survives it.

Against SPY in the calm decade, which is what the entire mega-cap programme was
launched to fix (baseline -4.6% at the time, -1.23% on the adopted config):

    0% growth-parked   -1.23%      75%   +3.16%
   25%                 +0.09%     100%   +4.73%
   50%                 +1.60%

## Why I discount it anyway

**The optimum is at the boundary and still rising at 100%.** That is not a
saturation curve — it is "more of this asset is always better", which restates
*this asset outperformed*. The `rev` index compounds 19.07x against blend's
11.22x.

This campaign has already made exactly this call against itself: `b20/s99` was
the maximum cell of the threshold grid and was **declined** for being at a
boundary, while s97 was adopted because it **saturated** (95->97 worth +9.4pp,
97->99 worth -1.4pp). This is not that shape.

**It measures a factor, not the funnel.** The backtest can only show the
premium existed 1998-2019 — the greatest mega-cap-growth era in market history.
It cannot show it persists.

**The screen is momentum-adjacent.** Top half by trailing revenue growth among
current mega-caps, cap-weighted, rebalanced quarterly, is close to
momentum-on-mega-caps. Construction is point-in-time clean; the *selection* is
not neutral.

## CONTAMINATION NOTE

This work was done **after** the 2020-2026 reserve was spent, so it has no clean
out-of-sample test available and never will. Additionally, **reserve-period
behaviour of the growth index was briefly observed before the adoption
decision.** Recorded because the person most likely to be misled by a record
implying otherwise is us, later, reconstructing why this was done.

The motivation itself predates the reserve — the factor decomposition
(selection +8.8%, positive 8 of 8 sub-periods; the factor leg is the variable
one) and the "funnel structurally cannot own mega-cap" argument are both
development-era. The reserve supplied urgency, not evidence.

## Live status

**Live has no reserve at all** — zero park/reserve/idle machinery in
`autotrade.py` or `broker.py`; the paper account pairs each funnel lot with an
equal benchmark buy, which is a measurement design, not a portfolio.

This was initially written up as a blocker on adoption. **That was wrong**, and
Adam corrected it: the frozen configuration itself uses `--park`, so the
baseline that was just tested against the reserve is *equally* unimplementable.
A reserve mechanism was always required. This is therefore not an extra
architecture project — it is a parameter on one already needed, which makes
"what does the reserve hold" a live design question rather than a hypothetical.

## Recommendation

**Adopt at 50%, on risk grounds, explicitly NOT at the 100% the curve points
to.** The reasoning is not the backtest:

- SPY is cap-weighted and by 2026 is itself heavily mega-cap growth, so a fully
  growth-tilted reserve doubles down rather than diversifying.
- But SPY also contains the value names the funnel buys, so it is a partially
  *correlated* reserve — a growth tilt is the purer offset.
- Half in the factor the funnel cannot own, half in the broad market, resolves
  those against each other without taking a boundary bet.

That is the risk/diversification argument the original record said this always
was: *"coherent, but a risk argument made against the return evidence, not
supported by it."* It is now a risk argument made **with** the return evidence
rather than against it — which is a better position, and still not a mandate to
put the whole reserve in one factor.

## Reproduce

```bash
python scripts/build_megacap_index.py --growth rev
python scripts/backtest_universe.py --sp500 --trim-only --buy-pctl 0.05 \
  --sell-pctl 0.97 --inject 20000 --park --cadence monthly --lot-frac 0.01 \
  --cap 12 --mega-fscore 5 --park-mega 0.5 \
  --mega-index megacap-index-rev.json --start 1998-01-01 --end 2019-12-31
```


---

# APPENDED: the cash sleeve, tested and rejected; and the chosen setting

## Adam's proposal: 75% growth / 25% cash

The reasoning was a barbell aimed squarely at the two-part finding — growth for
the BOOM carry, cash for the CRISIS liquidation. Both mechanisms were built and
tested rather than argued about.

**`--park-cash F`** — a three-way constant mix (growth / cash / SPY), cash from
the 3-month T-bill (`data/regime/dtb3.csv`, cached from FRED DTB3 so a backtest
has no network dependency).

**`--cash-first`** — the sleeves held SEPARATELY, cash spent BEFORE the risk
leg, refilled only when the risk index is at a running high. This is the only
form in which a cash allocation is a buffer at all: a constant mix cannot be,
because weekly rebalancing feeds cash INTO the falling leg and `from_reserve()`
sells the blend pro rata.

Both verified inert when off — the default path reproduced the committed
2010-2019 result byte-identically before either was trusted.

| reserve | 1998-2019 | 2000-2019 | 2010-2019 |
|---|---|---|---|
| 75% growth / 25% SPY | +1.66% | +2.20% | +4.44% |
| 100% growth | +2.12% | +2.92% | +6.03% |
| **75% growth / 25% cash — constant mix** | **-0.90%** | **-1.55%** | **-0.67%** |
| **75% growth / 25% cash — cash-first** | **-2.08%** | **-1.84%** | **-1.05%** |

**Both land below the plain SPY baseline.** Cash carry is too poor to pay for
the buffer it provides.

### My refill rule was flawed, and it is why cash-first is the worse of the two

Refill-at-running-high makes it a ONE-WAY DRAIN out of growth: the sleeve is
spent on fills and rebuilt by selling growth at highs, with no path back in.
Constant mix rebalances both directions; cash-first only ever sells. Over 22
years of a rising asset that bleeds growth exposure systematically. A version
refilling only from contributions would be strictly better — and would still
carry 25% in an asset earning ~1.9%/yr, which is the damage that actually
matters.

### The diagnosis behind the proposal was RIGHT, and here is its evidence

Starvation, which terminal wealth hides:

| reserve | starved signals, 1998-2019 |
|---|---|
| SPY baseline | **609** |
| 75% growth / 25% SPY | 682 |
| 100% growth | **710** |
| 75/25 cash-first | 671 |

**The growth tilt costs 101 extra starved signals** — the reserve is worth less
precisely when the funnel fires at 354 fills per 100 weeks. That liquidation
problem is real and measurable. The buffer even works on its own terms
(710 -> 671); it just costs 2.08% of terminal value to buy 39 fills.

**Right diagnosis, wrong remedy.** The lever that addresses the same problem
without the carry cost is simply holding less growth.

## The full ladder — and everything is LINEAR

| growth share | 1998-2019 | 2000-2019 | 2010-2019 | starved '98 | vs SPY 2010 |
|---|---|---|---|---|---|
| 0% (all SPY) | — | — | — | 609 | -1.23% |
| 25% | +0.48% | +1.01% | +1.34% | 614 | +0.09% |
| 50% | +1.07% | +1.50% | +2.86% | 660 | +1.60% |
| **66.7% (2/3)** | **+1.50%** | **+1.99%** | **+4.07%** | **676** | **+2.79%** |
| 75% | +1.66% | +2.20% | +4.44% | 682 | +3.16% |
| 100% | +2.12% | +2.92% | +6.03% | 710 | +4.73% |

Return is linear in the growth share. So is starvation: 609 + 0.667 x 101 =
676.4 against 676 observed. So is the SPY gap.

**THERE IS NO INTERIOR OPTIMUM.** This is not a strategy parameter with a right
answer; it is a dial on how much growth exposure is wanted, and every setting is
exactly as supported as every other. The only defensible claims are directional:
more growth = more return and more crisis starvation, monotonically, on one
path, in that factor's best era.

(The linearity is also a sanity check: a constant-mix blend of two assets should
interpolate linearly, and it does.)

## CHOSEN: 2/3 growth, 1/3 SPY

**Adam's call on a dial, NOT a derived result.** Recorded that way deliberately,
because a future reader who believes the number was optimised will over-trust
it. Every alternative on the table — 50%, 75% — is equally supported by this
evidence, which has no opinion between them.

What it buys: +1.50 / +1.99 / +4.07 against the adopted configuration, positive
in all three windows; the calm decade moves from -1.23% to +2.79% against SPY,
closing the entry-regime gap the whole mega-cap programme was launched to fix.
What it costs: 67 extra starved signals over 22 years, about one missed fill
every four months.

Implement with **VOO** for the SPY third — same index, ~6bp/yr cheaper, and
already `benchmark = "VOO"` in config. SPY is used in the harness only because
it is the one ETF in the Sharadar store and VOO's inception is 2010.
