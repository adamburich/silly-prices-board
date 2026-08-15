# Buy/sell percentile sensitivity grid — S&P 500 point-in-time, 1998–2015

*Generated 2026-08-10 by 16 runs of `scripts/backtest_universe.py --sp500`
($100k, 100 × $1k lots, TRIM-only sells at the given percentile + optimistic
owner-earnings DCF, or pure hold). Benchmarks constant across cells:
survivorship-free equal-weight members $395.4k, SPY $289.1k.*

## Final value of $100,000

| buy pctl \ sell | TRIM s85 | TRIM s90 | TRIM s95 | hold |
|---|---|---|---|---|
| **bottom 5%** | $646.2k | $678.2k | **$740.4k** | $621.0k |
| **bottom 10%** | $564.8k | $589.6k | $659.9k | $617.1k |
| **bottom 15%** | $551.1k | $573.9k | $650.8k | $604.1k |
| **bottom 20%** | $565.6k | $576.6k | $671.6k | $578.8k |

## Reading

1. **The s95 column is a genuine plateau.** TRIM at the top-5% band (plus the
   DCF leg) wins its row at every entry depth — $650.8k–$740.4k — beating
   both tighter TRIM settings and pure hold everywhere. This is the robust,
   directionally trustworthy finding: **make TRIM rare and it turns from a
   cost into an improvement.** Mechanically, rare TRIM harvests only the most
   egregious recoveries and re-arms the ladder without surrendering ordinary
   compounders.
2. **Entry depth matters much less, except at the extreme.** Row means:
   b5 $671k, b10 $608k, b15 $595k, b20 $598k. Bottom-10/15/20 are
   statistically indistinguishable; only bottom-5% stands out, and its
   best cell ($740.4k) is a **corner of the grid** — there is no b2.5% row
   to confirm a plateau beyond it. Treat b5/s95 as promising, not proven.
   (It is at least not thin: 258 fills across 62 names.)
3. **Every cell beats the survivorship-free equal-weight market** ($395.4k)
   and SPY ($289.1k). The funnel's edge does not depend on the parameter
   choice; parameters modulate between +10.0% and +11.8% CAGR.
4. Note on the s95 cells' ending cash (e.g. $196k of b5/s95's $740k): late
   TRIM harvests often found no re-entry signals, so the strategy drifted
   toward cash into 2015 — an emergent defensiveness, valued at face.

## Caveats

- One 18-year window; all cells share the same two crashes and recoveries.
  The grid defends against parameter overfitting, not against window luck.
- The s95 preference was formed after seeing s85 results (sequential
  testing); the plateau mitigates but does not eliminate that bias.
- Portfolio-level results include allocation effects (lot concentration,
  TRIM recycling). Per-episode entry quality does not improve at deeper
  buy percentiles (cohort medians are flat-to-slightly-worse); the gains
  come from concentrating capital, not from better per-name picks.
- No Stage-2 human judgment anywhere in these sims — live results would
  layer it on top of whatever these mechanics deliver.

## Implication for the live config

Evidence supports raising the TRIM review-flag line (`sell_percentile`)
from 0.85 toward 0.95 — it is currently flagging ~3× more names than the
backtest-optimal rarity. Entry (`valuation_percentile` 0.20) has no urgent
case for change; bottom-5% deserves a follow-up with intermediate rows
(b2.5, b7.5) before being taken seriously.
