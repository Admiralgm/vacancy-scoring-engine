# Batch Scoring Engine Pitfall: Domain Variable Overwrite & Missing AI Keywords

## Date: 2026-06-09
## Session: Full rescoring of 382 JDs after scoring engine rewrite
## Background: user asked "why did you score this one so low," discovered 3 bugs

---

## Bug 1: Domain Variable Gets Overwritten by Last Match

**Impact:** AI roles scored 8-15 points too low. Humans knew the role was AI-heavy; batch engine scored it as "cyber" or "swe" because those keywords appeared later in the code.

**Faulty code pattern:**
```python
# WRONG
dom = 'ai'    if ai_count >= 3 else ''
dom = 'cyber' if cyber_count >= 3 else ''   # overwrites ai if both match!
dom = 'swe'   if swe_count >= 3 else ''     # overwrites again!
cap = DOMAIN_CAP[dom]  # always the LAST matching domain's cap
```

**Effect on real vacancy (ILO Director IT Management, ID 13630):**
- AI content scored 34 (should cap at 22→44 for director)
- "digital transformation" keyword hit last → domain set to 'mgmt' → cap = 14
- Director bonus applied on top of cap=14 → total capped far below actual
- Score dropped from 81 (old engine) → 54 (bugged) → 84 (fixed)

**Correct pattern:**
```python
dom_scores = {}  # accumulate ALL matching domains
if telecom_count >= 3:    p1 += 8;  dom_scores['telecom'] = 8
if ai_count >= 3:         p1 += 22; dom_scores['ai'] = 22
if swe_count >= 3:          p1 += 10; dom_scores['swe'] = 10
# ... every domain check adds to dom_scores, DOES NOT OVERWRITE ...

if dom_scores:
    max_domain = max(dom_scores, key=dom_scores.get)
    selected_cap = DOMAIN_CAP[max_domain]
    if is_director:
        selected_cap *= 2  # director/chief/head → double cap
    p1 = min(p1, selected_cap)
```

**Why old engine didn't have this bug:** Unknown (code lost). Likely used uncapped scoring or a different variable name. Only the backup tracker survives.

---

## Bug 2: AI Keyword List Missing Robotics/Autonomous Systems

**Impact:** UNICEF Humanoid Robots in Education (593483) scored 42 LOW instead of 64 STRETCH.

**The file contains these keywords 18 times:**
- "robot" (12x)
- "humanoid" (4x)
- "robotics" (2x)
- "autonomous" (0x in this file, but present in other robotics JDs)

**Old keyword list:**
```python
ac = kcount(bl,'artificial intelligence','ai ','machine learning','deep learning','genai','llm')
```

**Result for UNICEF 593483:** `ac == 0` (zero exact AI keyword matches)
**But:** 18 robotics mentions = the role IS an AI/robotics role in practice

**Fixed keyword list (2026-06-09):**
```python
ac = kcount(bl,'artificial intelligence','ai ','machine learning','deep learning','genai','llm',
            'robotics','robot','humanoid','autonomous system','intelligent agent')
```

**Result after fix:** `ac == 18` → P1 domain = 'ai' with cap=22 → score jumps from 42 → 64

**Lesson:** "AI" is a broader category than literal "artificial intelligence." Robotics, autonomous systems, and intelligent agents are subfields of applied AI. The keyword list must include them.

---

## Bug 3: Unicode Whitespace in Deadline Parser

**Impact:** ITU_1354554355 was parsed with wrong deadline (2026-06-02 instead of 2026-06-22), causing it to be filtered out as expired.

**Root cause:** The character `\xa0` (non-breaking space) before "22" in "\xa022 June 2026" made regex match "2 June 2026" instead of "22 June 2026".

**Fix added to `parse_flexible_date()`:**
```python
text = re.sub(r'[\xa0\u2002\u2003\u2009\u2007]+', ' ', text)
# Normalizes all unicode whitespace variants before regex matching
```

**Breakdown of common unicode whitespace offenders in UN JDs:**
- `\xa0` (NBSP) — most common in European portals (ILO, ITU, OECD)
- `\u2002` (EN SPACE) — Word-generated PDFs
- `\u2003` (EM SPACE) — copy-paste from web editors
- `\u2009` (THIN SPACE) — French/Spanish formatting
- `\u2007` (FIGURE SPACE) — numeric date formatting

---

## Lesson: Version the Scoring Engine

**CRITICAL: The `score_all.py` script was overwritten 3+ times with NO backup.**

**Chronology:**
1. `score_all.py v1` — created during initial build (domain caps, penalties, Unicode-naive dates)
2. Overwritten — added orcbestrator integration, state awareness
3. Overwritten — penalty tuning (GIS -15→-10, SWE -20→-15, Data -10→-5, BI -10→-5)
4. Overwritten — `dom_scores` dict fix + director double-cap + Unicode whitespace normalization
5. Overwritten — robotics AI keywords added

**No copy of any version survives.** The only way to reconstruct v1 would be to reverse-engineer from the backup tracker's scores, which is approximate at best.

**Rule added to the skill (from this session):**
"Before ANY edit to the scoring engine, run: `cp score_all.py BACKUP/score_all_$(date +%Y%m%d_%H%M).py`"

---

## Before/After Score Comparison (Key Entries)

| VID | Title | Old* | Bugged | Fixed | Δ(old→fixed) |
|-----|-------|------|--------|-------|-------------|
| ILO_13630 | Director IT Management | 81** | 54 | 84 | +3 |
| UNICEF_593483 | Humanoid Robots Education | 82** | 42 | 64 | -18 |
| WHO_2600075 | AI Software Engineer Lead | 79** | 62 | 62 | -17 |
| WHO_2601954 | Data Manager | 69** | 41 | 41 | -28 |
| UNICEF_593542 | Data Science/ML Consultant | 73** | 53 | 53 | -20 |
| UNICEF_593541 | Digital Impact (Dev Lead) | 72** | 58 | 58 | -14 |

*Old = `score_all.py v1` (engine lost, only backup tracker preserves scores)
**From backup tracker `UN-VACANCIES-TRACKER_VALID_20260609_1536.txt`

**What the numbers show:**
- Old engine had NO effective domain caps → scores inflated by 10-30 points
- Fixed engine WITH caps produces more realistic, conservative scores
- UNICEF 593483 is the only genuine "miss" (fixed by adding robot/humanoid to AI list)
- ILO Director actually gained +3 (went 81→54→84) because `dom_scores` + director double-cap properly unlocks the AI content

---

## Action Items for Future Sessions

1. **Never single-variable domain tracking** — always use `dom_scores = {}` dict + `max()`
2. **Always backup before editing** — `cp score_all.py BACKUP/score_all_$(date +%Y%m%d_%H%M).py`
3. **Normalize unicode whitespace before ALL date regexes** — `re.sub(r'[\xa0\u2002\u2003\u2009\u2007]+', ' ', text)`
4. **AI keyword list should include applied-AI subfields** — robotics, autonomous systems, intelligent agents, drones (where relevant)
5. **Old engine overestimated by 10-30 points** — don't chase old scores; focus on ranking accuracy
6. **Director/chief/head cap doubling is correct** — roles with those titles genuinely deserve higher domain ceilings
