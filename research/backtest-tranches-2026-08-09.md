# Stage-4 tranche stress test — 2026-08-09

Do the staged-entry rules deploy capital sensibly in a **real** bear market? The funnel backtest cannot reach one (SEC XBRL has no point-in-time fundamentals before ~2017), so this isolates the entry mechanics, which need prices only.

Trigger: Stage-1 price criterion alone — drawdown >= 20% from the 52-week high, exceeding SPY's drawdown by >= 10%. Rules as they stand: T2 at a close <= 87.5% of T1 **or** 8 weeks without a new low; T3 at a close >= 110% of the running low, >= 4 weeks after T1; all tranches expire 52 weeks after T1. Outcomes measured over 3 years from T1.

## What the decline actually looked like

| Window | Episodes | Median fall from T1 to the low | Median weeks T1 → low |
|---|---|---|---|
| Dot-com bust | 752 | 21% | 24 |
| Global financial crisis | 745 | 30% | 20 |
| Tested regime (control) | 1533 | 19% | 25 |

## Is it actually staging the entry?

If all three tranches fill within a few weeks of each other, staged entry is a slightly delayed lump sum rather than a way to average into a decline.

| Window | Avg tranches filled (of 3) | Median weeks T1 → T2 | Median weeks T1 → T3 |
|---|---|---|---|
| Dot-com bust | 2.98 | 8 | 5 |
| Global financial crisis | 2.97 | 8 | 6 |
| Tested regime (control) | 2.98 | 9 | 7 |

## Did tranching beat buying it all at T1?

Cost basis and lump-sum-at-T1 are both expressed as a median premium over the price at the eventual bottom — lower is better. 'Improvement' is how many percentage points of that premium the tranching saved; negative means tranching paid MORE than a single purchase at T1.

| Window | Cost basis over low | Lump-sum over low | Improvement |
|---|---|---|---|
| Dot-com bust | +32% | +26% | -5.6 pts |
| Global financial crisis | +37% | +43% | +6.6 pts |
| Tested regime (control) | +24% | +23% | -0.4 pts |

## Was T3 a recovery signal or a bear-rally detector?

A T3 fill is a **false confirmation** when price later falls below the low that the 10% bounce was measured from — i.e. the 'recovery' was a rally inside an ongoing decline.

| Window | T3 fired | False confirmations |
|---|---|---|
| Dot-com bust | 99% of episodes | **61%** of T3 fills |
| Global financial crisis | 99% of episodes | **64%** of T3 fills |
| Tested regime (control) | 99% of episodes | **60%** of T3 fills |

## Sensitivity — would different constants have done better?

Swept over the global financial crisis, one parameter at a time from the current rule.

| Variant | Cost basis over low | False confirmations |
|---|---|---|
| current rule | +37% | 64% |
| T2 at −20% (not −12.5%) | +37% | 64% |
| T2 at −30% | +40% | 64% |
| T2 stabilization off | +33% | 64% |
| T3 needs +20% bounce | +39% | 56% |
| T3 needs +30% bounce | +40% | 48% |
| T3 waits 26 weeks | +45% | 48% |
| tranche window 104 weeks | +37% | 64% |
| +30% bounce & 104-week window | +40% | 46% |

## Reading the result

**1. The schedule is front-loaded against the actual shape of a drawdown.** The
median episode bottoms 20–25 weeks after T1, but all three tranches are filled by
week 9 — and T3 (median week 5–7) typically lands *before* T2 (median week 8–9),
which inverts the intended order. In 99% of episodes all three fire, averaging
2.98 of 3. Staged entry as currently parameterized is a lump sum spread over about
two months, deployed well before the typical bottom. This holds in all three
windows, so it is not a bear-market artifact.

**2. Tranching does not reliably beat a single purchase at T1.** It helped in the
GFC (+6.6 points), hurt in the dot-com bust (−5.6 points), and was a wash in the
tested regime (−0.4). One good window out of three is not evidence of a mechanism.

**3. The price-bounce trigger for T3 is a noise detector — and this indicts the
*mechanization*, not the methodology.** A 10% bounce off the running low is a false
signal ~60% of the time in *every* window, including the benign control. The
tested regime never exposed this because prices recovered anyway, so a false
confirmation cost nothing. Note that METHODOLOGY Stage 4 does **not** specify a
price bounce: it says T3 fires on "the first evidence the cause is resolving
(clean quarter, bounded settlement, guidance re-affirmed)". The +10% bounce was a
proxy adopted for the backtest because it is automatable. The result is therefore
evidence *for* the written rule and against the proxy: an automated T3 must key on
a fundamental event, not on price action.

**4. The 8-week stabilization leg of T2 is harmful — with an important caveat on
the metric.** Turning it off improves cost basis from +37% to +33% with no change
in false confirmations, the largest single-parameter gain in the sweep. It fires
during consolidations that precede further declines, exactly the failure it was
meant to avoid.

But **cost basis here is an average over *filled* tranches only** (`backtest_tranches.py`,
`sum(prices) / len(prices)`), so a stricter trigger that simply never fires
improves the number for free — undeployed capital is invisible to it. The sweep
also ran on the GFC alone, the window least sensitive to this, because declines
were deep enough to fill a drop-only trigger anyway. Measured across all three
windows, a drop-only T2 goes unfilled in:

| Window | Avg tranches filled | T2 unfilled |
|---|---|---|
| Dot-com bust | 2.98 → 2.53 | 46% |
| Global financial crisis | 2.97 → 2.64 | **34%** |
| Tested regime (control) | 2.98 → 2.50 | 48% |

So the honest claim is "no harm visible in a GFC-only sweep, on a metric that
cannot see undeployed capital" — not "no offsetting harm". The change is still
right, because buying only at genuinely lower prices is coherent with the
philosophy and dry powder is a feature for a bucket-out investor. But it is a
**deployment-rate decision**, not a free win, and the annual review should track
deployment rate for exactly this reason.

**4b. Scope of the T2 evidence.** METHODOLOGY's leg (b) reads "price
holds/base-builds for several weeks, **or a confirmation signal fires (insider
buying, in-range earnings print)**". This test proxied only the price/time half.
The confirmation-signal half was never exercised and nothing here speaks to it —
deleting the whole leg on this evidence would repeat the proxy-versus-rule
conflation that finding 3 warns about. The supported edit is surgical: remove the
price/time path, retain the confirmation-signal path.

**5. Demanding stronger confirmation is a real trade, not a free win.** Raising the
T3 bounce to +30% cuts false confirmations from 64% to 48%, but *raises* cost basis
from +37% to +40%. Waiting 26 weeks is worse still (+45%). There is no parameter
setting in the sweep that improves both.

### Changes made to Stage 4

- **T2:** removed the price/time path ("price holds/base-builds for several
  weeks") only; the confirmation-signal path (insider buying, in-range earnings
  print) is retained, untested, on reasoning. Recorded the deployment-rate
  consequence: a drop-only T2 lapses in ~34% of deep-bear and ~46–48% of ordinary
  episodes, and the annual review now tracks deployment rate.
- **T3:** price action removed as a trigger. Automated first pass is a new
  quarterly filing with the disqualifier sweep clean — a floor, not the rule,
  since the sweep tests trap markers rather than whether the specific cause
  resolved. Cause-specific resolution remains a human-fired override. **The gate
  is asserted, not backtested**; pre-2017 point-in-time fundamentals do not exist.
- **Metric:** codified that T3 is judged on false-confirmation rate *subject to
  the trigger remaining reachable within the window*, not on cost basis — a
  trigger that never fires scores perfectly and is worthless. A filing-gated T3
  will fill above the T1 price more often than the bounce rule did; that is the
  design working.
- **Not changed — the window.** Extending it from 52 to 104 weeks moved nothing
  (everything already filled by week 9), so the binding constraint was trigger
  sensitivity, not clock length. The two trigger edits push T3 past the ~13-week
  filing floor on their own, which also repairs the T3-before-T2 inversion.

## Honest limits

- **Mechanics only.** This says nothing about which companies to buy. The trigger is the price criterion alone, because the valuation half of Stage 1 needs fundamentals that do not exist for this era.
- **Survivorship, but narrower than it sounds.** The universe is today's index
  members, so the roughly 2–4% of S&P 500 constituents that went to zero in these
  bears (Lehman, Washington Mutual, Bear Stearns, old GM; Enron, WorldCom, Kmart)
  are absent. But the near-death *survivors* are fully captured, and they supply
  the extreme cases: AIG fell 99% further after its trigger (cost basis 154× the
  eventual low), Citigroup 98%, Bank of America 92%, Morgan Stanley 82%, GE 77%.
  Since this test measures a rule's response to a price path rather than a
  company's fate, and since the deepest price paths are already in the sample,
  the missing names would fatten the tail rather than move the medians. The
  false-confirmation rates remain a floor, but by a smaller margin than a bare
  "survivorship bias" caveat would imply.
- **SPY as the sole benchmark.** Sector ETFs mostly postdate 1999, so the idiosyncratic test is coarser than the live screen's.
- Split-adjusted closes, not dividend-adjusted — the same basis the live screen uses. Weekly resolution; intra-week extremes are not seen.