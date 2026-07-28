# Batch Scoring Workflow — Script Reference

## What Changed (2026-06-03)

The `impactpool-scan` skill now ships a standalone `scripts/batch_scoring.py` that
implements this full batch-scoring methodology. It replaces ad-hoc `/tmp/batch_score.py`
scripts that were previously written from scratch each session.

## When to Use the Script

Run the script instead of writing scoring Python inline:

```bash
python3 /path/to/impactpool-scan/scripts/batch_scoring.py [/path/to/workdir]
```

The script:
1. Scans all .md files in workdir/{impactpool,UNjobsnet,UNTALENT}
2. Applies hard filters (Ukraine, nationals-only, grade floor, intern/volunteer, expired)
3. Assigns P1-P7 via keyword/pattern matching (not full JD reading)
4. Applies domain penalties (GIS -15, SWE -20, Data Eng -10, etc.)
5. Sorts by deadline ascending, writes tracker + .bak
6. Outputs color-coded distribution to stdout

## Validation

Validated on 390 files (2026-06-03). Results aligned with manual spot-checks on
top 15 entries. Batch-scored 204 active + 186 disqualified.

## When NOT to Use the Script

For individual JD scoring or spot-checking top candidates, use the manual
7-parameter engine format (P1-P7 arithmetic with explicit reasoning per row).
The script is a bulk filter — it lacks the nuanced reasoning the manual mode
provides for edge cases.