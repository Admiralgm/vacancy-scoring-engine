# Batch Scoring Bugs Discovered — Impactpool-Scan v4.0  
**Date:** 2026-06-21
**Skill:** impactpool-scan
**Tool:** scripts/batch_score_new.py
**Context:** User challenged 75+ 🔴 entries as unrealistic. Manually verified top 10 against full V5.0.0 scoring. Found systematic inflations.

---

## Bug 1: Roster Inflation
**Symptom**: Roster entries getting 🔴 scores (e.g. 90, 82, 82)
**Root Cause**: Script treats roster=permanent role. Adds P2 seniority + P7 competitive bonuses as if actual vacancy.
**Impact**: +10-15 points per roster entry. At least 6 🔴 entries are roster.
**Fix for Manual Scoring**:
  - P2 cap for roster = 11 max
  - P7 cap for roster = +1 bonus max
  - Roster scoring 🔴 → requires human override

## Bug 2: Duplicate Semantic Keyword Counting
**Symptom**: AI role scoring 90+ when it should be 80
**Root Cause**: "AI" + "machine learning" + "LLM" + "generative AI" + "large language model" counted as 5 separate hits at ~3pts/hit = +15 pts for ONE concept
**Impact**: P1 inflated by 10-20 points on AI/telecom/swe roles
**Fix**:
  - Semantic clustering required — one domain cluster = one hit group
  - AI cluster (all AI/ML/LLM/GENAI terms) → count ONCE
  - Telecom cluster (connectivity/fibre/ISP/5G/etc) → count ONCE
  - EdTech cluster (LMS/Canvas/Moodle/education/etc) → count ONCE

## Bug 3: French Language Gap Ignored
**Symptom**: UNICEF Digital Learning & ADT Consultant scored 🔴87 despite "Fluency in English and French is required"
**Root Cause**: No French check in batch script. User has ZERO French per CV repo.
**Impact**: Score capped to 65 maximum per V5.0.0 but script outputs 87.
**Fix per V5.0.0**:
  - French required + central to job = cap at 65
  - French required + not central = cap at 75
  - Always flag SOFT_NO_REQUIRED_LANGUAGE_GAP

## Bug 4: Missing G-Grade / Assistant / Associate Detection
**Symptom**: Telecommunications Assistant (G-6) scored 🔴89, Senior IT Assistant scored 🔴76
**Root Cause**: Regex UKRAINE_PATTERN, NATIONALS_ONLY_PATTERN miss G-grade, Assistant, Associate, Junior titles
**Impact**: Below-P3 roles appearing in 🔴 band
**Fix in SKILL.md**: Added explicit title-level hard filter guidance (already in V5.0.0 Section 2). Batch script needs these regexes added.

## Bug 5: Cookie Banner Interference
**Symptom**: "no longer open" string from "Similar Jobs" section at bottom of scraped page causing FALSE disqualifications
**Root Cause**: Script reads full scraped text including sidebar/similar jobs without isolating the actual JD content
**Impact**: Valid entries wrongly flagged CLOSED/GARBAGE, or valid entries with sidebar noise getting inflated by keywords in sidebar
**Fix Strategy**:
  - For UNJobNet/Impactpool scrapes: extract text between "Job Description" and "Advertisement/Similar Jobs"
  - For Impactpool: text after cookie banner purges must be scanned AT END for actual JD content

---

## Calibration After Fix
- Pre-fix 🔴 count: 68 entries
- Post-manual-top10: ~5/10 of top 🔴 were genuinely 🔴, ~5/10 should be 🟠
- Estimation: true 🔴 band ≈ 35-45 entries (not 68)
- Recommendation: BEFORE relying on batch scoring, always verify top 🔴 manually or apply semantic-clustering patch
