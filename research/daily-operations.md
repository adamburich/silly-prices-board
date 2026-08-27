# Daily operations

**Living document** — unlike `production-port-audit.md`, which is frozen at the
port baseline, this changes as the operating loop does. Last audited 2026-08-27.

## The routines

Listed in dependency order, which is not clock order — see "Four schedules, no
pipeline" below for why that is fine.

| routine | where | schedule | trades? | last verified |
|---|---|---|---|---|
| **Refresh HY OAS seed** | `refresh-hy-oas.yml` | daily 12:20 UTC | no | first run 2026-08-27 18:40 UTC, green in 15s (6402 → 7189 rows) |
| **Stage-2 + Stage-5B diagnosis** | cloud routine `trig_016ELzzftkdu6m136QT5BQ37` | daily 07:00 UTC | no | fired 2026-08-27 07:29–07:39 UTC: 8 verdicts + 4 hand targets, pushed to `main` |
| **Paper routines** — *also executes Stage-5 exits* | `paper-routines.yml` | Mondays 21:30 UTC | **YES** | 2/2 scheduled fires green (08-17, 08-24) |
| **Publish board** | `publish-board.yml` | daily 22:30 UTC + push | no | green; ~1m30s on push |
| **Invariants** | `invariants.yml` | every push | no | green, ~18s |

There is no `publish-staging.yml`. Earlier revisions of this table listed one as
"dormant since 2026-08-06"; `git log --all` shows no commit has ever touched that
path in this repo, so the row was wrong rather than stale. Removed 2026-08-27.

## Four schedules, no pipeline

Four routines on four schedules across two platforms reads as sprawl, and the
reflex is to consolidate. Two of the constraints are hard, and one was bought
with a near-miss:

- **Stage 2 cannot run in CI at all.** It runs on the Claude plan; the metered
  API path is the only way to do it in GitHub Actions and `.claude/commands/judgment.md`
  explicitly forbids it. The platform split is a constraint, not drift.
- **Research and trading must stay apart.** See "One writer on the ledger" —
  that edge was removed on 2026-08-15 rather than managed, and merging the jobs
  recreates it.
- **The cadences are tied to constants**, not preference: the ladder is weekly
  because `LOT_SPACING_DAYS = 7`; the refresh is daily against
  `OAS_STALE_DAYS = 14`; verdicts carry 7-day and 90-day cadences.

What makes the sprawl safe is that **this is not a pipeline**. The ordering is
implied by clock arithmetic, but nothing depends on it, because every job
degrades on its own toward inaction:

| if it stops | what happens |
|---|---|
| refresh-hy-oas | ~13 days of slack, then the credit leg drops and the regime line says `VIX-only stress` out loud |
| Stage 2 | no fresh verdicts; they age past `max_age_days` and the ladder buys nothing |
| paper-routines | no fills that week; the ladder is weekly anyway, so nothing is lost |
| publish-board | yesterday's board stays up |

So four schedules do not compound into four ways to break. Where ordering
genuinely matters it is *enforced* rather than timed: `trading_preflight`
refuses to trade on a branch behind upstream.

One reason not to fold the refresh into the publish job specifically:
`publish-board.yml` runs with `contents: read`, and the refresh needs
`contents: write`.

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
notes were keyed to step *numbers* in `.claude/commands/judgment.md` (then named
`stage2.md`), and inserting
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
`.claude/commands/judgment.md`. The queue already decides which names need fresh
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

## The credit leg, fixed 2026-08-27

**It had never run in production.** `fred_api_key` was read only from
`config.local.toml`, which is gitignored — so the developer laptop had it and
nothing else did. The cloud routine and every CI job fell back to the seed at
`data/regime/hy_oas.csv`, which was *also* laptop-only, so they had no credit
data at all and classified on VIX-only stress. Nothing surfaced it because VIX
and OAS agree in a calm market, and the market has been calm. The leg exists to
move *before* VIX in a credit event, so it was missing precisely where it would
have had to fire.

**A second, separate problem: an 892-day hole.** FRED serves only a trailing
three years of `BAMLH0A0HYM2` — its own series note says the change took effect
April 2026. The seed ended 2021-03-19, FRED now starts 2023-08-28, and the span
between is unreachable from that source for anyone. Nobody lapsed; until April
the full history was available on demand, which is why a short seed had been
harmless. `reports/regime-history.csv` carries 122 VIX-ONLY weekly rows as a
result (2021-04-12 … 2023-08-07).

**What was done:**

- `config.py` reads `$FRED_API_KEY` when the file is absent (file still wins).
- The seed is now tracked (`!data/regime/` in `.gitignore`, with the ICE
  internal-use note — this repo is private and must stay so while it carries
  that file).
- `refresh-hy-oas.yml` appends the FRED tail daily. **Append-only**: it aborts
  rather than repairing if the seed is missing, shrinks, or moves backwards.
  A missing seed means a bad checkout, not a seed to recreate — the failure it
  guards against is committing a three-year file over 25 years of history.
- `paper-routines.yml` and `publish-board.yml` carry the key as a *backstop*, so
  a broken refresh cannot silently degrade them.
- The 122-week gap is **carried with a flag, never excluded**, in all three
  consumers. Excluding would not remove the gap — each joins the CSV as a step
  function, so dropping the rows silently carries the 2021-03 label across 2.4
  years. The error is one-directional (`stress = max(vix, oas)`, so a missing
  credit leg can only *under*-state) and points the safe way in each consumer.

**What to watch.** The routine prints its regime line every run. If it ever
reads `VIX-only stress` again, the refresh job has stopped and there are roughly
13 days of slack before anything else notices. That string is the tell; before
2026-08-27 the same condition printed `Market regime: unavailable`, which sent
the reader looking at the API key rather than at the credit leg.

## The queue can no longer be emptied by one name (2026-08-27)

`build_stage2_queue` used to let a prompt-building failure propagate: three
hand-target names (EG, CPT, STZ) raised inside `build_user_prompt`, and the
**whole** queue came back empty from 2026-08-19 to 2026-08-27. The routine
reported the problem accurately every day and could do nothing about it; the
other ~500 names simply stopped being researched, silently.

Failures are now isolated per ticker and emitted as queue items carrying an
`error` key and an empty `prompt`, printed to stderr under "could not have a
prompt built". `.claude/commands/judgment.md` tells the routine these are pipeline
defects rather than research work, and that **reporting them by ticker is the
work** for those names.

**Note the shape of this failure**, because it is the same one as the credit
leg and worth recognising early: a component reported success while producing
nothing, and the only symptom was an absence.

