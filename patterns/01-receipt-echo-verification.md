# 01 — Receipt-Echo Verification

## Problem

Ask an LLM to "look up X and use it" and three failure paths open: it recalls a stale value from training memory, it misreads the page, or it invents a plausible number. All three produce confident downstream computation with no audit trail.

## Failure mode this prevents

Silent fabrication. A fabricated input is worse than a missing one: it propagates through every computation, survives summarization, and reads as verified because it sits next to values that were.

## The pattern

A retrieved value does not exist until the model has (1) opened the source in the current turn and (2) echoed the raw line verbatim — label, value, and as-of date — **before** any use. The echo is the receipt. No receipt → the field is `MISSING`, and the pipeline either continues with `MISSING` or halts, per spec. Values from memory, from earlier turns, or "approximately known" are banned by name.

## Copy-paste template

```
RECEIPT RULE (hard gate):
Before any external value V may be used in reasoning or computation:
1. OPEN the source in this turn.
2. ECHO verbatim, before first use:
   RECEIPT_ECHO=<exact label + value line(s) + as-of date as shown by source>
3. Only values echoed in THIS turn exist. No echo -> V=MISSING.
Forbidden: values from memory, from prior turns, or "approximate" values.
If a required V is MISSING and the spec does not allow continuing, output:
HALT — DATA NOT VERIFIED. REASON: <not_opened | unreadable | ambiguous>
```

## Worked example (demo values, deliberately fake)

Task: fetch EXAMPLE-CO's latest revenue and compute gross margin.

**Bad run (violation):**

> EXAMPLE-CO's revenue is around 4.2B, so gross margin is roughly 31%.

No source opened, hedge words, no receipt — two fabrications compounding.

**Good run:**

> RECEIPT_ECHO="Revenue (FY2025): 4,180" — annual report p.2, as-of 2026-03-31
> RECEIPT_ECHO="Gross profit (FY2025): 1,254" — annual report p.2, as-of 2026-03-31
> GROSS_MARGIN=30.0% [COMPUTED_FROM_SOURCE: 1,254 / 4,180]

Every number in the output traces to an echoed line.

## Cost and when to use

Costs tokens per value and forbids shortcuts. Use it wherever a number feeds a decision. Skip it in casual conversation; never skip it in a pipeline.

