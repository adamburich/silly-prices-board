# Lot size is an exposure dial — 2026-08-11

Started from Adam's concern that a 28% starvation rate lets luck into the
method. It does not, and chasing it produced a better finding than the one we
were looking for.

## 1. The starvation metric was measuring the wrong thing

Actual starvation under the configs in play (2000-2019, monthly $20k/yr):
full-month lot + cap 3 was starved **54% of weeks** (4,840 signals), not 28%.
Quarter-month + cap 8: 26%.

But shrinking the lot to $100 drove starvation to **0.0% while leaving
$327,651 idle**. Starvation asks "can I afford one lot", not "am I deploying
capital" — make the lot small enough and the answer is always yes. It is a
broken proxy for the thing it was being used to measure.

## 2. The apparent "concentration wins" result was cash drag

Unparked, returns rose monotonically with lot size (-16.9% at $100 to +10.6%
at $600) and it was tempting to read that as concentration beating dilution.
Adam challenged it as front-loading/exposure. He was right. Parked:

| lot | unparked vs EW | parked vs EW |
|---|---|---|
| $100 | -16.9% | +11.9% |
| $200 | +1.3% | +18.0% |
| $400 | +9.8% | +16.6% |
| $600 | +10.6% | +17.1% |
| $1,667 cap 3 | +1.5% | +2.9% |

The spread collapses from 27.5 points to 6.1 — roughly **78% of the gradient
was idle cash**. Name selection is identical across every cell (same buy rule,
same signals); only the dollars differ.

## 3. Lot size is an exposure dial, and its sign is regime-dependent

With the weekly reserve series recorded, average capital actually invested in
funnel positions (rather than the parked reserve) is computable:

| lot | avg invested 2000-19 | avg invested 2010-19 | vs EW 2000-19 | vs EW 2010-19 |
|---|---|---|---|---|
| $100 | 58.8% | 39.1% | +11.9% | +7.9% |
| $200 | 80.9% | 58.5% | +18.0% | +4.9% |
| $400 | 87.0% | 70.6% | +16.6% | +2.3% |
| $600 | 88.9% | 80.7% | +17.0% | +5.0% |

**corr(avg invested %, edge): +0.90 in 2000-2019, -0.73 in 2010-2019.**

Exposure to the funnel's picks pays when the picks beat SPY (crisis-rich) and
costs when they do not (crisis-poor). That is why the lot-size ordering
*inverts* between windows — $100 was worst in one and best in the other. Not
noise: regime-dependent exposure. "Which lot size" was always an
asset-allocation question in disguise.

Corollary, stated plainly: **in the calm decade the funnel's own selections
underperformed SPY at every lot size**, and the parked reserve carried the
result. Both windows beat EW; only the crisis-rich one did so on the picks.

## 4. Monthly spend cap and the CRISIS exemption

New mechanisms (scripts/backtest_universe.py `--month-cap`, `--crisis-exempt`).
The cap limits any calendar month's spend to a fraction of the annual
contribution, so a month that catches signals while the account is flush
cannot outbid the lean months. The exemption suspends the cap in any week
regime.py reads CRISIS — binary, never a multiplier, since a multiplier would
be a fitted parameter.

Exemption's own contribution (CX minus capped):

| window | CRISIS weeks | $200 | $400 |
|---|---|---|---|
| 2000-2019 | 15.0% | +3.1pp | +2.8pp |
| 2010-2019 | 1.3% | +0.77pp | +0.31pp |

It scales with crisis exposure in both windows at both lot sizes — the
mechanism behaves as its story requires, and it is a regime-conditional
exposure increase, which §3 says is exactly the right shape.

**But the cap alone is neutral** (-0.2/-1.9/-0.5/-6.1pp in 2000-19;
+0.95/-0.93pp in 2010-19 — no consistent sign), and the exemption exists to
undo the cap. Net of each other the pair is a wash: two new mechanisms plus a
regime control input to land where parking alone already puts us.

The pair's remaining argument is the cap's fairness property (equal purchasing
power per month, less arrival-order luck). That property is **unmeasured** —
every number here is terminal wealth on one path. Adopting the pair currently
requires taking the structural argument on faith.

## Status

- Not adopted. No live config change from any of this.
- Retracted along the way: a "floor at $200" claim that failed to replicate
  ($100 was worst in 2000-19, best in 2010-19).
- $200 (1% of annual / 12% of a month) is a defensible operating choice, but
  on operational grounds — most granularity without dilution, inside a region
  where returns do not discriminate — **not** because it wins. It led in
  2000-19 and placed third in 2010-19.
- Cap above ~12 is inert: cap 12 and cap 25 are identical at every lot size,
  because the ladder needs successively lower weekly closes and runs that long
  essentially do not occur.
- All of the above is training-side (1998-2019) per
  reports/holdout-discipline-2026-08-11.md.
