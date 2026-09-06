# VTR-DEV-001 PUBLIC SIMPLIFICATION AND TERMINOLOGY FREEZE
Version: 1.0.1 — 2026-09-06
Replaces: prompt v1.0.0 (rejected — four incorrect mappings detected by external auditor)
Input: VTR-DEV-001-internal.md v0.3.0 SHA e1fcdf4a...
Output: VTR-DEV-001-public.md v0.4.0 + 3 operational rules

## Corrections from v1.0.0

Four mappings from v1.0.0 are REJECTED:

  REJECTED: PROBABLE -> DESIGNED_NOT_TESTED
    Reason: PROBABLE can include empirical evidence with insufficient
    replication. DESIGNED_NOT_TESTED implies no evidence exists.
    These are distinct epistemic states. Collapsing them loses information.

  REJECTED: NO-CUBIERTO -> OUT_OF_SCOPE
    Reason: OUT_OF_SCOPE means deliberately excluded from scope.
    NO-CUBIERTO means not yet evaluated. Different predicates.

  REJECTED: tau -> BLAST_RADIUS_UNKNOWN
    Reason: tau is an independence threshold (D(i1,i2) >= tau).
    Blast radius is a security impact property. Not the same concept.
    Substitution changes the predicate, not only the name.

  REJECTED: RQ4 CONFIRMED (partial)
    Reason: No epistemologically clean category of CONFIRMED (1/3).
    Already corrected in commit 8b91704.

## Structural requirement: STATE and PROPERTY are separate fields

Do not mix epistemic states with experimental properties.

EPISTEMIC STATES (what we know):
  REPRODUCED         — reproducible with artifact + SHA + log + independent verification
  DESIGNED_NOT_TESTED — designed and specified, no empirical evidence yet
  OBSERVED_ONCE      — observed empirically, not yet replicated
  NOT_EVALUATED      — not yet within audit scope (replaces NO-CUBIERTO)
  FALSIFIED          — hypothesis refuted with concrete evidence

EXPERIMENTAL PROPERTIES (conditions of the experiment):
  INDEPENDENT_REPRODUCTION_FOUND    — different auditor found same divergence class
  PENDING_INDEPENDENT_REPRODUCTION  — independent reproduction not yet executed
  INDEPENDENCE_THRESHOLD_UNESTABLISHED — tau not yet defined from empirical evidence
  SOURCE_ONLY_AUDIT                 — audit conducted on source without citing own docs
  ZERO_PRESUMPTION                  — no assumption imported without evidence

These two vocabularies must appear as separate fields, never conflated.

Example of correct usage:
  cfg-shield:
    state:    REPRODUCED
    property: INDEPENDENT_REPRODUCTION_FOUND

  vtr-sentinel-kmod:
    state:    OBSERVED_ONCE (VS-006 detected by first auditor)
    property: PENDING_INDEPENDENT_REPRODUCTION

  RQ4 global:
    state:    PROBABLE
    property: PENDING_INDEPENDENT_REPRODUCTION (2/3 artifacts)

  tau:
    state:    — (tau is not an epistemic state)
    property: INDEPENDENCE_THRESHOLD_UNESTABLISHED

---

## INSTRUCTIONS — EXECUTE IN ORDER, DO NOT SKIP

### 1. TERMINOLOGY FREEZE — APPROVED MAPPINGS ONLY

Replace in all public documents:

  CONFIRMADO  -> REPRODUCED
  OBSERVADO   -> OBSERVED_ONCE
  REFUTADO    -> FALSIFIED
  NO-CUBIERTO -> NOT_EVALUATED

DO NOT replace:
  PROBABLE    — keep as PROBABLE in public documents
                (it is already standard English and its meaning
                is preserved; collapsing to DESIGNED_NOT_TESTED
                loses information about the evidence level)

Replace in RQ4 and related documents:
  Delta_A SATISFIED     -> INDEPENDENT_REPRODUCTION_FOUND
  Delta_A PENDING       -> PENDING_INDEPENDENT_REPRODUCTION
  tau INDEFINIDO        -> INDEPENDENCE_THRESHOLD_UNESTABLISHED
  CAPA 0                -> SOURCE_ONLY_AUDIT
  ZIC                   -> ZERO_PRESUMPTION

Prohibited: adding synonyms not in this list.
Prohibited: using BLAST_RADIUS as a substitute for tau.
Prohibited: collapsing PROBABLE into DESIGNED_NOT_TESTED.

### 2. REMOVE NON-AUDITABLE REFERENCES

In VTR-DEV-001-public.md run and verify:

  grep -i "VTR-METH-001|Prompt Maestro|vtr-audit-tools|vtr-elite|zenodo|DOI" VTR-DEV-001-public.md
  Expected result: 0 matches

If >0, remove the complete line where it appears.

Remove:
  §Registro de Artefactos de este Documento
  §Proceso de Expurgacion Controlada
  Tabla "Conexion con VTR-METH-001"

Replace tabla "Conexion con VTR-METH-001" with:

  ## Verification Dimensions
  | Dimension    | Question                                                  |
  |--------------|-----------------------------------------------------------|
  | Artifact     | What exactly is claimed about this binary/module/library? |
  | Boundary     | What is the contract at this interface, verified?         |
  | Lifecycle    | What is the state of this module and its data?            |
  | Sequence     | What is the phase sequence and evidence?                  |
  | Composition  | How do components interact, verified?                     |
  | Provenance   | SHA-256 + snapshot + commit + UTC timestamp — all present?|
  | Runtime      | Has dynamic behavior been observed and documented?        |
  | Falsification| Are there claims stronger than available evidence?        |

### 3. SIMPLIFY R-01 TO R-09 TO 3 OPERATIONAL RULES

Replace 9 rules with:

  R1 — Contract Before Code
  Specification with types, sizes, endianness, and CRC parameters
  written before implementation. Enforced with _Static_assert on
  offsetof and sizeof. Evidence: spec file SHA + compile log.

  R2 — Hash Before Use
  Every artifact loaded, deployed, or distributed must have SHA-256
  recorded with UTC timestamp before being used as input to another
  phase. Evidence: SHA-256 + timestamp in reviews/.

  R3 — Claim = Log + Falsifier
  Every non-trivial claim must define:
    - claim text
    - current epistemic state (from vocabulary above)
    - evidence (commit hash + log + artifact SHA)
    - falsifier: condition that would refute the claim
    - reproduction criterion: what constitutes independent verification
    - proposed experiment, when state is not REPRODUCED

  Claim -> State mapping:
    "I designed it"                          -> PROBABLE
    "I designed and specified it"            -> PROBABLE
    "I implemented but have not tested it"   -> PROBABLE
    "I observed it once"                     -> OBSERVED_ONCE
    "I validated with reproducible evidence" -> REPRODUCED
    "Independent auditor reproduced it"      -> REPRODUCED + INDEPENDENT_REPRODUCTION_FOUND

  Note: "I can defend it to a third party" is NOT sufficient
  for REPRODUCED. Defense ability is not independent reproduction.

Keep Second Reviewer Protocol and vtr-sentinel-kmod case without
modification, only with state vocabulary mapping applied.

### 4. MANDATORY FINAL VERIFICATION — 3 COMMANDS

  a) grep -i "VTR-METH-001|Prompt Maestro|vtr-audit-tools|vtr-elite" VTR-DEV-001-public.md
     Expected: 0

  b) ls -l designs/proposed/c_rust_contract_verification.json \
            designs/proposed/kernel_userspace_semantic_isolation.json
     Expected: both files exist

  c) grep -i "CONFIRMED (partial|CONFIRMED (parcial|tau.*BLAST" VTR-DEV-001-public.md
     Expected: 0

If any verification fails, do not tag.

### 5. RQ4 SUMMARY FOR SCALE 24x

Translate RQ4 state (commit 8b91704) to one line for proposal:

  "Build divergence reproduced by independent auditor in 1/3 artifacts
  (cfg-shield). 2 pending: kmod requires FreeBSD with kernel headers,
  continuity requires SOURCE_ONLY_AUDIT. Independence threshold not
  yet established."

Prohibited in SCaLE submission: tau, Delta_A, RQ4, ZIC, CONFIRMADO,
PROBABLE (use "likely" or "not yet reproduced"), CAPA 0.

---

## What this prompt does NOT change

- The falsification schema (hypothesis / falsifiers / confirmation_criteria /
  proposed_experiment / required_artifacts) remains mandatory for every
  non-trivial claim. It is not a rule — it is a structural requirement
  of R3. Removing it would revert the methodology to pre-R09 state.

- The epistemic states remain hierarchical:
  PROBABLE < OBSERVED_ONCE < REPRODUCED
  A state cannot advance without satisfying its transition conditions.

- STATE and PROPERTY remain separate fields. Never conflate them.

---

## Change log from v1.0.0 to v1.0.1

  REJECTED: PROBABLE -> DESIGNED_NOT_TESTED (loses evidence information)
  REJECTED: NO-CUBIERTO -> OUT_OF_SCOPE (wrong predicate)
  REJECTED: tau -> BLAST_RADIUS_UNKNOWN (different concept)
  REJECTED: RQ4 CONFIRMED (partial) (no clean category for 1/3)
  ADDED: STATE / PROPERTY separation as structural requirement
  ADDED: NOT_EVALUATED as replacement for NO-CUBIERTO
  ADDED: INDEPENDENCE_THRESHOLD_UNESTABLISHED for tau
  ADDED: Note that "defense ability" is not sufficient for REPRODUCED
  CORRECTED: R3 now explicitly includes falsifier as mandatory field
  CORRECTED: PROBABLE kept as-is in public vocabulary (not collapsed)
