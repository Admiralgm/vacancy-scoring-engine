# Deadline-Sort Rescore Output — Validated 2026-06-02

## When to Use

After producing the primary `_rescored.txt` (sorted by score descending), produce a
**deadline-ascending variant** as a second output file: `_rescored_by_deadline.txt`.

## Rationale

The score-descending variant tells User which vacancies are *best-fitting*. The
deadline-ascending variant tells him what is *most urgent*. Both views serve
different decision points — the first prioritises applications, the second prevents
missing deadlines.

## Pipeline

1. From the existing `_rescored.txt`, parse the VACANCY SUMMARY TABLE rows
2. For each row, extract: org, title, deadline, score, VID, applied, status
3. Sort by deadline ascending:
   - Dated entries first (`2026-06-01` → `2026-12-31`)
   - `TBD` entries after all dated entries
   - `Open (Roster)` entries last
4. Re-number sequentially from 1
5. Update the header line to read: `"RESCORED (sorted by deadline ascending)"`
6. Append the deadline-sorted table, preserve all original header/footer/scoring-detail sections unchanged
7. Save as `UN_SECTOR_VACCANCIES_rescored_by_deadline.txt`

## Parsing Strategy (Fixed-Width Lines)

Use **double-space splitting** on the raw table lines to recover the 8 columns:

```
#     Organization             Position Title                               Deadline       Score      Vacancy ID                   Applied  Status
```

Split on `\s{2,}` yields 8 parts per row (or 7 if the organization name doesn't fill
its field completely). The deadline is always the 4th part (0-indexed: parts[3]).

Fallback for entries where deadline is fused with emoji (like `Open (Roster) 🟢 39`):
- Parse by finding the organisation name first, then extracting the deadline substring
  between organisation's end and the score emoji

## Deadline Timezone Edge Case

If current time is past midnight CEST but before midnight in Washington DC:
- IMF and World Bank deadlines should use **Eastern Daylight Time (EDT, UTC-4)**
- UN deadlines in New York also use EDT
- European org deadlines use **Central European Summer Time (CEST, UTC+2)**
- Rule of thumb: if it's before 06:00 CEST, DC is still the previous day

This matters because User operates in CEST (Belgrade). At 23:40 CEST = 17:40 EDT
on the same calendar day. So an IMF deadline on 2026-06-01 is still open until
06:00 CEST on 2026-06-02.

## Output File Location

```
~/Downloads/DATA_REPOSITORY/UN_SECTOR_VACCANCIES_rescored_by_deadline.txt
```

Same directory as the primary rescored file, same `.txt` extension. Both files
share the identical `SCORING DETAILS` section (referenced by original tracker #,
not the new sequence #).

## Verification

After writing, check:
- [ ] Row count matches original `_rescored.txt` count
- [ ] First rows are nearest deadlines (today/tomorrow)
- [ ] Last rows are TBD/Open (Roster)
- [ ] Scoring details section is identical to original (no data loss)
- [ ] Table header says `"sorted by deadline ascending"` not `"score descending"`
