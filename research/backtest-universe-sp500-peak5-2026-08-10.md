# S&P 500 point-in-time funnel backtest — 1998-01-05 .. 2015-12-28

*Generated 2026-08-10 by `scripts/backtest_universe.py`. 951 tickers swept; 766 produced episodes. Filters: mcap >= $500M, price >= $3.0, F >= 7.*

*Requested start 1995-01-01; price data begins 1998-01-05 (see reports/research-backtest-data-costs-2026-08-09.md).*

| | Funnel portfolio | Survivorship-free EW market |
|---|---|---|
| Final value of $100,000 | $508,276 | $395,421 |
| CAGR | +9.5% | +7.9% |
| Fills / names | 529 / 110 | all |
| Sells | 107 (0 band-top, 0 live-TRIM, 0 thesis-break, 0 peak-dip, 107 peak-5-day) | — |
| Cash remaining | $60,092 | — |

*SPY (div-adj) over the same window: $289,108 — cap-weighted control; VOO's inception is 2010, hence SPY.*

## Growth of $10,000 (first week of each year)

| Year | Funnel | EW market |
|---|---|---|
| 1998 | $100,000 | $100,000 |
| 1999 | $106,242 | $115,298 |
| 2000 | $106,902 | $131,119 |
| 2001 | $123,458 | $135,286 |
| 2002 | $140,071 | $127,083 |
| 2003 | $141,135 | $110,113 |
| 2004 | $187,262 | $139,611 |
| 2005 | $218,930 | $161,288 |
| 2006 | $248,730 | $180,890 |
| 2007 | $286,984 | $202,420 |
| 2008 | $291,278 | $215,795 |
| 2009 | $209,454 | $158,541 |
| 2010 | $279,927 | $197,637 |
| 2011 | $324,313 | $233,595 |
| 2012 | $322,861 | $241,709 |
| 2013 | $359,515 | $279,560 |
| 2014 | $458,160 | $337,706 |
| 2015 | $527,196 | $390,703 |
| Final | $508,276 | $395,421 |

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
| 1998-08-24 | NOC | 2 | $17.47 | 17% |
| 1998-08-24 | PCAR | 1 | $2.07 | 0% |
| 1998-08-31 | APD | 2 | $14.70 | 0% |
| 1998-08-31 | DALRQ | 1 | $49.95 | 0% |
| 1998-08-31 | DE | 3 | $9.51 | 0% |
| 1998-08-31 | GWW | 3 | $25.03 | 0% |
| 1998-08-31 | HPQ | 2 | $6.12 | 0% |
| 1998-08-31 | ITW | 1 | $13.25 | 19% |
| 1998-08-31 | MRO | 2 | $4.39 | 0% |
| 1998-08-31 | NOC | 3 | $16.22 | 17% |
| 1998-08-31 | PCAR | 2 | $2.02 | 0% |
| 1998-09-07 | DALRQ | 2 | $45.91 | 0% |
| 1998-09-07 | DOV | 2 | $10.77 | 19% |
| 1998-09-07 | ITW | 2 | $12.78 | 19% |
| 1998-09-07 | PCAR | 3 | $2.01 | 0% |
| 1998-10-05 | APD | 3 | $14.57 | 0% |
| 1998-10-05 | PCAR | 1 | $1.88 | 0% |
| 1998-11-16 | GWW | 1 | $27.88 | 6% |
| 1998-11-30 | GWW | 2 | $27.07 | 6% |
| 1998-12-07 | PCAR | 1 | $2.15 | 0% |
| … 489 more | | | | |

## Sells (first 40)

| Week | Ticker | Reason | Proceeds | Episode opened |
|---|---|---|---|---|
| 1998-06-22 | GLW | peak5 | $991 | 1998-06-15 |
| 1998-08-03 | DE | peak5 | $971 | 1998-07-27 |
| 1998-08-10 | APD | peak5 | $980 | 1998-08-03 |
| 1998-08-17 | GWW | peak5 | $993 | 1998-08-10 |
| 1999-01-11 | GLW | peak5 | $4,248 | 1998-06-22 |
| 1999-04-05 | BC | peak5 | $972 | 1999-03-29 |
| 1999-04-12 | FTL.A | peak5 | $848 | 1999-04-05 |
| 1999-05-10 | ADSK | peak5 | $881 | 1999-05-03 |
| 1999-05-17 | NEE | peak5 | $1,023 | 1999-05-10 |
| 1999-09-06 | SHW | peak5 | $927 | 1999-08-30 |
| 1999-10-04 | PGR | peak5 | $1,030 | 1999-09-27 |
| 1999-11-22 | TT | peak5 | $992 | 1999-11-15 |
| 1999-12-13 | DOV | peak5 | $3,452 | 1998-08-10 |
| 1999-12-20 | WHR | peak5 | $1,072 | 1999-12-13 |
| 2000-02-21 | HSH | peak5 | $970 | 2000-02-14 |
| 2000-03-06 | VZ | peak5 | $1,143 | 2000-02-28 |
| 2000-04-03 | DHR | peak5 | $1,193 | 2000-03-27 |
| 2000-04-10 | SNA | peak5 | $973 | 2000-04-03 |
| 2000-04-10 | SWK | peak5 | $984 | 2000-04-03 |
| 2000-04-24 | CCL | peak5 | $1,065 | 2000-04-17 |
| 2000-05-08 | LUMN | peak5 | $969 | 2000-05-01 |
| 2003-08-18 | PCAR | peak5 | $32,783 | 2000-03-20 |
| 2003-10-13 | ESRX | peak5 | $980 | 2003-10-06 |
| 2004-01-05 | HRB | peak5 | $2,720 | 2003-08-18 |
| 2004-02-09 | QLGC | peak5 | $1,036 | 2004-02-02 |
| 2004-03-22 | ORCL | peak5 | $973 | 2004-03-15 |
| 2004-03-29 | BMY | peak5 | $995 | 2004-03-22 |
| 2004-04-05 | ADSK | peak5 | $5,414 | 1999-05-10 |
| 2004-04-19 | FDO | peak5 | $994 | 2004-04-12 |
| 2004-05-17 | APCC | peak5 | $987 | 2004-05-10 |
| 2004-10-04 | BC | peak5 | $12,434 | 1999-11-01 |
| 2004-10-11 | WOR | peak5 | $10,255 | 2003-09-22 |
| 2004-10-25 | MRSH | peak5 | $1,033 | 2004-10-18 |
| 2005-02-07 | NEE | peak5 | $19,176 | 2000-01-31 |
| 2005-02-28 | CSC | peak5 | $1,014 | 2005-02-21 |
| 2005-02-28 | QCOM | peak5 | $1,043 | 2005-02-21 |
| 2005-03-07 | EBAY | peak5 | $978 | 2005-02-28 |
| 2005-05-16 | ESRX | peak5 | $4,959 | 2003-10-13 |
| 2005-11-28 | UNP | peak5 | $2,175 | 2000-03-27 |
| 2005-12-05 | ABT | peak5 | $981 | 2005-11-28 |
| … 67 more | | | | |

## Honest limits

- No Stage-2 human judgment; the DJIA run showed the deterministic sweep
is procyclical (vetoes recessions) — cohort VETOED-vs-PASSED quantifies
what that judgment layer would have to beat.
- No sells; portfolio cash can exhaust (cohorts are the cash-free view).
- Delisted names' final value = last trade (bankruptcy often overstates,
buyouts understate).
- F>=7 + $500M cap is a mechanical stand-in for the hand-built watchlist.
- ADRs are not excluded (store lacks the category column).
