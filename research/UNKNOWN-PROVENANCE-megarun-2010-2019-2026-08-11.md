# S&P 500 point-in-time funnel backtest — 2010-01-04 .. 2019-12-30

*Generated 2026-08-11 by `scripts/backtest_universe.py`. 709 tickers swept; 519 produced episodes. Filters: mcap >= $500M, price >= $3.0, F >= 7.*

*Requested start 2010-01-01; price data begins 2010-01-04 (see reports/research-backtest-data-costs-2026-08-09.md).*

| | Funnel portfolio | Survivorship-free EW market |
|---|---|---|
| Contributed ($1,667 each month)
| Final value | $394,475 | $371,072 |
| Growth multiple (identical cashflows) | 1.97× | 1.86× |
| Fills / names | 577 / 91 | all |
| Sells | 12 (0 band-top, 12 live-TRIM, 0 thesis-break, 0 peak-dip, 0 peak-5-day; 0 of the TRIMs via the negative-EBIT EV/S+ratchet fallback) | — |
| Reserve at end (SPY-parked) | $175,042 | — |
| Starved signals (BUY, no lot of powder) | 293 in 29 weeks | — |
| Blocked by the monthly spend cap | 0 | — |
| Fills the CRISIS exemption allowed | 0 | — |

*SPY (div-adj) over the same window: $408,032 — cap-weighted control; VOO's inception is 2010, hence SPY.*

## Portfolio value (first week of each year; all columns receive the same contributions)

| Year | Funnel | EW market |
|---|---|---|
| 2010 | $1,667 | $1,667 |
| 2011 | $25,279 | $24,715 |
| 2012 | $44,256 | $44,643 |
| 2013 | $69,805 | $74,027 |
| 2014 | $120,986 | $117,453 |
| 2015 | $155,808 | $155,246 |
| 2016 | $168,667 | $175,392 |
| 2017 | $219,313 | $218,353 |
| 2018 | $290,438 | $276,867 |
| 2019 | $299,009 | $283,448 |
| Final | $394,475 | $371,072 |

## Cohort forward returns (median excess vs EW market, identical windows)

| Cohort | n | 1y | 3y | 5y |
|---|---|---|---|---|
| PASSED (funnel buys) | 203 | +2.5% (185) | +4.0% (164) | -8.6% (132) |
| VETOED (trap sweep) | 315 | +0.4% (299) | +4.7% (261) | +6.2% (210) |
| NAIVE 30% dip (no gates) | 1756 | -1.2% (1601) | -10.9% (1198) | -31.6% (771) |

## Fills (first 40)

| Week | Ticker | Lot | Fill | Band pctl |
|---|---|---|---|---|
| 2010-01-04 | FDO | 1 | $25.50 | 2% |
| 2010-02-15 | GME | 1 | $3.22 | 0% |
| 2010-02-22 | GME | 2 | $3.21 | 0% |
| 2010-02-22 | NDAQ | 1 | $4.90 | 3% |
| 2010-03-01 | GME | 3 | $2.94 | 0% |
| 2010-04-05 | GME | 1 | $3.80 | 0% |
| 2010-05-17 | GME | 1 | $3.70 | 0% |
| 2010-05-17 | KG1 | 1 | $8.82 | 3% |
| 2010-05-24 | EBAY | 1 | $8.06 | 4% |
| 2010-05-24 | GME | 2 | $3.57 | 0% |
| 2010-05-24 | GOOGL | 1 | $11.84 | 5% |
| 2010-05-24 | KG1 | 2 | $8.60 | 3% |
| 2010-05-24 | NDAQ | 1 | $4.85 | 0% |
| 2010-05-31 | BIIB | 1 | $43.72 | 0% |
| 2010-06-07 | AFL | 1 | $13.76 | 3% |
| 2010-06-07 | CA1 | 1 | $14.89 | 0% |
| 2010-06-07 | GAP | 1 | $12.71 | 3% |
| 2010-06-07 | GME | 3 | $3.55 | 0% |
| 2010-06-07 | KG1 | 3 | $7.90 | 3% |
| 2010-06-07 | NDAQ | 2 | $4.82 | 0% |
| 2010-06-14 | BIIB | 2 | $43.13 | 0% |
| 2010-06-21 | GME | 4 | $3.17 | 0% |
| 2010-06-28 | BBY | 1 | $20.60 | 2% |
| 2010-06-28 | BIGGQ | 1 | $24.84 | 2% |
| 2010-06-28 | EBAY | 2 | $7.82 | 4% |
| 2010-06-28 | GAP | 2 | $12.38 | 3% |
| 2010-06-28 | GME | 5 | $3.15 | 0% |
| 2010-06-28 | GOOGL | 2 | $11.71 | 5% |
| 2010-06-28 | LLL1 | 1 | $59.10 | 0% |
| 2010-07-05 | ALL | 1 | $19.82 | 4% |
| 2010-07-05 | BBBYQ | 1 | $31.37 | 4% |
| 2010-07-05 | BBY | 2 | $19.82 | 2% |
| 2010-07-05 | BIGGQ | 2 | $24.75 | 2% |
| 2010-07-05 | CA1 | 1 | $14.13 | 0% |
| 2010-07-05 | DGX | 1 | $36.55 | 0% |
| 2010-07-05 | EBAY | 3 | $7.27 | 4% |
| 2010-07-05 | GAP | 3 | $12.02 | 3% |
| 2010-07-05 | GME | 6 | $3.07 | 0% |
| 2010-07-05 | GOOGL | 3 | $10.83 | 5% |
| 2010-07-05 | HD | 1 | $18.95 | 25% |
| … 537 more | | | | |

## Sells (first 40)

| Week | Ticker | Reason | Proceeds | Episode opened |
|---|---|---|---|---|
| 2013-11-11 | SEE | trim | $1,266 | 2011-07-18 |
| 2014-06-02 | FRX1 | trim | $2,548 | 2011-11-07 |
| 2015-01-12 | FDO | trim | $602 | 2010-01-04 |
| 2015-07-20 | LXK | trim | $5,156 | 2012-04-30 |
| 2016-05-09 | GHC | trim | $551 | 2010-08-16 |
| 2017-06-05 | MCHP | trim | $1,275 | 2012-05-14 |
| 2017-08-07 | ABT | trim | $660 | 2013-01-07 |
| 2017-11-20 | EMR | trim | $634 | 2012-07-16 |
| 2017-12-18 | RTX | trim | $1,572 | 2015-08-24 |
| 2018-01-29 | ITW | trim | $1,847 | 2011-11-14 |
| 2018-10-01 | CPB | trim | $438 | 2018-05-21 |
| 2019-09-09 | WDC | trim | $611 | 2010-08-16 |

## Honest limits

- No Stage-2 human judgment; the DJIA run showed the deterministic sweep
is procyclical (vetoes recessions) — cohort VETOED-vs-PASSED quantifies
what that judgment layer would have to beat.
- No sells; portfolio cash can exhaust (cohorts are the cash-free view).
- Delisted names' final value = last trade (bankruptcy often overstates,
buyouts understate).
- F>=7 + $500M cap is a mechanical stand-in for the hand-built watchlist.
- ADRs are not excluded (store lacks the category column).
