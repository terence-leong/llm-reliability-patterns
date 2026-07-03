# 07 — Self-Check Gates

## Problem

Completion bias peaks at the end of a reply — precisely where rule-following is weakest. Instructions issued at the top of a prompt decay by the bottom; the model would rather ship something plausible than verify it.

## Failure mode this prevents

Confident non-compliance: outputs that read as finished while quietly violating receipt, diff, schema, or attempt-count rules — and then asking the user to approve them.

## The pattern

Every step ends with a mandatory compliance check against a short hard-fail list. The output of the check is exactly one line: a pass, or a named failure of ten words or fewer that forces a rerun. On failure the model prints **only** that line — no state, no summary, no approval request. Forcing the model to name a violation re-invokes the entire rulebook at the moment of highest drift risk, and the ≤10-word constraint forbids vague excuses.

## Copy-paste template

```
SELF-CHECK GATE (run at the end of every step, before any approval request):
Hard-fail conditions: unverified number used | receipt missing | untagged value |
diff outside declared keys | schema drift | required field absent | partial stage |
attempt count short of spec.
Output exactly one line:
  SELF_CHECK=PASS — proceed to user gate
or
  Rerun this step — <≤10 words naming the violation>
On fail: print only that line. No state block, no approval request, no summary.
```

## Worked example

Well-formed failure lines (each names the violation, none exceeds ten words):

```
Rerun this step — missing receipts for gross margin
Rerun this step — attempt count short: required 3, did 1
Rerun this step — schema drift: key renamed
Rerun this step — diff outside declared keys: NOTES
```

A malformed failure line — "Rerun this step — something seems off" — is itself a violation: it names nothing.

## Cost and when to use

One line per step. The cheapest pattern in this collection and the one that catches everything the others miss.
