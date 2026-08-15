# The CRISIS exemption, against the nulls

*`--month-cap 0.083333` (one month's inflow) with and without `--crisis-exempt`, on the CURRENT configuration (2/3 growth reserve). Every figure is a median across windows. Bars declared in `scripts/research_crisis_exempt.py` before running; all reported.*

Flags: `C` beat the capped baseline, `U` the pair beat the uncapped baseline, `R` beat every shuffled-label seed, `B` beat every block-shuffled seed, `P` beat every null in its own window in a majority of windows, `L` survived both lags.

## On the nested windows

*The eight the brief prescribes. They count 2000-02 five times, so a crisis-keyed mechanism is scored on its best case repeatedly.*

| reserve | profile | crisis wks | cap alone | exemption vs CAPPED | the PAIR vs UNCAPPED | shuffle med (min..max) | block med (min..max) | per-window rank | lag4 | lag13 | verdict |
|---|---|---|---|---|---|---|---|---|---|---|---|
| `g0` | A | 11.5% | -0.61% | **+0.51%** | **+0.19%** | -0.01% (-0.16%..+0.26%) | +0.00% (-0.24%..+0.30%) | 1st in 3/8 (med 3/13) | +0.53% | -0.15% | fail CURB-- |
| `g0` | A400 | 11.5% | -0.80% | **+0.44%** | **-0.92%** | +0.17% (-0.17%..+0.63%) | -0.05% (-0.47%..+1.46%) | 1st in 1/8 (med 8/13) | +0.42% | +2.38% | fail C----L |
| `g0` | C | 11.5% | -1.31% | **-0.20%** | **-1.54%** | +0.02% (-0.35%..+0.27%) | +0.00% (-0.19%..+0.51%) | 1st in 1/8 (med 12/13) | -0.01% | -0.19% | fail ------ |
| `g67` | A | 11.5% | +9.80% | **-9.74%** | **+2.30%** | -3.64% (-5.48%..-2.83%) | -0.36% (-1.56%..+0.00%) | 1st in 1/8 (med 13/13) | -8.79% | -9.40% | fail -U---- |
| `g67` | A400 | 11.5% | +9.87% | **-6.69%** | **+2.36%** | -5.58% (-7.09%..-4.12%) | -1.59% (-4.24%..-0.13%) | 1st in 0/8 (med 11/13) | -4.89% | -3.73% | fail -U---- |
| `g67` | C | 11.5% | +10.36% | **-10.07%** | **+2.00%** | -2.70% (-3.41%..-1.88%) | -0.17% (-1.03%..-0.14%) | 1st in 0/8 (med 13/13) | -9.35% | -9.44% | fail -U---- |

**0 of 6 reserve x profile cells clear every bar.**


## On the five disjoint sub-periods

*No week appears in two of these.*

| reserve | profile | crisis wks | cap alone | exemption vs CAPPED | the PAIR vs UNCAPPED | shuffle med (min..max) | block med (min..max) | per-window rank | lag4 | lag13 | verdict |
|---|---|---|---|---|---|---|---|---|---|---|---|
| `g0` | A | 3.8% | +0.23% | **+0.11%** | **+0.24%** | +0.01% (-0.04%..+0.30%) | +0.05% (-0.00%..+0.20%) | 1st in 2/5 (med 3/13) | +0.06% | +0.11% | fail CU---L |
| `g0` | A400 | 3.8% | -0.60% | **+0.02%** | **-0.59%** | -0.05% (-0.50%..+0.39%) | +0.00% (-0.01%..+0.29%) | 1st in 0/5 (med 8/13) | +0.00% | +0.10% | fail C----- |
| `g0` | C | 3.8% | -0.18% | **+0.15%** | **-0.18%** | +0.00% (+0.00%..+0.20%) | +0.00% (+0.00%..+0.29%) | 1st in 1/5 (med 3/13) | +0.00% | +0.05% | fail C----- |
| `g67` | A | 3.8% | +1.04% | **+0.09%** | **+1.13%** | -0.00% (-0.16%..+0.10%) | +0.00% (-0.06%..+0.00%) | 1st in 3/5 (med 1/13) | +0.01% | +0.00% | fail CU-BP- |
| `g67` | A400 | 3.8% | -0.47% | **-0.03%** | **-0.49%** | -0.07% (-0.41%..+0.01%) | +0.00% (-0.47%..+0.00%) | 1st in 1/5 (med 8/13) | +0.00% | -0.02% | fail ------ |
| `g67` | C | 3.8% | -0.02% | **+0.00%** | **-0.09%** | -0.01% (-0.13%..+0.00%) | +0.00% (+0.00%..+0.00%) | 1st in 1/5 (med 10/13) | +0.00% | +0.00% | fail ------ |

**0 of 6 reserve x profile cells clear every bar.**

## Per-window detail (exemption vs capped)

*Profile A. `g0` is the reserve the original result was measured on; `g67` is live.*

| window | crisis wks | exemption, g0 | exemption, g67 | best shuffle (g67) | best block (g67) |
|---|---|---|---|---|---|
| 1998-2026 | 167/1493 | -1.08% | -16.06% | -2.56% | -0.24% |
| 1998-2019 | 160/1148 | +0.28% | -10.91% | -2.06% | +0.86% |
| 2000-2026 | 164/1389 | +0.94% | -13.13% | -3.60% | +0.53% |
| 2000-2020 | 164/1096 | +0.49% | -12.61% | -7.33% | +0.11% |
| 2000-2019 | 157/1044 | +1.66% | -8.56% | -2.68% | +0.53% |
| 2010-2026 | 14/867 | -0.05% | -2.89% | +0.34% | +0.20% |
| 2010-2019 | 7/522 | +0.55% | -1.11% | +0.00% | +0.00% |
| 2020-2026 | 7/345 | +0.54% | +0.10% | +0.10% | +0.10% |
| 1998-2002 | 81/261 | +1.04% | +1.13% | +5.77% | +6.20% |
| 2003-2007 | 10/261 | +0.11% | +0.04% | +0.37% | +0.04% |
| 2008-2012 | 66/261 | +0.00% | +0.09% | -0.09% | -0.02% |
| 2013-2019 | 3/365 | +0.00% | +0.00% | +0.00% | +0.00% |
