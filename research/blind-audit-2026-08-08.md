# Blinded Stage-2 leakage audit — 2026-08-08

Testing whether a Stage-2 diagnosis can be made on period-locked evidence without
the diagnoser knowing which company it is looking at — the prerequisite for
measuring whether temporary-vs-permanent judgment adds value over the mechanical
funnel.

**Method.** 12 case packets built by [scripts/stage2_blind.py](../scripts/stage2_blind.py)
from passed episodes in the funnel backtest, stratified 2017–2026. Each carries
point-in-time fundamentals (indexed, magnitudes bucketed), relative price context,
quality screens, and the MD&A + risk factors of the 10-Q/10-K **filed before the
signal date** — evidence that cannot contain hindsight because the SEC timestamps
it. Identity redacted; absolute years mapped to labels relative to the fiscal year.

Adversarial audit: one subagent per packet, reading only that packet, instructed
to identify the company and year.

## Result: blinding failed on every case audited

| Round | Audited | Identified | Confidence |
|---|---|---|---|
| 1 — initial build | 6 | 6 | all HIGH |
| 2 — after fixing every mechanical leak | 4 | 4 | all HIGH |
| 3 — after replacing the narrative with an LLM neutral rewrite | 6 | 6 | all HIGH |

Round 2 re-audited a deliberate mix: two cases whose original tell was a bug that
had since been fixed (RL, UNH) and two whose tell was content that cannot be
regex-redacted (FFIV, SYF). All four were still identified.

## Leaks found, and whether they are fixable

**Fixed and verified closed:**

| Leak | Example | Fix |
|---|---|---|
| Signature blocks and exhibit indexes | `RALPH LAUREN CORPORATION / By: /S/ …` | Truncate the section at the signature marker |
| **Redaction inverted by statutory years** | `Securities Exchange Act of [YEAR+85]` → reader knows the Exchange Act is 1934 → solves to 2019 | Protect `Act of <year>` citations from relativization; leave era-neutral statutory dates verbatim |
| Substring corruption | Former-name fragment `United` rewrote `UnitedHealth` → `the CompanyHealth`, a *sharper* fingerprint than the original | Anchor matches at word start, consume the rest of the token |
| Missing current name | EDGAR returns `entityName: null` for some filers; only former names were redacted | Draw the name from companyfacts, submissions, former names, and config |
| Tickers under the length floor | `EQR issued 69,940 Common Shares` | Always redact the ticker regardless of length |
| URLs | `https://investors.<name>.com` | Strip URLs |

Verified after fixes: **12/12 packets clean of company name and ticker**, zero
invertible statutory offsets, zero substring corruption.

**Not fixable by redaction — the finding that matters:**

- **Product and brand names.** F5 was identified from `BIG-IP`, `VIPRION`,
  `Local Traffic Manager`; Synchrony from `CareCredit®`, `SyPI™`, `Quickscreen®`.
  Both had their company name fully redacted and were still called with HIGH
  confidence.
- **Segment and business-model self-description.** "Premium revenues from
  risk-based products constitute nearly 80% of our total consolidated revenues"
  identifies a managed-care insurer; the four-segment Optum/UnitedHealthcare
  structure identifies which one.
- **Related-party and regulatory identifiers.** `Lauren Family, L.L.C.`; SEC file
  number `333-24733` (Ralph Lauren's 1997 IPO registration); named officers in
  exhibit lists.
- **The financial fingerprint itself.** Several auditors reconstructed the company
  from the indexed series alone — a dual-class discretionary retailer with a
  restructuring loss in Y-2 recovering to 8.9% margins narrows to a handful of
  names before any text is read.

## Round 3 — the LLM redaction pass

A rewriter subagent (which sees the identity, in order to remove it) restated each
filing excerpt as a neutral case description: product and segment names replaced
with generic descriptors, absolute amounts dropped, percentages and management's
stated causal logic preserved. Applied to the four cases that survived regex
redaction plus the two most famous companies in the sample.

**The rewrite worked on text and failed anyway.** F5's `BIG-IP`, `VIPRION` and
module names are gone; the surviving substance (margin drivers, buybacks at ~80%
of operating cash flow, deferred-revenue trend) is intact and genuinely useful.
All six were still identified at HIGH confidence — but by different evidence:

**The dominant new tell is the quantitative section, which I wrote, not the filing.**
Five of six auditors reconstructed the company by matching the *indexed* series
against public financial history:

- Alphabet: "rebased to 100 gives 100/123/152/179/202/285/313 — the packet's
  Y-1 = 285 and Y0 = 313 land on the decimal"
- F5: "matches F5 FY2011–FY2017 revenue to within a point at every node"
- UnitedHealth: "All seven points match within rounding"
- Ralph Lauren: the EBIT-margin row "maps line-for-line onto RL FY2013→FY2019"

The Ralph Lauren auditor stated the mechanism precisely: *"Indexing the revenue row
is pointless when the EBIT-margin and ROIC rows are published at full precision,
since margin × indexed revenue reconstructs absolute dollars up to a single scale
factor, and the '$5–20B revenue' band fixes that factor closely enough."*

Bucketing magnitudes and indexing series does not anonymize. **Ratios are
scale-free, so indexing preserves the fingerprint exactly** — a seven-year revenue
and margin path is effectively unique for any public company.

Residual text tells also survived the rewrite, because they are structural rather
than lexical: UnitedHealth's two-business/four-segment shape (care delivery, data
analytics, pharmacy, benefits), Meta's DAU/MAU-plus-DAP/MAP metric taxonomy,
Synchrony's partner taxonomy. And the percentages I explicitly told the rewriter to
keep are themselves identifiers (Meta: +11%/+12%/+15%/+14% user growth, 38% margin,
12.2% tax rate).

## Conclusion

Regex redaction cannot blind an SEC filing. The MD&A exists precisely to describe
the business distinctively, so the content that makes it useful for a Stage-2
diagnosis is the same content that identifies the filer. Name, date, and amount
redaction are necessary and now work; they are nowhere near sufficient.

Consequence for the study: **the diagnoser knows the company, and therefore knows
the outcome.** Verdicts collected this way measure hindsight, not judgment, and
cannot answer whether Stage-2 discrimination is real.

The deeper result from round 3 is that this is not a redaction problem. A company's
multi-year financial trajectory is simultaneously (a) the evidence needed to judge
temporary-versus-permanent and (b) a unique identifier. Any presentation faithful
enough to support the diagnosis is faithful enough to identify the filer. Two
independent anonymization strategies — lexical and generative — both failed, and
the second failed on a channel the first had masked by accident.

## Options tried and remaining

| Option | Status |
|---|---|
| Regex redaction of names, dates, amounts | **Tried — failed.** Product names, segment structure, related entities |
| LLM neutral rewrite of the narrative | **Tried — failed.** Indexed financial series is invertible |
| Restrict to obscure names | **Rejected on evidence.** FFIV, SYF, HST, BEN are not household names and were identified anyway |
| **Perturb the numbers** | **Untried.** Jitter indexed series and round ratios coarsely enough to break matching while preserving the qualitative shape ("revenue down three years, margin halved"). Costs measurement precision and needs its own audit round; the harness to test it now exists |
| Numbers-only packets | Available, but no longer Stage 2 — that is a quality screen, which the funnel backtest already measured |
| **Forward validation only** | The reliable instrument. On the live paper portfolio, verdicts are recorded before outcomes exist, so contamination is impossible by construction rather than by effort |

*Packets and answer key: `data/blind/`. `raw/` holds unredacted staging for the
rewriter and is never shown to a diagnoser or auditor; `neutral/` holds the
rewrites. The key is never included in a packet.*
