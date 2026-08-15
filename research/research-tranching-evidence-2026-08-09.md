# Is tranching the right aim? — literature review, 2026-08-09

Follow-up to [backtest-tranches-2026-08-09.md](backtest-tranches-2026-08-09.md), which found no
cost-basis edge for the current tranche rules over a lump sum at T1 (−5.6 / +6.6 / −0.4 pts across
the three windows). Question: is there meaningful external evidence that staged entry outperforms
a single larger purchase — and if not, what is tranching actually *for*?

## Short answer

No credible evidence exists that staging improves **raw expected return** — theory, index-level
empirics, and our own backtest all agree the lump sum wins on expectation, because cash waiting
for later tranches drags against a positive expected return. But the question is misposed for this
methodology. The literature contains one directly-on-point result — staging a **single security**
into an existing portfolio — where staged entry dominates lump entry on **risk-adjusted**
(certainty-equivalent) grounds. And the oldest theoretical critique of averaging-in cuts in our
favor once the triggers are information-based rather than clock- or price-based. Tranching is
defensible as **insurance against Stage-2 misdiagnosis**, judged on dispersion and worst-case,
not on cost basis.

## The evidence, by line

### 1. The anti-DCA literature is real but answers a different question

- **Constantinides (1979)**, the foundational result: predetermined dollar-cost averaging is
  dominated both by optimal lump-sum policies and — critically — by *sequential* strategies that
  condition on information arriving during the entry window
  ([JFQA](https://econpapers.repec.org/RePEc:cup:jfinqa:v:14:y:1979:i:02:p:443-450_00)).
- **Vanguard (2012, updated 2023)**: lump sum beat a 12-month DCA schedule in roughly two-thirds
  of historical periods across US/UK/AU markets, by ~1.5–2.4 pts over the horizon; stretching DCA
  to 36 months pushed lump-sum's win rate toward 90%
  ([Vanguard](https://investor.vanguard.com/investor-resources-education/news/lump-sum-investing-versus-cost-averaging-which-is-better),
  [original PDF](https://static.twentyoverten.com/5980d16bbfb1c93238ad9c24/rJpQmY8o7/Dollar-Cost-Averaging-Just-Means-Taking-Risk-Later-Vanguard.pdf)).
- **Hayley (2010)**: value averaging's apparent outperformance is an IRR hindsight-bias artifact;
  any strategy that scales purchases up after losses mechanically inflates its IRR without raising
  expected terminal wealth
  ([SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=1606347)). This caveat applies
  directly to any internal money-weighted-return evaluation of our tranches: **judge tranching on
  terminal wealth or CE, never on IRR/cost-basis flattery.**
- Index-level buy-the-dip studies point the same way: mechanical dip-buying underperforms passive
  most of the time and does worst when the dip begins a prolonged decline
  ([Alpha Architect](https://alphaarchitect.com/is-trend-following-better-than-buy-the-dip/),
  [Evidence Investor](https://www.evidenceinvestor.com/post/buy-the-dip)).

All of this concerns spreading a windfall into a **diversified portfolio** on a **clock**. Our
Stage 4 is neither: single names, event-triggered, conditional tranches.

### 2. The one directly relevant result supports staging — on risk-adjusted grounds

**Brennan, Li & Torous (2005), "Dollar Cost Averaging"**
([UCLA PDF](https://www.anderson.ucla.edu/documents/areas/fac/finance/dollarcostave.pdf)) is the
closest published analogue to our use case: buying an **individual stock to add to an
already-diversified portfolio** (CRSP, 1926–2003, certainty-equivalent framework):

- For the **market portfolio**, they reproduce the standard result: DCA is dominated by lump-sum
  and by a 50:50 rebalanced mix. No support for staging an index buy.
- For an **individual security added to a diversified portfolio**, staged entry **dominates lump
  entry for every risk-aversion level tested when the initial portfolio is value-weighted**, with
  an advantage of ~3–6% at the 36-month horizon even after handicapping the test by optimally
  levering the investor's starting portfolio. The advantage survives their Monte Carlo replication
  and is attributed to **transitory mispricing / mean reversion in individual names**
  (Poterba–Summers) plus idiosyncratic volatility.
- Corollary they draw: the same logic says **sell in a block, not in stages** — consistent with
  Stage 5 selling on thesis-break rather than scaling out.

Two qualifications matter for us. The advantage is in **certainty-equivalent**, not expected
wealth — expected wealth still favors lump sum. And their DCA executes over **12–48 months**;
the benefit peaks around 36. Our backtest showed the current constants complete entry by week 9 —
far too fast to capture the effect this paper documents. The literature's effective staging
horizon and our observed bottoming times (median 20–25 weeks to the low) point the same
direction: **if tranching, tranche slower.** The proposed T2/T3 redesign (drop-leg-only T2,
filing-gated T3) moves toward this without touching the window constant.

### 3. The dislocation context argues for *delayed* deployment specifically

Already documented in [02-signals.md](../docs/research/02-signals.md): PEAD and 52-week-high
momentum say fresh bad news keeps drifting down for 3–12 months; De Bondt–Thaler reversal pays
at 3–5 years. The Brandes Institute's "Falling Knives Around the World" found stocks down ≥60%
in 12 months outperformed their market benchmarks by ~6.6%/yr over the following three years —
but with wide dispersion and a real bankruptcy tail
([Quant Investing summary](https://www.quant-investing.com/blog/why-and-how-to-implement-a-falling-knife-investment-strategy-in-your-portfolio)).
The expected *path* after T1 is: more downside near-term, recovery at the multi-year horizon,
with a fat left tail (our own AIG episode: cost basis 154× the eventual low). A full-size
purchase at T1 maximizes exposure to exactly the segment of the path with the worst odds and
takes the full tail when Stage 2 is wrong.

### 4. Constantinides condemns our proxies, not our design

His theorem is not "never stage" — it is that **predetermined** schedules are dominated by
sequential strategies that use arriving information. A clock-DCA and a price-bounce T3 are
predetermined schedules in the relevant sense (they condition on nothing the market hasn't
already priced). A T3 gated on a post-T1 filing with a clean disqualifier sweep **is** a
sequential information strategy. The oldest and strongest theoretical result in this literature
therefore endorses the direction of the redesign: replace clock/price legs with event legs. This
is the same verdict the internal backtest reached empirically.

### 5. What tranching is actually for

Given raw expected return favors the lump sum, the honest case for keeping staged entry:

1. **Misdiagnosis insurance (the strongest).** Stage 2's temporary-vs-permanent call is the
   system's single point of failure, and a silly price and a value trap are statistically
   identical at T1. A ⅓ initial position caps the capital committed before disconfirming
   evidence can arrive; an information-gated T3 means the last third is never deployed into a
   name whose disqualifiers have started firing. The value of tranching scales with the false-
   positive rate of the diagnosis — which is unknown and unknowable at T1. This is position
   sizing under parameter uncertainty, not return enhancement.
2. **Risk-adjusted utility** (Brennan–Li–Torous): for single names, staging dominates in CE terms
   at realistic risk aversion. Real, but it assumes staging over 1–3 years, not 9 weeks.
3. **Behavioral executability** (Statman 1995): a rule that gets a second and third purchase
   made *during* a panic beats an "optimal" lump sum that never gets executed because the buyer
   flinched. Worth little in a fully automated pipeline, worth a lot for the hand-target lanes.

And the honest costs: cash drag on the reserved tranches (the entire Vanguard result), and
complexity/false triggers (the entire tranche backtest).

## Implications

- **Keep tranching, but re-aim the yardstick.** Stop expecting or measuring a cost-basis edge;
  the annual-review question for Stage 4 is "how much did tranches 2–3 save us in the episodes
  where the diagnosis was wrong, and what did the cash drag cost in the episodes where it was
  right?" — a dispersion/worst-case ledger, not an average.
- **The redesign directions are the ones the literature endorses:** event-gated tranches
  (Constantinides), slower effective schedule (Brennan–Li–Torous, and our own bottoming-time
  data), block selling (their corollary, already Stage 5 policy).
- **A two-tranche variant deserves a look.** T2's marginal value was negative or nil outside the
  GFC even before the redesign; a simpler ½-at-target / ½-on-filing-confirmation structure keeps
  the misdiagnosis insurance and the information gate while halving the trigger surface. Worth a
  sweep when the T2/T3 rebuild happens.
- **Never evaluate tranches by IRR** (Hayley) — scaling in after declines flatters IRR
  mechanically. Terminal wealth or CE only.

## Sources

- Constantinides (1979), *A Note on the Suboptimality of Dollar-Cost Averaging* — [JFQA](https://econpapers.repec.org/RePEc:cup:jfinqa:v:14:y:1979:i:02:p:443-450_00) · [PDF](https://www.cambridge.org/core/services/aop-cambridge-core/content/view/0C483B96429655B24F34FB628CF9CEEB/S0022109000005408a.pdf/note_on_the_suboptimality_of_dollarcost_averaging_as_an_investment_policy.pdf)
- Brennan, Li & Torous (2005), *Dollar Cost Averaging* — [UCLA Anderson PDF](https://www.anderson.ucla.edu/documents/areas/fac/finance/dollarcostave.pdf)
- Vanguard, *Lump-sum investing versus cost averaging* — [article](https://investor.vanguard.com/investor-resources-education/news/lump-sum-investing-versus-cost-averaging-which-is-better) · [2012 PDF](https://static.twentyoverten.com/5980d16bbfb1c93238ad9c24/rJpQmY8o7/Dollar-Cost-Averaging-Just-Means-Taking-Risk-Later-Vanguard.pdf)
- Hayley (2010), *Value Averaging and How Dynamic Strategies Bias the IRR* — [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=1606347) · [Bayes PDF](https://www.bayes.citystgeorges.ac.uk/__data/assets/pdf_file/0007/126736/Hayley.pdf)
- Brandes Institute, *Falling Knives Around the World* — [summary](https://www.quant-investing.com/blog/why-and-how-to-implement-a-falling-knife-investment-strategy-in-your-portfolio)
- Alpha Architect, *Is Trend Following Better than Buy the Dip?* — [article](https://alphaarchitect.com/is-trend-following-better-than-buy-the-dip/)
- The Evidence-Based Investor, *Does buy the dip actually work?* — [article](https://www.evidenceinvestor.com/post/buy-the-dip)
