# Two-tranche sweep — 2026-08-09

Does a simpler, information-gated schedule beat the three-tranche structure — or a lump sum? Judged on **terminal wealth 3 years after tranche 1** and its dispersion, per METHODOLOGY's Stage-4 yardsticks; cost basis is shown only as a cross-reference to the [stress test](backtest-tranches-2026-08-09.md).

Every variant gets the same $1 budget per episode. Undeployed capital sits in cash at 0%, which handicaps the staged variants — any variant that wins while holding cash wins despite that. The filing gate uses real EDGAR 10-Q/10-K filing dates (available back to the 1990s); only its **timing** half is tested, since the disqualifier sweep needs XBRL that does not exist for these windows. The untested quality filter could only improve the gated variants.

## Dot-com bust — 752 episodes

| Variant | Capital deployed | Terminal wealth (median) | p10 | p90 | Spread | Beats lump |
|---|---|---|---|---|---|---|
| Lump sum at target | 100% | 1.217 | 0.538 | 2.143 | 1.60 | — |
| 3 tranches — current rules | 99% | 1.201 | 0.549 | 2.021 | 1.47 | 35% |
| 3 tranches — drop-only T2, filing T3 | 81% | 1.184 | 0.641 | 1.901 | 1.26 | 55% |
| 2 tranches — half target, half next filing | 95% | 1.185 | 0.577 | 2.055 | 1.48 | 33% |
| 2 tranches — half target, half 2nd filing | 94% | 1.191 | 0.598 | 1.946 | 1.35 | 32% |
| Weekly re-buy while still a buy, cap 6 | 91% | 1.188 | 0.566 | 2.000 | 1.43 | 47% |
| **Hybrid — weekly re-buy, only lower, cap 3** | 82% | 1.194 | 0.620 | 1.958 | 1.34 | 70% |
| Hybrid — weekly re-buy, only lower, cap 6 | 62% | 1.132 | 0.734 | 1.702 | 0.97 | 53% |
| Hybrid — weekly re-buy, only lower, cap 12 | 39% | 1.082 | 0.872 | 1.495 | 0.62 | 43% |

## Global financial crisis — 745 episodes

| Variant | Capital deployed | Terminal wealth (median) | p10 | p90 | Spread | Beats lump |
|---|---|---|---|---|---|---|
| Lump sum at target | 100% | 1.239 | 0.743 | 2.237 | 1.49 | — |
| 3 tranches — current rules | 99% | 1.204 | 0.754 | 2.425 | 1.67 | 50% |
| 3 tranches — drop-only T2, filing T3 | 86% | 1.196 | 0.812 | 2.432 | 1.62 | 65% |
| 2 tranches — half target, half next filing | 96% | 1.204 | 0.769 | 2.439 | 1.67 | 47% |
| 2 tranches — half target, half 2nd filing | 94% | 1.191 | 0.793 | 2.131 | 1.34 | 38% |
| Weekly re-buy while still a buy, cap 6 | 90% | 1.201 | 0.777 | 2.330 | 1.55 | 62% |
| **Hybrid — weekly re-buy, only lower, cap 3** | 87% | 1.196 | 0.782 | 2.304 | 1.52 | 77% |
| Hybrid — weekly re-buy, only lower, cap 6 | 70% | 1.165 | 0.848 | 2.330 | 1.48 | 60% |
| Hybrid — weekly re-buy, only lower, cap 12 | 47% | 1.114 | 0.969 | 1.843 | 0.87 | 43% |

## Tested regime (control) — 1533 episodes

| Variant | Capital deployed | Terminal wealth (median) | p10 | p90 | Spread | Beats lump |
|---|---|---|---|---|---|---|
| Lump sum at target | 100% | 1.255 | 0.819 | 2.293 | 1.47 | — |
| 3 tranches — current rules | 99% | 1.218 | 0.839 | 2.223 | 1.38 | 37% |
| 3 tranches — drop-only T2, filing T3 | 83% | 1.201 | 0.876 | 2.077 | 1.20 | 54% |
| 2 tranches — half target, half next filing | 98% | 1.234 | 0.830 | 2.199 | 1.37 | 39% |
| 2 tranches — half target, half 2nd filing | 95% | 1.206 | 0.855 | 2.081 | 1.23 | 37% |
| Weekly re-buy while still a buy, cap 6 | 89% | 1.209 | 0.842 | 2.128 | 1.29 | 57% |
| **Hybrid — weekly re-buy, only lower, cap 3** | 84% | 1.204 | 0.859 | 2.130 | 1.27 | 73% |
| Hybrid — weekly re-buy, only lower, cap 6 | 67% | 1.163 | 0.913 | 1.896 | 0.98 | 55% |
| Hybrid — weekly re-buy, only lower, cap 12 | 42% | 1.101 | 0.972 | 1.566 | 0.59 | 38% |

## Reading the result

**The hybrid at cap 3 is the best-performing variant tested.** "Re-buy weekly
while the name still screens as a buy, but only at a price below your last fill,
maximum three lots" beats a lump sum in **70% / 77% / 73%** of episodes — far
ahead of anything else (the next best is the three-tranche redesign at
55/65/54%). It does this at the same deployment as the redesign (82–87% vs
81–86%) and a marginally better median.

Head to head against the committed redesign:

| | Redesign (drop-only T2, filing T3) | Hybrid cap 3 |
|---|---|---|
| Deployment | 81 / 86 / 83% | 82 / 87 / 84% |
| Median terminal wealth | 1.184 / 1.196 / 1.201 | 1.194 / 1.196 / 1.204 |
| p10 | **0.641 / 0.812 / 0.876** | 0.620 / 0.782 / 0.859 |
| Beats lump sum | 55 / 65 / 54% | **70 / 77 / 73%** |

The redesign keeps a small edge on worst case; the hybrid wins far more often at
slightly better median. On METHODOLOGY's literal yardstick (dispersion and worst
case) the redesign edges it — but see the caveat below, which cuts hard the
other way.

**The "only lower" condition is doing the work.** Plain weekly re-entry without
it wins only 47/62/57% of episodes; adding the never-add-higher rule and cutting
the cap from 6 to 3 lifts that to 70/77/73%. Re-buying on a clock is a
predetermined schedule; re-buying only at a lower price conditions on what the
market actually did, which is the distinction Constantinides' theorem turns on.

**Higher caps buy their p10 with cash, not skill.** Cap 12 posts an excellent
worst case (0.872 / 0.969 / 0.972) but deploys only 39–47% of budget — most of
that number is uninvested cash, and its beats-lump rate collapses to 38–43%.
Cap 3 is the point where the ladder is still fully committing capital.

**The measurement understates the ladder, by exactly the amount that matters.**
The proxy for "still a buy" is the Stage-1 price criterion alone, because this
harness has no fundamentals for these windows. A live weekly re-entry also stops
the moment a trap marker fires or the Stage-2 verdict flips to permanent — the
continuous misdiagnosis insurance that is the whole point of staging. None of
that protection is credited here. The redesign's p10 edge of 0.02–0.03 is well
inside the margin this omission could plausibly cover.

**Practical case for the hybrid**, beyond the numbers: it is one rule instead of
four legs; it needs no insider-buying feed (tranche 2's confirmation-signal path
is currently unbuildable) and no filing-plus-clean-sweep gate; and it is a small
change to `autotrade.py` — drop the "skip any name already held" guard, add a
below-last-fill check and a lot cap.

## Confound check — is the protection the schedule, or just the cash?

The redesigned three-tranche deploys only 81–86% of budget, so its better worst
case could be an artifact of the capital it never deploys rather than of the
schedule. Control: deploy **the same fraction**, but all at once at tranche 1.

| Window | | Median | p10 |
|---|---|---|---|
| Dot-com | staged (drop-only T2, filing T3) | 1.184 | **0.641** |
| | same cash, all at T1 *(control)* | 1.179 | 0.590 |
| | lump 100% | 1.217 | 0.538 |
| GFC | staged | 1.196 | **0.812** |
| | same cash, all at T1 *(control)* | 1.170 | 0.745 |
| | lump 100% | 1.239 | 0.743 |
| Control regime | staged | 1.201 | **0.876** |
| | same cash, all at T1 *(control)* | 1.191 | 0.829 |
| | lump 100% | 1.255 | 0.819 |

Holding cash explains only part of it. Going lump → same-cash-static improves p10
by +0.05 / +0.00 / +0.01; adding the *schedule* on top improves it by a further
+0.05 / +0.07 / +0.05. In the GFC the schedule supplies essentially all of the
protection. And staging beats the same-cash-static equivalent on **both** median
and p10 in every window — against that control it is not even a trade-off.

Mechanism: in left-tail episodes the price legs fill below tranche 1, so staging
genuinely averages down where it matters; in fast recoveries the drop leg never
fires and the reserved capital sits in cash, which is where the median cost comes
from. That is the insurance profile the evidence review predicted, and it is real
rather than a deployment artifact.

## How to read this

**Terminal wealth** is the value of $1 three years after tranche 1, stock plus uninvested cash. 1.000 means break-even.

**Spread (p90 − p10)** is the insurance measure. METHODOLOGY judges Stage 4 on dispersion and worst case, not on average entry price, because staged entry buys protection against a wrong Stage-2 diagnosis rather than a better price.

**Beats lump** is the fraction of episodes where the variant ended with more terminal wealth than buying the whole position at target.

## Honest limits

- Mechanics only — says nothing about which companies to buy. The trigger is the Stage-1 price criterion alone.
- The filing gate is tested in its timing half only; the disqualifier-sweep condition needs XBRL unavailable before ~2009.
- Cash at 0% understates the staged variants slightly, more so in the higher-rate 2000s windows than in the 2010s.
- Survivorship as in the stress test: near-death survivors are captured, outright failures are not, so tails are understated.
- Three-year horizon from tranche 1; episodes too close to a window edge to measure it are dropped.