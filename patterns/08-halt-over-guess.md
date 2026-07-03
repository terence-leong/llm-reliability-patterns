# 08 — Halt-Over-Guess Protocols

## Problem

Models experience stopping as failure, so they guess. Every other pattern in this collection depends on halting being cheap, legible, and rewarded — otherwise the model routes around its own gates with a plausible answer.

## Failure mode this prevents

The hedge-smuggled guess: "likely around 4.2B" entering a pipeline as if it were data. Hedge vocabulary is how fabrication dresses as caution.

## The pattern

Predeclare exact stop strings for each failure class. A stop string is greppable, names its reason, and specifies the next action — so a halt is a fully-formed, machine-checkable output rather than an apology. State the priority order explicitly in the prompt: **Correct > Fast > Elegant; stopping is preferred to guessing.** Ban hedge vocabulary in data contexts by listing the words. Treat a correctly-formed halt as a successful output.

## Copy-paste template

```
HALT PROTOCOL (keystone):
Priority: Correct > Fast > Elegant. Stopping is preferred to guessing.
When a hard gate fails, print the exact stop string for its class and end the reply:
  HALT — DATA NOT VERIFIED.
  REASON: <missing_source | unreadable | ambiguous | not_opened>
  ACTION REQUIRED: <corrected input | user approval | paste latest state>
Banned in data contexts: "likely", "around", "typically", "should be",
"roughly", "from memory".
A correctly-formed HALT is a successful output, not a failure.
```

## Worked example (demo values, deliberately fake)

Task: pull the share count for DEMO-A. The source shows two listings with different counts and no disambiguation.

**Bad run:** silently picks one listing and proceeds. Downstream, every per-share figure inherits an unflagged coin flip.

**Good run:**

```
HALT — DATA NOT VERIFIED.
REASON: ambiguous (two listings, conflicting share counts)
ACTION REQUIRED: user to specify listing (primary vs secondary)
```

Ten seconds of user input replaces an invisible 50% error rate.

## Why this is the keystone

Patterns 01–07 detect problems; this one makes the detection survivable. Every gate in this collection terminates here when it fires. If halting is punished, all of them silently stop working.
