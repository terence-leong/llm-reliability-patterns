# 02 — Provenance Tagging

## Problem

In a long context, origins blur. A value introduced as a rough guess in step 2 reads as an established fact by step 9 — to the model and to the human. Repetition launders it.

## Failure mode this prevents

Audit impossibility. When something breaks, nobody can tell which numbers were verified, which were derived, and which were assumptions wearing a suit.

## The pattern

Every numeric value printed carries exactly one origin tag:

- `DIRECT_FROM_SOURCE` — echoed verbatim from an opened source (see Pattern 01)
- `COMPUTED_FROM_SOURCE: <formula>` — derived; formula and inputs shown
- `USER_PROVIDED_OVERRIDE` — supplied by the human (pastes, screenshots)
- `MISSING — NOT VERIFIED`

An untagged number is a violation. Tags travel with values when state is copied between steps, so a full audit is a text search, not an archaeology project.

## Copy-paste template

```
PROVENANCE RULE (mandatory on every number):
Every numeric value printed must carry exactly one tag:
  [DIRECT_FROM_SOURCE]
  [COMPUTED_FROM_SOURCE: <formula>]
  [USER_PROVIDED_OVERRIDE]
  [MISSING — NOT VERIFIED]
An untagged number is a violation: stop and re-emit with tags.
A computed value whose inputs are not DIRECT or OVERRIDE must itself be MISSING.
```

## Worked example (demo values, deliberately fake)

```
REVENUE_TTM=4,180        [DIRECT_FROM_SOURCE]
SHARES_OUT=910           [USER_PROVIDED_OVERRIDE]
REV_PER_SHARE=4.59       [COMPUTED_FROM_SOURCE: 4,180 / 910]
NET_DEBT=MISSING         [MISSING — NOT VERIFIED]
EV_MULTIPLE=MISSING      [MISSING — NOT VERIFIED]  <- blocked: depends on NET_DEBT
```

The last line is the pattern working: the derived ratio inherits `MISSING` instead of being quietly estimated.

## Cost and when to use

Visual noise in exchange for machine-parseable honesty. Worth it in any multi-step numeric workflow; overkill for prose.
