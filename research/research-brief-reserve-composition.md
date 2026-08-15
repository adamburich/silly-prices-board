# Research brief — what should the parked reserve hold?

**For an agent working the Silly Prices harness. Read this whole file before
running anything.** It contains the question, what is already known (including
several results that were wrong on first pass), the discipline this project
holds itself to, and what will and will not count as an answer.

---

## 1. The question

The funnel parks idle money rather than holding cash (`--park`). What that
reserve holds is currently **two-thirds a synthetic mega-cap growth index,
one-third SPY**, rebalanced weekly.

Three questions, in priority order:

1. **Should the composition depend on the FUNDING PROFILE?** Contributions and
   lump sums appear to want opposite things.
2. **What composition is right for each profile?** Including whether a cash /
   T-bill sleeve belongs in it, and whether it should be a constant mix or a
   spend-first buffer.
3. **Should the composition depend on the DETECTED MARKET REGIME?** See §6 —
   this one carries a much higher burden of proof than the other two.

## 2. Why this is open — the finding that started it

The 2/3 growth figure was chosen on **contribution-funded windows only**
(1998–2019, 2000–2019, 2010–2019, all `--inject 20000`). Under lump-sum funding
it inverts.

Lump sum $400k at Jan 2000, sweeping the growth fraction:

| growth share | vs EW | vs SPY | vs BRK.B | trough |
|---|---|---|---|---|
| 0 | −24.16% | +22.42% | −38.52% | $246,345 |
| 25% | −27.60% | +16.87% | −41.31% | $218,181 |
| 50% | −35.05% | +4.83% | −47.35% | $185,047 |
| 66.7% (adopted) | −40.89% | −4.58% | −52.08% | $162,712 |
| 100% | −53.20% | −24.45% | −62.06% | $123,093 |

Monotone, and the **opposite sign** to the contribution-funded sweep that chose
2/3 (which measured +1.50 / +1.99 / +4.07 for the same dial). The parameter is
not funding-neutral, and that is the actual finding — not any particular value.

### The mechanism, which the research should respect

For a lump sum the reserve **is** the portfolio for years. In the 2000–2019 run
it was 99.8% of the account at week 0 and still 71% in late 2002. So its
composition is the dominant allocation decision, not a parking detail. For
contribution funding the reserve is small against future inflows, a drawdown in
it is repaired by later contributions, and the same dial is nearly free.

The reserve has two jobs in tension — earn while waiting, and **be there when
you need it**. Which dominates is a function of whether more money is coming.
In the 2000 lump run the reserve was down to **$166** by March 2009: destroyed
*and* spent, with no powder for the crisis the strategy exists to exploit.

## 3. Results already in hand (do not re-derive)

Artifacts are in `reports/`, dated `2026-08-12`, tag prefix
`backtest-universe-sp500-trimonly-b5s97-`.

Lump sum, eight windows, `--lots 1200`, vs BRK.B:

| window | 2/3 growth | 50%cash/25/25 mix | 50% cash-first |
|---|---|---|---|
| 1998–2026 | +1.6% | −7.3% | −6.7% |
| 1998–2019 | −11.0% | −8.0% | −6.0% |
| 2000–2026 | −40.6% | −39.7% | −29.8% |
| 2000–2020 | −34.7% | −28.0% | −19.3% |
| 2000–2019 | −44.4% | −36.4% | −28.3% |
| 2010–2026 | +73.2% | −20.8% | −26.3% |
| 2010–2019 | +29.3% | −19.6% | −23.1% |
| 2020–2026 | +36.9% | −7.2% | −10.8% |
| **stdev** | **39.5** | 12.2 | **9.1** |
| **worst** | −44.4% | −39.7% | **−29.8%** |
| **beats BRK** | **4/8** | 0/8 | 0/8 |

Read: a cash sleeve buys a 4x reduction in dispersion and gives up ever
winning. `--cash-first` (spend cash before selling the risk sleeve, refill only
at running highs) beats the constant mix on both dispersion and worst case —
the constant mix rebalances *into* the falling leg, which is the opposite of
holding a buffer.

**Only BRK.B was measured.** That is one comparator and probably the wrong one
for a conservative client. See §5.

## 4. Mistakes already made — do not repeat them

These cost real time today. Each is a live trap in this harness.

1. **The filename tag omits `--lots`.** Runs differing only in lot size
   overwrite each other silently. Copy each artifact aside before the next run,
   or you will report one configuration's numbers under another's label.
2. **Lot size dominates the reserve tilt for lump sums.** Holding the lot at a
   flat $200 while capital went to $400k left ~89% parked through 2000–02 and
   produced a −52% vs BRK.B that was mostly a sizing artifact. Always size lots
   proportionally (`--lots 1200`, i.e. capital ÷ 1200) unless lot size is the
   variable under test.
3. **A single-window sweep found $2,000 and $10,000 lots turning −52% into
   +54% and +211%.** Across all eight windows the same rule family barely moved
   dispersion (51.2 → 45.9 stdev). Classic point optimum. **Never conclude from
   one window.**
4. **`--park-cash` without `--cash-first` is a constant mix, not a buffer.**
   The help text says so. They are different mechanisms with different results.

## 5. What a real answer looks like

**Sweep across ALL eight windows, per funding profile.** One window proves
nothing here; the campaign's own holdout Spearman ρ between development and
reserve rankings is **0.24**. Report dispersion, not just central tendency.

**Three funding profiles, all in `reports/backtest-current-2026-08-12.html`:**

| profile | flags |
|---|---|
| A — contributions | `--inject 20000 --lot-frac 0.01` |
| B — lump sum | `--capital <A's total> --lots 1200` |
| C — live (10k seed + 1k/mo) | `--inject 12000 --open-with 10000 --lot-frac 0.008333` |

Profile C is the one that matters operationally — it is the live paper account
— and it is currently the least tested. Its seed is ~4% of eventual
contributions, which puts it close to A; verify that rather than assume it.

**Benchmark against more than Berkshire.** BRK.B is one comparator and it went
*up* through 2000–02, which makes it the sharpest possible foil for a
growth-tilted reserve. Report vs the survivorship-free EW market, SPY, QQQ and
BRK.B, and note that a conservative client's real alternative may be a 60/40 or
a savings account — neither of which is in the harness. Say so rather than
implying Berkshire is the bar.

**Report the cost of insurance explicitly.** Parking exists because cash drag
was measured and material — it removed ~78% of the lot-size gradient. Any cash
recommendation must state what it gives up in the windows where it loses, not
only what it saves in the windows where it helps.

**Prefer a class-level rule to a fitted number.** "More cash as the seed-to-
total-funding ratio rises" is the kind of finding that transfers. "51% cash" is
not. If the evidence only supports a direction, report a direction.

## 6. The regime question — higher bar

Whether the split should key off the detected market regime (`sillyprices/regime.py`,
5 states: BOOM / STALL / DECLINE / BEAR / CRISIS) is worth testing, but it is a
**market-timing rule**, and this project has refused fitted timing parameters
before. It must clear all of the following or be reported as unsupported:

1. **Beat the best STATIC mix** on the same windows, not just beat the current
   one. A regime rule that loses to a constant 30% cash has proven nothing.
2. **Beat a randomised-regime null.** Shuffle the regime labels (preserving the
   number of weeks in each state) and re-run several seeds. If the real labels
   do not clearly exceed the shuffled ones, the "signal" is the switching
   frequency, not the regime.
3. **Beat a lagged-regime control.** Re-run with labels delayed 4 and 13 weeks.
   A rule that survives a one-quarter lag is robust; one that dies is trading
   on a coincidence of timing.
4. **Be decidable on the day.** `regime.py` labels are trailing-window only and
   PIT-clean by construction — verify no lookahead is introduced by the
   *reserve* rule itself (e.g. rebalancing on a label that is revised later).
5. **Have few parameters, declared in advance.** A 5-state × 3-asset mapping is
   fifteen free numbers. Prefer a two-state collapse (calm vs stressed) or a
   single monotone dial keyed to one regime variable, and say which you fixed
   before seeing results.

**Known data hazard:** the HY OAS series has a 122-week blackout
(2021-04-09 → 2023-08-04) sitting inside the 2020–2026 window. Confirm how
`regime.py` labels that stretch before drawing conclusions from it.

## 7. Holdout status — read before planning validation

**There is no unused window left.** 2020–2026 was the reserve, was fired once on
the SPY-parked configuration, and has since been re-run under the live one. It
is development data now. Any claim of out-of-sample validation must come from
time passing, not from a window in this dataset.

Practical consequence: report robustness as *consistency across windows and
across nulls*, not as "it held up out of sample." Do not manufacture a holdout
by carving 2024–2026 out of a window the configuration has already seen.

## 8. Deliverables

1. A results table per funding profile: composition × window, vs all four
   benchmarks, with dispersion and worst case.
2. A recommendation **per funding profile**, stated as a class-level rule where
   the evidence supports only that.
3. An explicit statement of what the recommendation costs in its losing windows.
4. For the regime question: the four nulls in §6, or a clear "not supported".
5. A decision record in `reports/`, following the house pattern — the reasoning
   and the rejected alternatives, not only the chosen number. See
   `reports/megacap-growth-reserve-2026-08-11.md`, which records its own 2/3
   choice as "a call on a dial, NOT a derived result" and is the reason this
   brief exists.

## 9. Operating notes

- Store: `%LOCALAPPDATA%\sillyprices\sharadar.sqlite3`, rebuildable from
  `data/bulk/*.zip` via `python scripts/pull_sharadar_bulk.py --skip-download`.
  `TABLES` must include `sp500` and `funds` or the store is silently unusable.
- SPY, QQQ, MGK and MGC live in the `funds` export, not `stocks`; `ETF_KEEP`
  controls which are ingested.
- A full window takes roughly 1–3 minutes. Eight windows × several compositions
  is a background job, not an interactive one.
- Rebuild the display with `python scripts/build_backtest_page.py`.
