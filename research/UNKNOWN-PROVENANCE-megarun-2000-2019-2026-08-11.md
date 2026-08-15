# S&P 500 point-in-time funnel backtest — 2000-01-03 .. 2019-12-30

*Generated 2026-08-11 by `scripts/backtest_universe.py`. 976 tickers swept; 820 produced episodes. Filters: mcap >= $500M, price >= $3.0, F >= 7.*

*Requested start 2000-01-01; price data begins 2000-01-03 (see reports/research-backtest-data-costs-2026-08-09.md).*

| | Funnel portfolio | Survivorship-free EW market |
|---|---|---|
| Contributed ($1,667 each month)
| Final value | $1,362,792 | $1,135,662 |
| Growth multiple (identical cashflows) | 3.41× | 2.84× |
| Fills / names | 1742 / 199 | all |
| Sells | 56 (0 band-top, 56 live-TRIM, 0 thesis-break, 0 peak-dip, 0 peak-5-day; 13 of the TRIMs via the negative-EBIT EV/S+ratchet fallback) | — |
| Reserve at end (SPY-parked) | $429,793 | — |
| Starved signals (BUY, no lot of powder) | 1785 in 71 weeks | — |
| Blocked by the monthly spend cap | 0 | — |
| Fills the CRISIS exemption allowed | 0 | — |

*SPY (div-adj) over the same window: $1,157,172 — cap-weighted control; VOO's inception is 2010, hence SPY.*

## Portfolio value (first week of each year; all columns receive the same contributions)

| Year | Funnel | EW market |
|---|---|---|
| 2000 | $1,667 | $1,667 |
| 2001 | $25,145 | $23,518 |
| 2002 | $54,654 | $43,979 |
| 2003 | $67,657 | $59,051 |
| 2004 | $115,388 | $98,311 |
| 2005 | $152,790 | $136,899 |
| 2006 | $189,488 | $175,496 |
| 2007 | $234,852 | $219,647 |
| 2008 | $251,996 | $246,905 |
| 2009 | $197,403 | $198,343 |
| 2010 | $284,587 | $263,490 |
| 2011 | $362,965 | $331,251 |
| 2012 | $383,662 | $358,970 |
| 2013 | $458,114 | $430,640 |
| 2014 | $618,543 | $550,062 |
| 2015 | $726,657 | $637,621 |
| 2016 | $735,099 | $645,904 |
| 2017 | $869,672 | $758,353 |
| 2018 | $1,073,397 | $908,668 |
| 2019 | $1,069,182 | $898,176 |
| Final | $1,362,792 | $1,135,662 |

## Cohort forward returns (median excess vs EW market, identical windows)

| Cohort | n | 1y | 3y | 5y |
|---|---|---|---|---|
| PASSED (funnel buys) | 614 | +3.9% (596) | +5.3% (576) | +1.6% (543) |
| VETOED (trap sweep) | 1071 | +1.3% (1055) | +0.3% (1016) | +2.7% (966) |
| NAIVE 30% dip (no gates) | 5469 | -0.8% (5314) | -6.1% (4911) | -11.4% (4484) |

## Fills (first 40)

| Week | Ticker | Lot | Fill | Band pctl |
|---|---|---|---|---|
| 2000-01-03 | NEE | 1 | $2.23 | 0% |
| 2000-01-03 | PGR | 1 | $3.03 | 0% |
| 2000-01-03 | SHW | 1 | $4.51 | 0% |
| 2000-01-10 | PGR | 2 | $2.97 | 0% |
| 2000-01-10 | SHW | 2 | $4.27 | 0% |
| 2000-01-17 | SHW | 3 | $4.23 | 0% |
| 2000-01-24 | BC | 1 | $13.43 | 0% |
| 2000-01-24 | PGR | 3 | $2.83 | 0% |
| 2000-02-07 | BC | 2 | $11.48 | 0% |
| 2000-02-07 | NEE | 1 | $2.25 | 0% |
| 2000-02-07 | PGR | 4 | $2.58 | 0% |
| 2000-02-14 | HSH | 1 | $14.91 | 0% |
| 2000-02-14 | NEE | 2 | $2.20 | 0% |
| 2000-02-14 | PGR | 5 | $2.23 | 0% |
| 2000-02-14 | TT | 1 | $8.28 | 0% |
| 2000-02-21 | HSH | 2 | $14.47 | 0% |
| 2000-03-06 | HSH | 3 | $12.36 | 0% |
| 2000-03-06 | TT | 2 | $7.51 | 0% |
| 2000-03-13 | BA | 1 | $21.10 | 0% |
| 2000-03-13 | PGR | 6 | $2.11 | 0% |
| 2000-03-13 | TT | 3 | $7.15 | 0% |
| 2000-03-20 | PCAR | 1 | $2.53 | 0% |
| 2000-03-27 | CINF | 1 | $12.33 | 0% |
| 2000-03-27 | KMB | 1 | $20.86 | 5% |
| 2000-03-27 | UNP | 1 | $5.79 | 5% |
| 2000-04-10 | HSH | 1 | $14.96 | 0% |
| 2000-04-17 | WOR | 1 | $3.54 | 0% |
| 2000-04-24 | HSH | 2 | $14.63 | 0% |
| 2000-04-24 | PCAR | 2 | $2.45 | 0% |
| 2000-04-24 | WOR | 2 | $3.45 | 0% |
| 2000-05-01 | HSH | 3 | $13.29 | 0% |
| 2000-05-08 | PCAR | 3 | $2.38 | 0% |
| 2000-05-22 | PCAR | 4 | $2.33 | 0% |
| 2000-05-29 | PCAR | 5 | $2.29 | 0% |
| 2000-06-05 | HSH | 1 | $15.24 | 0% |
| 2000-06-12 | DDS | 1 | $8.16 | 5% |
| 2000-06-12 | PCAR | 6 | $2.28 | 0% |
| 2000-06-19 | JCI | 1 | $4.58 | 0% |
| 2000-06-19 | PCAR | 7 | $2.25 | 0% |
| 2000-06-19 | TT | 1 | $8.43 | 0% |
| … 1702 more | | | | |

## Sells (first 40)

| Week | Ticker | Reason | Proceeds | Episode opened |
|---|---|---|---|---|
| 2000-10-02 | NEE | trim | $1,028 | 2000-02-07 |
| 2002-01-14 | GIS | trim | $938 | 2000-08-21 |
| 2002-01-28 | GWW | trim | $334 | 2001-03-26 |
| 2002-03-11 | MOLX | trim | $261 | 2001-09-17 |
| 2002-04-08 | GAP | trim | $162 | 2000-10-02 |
| 2002-04-29 | BBBYQ | trim | $341 | 2001-09-17 |
| 2003-12-22 | JCI | trim | $2,327 | 2000-12-11 |
| 2004-04-12 | TUPBQ | trim | $865 | 2003-06-23 |
| 2004-12-20 | BMY | trim | $662 | 2004-03-22 |
| 2006-03-13 | MEDI1 | trim | $668 | 2002-04-08 |
| 2008-03-03 | UPS | trim | $875 | 2006-08-07 |
| 2008-06-16 | DDS | trim | $237 | 2000-06-12 |
| 2008-09-01 | HSH | trim | $1,793 | 2000-06-05 |
| 2010-02-15 | ROK | trim | $187 | 2007-04-30 |
| 2010-03-01 | APA | trim | $9,909 | 2006-06-12 |
| 2010-03-01 | HOG | trim | $114 | 2006-03-20 |
| 2010-04-26 | ADSK | trim | $1,357 | 2008-11-03 |
| 2010-09-27 | HWM | trim | $395 | 2007-11-26 |
| 2011-02-28 | MRK | trim | $3,962 | 2004-10-04 |
| 2012-04-23 | MO | trim | $15,751 | 2002-09-23 |
| 2013-01-28 | AVP | trim | $695 | 2009-03-02 |
| 2013-08-05 | TYC | trim | $3,664 | 2012-10-01 |
| 2013-11-11 | SEE | trim | $8,054 | 2011-12-19 |
| 2013-11-18 | HMA1 | trim | $316 | 2002-11-11 |
| 2013-12-09 | CPB | trim | $1,286 | 2008-01-14 |
| 2014-06-02 | FRX1 | trim | $8,343 | 2011-09-26 |
| 2014-09-08 | ADBE | trim | $2,364 | 2008-12-01 |
| 2015-01-12 | FDO | trim | $20,890 | 2010-01-04 |
| 2015-02-09 | QLGC | trim | $1,103 | 2005-10-24 |
| 2015-02-16 | EFX | trim | $6,127 | 2009-07-06 |
| 2015-02-23 | BMS | trim | $684 | 2003-03-10 |
| 2015-06-22 | CAG | trim | $2,653 | 2008-06-30 |
| 2015-07-20 | LXK | trim | $10,268 | 2012-04-30 |
| 2015-10-05 | DRI | trim | $3,095 | 2003-05-05 |
| 2015-11-02 | MXIM | trim | $4,206 | 2007-04-02 |
| 2015-11-30 | BDX | trim | $2,602 | 2008-11-03 |
| 2016-05-09 | GHC | trim | $551 | 2010-08-16 |
| 2017-03-06 | VTRS | trim | $451 | 2007-03-05 |
| 2017-06-05 | MCHP | trim | $4,156 | 2012-05-14 |
| 2017-08-07 | ABT | trim | $4,785 | 2013-01-07 |
| … 16 more | | | | |

## Honest limits

- No Stage-2 human judgment; the DJIA run showed the deterministic sweep
is procyclical (vetoes recessions) — cohort VETOED-vs-PASSED quantifies
what that judgment layer would have to beat.
- No sells; portfolio cash can exhaust (cohorts are the cash-free view).
- Delisted names' final value = last trade (bankruptcy often overstates,
buyouts understate).
- F>=7 + $500M cap is a mechanical stand-in for the hand-built watchlist.
- ADRs are not excluded (store lacks the category column).
