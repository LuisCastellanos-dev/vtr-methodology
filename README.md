# vtr-methodology

Methodology documents from Vector Telemetry Research (VTR) — open versions of VTR development and verification frameworks.

## Documents

| Document | Version | Status | Description |
|----------|---------|--------|-------------|
| [VTR-DEV-001](docs/VTR-DEV-001.md) | 0.4.0 | Active | Development methodology — 3 operational rules, STATE/PROPERTY vocabulary, falsification schema |
| [VTR-COMP-001](docs/VTR-COMP-001.md) | 0.1.0 | Active | Compilation context as a security boundary |

## Proposed Experiments

| Experiment | State | Property |
|------------|-------|----------|
| [c_rust_contract_verification](designs/proposed/c_rust_contract_verification.json) | REPRODUCED | INDEPENDENT_REPRODUCTION_FOUND |
| [kernel_userspace_semantic_isolation](designs/proposed/kernel_userspace_semantic_isolation.json) | REPRODUCED | INDEPENDENT_REPRODUCTION_FOUND |

## Second Auditor Protocol

Independent audit instructions for RQ4 Delta_A satisfaction:
[VTR-RQ4-SECOND-AUDITOR-PROTOCOL.md](VTR-RQ4-SECOND-AUDITOR-PROTOCOL.md)

Audit reports are in [reviews/](reviews/).

## Simplification Procedure

Terminology freeze and public simplification instructions:
[VTR-DEV-001-SIMPLIFICATION-PROCEDURE-v1.0.1.md](VTR-DEV-001-SIMPLIFICATION-PROCEDURE-v1.0.1.md)

## Epistemic Vocabulary (v0.4.0)

STATE and PROPERTY are separate fields. Never conflated.

**Epistemic states:**

| State | Meaning |
|-------|---------|
| **REPRODUCED** | Demonstrated empirically with reproducible evidence and independent verification |
| **PROBABLE** | Designed or has partial evidence; full reproduction not yet achieved |
| **OBSERVED_ONCE** | Observed empirically once; not yet replicated |
| **NOT_EVALUATED** | Not yet within audit scope |
| **FALSIFIED** | Hypothesis refuted with concrete evidence |

**Experimental properties:**

| Property | Meaning |
|----------|---------|
| INDEPENDENT_REPRODUCTION_FOUND | Different auditor found same divergence class |
| PENDING_INDEPENDENT_REPRODUCTION | Independent reproduction not yet executed |
| INDEPENDENCE_THRESHOLD_UNESTABLISHED | Independence criterion not yet derived from evidence |
| SOURCE_ONLY_AUDIT | Audit conducted on source without citing own documentation |
| ZERO_PRESUMPTION | No assumption imported without evidence |

## Operational Rules (VTR-DEV-001 v0.4.0)

**R1 — Contract Before Code**
Specification with types, sizes, endianness, and CRC parameters written before implementation. Enforced with `_Static_assert`. Evidence: spec SHA + compile log.

**R2 — Hash Before Use**
Every artifact loaded, deployed, or distributed must have SHA-256 recorded with UTC timestamp before use. Evidence: SHA-256 + timestamp in reviews/.

**R3 — Claim = Log + Falsifier**
Every non-trivial claim must define: epistemic state, evidence (commit + SHA), falsifier, and proposed experiment when state is not REPRODUCED.

Note: "I can defend this claim to a third party" is NOT sufficient for REPRODUCED. Defense ability is not independent reproduction.

## RQ4 Status

Build divergence reproduced by independent auditor in 1/3 artifacts (cfg-shield). 2 pending: kmod requires FreeBSD with kernel headers, continuity requires SOURCE_ONLY_AUDIT. Independence threshold not yet established.

| Artifact | State | Property |
|----------|-------|----------|
| cfg-shield | PROBABLE | INDEPENDENT_REPRODUCTION_FOUND (VS-009) |
| vtr-sentinel-kmod | PROBABLE | PENDING_INDEPENDENT_REPRODUCTION |
| vtr-continuity | PROBABLE | PENDING_INDEPENDENT_REPRODUCTION |
| RQ4 global | PROBABLE | NOT_YET_REPRODUCED |
| tau | — | INDEPENDENCE_THRESHOLD_UNESTABLISHED |

## License

Documentation: [CC BY 4.0](LICENSE)
Any code samples: BSD 2-Clause

## About

Vector Telemetry Research (VTR) — OT/ICS Security · Tampico, Tamaulipas, México
SIGNAL. VECTOR. INTELLIGENCE.
