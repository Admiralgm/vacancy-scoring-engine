# Short-Title vs Full-JD Scoring Calibration
## Empirical benchmark: UNICEF Top 20 (May 2026)

## Background
This calibration was produced by rescoring 20 UNICEF vacancies from the Top 20 
list using **full raw job descriptions** (extracted via browser_console `article.innerText`),
compared against the original scores derived from 44-char tracker titles.

Result: Short-title scoring is systematically wrong by an average of **−14 points**.

## Key Finding
| Metric | Short-Title | Full-JD | Delta |
|--------|------------|---------|-------|
| Average score | 52 | 37 | −14 |
| Jobs needing upward revision | — | 5 | hidden matches revealed |
| Jobs needing downward correction | — | 15 | false positives caught |

## Top 5 Overestimates (title keywords were misleading)

| Job | Old | New | Drop | Root Cause |
|-----|-----|-----|------|------------|
| Akelius Digital Language, BiH | 80 | 30 | −50 | National-only BiH, BCS language required, language teacher role |
| Graphic Design Consultant, ECW | 55 | 10 | −45 | Pure InDesign/Photoshop — zero tech or AI |
| Education Officer P-2, Mali | 60 | 25 | −35 | P-2 junior grade, French mandatory |
| Digital Media Consultant, Kosovo | 50 | 15 | −35 | National-only Kosovo, Albanian required |
| Health Specialist P-3, Angola | 50 | 15 | −35 | Pure public health epidemiology |

## Top 5 Underestimates (full JD revealed hidden match)

| Job | Old | New | Gain | Why |
|-----|-----|-----|------|-----|
| T4D Specialist P-3, Cambodia | 40 | 70 | +30 | ICT4D/digital health — direct GIGA+LP match |
| Innovation Specialist P-3, Stockholm | 40 | 62 | +22 | Venture Fund invests in AI/blockchain |
| DPGs Community Manager, Valencia | 45 | 65 | +20 | Digital product operating model |
| Programme Manager Infra Finance P-4 | 85 | 98 | +13 | IFI/DFI engagement, blended finance |
| Evidence Synthesis, Innocenti | 40 | 48 | +8 | AI & children's digital rights research |

## Four Systematic Factors Causing Mis-scoring

These four factors explain why short-title scoring is unreliable:

1. **Grade level invisible in title** — "Education Officer P-2" should score ~25 for a COO, not 60
2. **Language requirements invisible** — French/Portuguese/Spanish mandatory in the JD but not in title
3. **National-only restrictions invisible** — BiH, Kosovo, Mexico require local nationality
4. **Role type vs keyword inflation** — "Digital" in "Digital Media Consultant Kosovo" = social media, not ICT4D; "AI" in roles = actually graphic design

## Protocol for Flagging Tentative Scores

When only a title/44-char description is available (e.g., from Impactpool or tracker file):

1. Append `⚠️ TENTATIVE` to the score
2. Apply the four-factor quick check:
   - Grade level: is it junior (P-2/NO-1/G-5)?
   - Language: does the org/sector typically require French/Arabic/Spanish?
   - National vs International: is it a country-office role?
   - Role type: do "digital/AI/innovation" describe tech or comms/fundraising?
3. If any factor suggests the title is misleading, reduce the score by 10-20 points
4. **Flag in the tracker entry**: "Score is TENTATIVE — needs full JD verification before application decision"
5. Never make a real application decision from a tentative score

## Empirical Verdict

**Full-JD extraction is mandatory before any application decision is made.**

Short-title scores are worse than random — they are **systematically misleading in the wrong direction**:
- They inflate scores for roles with misleading keywords (digital media, graphic design, fundraising)
- They deflate scores for roles with hidden technical depth (T4D, Venture Fund, DPGs)
- They miss grade mismatches, language barriers, and nationality restrictions

The recommendation: **Always extract the full JD before scoring.** The 44-char tracker title alone produces systematically wrong scores.

## Reference Jobs That Matter (after rescoring)

| Rank | Job | Score | Action |
|------|-----|-------|--------|
| 1 | Programme Manager Infra Finance P-4, GIGA | 98 | Strongest match — apply now |
| 2 | T4D Specialist P-3, Cambodia | 70 | Apply |
| 3 | DPGs Community Manager, Valencia | 65 | Apply |
| 4 | Innovation Specialist P-3, Stockholm | 62 | Apply |
| 5 | MER Coordinator, Digital Education | 50 | Marginal — M&E not core strength |
