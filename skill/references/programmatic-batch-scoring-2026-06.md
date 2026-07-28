# Programmatic Batch Scoring — Approach Validated 2026-06-01, Extended 2026-06-01

## When to Use
Scoring 20+ JD files from disk. Manual per-file scoring (as the engine's default mode prescribes) is impractical at this scale. A Python script can approximate all 7 parameters with keyword/pattern matching, then the agent validates the top outliers.

## Content Cleaning Pipeline (prerequisite before scoring)

When JD files come from HTML-scraped sources (not clean API text), clean them first:

### Step 1 — Strip HTML garbage
Regex patterns to strip:
```
# Cookie/session banners
r'Accept All Cookies|Cookie Policy|This site uses cookies|By continuing.*you agree'

# Navigation UI
r'Skip to main content|Toggle navigation|Menu|Sitemap|Search this site'

# Footer/links that don't belong
r'Connect with us|Follow us on|Share this|Print this page|^\\*\\*Links\\*\\*$'

# Language selectors
r'Select Language|Choose Language|English\s*[|/]\s*French'

# Empty/whitespace-only lines (multiple runs)
r'^\s*$' (run 2-3× until stable)

# Session messages
r'Your session will expire|Sign in to save|Create an account to apply'
```

### Step 2 — Content anchoring
Many scrapes have the real JD content buried after nav/cookie UI. Find the first real content line and strip everything above it:

```python
# Known section headers that start real JD content (ordered by prevalence)
CONTENT_ANCHORS = [
    'Background',
    'Job Description',
    'The Position',
    'Summary',
    'Organizational Setting',
    'Description of Duties',
    'Duties and Responsibilities',
    'Purpose of the Position',
    'Organisation Context',
    'Main Purpose',
    'How you can make a difference',
    'Organizational Context',
    'Accountabilities',
    'Key Responsibilities',
    'Minimum Requirements',
    'Qualifications',
    'Vacancy Details',
    'Description',
    'Grade',
    'Contract type',
]

# Find first anchor in file content, strip everything before it
first_anchor_line = None
for i, line in enumerate(lines):
    for anchor in CONTENT_ANCHORS:
        if anchor in line and len(line.strip()) < 80:  # avoid matching inside long paragraphs
            first_anchor_line = i
            break
    if first_anchor_line is not None:
        break
```

### Step 3 — Inject metadata header
After cleaning, prepend a structured header for scoring readability:

```python
HEADER_TEMPLATE = """---
Org: {org}
Position: {title}
Deadline: {deadline}
Grade: {grade}
Location: {location}
---
"""
```

### Step 4 — Extract org name from directory structure
If files are organized as `ORGNAME/job-title.md`, extract org from the dir name. Normalise common variants:
- WB → World Bank
- IFC → World Bank
- IAEA → IAEA
- UNJSPF → UN Joint Staff Pension Fund

### Step 5 — Multi-pass blank line stripping
After regex passes, run blank-line removal 2-3× in a loop until no more changes (garbage can leave fragments that collapse to blank lines only after prior passes are done).

## Approach

### Pre-filter (before any scoring)
```
For each .md file:
1. Read file
2. Extract title, grade, deadline, location from frontmatter/lines[:30]
3. Apply hard filters in this order:
   - Ukraine in content → skip
   - "National Consultant" / "National Officer" in title → skip
   - Intern/Volunteer in title → skip
   - Junior + (Officer/Analyst/Professional) in title → skip
   - Grade field: P2/G4/G5/G6/G7/NOA/NOB → skip
   - P2 in title string → skip
   - "IT Assistant" / "IT Support" in title → skip
   - Empty file (<500 bytes) → skip
   - WHO: "Band level B" → skip (~$341/day < $350)
   - ITU: max daily rate < 350 → skip
   - ITU: "CHF 201" → skip (~$220/day)
   - World Bank: "Local Recruitment" + "Chennai" → skip
   - World Bank: Washington, DC location (no US work auth) → skip
4. Passes all filters → score
```

### P1 — Domain/Technical Fit (max 25)
Score by keyword categories in JD body:
- AI/ML/Agentic/GenAI keywords: +8
- Digital transformation/digital strategy: +5
- Data engineering/data pipeline/ETL: +6
- Software engineering/full-stack/devops/web: +5
- Cloud/platform/infrastructure/network/telecom: +5
- Solution/enterprise architecture: +4
- Project/programme management, product owner: +3
- GIS/geospatial/satellite imagery: +3
- Innovation/digital learning/EdTech: +3
- Broadcast/conferencing/AV: +2
- Instructional design/elearning/Moodle: +2
- Market infrastructure/payment/digital euro: +3

Penalties: pension/benefits/HR (-3), instrumentation/nuclear/safeguards (-2)

Cap at 25, min 0.

### P2 — Seniority & Experience Volume (max 15)
Base on title + grade field:
- Director/Chief: 13
- P5: 12
- P4/D2: 11
- P3: 10
- Senior in title: 10
- Lead: 9
- Specialist/Expert: 8
- Consultant/Officer: 7
- Default: 6

Adjust up if years-of-experience regex finds ≥10 yrs (12), ≥7 yrs (10), ≥5 yrs (8).

### P3 — UN/IFI/Development Fit (max 15)
- UNICEF: 9 (direct experience)
- WHO/FAO/UNIDO/WFP/ITU: 7
- IAEA/ILO/UNCTAD/UNOPS/UNJSPF/OICT: 5
- IMF/World Bank/UNDP: 4
- ICAO/UNESCO: 4
- Others: 3

+2 if JD mentions UNICEF/UN/SDG. +3 if mentions school connectivity/GIGA/Learning Passport.

### P4 — Education & Credentials (max 10)
- Base: 6 (MSc+MPhil EE/Telecom)
- +2 if JD requires Master's/PhD
- +2 if field is EE/Telecom/CS/IT/Engineering

### P5 — Language Requirements (max 10)
- Base: 8 (English fluent + Russian fluent)
- French required: drop to 5 (or 6 if Russian known)
- French desirable: 9
- Russian desirable: +1

### P6 — Logistics & Eligibility (max 10)
- EU/Schengen location: 10
- Home-based/remote/multiple: 10
- Non-EU developing country (Nairobi, Phnom Penh, Dakar, Bangkok, Kingston): 6
- Non-EU hardship (Yaounde, Cameroon): 5
- USA/Washington DC: 4 (no work authorization)

### P7 — Competitive Realism (max 15)
- Base: 7
- Agentic/AI framework keywords: +4
- UNICEF + digital/education/connectivity: +3
- Telecom/ISP/connectivity: +2
- Enterprise/solution architecture: +2
- Director/Chief/Executive in title: -3
- Pension/benefits/payroll/HR: -3
- Washington DC location (P6 ≤ 5): -2

### Color Coding (per User's June 2026 request)
- 🔴 75+: highest match
- 🟠 65-74: strong fit
- 🟡 50-64: stretch
- 🟢 <50: low fit

## Deadline Extraction — Org-Specific Patterns

Different portals format deadlines differently. Use these regex patterns in order:

```python
DEADLINE_PATTERNS = [
    # Standard YYYY-MM-DD
    (r'(\d{4}-\d{2}-\d{2})', 1),
    # ISO-like with month names: "2026-06-15T23:59" or "01 June 2026"
    (r'(\d{1,2}\s+(?:January|February|March|April|May|June|July|August|September|October|November|December)\s+\d{4})', 
     lambda m: parse_date(m.group(1), dayfirst=False)),
    # US style: "June 1, 2026" or "Jun 01, 2026"
    (r'((?:January|February|March|April|May|June|July|August|September|October|November|December|Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\s+\d{1,2},?\s+\d{4})', 
     lambda m: parse_date(m.group(1))),
    # European day-first: "15 June 2026"
    (r'(\d{1,2}\s+(?:Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)[a-z]*\s+\d{4})',
     lambda m: parse_date(m.group(1), dayfirst=True)),
    # Taleo triple-line format: 
    #   "Closing Date"
    #   ":"
    #   "Jun 4, 2026, 11:59:00 PM"
    (r'Closing Date\s*:?\s*\n?(\d{1,2}\s+(?:Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)[a-z]*[\s,]*\d{4})',
     lambda m: parse_date(m.group(1).strip())),
    # Workday "Job Posting End" field
    (r'(?:Job Posting End|End Date|Closing date)[:\s]*(\d{4}-\d{2}-\d{2})', 1),
    # UNOPS "Deadline" field
    (r'(?:Deadline|Application deadline)[:\s]*(\d{4}-\d{2}-\d{2})', 1),
]
```

### Roster/Open-Ended Detection
Files listing multiple positions or open-ended calls should be separated from active vacancies:

```python
ROSTER_KEYWORDS = [
    'roster', 'Roster', 'ROSTER',
    'open-ended', 'Open-ended', 'Open Ended',
    'ongoing', 'Ongoing',
    'multiple positions',
    'multiple vacancies',
    'Talent Pool',
    'talent pool',
    'Expression of Interest',
    'Consultant Roster',
    'vacancy announcement',
    'generic vacancy',
]
```

If ANY keyword matches the file, classify as roster. Roster entries go in a separate section below active vacancies in the tracker.

## Grade Extraction — Org-Specific Patterns

```python
GRADE_PATTERNS = [
    # Standard UN: P-3, P-4, D-1, etc.
    (r'(P-\d+|D-\d+|G-\d+|NO[A-Z])', 1),
    # World Bank: GF, GG, GH
    (r'(Grade\s*(?:Level)?[:\s]*([A-Z]{2,3}))', 2),
    # IMF: A11, A12, etc.
    (r'(A\d{2})', 1),
    # OECD: A2, HG2
    (r'(HG\d+|A\d)', 1),
    # WHO: Band level B, Band level C
    (r'(Band\s+level\s+[A-E])', 1),
    # ITU: P-3/P-4 or just grade in text
    (r'(?:Grade|Level)[:\s]*([A-Z]-\d+)', 1),
]
```

## Pitfalls Discovered

### WHO Band B Consultant Rate
"Band level B - USD 7,500 per month" → $7,500 ÷ 22 working days ≈ $341/day < $350 floor.
**Rule:** Monthly consultant rates must be divided by 22, not compared directly to daily floor.
Affects: WHO GIS Specialist, WHO Data Engineering Developer (both Band B).

### World Bank Washington DC Roles
WB local recruitment is NOT always explicit in the "Recruitment Type" field. Check the Location field for "Washington, DC". Even if the role says "Associate" or "Lead" with no local-recruitment flag, a DC location means the candidate needs US work authorization. P6 = 4 for USA. No US work rights means these are effectively unreachable.

### ITU Roster Rate Ranges
ITU rosters have variable rate ranges (e.g. "$230-$430/day"). When the floor is below $350 but the ceiling is above, PASS — the candidate would negotiate the senior rate. Only disqualify when the MAX rate is below $350 or the role explicitly caps at a fixed low rate.

### ICMPD Junior ICT Officer — False Positive
ICMPD_1155 "Junior ICT Officer - Infrastructure" has "Junior" in title AND "P2" in the vacancy code. These are easy to miss in batch. Always check title AND vacancy code string for "P2"/"junior".

### ICRC Cookie-Banner Pages
ICRC scrapes from careers.icrc.org often return only cookie banner HTML + job search filters with zero actual JD content (10343 bytes of navigation UI, no duties/qualifications). These need to be flagged as "listing dumps" even though they're >10KB.

## Title-Only Scoring Mode (from tracker, no JD files on disk)

When scoring 70 entries from the tracker file directly (no JD files), use this alternative pipeline. Validated 2026-06-02 on a full rescore.

### Parser — Fixed-Width Tracker with Double-Space Columns

The tracker file uses `'  '` delimiters that don't align with logical columns. Split on `'  '`, take first 2 tokens as (num, org), last 3 as (score_emoji, vid, applied), middle as compound title+deadline. Extract deadline from end of middle via regex.

```python
for line in lines[9:79]:  # The 70 data lines
    parts = [p.strip() for p in line.rstrip('\n').split('  ') if p.strip()]
    if len(parts) >= 6:
        num, org = int(parts[0]), parts[1]
        score_emoji, vid, applied = parts[-3], parts[-2], parts[-1]
        middle = ' '.join(parts[2:-3])
        dm = re.search(r'(202[67]-\d{2}-\d{2}|TBD|Open \(Roster\))\s*$', middle)
        deadline = dm.group(1) if dm else 'TBD'
        title = middle[:dm.start()].strip().rstrip('…') if dm else middle.strip()
```

Titles are 44-char truncated with `…`. The `…` suffix is meaningful — strip it before scoring.

| Title Fragment | Likely Full Title | 
|---|---|
| `Programme Manager (Infrastructure Finance)…` | Programme Manager (Infrastructure Finance) P4 |
| `Behavioural Data Science Consultant, GPD/So…` | Behavioural Data Science Consultant, GPD/Social and Behaviour Change |

### P1 Calibration for Short Titles (max 25)

No full JD — score purely by keyword presence in truncated 44-char title:

| Category | Keywords | Points |
|---|---|---|
| AI/ML/Agentic | `\bai\b`, `artificial intelligence`, `machine learning`, `llm`, `agent`, `genai` | +10 |
| Data engineering | `data engineer`, `data science`, `data analytics`, `data platform`, `data pipeline` | +8 |
| Digital transformation | `digital transformation`, `digital strategy`, `digital` | +5 |
| Software | `software`, `developer`, `full.?stack`, `devops`, `web` | +5 |
| Telecom/connectivity | `telecom`, `connectivity`, `broadband`, `isp`, `transformation` | +4 |
| Cloud/Infra/Platform | `cloud`, `infrastructure`, `network`, `platform`, `system` | +4 |
| Architecture | `architect`, `solution`, `enterprise` | +3 |
| Project/Programme Mgmt | `programme manager`, `project manager`, `operations`, `coordinat` | +3 |
| Innovation/EdTech | `innovation`, `digital learning`, `edtech`, `learning`, `education` | +3 |
| GIS/Geospatial | `gis`, `geospatial`, `cadastral` | +3 |
| FinTech/Payments | `market infrastructure`, `payment`, `digital euro`, `finance` | +3 |
| ICT/IS broad | `information systems`, `information technology`, `ict` | +2 |
| Security/Cyber | `security`, `comsec`, `cyber` | +2 |
| Scientific/Research | `scientific`, `research` | +2 |
| Senior leadership | `chief`, `director`, `head of`, `deputy head` | +3 |

Cap at 25, min 0 (floor at 3 for titles with no keywords).

### P2 Seniority — Title-Only

| Title Contains | Score |
|---|---|
| `director`, `chief`, `head of`, `d-1`, `d-2`, `deputy head` | 13 |
| `senior` | 11 |
| `lead`, `manager` | 10 |
| `specialist`, `expert`, `advisor` | 9 |
| `officer`, `consultant`, `engineer` | 8 |
| none of above | 7 |

### P3 UN Fit — Title-Only

Use the same org→score map as JD mode. UNICEF gets +2 if truncated title contains `digital`, `ai`, `innovation`, `education`, `connectivity`, `ict`, `t4d`. +3 for `giga`, `learning passport`, `school`, `infrastructure finance`.

### P4 Education — Title-Only

Base 8 (MSc+MPhil meets any Master's requirement). 10 if title contains any engineering/IT/CS/telecom/security/technology keyword.

### P5 Language — Title-Only

Base 8 (English + Russian). Down to 6 for ICRC, 7 for UNESCO. Check for `french`/`francais` in title.

### P6 Logistics — Title-Only

Most UN orgs = Geneva/Vienna → 10. EU orgs (ECB, OECD, ICRC, NATO) → 10. IMF → 4 (DC). IAEA → 10 (Vienna is EU). Check title for `remote`/`home based` → 10, `belgrade` → 10.

### P7 Competitive Realism — Title-Only

Base 7. +4 AI/ML keywords. +3 UNICEF+digital/ed. +2 telecom/architecture. −3 Director/Chief/Exec. −2 GIS.

### Domain Penalties (applied to TOTAL, not hidden inside P1)

| Pattern | Penalty |
|---|---|
| `gis`, `geospatial` (not `ai` or `data science`) | −15 |
| `full.?stack` | −20 |
| `data engineer` (no `senior`) | −10 |
| `software developer`/`software engineer` (no `lead`/`senior`) | −15 |

### Deadline Edge Cases

- **IMF:** June 1 in CEST = still June 1 in EDT. Don't disqualify until June 2 CEST.
- **"TBD" / "Open (Roster)":** never expired.
- **Cutoff:** Use `datetime(2026, 6, 2)` for rescore date. Expired = deadline < (not ≤) cutoff.

### No JD? Don't Fabricate

Title-only scores are **provisional**. Every scoring detail line must show `TITLE_ONLY` as the JD status. The output file header notes that full JDs would refine scores.

### Output File Convention

Write to `UN_SECTOR_VACCANCIES_rescored.txt` alongside the original. Do NOT overwrite the original tracker — `_rescored` suffix signals provisional status.

File structure (438 lines, ~30 KB for 70 entries):

```
UN SECTOR VACCANCIES TRACKER — Rescored
Generated: YYYY-MM-DD | AGENT (DeepSeek V4 Flash)
Method: 7-parameter scoring engine (vacancy-compatibility-scoring-engine v4.1)
JD status: JD_ANALYZED | TITLE_ONLY
Scoring bands: ...
[separator]

🔵 VACANCY SUMMARY TABLE — RESCORED (sorted by score descending)
[table sorted by score DESC not deadline ASC]
--- DISQUALIFIED ---
[separator]

SCORING DETAILS — Arithmetic (P1+P2+P3+P4+P5+P6+P7 = TOTAL)
[per entry with explicit arithmetic]
📊 SUMMARY STATISTICS
TOP 10 BY SCORE
SURVIVORS (score >= 50)
```

## Verification
After batch scoring:
1. Spot-check 3-5 files manually using the full 7-parameter engine
2. Verify the arithmetic: open the scored file, pick a random entry, confirm P1+P2+...+P7 = Total
3. Check that hard-filtered files were genuinely disqualified (not false positives)
4. Verify color coding matches the June 2026 band scheme
5. For title-only mode: confirm all 70 entries were parsed (scan output, look for missing track numbers)
