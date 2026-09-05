# VTR-DEV-001 — Code Management and Creation Methodology
## Vector Telemetry Research — Public Edition
### Version: 0.3.0 · Date: 2026-09-05 · License: CC BY 4.0

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

### 1. Explicit boundaries = verification points — CONFIRMED

Every transition between components (`kernel → /dev/vtr0 → userspace →
evidence`) must be a point where a hash can be placed before and after.

**Evidence:** Phase 2 of vtr-sentinel-kmod demonstrated via `hexdump -C`
that the fork→exec event crosses the ring buffer boundary with correct wire
format and intact CRC-32. Commit f6767c0.

**Applied to code:** every interface between modules must have an explicit
contract — type, size, endianness, CRC parameters — specified before
implementation begins. Not after.

### 2. Separation of privilege and semantics — CONFIRMED

The kernel observes. It does not interpret.

`kernel = mechanism` · `userspace = policy and meaning`

**Evidence:** vtr-sentinel-kmod architecture — no event kind contains
protocol classification logic. All 46 event kinds are observation primitives.
Phase 4 (DNP3/Modbus) is explicitly assigned to userspace.

**Documented epistemic limit:** that a userspace parser cannot reinterpret
an observation primitive as a semantic diagnosis without producing a
detectable artifact in the evidence chain is an architectural property —
NOT-COVERED until the corresponding experiment is executed.
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
| **CONFIRMED** | Property empirically demonstrated with reproducible evidence |
| **PROBABLE** | Designed and specified; not yet experimentally verified |
| **OBSERVED** | Behavior seen once; not systematically reproduced |
| **NOT-COVERED** | Outside current scope — document explicit reason |
| **REFUTED** | Hypothesis falsified with concrete evidence |

**Critical rule:** a specification is not evidence of implementation.
`_Static_assert` in C confirms layout at compile time — it does not confirm
that the Rust side interprets the same bytes.

**Warning on defensive use:** PROBABLE and NOT-COVERED used as habit produce
a system that can never be wrong — because it never asserts anything
falsifiable. Every PROBABLE must have a proposed experiment. See R-09.

---

## Development Rules

### R-01 · Contract before implementation

Every contract between modules must be fully specified before either side
begins implementation.

**State in vtr-sentinel-kmod:** PROBABLE. The C↔Rust contract is specified.
C-side has compile-time enforcement. Byte-level cross-language verification
has not been executed. Documenting the violation is part of the method.

**Minimum evidence to declare a contract CONFIRMED:**
- Written specification with exact types, sizes, and parameters
- Compile-time enforcement (`_Static_assert` on `offsetof` and `sizeof`)
- Byte-level cross-language test executed and documented
- SHA-256 of artifacts under test with UTC timestamp

### R-02 · Declared property ≠ verified property — CONFIRMED

Before declaring a property fulfilled, classify explicitly:

1. **FACT:** concrete evidence with traceable log
2. **INFERENCE:** valid reasoning, not yet experimentally verified
3. **PROJECTION:** probable direction without current basis

**Evidence:** commit b20d8a2 — wire contract kept at PROBABLE rather than
declared CONFIRMED without proof.

### R-03 · CRC and checksums — delimit the scope

CRC-32 detects accidental corruption. It does **not** provide cryptographic
integrity or authenticity against an active adversary.

Document at every use: what it covers, at which boundary, what it does not
guarantee.

### R-04 · Phases with defined completion criteria

A phase is complete when its criterion is satisfied with evidence — not when
functionality "seems to work". Criterion must be defined before the phase
begins.

```
Phase N — [name]
Criterion: [what must be true]
Required evidence: [artifact / log / test]
State: PENDING / IN-PROGRESS / CONFIRMED
```

### R-05 · Snapshot before any state change

Capture a ZFS snapshot and record its name with timestamp before modifying
any environment that contains verifiable evidence.

*Applied prospectively from version 0.3.0.*

### R-06 · SHA-256 of own artifacts

Every artifact that is loaded, deployed, or distributed must have its
SHA-256 recorded before being used as input to another phase.

*Applied prospectively from version 0.3.0.*

### R-07 · Extensible vocabulary, not a list to fill

Categories grow when evidence justifies it — not because they are missing.

### R-08 · Do not declare done without closing evidence — CONFIRMED

Every claim about system state must be answerable with exactly one of these:

- "I designed it" → PROBABLE
- "I implemented it" → PROBABLE (until tested)
- "I observed it" → OBSERVED
- "I validated it with reproducible evidence" → CONFIRMED
- "I can defend this claim to a third party with an artifact" → CONFIRMED

**Evidence:** Phase 2 — wire contract kept at PROBABLE rather than forced
to CONFIRMED without proof. Commit b20d8a2.

### R-09 · Every epistemic limit requires a proposed experiment

Documenting that something is PROBABLE or NOT-COVERED is correct. Stopping
there is a cover.

*R-09 added as a result of a second-reviewer finding on 2026-09-05.*

```json
{
  "hypothesis": "[claim to be elevated to CONFIRMED]",
  "current_state": "PROBABLE | NOT-COVERED",
  "falsifiers": ["[condition that refutes the hypothesis]"],
  "confirmation_criteria": "[evidence that elevates state to CONFIRMED]",
  "proposed_experiment": "[concrete experimental design]",
  "required_artifacts": ["[artifact 1]"]
}
```

**Artifacts in designs/proposed/:**
- `c_rust_contract_verification.json` — 2026-09-05 ✓
- `kernel_userspace_semantic_isolation.json` — 2026-09-05 ✓

R-09 fulfilled. No open violations.

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
### 4. Claims the reviewer considers CONFIRMED
```

### Reviewer eligibility criteria

- Not the author of the code under review
- Access only to the three defined artifacts
- No verbal briefing before the review
- Delivers report before discussing findings with the author

### Recommended cadence

- **Per phase:** before declaring any phase CONFIRMED
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
11. For each PROBABLE/NOT-COVERED that remains: create `designs/proposed/`

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
