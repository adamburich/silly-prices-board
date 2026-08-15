# Stage-5 autonomy correction — architecture audit, 2026-08-15

**Status: ACTED ON.** The refactor described in Part 3 was implemented the same
day — see [stage5-autonomy-decision-2026-08-15.md](stage5-autonomy-decision-2026-08-15.md)
for the decision record, including the two questions this audit left open
(precedence, full-vs-partial) and how they were settled. This document is kept
as the pre-change survey; the line numbers below refer to the code as it stood
before, and `trim.py` has since been renamed `valuation_exit.py`.

This records (a) what the historically tested TRIM rule actually did, and (b)
every place the implementation still encoded "Stage 5 terminates in human
review". The directive was to refactor the sell side into Stage 5A
(deterministic monitoring) → Stage 5B (LLM exit diagnosis) → automated
execution, with no normal path ending at a human.

---

## Part 1 — TRIM correspondence audit

The question: does the frozen production TRIM condition exactly match a
historically tested mechanical exit rule? If yes, preserve the automatic rule
rather than inserting an untested LLM veto for architectural symmetry.

**Answer: yes, with two exceptions that must be resolved before execution is
authorized.**

### What the harness actually tested

`scripts/backtest_universe.py`, exit reason `"trim"` (emitted at
[backtest_universe.py:453-490](../scripts/backtest_universe.py:453)), selected by
`--trim-only` (`sell_reasons = frozenset({"trim"})`,
[:1629-1634](../scripts/backtest_universe.py:1629)).

**1. Full liquidation, not a partial trim.**
[portfolio() :848-857](../scripts/backtest_universe.py:848) does
`n = shares.pop(t, 0.0)` — the entire position in that ticker is sold, every
lot, and `state.pop(t)` discards the episode so the ladder re-arms from scratch.
`reports/backtest-synthesis-2026-08-10.md:130` states this explicitly:
*"full-position sells were what we simulated (live 'trim a slice' is gentler
than anything tested)."* **The name "TRIM" is a misnomer for the tested rule.**

**2. Two legs, as documented.**
- Leg 1: current multiple ≥ `cfg.sell_percentile` of the name's own full band
  (EV/EBIT; P/B for financials).
- Leg 2: market cap ≥ `optimistic_ceiling(build_inputs(...))` — the optimistic
  owner-earnings DCF, computed as a total-dollar ceiling.

**3. The no-DCF fallback is THREE cases, not one.** This matters because
`trim.py`'s docstring collapses them into "history leg alone", which is only
true of the first:

| Case | Condition | Tested behavior |
|---|---|---|
| Thin history / no mcap | `len(recs) < 2` or `mc_now is None` | `trim = True` — **history leg alone**. The ADR/financial/erratic-EBIT lane. |
| Owner earnings ≤ 0 | `optimistic_ceiling()` returns None | **NOT history alone.** Requires both parameter-free ratchets: mcap > all-time filed max AND price ≥ prior all-time-high close ([:474-475](../scripts/backtest_universe.py:474)). |
| No EV/EBIT at all (`fb_top`) | negative EBIT, positive revenue | EV/S at the sell percentile AND both ratchets ([:433-452](../scripts/backtest_universe.py:433)). |

`review.py` mirrors all three correctly — `no_oe_anchor` / `record_line`
([review.py:161-186](../sillyprices/review.py:161)) and
`sell_basis == "no-EBIT"` ([:139-148](../sillyprices/review.py:139)). The
harness comment at [:455-457](../scripts/backtest_universe.py:455) says
"mirrors review.py exactly", and it does.

**→ Defect: [trim.py:11-14](../sillyprices/trim.py:11) understates the rule.**
The file that claims to be the single authoritative statement of the policy
describes only the first fallback case. `trim.evaluate()` implements only that
case too ([:113-119](../sillyprices/trim.py:113)) — it has no `no_oe_anchor`
parameter, so calling it on an owner-earnings-≤0 name returns TRIM where both
the harness and `review.py` return HOLD. `evaluate()` is currently used only by
`tests/test_behavioral_fixtures.py`, so this is latent, not live.

**4. Precedence relative to thesis/disqualifier signals: the tested rule had
none.** In the harness, `trim` and `trap` are independent exit reasons emitted
in the same weekly loop. Under `--trim-only`, **trap flags neither suppress a
trim nor cause a sell.** The validated configuration is: *TRIM fires on
valuation; fired disqualifiers do nothing.*

Live `review.py` inverts this:

```python
if row.trap_flags:
    return "REVIEW", [...]        # line 125 — BEFORE the TRIM test
```

A name with any fired disqualifier can never reach the TRIM branch, and
`tests/test_exit_paths_unblocked.PrecedenceIsPreserved.test_disqualifier_outranks_overvaluation`
locks that in. **This is the one place where the live rule is not the tested
rule**, and it is exactly the place the 5A/5B refactor most needs to get right:
under the proposed design, a trap-flagged expensive name would route to the LLM,
and an LLM `hold` would suppress a mechanical exit the harness validated firing.

**5. 56-day cooldown per exit reason.** `emit()`
([:410-416](../scripts/backtest_universe.py:410)) refuses to re-emit the same
reason within 56 days. `review.py` is a stateless daily flagger with no
analogue — harmless while it only prints, mandatory the moment it executes, or a
name parked above its line emits an exit every single day.

**6. Threshold.** `config.toml:69` `sell_percentile = 0.97` matches
`trim.py`'s frozen 0.97. Tested at 0.95 / 0.97 / 0.99, all positive; per
`reports/sell-threshold-decision-2026-08-11.md` the choice inside [0.95, 0.99]
is second-order (0.64pp in the adopted funding regime).

### Why the mechanical/LLM split the directive proposes is the right one

`reports/backtest-synthesis-2026-08-10.md:49-64` is the load-bearing evidence
and it supports the split exactly as stated:

- **Finding 2:** *"Mechanical thesis-break selling destroys value, always."*
  Every policy containing trap-flag exits lost in every configuration
  ($373–434k vs $579k hold). Mechanism understood: recession filings make
  quality names look statistically broken, so mechanical fundamentals exits sell
  the 2001/2009 lows. **"Trap exits are human-judgment territory, permanently."**
- **Finding 3:** *"If you sell mechanically at all, sell rarely and on
  valuation-plus-DCF."* The TRIM class beat the hold class in both holdout
  windows; the two-leg design is what makes rare TRIM safe on compounders.

So: mechanical valuation exit = tested and kept automatic. Business/thesis exit
= mechanically refuted, therefore routed to judgment. The directive's
substitution of *LLM judgment* for *human judgment* in Finding 2 is the actual
architectural claim being made here — the harness measured that mechanical
rules fail, not that a human specifically is required.

### Two things to settle before execution is authorized

1. **Full exit or partial?** The tested rule liquidates the whole position. If
   automated execution sells a slice, it is executing something no backtest
   covers. Either execute the full exit (tested) or record the partial as a new,
   untested behavior.
2. **The trap-flag precedence inversion.** Under the tested rule, a fired
   disqualifier does not block a valuation exit. Preserving `REVIEW` ahead of
   `TRIM` and letting 5B's `hold` win would create a suppression path that never
   existed in the harness.

### Change-control note

`AUTOMATIC_EXECUTION_AUTHORIZED = False` is a deliberate, dated, test-enforced
decision (2026-08-14) sitting inside the `production-port-baseline-2026-08-14`
change-control boundary, and `reports/production-port-audit.md:76` lists it as
audit invariant 21. Flipping it is an architectural decision, not a bug fix, and
warrants its own decision record.

---

## Part 2 — Every place the obsolete architecture is still encoded

### A. Behavioral code (changes what the system does)

| # | Location | What it encodes |
|---|---|---|
| A1 | [review.py:101-196](../sillyprices/review.py:101) `sell_signal()` | Returns terminal strings `SELL-REVIEW` / `REVIEW`. No exit-diagnosis call, no persistence, no action authorization. This is the whole 5A/5B seam, absent. |
| A2 | [review.py:125-127](../sillyprices/review.py:125) | Trap flags short-circuit to `REVIEW` *before* the TRIM test — the precedence inversion vs the tested rule (Part 1 §4). |
| A3 | [review.py:58](../sillyprices/review.py:58) | `Holding.verdict` domain is `HOLD \| TRIM \| REVIEW \| SELL-REVIEW \| UNTRACKED` — no `sell` / `hold` / `uncertain` / `error`. |
| A4 | [trim.py:66](../sillyprices/trim.py:66) | `AUTOMATIC_EXECUTION_AUTHORIZED = False`; `TrimPolicy.enabled` defaults False; `evaluate()` returns HOLD + "automatic valuation trim is disabled". |
| A5 | [trim.py:69](../sillyprices/trim.py:69) | `PRECEDENCE = ("SELL-REVIEW", "REVIEW", "TRIM", "HOLD")` — the review-terminal states *are* the frozen precedence constant. |
| A6 | [trim.py:113-119](../sillyprices/trim.py:113) | `evaluate()` implements only one of three no-DCF cases (Part 1 §3 defect). |
| A7 | **Absent: exit-diagnosis persistence.** `data/diagnosis/` holds only Stage-2 *entry* verdicts. No `Stage5Finding`, no exit prompt, no exit prompt SHA, no triggering-condition or action-authorization field. | The pattern to copy is `diagnose._stamp` + `_write_cache` ([diagnose.py:387-442](../sillyprices/diagnose.py:387)), which already carries provider / model / prompt_sha / decided_at / evidence / params. |
| A8 | **Absent: CLI surface.** [__main__.py](../sillyprices/__main__.py) has `stage2-queue`/`stage2-record` and `target-queue`/`target-record`, but no `stage5-queue`/`stage5-record`. | The routine has no way to feed an exit verdict back. |
| A9 | [dashboard.py:589-625, 645-710](../sillyprices/dashboard.py:589) | Held names ride the **Stage-2 entry queue** with `HOLDING_PREAMBLE`. The classification enum stays `temporary\|permanent\|uncertain`, the recommendation enum stays `proceed to Stage 3 \| reject (value trap) \| human review`, and the verdict is stamped with the *entry* prompt SHA. There is no exit decision, no triggering condition, no action authorization. |
| A10 | [diagnose.py:85, 151, 259](../sillyprices/diagnose.py:85) | `"human review"` is a first-class value in the Stage-2 output schema, the errored-finding default, and the missing-field default. Fail-closed *for entry* this is correct; the exit path needs `uncertain → retain, no sell, requeue` instead. |
| A11 | [autotrade.py:21-22](../sillyprices/autotrade.py:21) | *"No auto-sells — Stage 5 is human; exits go through `broker sell` after a `review` flag."* Describes a real absence of wiring, not just a comment. |
| A12 | [review.py:265-268](../sillyprices/review.py:265) | Reallocation sums `TRIM`/`SELL-REVIEW` market value as capital "a human would free". |
| A13 | [dashboard.py:864-867](../sillyprices/dashboard.py:864) | Board veto strings: *"human review before any buy"*. Entry side — arguably legitimate, but same vocabulary. |

### B. Tests that lock the obsolete architecture in place

| # | Location | Assertion |
|---|---|---|
| B1 | [test_trim.py:83-88](../tests/test_trim.py:83) | `AUTOMATIC_EXECUTION_AUTHORIZED is False`, and `TrimPolicy().enabled == it`. |
| B2 | [test_exit_paths_unblocked.py:132-138](../tests/test_exit_paths_unblocked.py:132) | `test_disqualifier_outranks_overvaluation` — pins the precedence inversion. |
| B3 | [test_exit_paths_unblocked.py:68-71](../tests/test_exit_paths_unblocked.py:68) | `sell_signal`'s signature must be exactly `(h, row)` — **a 5B implementation needing provider/model config will trip this.** The invariant behind it is good (exits must not be gated by Stage-2 config) and must be preserved differently: 5A stays config-free; 5B may take config but must fail to `uncertain → retain`. |
| B4 | [test_exit_paths_unblocked.py:73-82](../tests/test_exit_paths_unblocked.py:73) | Asserts `SELL-REVIEW` / `REVIEW` are the returned verdicts by name. |
| B5 | [test_behavioral_fixtures.py:159-178](../tests/test_behavioral_fixtures.py:159) | `Fixture5_Trim` — *"fires as a REVIEW SIGNAL; execution stays unauthorized."* |

### C. Documentation

| # | Location | Text |
|---|---|---|
| C1 | [METHODOLOGY.md:201-202](../METHODOLOGY.md:201) | *"a first pass for review, not the final call — the five-question judgment and the decision to commit capital remain yours… a 'human review' verdict means do the work by hand."* |
| C2 | [METHODOLOGY.md:338-361](../METHODOLOGY.md:338) | Stage 5 proper. **Neutral on who decides** — states the four sell conditions without naming a decider. No change strictly required; it is the doc the refactor should key off. |
| C3 | [docs/PIPELINE.md:142-158](../docs/PIPELINE.md:142) | *"automated as a first pass"*, *"sizing stays human"*, precedence chain named as `SELL-REVIEW → REVIEW → TRIM → HOLD`. |
| C4 | [docs/PIPELINE.md:167-170](../docs/PIPELINE.md:167) | *"auto-generated first pass for your review… never the final call"*; recommendation enum incl. `human review`. |
| C5 | [README.md:66-69](../README.md:66) | *"Flags for human review — nothing is executed."* |
| C6 | [review.py:1-26](../sillyprices/review.py:1) | Module docstring: *"first pass for human review — never the final call"*, *"sizing is the human's"*. |
| C7 | [review.py:311-313](../sillyprices/review.py:311) | Generated report header: *"Verdicts flag for review; they do not execute anything."* Written into every `review-DATE.md`. |
| C8 | [review.py:339](../sillyprices/review.py:339) | *"Sizing is a human decision."* |
| C9 | [review.py:136-138, 144-147, 182-184](../sillyprices/review.py:136) | *"that judgment is the reviewer's"*, *"JUDGMENT REQUIRED"* ×2 — reason strings emitted to the report. These are exactly the conditions 5B should diagnose. |
| C10 | [trim.py:1-56](../sillyprices/trim.py:1) | The entire docstring, especially :29 *"FROZEN — REVIEW SIGNAL ONLY. AUTOMATIC EXECUTION IS NOT AUTHORIZED"* and :36-48 (the two-claims argument). |
| C11 | [diagnose.py:11-12](../sillyprices/diagnose.py:11) | *"AUTO-GENERATED FIRST PASS for human review… never the final call."* |
| C12 | [diagnose.py:524](../sillyprices/diagnose.py:524) | Note template: *"review before acting; this is not a final human judgment."* |
| C13 | [targets.py:51, 128](../sillyprices/targets.py:51) | *"a first pass for human review, never a final call"* (hand-target lane — adjacent, same doctrine). |
| C14 | [screen.py:7, 74](../sillyprices/screen.py:7) | *"(human diagnosis) still owns the final call either way"*; *"flags for manual review"*. |
| C15 | [.claude/commands/stage2.md:64, 111, 119, 167, 180](../.claude/commands/stage2.md:64) | *"record it `uncertain` / `human review`, which fails safe"*; *"a human may still run it"*; *"funding is the human's call"*; *"a human should do the work by hand"*; the recommendation enum. |
| C16 | [config.toml:83, 219, 259](../config.toml:83) | *"no auto-sells"*; *"Promoting a confirmed trap to AVOID is still the human's call"*; *"a human judgment call, edit freely"*. |
| C17 | [test-inventory.md:105](../test-inventory.md:105) | H2 Stage-5 review — *"flags only, executes nothing"*. |
| C18 | [reports/production-port-audit.md:76, 122](production-port-audit.md:76) | Invariant 21 *"Mechanical thesis-break selling not introduced"*; *"Trim execution unauthorized"*. |

### D. UI labels

| # | Location | Text |
|---|---|---|
| D1 | [board.py:608](../sillyprices/board.py:608) | `review.html` masthead: *"A verdict is a flag for human review, never an executed decision."* |
| D2 | [board.py:429](../sillyprices/board.py:429) | *"Auto-generated first pass — validate against your own external sources before acting."* |
| D3 | [board.py:135](../sillyprices/board.py:135) | *"human review happens against EXTERNAL tools"*. |
| D4 | [board.py:422](../sillyprices/board.py:422) | `<summary>` label: *"Full first-pass verdict — the case under review"*. |
| D5 | [board.py:224, 295-297](../sillyprices/board.py:224) | FLAGGED chip: *"the buy is vetoed pending human review"* (entry side). |
| D6 | [board.py:597](../sillyprices/board.py:597) | `flagged` counter lumps `TRIM`/`REVIEW`/`SELL-REVIEW` as one "flagged" class. |
| D7 | [review.py:357-368](../sillyprices/review.py:357) | Console table `Verdict` column renders the review-terminal strings. |

### E. Out of scope — do not rewrite

`reports/review-2026-08-07.md`, `-08-10.md`, `-08-11.md` and every dated
decision record (`backtest-*`, `sell-threshold-decision-*`,
`stage1-stage5-thresholds-decision-*`, `production-port-audit.md`) are journal
entries recording what was true when written. They stay. The one exception worth
noting is `reports/daily-operations.md:14`, which references a dormant
`stage5-review` git branch — naming only, no behavior.

---

## Part 3 — Proposed target shape

Derived from the correspondence audit, not from symmetry.

**Stage 5A — deterministic monitoring** (`review.sell_signal`, config-free,
keeps invariant B3's spirit). Emits *conditions*, not verdicts:

| Condition | Source | Routes to |
|---|---|---|
| `valuation_exit` | The frozen two-leg rule + all three fallback cases | **Automatic execution.** Tested; no LLM veto. |
| `thesis_warning` | Stage-2 `permanent`, or `rating == AVOID` | 5B |
| `disqualifier_warning` | `trap_flags` non-empty | 5B |
| `no_anchor` / clear | — | HOLD |

**Precedence question to settle:** the tested rule ran `valuation_exit`
independent of trap state. Recommend: `valuation_exit` executes regardless of
any concurrent warning (matching the harness), and warnings route to 5B in
parallel rather than pre-empting it. This *reverses* the current live precedence
and requires B2 to change — flagging it because it is the substantive
behavioral decision in this refactor, not a mechanical one.

**Stage 5B — LLM exit diagnosis.** New module (`exit_diagnosis.py`), its own
system prompt and therefore its own `EXIT_PROMPT_SHA` — must not reuse
`diagnose.SYSTEM_PROMPT_SHA`, whose whole purpose is grouping decisions by the
instructions that produced them. Decision enum `sell | hold | uncertain`, plus
`error` on failure. Persisted record (mirroring `Stage2Finding`) carrying:
provider, model, exit prompt SHA, `decided_at`, the exact evidence supplied,
triggering condition, classification, reasoning, action authorization.

**Fail-closed semantics** as specified: `sell` → authorize exit; `hold` →
retain; `uncertain` / `error` → retain, authorize no sell, requeue. An uncertain
or failed call must never force liquidation. Note this is the *opposite*
polarity from the entry-side fail-closed rule (`test_exit_paths_unblocked`'s
"no new exposure, exits stay open") — the exit path fails toward *inaction*,
the entry path fails toward *no exposure*. Both are "do nothing new", stated
from opposite sides; worth writing down so a future reader doesn't "fix" one to
match the other.

**Execution gaps to close before wiring:** the 56-day per-name cooldown
(currently harness-only), full-vs-partial liquidation semantics, and a
`broker.sell` path invoked from the routine (`autotrade.py` currently has no
sell wiring at all beyond reserve-leg rebalancing).
