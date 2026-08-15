# "Should a decline deploy the parked reserve?" — asked after the reserve run

*2026-08-11, after the reserve was spent. Written to record the reasoning
BEFORE any work, because work begun after seeing a holdout needs its
provenance on the record or it cannot be trusted later.*

## The question

The frozen leg finished the reserve window with **$88,251 parked — 40% of the
book — and only 16 starved signals in 3 weeks.** It was never short of money;
it was short of qualifying names. Adam asked whether the system is supposed to
deploy the parked reserve when the regime classifier reads DECLINE or CRISIS.

**Mechanically, no.** `from_reserve()` is called only to fund a fill.
`crisis_weeks` is `None` in the frozen configuration, so `regime.py` is never
consulted. The reserve drains when a name enters the buy zone, and for no other
reason.

## Contamination status of any work that follows

Rule 1 of the holdout discipline forbids configuration choices informed by
results measured after 2019-12-31. The reserve has now been seen, so this needs
stating explicitly rather than assumed.

**The motivation predates the reserve, on development evidence only:**

- 2010-2019: "in the calm decade the funnel's own selections underperformed SPY
  at every lot size, and the parked reserve carried the result"
  (lot-size-exposure-finding-2026-08-11.md).
- The `--open-with` experiment, measured entirely on 2000-2019: at zero
  starvation F>=7 goes **negative** against its own benchmark and loosening is
  worth +9.26pp (f-bar-capital-scale-2026-08-11.md).

The reserve **confirmed** an idle-capital problem that development had already
surfaced; it did not reveal it. That is what makes development work here
legitimate rather than reserve-tuned. What the reserve supplied is urgency, and
urgency is not evidence.

## THE IDEA IS LARGELY ALREADY IMPLEMENTED — and was investigated

`reports/regime-adaptive-investigation-2026-08-11.md` asked a near-identical
question a few hours earlier and answered it. Three findings bear directly:

**1. The buy rule is already regime-conditional.** PASSED episodes per 100
weeks:

| regime | per 100wk | vs baseline |
|---|---|---|
| CRISIS | 121.9 | **2.43x** |
| DECLINE | 91.3 | 1.82x |
| BEAR | 90.5 | 1.80x |
| STALL | 50.5 | 1.01x |
| BOOM | 22.6 | **0.45x** |

A **5.4x density ratio** between CRISIS and BOOM, achieved entirely by the
existing drawdown trigger and valuation percentile. The system already deploys
far harder in declines. Its own words: "bolting an explicit regime gate onto
the buy rule would largely duplicate what the buy rule already does."

**2. Regime is mostly depth wearing a costume.** CRISIS entries have a median
drawdown of 36.9% against ~25% everywhere else. Test regime *within* depth
buckets and the advantage is inconsistent in sign, with CRISIS the *worst*
regime in two of three buckets.

**3. Idle capital in a boom may be correct behaviour, not a defect.** BOOM
entries have the worst 5-year excess of any regime at **-10.5%**. Signals are
0.45x baseline there because there is genuinely little worth buying. The
parked reserve sits in SPY, which is the right place for money when nothing is
dislocated — and the frozen leg beat equal-weight by +3.73% *while* holding
48% of its book parked. The parking did its job.

**So the "oversight" is mostly not one.** I described it as a gap in
conversation and that was too strong. What is real is narrower: the frozen
config parked 48% against the incumbent's 20% and lost the head-to-head. What
is not established is that deploying that reserve would have helped, and one
development finding says the opposite.

## THE HORIZON STORY — the best explanation yet of the head-to-head

The same investigation produced the cleanest validation of the quality gates in
the campaign, and it is a *horizon* result:

> Depth buys a bounce, regardless of quality. At 1y, gate-VETOED deep names
> return **+12.3%** and gate-PASSED **+12.4%** — indistinguishable. The gates
> make it durable: at 3y PASSED holds +13.8%/+7.6% while VETOED flattens to
> ~-1% and naive dip-buying goes to -4.4%/-10.0%.

**Loosening the quality bar buys a one-year rebound that you give back by year
three.** That predicts precisely the reserve pattern:

| start | window | crash in window | terminal Δ | edge gap |
|---|---|---|---|---|
| 2020 | 6.6y | COVID crash + rebound | -6.44% | -7.14pp |
| 2021 | 5.6y | 2022 bear + rebound | -8.65% | -9.40pp |
| 2022 | 4.6y | 2022 bear (entered mid) + rebound | -3.43% | -3.60pp |
| 2023 | 3.6y | **none** | **+0.08%** | **+0.10pp** |

*The "crash in window" column is retained only to show what was claimed; the
per-year decomposition below refutes it as the driver. Units corrected
2026-08-11.*

**VOID as an account of these four windows, 2026-08-11.** The pattern it reads
off is not there: a per-year decomposition shows the frozen leg AHEAD through
January 2025, with the whole gap opening in 2025-2026. The cohort finding may
still be true on its own terms; it is NOT evidenced by these windows and must
not be cited from them. See reports/pre-integration-audit-2026-08-11.md.

~~The loose bar wins wherever there is a rebound to capture and ties exactly
where there is not.~~ This explanation is grounded in pre-2020 cohort data,
which makes it materially stronger than the post-hoc "indiscriminate recovery"
hypothesis recorded in frozen-config-2026-08-11.md — it is that hypothesis,
with development evidence already behind it.

It also reframes the head-to-head loss: the incumbent did not select better. It
bought more bounce. Whether it keeps it is a question these windows are too
short to answer, and the 2023 dead heat is the first hint that it does not.

## What is genuinely open

1. **Does the incumbent's advantage decay with holding period?** The cohort
   data says it should. Terminal wealth over 3.6-6.6 years cannot see it.
   Testable on development windows by comparing the two configurations'
   *cohort* forward returns at 1y/3y/5y rather than terminal value.
2. **Is there any version of regime-conditional deployment that is not a
   duplicate of the buy rule?** The density table says an explicit gate would
   duplicate; nobody has tested relaxing the *quality* bar by regime, which is
   a different lever from relaxing entry.
3. **2020-2026 can no longer validate any of it.** Whatever gets built now has
   no clean holdout. Forward live is the only remaining uncontaminated test,
   and that should be stated in any decision record that follows.

## Recommendation

**Do not build regime-triggered reserve deployment.** Three independent
development findings say it duplicates the existing buy rule, that regime is
mostly depth in costume, and that idle capital in a boom is probably correct.

**Do test the horizon question (1).** It is the one that decides how to read the
reserve result, it runs entirely on development windows, and it needs no new
mechanism — only cohort returns for two configurations that already exist.
