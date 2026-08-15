# The reserve composition is one dial, and the funding profile sets its gain

*2026-08-12. Answers `reports/research-brief-reserve-composition.md`. Full
grids in `reports/research/reserve/` (`summary.md`, `cells.csv`,
`regime-verdict.md`); driver in `scripts/research_reserve.py`.*

**Recommendation in one line: the live account (profile C) keeps two-thirds
growth and no cash, unchanged; a lump sum steps down to half; nobody holds
cash; and the composition does not key off the market regime. The funding
profile changes how hard the dial bites, not which way it turns — so there is
one dial, not three answers.**

| the brief's three questions | answer |
|---|---|
| 1. Should composition depend on the FUNDING PROFILE? | **Not in direction — only in how much it matters.** Same sign in every era for every profile; a lump sum takes 2–3x the swing. |
| 2. What composition per profile? Cash? Mix or buffer? | **2/3 growth for contribution funding, 1/2 for a lump sum. No cash at any profile.** Constant mix vs spend-first buffer is a non-effect at matched weights. |
| 3. Should it depend on the DETECTED REGIME? | **Not supported. 0 of 24 cells clear the bars on either window set.** |

---

## 0. What was actually run

Twelve windows x eight funding profiles x 25 reserve compositions = 2,400
simulations, plus 2,952 regime cells. That is affordable because the expensive
part — `sweep_name()` over every point-in-time S&P 500 member — depends on none
of it. One sweep per window feeds every cell, which is the argument
`--also-inject` already makes inside `backtest_universe.py`, generalised.

**Every window's grid is gated on reproducing the committed CLI artifact to
the dollar** before it is allowed to write a file: funnel value, EW market
value and starved-signal count, for the `g67` cell in every profile that has a
reference run. All twelve pass. The replay is the harness, not a second
implementation of it.

Two things had to be repaired first, and both are the kind of thing that
silently corrupts a result rather than announcing itself:

- **The store had lost QQQ, MGK and MGC.** `SPY` was present, the other three
  ETFs had zero rows, and `sharadar.load_closes` returns an empty dict rather
  than raising — so a QQQ benchmark simply vanishes from the output. Re-ingested
  from `data/bulk/funds-full.csv.zip` (all four present, through 2026-08-11).
- **Profile B was over-funded by 1.16% in every 2026 window.** The committed
  runs used a rounded `--capital 580000` where profile A's total is
  $573,333.33, and lump-sum results scale linearly in capital, so B carried a
  free 1.16% in exactly the comparison that is supposed to isolate timing.
  All 17 reference artifacts (8 x A, 8 x B, 1 x C) were regenerated on one data
  snapshot with exact totals, plus 8 more for the sub-periods, and
  `reports/backtest-current-2026-08-12.html` was rebuilt from them. Only the
  four 2026 windows moved, and only in level: every `vs` percentage on that
  page is unchanged, because funnel and benchmark scale together — which is
  itself the proof that the 1.16% was a free gift and not a wash.

Three harness changes, all verified inert when off: `--park-cash` now works
without `--park-mega` (it was silently ignored in constant-mix form while the
filename still said `-cash50`); `--cash-seed` funds a cash-first buffer at week
0 instead of waiting for a running high; and **`--lots` is now in the report
filename** — the collision the brief opens with.

---

## 1. Three of the brief's own findings do not survive re-measurement

The brief was explicit that its §2 and §3 results were first-pass. They are.

### 1a. The §3 cash columns were not the same portfolio

`-mega50-qqq-cash50` is `--park-mega 0.5 --park-cash 0.5`, which is **50%
growth / 0% SPY / 50% cash** — not the "50%cash/25/25" the table calls it.
`-cash50first` *is* 25/25/50, because with `--cash-first` the mega share
applies to the risk sleeve only. So the row that "proves cash-first beats the
constant mix" compares two different asset mixes.

Run at **matched weights**, the mechanism is a non-effect:

| weights (growth/SPY/cash) | buffer − mix, nested | buffer − mix, disjoint | maxDD delta |
|---|---|---|---|
| 25/25/50 | +0.15% | +0.95% | −0.1pp |
| 50/25/25 | +1.60% | +0.58% | −0.1pp |
| 0/50/50 | −0.29% | −0.25% | −0.1pp |
| 50/0/50 | +3.70% | −0.12% | −1.4pp |
| 0/75/25 | −0.56% | +0.06% | +0.3pp |

(Profile B; A and C are the same within ±1.5%.) Funding the buffer at week 0
rather than at a running high — the mechanical flaw noted in the 2026-08-11
record — moves it a further −0.5% to 0.0%. **Constant mix versus spend-first
buffer does not matter. The weights matter.** That is a real answer to the
brief's second question, and it is "neither".

### 1b. "A cash sleeve buys a 4x reduction in dispersion" is a fact about BRK.B

Same cells, two denominators, profile B, standard deviation across the eight
windows:

| composition | stdev vs BRK.B | stdev vs SPY |
|---|---|---|
| `g67` (incumbent) | **42.3** | 14.8 |
| `g50` | 36.2 | **11.3** |
| `g25c50` (25/25/50 mix) | **9.4** | 33.4 |
| `c100` (all T-bills) | 18.8 | **54.4** |

(The brief's §3 quotes 39.5 / 12.2 / 9.1 for the same three cells against
BRK.B. Those are population standard deviations; these are sample ones, a
factor of sqrt(8/7). Every underlying per-window figure reproduces to the
decimal — the §3 numbers are right, the label on one of its columns is not.)

Cash minimises dispersion against Berkshire and **maximises** it against SPY.
Berkshire rose through 2000–02 and lagged the 2010s, which is the same era
profile a cash-heavy reserve has; the ratio is stable because the numerator and
denominator move together, not because the strategy got steadier. The brief
suspected this ("the sharpest possible foil"); it is quantified now. Read
dispersion off the benchmark-free column, or off drawdown, not off BRK.B.

### 1c. The eight windows count the dot-com unwind five times

1998-2026, 1998-2019, 2000-2026, 2000-2020 and 2000-2019 all contain 2000–02.
Three distinct start dates are wearing eight labels. So the study was also run
on **five disjoint sub-periods** — 1998-2002, 2003-2007, 2008-2012, 2013-2019,
2020-2026, no week in two of them, each funded in its own right and each gated
on its own reference artifact.

The conclusion inverts. Profile B, median against the incumbent:

| composition | nested 8 | n+ | disjoint 5 | n+ |
|---|---|---|---|---|
| `g50` | **+4.56%** | 5/8 | **−0.7%** | 1/5 |
| `g25` | **+4.64%** | 5/8 | **−2.6%** | 1/5 |
| `g0` | −1.34% | 4/8 | −5.6% | 1/5 |
| `g25c50` | −1.41% | 4/8 | **−18.3%** | 2/5 |
| `c100` | −10.54% | 3/8 | **−33.3%** | 2/5 |

On the disjoint set the only composition with a positive median in any profile
is `g100` for C (+0.4%, 3/5) — and it is −0.1% for B and −0.2% for A. Every
de-risking cell is negative. **The de-risking result in the nested set is the
2000–02 crash counted five times.**

Neither set is wrong. The nested windows answer *"if I start at one of three
dates and hold to the end, what would I have wanted?"* — decision-relevant for
a hold-forever program. The disjoint set answers *"in a typical market episode,
how often does this help?"* — the honest frequency. **A claim that only holds
in one of the two is a claim about window construction.**

---

## 2. What the profile actually changes: gain, not sign

The five disjoint episodes, every composition against the incumbent, all three
profiles. The signs move together; the magnitudes do not:

| `c100` (all cash) vs `g67` | 1998-2002 | 2003-2007 | 2008-2012 | 2013-2019 | 2020-2026 |
|---|---|---|---|---|---|
| A — contributions | +24.5% | −5.8% | −0.9% | −32.9% | −16.7% |
| C — live | +31.6% | −12.7% | −0.4% | −40.3% | −28.9% |
| B — lump sum | +33.7% | −33.3% | **+24.6%** | −63.4% | −56.5% |

Every profile wants less risk in 1998-2002 and more of it in 2003-2007,
2013-2019 and 2020-2026. The lump sum just wants it two to three times as much.
Only 2008-2012 separates them, and it separates them by amplitude too: the
contribution profiles are flat there (−0.9%, −0.4% — the GFC arrived when the
reserve was small relative to money still to come) while the lump sum, holding
most of its money in the reserve, gains +24.6%. On the growth dial the signs
agree in **all five** episodes, and the magnitude is monotone in seed:

| period | A | C | S0 | S25 | S50 | B |
|---|---|---|---|---|---|---|
| 1998-2002 | −10.4% | −14.8% | −18.0% | −19.8% | −21.3% | **−24.1%** |
| 2013-2019 | +16.5% | +20.3% | +18.7% | +23.9% | +27.9% | **+33.8%** |

Monotone in seed fraction, same sign throughout. **The funding profile is an
amplifier on a dial whose direction is set by the market, not by the profile.**
The brief's framing — "contributions and lump sums appear to want opposite
things" — is an artifact of aggregating an amplified dial over a window that
contains both eras: a bigger seed loses more in the crash, compounds off a
smaller base afterwards, and the long-run net moves even though the per-era
preference never flipped.

### This is the campaign's own class rule, showing up in a new place

`reports/backtest-synthesis-2026-08-10.md` established that **every capacity
knob is the same knob — exposure — and that its sign flips by regime**:
corr(average invested %, edge) = +0.90 in the crisis-rich 2000-2019 window and
−0.73 in the crisis-poor 2010-2019 one. That is why lot-size and depth-trigger
orderings invert between windows.

The reserve composition is the same shape with one difference worth being
precise about: it does **not** move average invested percentage at all (profile
B holds ~50% parked at every composition, within half a point). It moves the
beta of the parked half. Same era-dependence, same sign flip, different
mechanism — which is a modest strengthening of the class rule rather than a new
phenomenon, and it is the reason nothing here should be read as "the reserve
has an optimal composition".

And it is the second time `reports/megacap-decision-2026-08-11.md`'s own
warning applies: **never evaluate a factor bet over the window that factor
won.** The growth index wins 1998-2026. Every number in this record inherits
that.

### The seed dial, which is the transferable form

Total money and the lot rule (total/1200) identical across all five; only the
fraction arriving at week 0 changes. Median against `g67` over the eight
windows:

| composition | seed 0% | 5% | 25% | 50% | 100% |
|---|---|---|---|---|---|
| `g100` | **+2.34%** | +1.39% | +0.38% | −2.25% | −11.63% |
| `g50` | −1.84% | −1.55% | −1.07% | +1.19% | **+4.56%** |
| `g25` | −4.45% | −4.88% | −3.96% | +0.85% | **+4.64%** |
| `c100` | −25.23% | −25.83% | −27.96% | −18.45% | −10.54% |

Monotone in both directions, crossing between 25% and 50% seed. **The
class-level rule: the more of your lifetime funding is already in the account,
the less growth tilt the reserve should carry — and cash is never on the
ladder at any seed level, only less bad at the top of it.** That is a
direction. It is not a licence to compute a seed-to-cash formula; the crossing
point is a property of 1998–2026, not of the world.

---

## 3. The cost of insurance, stated as the brief demands

A cash sleeve buys drawdown. It is not free and it is not cheap.

**What it buys** (mean / worst maximum drawdown, five disjoint episodes):

| | A — contributions | C — live | B — lump sum |
|---|---|---|---|
| `g67` (incumbent) | 20.3% / 27.8% | 21.6% / 31.6% | 34.5% / 65.5% |
| `g67c25` (25% cash) | 18.2% / 26.8% | 18.1% / 29.6% | 30.5% / 60.6% |
| `g25c50` (50% cash) | 16.5% / 26.9% | 14.4% / 29.6% | **19.0% / 33.7%** |
| `c100` (all cash) | 12.8% / 26.9% | 10.2% / 29.5% | 6.6% / 16.7% |

**What it costs** (median terminal value against `g67`):

| | A | C | B |
|---|---|---|---|
| `g67c25` | −1.5% disjoint, −6.6% nested | −3.3% / −7.8% | −8.8% / −5.2% |
| `g25c50` | −3.1% / −15.1% | −6.7% / −17.0% | −18.3% / −1.4% |
| `c100` | −5.8% / −24.5% | −12.7% / −28.0% | −33.3% / −10.5% |

Read the diagonal. **For a contribution-funded account the trade is bad in both
directions**: half the account in cash cuts the mean drawdown by 4 points
(20.3% → 16.5%) — because the drawdown is driven by the funnel's own holdings,
not the reserve — and costs 3% to 15% of terminal value. In 2008-2012 profile A
draws down 26.6–26.9% at *every* composition including 100% cash. The reserve
cannot protect what it is not holding.

**For a lump sum the trade is real**: 50% cash nearly halves the mean drawdown
(34.5% → 19.0%) and cuts the worst from 65.5% to 33.7%, for a median 18.3% of
terminal value on the disjoint set. That is a genuine risk-preference decision
and this study has no standing to make it. What it can say is that the price
is about 1.2% of terminal wealth per point of mean drawdown avoided, and that
the protection arrives concentrated in two of five episodes.

### The other cost, which terminal value hides

Starved signals — BUY-rated names with no lot of powder — at the incumbent,
mean over the eight windows: `g100` 1,640, `g67` 1,210, `g50` 1,037, `g0` 676,
`c100` 73 (profile B). **The growth tilt is worth less exactly when the funnel
fires**, which is the diagnosis the 2026-08-11 record already made and got
right. Cash fixes starvation almost completely and pays for it in carry.

---

## 4. Against the four benchmarks, and the two that were missing

Full tables in `summary.md`. Two legs were synthesised for this study because
the brief is right that a conservative client's real alternative is not in the
harness: **a savings account** (roll 3-month T-bills, `data/regime/dtb3.csv`)
and **a 60/40** (60% SPY / 40% T-bill, rebalanced weekly). Honest limit: the
40 is T-bills, not intermediate Treasuries, so it understates a real 60/40
across the falling-rate era — read it as a floor.

The headline is unchanged by benchmark choice for the contribution profiles and
completely determined by it for the lump sum (§1b). Against SPY, profile B at
`g67` wins all eight windows; against BRK.B it wins four; against the
survivorship-free EW market it wins four. **Berkshire is not the bar. It is one
comparator with a strong era profile, and this study's own dispersion result
flips sign when it is swapped out.**

The conservative alternatives, at the incumbent composition, median across
windows (`beats` = windows won):

| | vs 60/40 | vs savings account | vs SPY | vs BRK.B |
|---|---|---|---|---|
| A, nested 8 | +89.0% (8/8) | +267.8% (8/8) | +27.3% (7/8) | +33.1% (8/8) |
| A, disjoint 5 | +12.8% (5/5) | +29.8% (4/5) | +1.1% (4/5) | +4.4% (3/5) |
| C, nested 8 | +93.0% (8/8) | +273.9% (8/8) | +31.0% (8/8) | +31.9% (8/8) |
| C, disjoint 5 | +12.2% (5/5) | +29.5% (4/5) | +3.8% (5/5) | +11.9% (3/5) |
| B, nested 8 | +85.6% (8/8) | +321.5% (8/8) | +23.7% (8/8) | −4.7% (4/8) |
| B, disjoint 5 | +24.0% (4/5) | +58.9% (4/5) | +12.0% (5/5) | +24.6% (3/5) |

The single window where the funnel loses to a 60/40 is 2008-2012 for the lump
sum (−9.7%), and the savings account beats it in 1998-2002. Both are the same
fact as everything else in this record: **the reserve is the account early on,
and early on is when the crash happens or does not.** Read the disjoint row —
the nested row's +268% against T-bills is 28 years of compounding against 2%,
which is arithmetic, not evidence.

---

## 5. The regime question

### What was declared before any regime result was seen

A **two-state collapse**, never a 5-state x 3-asset mapping (which would be
fifteen free numbers). Both endpoint compositions are cells that already exist
in the static grid, so every rule is bracketed by two measured constants. One
dimension moves at a time. **Both directions of every rule** are run, because
the sign is the finding and choosing it afterwards would be the fit. Both
partitions are run.

| rule | calm | stressed | what it says |
|---|---|---|---|
| `powder` | `g67c25` | `g67` | save powder in the boom, spend it into the panic |
| `powder_inv` | `g67` | `g67c25` | raise cash after the fall |
| `tilt` | `g67` | `g0` | drop the style tilt when stress arrives |
| `tilt_inv` | `g0` | `g67` | take the style tilt on when stress arrives |

Partitions: `BC` = {BEAR, CRISIS}; `DBC` = {DECLINE, BEAR, CRISIS}.

**Decidable on the day (§6.4), and this is the part that is easy to get wrong.**
The weight earning the return from week *i−1* to week *i* is the weight set at
week *i−1*, from the label published on or before *i−1*. Weighting week *i*'s
return by week *i*'s label would let the reserve de-risk on the same bar the
crash prints — not a strategy, a peek. Every leg here, real and null alike, uses
*i−1*. The lag controls are therefore 4 and 13 weeks *on top of* the rule's own
one-week decision lag.

### Two things about the labels themselves, audited before use

**The blackout is real, and it is not where the brief says it is.**
`data/regime/hy_oas.csv` has **no internal gap at all** — it is contiguous daily
from 1996-12-31 and simply *ends* at 2021-03-19. The gap is created at merge
time, because FRED/ICE truncated `BAMLH0A0HYM2` to a rolling three-year window.
The label series carries `stress_basis = VIX-ONLY` for exactly **122 weeks,
2021-04-09 → 2023-08-04** — two weeks later than the data ends, because
`OAS_STALE_DAYS = 14` forward-fills the last value twice before nulling it.
`hy_oas` is empty on all 122 rows: nothing frozen, nothing interpolated.

The bias is **one-directional**: credit enters stress only through a `max()`,
so a missing OAS can only *understate* stress. Those 122 weeks contain **zero
CRISIS labels**, and CRISIS is reachable only via `stress == 3` (VIX10 > 40 or
OAS > 8%). Had credit read bucket-2 (≥ 6%), 63 of the 122 weeks would flip
label. Whether it did is not answerable from this repo.

**One correction to the brief's own §6.4.** "PIT-clean by construction" holds
for the trend, drawdown, VIX and OAS legs — every window terminates at the
as-of index, and there is no full-sample percentile anywhere in `regime.py`.
It does **not** hold for the Sahm/recession leg: `_unrate_series` fetches the
BLS *current* series, which is revised and annually re-seasonally-adjusted, so
the June-2008 unemployment rate the classifier sees is not the one published in
July 2008. Seasonal adjustment is a full-sample filter. Second-order here — the
Sahm gate only qualifies the BEAR rung between 10% and 20% drawdown — but the
claim as written is too strong.

**And a reproducibility hazard nobody has recorded:** `data/regime/hy_oas.csv`
is gitignored. On a clean checkout, `regime --history --csv` would emit
VIX-only stress for **all 1,493 weeks**, silently, with only the `stress_basis`
column to reveal it. `reports/regime-history.csv` is not reproducible from the
repository.

### What the two-state collapse actually is, which decides how to read any pass

Before any result: the wider partition is **not a regime signal**. Over the full
1,493-week history, `DBC` = {DECLINE, BEAR, CRISIS} is 380 weeks, and a pure
*"the S&P 500 is at least 10% below its 52-week high"* rule is 342 weeks —
**every one of which is inside DBC**, Jaccard agreement **90.0%**. 88.7% of DBC
weeks are also below the 200-day average. The credit spread, the VIX, the Sahm
rule and four of the five states are doing almost no work in that partition.

`BC` = {BEAR, CRISIS} is different: 220 weeks, 76% of them `stress == 3`, i.e.
a VIX or credit spike. Jaccard against the drawdown rule is 63.8%. That is a
genuinely vol/credit-driven partition — and it is also the short-lived one.

So a `DBC` rule that works should be read as **"reduce the growth tilt while the
index is 10% off its high"**, a trend/drawdown filter with the classifier as an
expensive wrapper. A `BC` rule that works would be the interesting one, because
nothing simpler produces it.

### Verdict: NOT SUPPORTED

**0 of 24 rule x partition x profile cells clear the bars, on either window
set.** Full table in `reports/research/reserve/regime-verdict.md` — 2,952
regime cells on the nested windows and 1,476 more on the disjoint ones, every
one reported.

Exactly one cell came close, and following it down is the whole answer.

**`tilt` at `DBC` for the lump sum** — drop the growth tilt to all-SPY whenever
the label is DECLINE, BEAR or CRISIS — returns **+30.87%** median against the
incumbent on the eight nested windows. It beats both its own endpoints, beats
the best static composition in the grid (+11.21%), beats every shuffled and
every block-shuffled seed on medians, and survives both lags (+31.67% at 4
weeks, +25.61% at 13). On the brief's four bars as literally written, it
passes.

It is still not a finding, for four independent reasons.

**One: it is a drawdown filter wearing a classifier.** `DBC` is 90.0% Jaccard-
identical to "the index is 10% off its high" (see above). Nothing in the credit
spread, the VIX or the Sahm rule is doing the work — which is also why it
survives a 13-week lag, since drawdowns persist for quarters. The `BC`
partition, the one that *is* vol/credit-driven, fails everywhere: `tilt`/`BC`/B
is +7.42% and dies at lag 13 (−6.30%).

**Two: the median across windows hides that shuffled labels beat it, in its own
windows.** Per window, ranking the real labels among all twelve nulls:

| window | real vs `g67` | lag4 | lag13 | best shuffle | rank of real (1–13) |
|---|---|---|---|---|---|
| 1998-2026 | +48.5% | +41.5% | +37.7% | **+58.1%** | 2 |
| 1998-2019 | +43.0% | +38.5% | +39.1% | +28.1% | **1** |
| 2000-2026 | +34.0% | +35.3% | +25.8% | **+55.5%** | 3 |
| 2000-2020 | +30.2% | +30.2% | +25.4% | +12.4% | **1** |
| 2000-2019 | +31.6% | +33.2% | +27.6% | +13.1% | **1** |
| 2010-2026 | +6.6% | **−16.3%** | −15.4% | +39.5% | 7 |
| 2010-2019 | +11.9% | **−9.4%** | −4.5% | +11.5% | **1** |
| 2020-2026 | **−7.4%** | −8.7% | −13.5% | +43.1% | 6 |

Top of thirteen in four of eight windows — and all four contain a crash. In the
two windows with no crisis to catch it is **mid-pack among randomly shuffled
labels**, and it is the only one of the eight where the rule loses money. The
lag columns tell the same story from the other side: the rule survives a
quarter's delay in every crash window and dies under it in every calm one. That
is a rule that works when a big drawdown happens to be in the sample, which is
the definition of the thing the nulls exist to catch. Adding "beat every null
in its own window, in a majority of windows" as a fifth bar takes the count from
1 of 24 to **0 of 24**.

**Three: it does not survive the disjoint episodes.** On the five sub-periods
the same cell is **−3.89%**, first of thirteen in two of five, median rank
6/13, and **both lags are negative** (−4.40%, −4.15%). The +30.87% was the
2000–02 crash counted five times, exactly as in §1c.

**Four: the direction that should work, does not.** `powder` — save cash while
calm, spend it into the panic, the one rule with a mechanism the reserve's own
job argues for — is negative in every cell on both window sets, and loses to
its own endpoints. Its inverse does no better. If regime information were in
these labels at all, the economically motivated rule is where it should have
shown up.

### How well the nulls matched

The shuffle null switches ten to twelve times as often as the real labels
(2000-2019, `BC`: 30 real switches vs 312–343), which is the point — it
destroys the clustering. The block null keeps runs contiguous and reorders only
when they happen; it switches somewhat more often than the real labels (36–45
against 30) because reordering five-state runs merges some adjacent ones under
a two-state collapse. Reported rather than claimed as an exact match. Neither
imperfection favours the conclusion: both nulls are, if anything, handicapped,
and the real labels still did not clear them.

### The CRISIS exemption, put through the same battery

`reports/lot-size-exposure-finding-2026-08-11.md` §4 measured `--crisis-exempt`
at +3.1 / +2.8pp (2000-2019) and +0.77 / +0.31pp (2010-2019) against the capped
run, scaling with crisis exposure. That result reproduces exactly — the "pp"
are percentage points of the vs-EW excess, and 1354024/1135662 −
1318300/1135662 = +3.15pp. It was never run against a null. It has been now
(`scripts/research_crisis_exempt.py`, `crisis-exempt-verdict.md`).

**It was measured on a reserve that no longer exists.** Both reserves, run off
the same sweeps, profile A, median across the eight nested windows:

| reserve | cap alone | exemption vs capped | per-window rank of real labels | lag13 |
|---|---|---|---|---|
| `g0` — plain SPY, what the original ran | −0.61% | **+0.51%** | 1st in 3/8 (median 3/13) | −0.15% |
| `g67` — 2/3 growth, live | **+9.80%** | **−9.74%** | 1st in 1/8 (**median 13/13**) | −9.40% |

On its own configuration the original finding holds up: the cap is neutral, the
exemption is mildly positive, and it beats both null families on medians. It
still fails two bars — it is top-ranked in only three of eight windows, and it
dies under a one-quarter lag. **On the live configuration the sign inverts and
the nulls become emphatic: median rank 13 of 13.** Releasing the budget in real
CRISIS weeks is the *worst* available timing, worse than every shuffled and
block-shuffled draw, in essentially every window.

The mechanism is not mysterious and it is this study's own subject: a crisis is
exactly when the reserve is cheapest to liquidate. Spending it then is fine
when it holds SPY and expensive when it holds mega-cap growth, which fell
harder in 2000-02 and compounded further afterwards. **The exemption's value is
a function of what the reserve holds** — which is why it belongs in this record
rather than its own.

The single most direct refutation is 1998-2002, the most crisis-dense disjoint
episode (81 of 261 weeks): the exemption on real labels is **+1.13%**, while the
best shuffled draw is **+5.77%** and the best block draw **+6.20%**. Random
weeks did five times better than the actual crisis weeks.

**Two honest caveats on my own test.** First, the cap's large `g67` numbers
(+9.80%) are mostly the exposure dial again — "spend slower" means "stay parked
in growth longer", the campaign's `every capacity knob is exposure` rule
arriving through the budget. Second, the cap binds as a function of how much
reserve has accumulated, which scales with window length: 100 blocked fills in
1998-2002 against 2,996 in 1998-2019. On the disjoint sub-periods the whole
effect nearly vanishes (cap +0.23% / +1.04%, exemption +0.11% / +0.09%). The
big nested numbers should not be over-read in either direction.

**Verdict: 0 of 6 reserve x profile cells clear the bars, on either window
set.** The exemption is not a live positive and should not be cited as one.
What it demonstrably is: a mechanism whose sign is set by the reserve
composition, tested once on a configuration that has since changed underneath
it.

### This agrees with the campaign's existing answer

`reports/regime-adaptive-investigation-2026-08-11.md` ran six independent design
lenses at the *buy rule* and all six returned do-nothing with zero new
parameters. This is the same question asked of a different lever — what the
idle money holds rather than when it is spent — and it returns the same answer
by a different route. Two independent do-nothings is a better position than one.

---

## 6. Recommendation

**Profile A (contributions) — keep two-thirds growth. No cash.**
Nothing beats it on the disjoint set; `g100` beats it by +2.13% median on the
nested set and by −0.2% on the disjoint one, which is the definition of not
established. Every cash cell loses in four of five episodes and costs 1.5% to
5.8% median for a drawdown reduction the funnel's own holdings swamp.

**Profile C (live, $10k seed + $1k/mo) — keep two-thirds growth. Same answer
as A, for the same reason.** The brief asked whether C sits near A; it does on
this question — its seed is 4% of lifetime funding, at the flat end of the seed
dial. But C is *not* like A on a different axis, and this is the operational
finding of the study: **C parks 41.7% of the account on average, against A's
32.6%, and its reserve never drops below 10.4% where A's floor is 0.2%.** In
2020-2026 C parks 67.7%. The live account is a reserve with a funnel attached,
not the other way round. That makes the composition a larger decision for the
live system than for A even though the answer is the same — and it raises a
question this study did not test: whether $100 lots are too small for $1,000 a
month.

**Profile B (lump sum) — step the growth share down to half. Not because it
earns more, but because it does not earn less and is better on everything
else.**

| `g50` vs `g67`, profile B | nested 8 | disjoint 5 |
|---|---|---|
| terminal value, median | **+4.56%** (5/8, worst −7.8%) | **−0.66%** (1/5, worst −4.4%) |
| maximum drawdown, mean | **44.8%** vs 49.2% | **32.5%** vs 34.5% |
| starved signals, mean | **1,037** vs 1,210 | 154 vs 150 |

The return case is frame-dependent and I will not lean on it: +4.56% on the
nested windows is the 2000–02 crash counted five times, and it is −0.66% when
that crash is counted once. **The drawdown case is not frame-dependent** — 4.4
and 2.0 points better respectively — and it costs nothing measurable. `g50` is
also exactly where the seed dial (§2) points at 100% seed, so this is the class
rule being applied, not a cell being picked. It is not a boundary and it is not
a fitted number: `g25` is the adjacent cell and is a wash on return with three
times the dispersion, which is what makes 50% rather than 25% the answer.

What is *established* for B independently of any of that: **the same dial has
two to three times the effect it has for A, so for a lump sum the composition
is a portfolio decision, not a parking detail** — the brief's §2 mechanism,
confirmed.

**What none of this supports: a different composition per profile.** The
direction of the dial is the same everywhere; only the gain changes. Running
one composition across all three profiles is simpler, is what the live system
already does, and gives up nothing this study can measure.

### Rejected, and why

- **A cash sleeve at any weight.** Loses in four of five disjoint episodes in
  every profile. Its only clean win is the crash-start case, which is an entry
  timing bet.
- **`--cash-first` as a distinct mechanism.** Indistinguishable from a constant
  mix at matched weights (§1a). Keep the flag — it costs nothing and the
  question will be asked again — but do not treat the two as alternatives.
- **`g100`.** +2.13%/+2.22% median on the nested set for A/C, ~0 on the
  disjoint set, and the worst maximum drawdown of any composition for B
  (76.5%). Same boundary-optimum shape the 2026-08-11 record declined.
- **A per-profile composition *direction*.** Not supported: the profiles do not
  disagree about which way the dial should turn, only about how hard it bites.
  The one per-profile difference recommended above (`g50` for a lump sum) is
  the seed dial applied, not a separate finding.

---

## 7. Limits

**No out-of-sample test exists and none will.** 2020-2026 was the holdout, was
spent, and is development data now (`reports/holdout-discipline-2026-08-11.md`).
Everything here is consistency across windows, across disjoint episodes and
across nulls. The disjoint sub-period set is a better dispersion measure than
the nested one; it is not a holdout.

**The growth index is the same one the 2026-08-11 record discounted itself for.**
`megacap-index-qqq.json` measures a factor in that factor's best era, and the
whole grid inherits that. A finding that *more* growth is better would be
suspect for exactly this reason; the finding here is "do not move", which is
less exposed to it but not immune.

**Twelve windows over one 28-year path.** The disjoint set is five episodes,
which is five, not fifty. A median of five numbers has a standard error nobody
should ignore, and I have quoted `n+` counts rather than p-values because the
counts are honest and the p-values would not be.

**The 60/40 leg understates real 60/40** (T-bills, not duration).

**The regime battery covers less than the static grid.** It ran on profiles A,
B and C but not the seed dial, and its rules are constant-mix only — a
regime-conditional *buffer* was not tested, because §1a had already shown the
buffer mechanism to be a non-effect at matched weights and there was no reason
to expect switching to change that. If someone wants the regime-conditional
buffer measured, it is a small addition to `research_reserve_regime.py`, not a
new study.

**`reports/regime-history.csv` is not reproducible from a clean checkout**
(§5). Anyone re-running the regime battery elsewhere must restore
`data/regime/hy_oas.csv` first or they will get VIX-only labels for the whole
history with no error.

**One degree of freedom was spent after seeing results:** the disjoint
sub-period set was added mid-study, after the nested eight had been run and the
over-weighting of 2000–02 became visible. The sub-period boundaries are
calendar-round and were not tuned, but they were chosen by someone who had
already seen which era drove the nested result. Recorded because the person
most likely to be misled by omitting it is us, later.

---

## Reproduce

```bash
python scripts/pull_sharadar_bulk.py --skip-download --tables funds
python scripts/research_reserve.py --window 2000-2019
python scripts/research_reserve_regime.py --window 2000-2019
python scripts/research_reserve_report.py
python scripts/research_reserve_regime_report.py
```

One window's grid is ~2-10 minutes; all twelve, four at a time, about 40.
Every run re-verifies itself against the committed CLI artifact before writing.
