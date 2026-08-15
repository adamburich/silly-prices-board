# Stage 5 becomes autonomous — decision record, 2026-08-15

Supersedes the 2026-08-14 freeze recorded in `sillyprices/trim.py` and
`reports/production-port-audit.md` (invariant 21, "Trim execution unauthorized").
Audit that preceded it: [stage5-autonomy-audit-2026-08-15.md](stage5-autonomy-audit-2026-08-15.md).

This is a **change-control event** against `production-port-baseline-2026-08-14`.
It is not a defect fix and not a preregistered question; it is an architectural
correction, decided by Adam, and it changes investment behaviour. Recorded in
full so it is reviewable on its merits rather than reconstructible from a diff.

## What was wrong

The sell side terminated in `REVIEW` / `SELL-REVIEW` — states meaning "a human
decides from here". The intended architecture has no routine human investment
review; human involvement is operational and oversight only. So those states did
not route decisions to a better decider. They routed them to nobody, and a sell
rule that never fires is a hold rule.

The 2026-08-14 freeze rested on a distinction that looked like caution and was
actually an unstated assumption:

> 1. this reading is worth a human's attention — evidenced;
> 2. this reading is worth selling on, unattended — NOT evidenced.

"Unattended" was doing the work. Read without the assumption that a human was
the alternative, the evidence for (2) is the same evidence as for (1):
`backtest-synthesis-2026-08-10.md` finding 3 measured the TRIM class beating the
hold class in **both** holdout windows. That is a measurement of the rule
executing, not of the rule being read.

## What changed

| | Before | After |
|---|---|---|
| Module | `trim.py` | `valuation_exit.py` — the tested behaviour is a full exit, not a trim |
| Verdicts | `HOLD · TRIM · REVIEW · SELL-REVIEW · UNTRACKED` | `SELL · HOLD · UNTRACKED · BENCHMARK` + a `conditions` list |
| Precedence | `SELL-REVIEW > REVIEW > TRIM > HOLD` (a suppression chain) | no chain; every condition that holds is emitted |
| Authorization | none — a human was assumed | `valuation_exit OR current_valid_stage5b_sell` |
| Thesis / disqualifier warnings | terminal review states | Stage 5B: LLM thesis-intact diagnosis |
| Execution | none | `paper-routine`, full liquidation, 56-day per-name cooldown |
| `AUTOMATIC_EXECUTION_AUTHORIZED` | `False` | `True` (governs the **valuation** exit only) |

## The two substantive decisions

### 1. Precedence: a disjunction, not a chain

`SELL_AUTHORIZED = valuation_exit OR current_valid_stage5b_sell`

Not `valuation_exit AND no warning`, and not `valuation_exit AND Stage 5B
agrees`. **This is a behavioural change, and it corrects live code that never
matched the harness.**

The harness emitted `trim` and `trap` as independent exit reasons in one weekly
loop; under `--trim-only` (`sell_reasons = frozenset({"trim"})`) a fired trap
flag neither sold nor suppressed a valuation sell. Live `review.sell_signal`
returned `REVIEW` on any trap flag *before* testing valuation, so a flagged name
could never reach the valuation exit — and
`test_exit_paths_unblocked.PrecedenceIsPreserved.test_disqualifier_outranks_overvaluation`
pinned that inversion in place.

Keeping the chain while routing warnings to an LLM would have been strictly
worse than the status quo: a model verdict of `hold` would suppress the one sell
rule with out-of-sample support. Stage 5B is therefore **informational** when a
valuation exit has already fired, and the audit trail records
`authorization = "valuation-exit"` so it is never ambiguous which rule executed.

### 2. Full liquidation, no invented partial

The harness sold the whole position (`shares.pop(t, 0.0)`, episode state
discarded, ladder re-armed), and `backtest-synthesis-2026-08-10.md:130` says so:
*"full-position sells were what we simulated (live 'trim a slice' is gentler
than anything tested)."* A fractional sale would be an untested parameter, so
execution calls `broker.sell(sell_all=True)` and never computes a share count —
a test asserts the absence.

The name `TRIM` invited exactly the wrong reading, so it is gone. Behaviour
unchanged; the name now describes it.

## What is NOT authorized, and why the split is empirical

Mechanical **thesis-break** selling stays refuted.
`backtest-synthesis-2026-08-10.md` finding 2: every policy containing trap-flag
exits lost in every configuration tested ($373–434k vs $579k hold), because
recession filings make quality names look statistically broken and the rule
therefore sells the 2001/2009 lows.

So the split is not architectural symmetry — it is what was measured:

- valuation exit → **mechanical, executes** (finding 3);
- thesis / disqualifier warning → **judgment, then executes** (finding 2).

Substituting *LLM judgment* for *human judgment* in finding 2 is the actual
architectural claim being made. The harness measured that mechanical rules fail
here, not that a human specifically is required.

## Defect found during the correspondence audit

`trim.evaluate()` implemented **one** of the rule's three no-anchor fallbacks.
The harness and `review.py` both distinguish:

1. thin history / no market cap → history leg alone;
2. owner earnings ≤ 0 → history leg is **not** enough; requires record price AND
   record market cap (an EBIT just back above zero prints "richest ever" at the
   *start* of a recovery — ~127 such sells in the 1998-2026 sim);
3. no EV/EBIT at all → EV/S percentile plus both ratchets.

Case 2 through the old `evaluate` returned TRIM where the live path correctly
returned HOLD. Latent — `evaluate` was test-only — but it is why
`review.sell_signal` now *calls* the policy module instead of restating it. Two
statements of one rule is how the drift happened.

Also fixed, caught by running the new commands: `stage5-queue` reported "empty"
while `review` reported the same holding warned and retained, because only
`review` called `run_stage2` to attach the verdicts that define a
`thesis_warning`. Three consumers now attach them, and a test pins each.

## Fail-closed semantics, and their polarity

| Stage-5B outcome | Action |
|---|---|
| `sell` | authorize a full exit |
| `hold` | retain |
| `uncertain` | retain, authorize nothing, requeue |
| `error` | retain, authorize nothing, requeue |
| no diagnosis on file | retain, authorize nothing, queue |

An uncertain or failed call can never liquidate anything. The authorization is
**derived** from the decision inside `_stamp`, never supplied by the model — a
model that emitted its own `action_authorization` could authorize a sale by
asserting one.

Note the polarity is the **mirror** of the entry side, not a copy: entry fails
toward *no new exposure*, exit fails toward *no sale*. Both are "do nothing
new", stated from opposite sides. `test_exit_paths_unblocked` documents this so
a future reader does not "fix" one to match the other.

An authorization must also be **current**: a `sell` verdict expires after
`[stage5].requeue_days` (1), because liquidating days later on evidence that has
moved is not the decision that was made.

## Audit record

Every Stage-5B decision persists provider, model, exit prompt SHA, decision
instant, the **exact evidence supplied**, the triggering condition, all
conditions at run, the classification, the reasoning and the action
authorization — in `data/exit-diagnosis/` (committed; these are the audit trail
for decisions that sold things) with a readable note under `theses/exit/`.
Executed exits append to `portfolio/exit-log.jsonl`.

The exit prompt has **its own SHA**, deliberately not
`diagnose.SYSTEM_PROMPT_SHA`: grouping exit decisions under the entry prompt's
hash would make two different decisions look reproducible from one set of
instructions. Errors are logged to `_errors.jsonl` but never written to the
verdict cache — a cached error would be served as the current verdict and mask a
later good run.

## Known limits

- **The 56-day cooldown is ported, not re-measured.** It is the harness's own
  spacing (`emit()`), and live had no analogue because live never executed.
- **Stage 5B has no backtest and cannot have one.** It is a judgment layer; the
  harness cannot score anything whose value is a verdict. Its analogue on the
  buy side is measured by the graveyard (rejects vs accepts, forward returns net
  of VOO) and by the AUTO-minus-MECH shadow lane. **An exit-side graveyard is the
  obvious next instrumentation and does not exist yet** — there is currently no
  measurement that would tell us Stage 5B is adding value rather than churning.
- **No threshold was re-tuned.** `sell_percentile` stays 0.97; the entry gates,
  ladder and reserve are untouched. This changes who decides and whether the
  decision executes, nothing about what the rules say.
- **Paper only.** `[paper]` is the only account family this executes in.
