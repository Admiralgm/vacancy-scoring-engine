# Batch Scoring Engine Bugs — Fixed June 9, 2026

## Session: Full rescoring audit of 382 JD files

### Bug 1: Domain Variable Overwrite (CRITICAL)

**Symptom:** AI roles with secondary cybersecurity content scored at cyber cap (14) instead of AI cap (22). E.g. ILO Director IT Management scored 54 instead of 84.

**Root cause:** Single `dom` variable overwritten by each sequential keyword check:
```python
# WRONG — overwrites
if match('ai'): dom = 'ai'; p1 += 22
if match('cyber'): dom = 'cyber'; p1 += 14  # overwrites dom!
```

**Fix:** Use `dom_scores = {}` dict + `max()` selection:
```python
dom_scores = {}
if match('ai'): dom_scores['ai'] = 22
if match('cyber'): dom_scores['cyber'] = 14
dom = max(dom_scores, key=dom_scores.get) if dom_scores else None
```

**Impact:** ILO Director IT went from 54 → 84 (+30). Multiple other entries shifted tier.

---

### Bug 2: Director/Head/Chief Not Getting Cap Doubling

**Symptom:** Director-level roles capped at normal domain max (e.g. AI=22) instead of doubled (44).

**Root cause:** No logic to detect `\b(director|chief|head of|deputy head)\b` in title and double the cap.

**Fix:** After computing `best_cap = DOMAIN_CAP[dom]`, check if title matches director pattern and double:
```python
if re.search(r'\b(director|chief|head of|deputy head)\b', title_lower):
    best_cap *= 2
```

**Impact:** Director roles now correctly score at higher P1 values.

---

### Bug 3: Unicode Whitespace in Deadline Parsing

**Symptom:** Deadlines with `\xa0` (non-breaking space) or `\u2002`/`\u2003` parsed incorrectly.

**Root cause:** Date regex `\d+\s+` didn't match unicode whitespace. "22 June 2026" with `\xa0` before 22 matched only "2 June 2026".

**Fix:** Normalize all unicode whitespace before regex:
```python
body = re.sub(r'[\xa0\u2002\u2003\u2009\u2007]', ' ', body)
```

**Impact:** ITU AI Consultant deadline corrected from 2026-06-02 to 2026-06-22.

---

### Bug 4: Robotics Not Counted as AI

**Symptom:** UNICEF Humanoid Robots role scored 42 (LOW) despite being AI/robotics work. "Robot" appeared 18 times but AI keyword count was 0.

**Root cause:** AI keyword list didn't include `robot`, `robotics`, `humanoid`, `autonomous system`, or `intelligent agent`.

**Fix:** Added to AI keyword detection:
```python
ac = kcount(bl, 'artificial intelligence', 'ai ', 'machine learning', 
            'deep learning', 'genai', 'llm',
            'robotics', 'robot', 'humanoid', 
            'autonomous system', 'intelligent agent')
```

**Impact:** UNICEF Humanoid Robots went from 42 → 64 (🟡 STRETCH, up from 🟢 LOW).

---

### Bug 5: Hardcoded Penalty Display Text

**Symptom:** Even after PENALTIES dict was updated (GIS -15→-10, SWE -20→-15), output showed old values because display strings were hardcoded.

**Fix:** Changed all display text to use `PENALTIES['key']` dynamically.

---

### Bug 6: Variable Name Inconsistency (swe_kw_c vs swe_kw)

**Symptom:** `NameError` on penalty display because `swe_kw_c` was referenced but `swe_kw` was the actual variable holding the count.

**Fix:** Renamed `swe_kw_c` → `swe_kw` throughout.

---

### Architecture: Where score_all.py Lives

The current working engine is at:
```
~/Downloads/DATA_REPOSITORY/WORKDIR/score_all.py
```

This is an executable script, not a Hermes skill file. It auto-scans `JD_FILES/` and rebuilds `UN-VACANCIES-TRACKER.txt`.

**Backup: NONE exists.** The old engine was overwritten in-place during the session. The only copies are:
1. This current working file (~514 lines, as of 2026-06-09 18:46)
2. No version control
3. No `skills_backup/` copy of the executable engine

**Recommendation:** Snapshot this file into the skill's `scripts/` directory after every significant change. The skill's methodology (SKILL.md v5.0.0) exists, but the executable engine is orphaned in the working directory.

---

### Calibration Check After Fixes

After applying fixes 1-4, the score distribution of 382 files became:

| Tier | Count | Old Count (pre-fix) |
|------|-------|---------------------|
| 🔴 STRONG (75+) | 1 | 0 |
| 🟠 COMP (65-74) | 2 | 2 |
| 🟡 STRETCH (50-64) | 30 | 29 |
| 🟢 LOW (<50) | 51 | 52 |

The fixes brought ILO Director IT to the top at 84 (from 54), and elevated UNICEF Humanoid Robots from LOW to STRETCH. Distribution remained realistic.

---

### Known Limitations (Not Bugs)

1. **Title-only scoring from tracker:** When JD file is missing, scores from tracker title are provisional and may shift ±15 points.
2. **Login-gated portals:** Some files remain as failed extractions (WMO SSL expired, UNESCO login, ICMPD PDF). Score=0 for these.
3. **No deadline = skip from main tracker:** Roster roles and expired entries go to separate tracker.
4. **State awareness:** `STATE/download_index.json` tracks file hashes for incremental daily scans — prevents re-fetching already-pulled files.
