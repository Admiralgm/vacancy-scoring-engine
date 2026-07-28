# Domain-Capped P1 Scoring Methodology — June 2026

## The Problem

Simple keyword counting for P1 (Domain Fit) systematically overestimates compatibility by +15-20 points. The root cause: a role's keyword density (how often "data", "AI", "digital" appear in the JD) has **no correlation** with whether the role actually demands deep expertise in those areas.

**Real-world proof:** The IFRC Senior Data Architect JD mentions "data" 40+ times because data IS the entire job. Keyword P1 gave 22/25. Honest P1: 12 (User's data strength is 3/5 — moderate, not expert).

## The Fix — Domain-Capped P1

### Step 1: Classify the role's primary domain

Read the JD's "Key Accountabilities" / "Duties and Responsibilities" section, not the keywords in the intro or qualifications. Ask: "What will this person DO 60%+ of their time?"

| Primary Domain | Detection Signals |
|---|---|
| Telecom/Connectivity | ISP, fibre, satellite, broadband, VSAT, connectivity, network infrastructure, undersea cable, backhaul, last mile, spectrum |
| AI/ML/Agentic | Machine learning, LLM, agent, RAG, prompt engineering, model fine-tuning, AI pipeline, NLP, generative AI |
| Digital Transformation | Digital, transformation, innovation, modernisation, e-services, digitalisation (watch for over-broad use) |
| ICT Management/IT Strategy | ICT, IT infrastructure, IT services, enterprise architecture, IT governance, IT operations, technology strategy |
| Data Engineering/Analytics | Data engineer, data pipeline, ETL, data warehouse, BI, analytics, data lake, data architect, data governance |
| Finance/Infrastructure Investment | Infrastructure finance, PPP, project finance, IRR, DCF, investment, treasury, financial modelling |
| Management-Only | Programme management, operations management, administration, coordination, COO, country representative |
| GIS/Remote Sensing | GIS, remote sensing, satellite imagery, geospatial, spatial analysis |
| Pure SWE (no AI/ML) | Software engineer, full stack, web developer, application development, frontend, backend, API development |

### Step 2: Apply the P1 cap

| Detected Primary Domain | P1 Max |
|---|---|
| Telecom/Connectivity | 22 |
| AI/ML/Agentic | 22 |
| Digital Transformation | 20 |
| ICT Management/IT Strategy | 18 |
| Data Engineering/Analytics | 16 |
| Finance/Infrastructure Invest | 16 |
| Management-Only (non-tech) | 14 |
| GIS/Remote Sensing | 10 |
| Pure SWE (no AI/ML) | 8 |

### Step 3: Score within the cap using keyword density NOT raw count

Within the cap, use a scaled approach:

- **5-6 mentions of domain keywords** + role title matches domain → high end of cap
- **3-4 mentions** → mid-range
- **1-2 mentions** (or role is secondary to another domain) → low end
- **0 mentions** but domain inferred from org/grade → 1-2 pts

Example: A "Digital Transformation Consultant" at UNIDO mentioning "digital" 6x and "transformation" 4x → P1=18 (capped at 20). A "Data Engineer" at IMF mentioning "data" 30x → P1=16 (capped at 16).

### Step 4: Check secondary keywords

Add 0-2 bonus points if the role has secondary overlap with a second domain where User is strong:
- Telecom role + AI mention → +1-2
- Digital transformation + connectivity → +1-2
- ICT management + AI → +1

Never exceed the P1 cap even with secondary overlap.

## Post-Scoring Distribution Check (MANDATORY)

After scoring all entries in a batch:

1. Count entries at each tier
2. Check against realistic upper bounds:

| Tier | Realistic Count (per ~200) | If exceeding, fix: |
|---|---|---|
| 🔴 75+ | 3-6 | P1 caps too high; add role-type penalty |
| 🟠 65-74 | 15-25 | P1 secondary-scope bonus too generous |
| 🔴+🟠 combined | 18-31 | Distribution too aggressive — lower caps |
| Average | 55-62 | Whole curve shifted up; reduce baseline scores |

3. Manually verify top 5 entries with full 7-parameter engine
4. If any manual score differs from batch score by >10 points → recalibrate

## Concrete Example: Scoring a "Data Architect"

**Role:** IFRC Senior Data Architect (VD 1214841)
**Keyword count:** "data" ~40x, "architecture" ~8x
**Keyword-scored P1:** 22/25 (keyword algorithm)
**Honest classification:** Primary domain = Data Engineering → P1 max = 16
**Honest P1 score:** 12 (mid-range — data is secondary strength)
**Delta:** -10 points on P1 alone → difference of 18 pts in total score (47 vs 65)

## Concrete Example: "Cloud Engineer" Trap

**Role:** UNJSPF "Cloud Engineer" P-4 (Oracle Fusion Financials)
**Actual work:** Oracle ERP pension administration and financial systems
**Keyword trap:** "cloud", "engineer", "AWS/Azure" appear in intro but actual duties are ERP configuration
**Honest classification:** Management-Only (non-tech ERP ops) → P1 max = 14
**Honest P1 score:** 6 (no telecom, no AI, no digital transformation)
**Keyword-scored P1:** 18 (because "cloud" and "engineer" matched tech keywords)
**Total swing:** 68 (keyword) → 49 (honest) = -19 pts

## Integration with batch_scoring.py

The batch_scoring.py script in impactpool-scan/scripts/ must implement these P1 caps. Use the STRENGTH map and primary domain detection from rescore_brutally_honest_v3.py in WORKDIR as the reference implementation.