# Batch Scoring + Calibration Workflow — Validated 2026-06-03

## Context
90 JD files needed scoring. Manual one-by-one was impractical. Used programmatic
batch scoring with the scoring engine's reference file (`programmatic-batch-scoring-2026-06.md`)
as the method, then applied manual calibration overrides for the top 10-20 entries.

## Pipeline Scripts (in order, from un-jobs-search skill)

1. `scripts/prefilter_and_classify.py` — classify all JDs into SCOREABLE / DISQUALIFIED
2. `scripts/batch_score_all.py` — 7-parameter batch scoring + tracker rebuild
3. Manual calibration of top 10-20 entries — see calibration table below

## Calibration Corrections Made (batch → calibrated) on 2026-06-03

| Entry | Batch | Calibrated | Delta | Reason |
|-------|-------|------------|-------|--------|
| ILO Director IT D-2 | 53 | 81 | +28 | COO+Group CTO perfect match for CIO role, Geneva location |
| IMF IT Strategist | 51 | 75 | +24 | Agentic AI unique differentiator calibrated at 85; DC P6 penalty reduces to 75 |
| WTO Dig Learning Tech Spec | 43 | 66 | +23 | Current Moodle/Canvas integration at Olivia Education overrides CV gap |
| ITU Home Based Full Stack | 70 | 70 | 0 | Home-based + strong keyword match = correct |
| INSPIRA Platform Arch P4 | 77 | 69 | -8 | P4 is Senior/Lead not Director; P6 neutral, not DC |
| UNICEF UPSHIFT AI | 66 | 75 | +9 | AI strategy + EdTech + UNICEF context calibrates to anchor |
| UNICEF T4D P-3 | 58 | 69 | +11 | P-3 but Tech for Development is strong UNICEF domain fit |
| ICRC Belgrade Collab | 58 | 67 | +9 | Local role (P6=10), Collaboration Tech = telecom domain match |
| UNIDO Sr Process Trans & AI | 66 | 70 | +4 | Process + AI integration is strong digital transformation fit |
| World Bank Sr GenAI Prac | 65 | 69 | +4 | GenAI practitioner, but DC location caps P6=4 |
| INSPIRA CIO P5 New York | 45 | 61 | +16 | P5 Senior IS Officer = CIO-level, but P6=4 for DC |
| UNOPS AI Geo Data Sci | 51 | 58 | +7 | AI+GIS hybrid, domain cap at 10 for GIS keeps P1 under 12 |

## Distribution Benchmarks
- STRONG (75+): 4 (expected: 3-6) ✓
- COMPETITIVE (65-74): 16 (slightly above 13 expected; ITU roster entries inflate)
- STRETCH (50-64): 28 ✓
- LOW (<50): 71 (includes many roster entries with no-ICT content)
- Average: ~51 (expected: 55-62; roster entries pull it down)
- Top score: 81 (ILO D-2) — within expected range of 76-82

## Key Patterns Discovered
1. **WHO Band B consultants** ($7,500/mo = $341/day < $350 floor) need explicit rate check
2. **Hardship locations** (Congo-Brazzaville → P6=5) penalize otherwise strong domain matches
3. **INSPIRA US roles** (New York, Washington → P6=4) impact overall by -6 points
4. **G-6 Geneva** should be excluded as below grade floor despite being EU location
5. **ITU rate ranges** ($230-$430/day) pass if ceiling above $350 floor
6. **French "réservé aux ressortissants"** pattern caught by nationals-only regex

## Recommendations
- Add WHO Band B explicit rate check to pre-filter
- Pre-populate a calibration-overrides dict before running batch scorer
- Use weighted keyword frequency (not binary presence) for P1 in next iteration