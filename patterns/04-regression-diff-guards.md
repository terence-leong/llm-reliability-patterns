# 04 — Regression Diff Guards

## Problem

Instruction is not enforcement. Even under a copy-forward lock (Pattern 03), drift happens. Trusting the model to have copied faithfully is the same mistake as trusting it to have looked something up.

## Failure mode this prevents

Out-of-scope mutation shipping unnoticed — the "one word changed in an untouched field" class of corruption.

## The pattern

Before printing updated state, the model performs a line-by-line diff of the candidate block against the anchor, **prints the actual changed-key list**, and asserts it is a subset of the declared keys plus bookkeeping fields. If the assertion fails, it prints a named error carrying the offending keys and stops — no state block is printed. Making the model emit the diff is the mechanism: it forces attention onto the comparison at exactly the moment drift would otherwise slip through.

## Copy-paste template

```
DIFF GUARD (hard; run before printing updated state):
1. Line-diff the candidate state vs the anchor.
2. Print: CHANGED_KEYS_ACTUAL=<comma list>
3. Print: DIFF_ASSERT = CHANGED_KEYS_ACTUAL ⊆ UPDATED_KEYS ∪ {STATE_SEQ, LAST_STEP}
4. If the assert fails, print exactly and stop:
   REGRESSION_ERROR — NON-UPDATED KEY CHANGED: <keys>
No state block may be printed after a failed assert.
Extension (value-downgrade ban): a previously verified field may not become
MISSING/N/A outside UPDATED_KEYS, even if the diff is otherwise legal.
```

## Worked example (demo values, deliberately fake)

Declared: `UPDATED_KEYS=PRICE_STATUS`

Model's own printed diff:

```
CHANGED_KEYS_ACTUAL=PRICE_STATUS,NOTES
DIFF_ASSERT = FAIL
REGRESSION_ERROR — NON-UPDATED KEY CHANGED: NOTES
```

The run halts, the human sees exactly which field drifted, the step reruns. Total cost of the catch: three printed lines.

## Cost and when to use

Three lines of overhead per state update. Pairs with Pattern 03 — 03 is the discipline, 04 is the tripwire. Use both or neither.
