# Pre-integration audit: four claims corrected, one defect confirmed

*2026-08-11. Ten-agent adversarial audit run before integrating any of the day's
work into `main`. Five skeptics instructed to REFUTE the load-bearing claims,
four auditors reading the live path, one synthesis. Read-only throughout.*

Motivation: the day's base rate. Two load-bearing defects found by reading code,
a bug in the index builder that would have shipped as a finding had the numbers
not been identical, an "exposure dial" explanation refuted on first check, and a
pre-registered capital-scale prediction that failed. Integration was imminent.

---

## 1. MY QQQ SPLICE WAS BROKEN, AND I REPORTED THE ARTIFACT AS A RESULT

`data/megacap-index-qqq.json` chained SPY to QQQ at 1999-03-08 with
`lvl *= q / prev`, where `prev` held the last SPY **price level** and `q` the
first QQQ **price level** — a ratio between two instruments, not a return. It
injected a fictional **-45.04%** week, the worst move in the entire series.

Fixed by chaining each segment against its own prior bar and carrying the level
across the join. Worst weekly step is now **-24.62% on 2000-04-10**, the real
Nasdaq crash.

| 1998-2019, QQQ reserve | broken splice | corrected |
|---|---|---|
| 2/3 | +4.08% | **+5.31%** |
| 100% | +3.61% | **+5.20%** |
| starved at 2/3 | 706 | 663 |

2000-2019 and 2010-2019 are UNAFFECTED — their windows start after the splice,
and the blend consumes `mega[i]/mega[i-1]`, so a uniform rescaling of post-splice
levels leaves those ratios identical.

**THE "INTERIOR OPTIMUM" WAS THE ARTIFACT.** It was reported as answering the
standing "boundary optimum, therefore distrust it" objection. Corrected, the
1998 margin is **0.11pp (5.31 vs 5.20), not 0.47pp** — a tie on one path. The
objection STANDS and the claim that data had answered it is withdrawn.

## 2. "ALL THE INCUMBENT'S ADVANTAGE CAME FROM 2020-2022" IS REFUTED

Both 2020-start reports carry a per-year portfolio table on the same path with
identical contributions, so the legs difference directly with no nesting
problem. Incumbent minus frozen, first week of each year:

    2021  +$477 | 2022  -$637 (frozen AHEAD) | 2023 +$2,271
    2024  +$431 | 2025  -$615 (frozen AHEAD) | 2026 +$8,608   final +$15,152

**Through January 2025 the frozen leg was AHEAD by $615.** The entire gap opened
in calendar 2025 (+$9,223) and Jan-Aug 2026 (+$6,544).

The nested caveat was flagged and then not honoured: the 2021-start gap
(+$15,578) is LARGER than the 2020-start gap (+$15,152), and 2021 excludes
calendar 2020 — so COVID contributed nothing, or slightly favoured the frozen
leg. The head-to-head column (-6.44, -8.65, -3.43, +0.08) is non-monotone and
was read as a decay.

**Consequence: the "horizon story" is void.** The account that loosening buys a
one-year rebound given back by year three was built on this reading and is
recorded in `frozen-config-2026-08-11.md` and
`regime-deployment-question-2026-08-11.md`. It may still be true as a cohort
finding; it is NOT what these four windows show, and it must not be cited from
them.

## 3. UNIT ERROR IN THE HEAD-TO-HEAD

`-6.44` is `219952/235104 - 1` — a relative terminal-value shortfall, not a
percentage-point difference of edges. The edge difference is
`3.73 - 10.87 = -7.14pp`. The table places these ratios in a column labelled
"pp" beside genuine "vs EW" percentages, which invites the conflation. Same for
-8.65 (edge diff -9.4pp) and -3.43 (edge diff -3.6pp).

## 4. THE MEGA-CAP CONTROL — THIS FINDING WAS ITSELF WRONG

**CORRECTED same day, and recorded rather than quietly edited.** The auditor
reported the control unverifiable because `ls reports/ | grep -E "cap12-f[456]"`
returned nothing. That is the wrong pattern: `tag_pre` appends `-f{N}` BEFORE
the funding suffixes, so the files are `...-b5s97-f5-dca20k-park-mo-lf1-cap12-`.
They exist and are committed in 1d307e0.

Recomputed from those artifacts, the control **reproduces exactly**:

| window | baseline F>=7 | F>=5 GLOBAL | mega F>=5 only |
|---|---|---|---|
| 2010-2019 | $401,244 | $365,798 (**-8.8%**) | $403,010 (**+0.4%**) |
| 2000-2019 | $1,345,039 | $1,275,457 (**-5.2%**) | $1,381,971 (**+2.7%**) |
| 1998-2019 | $1,648,017 | $1,830,185 (**+11.1%**) | $1,743,873 (**+5.8%**) |

Matching -8.8 / -5.2 / +11.1 and +0.4 / +2.7 / +5.8 to the decimal.

**A verified claim was reported as unverifiable, and I committed that before
checking it.** The lesson is symmetrical with the rest of this audit: a
skeptic's negative finding needs the same verification as the claim it attacks.

### What DOES survive from that finding

The volume-match objection, and it is now quantified. Starved signals:

| window | baseline | F>=5 global | mega F>=5 |
|---|---|---|---|
| 2010-2019 | 178 | 1,133 | 284 |
| 2000-2019 | 715 | 5,934 | 1,508 |
| 1998-2019 | 242 | 5,212 | 609 |

Global F>=5 multiplies starvation by **6-21x**; mega-only by **1.6-2.5x**. The
two arms are not volume-matched, so the control separates "loosen for nearly
everything" from "loosen for a small subset" — it does NOT separate "mega-caps
specifically" from "any small subset". The size-matched null (bar of 5 for a
random 50-name PIT basket) remains un-run, and is the control that would settle
it.

## 4b. (superseded heading kept for the record) THE MEGA-CAP CONTROL HAS NO ARTIFACT

`-8.8 / -5.2 / +11.1` — cited repeatedly as "the control that rules out generic
loosening" and "the strongest evidence the mega-cap rule has" — **exists only as
prose**. A global `--fscore N` run tags `-f{N}`; no such file exists in
`reports/` or anywhere in git history. Unverifiable as it stands.

And the inference overstates even if the numbers are real: the two arms are not
volume-matched (global F>=5 drives starvation 715 -> 5,934; mega-only F>=5 moves
it 715 -> 1,508), so the control separates "loosen for nearly everything" from
"loosen for a small subset" — NOT "mega-caps specifically" from "any small
subset". The size-matched null (bar of 5 for a random 50-name PIT basket) was
never run.

## 5. MEGA-CAP POPULATION MISMATCH, MEASURED

The harness basket is time-varying across 114 snapshots, not a fixed 50:
**185 distinct names** got the lower bar in 1998-2019, 133 in 2000-2019, 89 in
2010-2019. `fscore-bar-and-rationing-2026-08-11.md` says "applied to 52 names" —
wrong for every development window.

Live applies it to a STATIC 50-name list, permanently, with no membership test:

- Only **38 of 50** overlap the tested population.
- **7 can never appear** (TSM, ASML, BABA, HSBC, AZN, NVS, RY) — the harness
  basket is S&P 500 point-in-time, so non-US names are structurally excluded.
- **5 more** (AMD, GEV, LRCX, PLTR, TSLA) enter the basket only AFTER 2019, so
  they are outside every development window the rule was measured on.
- Reconciled 2026-08-11: **38 of 50 (76%)** were ever in the tested population.
- **45% of the measured gain** (90 of 201 extra fills, over 14 names) comes from
  names live will never treat as mega-cap: AIG, MMM, MO, DIS, GILD, QCOM, IBM,
  MDT, BIIB, AMGN, CAH, CL, LOW, PHA.
- **5 of the 50** (HSBC, BABA, NVS, AZN, RY) are not in `[[watchlist]]` at all,
  so `dashboard.py:269` can never match them — dead entries.

## 6. CONFIRMED, AND WORSE THAN STATED: GATED names lose Stage-2

`_should_diagnose` (dashboard.py:556-576) returns
`row.rating in ("BUY","CLOSE","FLAGGED")`. GATED is absent and the NO-VALUE
branch cannot catch it, because a GATED row has `buy_below is not None` by
construction.

**It hits BOTH consumers**, not one: `build_stage2_queue` (the worklist) and
`run_stage2` (the render path). So a GATED name loses a fresh verdict AND the
display of an existing one.

Empirically, on today's board — all six GATED names (ZTS, IT, ACN, ROL, MKC,
TRMB) sit at or below their buy target, so all six would have been BUY/CLOSE and
queued; **all six have Stage-2 notes already written on disk that the board no
longer surfaces.**

Silent by construction: `_run_stage2_queue` prints only the queue contents, and
its empty-queue message reads "every near-buy name has a still-valid cached
verdict" — false when names were excluded by rating.

Provenance: an oversight, not a decision. Commit 50f90dc audited the rating
vocabulary and concluded "the functional paths needed no change — autotrade.py
and review.py both key on rating == 'BUY'". `_should_diagnose` was not examined,
and no decision record argues GATED should be denied Stage-2.

Related, same root: `build_stage2_queue:636` decides prompt framing with
`if r.ticker in held and not _should_diagnose(r, cfg)`, so a held GATED name now
takes the HOLDING_PREAMBLE branch and is told to ignore cheapness framing.

## What survives unchanged

- The reserve run's **primary result** (+3.73% vs EW) and both mechanism checks:
  arithmetic verified, pre-registration ordering confirmed in git (9d79dc6 ->
  a0c0a3b -> f8a9d78, with a0c0a3b containing no deleted lines).
- The **three mega-cap cells** (+0.44 / +2.75 / +5.82) reproduce exactly from
  committed artifacts. It is the control and the population transfer that are in
  question, not the measurement.
- The **QQQ reserve tilt itself**, which strengthened after the splice fix.

## Status

No integration has occurred. The corrections above land before any of it.
