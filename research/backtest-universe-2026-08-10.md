# Full-universe funnel backtest — 1998-01-05 .. 2015-12-28

*Generated 2026-08-10 by `scripts/backtest_universe.py`. 21941 tickers swept; 4581 produced episodes. Filters: mcap >= $500M, price >= $3.0, F >= 7.*

*Requested start 1995-01-01; price data begins 1998-01-05 (see reports/research-backtest-data-costs-2026-08-09.md).*

| | Funnel portfolio | Survivorship-free EW market |
|---|---|---|
| Final value of $100,000 | $497,577 | $362,862 |
| CAGR | +9.3% | +7.4% |
| Fills / names | 100 / 28 | all |
| Cash remaining | $0 | — |

*SPY (div-adj) over the same window: $289,108 — cap-weighted control; VOO's inception is 2010, hence SPY.*

## Growth of $10,000 (first week of each year)

| Year | Funnel | EW market |
|---|---|---|
| 1998 | $100,000 | $100,000 |
| 1999 | $109,914 | $106,884 |
| 2000 | $112,362 | $136,717 |
| 2001 | $135,686 | $137,216 |
| 2002 | $134,435 | $130,285 |
| 2003 | $125,669 | $113,817 |
| 2004 | $167,259 | $151,412 |
| 2005 | $213,644 | $174,559 |
| 2006 | $235,975 | $194,117 |
| 2007 | $272,980 | $221,412 |
| 2008 | $302,805 | $233,241 |
| 2009 | $197,447 | $174,040 |
| 2010 | $247,862 | $218,536 |
| 2011 | $322,379 | $258,072 |
| 2012 | $307,295 | $258,094 |
| 2013 | $370,348 | $294,647 |
| 2014 | $477,390 | $339,187 |
| 2015 | $538,559 | $368,006 |
| Final | $497,577 | $362,862 |

## Cohort forward returns (median excess vs EW market, identical windows)

| Cohort | n | 1y | 3y | 5y |
|---|---|---|---|---|
| PASSED (funnel buys) | 2400 | +2.5% (2293) | +3.5% (2175) | +3.9% (1713) |
| VETOED (trap sweep) | 7224 | -1.6% (6802) | -4.1% (6264) | -10.9% (5032) |
| NAIVE 30% dip (no gates) | 27324 | -7.3% (25570) | -13.5% (23532) | -23.9% (20440) |

## Fills (first 40)

| Week | Ticker | Lot | Fill | Band pctl |
|---|---|---|---|---|
| 1998-05-18 | AMP1 | 1 | $34.71 | 13% |
| 1998-06-15 | GLW | 1 | $7.37 | 0% |
| 1998-06-22 | AMP1 | 1 | $34.52 | 13% |
| 1998-06-22 | EAT | 1 | $5.75 | 14% |
| 1998-06-22 | GLW | 2 | $7.30 | 0% |
| 1998-07-06 | AMP1 | 2 | $33.92 | 13% |
| 1998-07-13 | AMP1 | 3 | $31.88 | 7% |
| 1998-07-13 | GLW | 3 | $7.19 | 0% |
| 1998-07-27 | DE | 1 | $12.25 | 7% |
| 1998-08-03 | APD | 1 | $16.80 | 0% |
| 1998-08-03 | DE | 2 | $11.89 | 7% |
| 1998-08-03 | EAT | 1 | $5.84 | 14% |
| 1998-08-10 | APD | 2 | $16.47 | 0% |
| 1998-08-10 | DOV | 1 | $11.12 | 19% |
| 1998-08-10 | GWW | 1 | $27.31 | 7% |
| 1998-08-10 | HPQ | 1 | $6.45 | 12% |
| 1998-08-17 | GWW | 2 | $27.11 | 6% |
| 1998-08-17 | MRO | 1 | $4.91 | 0% |
| 1998-08-17 | NOC | 1 | $17.65 | 17% |
| 1998-08-24 | DE | 3 | $10.83 | 7% |
| 1998-08-24 | GWW | 3 | $26.99 | 6% |
| 1998-08-24 | NOC | 2 | $17.47 | 17% |
| 1998-08-24 | PCAR | 1 | $2.07 | 0% |
| 1998-08-31 | APD | 3 | $14.70 | 0% |
| 1998-08-31 | DALRQ | 1 | $49.95 | 0% |
| 1998-08-31 | EAT | 1 | $5.30 | 7% |
| 1998-08-31 | HPQ | 2 | $6.12 | 0% |
| 1998-08-31 | ITW | 1 | $13.25 | 19% |
| 1998-08-31 | MRO | 2 | $4.39 | 0% |
| 1998-08-31 | MZTI | 1 | $13.52 | 0% |
| 1998-08-31 | NOC | 3 | $16.22 | 17% |
| 1998-08-31 | PCAR | 2 | $2.02 | 0% |
| 1998-08-31 | USG1 | 1 | $41.33 | 0% |
| 1998-09-07 | DALRQ | 2 | $45.91 | 0% |
| 1998-09-07 | DOV | 2 | $10.77 | 19% |
| 1998-09-07 | ITW | 2 | $12.78 | 19% |
| 1998-09-07 | PCAR | 3 | $2.01 | 0% |
| 1998-10-05 | CSL | 1 | $11.62 | 0% |
| 1998-10-05 | PCAR | 1 | $1.88 | 0% |
| 1998-10-05 | USG1 | 2 | $38.51 | 0% |
| … 60 more | | | | |

## Honest limits

- No Stage-2 human judgment; the DJIA run showed the deterministic sweep
is procyclical (vetoes recessions) — cohort VETOED-vs-PASSED quantifies
what that judgment layer would have to beat.
- No sells; portfolio cash can exhaust (cohorts are the cash-free view).
- Delisted names' final value = last trade (bankruptcy often overstates,
buyouts understate).
- F>=7 + $500M cap is a mechanical stand-in for the hand-built watchlist.
- ADRs are not excluded (store lacks the category column).
