# Impactpool HTML-Scraped File Pattern Reference
## Discovered during batch scan of 173 files on 2026-05-31

## FILE STRUCTURE
When Impactpool job pages are scraped via browser automation, the resulting .md files have this consistent structure:

```
Lines 1-5:       Raw title + Source URL
Lines 5-365:     Cookie consent HTML, cookie tables, ad/tracking scripts (complete noise)
Lines 370-380:   KEY METADATA BLOCK (the most important area)
Lines 380-400:   Impactpool-generated AI summary paragraph
Lines 400+:      Actual job description (tasks, qualifications, requirements)
```

## METADATA BLOCK (lines 370-380) — Field Positions
Position varies by +/- 1 line, but the relative order is reliable:

```
Line 370: Organization name     e.g. "IMF - International Monetary Fund"
Line 371: Duty station          e.g. "Washington D.C."
Line 372: National flag         e.g. "National", "International", "Both national and international"
Line 373: Grade/Level           e.g. "A11, A12 level" or "P-3, International Professional"
Line 374: Languages             e.g. "Speaks English" or "Speaks Chinese, Arabic, English, French, Russian, Spanish"
Line 375: Deadline              e.g. "Application deadline: June 02, 2026"
```

## NATIONAL/INTERNATIONAL FLAG — CRITICAL INTERPRETATION
Impactpool uses "National" inconsistently:

- **UN orgs** (UNICEF, ILO, WHO, UNDP, WFP): "National" + NO grade = nationals-only = FAIL
- **INGO/NGO** (DRC, CHAI, IRC, Medair, WHH): "National" = locally-employed under local terms = NOT nationals-only (User's EU passport satisfies work permits for EU stations)
- **IFRC/ICRC**: "National" = National Staff grade = nationals-only = FAIL

**Key test:** If duty station is EU/Schengen and org is INGO, "National" is NOT a disqualifier.

## WORLD BANK LOCAL RECRUITMENT DETECTION
World Bank positions on Impactpool are a special trap:

1. Lines 370-375 show "Both national and international" — looks promising
2. But deeper in the file (~line 405-410) there's "Local Recruitment" field
3. If BOTH appear: it's a local-recruitment position (Chennai, Washington DC local, Sofia local)

Always grep for "Local Recruitment" when dealing with WB positions.

## COMMON GRADE PATTERNS FOUND IN THIS SCAN

| Grade on Impactpool | Meaning | Pass/Fail |
|---|---|---|
| P-3, P-4, P-5 | Standard UN Professional | PASS |
| D-1, D-2 | UN Director | PASS |
| GS-4, GS-5, GS-6, GS-7 | General Service | FAIL (< P-3) |
| NO-A, NO-B, NO-C | National Officer | FAIL (nationals-only) |
| GG, GH, GF | World Bank grades | GG/GH = PASS (~P-3+), GF = mixed |
| AD-8, AD-9+ | EU Agency grades | PASS (~P-3+) |
| A11, A12 | IMF grades | PASS (~P-3+) |
| A2, A4 | ESA grades | PASS (~P-3+) |
| B2 | ICRC level | FAIL (below B3/P-3) |
| UG | Ungraded (consultant) | Check for daily rate >= $350 |
| SSA8 | WFP SSA | Mixed - check seniority signals |
| Consultant | No specific grade | Check for daily rate >= $350 |
| Mid level | Vague | Check JD for seniority signals |
| 4 level | INTERPOL grade | Check against equivalency table |
| Grade 5, 6-7 | CERN/EMBL grades | Generally below P-3 = FAIL |

## NATIONAL-ONLY PATTERNS TO GREP FOR
```
"national staff"          -> IFRC/ICRC national positions
"nationals only"          -> explicit restriction
"National Post"           -> UNFPA style
"Local Recruitment"       -> WB local positions
"NO-" (NOA, NOB, NOC)     -> National Officer grade
"NPO"                     -> National Professional Officer
"nacional" / "nationale"  -> Spanish/French versions
```

## UKRAINE DETECTION
Grep for: ukraine, kyiv, kiev, dnipro, odesa
If found anywhere in the file -> immediate FAIL

## INTERNSHIP/VOLUNTEER DETECTION
Grep for: internship, intern, volunteer, volontariat, stagiaire, estagiario, unpaid
But beware of false positives — these words can appear in "similar jobs" sidebar sections.
Verify the actual role title and grade.
