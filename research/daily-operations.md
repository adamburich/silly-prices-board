# Daily operations

**Living document** — unlike `production-port-audit.md`, which is frozen at the
port baseline, this changes as the operating loop does. Last audited 2026-08-15.

## The routines

| routine | where | schedule | trades? | last verified |
|---|---|---|---|---|
| **Stage-2 + Stage-5B diagnosis** | cloud routine `trig_016ELzzftkdu6m136QT5BQ37` | daily 07:00 UTC | no | verified live 2026-08-15 20:41 UTC (`enabled`, next fire 2026-08-16 07:05 UTC); never fired yet |
| **Paper routines** — *also executes Stage-5 exits* | `paper-routines.yml` | Mondays 21:30 UTC | **YES** | first scheduled fire 2026-08-17 |
| **Publish board** | `publish-board.yml` | daily 22:30 UTC + push | no | 8/8 scheduled runs green |
| **Invariants** | `invariants.yml` | every push | no | green, ~18s |
| **Publish STAGING** | `publish-staging.yml` | push to `stage5-review` | no | dormant since 2026-08-06 |

`paper-routines.yml` landed 2026-08-12 (a Wednesday), so **no Monday has passed
yet** — "never ran on schedule" is expected, not a fault.

## The two halves of Stage 5, on two schedules

Stage 5 is split across both routines, and the split is why a Stage-5B `sell`
authorization has no calendar expiry (see
[stage5-authorization-lifecycle-2026-08-15.md](stage5-authorization-lifecycle-2026-08-15.md)):

| | produces | executes |
|---|---|---|
| daily 07:00 UTC | Stage-5B verdicts (`stage5-record` / `stage5-error`) | nothing — the routine never trades |
| Mondays 21:30 UTC | — | `run_stage5_exits`, before the entry ladder |

The decider runs daily and the executor weekly, so an authorization has to
survive up to a week of waiting. `python -m sillyprices review` is the read-only
Stage-5A report and is **not scheduled** — the Monday job recomputes conditions
from fresh rows rather than reading anything `review` wrote, so a stale report
can never cause a trade.

**Routine prompt drift, found and fixed 2026-08-15.** The cloud routine's inline
notes were keyed to step *numbers* in `.claude/commands/stage2.md`, and inserting
Stage 5B as step 6 desynced all of them: its "step 7: SKIP IT" landed on
"rebuild the board" and its "step 8: commit and push" landed on the trading step.
It also never mentioned Stage 5B at all, and its `git add` omitted
`data/exit-diagnosis` and `theses/exit`, so any exit verdict it produced would
have been left uncommitted and lost on the next fresh checkout. The prompt is now
keyed to step **names**, which cannot desync on a renumber.

## One writer on the ledger

Until 2026-08-15 this section documented the loop's sharpest edge: the daily
desktop routine (step 7) and the Monday CI job **both** ran `paper-routine` and
both pushed `portfolio/ledger.jsonl` and `portfolio/ladder-state.json`. Safety
rested on their being ten hours apart and on the earlier one having pushed — a
timing argument, and on 2026-08-15 the desktop push actually landed at 17:21 UTC
rather than ~11:30, leaving four hours rather than ten.

That edge is now **removed rather than managed**. Stage 2 moved to a cloud
routine that does not trade, so `paper-routines.yml` is the only writer of
`portfolio/`. The ladder was always weekly by design (`LOT_SPACING_DAYS = 7`), so
the daily entry pass was never adding a fill the Monday job would not make — it
was only adding a second writer.

The preflight added the same day still stands as defence in depth:
`autotrade.trading_preflight` refuses to trade when `portfolio/` has uncommitted
changes or the branch is behind upstream. A refusal exits 1 and trades nothing,
so the Monday job goes **red** instead of quietly trading on a stale ledger. Dry
runs are exempt. It degrades to "proceed" when git cannot answer (detached HEAD,
no upstream), which is safe because CI checks out the tip into a clean tree.

**Know its blind spot.** A clean checkout at tip is exactly what the preflight
reads as safe, so it could not have caught a *cloud* routine writing fills to an
unmerged branch. That hazard is closed by the routine's own hard rule — it never
runs `paper-routine` and never writes under `portfolio/` — not by the preflight.
If a future routine is ever given trading duties, the preflight will not stop it.

## Trading now depends on research having run

New as of 2026-08-14 (§13.11). Stale Stage-2 evidence no longer authorizes new
exposure, so if the Stage-2 routine stops running, verdicts age past
`max_age_days` and the Monday trading job buys **nothing**.

That is the intended failure direction — toward inaction — but it is a coupling
that did not exist before: the trading job's effectiveness is now a function of
the research job's health. A quiet research outage looks identical to "no
opportunities" unless someone checks.

**What to watch:** names appearing on the board with
`NO NEW EXPOSURE: Stage-2 evidence needs refresh`. A handful is normal churn; a
growing set means the research routine is not keeping up.

## Rehearsal of 2026-08-17 (run 2026-08-15)

| step | outcome |
|---|---|
| preflight | PASS |
| monthly contribution | SKIPPED — day 17, first week only |
| entry ladder | 6 names at BUY, **0 fills** |
| — held back | awaiting lower price: FDS, TYL, UHS, BSX · weekly spacing: CLX, DPZ |
| board / graveyard / ledger gate | all exit 0 |

Zero fills is healthy ladder behaviour, not a funding problem: AUTO holds $201
cash plus a $2,516 SPY reserve against $100 lots.

Note this is the **first automated trade** carrying the reduced BUY universe
(21 → 6), the SPY-only reserve, and the freshness gate.

## Known-stale, resolved 2026-08-15

- `silly-prices-stage2-resume` **deleted**. It was armed for 10:00 UTC today,
  its only action (re-enable an already-enabled task) was a no-op, and all three
  of its briefing points were superseded — including a recommendation to change
  `max_age_days`, which had been explicitly declined.
- The committed `reports/dashboard-latest.json` was refreshed. It had shown
  BUY 21 / CLOSE 7 from before §13.2 and §13.11. The published board was never
  affected (it is regenerated from live data), and nothing in the runtime reads
  the file.

## Stage 2 moved to a cloud routine (2026-08-15)

**One producer, uncapped, research-only.** Routine
`trig_016ELzzftkdu6m136QT5BQ37` ("Silly Prices — Stage-2 diagnosis (sole
producer)"), daily `0 7 * * *` UTC = 03:00 ET, model pinned `claude-opus-5`,
sources `Silly-Prices-Holdings` + `paper-broker`.

Fires at 03:00 ET so an uncapped run cannot drain the 5-hour usage window the
human is about to use, and because nothing else runs between 01:00 and 12:00 UTC
— which also makes the window a clean measurement of this routine alone. Note
the cron is UTC, so it becomes 02:00 local when DST ends (2026-11-01).

Three producers were retired for it:

| retired | was | why |
|---|---|---|
| desktop task `silly-prices-stage2` | daily 07:04 local, **traded** | deleted; SKILL.md kept for recovery |
| `trig_019xmPCeQh15KsfEUvztcjyQ` | daily 11:00 UTC | disabled — duplicate |
| `trig_01F9q8oiBSkCP1uGnLdd3u4q` | daily 21:30 UTC | disabled — duplicate, and collided with the Monday CI slot |

**What was actually wrong.** The two cloud routines pushed to `claude/*` branches
(19 of them on origin, all unmerged), so their verdicts never reached `main` or
the ladder. Because `stage2-queue` is deterministic and ordered nearest-to-buy
first, the next producer then rebuilt the *identical* queue — on 2026-08-15 the
07:12 UTC cloud run and a 17:15 UTC local run diagnosed the same ten tickers.
Both routines had also failed outright on 2026-08-14 with a seven-day rate-limit
rejection; running two Opus passes a day over one job is what exhausted it.

**Two things the new routine fixes structurally**, not by instruction: it pushes
`HEAD:main` so work lands where the ladder reads it, and it carries
`adamburich/paper-broker` in `sources` — the private submodule that
`sillyprices/broker.py` imports and that `stage2-queue` itself needs, which the
old routines had to bootstrap mid-run.

**Uncapped.** The 10-verdict and 3-hand-target per-run caps are gone from
`.claude/commands/stage2.md`. The queue already decides which names need fresh
research; a cap on top of it only manufactured stale unresolved work (75 names
were queued on 2026-08-15 against a cap of 10). Evidence standards are unchanged
— each name is researched and recorded individually, and a long queue is worked
over more runs rather than to a weaker standard. Commits go in batches of ~10 for
durability, so a rate limit costs the current batch and not the run.

**Known gap, unchanged.** `[stage2] model = "plan-agent"` still stamps every
verdict regardless of which model produced it, so the §14 record cannot group
decisions by decider. Pinning the routine's model narrows the ambiguity to one
system going forward. Settling the config value is a prospective-validation
decision (§13.4) and deliberately not made here.

### Deliberately running without a budget stop — watch this

Measured 2026-08-15: **one name costs ~99.6k tokens** (19 tool calls, 5m39s),
confirming the ~94k sequential figure from 08-14. Against a 5-hour window of
roughly 3.3–4.0M tokens that is **~2.5–3% per name, so ~33–40 names per window**
— tokens bind well before wall-clock does.

So an uncapped run consumes most of a window in one sitting. Today's queue was
64 Stage-2 names (25 valued, of which 3 at/below target; 39 with no buy target
at all) plus 102 hand targets.

A budget-aware stopping rule was proposed and **deliberately declined** in favour
of observing one real uncapped run first. The failure mode to watch for: if the
week's budget is exhausted mid-week, a run is rejected outright in ~1 second (as
both old routines were on 08-14), verdicts age past `max_age_days`, and **the
Monday ladder buys nothing.** That fails toward inaction, which is safe, but it
is silent — the tell is the same one in "Trading now depends on research having
run": board rows reading `NO NEW EXPOSURE: Stage-2 evidence needs refresh`.
