# LLM Reliability Patterns

Generic, IP-safe engineering patterns for forcing honesty in LLM data pipelines.

## Why this exists

LLMs are completion engines: when a value is missing, ambiguous, or expensive to verify, the path of least resistance is to produce something plausible and keep going. In a data pipeline, plausible-but-unverified is worse than absent — it corrupts everything downstream while looking finished. These patterns convert "sounds right" into "provably sourced, or explicitly missing."

They were extracted from a production equity-research pipeline iterated across 66+ versions of live daily use. Everything here is generic and contains none of that system's scoring logic.

## The patterns

| # | Pattern | One-line summary |
|---|---|---|
| 01 | [Receipt-echo verification](patterns/01-receipt-echo-verification.md) | No value exists unless echoed verbatim from an opened source in the same turn |
| 02 | [Provenance tagging](patterns/02-provenance-tagging.md) | Every number carries exactly one origin tag |
| 03 | [Copy-forward baseline locks](patterns/03-copy-forward-baseline-locks.md) | State is copied verbatim and mutated only through declared keys |
| 04 | [Regression diff guards](patterns/04-regression-diff-guards.md) | The model prints its own diff and halts on out-of-scope change |
| 05 | [Schema drift detection](patterns/05-schema-drift-detection.md) | Key spelling, order, and count are locked; drift is a hard stop |
| 06 | [Atomic step execution](patterns/06-atomic-step-execution.md) | Interdependent stages run in one pass or not at all |
| 07 | [Self-check gates](patterns/07-self-check-gates.md) | Every step ends with a pass/fail line; fail forces a rerun |
| 08 | [Halt-over-guess protocols](patterns/08-halt-over-guess.md) | Stopping is the rewarded behavior: exact stop strings, named reasons |

## How they compose

01–02 govern how values **enter** the pipeline. 03–05 govern how state **persists**. 06 governs how work is **scheduled**. 07–08 are the **enforcement layer** every other pattern terminates in when it fires.

## Format

Every pattern file follows the same schema: Problem → Failure mode it prevents → The pattern → Copy-paste template → Worked example → Cost and when to use.

## Boundary

Public: the integrity techniques. Private: the production system's prompts, scoring weights, taxonomies, and thresholds.
