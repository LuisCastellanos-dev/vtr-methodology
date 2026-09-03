# VTR-DEV-001 — Software Development Methodology
## Vector Telemetry Research
### Version: 0.1.0-draft · Date: 2026-09-03

---

> "Design around identifiable boundaries, not in spite of them."

---

## Purpose

This document formalizes the epistemological principles VTR applies to the
development of its own software. It is not an audit process — it is the
application of the same principles of non-presumption, verifiable evidence,
and falsifiability to the cycle of creating and managing code.

The core question this methodology answers:

> How do you distinguish between what you designed, what you implemented,
> what you observed, and what you can defend to a third party?

---

## Central Principle

Unix does not hide transitions between components — it exposes them. A security
system that needs to demonstrate how it reached a conclusion needs exactly that:
boundaries where evidence can be generated and verified independently.

Three practical consequences:

### 1. Explicit boundaries = verification points

Every transition between components must be a point where a hash can be placed
before and after. In a monolithic system, the question "is the event the kernel
produced bit-for-bit identical to what the userspace received?" has no answer.
In a system with explicit boundaries, that question is the basis of the design.

**Application:** every interface between modules must have an explicit contract
— type, size, endianness, CRC parameters — specified before implementation
begins. Not after.

### 2. Separation of privilege and semantics

The kernel observes. It does not interpret.

`kernel = mechanism` · `userspace = policy and meaning`

A protocol parser in the kernel expands the Trusted Computing Base and makes
evidence indemonstrable. The kernel produces a reliable representation of what
it observed; userspace decides what it means.

**Application:** no kernel module should contain protocol classification logic.
Event kinds are observation primitives, not semantic diagnostics.

### 3. Natural provenance

Every transition can carry: who, when, which version, which privilege level.
The evidence chain is not designed on top of the system — it is designed with
the properties of the system.

**Evidence chain objective:**

    observed event
        -> kernel artifact (SHA-256 of .ko)
        -> device boundary (/dev/vtr0)
        -> userspace record
        -> parser/version
        -> classification
        -> evidence with hash chain

---

## Evidence Vocabulary

| State | Meaning in development |
|-------|----------------------|
| **CONFIRMED** | Property demonstrated empirically with reproducible evidence |
| **PROBABLE** | Designed and specified; not yet experimentally verified |
| **OBSERVED** | Behavior seen once; not systematically reproduced |
| **NOT-COVERED** | Outside current scope — explicit reason documented |
| **REFUTED** | Hypothesis falsified with concrete evidence |

**Critical rule:** a specification is not evidence of implementation.
`_Static_assert` in C confirms layout at compile-time — it does not confirm
that the Rust side interprets the same bytes. These are two distinct claims
that require two distinct verifications.

## Development Rules

### R-01 · Contract before implementation

Every contract between modules (wire format, API boundary, shared header) must
be fully specified before either side begins implementation. A half-specified
contract produces two implementations each assuming the other does what was
not documented.

**Minimum evidence to declare a contract CONFIRMED:**
- Written specification with exact types, sizes, and parameters
- Compile-time enforcement on the C side (_Static_assert on offsetof and sizeof)
- Byte-level cross-language test executed and documented
- SHA-256 of artifacts under test recorded with UTC timestamp

### R-02 · Declared property != verified property

Before declaring a property as satisfied, classify explicitly:

1. **FACT:** concrete evidence with traceable log (commit / test / hash / date)
2. **INFERENCE:** valid reasoning, not experimentally verified
3. **PROJECTION:** probable direction without current basis

Never use evocative language ("robust", "production-ready", "validated")
without an immediate specific citation to back it up.

### R-03 · CRC and checksums — delimit the scope

CRC-32 detects accidental corruption in transmission or storage. It does NOT
provide cryptographic integrity or authenticity against an adversary who can
modify and recalculate the checksum.

Document explicitly in every use:
- Which bytes the checksum covers
- At which boundary it is calculated and verified
- What it does not guarantee

### R-04 · Phases with defined completion criteria

A phase is complete when its completion criterion is satisfied with evidence,
not when functionality "seems to work". The criterion must be defined before
the phase begins.

**Minimum criterion format:**

    Phase N - [name]
    Criterion: [what must be true]
    Required evidence: [which artifact / log / test demonstrates it]
    State: PENDING / IN-PROGRESS / CONFIRMED

### R-05 · Snapshot before any state change

Before modifying a development or test environment that already contains
verifiable evidence, capture a ZFS snapshot and record its name with
timestamp. The reversibility of the environment is part of the
reproducibility of the result.

### R-06 · SHA-256 of own artifacts

Every artifact that is loaded, deployed, or distributed must have its SHA-256
recorded before being used as input to another phase. The hash is not
decoration — it is the identity of the artifact in the evidence chain.

### R-07 · Extensible vocabulary, not a list to fill

Event kinds, classifications, and finding categories are a vocabulary that
grows when evidence justifies it. They are not a list that must be completed.
Adding a category because it "is missing" is presumption; adding a category
because an observed behavior does not fit existing ones is methodological
development.

### R-08 · Do not declare done without closing evidence

Each correction distinguishes between: I designed it / I implemented it /
I observed it / I validated it / I can defend this claim to a third party.

Examples of corrections VTR applied before declaring Phase 2 complete:
- /dev/vtr0 must not appear in Phase 2 if it belongs to Phase 3
- The wire contract must not be presented as validated before cross-language tests
- CRC-32 must not be described in a way that implies cryptographic integrity
- "Merkle chain" must not be used if the implementation is a SHA-256 hash chain

## Development Cycle

### Before writing code

1. Define the boundary the module will cross
2. Specify the contract of that boundary (types, sizes, parameters)
3. Define the completion criterion for the phase
4. Capture a snapshot of the environment

### During development

5. Implement with _Static_assert / exact types / no implicit assumptions
6. Record SHA-256 of each generated artifact
7. Classify each claim about state as FACT / INFERENCE / PROJECTION

### Before declaring a phase complete

8. Execute the test the completion criterion requires
9. Document the result with a traceable log (commit / hash / UTC timestamp)
10. Update state from PROBABLE to CONFIRMED only with evidence from step 9

### Before advancing to the next phase

11. Deliberate pause: are there claims in the documentation stronger than
    the available evidence?
12. Correct before adding functionality

---

## Reference Case: vtr-sentinel-kmod

VTR Sentinel is the laboratory case that demonstrates these principles
applied to a system built by VTR itself.

| Principle | Evidence in Sentinel |
|-----------|---------------------|
| Contract before implementation | vtr_event.h specified with offsetof + sizeof + CRC-32 params before implementing the Rust side |
| Compile-time enforcement | _Static_assert on C side — any layout change breaks the build |
| SHA-256 of artifact | sha256 vtr_sentinel.ko documented before kldload |
| ZFS snapshot | zroot-vtr/jails@pre-VAL-GHIDRA-20260828 before each session |
| Phases with defined criteria | Phase 2 CONFIRMED only after hexdump -C of fork->exec sequence |
| Extensible vocabulary | 46 event kinds as vocabulary, not a list to complete |
| No declaring done without evidence | Wire contract downgraded to PROBABLE until cross-language tests |

**Methodological finding:** the process of correcting the Phase 2 README
produced two real findings in the code:

1. CRC-32 lookup table with 10 transcription errors — table regenerated
   programmatically, cross-language contract upgraded from PROBABLE to
   CONFIRMED after 5 byte-level tests passed
2. EVFILT_READ not supported for character devices on FreeBSD 14.4 —
   confirmed via truss, replaced with select(2)

The methodology worked on the project itself — the artifact VTR builds is
subject to the same rules as the artifacts VTR audits.

---

## Verification Dimensions

These dimensions describe the aspects of a software artifact that require
independent verification. They apply equally to external artifacts under
review and to artifacts produced internally.

| Dimension | What to verify |
|-----------|---------------|
| Artifact | The module / binary / library produced |
| Boundary | The interface between own components |
| State | The lifecycle of the module and its data |
| Temporal | The sequence of phases and their criteria |
| Composition | The relationship between kernel producer and userspace consumer |
| Provenance | SHA-256 of .ko + snapshot + commit + UTC timestamp |
| Runtime | Dynamic observation of hooks and events |
| Falsifiability | Are there claims stronger than the available evidence? |

---

## Version History

| Version | Date | Change |
|---------|------|--------|
| 0.1.0-draft | 2026-09-03 | Initial version — foundational principles and rules R-01 to R-08 |

---

*Vector Telemetry Research — Tampico, Tamaulipas, Mexico*
*SIGNAL. VECTOR. INTELLIGENCE.*

All content released under CC BY 4.0.
DOI: pending — to be registered on Zenodo upon stable release.
