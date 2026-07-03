# 05 — Schema Drift Detection

## Problem

Across a long session, key names mutate (`REV_GROWTH` becomes `REVENUE_GROWTH`), casing shifts, order shuffles, a field quietly vanishes. Humans skim past it. Parsers, downstream steps, and future runs break silently.

## Failure mode this prevents

Structural rot: state that still looks like state but no longer machine-matches its own history, so comparisons, merges, and automated checks all degrade to guesswork.

## The pattern

The schema is canonical: exact key spelling, casing, order, and count are locked. Any deviation is a hard stop that names the offending key — never a silent auto-correct. Repairs are permitted but must be explicit and format-only: values never change during a repair. Corollary benefit: a locked schema is what makes chat output machine-parseable — it is the difference between a conversation and a pipeline.

## Copy-paste template

```
SCHEMA LOCK (hard):
The state schema is canonical: exact key spelling, casing, order, and count.
- No additions, deletions, renames, or reordering without an explicit
  schema-change instruction from the user.
- On any drift, print exactly and stop:
  SCHEMA_ERROR — KEY NAME NOT CANONICAL: <bad_key>
  or, for structural damage:
  HALT — FORMAT DRIFT (SCHEMA_LOCK)
- Repairs are format-only and must be announced. Values never change
  during a repair; unknowns are restored as MISSING, never invented.
```

## Worked example (demo values, deliberately fake)

Candidate block arrives with `Revenue_TTM=4,180` (casing drift) and `NOTES` absent (silent deletion):

```
SCHEMA_ERROR — KEY NAME NOT CANONICAL: Revenue_TTM
SCHEMA_ERROR — KEY MISSING FROM CANONICAL SET: NOTES
```

No auto-fix, no state printed. The repair rerun restores `REVENUE_TTM` and reinstates `NOTES` with its last verified value — announced, format-only.

## Cost and when to use

Near-zero cost once the schema exists. Mandatory anywhere output feeds a parser, a spreadsheet, or a future run of the same pipeline.
