# 03 — Copy-Forward Baseline Locks

## Problem

Ask an LLM to "update one field" in a state block and it will often regenerate the entire block from its gist of the conversation: paraphrased values, dropped keys, reordered lines, "helpfully" reformatted text. Each regeneration is 98% faithful — which is exactly the problem.

## Failure mode this prevents

State corruption that looks tidy. In a long pipeline, invisible mutation of untouched fields is deadlier than an obvious error, because nothing prompts anyone to check.

## The pattern

State lives in exactly one canonical `KEY=VALUE` block between explicit markers, carrying a monotonically increasing sequence number. An update must: echo the anchor's identity, declare which keys will change, copy the anchor **verbatim**, change only the declared keys, increment the sequence by exactly +1, and print exactly one block with nothing after it. If the anchor is not visible, the model must stop and request it — never rebuild state from memory.

## Copy-paste template

```
BASELINE LOCK (hard):
State lives only in:
BEGIN_STATE ... KEY=VALUE lines ... END_STATE   with STATE_SEQ=<n>
To update:
1. Echo the anchor: ANCHOR_SEQ=<n>, ANCHOR_FIRST_LINE=<...>, ANCHOR_LAST_LINE=<...>
   Cannot echo -> STOP: "PASTE LATEST STATE BLOCK — DO NOT REBUILD"
2. Declare UPDATED_KEYS=<k1,k2>
3. Copy the anchor verbatim; modify ONLY UPDATED_KEYS; set STATE_SEQ=n+1
4. Print exactly one block. No text after it. Never rebuild from memory.
```

## Worked example (demo values, deliberately fake)

Anchor:

```
BEGIN_STATE
STATE_SEQ=4
TICKER=DEMO-A
PRICE_STATUS=OK
REVENUE_TTM=4,180
NET_DEBT=MISSING
NOTES=awaiting H1 filing
END_STATE
```

Update instruction: mark NET_DEBT verified.

**Good run:** echoes `ANCHOR_SEQ=4`, declares `UPDATED_KEYS=NET_DEBT`, prints the identical block with only `NET_DEBT=320` changed and `STATE_SEQ=5`.

**Violation:** the new block also silently rewrites `NOTES=waiting for H1 filing`. One word changed, zero keys declared for it — this is the corruption class the lock exists to catch, and Pattern 04 is its tripwire.

## Cost and when to use

Feels bureaucratic for small tasks; becomes the backbone the moment state must survive more than two steps.
