# S&P 500 point-in-time funnel backtest — 1998-01-05 .. 2015-12-28

*Generated 2026-08-10 by `scripts/backtest_universe.py`. 951 tickers swept; 766 produced episodes. Filters: mcap >= $500M, price >= $3.0, F >= 7.*

*Requested start 1995-01-01; price data begins 1998-01-05 (see reports/research-backtest-data-costs-2026-08-09.md).*

| | Funnel portfolio | Survivorship-free EW market |
|---|---|---|
| Final value of $100,000 | $439,249 | $395,421 |
| CAGR | +8.6% | +7.9% |
| Fills / names | 785 / 112 | all |
| Sells | 245 (0 band-top, 0 live-TRIM, 0 thesis-break, 245 peak-dip) | — |
| Cash remaining | $7,681 | — |

*SPY (div-adj) over the same window: $289,108 — cap-weighted control; VOO's inception is 2010, hence SPY.*

## Growth of $10,000 (first week of each year)

| Year | Funnel | EW market |
|---|---|---|
| 1998 | $100,000 | $100,000 |
| 1999 | $104,771 | $115,298 |
| 2000 | $106,222 | $131,119 |
| 2001 | $126,882 | $135,286 |
| 2002 | $151,845 | $127,083 |
| 2003 | $151,773 | $110,113 |
| 2004 | $192,767 | $139,611 |
| 2005 | $223,569 | $161,288 |
| 2006 | $246,836 | $180,890 |
| 2007 | $276,794 | $202,420 |
| 2008 | $272,749 | $215,795 |
| 2009 | $199,648 | $158,541 |
| 2010 | $259,754 | $197,637 |
| 2011 | $302,433 | $233,595 |
| 2012 | $294,312 | $241,709 |
| 2013 | $322,280 | $279,560 |
| 2014 | $403,862 | $337,706 |
| 2015 | $451,635 | $390,703 |
| Final | $439,249 | $395,421 |

## Cohort forward returns (median excess vs EW market, identical windows)

| Cohort | n | 1y | 3y | 5y |
|---|---|---|---|---|
| PASSED (funnel buys) | 877 | +2.4% (844) | +2.5% (820) | +0.7% (669) |
| VETOED (trap sweep) | 1658 | -0.5% (1614) | -4.4% (1567) | -11.1% (1318) |
| NAIVE 30% dip (no gates) | 5210 | -1.5% (5018) | -3.7% (4879) | -8.0% (4425) |

## Fills (first 40)

| Week | Ticker | Lot | Fill | Band pctl |
|---|---|---|---|---|
| 1998-05-18 | AMP1 | 1 | $34.71 | 13% |
| 1998-06-15 | GLW | 1 | $7.37 | 0% |
| 1998-06-22 | AMP1 | 1 | $34.52 | 13% |
| 1998-06-22 | GLW | 1 | $7.30 | 0% |
| 1998-07-06 | AMP1 | 2 | $33.92 | 13% |
| 1998-07-13 | AMP1 | 3 | $31.88 | 7% |
| 1998-07-13 | GLW | 2 | $7.19 | 0% |
| 1998-07-20 | GLW | 3 | $7.01 | 0% |
| 1998-07-27 | DE | 1 | $12.25 | 7% |
| 1998-08-03 | APD | 1 | $16.80 | 0% |
| 1998-08-03 | DE | 1 | $11.89 | 7% |
| 1998-08-10 | APD | 1 | $16.47 | 0% |
| 1998-08-10 | DOV | 1 | $11.12 | 19% |
| 1998-08-10 | GWW | 1 | $27.31 | 7% |
| 1998-08-10 | HPQ | 1 | $6.45 | 12% |
| 1998-08-17 | GWW | 1 | $27.11 | 6% |
| 1998-08-17 | MRO | 1 | $4.91 | 0% |
| 1998-08-17 | NOC | 1 | $17.65 | 17% |
| 1998-08-24 | DE | 2 | $10.83 | 7% |
| 1998-08-24 | GWW | 2 | $26.99 | 6% |
| 1998-08-24 | MRO | 1 | $4.91 | 0% |
| 1998-08-24 | NOC | 1 | $17.47 | 17% |
| 1998-08-24 | PCAR | 1 | $2.07 | 0% |
| 1998-08-31 | APD | 2 | $14.70 | 0% |
| 1998-08-31 | DALRQ | 1 | $49.95 | 0% |
| 1998-08-31 | DE | 3 | $9.51 | 0% |
| 1998-08-31 | DOV | 1 | $11.18 | 19% |
| 1998-08-31 | GWW | 3 | $25.03 | 0% |
| 1998-08-31 | HPQ | 1 | $6.12 | 0% |
| 1998-08-31 | ITW | 1 | $13.25 | 19% |
| 1998-08-31 | MRO | 2 | $4.39 | 0% |
| 1998-08-31 | NOC | 2 | $16.22 | 17% |
| 1998-08-31 | PCAR | 1 | $2.02 | 0% |
| 1998-09-07 | DALRQ | 1 | $45.91 | 0% |
| 1998-09-07 | DOV | 2 | $10.77 | 19% |
| 1998-09-07 | ITW | 1 | $12.78 | 19% |
| 1998-09-07 | NOC | 3 | $15.65 | 8% |
| 1998-09-07 | PCAR | 2 | $2.01 | 0% |
| 1998-10-05 | APD | 3 | $14.57 | 0% |
| 1998-10-05 | PCAR | 1 | $1.88 | 0% |
| … 745 more | | | | |

## Sells (first 40)

| Week | Ticker | Reason | Proceeds | Episode opened |
|---|---|---|---|---|
| 1998-05-25 | AMP1 | peak | $1,032 | 1998-05-18 |
| 1998-06-22 | GLW | peak | $991 | 1998-06-15 |
| 1998-08-03 | DE | peak | $971 | 1998-07-27 |
| 1998-08-10 | APD | peak | $980 | 1998-08-03 |
| 1998-08-17 | DOV | peak | $1,085 | 1998-08-10 |
| 1998-08-17 | GWW | peak | $993 | 1998-08-10 |
| 1998-08-17 | HPQ | peak | $1,078 | 1998-08-10 |
| 1998-08-24 | MRO | peak | $1,000 | 1998-08-17 |
| 1998-08-24 | NOC | peak | $990 | 1998-08-17 |
| 1998-08-31 | PCAR | peak | $977 | 1998-08-24 |
| 1998-09-07 | DALRQ | peak | $919 | 1998-08-31 |
| 1998-09-07 | ITW | peak | $965 | 1998-08-31 |
| 1998-11-23 | AMP1 | peak | $4,301 | 1998-06-22 |
| 1998-12-14 | GLW | peak | $3,866 | 1998-06-22 |
| 1999-02-08 | HPQ | peak | $1,480 | 1998-08-31 |
| 1999-04-05 | BC | peak | $972 | 1999-03-29 |
| 1999-04-12 | DALRQ | peak | $1,477 | 1998-09-07 |
| 1999-04-12 | FTL.A | peak | $848 | 1999-04-05 |
| 1999-04-26 | APD | peak | $7,744 | 1999-01-25 |
| 1999-04-26 | ITW | peak | $1,570 | 1998-09-07 |
| 1999-05-10 | ADSK | peak | $881 | 1999-05-03 |
| 1999-05-17 | NEE | peak | $1,023 | 1999-05-10 |
| 1999-06-07 | GWW | peak | $8,406 | 1998-11-16 |
| 1999-06-21 | DOV | peak | $2,962 | 1998-08-31 |
| 1999-09-06 | SHW | peak | $927 | 1999-08-30 |
| 1999-10-04 | PGR | peak | $1,030 | 1999-09-27 |
| 1999-11-22 | TT | peak | $992 | 1999-11-15 |
| 1999-12-20 | WHR | peak | $1,072 | 1999-12-13 |
| 2000-02-21 | HSH | peak | $970 | 2000-02-14 |
| 2000-03-06 | VZ | peak | $1,143 | 2000-02-28 |
| 2000-03-13 | ADSK | peak | $4,231 | 1999-05-10 |
| 2000-03-20 | BA | peak | $1,134 | 2000-03-13 |
| 2000-03-20 | WOR | peak | $1,035 | 2000-03-13 |
| 2000-04-03 | CINF | peak | $1,212 | 2000-03-27 |
| 2000-04-03 | DHR | peak | $1,193 | 2000-03-27 |
| 2000-04-03 | KMB | peak | $1,141 | 2000-03-27 |
| 2000-04-03 | UNP | peak | $1,002 | 2000-03-27 |
| 2000-04-10 | SNA | peak | $973 | 2000-04-03 |
| 2000-04-10 | SWK | peak | $984 | 2000-04-03 |
| 2000-04-24 | CCL | peak | $1,065 | 2000-04-17 |
| … 205 more | | | | |

## Honest limits

- No Stage-2 human judgment; the DJIA run showed the deterministic sweep
is procyclical (vetoes recessions) — cohort VETOED-vs-PASSED quantifies
what that judgment layer would have to beat.
- No sells; portfolio cash can exhaust (cohorts are the cash-free view).
- Delisted names' final value = last trade (bankruptcy often overstates,
buyouts understate).
- F>=7 + $500M cap is a mechanical stand-in for the hand-built watchlist.
- ADRs are not excluded (store lacks the category column).
