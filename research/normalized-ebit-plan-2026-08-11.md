# Plan: normalized EBIT for the buy side

*2026-08-11. Branch `mechanized`. DESIGN ONLY — nothing implemented.*

## The gap

The buy path drops any name with no current multiple. For non-financials `cur`
requires `ebit > 0`, so a company at an earnings trough is not scored, not
gated, and not bought — it exits the loop before any gate runs.

Measured over 1998-2019 (instrumentation in commit 5685409, diagnostic only):

| | name-weeks | names |
|---|---|---|
| 20%+ off high, EBIT<=0, no multiple | **14,412** | 273 |
| ...EV/S in own bottom 25% | 8,890 | 218 |
| ...EV/S in own bottom 5% | 3,905 | 160 |

The funnel makes ~2,025 fills over the same window. **The buy side is skipping a
population several times the size of what it acts on.** An earlier guess that
F>=7 would veto these anyway is untested — they never reach the gate.

It is 14 names / 258 weeks for mega-caps, so this does not interact with
`--mega-fscore`.

The sell side does NOT have this gap: the negative-EBIT fallback built for the
GME hole (commit 2bc1d41, live at 8f36343) answers "is this egregiously
expensive?" for exactly these names. **Buy-side normalization completes a
symmetry rather than opening a one-way door**, which is the main reason it is
safe to attempt.

## Why not EV/S

EV/S is right for the sell fallback and wrong for entry. A low EV/S on a
money-loser is usually *correctly* priced — revenue is cheap to have, converting
it to profit is the hard part. The flagged population proves it: real cyclical
troughs (MU, DVN, LEN, NEM) sit alongside terminal cases, and **CHKAQ —
Chesapeake carrying its bankruptcy suffix — is flagged 66 times as cheap.**

There is also no buy-side mirror of what makes the sell fallback safe. That rule
leans on ratchets at an all-time HIGH, which are hard to fake. A record LOW is
exactly what terminal decline looks like.

## The measure

**Normalized EBIT = (own trailing-median EBIT margin) x (current revenue)**,
scored as `EV / normalized EBIT` against a band of the same quantity computed at
every prior filing.

Two construction rules, both non-negotiable:

- **Trailing-only median at each filing date.** Using a full-sample median to
  normalize a 2004 filing is lookahead of the subtle kind that survives review.
- **A separate band (`band_n`), not the existing EV/EBIT band.** The existing
  band is built from filed `evebit` and accepts only positive values, so it is
  composed entirely of profitable periods; a normalized numerator against an
  as-reported band is a units mismatch. Mirrors `band_s` exactly — same pattern,
  same place in the loop.

## The precondition — FIRST DESIGN REFUTED, DO NOT REBUILD IT

The original proposal was a profitability-history gate: EBIT > 0 in more than
half of the last 10 annual filings, median margin > 0. Stated prediction, made
before checking: *keeps MU, DVN, LEN, NEM; drops CHKAQ, VIAV, Q1, AMCC.*

Checked against the data:

```
CHKAQ   last 10 filings: 7/10 positive EBIT, median margin 13.2%  -> PASS
        2017 +$1,377M   2018 +$1,350M   2019 +$12M  ...Chapter 11 June 2020
MU      last 10 filings: 9/10 positive EBIT, median margin 27.1%  -> PASS
GME     last 10 filings: 4/10 positive EBIT, median margin -0.7%  -> FAIL (2026)
        (as of early 2021 it ran 7/10 positive -> would have PASSED)
```

**Chesapeake sails through.** It had $1.4B of EBIT two years before bankruptcy.
It did not lack earnings to normalize toward — it lacked a balance sheet: ~$9B
of debt against collapsing gas prices.

The lesson generalizes: **normalized EBIT measures earnings power; cyclicals die
of leverage at troughs, not of thin margins.** For a trough purchase, SURVIVAL
is the binding constraint, not valuation. The precondition must be a solvency
test — net debt / normalized EBIT, interest coverage on normalized EBIT, Altman
Z — not a profitability-history test.

Check in the same pass how many terminal cases the EXISTING trap flags already
catch. If Altman Z and the dilution flag were going to veto Chesapeake anyway,
the precondition is doing less work than it appears and should be simplified.

## Interaction with the sell side — three collisions

**1. The fallback's trigger is literally "no current multiple." Normalization
would silently disarm it.** `fb_top` evaluates only when `cur_s is None`. Wire
normalized EBIT into the shared `cur_mult` and negative-EBIT names suddenly have
a multiple, `cur_s` stops being None, and **the GME fix never fires again** — no
error, it just quietly stops working. The buy path must be strictly separate
from `cur_mult`, or the fallback's trigger rewritten as a deliberate decision.

**2. It concentrates the book in the sign-flip population.** Buying at EBIT
troughs means systematically holding names about to cross EBIT back up through
zero from below. At that crossing the real denominator is near zero, EV/EBIT
explodes, percentile hits 1.0, and TRIM fires at the START of the recovery —
the ADSK-2019 pattern (trimmed $157-168 en route to >$300) and the 258 blocked
anchorless trims, every one on a just-flipped-positive EBIT. The
record-price/record-marketcap gate currently intercepts these, but this change
would make that population the core of the book rather than an edge case. Buy
the bottom, sell the first green shoot.

**3. The resolution: normalize both sides.** If the sell band used the same
normalized denominator, EV/normEBIT stays continuous across the sign flip and
the artifact disappears at the root instead of being patched downstream. This is
an argument for doing it **symmetrically or not at all**. Cost of the symmetry:
a permanently impaired company never looks expensive either, because it keeps
getting credit for margins it will never earn again. The ratchets and the DCF
ceiling remain as conjuncts, but the exit slows on exactly the names one most
wants to exit.

**GME is not hypothetical here.** As of early 2021 it passed the precondition
(7/10 positive). At $4 in 2020 its EV against normalized earnings would sit near
the bottom of its own band: a buy. The fallback then trimmed it at $56.25. Buy
at $4, trim at $56 is the most attractive result this system could produce, and
it should be read as a warning rather than a validation — the same rule that
catches Micron 2023 catches Chesapeake 2015, and we only know which was which
because we know how they ended.

## Phases, each with a stop condition

**Phase 1 — the revised (solvency) precondition, no returns computed.**
Run the classifier alone over the 273-name skip population. Predict in writing
first, then check. STOP if it cannot separate the named cases. This is the
cheapest falsification available and it comes first deliberately.

**Phase 2 — cohort forward returns.** Admitted vs rejected within the skip
population at 1/3/5y, **means not medians** — these distributions are violently
right-skewed and the median has reversed a conclusion in this campaign three
times. STOP unless the admitted cohort mean is positive and beats rejected at 3y.

**Phase 3 — portfolio A/B behind `--norm-ebit`, default off, three windows,
WITH ITS NULL.** The rule ADDS signals, so it adds deployment, and deployment
alone is worth a great deal in crisis-rich windows. A raw A/B cannot separate
"normalization selects well" from "we deployed more capital". The null ships
with the test: **add the same number of signals drawn at random from the skipped
population** — the mirror of `--drop-frac`, which is what made the idiosyncratic
filter finding interpretable. Report average-invested beside every number.
- normalized ~= random-add -> a throttle-opener, not a selector. Reject.
- normalized > random-add -> real selection. Proceed.

**Phase 4 — live.** Only if 1-3 pass.

## The honest risk, and why the design may be wrong in shape

**Normalized earnings is the classic value trap, and this is its mechanism:
every secular decline looks cyclical from inside it.** Newspapers, coal and
video rental each had ten good years of margins immediately before they had
none, and each would pass a "profitable across most of the last decade" test on
the way down. The precondition is a filter on history, and history is exactly
what a secular decline still has.

Which points at a different shape for the whole thing. **If Stage-2 is the
backstop, the mechanical layer should be optimized for RECALL, not precision.**
The precondition felt inadequate because it was being asked to *decide*. If its
job is only to *surface* — put candidates in front of a diagnosis — then a false
positive costs a read, not a loss, and the bar can be far looser and simpler.

The population makes that practical: **160 names over 22 years is about seven a
year.** Not a screening problem, a reading list — and precisely the shape live
already has for `price_signal_only` names, which are flagged for manual review
rather than dropped.

Chesapeake favours that reading: its failure was arithmetic, not foresight. ~$9B
of debt visible in the filings for years. That is the read Stage-2 exists to do.

Two caveats on leaning on it:
- **Stage-2's hit rate is unmeasured.** Today the F-score gate proved worth
  something real and the idiosyncratic filter proved worth *less than random*,
  both against confidently-held priors. "In theory Stage-2 catches it" is the
  same shape of claim.
- **It is hard to test, for reasons specific to what it is.** Any retrospective
  run is contaminated — the model already knows Chesapeake went bankrupt and
  GameStop squeezed. A validation must be designed around leakage from the
  start (blinded tickers and dates, obscure names only), and the famous names
  have to be excluded from scoring precisely because they are the ones we care
  about most.

So the open question is not "will Stage-2 catch Chesapeake" — probably yes, on
the balance sheet alone. It is **"what does Stage-2 do with the forty ambiguous
cyclicals that have no obvious tell?"** That is where the expected value lives,
and no amount of thinking about Chesapeake answers it.

## Scale warning

This is potentially the largest expansion of the buy universe ever proposed
here: 160 names in the bottom-5% EV/S slice alone, against a funnel that
currently touches 263. And the losses are not the mirror of the wins — a
bankruptcy takes the lot to zero AND, because the ladder is capital-rationed and
starved ~28% of the time, takes with it every subsequent lot that capital would
have funded. The left tail is heavier than a win/loss count suggests, and it
clusters in the same crisis weeks where the edge is concentrated.
