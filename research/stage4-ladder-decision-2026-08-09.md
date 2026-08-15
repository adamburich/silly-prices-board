# Stage 4 decision record: from tranches to the entry ladder — 2026-08-09

Two days of measurement replaced Stage 4's three-tranche entry with a single
rule. This note records what was tested, what each test showed, the decision,
and what remains open — so the next review argues with the evidence rather than
re-deriving it.

## The decision

> **While the name is rated BUY, buy one lot per week — but only at a price
> strictly below the last fill, and never more than 3 lots per episode.**

An episode closes after ~4 consecutive weeks out of BUY; a later re-entry is a
new episode with a fresh Stage-2 verdict (pipeline-enforced) and a fresh 3-lot
budget. No calendar limit. The Stage-4 position cap (~5% at cost), not the
ladder, bounds total per-name exposure. Judged on terminal wealth, dispersion,
and the paired-benchmark difference — never IRR or cost basis.

## How we got here

| Step | Test | Finding |
|---|---|---|
| 1 | [Tranche stress test](backtest-tranches-2026-08-09.md) — T1/T2/T3 through the dot-com bust and GFC, 3,030 episodes | The rules weren't staging: all three tranches filled by week 9 vs bottoms at week 20–25, T3 before T2. The +10% bounce trigger for T3 was a false signal ~60% of the time **in every regime**. Tranching beat a lump sum in only 1 of 3 windows |
| 2 | [Evidence review](research-tranching-evidence-2026-08-09.md) — external literature | No credible raw-return edge for staging anywhere (Constantinides 1979, Vanguard). The right frame is **misdiagnosis insurance**, judged on dispersion/worst-case. Constantinides condemns predetermined schedules, endorses information-conditioned ones. Never judge by IRR (Hayley 2010) |
| 3 | [Structure sweep](backtest-two-tranche-2026-08-09.md) — lump vs 3-tranche variants vs 2-tranche, terminal wealth | Redesigned tranches (drop-only T2, filing T3) beat lump in 55–65% of episodes; two-tranche variants dominated. Confound check: the protection is the *schedule*, not the undeployed cash |
| 4 | Ladder variants added to the sweep | **"Weekly re-buy, only lower, cap 3" beat a lump sum in 70–77% of episodes** — the best of nine structures. The only-lower condition supplies the edge (plain weekly: 47–62%) |
| 5 | [Cap sweep](backtest-ladder-caps-2026-08-09.md) — fixed lots, cap untied from lot size | No per-lot return decay to lot 8 in a survivor universe; the cap is a **risk dial**, not a return optimum. Uncapped is ruled out: fill counts reach 20+ in grinds, and the one number favouring uncapped is survivorship-manufactured. Evidenced band: 3–6 |
| 6 | [Full-funnel run](backtest-funnel-2026-08-09.md) — ladder with real fundamentals and the weekly disqualifier re-check | The only tested entry whose **median fill beats the paired index** (1y +1.9% vs −1.9% for tranches; mean +7.5% vs +1.8%), with ~18% less capital deployed. Money-weighted curve vs paired VOO: a wash with tranches (~1.14×) — the edge is in the typical fill, not the tail |

## What the evidence settled

- **Staging costs the median and buys the tail.** A lump sum wins the median
  episode in every regime tested, on every metric. That cost is the insurance
  premium, accepted deliberately. The protection is real and confound-checked
  (schedule, not cash).
- **Price action is never a trigger.** The bounce-confirmation and quiet-period
  legs of the old design were noise detectors — ~60% false-signal rates,
  regime-independent. Every surviving mechanism conditions on *information*: a
  lower offered price plus a clean sweep against the newest filing.
- **The weekly BUY re-check is the point.** Discrete tranches validated the
  thesis at their trigger moments; the ladder validates the entire funnel —
  zone, sweep, verdict — before every dollar. This is the misdiagnosis
  insurance the methodology's first principle demands, now in the entry rule.
- **Simplicity was free.** One rule replaced four trigger legs, two of which
  were unbuildable (insider-buying feed; filing-plus-sweep gate). The ladder
  needs nothing the pipeline doesn't already compute weekly.

## Errors caught by measuring (kept for calibration)

Three confident predictions were overturned by their own tests: that the
insurance logic undercut T2's drop leg (removing it degraded worst-case in
every window); that a two-tranche design would win (dominated); that late
ladder lots would decay via adverse selection (flat to lot 8 in-sample). One
designed metric was tautological (an only-lower ladder always beats its
matched lump). The 52-week episode window was exposed as a measurement
artifact, not a strategy rule. Conclusion drawn: entry-mechanics intuition,
including the reviewer's, loses to the harness — sweep before adopting.

## Changes made

- **METHODOLOGY Stage 4 rewritten** around the ladder: rule, clause rationale,
  episode semantics, cap-as-risk-dial (3 chosen, 3–6 evidenced), lapse and
  deployment-rate tracking, judging standard, evidence links and status.
- **Annual review yardsticks updated**: deployment rate, halt causes
  (disqualifier/verdict halts vs no-lower-price), insurance ledger; IRR
  prohibition retained.
- Funnel diagram: Stage 4 annotation now "entry ladder".

## Open items

- ~~`autotrade.py` still enters lot 1 only~~ **Done (same day):** the routine
  now runs the ladder. Pure decision function (`ladder_decision`) with unit
  coverage of every branch; episode state in `portfolio/ladder-state.json`
  (committed, like the ledger); pre-ladder holdings migrate as live episodes
  seeded from their ledger lots; `paper-routine --dry-run` previews. First
  live dry run: 33 BUYs, 0 fills — 15 awaiting a lower price, 18 inside
  weekly spacing — i.e. the ladder held back everything the old routine had
  already bought, exactly as designed.
- **Bear-market validation of the fundamentals re-check** is impossible with
  free data (no point-in-time XBRL before ~2011); the pre-2017 evidence is
  price-only. The vendor purchase (point-in-time + delisted names) would
  settle it, and would also settle cap 3 vs 6 via zero-tail frequency.
- **The forward record is the real test.** The paper portfolio's paired-VOO
  design is immune to the scale-in IRR bias by construction; deployment rate
  and halt causes should be added to the review output once the ladder is
  live.
