# VTR — Second Auditor Protocol for RQ4
Date: 2026-09-05
Version: 1.0.0
Status: OPERATIVE — ready for external auditor
Classification: PUBLIC

---

## Purpose

This document provides complete, self-contained instructions for a
second auditor to independently replicate the audit context that
produced VS-005 through VS-009. The auditor must not receive any
verbal or written briefing from the author beyond this document.

The goal is to satisfy Delta_A != 0 for RQ4:

  RQ4: Does the audit context, as a variable distinct from the
  compilation context and the development process, systematically
  produce divergences between declared and implemented contracts
  in self-produced artifacts?

Current state: PROBABLE (one auditor, three artifacts).
To advance to CONFIRMED: a second auditor must independently
apply the same method to at least one artifact and either
confirm or refute the pattern.

---

## What the second auditor receives

Three artifacts and only three:

1. This document
2. The public repository list (below)
3. The VTR-METH-001 audit question (below)

The auditor does NOT receive:
- Knowledge of prior findings (VS-005 through VS-009)
- Access to vtr-research-future repository
- Any verbal explanation of what to look for
- The internal VTR-DEV-001 document

---

## The audit question (VTR-METH-001 CAPA 0)

For every contract between declared behavior and implemented behavior,
ask exactly this question:

  Does what you say you measure match what you actually measure?

More specifically, for each component:
  1. Find the declared contract — documentation, comments, type signatures
  2. Find the implemented behavior — source code, tests, build output
  3. Determine whether they match
  4. Classify the result

---

## Classification framework

| Classification | Criteria |
|----------------|----------|
| CONFIRMED      | Observable divergence between declared and implemented contract |
| PROBABLE       | Potential divergence, not yet empirically demonstrated |
| NO-FINDING     | Contract and implementation are consistent |
| NEEDS-EVIDENCE | Claim present but no verifiable evidence found |

---

## Artifacts to audit

### Artifact 1 — vtr-sentinel-kmod (C / FreeBSD kernel module)

Repository: https://github.com/LuisCastellanos-dev/vtr-sentinel-kmod
Branch: master
Language: C
Build target: FreeBSD 14.4-RELEASE-p8 / amd64

Audit scope:
  - vtr_event.h — wire format contract
  - vtr_ring.c  — CRC-32 implementation
  - vtr_hooks.c — event hook implementation
  - Makefile    — build context

Key contracts to verify:
  a. Every field declared in vtr_event.h comments matches the
     struct definition and _Static_assert enforcement.
  b. The CRC-32 parameters documented in vtr_event.h match
     the implementation in vtr_ring.c.
  c. Every event kind declared in vtr_event.h is implemented
     consistently in vtr_hooks.c.

Audit method:
  1. Clone the repository on any system with a C compiler.
  2. Read vtr_event.h — note every declared contract.
  3. Cross-reference each declaration against the implementation.
  4. If on FreeBSD: run make — verify build succeeds without errors.
  5. Document any divergence between declaration and implementation.

---

### Artifact 2 — vtr-continuity (Python)

Repository: https://github.com/LuisCastellanos-dev/vtr-continuity
Branch: main
Language: Python 3

Audit scope:
  - core/crypto_transport.py — ReplayWindow, NonceCounter
  - core/dtn_fragmenter.py   — FragmentStore
  - core/liveness.py         — LivenessTracker
  - docs/VTR-SURVEY-001.md   — channel parameters

Key contracts to verify:
  a. ReplayWindow: are COUNTER_WINDOW_BACK and COUNTER_WINDOW_FORWARD
     documented and justified in any specification document?
  b. FragmentStore: is DEFAULT_BUNDLE_TIMEOUT justified against
     the channel parameters in VTR-SURVEY-001.md?
  c. LivenessTracker: does the declared unit of
     heartbeat_timeout_seconds match the implementation?

Audit method:
  1. Clone the repository.
  2. Read docs/VTR-SURVEY-001.md — note channel parameters.
  3. Cross-reference channel parameters against default values
     in crypto_transport.py and dtn_fragmenter.py.
  4. Run: python3 -m pytest core/tests/ -q
  5. Document any divergence.

---

### Artifact 3 — cfg-shield (Rust)

Repository: https://github.com/LuisCastellanos-dev/cfg-shield
Branch: main
Language: Rust

Audit scope:
  - src/main.rs    — detector implementation
  - METHODOLOGY.md — declared behavior and limitations

Key contracts to verify:
  a. The detector claims CONFIRMADO when default build has 0 tests
     and feature build has N > 0 tests. Does the implementation
     correctly distinguish this from a compilation failure?
  b. Are all limitations in METHODOLOGY.md implemented as described?

Audit method:
  1. Clone the repository.
  2. Run: cargo build
  3. Create a test crate that does NOT compile without a feature.
  4. Run cfg-shield on that crate.
  5. Document the result.

---

## What the second auditor produces

A report with this structure:

  ## VTR RQ4 Second Auditor Report — [date]
  ### Auditor: [identifier]

  ### Artifact 1 — vtr-sentinel-kmod
  [findings or NO-FINDING for each contract]

  ### Artifact 2 — vtr-continuity
  [findings or NO-FINDING for each contract]

  ### Artifact 3 — cfg-shield
  [findings or NO-FINDING for each contract]

  ### Summary
  [total findings, classifications, observations]

The report must be produced BEFORE discussing findings with the author.

---

## Delivery

Send the report to: ing.castellanosdz@gmail.com
Subject: VTR RQ4 Second Auditor Report — [date]

Or submit as a pull request to:
https://github.com/LuisCastellanos-dev/vtr-methodology
Path: reviews/YYYY-MM-DD-rq4-second-auditor-[identifier].md

---

## What this audit contributes

If the second auditor finds divergences consistent with the RQ4 pattern:
  Delta_A != 0 is satisfied for at least one artifact.
  RQ4 can advance from PROBABLE toward CONFIRMED.

If the second auditor finds NO divergences:
  That is also a valid result — it refutes or weakens the pattern.
  RQ4 remains PROBABLE with narrower scope.

Both outcomes are methodologically valid and will be documented.

---

## Technical context (read AFTER completing the audit)

This section is intentionally placed last to avoid biasing the audit.
After submitting your report, you may read:
  https://github.com/LuisCastellanos-dev/vtr-research-future

This repository contains the findings from the first auditor session.
Comparing your independent findings against the existing record is
the validation step for Delta_A.

---

Vector Telemetry Research — Tampico, Tamaulipas, Mexico
VTR-DEV-001 v0.3.3 — RQ4 Second Auditor Protocol
2026-09-05
