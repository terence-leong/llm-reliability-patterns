# 06 — Atomic Step Execution

## Problem

Under output-budget pressure, models split interdependent computations across replies. "I'll compute the ratio in the next message" is a structural lie the model believes: intermediate context decays, and the deferred field silently never lands.

## Failure mode this prevents

Partial stages that look complete — outputs missing fields nobody notices are missing, because the visible part is polished.

## The pattern

Declare indivisible units. A stage whose sub-computations share intermediate state must complete in one pass or must not begin. If the remaining output budget cannot fit the whole stage, the model stops **before** the stage at a declared checkpoint and prints the completed state so far. A stage output missing any declared field is an invalid output and must not be printed. Splitting a stage across replies is a violation even if each half is individually correct.

## Copy-paste template

```
ATOMIC STAGE RULE (hard):
STAGE <X> is indivisible. Its outputs are: <list every field>.
- All outputs of STAGE <X> are produced in one pass, or the stage does not start.
- If the output budget is insufficient: STOP at CHECKPOINT <X-1>, print the
  completed state, and wait for the user.
- A stage output missing any declared field is invalid: do not print it.
- Splitting a stage across replies is a violation even if both halves are correct.
```

## Worked example (demo values, deliberately fake)

Invoice-processing stage declared atomic: `SUBTOTAL → TAX → TOTAL → VALIDATION_FLAG`.

**Violation:** a reply computes `SUBTOTAL=100` and `TAX=8`, then says "computing TOTAL next." The next reply summarizes the invoice; `TOTAL` and `VALIDATION_FLAG` never enter state, and nothing looks wrong.

**Good run:** the model sees the budget is tight, stops at the checkpoint before the stage, prints completed state, and executes all four fields in a single pass next turn.

## Cost and when to use

Costs an occasional extra turn at checkpoints. Essential wherever a stage's fields are consumed together downstream — a half-computed stage is worse than a delayed one.
