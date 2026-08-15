# S&P 500 point-in-time funnel backtest — 1998-01-05 .. 2019-12-30

*Generated 2026-08-11 by `scripts/backtest_universe.py`. 1047 tickers swept; 834 produced episodes. Filters: mcap >= $500M, price >= $3.0, F >= 7.*

*Requested start 1998-01-01; price data begins 1998-01-05 (see reports/research-backtest-data-costs-2026-08-09.md).*

| | Funnel portfolio | Survivorship-free EW market |
|---|---|---|
| Contributed ($1,667 each month)
| Final value | $1,682,262 | $1,562,449 |
| Growth multiple (identical cashflows) | 3.82× | 3.55× |
| Fills / names | 2018 / 210 | all |
| Sells | 63 (0 band-top, 63 live-TRIM, 0 thesis-break, 0 peak-dip, 0 peak-5-day; 13 of the TRIMs via the negative-EBIT EV/S+ratchet fallback) | — |
| Reserve at end (SPY-parked) | $524,692 | — |
| Starved signals (BUY, no lot of powder) | 1274 in 44 weeks | — |
| Blocked by the monthly spend cap | 0 | — |
| Fills the CRISIS exemption allowed | 0 | — |

*SPY (div-adj) over the same window: $1,317,086 — cap-weighted control; VOO's inception is 2010, hence SPY.*

## Portfolio value (first week of each year; all columns receive the same contributions)

| Year | Funnel | EW market |
|---|---|---|
| 1998 | $1,667 | $1,667 |
| 1999 | $25,025 | $23,446 |
| 2000 | $53,730 | $47,647 |
| 2001 | $78,842 | $69,074 |
| 2002 | $109,549 | $85,096 |
| 2003 | $115,907 | $93,055 |
| 2004 | $178,956 | $141,600 |
| 2005 | $225,480 | $185,401 |
| 2006 | $265,761 | $229,208 |
| 2007 | $325,165 | $277,829 |
| 2008 | $350,434 | $315,974 |
| 2009 | $261,464 | $248,875 |
| 2010 | $371,825 | $334,017 |
| 2011 | $476,570 | $417,332 |
| 2012 | $487,198 | $452,025 |
| 2013 | $586,814 | $543,532 |
| 2014 | $780,082 | $678,688 |
| 2015 | $909,238 | $806,549 |
| 2016 | $914,350 | $824,084 |
| 2017 | $1,080,615 | $958,314 |
| 2018 | $1,335,250 | $1,183,006 |
| 2019 | $1,319,911 | $1,160,687 |
| Final | $1,682,262 | $1,562,449 |

## Cohort forward returns (median excess vs EW market, identical windows)

| Cohort | n | 1y | 3y | 5y |
|---|---|---|---|---|
| PASSED (funnel buys) | 632 | +2.9% (614) | +2.8% (594) | -4.1% (561) |
| VETOED (trap sweep) | 1093 | +0.5% (1077) | -2.6% (1038) | -2.8% (988) |
| NAIVE 30% dip (no gates) | 6003 | -1.5% (5848) | -5.7% (5445) | -9.9% (5018) |

## Fills (first 40)

| Week | Ticker | Lot | Fill | Band pctl |
|---|---|---|---|---|
| 1998-06-15 | GLW | 1 | $7.37 | 0% |
| 1998-06-22 | GLW | 2 | $7.30 | 0% |
| 1998-07-13 | GLW | 3 | $7.19 | 0% |
| 1998-07-20 | GLW | 4 | $7.01 | 0% |
| 1998-07-27 | AMP1 | 1 | $29.28 | 0% |
| 1998-07-27 | GLW | 5 | $6.33 | 0% |
| 1998-08-03 | APD | 1 | $16.80 | 0% |
| 1998-08-10 | APD | 2 | $16.47 | 0% |
| 1998-08-10 | HPQ | 1 | $6.45 | 12% |
| 1998-08-17 | MRO | 1 | $4.91 | 0% |
| 1998-08-24 | PCAR | 1 | $2.07 | 0% |
| 1998-08-31 | APD | 3 | $14.70 | 0% |
| 1998-08-31 | DALRQ | 1 | $49.95 | 0% |
| 1998-08-31 | DE | 1 | $9.51 | 0% |
| 1998-08-31 | GLW | 1 | $5.26 | 0% |
| 1998-08-31 | GWW | 1 | $25.03 | 0% |
| 1998-08-31 | HPQ | 2 | $6.12 | 0% |
| 1998-08-31 | MRO | 2 | $4.39 | 0% |
| 1998-08-31 | PCAR | 2 | $2.02 | 0% |
| 1998-09-07 | DALRQ | 2 | $45.91 | 0% |
| 1998-09-07 | GLW | 2 | $5.17 | 0% |
| 1998-09-07 | PCAR | 3 | $2.01 | 0% |
| 1998-09-14 | DE | 2 | $9.06 | 0% |
| 1998-09-21 | DE | 3 | $8.86 | 0% |
| 1998-10-05 | APD | 4 | $14.57 | 0% |
| 1998-10-05 | DE | 4 | $8.82 | 0% |
| 1998-10-05 | GWW | 1 | $25.47 | 0% |
| 1998-10-05 | PCAR | 1 | $1.88 | 0% |
| 1998-11-30 | DE | 1 | $10.14 | 0% |
| 1998-12-07 | DE | 2 | $9.67 | 0% |
| 1998-12-07 | PCAR | 1 | $2.15 | 0% |
| 1998-12-14 | DE | 3 | $9.00 | 0% |
| 1998-12-14 | GWW | 1 | $26.04 | 0% |
| 1998-12-14 | PCAR | 2 | $2.02 | 0% |
| 1998-12-21 | DE | 4 | $8.63 | 0% |
| 1998-12-21 | GWW | 2 | $24.99 | 0% |
| 1998-12-21 | PCAR | 3 | $2.07 | 0% |
| 1998-12-28 | GWW | 3 | $24.83 | 0% |
| 1998-12-28 | PCAR | 4 | $2.05 | 0% |
| 1999-01-25 | GWW | 4 | $24.07 | 0% |
| … 1978 more | | | | |

## Sells (first 40)

| Week | Ticker | Reason | Proceeds | Episode opened |
|---|---|---|---|---|
| 1999-03-29 | AMP1 | trim | $381 | 1998-07-27 |
| 2000-01-24 | GLW | trim | $7,375 | 1998-08-31 |
| 2000-03-06 | HPQ | trim | $1,175 | 1998-08-10 |
| 2000-10-02 | NEE | trim | $4,312 | 2000-01-31 |
| 2002-01-14 | GIS | trim | $1,259 | 2000-08-21 |
| 2002-01-28 | GWW | trim | $2,056 | 2001-03-26 |
| 2002-03-11 | MOLX | trim | $261 | 2001-09-17 |
| 2002-04-08 | GAP | trim | $162 | 2000-10-02 |
| 2002-04-29 | BBBYQ | trim | $341 | 2001-09-17 |
| 2003-12-22 | JCI | trim | $3,299 | 2000-12-11 |
| 2004-04-12 | TUPBQ | trim | $865 | 2003-06-23 |
| 2004-12-20 | BMY | trim | $662 | 2004-03-22 |
| 2006-03-13 | MEDI1 | trim | $668 | 2002-04-08 |
| 2008-03-03 | UPS | trim | $875 | 2006-08-07 |
| 2008-06-16 | DDS | trim | $237 | 2000-06-12 |
| 2008-09-01 | HSH | trim | $2,068 | 2000-06-05 |
| 2010-02-15 | ROK | trim | $187 | 2007-04-30 |
| 2010-03-01 | APA | trim | $9,909 | 2006-06-12 |
| 2010-03-01 | HOG | trim | $114 | 2006-03-20 |
| 2010-04-26 | ADSK | trim | $1,665 | 2008-10-20 |
| 2010-09-27 | HWM | trim | $395 | 2007-11-26 |
| 2011-01-31 | SWK | trim | $961 | 2000-10-16 |
| 2011-02-28 | MRK | trim | $5,151 | 2008-09-22 |
| 2012-04-23 | MO | trim | $15,751 | 2002-09-23 |
| 2013-01-28 | AVP | trim | $695 | 2009-03-02 |
| 2013-08-05 | TYC | trim | $3,664 | 2012-10-01 |
| 2013-11-11 | SEE | trim | $9,597 | 2011-12-19 |
| 2013-11-18 | HMA1 | trim | $316 | 2002-11-11 |
| 2013-12-09 | CPB | trim | $1,286 | 2008-01-14 |
| 2014-04-07 | TIF | trim | $3,436 | 2008-07-14 |
| 2014-06-02 | FRX1 | trim | $12,827 | 2011-09-26 |
| 2014-09-08 | ADBE | trim | $2,364 | 2008-12-01 |
| 2015-01-12 | FDO | trim | $20,890 | 2010-01-04 |
| 2015-02-09 | QLGC | trim | $1,103 | 2005-10-24 |
| 2015-02-16 | EFX | trim | $6,882 | 2009-07-06 |
| 2015-02-23 | BMS | trim | $684 | 2003-03-10 |
| 2015-03-02 | APD | trim | $12,608 | 2008-10-27 |
| 2015-06-22 | CAG | trim | $2,653 | 2008-06-30 |
| 2015-07-20 | LXK | trim | $10,268 | 2012-04-30 |
| 2015-10-05 | DRI | trim | $3,095 | 2003-05-05 |
| … 23 more | | | | |

## Honest limits

- No Stage-2 human judgment; the DJIA run showed the deterministic sweep
is procyclical (vetoes recessions) — cohort VETOED-vs-PASSED quantifies
what that judgment layer would have to beat.
- No sells; portfolio cash can exhaust (cohorts are the cash-free view).
- Delisted names' final value = last trade (bankruptcy often overstates,
buyouts understate).
- F>=7 + $500M cap is a mechanical stand-in for the hand-built watchlist.
- ADRs are not excluded (store lacks the category column).
