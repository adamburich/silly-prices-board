# Sensitivity grid under annual contributions — S&P 500 PIT, 1998–2015

*Generated 2026-08-10: the 16-cell buy/sell grid rerun in `--inject 20000`
mode ($20k each January, $360k total contributed, $1k lots, TRIM-only sells,
same-cashflow benchmarks). Companion to the lump-sum grid
(backtest-grid-sp500-2026-08-10.md); this is the funding model the live
strategy actually uses. Benchmarks, same cashflows: EW members $854.0k,
BRK.B $807.2k, SPY $722.4k.*

## Final value of $360,000 contributed

| buy pctl \ sell | TRIM s85 | TRIM s90 | TRIM s95 | hold |
|---|---|---|---|---|
| **bottom 5%** | $1,069.7k | $1,073.3k | **$1,109.8k** | $982.0k |
| **bottom 10%** | $985.7k | $1,006.3k | $1,041.3k | $978.9k |
| **bottom 15%** | $955.3k | $1,008.5k | $1,055.5k | $974.9k |
| **bottom 20%** | $993.7k | $1,021.4k | $1,090.7k | $1,013.9k |

## Reading

1. **The live config (b5/s95) is the global maximum again.** The threshold
   decision taken on lump-sum evidence survives the realistic funding model
   — the strongest robustness statement available from this data.
2. **s95 wins every row in both grids (8/8 rows total)**, and under DCA it
   beats *hold* in every row as well: with contributions flowing, rare TRIM
   recycling is additive everywhere, not just at deep entries.
3. **Buy depth matters less under DCA, and the ordering bends.** b5 wins
   the three TRIM columns; b20 wins the hold column ($1,013.9k vs $982.0k)
   and its s95 cell trails the champion by only 1.7%. Parameter spread
   compressed from ±34% (lump) to ±16% (DCA) — annual contributions average
   away much of the sensitivity, which cuts overfitting risk in both
   directions. Practical implication: the b5 choice costs little even if
   wrong; its dominant effect is operational (fewer, deeper episodes and
   more idle cash — b5 rows finished with ~$200k uninvested and won anyway).
4. Follow-up worth testing: parking idle cash in SPY between signals — b5's
   cash drag is the obvious remaining inefficiency; parking would also
   stress-test whether dry powder held as equities survives arriving at
   dislocations intact.

## Caveats

Same window as the lump grid (shared crashes, shared recoveries) — this
derisks the funding model, not the era. Sequential-testing history applies
unchanged. Cohort entry-quality conclusions are unaffected by funding mode.
