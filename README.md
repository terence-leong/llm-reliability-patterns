# LLM Reliability Patterns

Generic, IP-safe engineering patterns for forcing honesty in LLM data pipelines:

- Receipt-echo verification — no value used unless echoed verbatim from an opened source
- Provenance tagging — every number carries DIRECT / COMPUTED / OVERRIDE / MISSING
- Copy-forward baseline locks — state carried verbatim between steps, no silent edits
- Regression diff guards — any change outside declared updated keys halts the run
- Schema drift detection — locked key order and spelling, hard stop on drift
- Atomic step execution — interdependent stages run as one indivisible pass
- Self-check gates — end-of-step compliance checks that force reruns
- Halt-over-guess protocols — stopping is always preferred to fabricating

Each pattern will be documented with a generic worked example, the failure mode it prevents, and a copy-paste template.
