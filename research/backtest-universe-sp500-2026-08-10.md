# S&P 500 point-in-time funnel backtest — 1998-01-05 .. 2015-12-28

*Generated 2026-08-10 by `scripts/backtest_universe.py`. 951 tickers swept; 766 produced episodes. Filters: mcap >= $500M, price >= $3.0, F >= 7.*

*Requested start 1995-01-01; price data begins 1998-01-05 (see reports/research-backtest-data-costs-2026-08-09.md).*

| | Funnel portfolio | Survivorship-free EW market |
|---|---|---|
| Final value of $100,000 | $578,841 | $395,421 |
| CAGR | +10.3% | +7.9% |
| Fills / names | 100 / 21 | all |
| Sells | 0 (recycling off) | — |
| Cash remaining | $0 | — |

*SPY (div-adj) over the same window: $289,108 — cap-weighted control; VOO's inception is 2010, hence SPY.*

## Growth of $10,000 (first week of each year)

| Year | Funnel | EW market |
|---|---|---|
| 1998 | $100,000 | $100,000 |
| 1999 | $105,752 | $115,298 |
| 2000 | $112,037 | $131,119 |
| 2001 | $129,266 | $135,286 |
| 2002 | $132,908 | $127,083 |
| 2003 | $131,516 | $110,113 |
| 2004 | $173,977 | $139,611 |
| 2005 | $219,570 | $161,288 |
| 2006 | $238,752 | $180,890 |
| 2007 | $279,926 | $202,420 |
| 2008 | $329,933 | $215,795 |
| 2009 | $211,905 | $158,541 |
| 2010 | $270,593 | $197,637 |
| 2011 | $347,593 | $233,595 |
| 2012 | $326,769 | $241,709 |
| 2013 | $402,154 | $279,560 |
| 2014 | $521,095 | $337,706 |
| 2015 | $610,297 | $390,703 |
| Final | $578,841 | $395,421 |

## Cohort forward returns (median excess vs EW market, identical windows)

| Cohort | n | 1y | 3y | 5y |
|---|---|---|---|---|
| PASSED (funnel buys) | 877 | +2.4% (844) | +2.5% (820) | +0.7% (669) |
| VETOED (trap sweep) | 1658 | -0.5% (1613) | -4.5% (1566) | -11.1% (1317) |
| NAIVE 30% dip (no gates) | 5210 | -1.5% (5018) | -3.7% (4879) | -8.0% (4425) |

## Fills (first 40)

| Week | Ticker | Lot | Fill | Band pctl |
|---|---|---|---|---|
| 1998-05-18 | AMP1 | 1 | $34.71 | 13% |
| 1998-06-15 | GLW | 1 | $7.37 | 0% |
| 1998-06-22 | AMP1 | 1 | $34.52 | 13% |
| 1998-06-22 | GLW | 2 | $7.30 | 0% |
| 1998-07-06 | AMP1 | 2 | $33.92 | 13% |
| 1998-07-13 | AMP1 | 3 | $31.88 | 7% |
| 1998-07-13 | GLW | 3 | $7.19 | 0% |
| 1998-07-27 | DE | 1 | $12.25 | 7% |
| 1998-08-03 | APD | 1 | $16.80 | 0% |
| 1998-08-03 | DE | 2 | $11.89 | 7% |
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
| 1998-08-31 | HPQ | 2 | $6.12 | 0% |
| 1998-08-31 | ITW | 1 | $13.25 | 19% |
| 1998-08-31 | MRO | 2 | $4.39 | 0% |
| 1998-08-31 | NOC | 3 | $16.22 | 17% |
| 1998-08-31 | PCAR | 2 | $2.02 | 0% |
| 1998-09-07 | DALRQ | 2 | $45.91 | 0% |
| 1998-09-07 | DOV | 2 | $10.77 | 19% |
| 1998-09-07 | ITW | 2 | $12.78 | 19% |
| 1998-09-07 | PCAR | 3 | $2.01 | 0% |
| 1998-10-05 | PCAR | 1 | $1.88 | 0% |
| 1998-11-16 | GWW | 1 | $27.88 | 6% |
| 1998-11-30 | GWW | 2 | $27.07 | 6% |
| 1998-12-07 | PCAR | 1 | $2.15 | 0% |
| 1998-12-14 | GWW | 3 | $26.04 | 0% |
| 1998-12-14 | NOC | 1 | $18.59 | 15% |
| 1998-12-14 | PCAR | 2 | $2.02 | 0% |
| 1998-12-21 | PCAR | 3 | $2.07 | 0% |
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
