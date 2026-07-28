---
name: vacancy-compatibility-scoring-engine
description: >-
  Seven-parameter job compatibility scoring engine for User's UN system
  and international development job search (V5.0.0). Load before any scoring.
  Requires full JD — short-title scoring is prohibited.
  CV Repository: ~/CV_REPOSITORY_DATABASE.md
version: 5.1.0
author: Hermes Agent
license: MIT
metadata:
  hermes:
    tags: [scoring, un-vacancies, compatibility, evaluation, evidence-based, v5-0-0]
    related_skills: [cv-repository, tracker-file-format, un-jobs-search]
    references:
      - references/v5-integrity-validation.md — post-incident integrity check script and poison signatures for V5.0.0
---

# HERMES AGENT — JOB COMPATIBILITY SCORING ENGINE v5.0.0
## MODIFICATION PATCH FOR HERMES AU — VACANCY SCORING & TRACKER WRITE AGENT

---

## YOUR ROLE

The `vacancy-compatibility-scoring-engine` is the **manual one-by-one scoring skill** used when User pastes a single JD for scoring. For programmatic batch scoring of many JD files at once, use the scoring engine inside `un-jobs-search` (`score_all.py`) which auto-scans `JD_FILES/` and rebuilds `UN-VACANCIES-TRACKER.txt`.

### ARITHMETIC RULE — NON-NEGOTIABLE

After assigning all seven parameter scores, you MUST manually sum them: P1 + P2 + P3 + P4 + P5 + P6 + P7 = TOTAL. Write out the addition explicitly before stating the total. Never state a total that does not match the sum of the seven scores. This check is mandatory on every single scoring output.

### FULL-JD RULE — NON-NEGOTIABLE

You MUST have the full job description before scoring. A title, short description, or keyword summary is NOT sufficient. If User provides only a title or short description, respond: "I need the full job description to score this accurately. Please paste the complete JD including minimum requirements, desirables, and key accountabilities." Do not produce a score until the full JD is provided.

### TRACKER-ONLY OUTPUT PREFERENCE

When User asks you to score a vacancy and update the tracker, do NOT write CVs, cover letters, or application strategies unless explicitly asked. The expected output is:
1. Hard/soft filter check
2. 7-parameter scoring with arithmetic
3. Tracker update (if score >= 75 and no blocking flags)
4. Brief summary of what was added

Do not include TOP 3 GENUINE STRENGTHS, TOP 3 REAL GAPS, or APPLICATION STRATEGY sections unless User specifically asks for them. These are CV/cover-letter preparation steps, not tracker-update steps.

---

## SECTION 1 — CANDIDATE PROFILE AND CV REPOSITORY SOURCE

CANDIDATE: User
NATIONALITY: Serbian / Czech Republic dual citizen
BASE: Belgrade, Serbia
EU STATUS: Czech Republic citizenship = EU citizen with full EU/EEA/Swiss work and residence rights
EXPERIENCE: 26+ years international experience

MANDATORY CV REPOSITORY SOURCE:

The full CV repository must be loaded from this exact local path before scoring:

`~/CV_REPOSITORY_DATABASE.md`

This file is the authoritative source of truth for User's experience, education, skills, languages, certifications, employment history, project history and application evidence.

DO NOT rely on memory.
DO NOT rely only on summaries.
DO NOT score vacancies unless the CV repository has been loaded from this path in the current session.
DO NOT invent experience, certifications, tools, languages, degrees, or organizational exposure.

If the file cannot be loaded, stop and output:

**ERROR: CV_REPOSITORY_NOT_LOADED** — scoring aborted. Load `~/CV_REPOSITORY_DATABASE.md` before proceeding.

Key known profile anchors, to be verified against the loaded CV repository:

* UNICEF Giga / Meaningful Connectivity / school connectivity / device strategy
* UNICEF Learning Passport / technical specifications / offline-first EdTech hardware
* Olivia Education / AI-enabled learning systems / LMS integration / AI agents / LLM workflows
* HRAM Medical Group / COO / operations transformation / P&L / compliance / vendors / financial controls
* Tetra Pak SEE / regional IT governance across 11 countries / SAP / Exchange / VMware / service standardization
* Globaltel / Procescom / MVNO / MVNE / API integration / mobile apps / payment platforms / developer supervision
* Uganda Telecom / Zamtel / LAP Green / Liquid Telecom / ISP, telecom, infrastructure and capacity transformation
* Undersea fibre / SEACOM / EASSy / IRU negotiation / VSAT and IP transit cost optimization
* BUSPLUS / mPARKING / mobile money / high-volume transaction systems
* AI agents / local LLMs / MCP architecture / workflow automation
* Leadership of teams of 14, 86, 127 and 180+ people
* Strong domains: ICT4D, telecom, connectivity, EdTech, AI transformation, enterprise IT, digital infrastructure, operations transformation, commercial growth, healthcare operations, international development

Language evidence must be taken from the loaded CV repository. Do not assume languages beyond what is explicitly listed there.

Known language baseline from repository should be verified:

* Serbian: native
* English: fluent/business
* Russian: fluent/business

If a vacancy requires a language not present in the CV repository, apply the SOFT NO language rule in Section 3.

---

## SECTION 2 — HARD FILTERS AND SOFT-NO FILTERS

Apply filters BEFORE scoring.

There are two categories:

A. HARD NO / HARD DISQUALIFICATION
B. SOFT NO / SCORE-DOWNGRADE FLAG

If a HARD NO condition is triggered:

* status = DISQUALIFIED
* total score = 0
* no parameter scoring is performed
* no tracker write occurs
* output exact JD evidence that triggered the filter

If a SOFT NO condition is triggered:

* continue scoring
* add a clear flag
* reduce the relevant parameter score
* downgrade recommendation
* do not auto-write to tracker without human confirmation

---

### A. HARD NO / HARD DISQUALIFICATION FILTERS

#### FILTER 1 — DUTY STATION UKRAINE / UKRAINE-BASED ROLE

Hard disqualify if the duty station, work location, deployment location, or mandatory regular presence is in Ukraine.

Trigger examples:

* Ukraine
* Kyiv
* Kiev
* Kharkiv
* Lviv
* Odesa
* Dnipro
* any Ukraine field office
* duty station: Ukraine
* based in Ukraine
* deployment to Ukraine required

Output reason:
**HARD_NO_UKRAINE_DUTY_STATION**

Note:
If Ukraine is mentioned only as one of many programme countries but the role is remote or based elsewhere, do not automatically disqualify. Flag for review instead.

---

#### FILTER 2 — POSITION LOWER THAN P-3 OR P-3 EQUIVALENT

Hard disqualify if the role is below P-3 level or below P-3 equivalent.

Disqualify the following:

* Internship
* Intern
* Traineeship
* Fellow
* Fellowship
* Volunteer
* UNV Volunteer
* JPO unless explicitly P-3 equivalent or strategically exceptional and user confirms
* GS- any level
* G- any level
* NO- any level
* NO-A
* NO-B
* NO-C
* NO-D
* NO-1
* NO-2
* NO-3
* NO-4
* National Officer roles
* SC- any level below P-3 equivalent
* SB-1 through SB-4
* IPSA levels clearly below P-3 equivalent
* Local Service Contract roles below P-3 equivalent
* Assistant, Associate, Coordinator, Officer roles that are clearly junior and not P-3 equivalent
* Any role explicitly requiring only 0–2 years of experience unless it is a senior consultancy with high pay and user confirms

Output reason:
**HARD_NO_BELOW_P3_EQUIVALENT**

Allowed:

* P-3 and above
* P-3 equivalent and above
* P-4, P-5, D-1, D-2
* Senior Consultant
* International Consultant
* SSA / Consultancy / Retainer if senior, technical, and compensation is acceptable
* SB-5 / SC-10 or higher if clearly P-3 equivalent or above
* IPSA-10 or higher if equivalent to P-3 or above, subject to context
* National-style contract only if clearly open internationally and not national-only

---

#### FILTER 3 — NATIONALS-ONLY / NATIONAL OFFICER / NO-X ROLES

Hard disqualify if the role is created for nationals, local applicants, national officers, or local hires.

Trigger phrases:

* nationals only
* only nationals of
* for nationals of
* national consultant
* national officer
* NO-A
* NO-B
* NO-C
* NO-D
* NO-X
* NOC
* NOD
* local position
* local hire
* locally recruited
* locally recruited staff
* LRS
* national staff
* local recruitment
* recruiting locally
* local contract
* employment under local labour law
* must be a citizen of [country]
* open only to citizens of [country]
* candidates must be nationals of [country]
* resident nationals only

Output reason:
**HARD_NO_NATIONALS_ONLY_OR_NOX**

Important:
If a role says "national consultant" but is unclear whether international applicants are accepted, mark HARD NO unless there is explicit text saying international applicants may apply.

---

#### FILTER 4 — VOLUNTEERING / INTERNSHIP / FELLOWSHIP

Hard disqualify if the opportunity is unpaid, volunteer-based, internship, traineeship or fellowship.

Trigger phrases:

* volunteer
* volunteering
* UNV Volunteer
* internship
* intern
* traineeship
* trainee
* fellowship
* fellow
* unpaid
* stipend only
* learning opportunity only
* student placement

Output reason:
**HARD_NO_VOLUNTEERING_INTERNSHIP_FELLOWSHIP**

Exception:
Only override if user explicitly instructs that a specific fellowship or JPO opportunity should be reviewed despite this filter.

---

#### FILTER 5 — FUNCTIONAL MISMATCH

Hard disqualify if the role has zero meaningful content in any of User's relevant domains:

* ICT
* telecommunications
* digital infrastructure
* AI
* data
* digital transformation
* technology management
* operations management at senior level
* shared services transformation
* enterprise IT
* solution architecture
* EdTech
* ICT4D / T4D
* connectivity
* healthcare operations / digital health
* FinTech / payments / MVNO
* commercial leadership
* senior programme/project management
* infrastructure finance / digital infrastructure economics
* UN / development digital programmes

Use contextual judgment. Do not reject based on title alone.

Examples:

* "Communications Officer" writing press releases only = functional mismatch.
* "Communications Officer" managing digital platforms, analytics, AI or ICT systems = may pass.
* "Legal Officer" requiring bar/legal specialization only = functional mismatch.
* "Digital Governance / AI Policy Advisor" = may pass.

Output reason:
**HARD_NO_FUNCTIONAL_MISMATCH**

---

#### FILTER 7 — WORK AUTHORIZATION WITHOUT SPONSORSHIP

Hard disqualify if the vacancy explicitly states that the employer does not provide visa sponsorship or work authorization support, AND the position is in a country where User does not already have independent work authorization.

Trigger phrases:
* "Must have unrestricted work authorization in the country where this position is located"
* "The Foundation does not provide immigration-related sponsorship for this role"
* "Must be able to legally work in the country where this position is located without visa sponsorship"
* "No visa sponsorship available"
* "Must already have the right to work in [country]"
* "Work authorization required, no sponsorship provided"
* "Candidates must have existing work authorization"

Output reason:
**HARD_NO_WORK_AUTHORIZATION_SPONSORSHIP**

Important:
- User has EU work rights (Czech citizenship) — EU-based roles with this clause are NOT disqualified
- User has Serbian work rights — Serbia-based roles with this clause are NOT disqualified
- User does NOT have US work authorization — ALL US-based roles with this clause are HARD NO
- User does NOT have Kenya work authorization — Kenya-based roles with this clause are HARD NO
- User does NOT have India work authorization — India-based roles with this clause are HARD NO
- For other countries, check if User has independent work rights before disqualifying

This filter is especially common at US-based foundations (Gates Foundation, Clinton Foundation, etc.) and at Workday-ATS portals that auto-append the clause to every US job posting.

#### FILTER 6 — CONSULTANCY COMPENSATION FLOOR

For consultancy / SSA / retainer / individual contractor roles, hard disqualify if the stated compensation is clearly below User's minimum threshold.

Hard disqualify if:

* daily rate is below USD 350 or equivalent
* annualized full-time equivalent is below USD 80,000
* monthly full-time equivalent is below USD 6,500
* role is unpaid or stipend-only

Output reason:
**HARD_NO_COMPENSATION_BELOW_FLOOR**

If compensation is not stated:

* Do not disqualify.
* Add flag: RATE_UNCONFIRMED.
* Continue scoring, but Strategic Value should not exceed 3/5 unless the organization is highly strategic.

---

### B. SOFT NO / SCORE-DOWNGRADE FILTERS

#### SOFT FILTER 1 — REQUIRED LANGUAGE GAP

If the vacancy insists on a language that User does not speak according to the CV repository, this is a SOFT NO.

Trigger conditions:

* Required language is listed as mandatory / required / essential.
* Required language is not listed in the CV repository.
* Required language level is above User's documented level.

Examples:

* "Fluency in French required" and CV does not show French fluency.
* "Advanced Urdu required" and CV does not show Urdu.
* "Arabic required" and CV does not show Arabic.
* "Spanish required" and CV does not show Spanish.

Do not apply this soft no if the language is only desirable, an asset, advantage, or preferred.

Output flag:
**SOFT_NO_REQUIRED_LANGUAGE_GAP**

Scoring impact:

* P5 Language score should normally be 1–4/10 depending on severity.
* Final score should normally be capped at 75.
* If the required language is central to the job and daily stakeholder engagement, final score should normally be capped at 65.
* Recommendation should be downgraded at least one level.
* Tracker write requires human confirmation.

Important:
This is not a hard disqualification because some organizations may still consider candidates if the language is useful but not practically central. However, it must be clearly flagged as a screening risk.

---

#### SOFT FILTER 2 — UNCONFIRMED COMPENSATION

If compensation is not stated for a consultancy or short-term contract:

* Add flag: RATE_UNCONFIRMED
* Continue scoring
* Strategic Value should be conservative
* **Do NOT block tracker write for score ≥ 75 or for ICA/staff/permanent contracts.** Only block writes for pure consultancy roles with score < 75.

**🚨 PITFALL — Compensation range is NOT unconfirmed:**
If the JD states a compensation RANGE (e.g. "$280-450/day"), compensation IS stated. Do NOT apply RATE_UNCONFIRMED. Only block if the entire range is below the $350/day floor. A range that extends above $350 means the role passes the compensation check. RATE_UNCONFIRMED applies ONLY when no compensation figure is mentioned at all.

---

#### SOFT FILTER 3 — POSSIBLE OVERQUALIFICATION

If the role is technically eligible but appears substantially below User's seniority:

* Add flag: POSSIBLE_OVERQUALIFICATION
* Reduce Seniority score (P2) by 2-4 points
* **NEVER block tracker writes.** The user decides whether overqualification matters.
* Mention the risk in the output for the user's awareness

---

## SECTION 3 — SCORING FRAMEWORK (max 100 points)

### STEP 1 — HARD FILTER AND SOFT FILTER CHECK

Run all HARD NO filters before scoring:

1. Ukraine duty station
2. Below P-3 / below P-3 equivalent
3. National-only / NO-X / local-hire roles
4. Volunteering / internships / fellowships
5. Functional mismatch
6. Consultancy compensation below floor
7. Work authorization without sponsorship

If any HARD NO filter triggers:

* Output DISQUALIFIED JSON.
* score = 0.
* Stop.
* Do not compute P1–P7.
* Do not write to tracker.

Then run SOFT NO filters:

1. Required language gap
2. Unconfirmed compensation
3. Possible overqualification

If a SOFT NO filter triggers:

* Continue scoring.
* Add relevant flag.
* Downgrade relevant parameter.
* Require human confirmation before tracker write.

---

### PARAMETER 1 — Domain / Technical Fit (max 25 pts)

**Does the role's subject matter match User's demonstrated expertise?**

**Scoring method:**
1. Identify the role's PRIMARY domain from the JD
2. Identify any SECONDARY/TERTIARY domains mentioned
3. Assign base score from domain cap table
4. If the role spans 2+ domains that intersect with User's profile, add intersection bonus
5. Cap at 25

**Domain Caps (V5.0.0):**

| Primary Domain | P1 Max | Rationale |
|---|---|---|
| Telecom / Connectivity / ISP / Undersea fibre | 25 | Core strength + extremely rare IRU expertise |
| AI/ML / Agentic Systems / LLM / MCP | 25 | Builds frameworks, not just uses tools |
| FinTech / Payments / Mobile Money / Digital Banking | 22 | 500K tx/day, EUR 15M/month, platform architecture |
| EdTech / LMS / K-12 / AI Education | 22 | GIGA + Learning Passport + current Olivia EdTech |
| Executive Operations / General Management / COO | 20 | Board-level P&L, 187 staff, multi-entity |
| Health / Healthcare / Pharma / Medical | 20 | EUR 3.5M COO, 86 staff, full digital transformation |
| Digital Transformation (broad) | 20 | Strong but diffuse |
| ICT Management / IT Strategy / CIO | 18 | Group Director, infrastructure, governance |
| Data / Analytics / BI / M&E | 18 | Up from 10 — COO-level BI + GIGA monitoring frameworks |
| GIS / Geospatial / Remote Sensing | 16 | Up from -15 — GIGA connectivity mapping + geo data |
| Procurement / Supply Chain / Vendor Mgmt | 16 | UNICEF procurement specs + 58 public tenders |
| Security / Cybersecurity / InfoSec | 14 | Managed at COO level across 3 entities |
| Finance / Infrastructure Investment / Blended Finance | 16 | IRU + IFI meetings + ITU Academy PPP cert |
| Pure SWE / Software Engineering (no AI/ML) | 14 | Up from 8 — leads dev teams + builds tools like Hermes |
| Generic Admin / HR / Finance only | 10 | Capped but never penalized |
| Pure research / Academic only | 8 | Practitioner, not researcher |

**Intersection Bonus:**
If a role spans 2+ domains where User has competence, add +3 per additional domain intersection (max +6):
- Example: "AI + Education" = base (EdTech 22) + intersection (+3) = 25 cap → 25
- Example: "Telecom + Africa + Management" = base 25 + intersections (+6) = cap at 25

**Key V5.0.0 change: NO NEGATIVE PENALTIES.** Previous versions had:
- Was: GIS -15 → Now: 0 to +16
- Was: SWE -20 → Now: 0 to +14
- Was: Data Eng -10 → Now: 0 to +18
- Was: BI -10 → Now: 0 to +18

### PARAMETER 2 — Seniority & Experience Volume (max 15 pts)

| Grade | Score | Rationale |
|-------|-------|-----------|
| P-3 | 13-15 | Perfect — technical depth + management experience |
| P-4/P-5 | 13-15 | Direct match — COO, Group Director, Board-level |
| D-1/D-2 | 10-15 | Within scope — EUR 3.5M COO + 187 staff + Board reporting |
| IC/SSA Consultant (rate >=$350/day) | 10-15 | Rate-based seniority, any grade |
| Consultant (rate not stated, P-3+ grade) | 8-12 | Ambiguous but likely senior |
| Roster/Pool (no specific grade) | 10-13 | Pre-screening — high potential return |

**Soft-No override:** If POSSIBLE_OVERQUALIFICATION flag is active, reduce by 2-4 points.

**🚨 PITFALL — Roster positions are systematically over-scored by batch keyword scripts:**
Batch scoring tools add base seniority points to roster entries AS IF they were permanent staff vacancies. Roster entries should be scored as follows:
- Roster = SPECULATIVE match, not a real vacancy
- P2 max for roster: 11 (not 13) regardless of grade stated
- P7 bonus for roster: +1 maximum (not +3)
- Roster entries scoring 🔴 (75+) require **manual verification** — do not auto-apppend

**Example:**
- ITU Roster "Emerging Technologies Consultant" batch-scored at 90 → manual score: 72
- All roster entries flagged with: **ROSTER — HUMAN_REVIEW_REQUIRED**

**🚨 PITFALL — Duplicate keyword inflation in batch scoring:**
Batch scripts count "AI", "machine learning", "generative AI", "LLM", "large language model" as FIVE separate keyword hits when they describe ONE semantic concept. This inflates P1 by 15+ points artificially.
- Fix: Combine all AI/ML/LLM keyword matches into ONE semantic cluster capped at 22
- Fix: Combine all telecom/connectivity keywords into ONE cluster capped at 25
- Fix: Combine all EdTech/LMS/school keywords into ONE cluster capped at 22

**🚨 PITFALL — French language gap is silently ignored by batch scripts:**
The tool `batch_score_new.py` does NOT enforce the SOFT_NO_REQUIRED_LANGUAGE_GAP cap. Manual scoring MUST check French/Spanish/Arabic requirements explicitly.
- If "fluency in French is required" appears ANYWHERE in the JD: cap score at 75 maximum
- If French is central to daily work (e.g., Francophone Africa HQ, bilateral French stakeholders): cap at 65 maximum
- Flag ALL French-required entries with **SOFT_NO_REQUIRED_LANGUAGE_GAP**

### PARAMETER 3 — UN / IFI / Development Fit (max 15 pts)

| Org Type | Score |
|----------|-------|
| UNICEF, UNDP, WHO, ITU, UNESCO, ILO, FAO, WFP, UNHCR, UNOPS, UNFPA, IAEA, UNIDO, UNITAR, IC, WMO, UPU, IMO | 14-15 |
| World Bank / IFC / MIGA / IDA | 14-15 |
| IMF | 14-15 |
| AfDB / ADB / AIDB / EIB / EBRD | 13-15 |
| OECD / NATO / OSCE / Council of Europe | 13-14 |
| CHAI / BMGF / PEPFAR / Global Fund | 12-14 |
| INGOs (ICRC, MSF, IRC, Amnesty, Oxfam) | 10-13 |
| Bilateral aid (GIZ, FCDO, USAID, JICA, AFD) | 10-12 |
| Other development orgs | 8-10 |

**Bonuses:**
- Role mentions "Africa": +3 (10+ years real experience)
- Role mentions "connectivity" or "schools": +2 (GIGA direct)
- Role mentions "digital transformation": +2 (Hermes/OpenClaw current)
- Role explicitly mentions AI/LLM/agentic: +1 (production practitioner)
- Previous employment at this org: +3 (UNICEF direct advantage)

### PARAMETER 4 — Education & Credentials (max 10 pts)

| Factor | Score |
|--------|-------|
| MSc + MPhil Elec Eng / Telecom | Full (10) for technical role |
| MSc accepted + finance/investment role | 8-10 |
| ITU Academy "Financing School Connectivity" | +2 for EdTech/IFI/connectivity |
| UNICEF certs (Ethics, PSEA, BSAFE, InfoSec) | +2 for UNICEF roles |
| Private Pilot License | 0 (interesting but not relevant) |

### PARAMETER 5 — LANGUAGE REQUIREMENTS (max 10 pts)

All language scoring must be based ONLY on the loaded CV repository:
`~/CV_REPOSITORY_DATABASE.md`

Do not assume language ability from nationality.
Do not assume Czech language fluency from Czech citizenship unless explicitly listed in CV.
Do not assume French, Spanish, Arabic, Urdu or other languages unless explicitly listed in the CV repository.

Known baseline:
- Serbian: native
- English: fluent/business
- Russian: fluent/business

**Scoring:**

* Required languages fully met at required level: 9–10 pts
* Required language mostly met, minor level uncertainty: 7–8 pts
* Required language partially met: 5–6 pts
* Required language not met but not central to the job: 3–4 pts and flag SOFT_NO_REQUIRED_LANGUAGE_GAP
* Required language not met and central to the job: 1–2 pts and flag SOFT_NO_REQUIRED_LANGUAGE_GAP
* English only required and CV confirms fluent/business English: 10 pts

If a language is listed only as desirable / asset / advantage:
- Do not trigger SOFT NO.
- Award bonus only if CV confirms the language.
- Do not penalize if desirable language is missing.

If a non-covered language is mandatory:
- Add flag: SOFT_NO_REQUIRED_LANGUAGE_GAP
- Downgrade final recommendation.
- Do not auto-write to tracker without user confirmation.

### PARAMETER 6 — Logistics & Eligibility (max 10 pts)

| Factor | Score |
|--------|-------|
| Remote | 10 |
| EU/Schengen/Switzerland | 10 |
| Serbia duty station | 10 |
| Africa duty station | 10 | 10+ years real experience, lived there |
| London/English-speaking | 8 |
| Middle East | 7 |
| Asia (non-CIS) | 6 |
| Americas | 6 |
| Hardship/hard-to-reach | 5 |

### PARAMETER 7 — Competitive Realism (max 15 pts)

| Factor | Score |
|--------|-------|
| Rare intersection match (e.g. "undersea fibre + Africa + IRU") | 14-15 |
| Niche specialization (e.g. "SEACOM cable experience") | 13-15 |
| Current work alignment (Olivia Education, Hermes, OpenClaw) | 12-14 |
| UNICEF inside track (prior GIGA/Learning Passport work) | 12-14 |
| Strong domain + UN org (e.g. telecom + ITU) | 11-13 |
| Slightly above demonstrated track (D-2 without prior UN D-level) | 6-10 |
| Common role, many applicants (generic PM, generic admin) | 5-8 |
| Clear gap (requires credential User doesn't have) | 2-5 |

Key question: "Would a hiring manager interview User for this?"
Most UN job descriptions list 15+ requirements; candidates meeting 70% are strong.

---

## SECTION 4 — RECOMMENDATION THRESHOLDS

Use these recommendation thresholds:

≥ 85:
**APPLY IMMEDIATELY** — strong match, prioritize application effort

75–84:
**APPLY** — good to strong match, tailored CV recommended

65–74:
**APPLY SELECTIVELY** — possible, but gaps or risks exist

50–64:
**REVIEW / OPPORTUNISTIC** — borderline, apply only if strategic or pipeline is thin

< 50:
**SKIP** — not worth application investment

0:
**DISQUALIFIED** — hard filter triggered

Override rules:
* Any HARD NO = score 0, DISQUALIFIED
* Any SOFT_NO_REQUIRED_LANGUAGE_GAP = recommendation cannot be higher than APPLY SELECTIVELY unless user confirms language is acceptable
* **Scores ≥ 75 (APPLY or APPLY IMMEDIATELY): auto-write to tracker regardless of soft-no flags.** The only exception is SOFT_NO_REQUIRED_LANGUAGE_GAP — if that flag exists AND the score is ≥75, set tracker_write = false and human review required. All other soft-no flags (RATE_UNCONFIRMED, POSSIBLE_OVERQUALIFICATION) are overridden by a ≥75 score.
* RATE_UNCONFIRMED = Note the flag in the output but **NEVER block tracker writes** — ICA/staff contracts always have unstated compensation. Only block if the role is a pure consultancy AND score < 75.
* POSSIBLE_OVERQUALIFICATION = Mention risk in the output but **NEVER block tracker writes**. Add to flags array; P2 is already reduced appropriately. The user makes the call.
* If the vacancy is below P-3 equivalent, do not score; hard disqualify

---

## SECTION 5 — DISQUALIFIED OUTPUT FORMAT

Use this disqualified output:

```json
{
  "status": "DISQUALIFIED",
  "vacancy_title": "...",
  "vacancy_org": "...",
  "vacancy_grade": "...",
  "vacancy_source": "IP | UJ | UT | DIRECT",
  "filter_triggered": "HARD_NO_UKRAINE_DUTY_STATION | HARD_NO_BELOW_P3_EQUIVALENT | HARD_NO_NATIONALS_ONLY_OR_NOX | HARD_NO_VOLUNTEERING_INTERNSHIP_FELLOWSHIP | HARD_NO_FUNCTIONAL_MISMATCH | HARD_NO_COMPENSATION_BELOW_FLOOR",
  "filter_evidence": "exact JD text that triggered filter",
  "total": 0,
  "recommendation": "DISQUALIFIED",
  "tracker_write": false
}
```

---

## SECTION 6 — SCORED OUTPUT FORMAT

### SCORED OUTPUT — FLAGS ARRAY

The scored output must include a flags array.

Possible flags:

* SOFT_NO_REQUIRED_LANGUAGE_GAP
* RATE_UNCONFIRMED
* POSSIBLE_OVERQUALIFICATION
* LANGUAGE_DESIRABLE_ONLY
* COMPENSATION_LOW_BUT_ABOVE_FLOOR
* DEADLINE_SOON
* STRONG_STRATEGIC_VALUE
* CV_TAILORING_REQUIRED
* HUMAN_REVIEW_REQUIRED

**Tracker write rules for soft-no flags:**

* **Score ≥ 75 (APPLY or APPLY IMMEDIATELY):** auto-write to tracker. Ignore RATE_UNCONFIRMED and POSSIBLE_OVERQUALIFICATION for write-blocking. Only SOFT_NO_REQUIRED_LANGUAGE_GAP can still block writes at ≥75.
* **Score 65–74 (APPLY SELECTIVELY):** soft-no flags block writes — set tracker_write = false, require human confirmation.
* **Score < 65:** soft-no flags block writes — set tracker_write = false.
* **RATE_UNCONFIRMED only:** Never blocks writes for ICA/staff/permanent contracts. Only blocks for pure consultancy roles AND score < 75.

### FULL SCORED OUTPUT TEMPLATE

### JOB: [Title] | [Organisation] | [Grade] | [Duty Station] | [Contract Type]

**SCORE ARITHMETIC**
P1 (___) + P2 (___) + P3 (___) + P4 (___) + P5 (___) + P6 (___) + P7 (___) = TOTAL: ___

| Parameter | Max | Score | Reasoning |
|---|---|---|---|
| 1. Domain / Technical Fit | 25 | XX | ... |
| 2. Seniority & Experience Volume | 15 | XX | ... |
| 3. UN / IFI / Development Fit | 15 | XX | ... |
| 4. Education & Credentials | 10 | XX | ... |
| 5. Language Requirements | 10 | XX | ... |
| 6. Logistics & Eligibility | 10 | XX | ... |
| 7. Competitive Realism | 15 | XX | ... |
| **TOTAL** | **100** | **XX** | |

**FLAGS**: [list any flags, or empty]
**VERDICT**: APPLY IMMEDIATELY (85+) | APPLY (75-84) | APPLY SELECTIVELY (65-74) | REVIEW / OPPORTUNISTIC (50-64) | SKIP (<50) | DISQUALIFIED (0)
**TRACKER_WRITE**: true | false
**TRACKER_WRITE_BLOCKED_REASON**: ... (if false)

**TOP 3 GENUINE STRENGTHS**
**TOP 3 REAL GAPS OR RISKS**
**APPLICATION STRATEGY**

---

## SECTION 7 — KEYWORD REFERENCE (V5.0.0)

### TIER 1 — CORE DOMINANT
AI, artificial intelligence, machine learning, LLM, large language model, agentic, agent, MCP, Model Context Protocol, digital transformation, robotics, robot, humanoid, autonomous system, intelligent agent, telecom, connectivity, fibre, fiber, broadband, internet, undersea, submarine, cable, capacity, wholesale, transmission, IP transit, VSAT, satellite, IRU, SDH, FTTX, FTTH, FTTC, GPON, 3G, 4G, 5G, Wi-Fi, WiFi, wireless, mobile, cellular, ISP, MVNO, MVNE, mobile money, fintech, payment, digital banking, transaction processing, edtech, education tech, education, school, learning, LMS, Moodle, Canvas, K-12, K12, AI curriculum, GIGA, UNICEF, UNDP, World Bank, IMF, AfDB, IFI, blended finance, PPP, public-private partnership, infrastructure investment, development finance, education technology, connectivity for schools

### TIER 2 — MANAGEMENT & OPERATIONS
COO, chief operating, chief operations, operations director, executive, P&L, P and L, general management, managing director, director, head of, chief, lead, manager, coordinator, advisor, adviser, consultant, specialist, officer, expert, strategist, architect, project manager, programme manager, portfolio, delivery, change management, transformation, restructuring, merger, acquisition, due diligence, M&A, business development, growth, market development, entrepreneur, startup, start-up, founder, venture, revenue, commercial, sales

### TIER 3 — TECHNICAL BROAD
cloud, AWS, Azure, GCP, Kubernetes, Docker, DevOps, SRE, platform engineer, systems, enterprise, IT strategy, IT governance, information management, data, analytics, business intelligence, data warehouse, data architecture, GIS, geospatial, spatial, remote sensing, monitoring, evaluation, M&E, M and E, security, cybersecurity, information security, InfoSec, vendor, procurement, sourcing, supply chain, contract, standard, policy, regulation, ITU, regulatory, compliance, governance, audit, risk, QA, quality assurance

### TIER 4 — CONTEXTUAL
Africa, African, East Africa, West Africa, Southern Africa, Uganda, Zambia, Rwanda, Kenya, Niger, Ivory Coast, South Sudan, emergency, crisis, humanitarian, relief, health, medical, hospital, pharma, pharmaceutical, clinical, COVID, pandemic, disaster, resilience, Russian, CIS, Balkan, Serbia, Belgrade, EU, European, digital divide, school connectivity, meaningful connectivity

### TIER 5 — SOFT ADJACENT
stakeholder, partnership, private sector, government, ministry, public-private, investment, finance, economic, trade, competitiveness, innovation, research, science, technology, STEM, digital, sustainability, climate, green, energy, IoT, internet of things, SDG, sustainable development

---

## SECTION 8 — CURRENT-WORK OVERRIDE (V5.0.0)

Active current work (June 2026):
1. Olivia Education (remote/Africa): Moodle + Canvas LMS integration, API/SCORM bridges, AI curriculum for K-12, active IFI engagement (WB/IMF/AfDB)
2. Hermes Agent (personal project): Custom AI agent framework, MCP servers, local LLM deployment, Ollama/MLX pipelines
3. OpenClaw (personal project): AI agent binary modification, production deployment

Before finalising any score, check if the role's core function overlaps with Olivia Education or Hermes current work. These can add +6 to +10 points when the CV database doesn't show direct experience.

---

## SECTION 9 — CALIBRATION ANCHORS (V5.0.0)

| Score | Role | Org | Key Notes |
|-------|------|-----|-----------|
| 85+ | IT Strategist | IMF | Agentic AI architecture rare differentiator |
| 78-82 | AI Software Engineer Lead | WHO | AI/ML production delivery |
| 78-82 | UPSHIFT AI Consultant | UNICEF | AI strategy + education + UNICEF context |
| 76 | Telecom Engineer P-3 Roster | UN Secretariat | Perfect telecom/connectivity domain match |
| 74 | UPSHIFT AI & Digital Strategy Consultant | UNICEF | AI + EdTech + UNICEF; LMS gap prevents 75+ |
| 72 | Digital Learning Tech Spec | WTO | Active Moodle/Canvas integration at Olivia Education |
| 70 | CIO/D-2 | ILO | COO+Group CTO -> CIO-level fit; Geneva location |

---

## SECTION 10 — VERIFICATION CHECKLIST

- [ ] CV repository loaded from `~/CV_REPOSITORY_DATABASE.md`
- [ ] Full JD was read (not title-only) OR explicitly flagged as TITLE_ONLY
- [ ] Hard filters (6 filters) fired in order before scoring
- [ ] Soft no filters (3 filters) checked
- [ ] All 7 parameters scored with evidence-based justification
- [ ] Intersection bonus applied correctly (if multi-domain role)
- [ ] Arithmetic shown explicitly: P1+P2+P3+P4+P5+P6+P7 = TOTAL
- [ ] Verdict label applied per V5.0.0 thresholds
- [ ] Flags array included
- [ ] tracker_write set correctly: true for scores ≥75 (unless SOFT_NO_REQUIRED_LANGUAGE_GAP), otherwise false for <75 with soft-no flags
- [ ] Top 3 strengths + Top 3 gaps listed honestly
- [ ] Application strategy is specific
