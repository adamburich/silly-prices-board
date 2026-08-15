# Production port — final audit

**Baseline commit:** `083e631` · **Tag:** `production-port-baseline-2026-08-14`
**Tests:** 200 · **CI:** green (`.github/workflows/invariants.yml`, ~18s, no network, no submodule)
**Manifest:** [`reports/production-baseline.json`](production-baseline.json)

Closes the research-to-implementation handoff. Every release blocker in §11 has
an executable assertion; nothing below rests on inspection alone.

---

## Invariant matrix

### §11.1 — Sleeve invariants

| # | invariant | status | test | implementation |
|---|---|---|---|---|
| 1 | An A purchase never sells B | **PASS** | `test_sleeves.Invariant1_APurchaseNeverSellsB` | `sleeves.open_a_position`, guarded on `b_tickers` |
| 2 | No A purchases ⇒ A sleeve reproduces SPY | **PASS** | `test_sleeves.Invariant2_NoPurchasesReproducesSPY` | `sleeves.contribute` |
| 3 | B path identical in treatment and control | **PASS** | `test_sleeves.Invariant3_…`, `Phase3_BIsTheRealSleeve` | `b_positions` reads through to `b.units` |
| 4 | A cannot create leverage | **PASS** | `test_sleeves.Invariant4_NoLeverage` | `LeverageError` on overdraft; `check_invariants` |

A short reserve raises rather than reaching further — that refusal *is* the
economic content of invariant 1.

### §11.2 — Stage-1 / gate invariants

| # | invariant | status | test | implementation |
|---|---|---|---|---|
| 5 | Weekly/quarterly inputs evaluated contemporaneously | **PASS** | `test_contemporaneous_gates.Invariant5and6_…` | `metrics.trap_flags` (pure, params only) |
| 6 | Annual caches do not freeze weekly/quarterly state | **PASS** | same | live path recomputes every run |
| 7 | Equivalent replay paths give invariant outcomes | **PASS** | `test_contemporaneous_gates.Invariant7_…` | purity; no memoisation |

The defect the handoff describes is real but confined to
`scripts/backtest_universe.py` (`gate_cache` keyed on the newest **annual**
filing while storing Altman-Z and revenue flags). That file is research-only
under §9 and is not ported. A test asserts the string `gate_cache` never appears
in the live modules.

### §11.3 — Stage-2 invariants

| # | invariant | status | test | implementation |
|---|---|---|---|---|
| 8 | No execution without a Stage-2 BUY | **PASS** *(was violated; fixed `634e5b6`)* | `test_verdict_veto.Invariant8_…` | `dashboard.apply_verdict_veto` |
| 9 | REJECT produces zero new fills | **PASS** | `test_behavioral_fixtures.Fixture1_Stage2Reject` | same |
| 10 | Record has timestamp, model/prompt version, evidence, decision | **PASS** | `test_stage2_record.Invariant10_…` | `diagnose.Stage2Finding` + `_stamp` |
| 11 | Model failure ⇒ no new exposure | **PASS** *(was violated; fixed `ef69435`)* | `test_verdict_veto.Invariant11_…` | whitelist `AUTHORIZING_CLASSIFICATIONS` |
| 12 | Authorization persists until trim | **PASS** | `test_evidence_freshness.StalenessNeverCausesASale` | classification never mutated by staleness |
| 13 | Episode reset does not trigger rediagnosis | **PASS** | `test_behavioral_fixtures.Fixture4_…` | `ladder_decision` is verdict-agnostic |

Invariants 8 and 11 were **live defects**, both from the same cause: the veto
was a blacklist that had to enumerate every way a verdict can fail, so a value
added later defaulted to *permitting* a buy. It is now a whitelist —
`{"temporary"}` — and anything unrecognised defaults to refusing.

### §11.4 — Ladder invariants

| # | invariant | status | test | implementation |
|---|---|---|---|---|
| 14 | Max three lots per live episode | **PASS** | `test_ladder_cap.Invariant14_…` | `autotrade.LADDER_CAP = 3` |
| 15 | No fourth lot before an episode reset | **PASS** | `test_ladder_cap.Invariant15_…` | `EPISODE_CLOSE_DAYS = 28` |
| 16 | Each added lot strictly below the prior fill | **PASS** | `test_ladder_cap.Invariant16_…` | `ladder_decision` |
| 17 | Disqualifiers / revalidation run before each add | **PASS** | `test_ladder_revalidation.Invariant17_…` | structural: rows rebuilt + Stage 2 re-run before the ladder |
| 18 | New episode distinguishable from a continuing thesis | **PASS** | `test_ladder_revalidation.Invariant18_…` | `open` vs `add`; `episode_opened` vs verdict `asof` |

Historical evidence for 14–16:
[`cap3-ladder-enforcement-audit.json`](research/mechb/cap3-ladder-enforcement-audit.json)
— **UPHELD** across 856,269 fills and 64,081 episodes, max 3, above-3 zero.

### §11.5 — Exit / accounting invariants

| # | invariant | status | test | implementation |
|---|---|---|---|---|
| 19 | Trims/exits use the frozen live rules | **PASS** | `test_trim.TheFrozenRuleIsTwoLegs` | `trim.py`, `review.sell_signal` |
| 20 | Exited A capital returns to the SPY reserve | **PASS** | `test_behavioral_fixtures.Fixture6_…` | `sleeves.exit_a_position` |
| 21 | Mechanical thesis-break selling not introduced | **PASS** | `test_trim.ExecutionIsFrozenUnauthorized` | `AUTOMATIC_EXECUTION_AUTHORIZED = False` |

**21 of 21 PASS. No FAIL.**

> **Superseded in part, 2026-08-15** — see
> [stage5-autonomy-decision-2026-08-15.md](stage5-autonomy-decision-2026-08-15.md).
> Invariants 19 and 21 still hold, but their evidence has moved:
> * 19 → `test_valuation_exit.TheFrozenRuleIsTwoLegs` +
>   `TheThreeNoAnchorFallbacksAreDistinct`, in `valuation_exit.py` (was `trim.py`).
>   The rule is unchanged; the audit above did not catch that `trim.evaluate`
>   implemented one of its three no-anchor fallbacks.
> * 21 → `test_stage5_authorization`, enforced now by *routing* rather than by
>   withholding execution: thesis and disqualifier warnings go to Stage 5B for
>   judgment and never sell mechanically. `AUTOMATIC_EXECUTION_AUTHORIZED` is
>   `True` as of 2026-08-15 and governs the VALUATION exit only.
>
> One row of this audit is retracted: the precedence it recorded
> (`SELL-REVIEW > REVIEW > TRIM > HOLD`) was not the tested rule. The harness
> emitted valuation and trap exits independently, and under `--trim-only` a fired
> trap flag neither sold nor suppressed a valuation sell.

---

## Safety property beyond §11

Failing closed on Stage 2 blocks **entry only** and can never trap a position
(`test_exit_paths_unblocked`). `review.sell_signal(h, row)` takes no config and
so cannot be gated by one; `broker.py` and `review.py` contain no `stage2_*`
reference; a row the veto rewrote to FLAGGED still classifies for exit rather
than becoming UNTRACKED. **No new exposure, every exit path open.**

## Runtime independence (§9, §16)

Guarded by `test_runtime_independence`, not assumed:

- imports are stdlib + own package only (checked against `sys.stdlib_module_names`);
  the sole non-stdlib exceptions are `paperbroker`, reachable only from
  `broker.py`, and `anthropic` on the opt-in metered path
- no research identifier (`oracle_label`, `tpr`, `tnr`, `thesis_cap3`,
  `rediagnose`, `error_mask`, `W1`/`W2`, …) appears in executable code
- no runtime path reaches `reports/research`, the archived ledgers, or the
  dashboards
- pure decision logic imports without the private submodule; CI checks out no
  submodules and needs no PAT

## Prospective logging (§14)

`scripts/prospective_logging_smoke.py` runs a **paper** candidate end to end and
reconstructs, from the persisted file alone, all seven required facts: what
Stage 1 saw, the exact evidence Stage 2 received, prompt/model/provider identity,
the decision and its instant, whether exposure was authorized, the fill/skip
reason, and the resulting thesis and ladder state. No trade, no live ticker,
nothing written to `data/diagnosis`.

---

## Intentionally provisional

Recorded as observations, **not** defects, and deliberately untouched in this pass.

| behaviour | why it stands |
|---|---|
| **Stage-2 v1 model unselected** | `[stage2]` records what is in force today (`claude-code`/`plan-agent`), not a blessed choice. Selection is a prospective-validation decision (§13.4, §14). |
| ~~**Trim execution unauthorized**~~ | *Reversed 2026-08-15.* "Unattended" was doing the work in that sentence, and it assumed the alternative was a human. It was not — there is no routine human investment-review layer, so withholding execution routed the decision to nobody. §4.6's rejection of mechanical thesis-break selling stands and is now enforced by routing those conditions to Stage 5B. See [stage5-autonomy-decision-2026-08-15.md](stage5-autonomy-decision-2026-08-15.md). |
| **Sleeves near 50/50 vs a 67/33 target** | Converging on contributions alone. A forced sale would be a reallocation decided by a target rather than on merits, and would run FIFO through the 33 preserved paired-VOO lots. |
| **B is legacy QQQ + emerging B25** | Not yet a pure B25 basket. The no-sale migration made the QQQ block unspendable rather than liquidating it. |
| **Six names NO-VALUE after §13.2** | AMP, AON, BA, BRK-B, CBOE, CPAY. Longer history pulls in crisis years and trips the *existing* erraticness guard. The guard was not touched — re-tuning it is the threshold optimisation §7.2 forbids. |
| **`max_age_days` 7 / 90** | Preserved; not optimised in this pass. |
| **cap 3, B25, sell_percentile 0.97** | Carried across unoptimised, with provenance. |

---

## Future-research questions

Not blockers. Each needs a preregistered question, not a tweak.

1. **The erraticness guard vs expanding history.** Six names lost their band
   because a fuller history is genuinely more volatile. Is "no stable normal
   multiple" the right answer for a company whose history spans a crisis, or
   should the guard be sector-aware the way Altman Z now is?
2. **Stage-2 prospective skill.** The historical frontier is a requirements
   analysis, not proof any real model achieves it. The logging is in place; the
   measurement is not yet possible.
3. **REIT dilution.** The net-dilution disqualifier is arguably mis-applied to
   REITs for the same reason Altman Z was — they must distribute ~90% of income
   and therefore fund growth by issuing equity.
4. **Episode-2+ attribution** (§17). Still the open authorization-lifetime
   question; cheap post-processing of the archived 369MB ledger.
5. **Bank valuation lens.** `config.toml` already anticipates a P/B or ROE screen;
   until it exists, banks reach the entry zone on an EV/EBIT percentile that does
   not apply to them (seen live on HBAN).

---

## Status

**The production port is CLOSED.** All 21 invariants pass with executable
assertions, the runtime is independent of the research machinery, and the
prospective-validation record is proven sufficient.

There are **no remaining blockers**. Further work belongs to prospective
operation and validation — running the system forward and measuring it — rather
than to historical architecture.

---

## Change control (from `production-port-baseline-2026-08-14`)

**This tag is a boundary, not just a milestone.** After it, a change to
**investment logic** requires one of exactly two justifications:

1. **a demonstrated correctness defect** — evidence the code does something other
   than what it is specified to do; or
2. **a new preregistered research question** — stated *before* looking at the
   result it would produce.

"It looks like it would do better" is neither.

**Why this rule exists.** The port established that exact point optima do not
generalise — the formal holdout showed poor persistence — and that class-level
economic logic is what carries the signal. Every parameter now in production was
carried across **unoptimised and with provenance**:

| parameter | value | provenance |
|---|---|---|
| ladder cap | 3 per live episode | risk dial, evidenced 3–6 band |
| Mechanism B | top 25, implied-share cap weights | only N with clean identity resolution |
| `sell_percentile` | 0.97 | `sell-threshold-decision-2026-08-11.md`; NOT the grid winner |
| `max_age_days` | 7 / 90 permanent | evidence-freshness gate |
| buy percentile / drawdown trigger | see `production-baseline.json` | frozen |
| sleeve split | 67 / 33 | handoff §3.1 |

Each is a frozen decision with a recorded reason. Re-tuning any of them from a
fresh look at outcomes is precisely the curve-fitting this campaign was spent
learning to avoid, and it would silently un-freeze the baseline.

**What this does not restrict:** bug fixes with a demonstrated defect, test
additions, documentation, behaviour-preserving refactors, and
operational/observability work. Prospective operation and validation is the
intended next phase and needs no special justification.

Ideas that are neither a defect nor a preregistered question belong in
**Future-research questions** above, not in the code.
