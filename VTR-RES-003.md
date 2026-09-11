# VTR-RES-003: Mathematical Formalization and Independent Validation of Evidence-Centered Security Auditing

**Status:** PROPOSED — NOT VALIDATED  
**Date:** 2026-09-11  
**Author:** Vector Telemetry Research (VTR)  
**Depends on:** VTR-METH-001 v5.1, VTR-COMP-001, VTR-DEV-001

---

## 1. Scope

This research investigates whether the evidence-centered principles developed through VTR can be formalized as a mathematical and experimentally testable layer for security auditing.

The objective is not to replace established security standards, vulnerability databases, scoring systems, or commercial auditing products. It is to determine, through controlled evaluation, whether an additional methodological layer can provide useful information about the quality, reproducibility, coverage, provenance, falsifiability, and evolution of security claims.

This study is proposed as an extension of the existing VTR methodological ecosystem — not as a replacement or premature modification of its foundations. No mathematical formulation introduced here modifies the normative methodology until it survives independent validation.

---

## 2. Central Research Question

Given a security claim, what can be formally established about the evidence supporting it?

A secondary structural question:

> Can a mathematical representation of VTR's evidence model recover established findings, characterize their evidentiary boundaries, and generate independently testable candidates — without using the original evidence as independent validation?

The constraint in the second question is fundamental. The architecture must satisfy:

```
VTR produces hypotheses ≠ VTR validates its own hypotheses
evidence of origin    ≠ evidence of validation
```

---

## 3. Hypotheses

**H1:** The epistemic states of VTR can be represented as a formal system of states and transitions subject to evidence preconditions.

**H2:** It is possible to express mathematically certain properties of falsification, coverage, and independence without converting them into arbitrary confidence metrics.

**H3:** The model can generate predictions or hypotheses whose validity is evaluable through experiments external to the mechanism that generated them.

**H4:** Evidence sequences may exhibit invariants, recursive relations, or observable convergence ratios.

Under H4, the golden ratio φ is one candidate structure among others (Fibonacci recurrence, power laws, discrete dynamical systems). It is not privileged. If experimentally some quantity converges toward:

    E_{n+1} / E_n → φ

that constitutes a result. If it converges toward a different constant, equally so. If no convergence exists, that is also a valid falsifiable outcome.

**H0 (null hypothesis):** No stable mathematical structure exists that explains observed state transitions beyond the explicit methodological rules already defined in VTR-METH-001.

H0 must remain available throughout the research. A result supporting H0 is publishable within VTR logic.

---

## 4. Formal Model

### 4.1 Audit State

An audit state is provisionally defined as:

    S = (C, E, F, K, P)

where:

- `C` — the observed condition or claim
- `E` — available evidence
- `F` — falsification state
- `K` — observed coverage
- `P` — provenance of the evidence

A claim `A` is represented as:

    A = (claim, S)

State transitions follow:

    S_n --[e_{n+1}]--> S_{n+1}

### 4.2 Transition Validity

The research investigates which transitions are legitimate. For example:

    PROBABLE → CONFIRMED

is not valid solely because the number of artifacts increased. It must satisfy the preconditions for CONFIRMED: demonstrated divergence, reproduction under registered conditions, and where applicable, independent demonstration of the consequence.

### 4.3 Evidence Monotonicity

A non-trivial research question is whether incorporating new evidence always increases — or can decrease — the justificatory strength of a claim.

If E1 ⊆ E2, does C(A, E1) → C(A, E2) always hold?

VTR already contains mechanisms (falsification, REFUTED state) that make non-monotonicity plausible. New evidence can reveal that a previously used premise was incorrect:

    C(A, E1) = 1
    C(A, E1 ∪ E2) = 0

This would not be a model failure. It would be an epistemic property to characterize: under what conditions is evidence monotonic, and under what conditions is it revisable?

### 4.4 Coverage as a Formal Space

The Coverage Matrix already provides a useful structure. An audited component carries a coverage vector:

    K_i = (artifact, boundary, state, temporal, composition, provenance, runtime, falsification)

where each dimension is 1 (covered), 0 (not covered), or N (not applicable with documented cause).

This allows investigating:

    Coverage(A)

without automatically converting it to:

    Confidence(A)

That separation is essential. NOT-COVERED answers whether a dimension was examined; the cause explains why it was not.

### 4.5 Composition Effects

The L-7 pattern already establishes that two controls may produce a surface that neither produces individually. This can be expressed as:

    R(A ∘ B) ≠ R(A) ∪ R(B)

The composition delta:

    Δ_AB = R(A ∘ B) − [R(A) ∪ R(B)]

becomes an object of investigation. The experiment must determine when Δ_AB appears, not assume it always exists.

### 4.6 Epistemic Debt

The development history of VTR artifacts (particularly vtr-sentinel-kmod) provides a sequence:

    S_0 → S_1 → S_2 → ... → S_n

where subsequent corrections modified earlier claims (CRC table fix, seq_delta rename, EIO mapping). The model can investigate:

    D_E(S_n) = { claims that depend on evidence subsequently modified }

This is not automatic invalidation. It localizes which parts of the reasoning chain require re-evaluation.

---

## 5. Retrospective Benchmark

### 5.1 Reference Corpus

The benchmark uses cases that already produced externally verifiable outcomes. The known result is frozen before the formal model is applied.

| Case | External Outcome | Role |
|---|---|---|
| FreeBSD D58754 / if_ovpn.c | Merged by kp — rGa841961da752 | Positive control |
| FreeBSD D59140 / sa_len | Under review — glebius | Active case |
| Raspberry Pi VTR-RPi-001 | Resolved — PR #7576 | Resource lifecycle |
| Raspberry Pi VTR-RPi-002 | Resolved — PR #7576 | Latent API / reachability |
| IBM Bank-of-Z Issue #205 / PR #210 | Merged | Systemic cause detection |
| Tailscale Issue #18136 | Reported | Representation divergence |
| Tailscale Issue #20960 | Reported | Platform misidentification |
| Cisco PSIRT-0108668756 | Accepted | Custody chain calibration |
| vtr-sentinel-kmod (internal) | Phase 3 CONFIRMED | Epistemic debt measurement |

### 5.2 Evaluation Categories

For each corpus case, the formal model produces one of four classifications:

**RECOVERED** — the model finds the known result.  
**EXTENDED** — the model finds an additional related property not in the original result.  
**NEW CANDIDATE** — the model finds something not in the original result; requires independent validation before elevation.  
**NOT DETECTED** — the model fails to reproduce the known finding.

NOT DETECTED results are retained, not excluded. If the model finds ten new candidates but fails to recover D58754 or VTR-RPi-002, there is no basis for trusting it on unknown cases.

### 5.3 Evaluation Metrics

These metrics are experimental instruments for evaluating the model — not normative VTR metrics.

    Recall_known       = recovered cases / known cases

    Novelty_validated  = new candidates confirmed independently / new candidates generated

    Epistemic_Precision = new hypotheses correctly classified / new hypotheses generated

### 5.4 Anti-Bias Controls

- Corpus for model construction and corpus for evaluation must be separated.
- If a known case (e.g., D58754) was used to develop the model, it cannot serve as independent validation.
- Validation experiments must be pre-registered before observing their results where technically feasible.
- The falsification phase has access to the hypothesis and its refutation criteria, but no ability to retroactively alter the conditions that produced it.

---

## 6. Research Phases

**Phase A — Formalization**  
Translate existing VTR categories into states, relations, preconditions, and mathematical transitions. The normative methodology is not modified.

**Phase B — Null Model**  
Construct a model that presupposes no convergence, no perfect independence, no monotonicity, and no special constant. This is the baseline.

**Phase C — Synthetic Validation**  
Use controlled cases where the evidence, true claims, and hypotheses to be refuted are known in advance.

**Phase D — Retrospective Application**  
Apply the model to existing VTR artifacts, maintaining separation between data used to build the model and data used to evaluate its predictions.

**Phase E — Blind Experiment**  
Provide a new case to the system without allowing the model prior knowledge of the expected result.

**Phase F — Independent Falsification**  
Subject model-generated predictions to the adversarial second phase of VTR-METH-001, preserving the separation between discovery and falsification.

**Phase G — Evaluation**  
Determine which mathematical properties survived, which were refuted, and which remain indeterminate.

---

## 7. Success Criteria

The study is not successful because it finds an elegant formula, discovers φ, or produces a result that confirms VTR.

The study is successful if it demonstrates that:

> A mathematical model can represent properties of VTR without eliminating uncertainty or introducing unjustified claims.

And additionally if it generates at least one prediction or hypothesis that can be subjected to an independent test.

A negative result is equally publishable within VTR logic:

> No sufficient evidence of a stable mathematical structure was found under the defined experimental conditions.

That outcome is preferable to adapting the model until a desired structure appears.

---

## 8. Relationship to Existing Security Practice

This research does not position VTR as a replacement for existing frameworks. The potential contribution is a different analytical axis:

Existing approaches address:

    Vulnerability | Risk | Severity | Control

The proposed axis investigates:

    Evidence | Reproducibility | Coverage | Falsifiability | Provenance

Whether this additional axis provides measurable value is itself an empirical question. Any claim that it constitutes a new auditing perspective requires independent validation.

---

## 9. Anti-Circularity Architecture

Every hypothesis generated by the model must register its epistemic provenance:

    H_n = (hypothesis, generator, E_origin, t)

The constraint:

    E_origin ≠ E_validation

Evidence that originated a hypothesis cannot count again as independent evidence for that same hypothesis. The architecture is:

    auto-generation of hypotheses + hetero-validation

not:

    auto-generation + auto-validation

The cycle:

    Observation → Model → Hypothesis → Independent Experiment → Evidence → Model Update

must not close epistemologically on itself. New evidence enters as input, not as automatic confirmation of the model that requested it.

---

## 10. Expected Outputs

**VTR-EPI**  
A formal model of states, evidence, and transitions.

**VTR-EPI-VAL**  
A reproducible set of experiments to validate or falsify properties of the model.

**VTR-EPI-LOG**  
An immutable record of which evidence produced each hypothesis, which experiment evaluated it, and what result was obtained.

These three artifacts must satisfy:

    M_{n+1} = F(M_n, E_n)

but never:

    M_{n+1} ⟹ M_n is true

---

## 11. Relationship to Existing VTR Artifacts

| Artifact | Role in VTR-RES-003 |
|---|---|
| VTR-METH-001 v5.1 | Normative methodology — not modified by this research |
| VTR-COMP-001 | R-09 provenance chain feeds Phase D corpus |
| VTR-DEV-001 | Artifact production rules govern VTR-EPI outputs |
| vtr-math | First experimental instance of formalized evidence structure |
| cobol-shield / cfg-shield | Corpus cases for boundary and feature-flag divergence |
| vtr-sentinel-kmod | Internal case for epistemic debt measurement |
| Zenodo DOI 10.5281/zenodo.22063208 | Existing published baseline for cross-language divergence |

---

*Vector Telemetry Research — Applied Cryptography and Systems Engineering*  
*Status: PROPOSED. No formulation in this document modifies VTR normative methodology until it survives independent validation.*
