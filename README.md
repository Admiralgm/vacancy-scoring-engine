# Vacancy Compatibility Scoring Engine

Seven-parameter, 100-point evidence-based job compatibility scoring engine for UN system and international development vacancies. V5.0.0 with domain-capped P1 methodology, hard/soft filters, and calibration anchors.

## Architecture

```
vacancy-scoring-engine/
├── skill/
│   ├── SKILL.md              # Full scoring engine spec (34KB)
│   └── references/           # 22 calibration & reference docs
│       ├── application-framing-examples.md
│       ├── batch-scoring-bugs-*.md (3 files)
│       ├── batch-scoring-calibration-2026-06-03.md
│       ├── batch-scoring-workflow.md
│       ├── batch-script-inflation-bugs-impactpool-2026-06-21.md
│       ├── chai-scoring-calibration-2026-06-09.md
│       ├── chatgpt-inflation-calibration-2026-05.md
│       ├── current-work-overrides-database-2026-06.md
│       ├── deadline-sort-output-2026-06.md
│       ├── domain-capped-p1-methodology-2026-06.md
│       ├── imf-compensation-*.md (2 files)
│       ├── impactpool-html-scrape-patterns-2026-05.md
│       ├── manual-scoring-session-10-entry-calibration-2026-06-21.md
│       ├── pmi-private-sector-scoring-pattern-2026-06-09.md
│       ├── programmatic-batch-scoring-2026-06.md
│       ├── short-title-vs-full-jd-calibration-2026-05.md
│       ├── unicef-scoring-calibrations-2026-06-25.md
│       ├── v5-0-design-summary-and-deployment-status-2026-06-09.md
│       ├── v5-integrity-validation.md
│       └── wto-calibration-2026-06-03.md
├── scripts/
│   └── verify_tracker_alignment.py
├── .gitignore
├── LICENSE
└── README.md
```

## Scoring Framework (7 Parameters, max 100)

| Parameter | Max | Description |
|-----------|-----|-------------|
| P1: Domain / Technical Fit | 25 | Domain-capped by primary domain, intersection bonuses |
| P2: Seniority & Experience | 15 | Grade-based scoring with roster pitfall awareness |
| P3: UN / IFI / Development Fit | 15 | Org type, Africa bonus, connectivity bonus |
| P4: Education & Credentials | 10 | MSc+MPhil, ITU Academy, UNICEF certs |
| P5: Language Requirements | 10 | CV-verified, soft-no on gaps |
| P6: Logistics & Eligibility | 10 | Remote/EU/Serbia/Africa = 10, US = 6 |
| P7: Competitive Realism | 15 | Would a hiring manager interview? |

## Hard Filters (6)

1. **Ukraine duty station** — auto-disqualify
2. **Below P-3 equivalent** — auto-disqualify
3. **Nationals-only / NO-X** — auto-disqualify
4. **Volunteering / internship / fellowship** — auto-disqualify
5. **Functional mismatch** — zero domain relevance
6. **Consultancy compensation below floor** — <$350/day or <$80K/yr
7. **Work authorization without sponsorship** — for countries without independent work rights

## Soft Filters (3)

1. **Required language gap** — cap at 75, flag for review
2. **Unconfirmed compensation** — flag but don't block staff/ICA
3. **Possible overqualification** — reduce P2 by 2-4, never block

## Recommendation Thresholds

| Score | Verdict | Action |
|-------|---------|--------|
| ≥85 | APPLY IMMEDIATELY | Auto-write to tracker |
| 75-84 | APPLY | Auto-write to tracker |
| 65-74 | APPLY SELECTIVELY | Human confirmation needed |
| 50-64 | REVIEW / OPPORTUNISTIC | Pipeline only |
| <50 | SKIP | No tracker write |
| 0 | DISQUALIFIED | Hard filter triggered |

## Domain Caps (P1)

| Domain | Max |
|--------|-----|
| Telecom / Connectivity / ISP / Undersea fibre | 25 |
| AI/ML / Agentic Systems / LLM / MCP | 25 |
| FinTech / Payments / Mobile Money | 22 |
| EdTech / LMS / K-12 / AI Education | 22 |
| Executive Operations / COO | 20 |
| Health / Healthcare / Pharma | 20 |
| Digital Transformation (broad) | 20 |
| ICT Management / IT Strategy / CIO | 18 |
| Data / Analytics / BI / M&E | 18 |
| GIS / Geospatial | 16 |
| Procurement / Supply Chain | 16 |
| Finance / Infrastructure Investment | 16 |
| Security / Cybersecurity | 14 |
| Pure SWE (no AI/ML) | 14 |
| Generic Admin / HR / Finance only | 10 |
| Pure research / Academic | 8 |

## Known Pitfalls (V5.0.0)

- **Roster over-scoring**: Batch scripts add full seniority to speculative roster entries. Manual cap: P2 max 11, P7 max +1.
- **Keyword inflation**: "AI" + "machine learning" + "LLM" counted as 3 hits for 1 concept. Combine into semantic clusters.
- **French language gap**: Batch scripts silently ignore. Manual scoring MUST enforce 75 cap for French-required roles.
- **Compensation range ≠ unconfirmed**: A stated range ($280-450/day) IS confirmed. Only flag RATE_UNCONFIRMED when NO figure exists.

## Calibration Anchors

| Score | Role | Org |
|-------|------|-----|
| 85+ | IT Strategist | IMF |
| 78-82 | AI Software Engineer Lead | WHO |
| 78-82 | UPSHIFT AI Consultant | UNICEF |
| 76 | Telecom Engineer P-3 Roster | UN Secretariat |
| 74 | UPSHIFT AI & Digital Strategy | UNICEF |
| 72 | Digital Learning Tech Spec | WTO |
| 70 | CIO/D-2 | ILO |

## License

MIT — see [LICENSE](LICENSE)
