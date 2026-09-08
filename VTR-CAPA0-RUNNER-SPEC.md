# vtr-capa0-runner — Minimal Artifact Specification

**Status:** PROPOSAL — Session Cutoff
**Layer:** External Evidence Layer (Doc 34)
**Purpose:** Enforce pre-registration, blind observation, deterministic comparison, and tamper-evident logging.

---

## 1. Problem Statement

VTR currently has:

* Taxonomy A/B/C (Doc 12 §10, Doc 29)
* H1 CANDIDATE: 0/2 honest vs 3/3 implicit may predict
* Layer separation defined in Doc 34: FIXED != PROBABLE
* R3: Claim = Log

The methodological risk is retrospective reinterpretation.

After the observed result is known, a subsequent auditor must not be able to modify, reinterpret, or silently replace the original prediction in order to make the prediction appear correct.

The ring case demonstrated the class of failure this artifact is intended to prevent: an initially recorded implicit interpretation could be retrospectively changed to honest after observing the result.

The runner therefore protects the boundary between:

PREDICTION -> OBSERVATION -> COMPARISON

It does not determine whether the hypothesis is true.

---

## 2. Non-Goals

vtr-capa0-runner is NOT:

* an ecosystem bundling vtr-methodology, vtr-sentinel, cfg-shield, or other VTR projects;
* a predictor or predictor validator;
* a Layer 1 finding/fix runner;
* a hypothesis evaluator;
* a mechanism for changing H1/H2 status;
* a mechanism for closing RQ4;
* an authority capable of declaring scientific falsification.

The artifact implements Layer 3 only:

  preregistration + blind observation + deterministic comparison + tamper-evident recording.

---

## 3. Artifact Definition

**Name:** vtr-capa0-runner
**Type:** CLI, single binary
**Target size:** <500 LOC for core enforcement logic
**Languages:** Go or Rust
**Platforms:** FreeBSD and Linux

### 3.1 Core Invariants

The runner MUST:

1. Never modify the original preregistration.
2. Never modify a hypothesis document.
3. Never modify a finding.
4. Never mark a finding FIXED or VERIFIED.
5. Never change a prediction after observation.
6. Never expose the prediction to the auditor during the blind-observation phase.
7. Never derive scientific conclusions from a single comparison.
8. Preserve both matching and non-matching results with equal fidelity.
9. Detect subsequent tampering with preregistration and observation records.
10. Record the exact evidence basis used for the observation.

---

## 4. Input Contract

### 4.1 Pre-registration

prereg.json MUST be created and finalized before the audit repository is obtained or the audit commit is selected.

Example:

{
  "artifact": "mbedTLS",
  "version": "3.6.2",
  "predicted_naming": "implicit",
  "predicted_class": "B",
  "confidence": "MEDIUM",
  "rationale": "Free-text rationale recorded before observation.",
  "evidence_cutoff": "commit-or-equivalent-reference",
  "timestamp": "2026-09-07T..."
}

The following fields are immutable after registration:
  artifact, version, predicted_naming, predicted_class,
  confidence, rationale, evidence_cutoff, timestamp

The runner calculates SHA-256(prereg.json) -> prereg.sha256
The original file is never rewritten by the runner.

---

### 4.2 Evidence Cutoff

The runner MUST use a deterministic evidence reference.
Preferred form: repository + commit hash

The runner MUST NOT treat filesystem clone time as proof of repository state.
Operational timestamps MAY be recorded for provenance, but MUST NOT substitute for an immutable evidence cutoff.

The audit MUST record: repository, commit, commit timestamp, resolution timestamp.

---

### 4.3 Repository Acquisition

The repository MUST be obtained after preregistration.
The audit is valid only if the selected evidence cutoff is compatible with the preregistration and has not been changed after observation.

---

## 5. Blind Observation

The prediction MUST NOT be displayed to the auditor during observation.
The runner presents only the observation protocol.

The auditor records observable facts:

{
  "observed_naming": "honest",
  "observed_evidence": [
    { "path": "src/example.c", "line_start": 123, "line_end": 128 }
  ],
  "dispatch_observation": "same-contract",
  "configuration_observation": "no-public-contract-change",
  "timestamp": "2026-09-07T..."
}

The auditor MUST NOT select a prediction-compatible interpretation merely because a result is expected.

---

## 6. CAPA 0 / 0a / 0b Observation Protocol

### 6.1 CAPA 0 -- Naming Discipline

Determine from repository evidence whether the public API naming is: honest or implicit.
The determination MUST be based on predefined observation rules.

### 6.2 CAPA 0a -- Runtime Contract Selection

Determine whether runtime dispatch selects different contracts or behavioral interfaces.
The observation is recorded independently of the preregistered prediction.

### 6.3 CAPA 0b -- Configuration Contract

Determine whether compilation/configuration flags alter the public contract without corresponding declaration.
The observation is recorded independently of the preregistered prediction.

---

## 7. Deterministic Classification

The runner MUST distinguish observation from classification.
The auditor records evidence and observations.
The runner then applies the previously frozen classification rules to those observations.
The auditor MUST NOT be allowed to redefine A/B/C criteria during comparison.

  raw observation -> frozen classification rules -> observed_class

Example:
{
  "observed_naming": "honest",
  "observed_class": "A",
  "classification_rule_set": "CAPA0-vX.Y"
}

If evidence is insufficient, the runner MUST record an explicit indeterminate state rather than forcing A/B/C.

---

## 8. Prediction Comparison

Comparison is performed only after the blind observation has been frozen.

The runner compares:
  REGISTERED PREDICTION vs OBSERVED RESULT

The prediction is never rewritten.
The comparison MUST expose two independent dimensions:

  naming_comparison: MATCH / MISMATCH
  class_comparison:  MATCH / MISMATCH

Combined result:
  HIT / MISS_NAMING / MISS_CLASS / MISS_BOTH

Example:
{
  "naming_comparison": "MISMATCH",
  "class_comparison": "MISMATCH",
  "result_type": "MISS_BOTH"
}

A MISS is a recorded experimental outcome, not an error to be corrected.

---

## 9. Hypothesis-Relevant Flags

The runner MAY calculate mechanical comparison flags, but MUST NOT interpret them as scientific conclusions.

The runner MUST NOT label any result:
  "strong falsifier" / "weak falsifier" / "hypothesis proven" / "hypothesis disproven"

unless such terminology has already been formally defined as a deterministic output category in the governing methodology.

The runner records evidence. The hypothesis layer interprets evidence separately.

---

## 10. Evidence Integrity

Every evidence reference MUST contain enough information to identify the audited state.

Minimum recommended representation:
{
  "repository": "mbedTLS",
  "commit": "<immutable commit>",
  "path": "src/example.c",
  "line_start": 123,
  "line_end": 128
}

Where practical, the runner SHOULD additionally record a hash of the referenced evidence fragment.

---

## 11. Audit Record

The runner produces:

audit-log/
  prereg.json
  prereg.sha256
  observation.json
  classification.json
  comparison.json
  provenance.json
  evidence/

No file in this directory may alter the original prediction.

---

## 12. Tamper-Evident Log

The runner maintains: vtr-capa0-log.jsonl

Each record contains:
{
  "prereg_sha256": "...",
  "evidence_commit": "...",
  "observation_sha256": "...",
  "comparison": {
    "naming": "MISMATCH",
    "class": "MISMATCH",
    "result_type": "MISS_BOTH"
  },
  "timestamp": "..."
}

Records are chained: H(n) = SHA-256(H(n-1) || canonical_record(n))

The log is tamper-evident, not intrinsically immutable.
Any modification that breaks the recorded chain MUST cause verification to fail.

---

## 13. State Machine

PRE-REGISTRATION
      |
      v
prereg.json + SHA-256
      |
      v
IMMUTABLE EVIDENCE CUTOFF
      |
      v
BLIND CAPA 0 / 0a / 0b
      |
      v
observation.json
      |
      v
FROZEN CLASSIFICATION
      |
      v
comparison.json
      |
      v
TAMPER-EVIDENT LOG
      |
      v
EXTERNAL EVIDENCE

At no point does the state machine transition into:
  H1 update / H2 update / FIXED / VERIFIED / RQ4 closure

Those transitions belong to other layers.

---

## 14. Layer Enforcement

### Layer 1 -- Findings

The runner MUST NOT: create/close a finding, mark FIXED or VERIFIED.

### Layer 2 -- Hypotheses

The runner MUST NOT: change H1/H2 status, declare falsification or confirmation, close RQ4.

### Layer 3 -- External Evidence

The runner MAY: freeze preregistration, execute blind CAPA 0/0a/0b, preserve evidence references,
classify according to frozen rules, compare prediction against observation,
append the comparison to the tamper-evident log.

---

## 15. Failure Conditions

The runner MUST fail closed when:
  prereg.json hash mismatch
  evidence cutoff differs from registered cutoff
  observation record modified after freeze
  classification rules unavailable or altered
  comparison record does not correspond to frozen prediction
  log chain verification fails

A failure MUST NOT trigger automatic correction.
It produces: AUDIT INVALID with the reason recorded.

---

## 16. Success Criteria

The artifact is successful if a second auditor can execute a CAPA 0 audit
without access to H1/H2 interpretation documents during the observation phase.

The ring case MUST remain a MISS when the registered prediction disagrees with the observed result.

A later reviewer MUST be able to answer:
  What was predicted?
  What was observed?
  What evidence supported the observation?
  What was the immutable evidence cutoff?
  Did prediction and observation match?
  Was any recorded artifact subsequently altered?

without reconstructing the answer from memory or narrative interpretation.

---

## 17. Implementation Constraints

Language:         Go or Rust
Distribution:     single static binary
Core logic:       <500 LOC target
Classification:   no external dependencies required
Storage:          local JSON + JSONL
Crypto integrity: SHA-256

The implementation MUST remain small enough that the enforcement logic
can itself be independently inspected.

---

## 18. What This Artifact Does Not Do

vtr-capa0-runner does not:
  predict / fix / prove / disprove / close RQ4 / reinterpret H1/H2
  modify findings / modify vtr-methodology
  bundle vtr-sentinel / vtr-sentinel-kmod / cfg-shield
  become a general VTR orchestration framework

Its single purpose:
  Prevent the observed result from retroactively changing the registered prediction.

---

## 19. Session Cutoff

This artifact implements the Doc 34 separation:

  Finding Layer       -> independent finding verification
  Hypothesis Layer    -> independent hypothesis evaluation
  External Evidence   -> vtr-capa0-runner

Therefore:
  VS-010 FIXED+VERIFIED    remains unchanged
  H1                       remains CANDIDATE
  H2                       remains CANDIDATE
  CAPA 0                   next observations may be registered through runner
  R3 Claim = Log           enforced through preregistration, frozen observation,
                           deterministic comparison, and tamper-evident recording

The runner does not make the prediction correct.
It makes the record of whether the prediction was correct non-reinterpretable after observation.
