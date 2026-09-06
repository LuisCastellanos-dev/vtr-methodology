# VTR-DEV-001 — Code Management and Creation Methodology
## Vector Telemetry Research — Public Edition
### Version: 0.4.0 · Date: 2026-09-06 · License: CC BY 4.0

---

> "Sentinel is not just built on Unix — it is built with Unix's properties
> as part of its observability model."
>
> — Design intent. State: PROBABLE. See Central Principle §3.

---

## Purpose

This document formalizes the epistemological principles VTR applies to
software development. It is not an audit process — it is the application
of non-presumption, verifiable evidence, and falsification principles,
applied to the code creation and management cycle.

The core question at every step:

> **Is this claim stronger than the evidence that supports it?**

---

## Central Principle

> **Design around identifiable boundaries, not despite them.**

Unix does not hide transitions between components — it exposes them. A
security system that needs to demonstrate how it reached a conclusion needs
exactly that: boundaries where evidence can be generated and verified
independently.

### 1. Explicit boundaries = verification points — REPRODUCED

Every transition between components (`kernel → /dev/vtr0 → userspace →
evidence`) must be a point where a hash can be placed before and after.

**Evidence:** Phase 2 of vtr-sentinel-kmod demonstrated via `hexdump -C`
that the fork→exec event crosses the ring buffer boundary with correct wire
format and intact CRC-32. Commit f6767c0.

**Applied to code:** every interface between modules must have an explicit
contract — type, size, endianness, CRC parameters — specified before
implementation begins. Not after.

### 2. Separation of privilege and semantics — REPRODUCED

The kernel observes. It does not interpret.

`kernel = mechanism` · `userspace = policy and meaning`

**Evidence:** vtr-sentinel-kmod architecture — no event kind contains
protocol classification logic. All 46 event kinds are observation primitives.
Phase 4 (DNP3/Modbus) is explicitly assigned to userspace.

**Documented epistemic limit:** that a userspace parser cannot reinterpret
an observation primitive as a semantic diagnosis without producing a
detectable artifact in the evidence chain is an architectural property —
NOT_EVALUATED until the corresponding experiment is executed.
Proposed experiment: `designs/proposed/kernel_userspace_semantic_isolation.json`

### 3. Natural provenance — PROBABLE

**State: PROBABLE** — Design property, not demonstrated. The complete chain
has not been executed end-to-end. `/dev/vtr0` belongs to Phase 3 (pending).

**Falsifier:** if, when executing the end-to-end chain in Phase 3, any
transition produces loss of provenance information not recoverable without
author context, the property is refuted for that transition.

**Target chain (design):**
```
observed event
    → kernel artifact (SHA-256 of .ko)
    → device boundary (/dev/vtr0)       ← Phase 3
    → userspace record
    → parser/version
    → classification
    → evidence with hash chain
```

---

## Evidence Vocabulary

| State | Meaning in development |
|-------|------------------------|
| **REPRODUCED** | Property empirically demonstrated with reproducible evidence and independent verification |
| **PROBABLE** | Designed and specified; not yet experimentally verified |
| **OBSERVED** | Behavior seen once; not systematically reproduced |
| **NOT_EVALUATED** | Outside current scope — document explicit reason |
| **REFUTED** | Hypothesis falsified with concrete evidence |

**Critical rule:** a specification is not evidence of implementation.
`_Static_assert` in C confirms layout at compile time — it does not confirm
that the Rust side interprets the same bytes.

**Warning on defensive use:** PROBABLE and NOT_EVALUATED used as habit produce
a system that can never be wrong — because it never asserts anything
falsifiable. Every PROBABLE must have a proposed experiment. See R-09.

---

## Development Rules

### R1 — Contract Before Code

Specification with types, sizes, endianness, and CRC parameters written
before implementation. Enforced with `_Static_assert` on `offsetof` and
`sizeof`. Evidence: spec file SHA + compile log.

**State in vtr-sentinel-kmod:** PROBABLE. The C↔Rust contract is specified.
C-side has compile-time enforcement. Byte-level cross-language verification
has not been executed. Documenting the violation is part of the method.

**Minimum evidence to declare a contract REPRODUCED:**
- Written specification with exact types, sizes, and parameters
- Compile-time enforcement (`_Static_assert` on `offsetof` and `sizeof`)
- Byte-level cross-language test executed and documented
- SHA-256 of artifacts under test with UTC timestamp

### R2 — Hash Before Use

Every artifact loaded, deployed, or distributed must have SHA-256 recorded
with UTC timestamp before being used as input to another phase.
Evidence: SHA-256 + timestamp in reviews/.

*Applied prospectively from version 0.3.0.*

### R3 — Claim = Log + Falsifier

Every non-trivial claim must define:
- claim text
- current epistemic state (from vocabulary above)
- evidence: commit hash + log + artifact SHA
- falsifier: condition that would refute the claim
- reproduction criterion: what constitutes independent verification
- proposed experiment, when state is not REPRODUCED

**Claim -> State mapping:**
- "I designed it" → PROBABLE
- "I designed and specified it" → PROBABLE
- "I implemented but have not tested it" → PROBABLE (until tested)
- "I observed it once" → OBSERVED_ONCE
- "I validated with reproducible evidence" → REPRODUCED
- "Independent auditor reproduced it" → REPRODUCED + INDEPENDENT_REPRODUCTION_FOUND

**Note:** "I can defend this claim to a third party with an artifact" is NOT
sufficient for REPRODUCED. Defense ability is not independent reproduction.

**Required JSON schema for every PROBABLE or NOT_EVALUATED claim:**

```json
{
  "hypothesis": "[claim to be elevated to REPRODUCED]",
  "current_state": "PROBABLE | NOT_EVALUATED",
  "falsifiers": ["[condition that refutes the hypothesis]"],
  "confirmation_criteria": "[evidence that elevates state to REPRODUCED]",
  "proposed_experiment": "[concrete experimental design]",
  "required_artifacts": ["[artifact 1]"]
}
```

**Artifacts in designs/proposed/:**
- `c_rust_contract_verification.json` — 2026-09-05 ✓
- `kernel_userspace_semantic_isolation.json` — 2026-09-05 ✓

---

## STATE and PROPERTY Vocabulary

These two vocabularies must appear as separate fields. Never conflate them.

**Epistemic states** (what we know about a claim):

| State | Meaning |
|-------|---------|
| **REPRODUCED** | Reproducible with artifact + SHA + log + independent verification |
| **PROBABLE** | Designed or has partial evidence; full reproduction not yet achieved |
| **OBSERVED_ONCE** | Observed empirically once; not yet replicated |
| **NOT_EVALUATED** | Not yet within audit scope |
| **FALSIFIED** | Hypothesis refuted with concrete evidence |

**Experimental properties** (conditions of the experiment):

| Property | Meaning |
|----------|---------|
| INDEPENDENT_REPRODUCTION_FOUND | Different auditor found same divergence class |
| PENDING_INDEPENDENT_REPRODUCTION | Independent reproduction not yet executed |
| INDEPENDENCE_THRESHOLD_UNESTABLISHED | Independence criterion not yet derived from evidence |
| SOURCE_ONLY_AUDIT | Audit conducted on source without citing own documentation |
| ZERO_PRESUMPTION | No assumption imported without evidence |

---

## Second Reviewer Protocol

### Why a second reviewer

The author of code is the worst candidate for detecting claims stronger than
their evidence — they know the original intent and can confuse it with the
actual implementation.

A second reviewer validates that claims about the code are consistent with
available evidence — not that the code works.

### What the second reviewer receives

Three artifacts and only three:

1. The README / current state documentation
2. The commit log
3. The completion criteria for each phase

If the second reviewer needs to ask the author to understand a claim, that
claim is not sufficiently documented.

### What the second reviewer produces

```
## VTR-DEV-001 Review — [project] — [date]
### Reviewer: [identifier]

### 1. Claims stronger than available evidence
### 2. Epistemic limits without proposed experiment
### 3. Inconsistencies between documentation and commits
### 4. Claims the reviewer considers REPRODUCED
```

### Reviewer eligibility criteria

- Not the author of the code under review
- Access only to the three defined artifacts
- No verbal briefing before the review
- Delivers report before discussing findings with the author

### Recommended cadence

- **Per phase:** before declaring any phase REPRODUCED
- **Per release:** before any public version tag
- **On request:** when the author detects defensive use of PROBABLE

### Review log

```
reviews/YYYY-MM-DD-[project]-[phase]-reviewer.md
```

With SHA-256 of the reviewed artifact and UTC timestamp.

**Reviews executed:**
- `2026-09-05-vtr-dev-001-v0.2.0-second-reviewer-llm.md` — produced
  findings that originated R-09, correction of Central Principle §3,
  expurgation process specification, traceability notes in R-05/R-06.
- `2026-09-05-vtr-dev-001-v0.3.0-draft-second-reviewer-llm.md` — verified
  correction of 3 blockers; identified CONFIRMED (prospective) as
  contradiction → corrected to PROBABLE; required
  kernel_userspace_semantic_isolation.json → created in this version.

---

## Development Cycle

### Before writing code

1. Define the boundary the module will cross
2. Specify the contract for that boundary
3. Define the phase completion criterion
4. Capture environment snapshot

### During development

5. Implement with `_Static_assert` / exact types / no implicit assumptions
6. Record SHA-256 of each generated artifact
7. Classify every claim as FACT / INFERENCE / PROJECTION

### Before declaring a phase complete

8. Execute the test the completion criterion requires
9. Document with traceable log (commit / hash / UTC timestamp)
10. Update to CONFIRMED only with evidence from step 9
11. For each PROBABLE/NOT_EVALUATED that remains: create `designs/proposed/`

### Before advancing to the next phase

12. Deliberate pause: are there claims stronger than available evidence?
13. Request second reviewer with the three defined artifacts
14. Incorporate reviewer findings before advancing
15. Correct before adding functionality

---

## Reference Case: vtr-sentinel-kmod

| Principle | State | Evidence |
|-----------|-------|----------|
| C↔Rust contract | PROBABLE | Specified; cross-language test pending |
| Compile-time `_Static_assert` | CONFIRMED | Commit a11b79b |
| Artifact SHA-256 | PROBABLE | R-06 prospective from 0.3.0 |
| ZFS snapshot | PROBABLE | R-05 prospective from 0.3.0 |
| Phase 2 criterion | CONFIRMED | `hexdump -C` fork→exec — commit f6767c0 |
| Extensible vocabulary | CONFIRMED | 46 event kinds, Phase 4 not advanced |
| Wire contract at PROBABLE | CONFIRMED | Commit b20d8a2 |
| R-09 fulfilled | CONFIRMED | Both JSON in designs/proposed/ — 2026-09-05 |

**Confirmed methodological finding:** correcting the Phase 2 README produced
two real findings in the code (CRC-32 lookup table with 10 transcription
errors; `EVFILT_READ` not supported for cdevs). The methodology worked on
the project itself.

---

## Verification Dimensions

| Dimension | Question |
|-----------|----------|
| Artifact | What exactly is being claimed about this binary / module / library? |
| Boundary | What is the contract at this interface, and is it verified? |
| State | What is the lifecycle state of this module and its data? |
| Temporal | What is the sequence of phases and their evidence? |
| Composition | How do components interact, and is that interaction verified? |
| Provenance | SHA-256 + snapshot + commit + UTC timestamp — all present? |
| Runtime | Has dynamic behavior been observed and documented? |
| Falsification | Are there claims stronger than the available evidence? |

---

## Changelog

| Version | Date | Change |
|---------|------|--------|
| 0.1.0 | 2026-09-03 | Initial version — R-01 to R-08 |
| 0.2.0 | 2026-09-05 | R-09; Second Reviewer Protocol; PROBABLE warning |
| 0.3.0 | 2026-09-05 | §3 PROBABLE with falsifier; R-09 fulfilled with both JSON artifacts; PROBABLE (prospective) corrected in reference table; second reviewer v0.3.0-draft execution logged |

---

*Vector Telemetry Research — Tampico, Tamaulipas, México*
*SIGNAL. VECTOR. INTELLIGENCE.*
*© 2026 — CC BY 4.0*
*Derived from VTR-DEV-001-internal.md via controlled expurgation.*
