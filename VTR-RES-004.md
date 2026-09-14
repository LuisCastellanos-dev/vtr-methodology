# VTR-RES-004: Epistemic State Separation in Computational Investigation
## A Methodological Hypothesis on the Independence of Observation, Attribution, and Validation

**Status:** PROPOSED — NOT VALIDATED  
**Date:** 2026-09-14  
**Author:** Vector Telemetry Research (VTR)  
**Depends on:** VTR-CE-001, VTR-RES-003  
**Relates to:** VTR-METH-001 v5.1 (operational methodology)  
**Classification:** Research — does not modify normative methodology

---

## 1. Scope

This document proposes a methodological hypothesis about the structure of computational
investigation. It does not describe how to conduct an audit. It asks whether explicit
separation of epistemic states during an investigation reduces the probability of a
falsified causal attribution being mistakenly treated as a falsification of the original
observation.

The hypothesis is stated in Section 5. Sections 2–4 establish the conceptual framework.
Section 6 connects the hypothesis to VTR's existing operational vocabulary. Section 7
identifies the field case that first exercises this structure. Section 8 states what would
falsify the hypothesis itself.

This document is a research program, not a result.

---

## 2. The Problem

In computational investigation, a common failure mode is the conflation of distinct
epistemic events:

- An **observation** is recorded.
- A **hypothesis** is formed to explain it.
- The hypothesis is **falsified** by independent review.
- The original observation is discarded along with the hypothesis.

This conflation is epistemically incorrect. A falsified attribution does not entail a
falsified observation. The phenomenon that motivated the hypothesis may remain a valid,
recorded observation even after its proposed explanation has been rejected.

The inverse failure also occurs: when an observation is refuted (the phenomenon does not
reproduce under scrutiny), its associated causal explanation is sometimes preserved — now
applied to a phenomenon that no longer has evidential support.

Both failures produce the same structural error: the epistemic status of one component is
transferred to another without justification.

---

## 3. The Epistemic State Model

A computational investigation involves at minimum the following distinct objects:

| Symbol | Object | Definition |
|--------|--------|------------|
| **O** | Observation | A recorded phenomenon under specified conditions, whose reproducibility is an empirical question independent of any explanation. |
| **H** | Hypothesis | A proposed explanation or attribution assigned to O. |
| **F** | Falsification attempt | An explicit effort to find conditions under which H fails. |
| **R** | Revision | The updated state of H after F. May be a restriction, a modification, or a rejection. |
| **E** | Evidence | Observable outputs obtained through a documented procedure. Not self-interpreting. |
| **V** | Validation state | The current epistemic status of a claim: OBSERVED, PROBABLE, CONFIRMED, FALSIFIED. |

These objects are **not sequentially ordered** in general. A real investigation may
exhibit concurrent transitions:

```
O → H₁ → F₁ → R₁ (H₁ falsified)
              ↓
         E₂ emerges independently
              ↓
         O → H₂ (new attribution, same observation)
```

Independent validation (V) may occur before a satisfactory causal explanation exists.
Falsification of H does not propagate automatically to O or to E.

The central structural claim is:

```
¬H ⊬ ¬O
```

A falsified hypothesis does not entail a falsified observation, except under conditions
that must be formally specified.

---

## 4. The Linear Model as a Special Case

A simplified representation of the investigation structure is:

```
O → H → F → R → E → V
```

This linear sequence describes one possible trajectory. It should not be treated as a
universal law of discovery or as the only valid investigation path.

Its utility is as a minimum legible model: it makes visible the transitions between what
was observed, what was assumed, what was tested, and what remains pending.

In practice, multiple hypotheses may exist simultaneously, evidence may arrive out of
order, and validation may be partial or domain-restricted. The model of states and
relations `{O, H, F, R, E, V}` is more general and more accurate than the linear sequence.

---

## 5. The Hypothesis

**VTR-RES-004-H1:**

> Explicit and documented separation of epistemic states (O, H, F, R, E, V) during a
> computational investigation reduces the probability that a falsified causal attribution
> (¬H) is treated as equivalent to a falsification of the originating observation (¬O).

This is a methodological hypothesis. It makes a claim about investigation practice, not
about mathematical truth.

It can be studied. It can produce positive and negative cases. It can be compared against
investigations that do not perform this separation explicitly. It is therefore subject to
evaluation.

**VTR-RES-004-H2** *(secondary hypothesis)*:

> When ¬H ⊬ ¬O is explicitly recognized and recorded during an investigation, the
> resulting evidence trail is more auditable by an independent party than one in which
> the falsification of H and the status of O are not distinguished.

H2 is treated as a secondary hypothesis because it introduces a variable that H1 does
not require: a measurable definition of "auditability." H1 can be studied through
conflation rates; H2 requires first operationalizing what auditability means and how it
is measured across independent reviewers. H2 cannot be tested until that
operationalization is established.

Neither H1 nor H2 is asserted as true. Both are proposed as falsifiable.

---

## 6. Relationship to VTR's Operational Vocabulary

VTR-METH-001 uses three epistemic categories for claim classification:

```
HECHO (fact) ≠ INFERENCIA (inference) ≠ PROYECCIÓN (projection)
```

These categories are operationally useful but are not formally derived. VTR-RES-004
proposes a possible structural correspondence:

| VTR-METH-001 | VTR-RES-004 | Epistemic function |
|---|---|---|
| HECHO | O + E | Recorded observation supported by documented evidence |
| INFERENCIA | H | Proposed explanation assigned to O, not yet validated |
| PROYECCIÓN | extrapolation beyond current E and V | Claim that exceeds available evidence |

This is a functional correspondence, not a formal equivalence. The research question is:
under what conditions does an observation O, supported by evidence E, justify the
transition to HECHO — without contamination by the causal attribution H?

VTR-CE-001 provides the operational answer: a claim transitions to CONFIRMED only when
variable isolation, documented reproduction, scope specification, and falsification record
are present. VTR-RES-004 asks whether that requirement is sufficient to preserve the
independence of O from H during a real investigation.

---

## 7. Field Case: D59140 (FreeBSD, 2026-09-12)

The following case is not evidence that VTR-RES-004-H1 is true. It is evidence that the
epistemic pattern described by the hypothesis appears in a real investigation under
external review.

**Observation (O):**
`write(2)` to a BPF descriptor on `lo0` returned `EAFNOSUPPORT` on FreeBSD
14.4-RELEASE-p8.

**Hypothesis 1 (H₁):**
The behavior was attributed to missing `sa_len` initialization in `if_output` drivers.

**Falsification (F₁):**
Independent review by Gleb Smirnoff (FreeBSD committer) established that the reproducer
violated the DLT_NULL contract: the required 32-bit address-family header was absent from
the packet.

**Result:**
```
H₁ → FALSIFIED
O  → PRESERVED (observation remained reproducible under corrected conditions)
```

**Hypothesis 2 (H₂):**
A defensive assertion `MPASS(hlen <= sizeof(sockp->sa_data))` in `bpf_movein()` makes
the existing BPF/ifnet contract explicit and machine-checkable. Proposed as hardening
derived from the review process, not as a fix for the original observation.

**Epistemic outcome:**
The investigation distinguished O from H₁ explicitly. The falsification of H₁ did not
eliminate O. A second hypothesis (H₂) emerged from the same observation after H₁ was
rejected. The falsification record was preserved in full.

**Record:** `vtr-sentinel-kmod/reproducers/D59140/RECORD.md`  
**Field case reference in VTR-CE-001:** §12, Field Cases, entry 1.

---

## 8. What Would Falsify This Research Program

VTR-RES-004 is itself subject to its own epistemic standard.

| Claim | Falsifying condition |
|---|---|
| **H1:** Explicit state separation reduces conflation of ¬H and ¬O | A study finds that investigations with explicit state separation produce conflation at rates equal to or higher than those without it. |
| **H2:** Explicit separation produces more auditable evidence trails | Independent auditors rate evidence trails with and without explicit separation as equally auditable. |
| **¬H ⊬ ¬O as a general principle** | A formally specified domain is identified in which falsification of H necessarily entails falsification of O under all conditions. |
| **The state model {O, H, F, R, E, V} is sufficient** | A real investigation is documented in which the model fails to represent a relevant epistemic transition, and no extension of the model can capture it. |

The state model `{O, H, F, R, E, V}` is not presented as a complete ontology of
investigation. It is a provisional model subject to extension or replacement by
evidence. A model that cannot be shown to be insufficient is not stronger — it is
less falsifiable. The fourth row of the table above is therefore not a weakness of
the framework; it is a requirement for its intellectual honesty.

**Note:** D59140 constitutes one case in which the pattern appeared. It does not
constitute validation of H1 or H2. One case is evidence of applicability, not evidence
of generality.

---

## 9. Relationship to Adjacent Work

| Work | Relationship |
|---|---|
| VTR-CE-001 | Provides the operational epistemic state model (OBSERVED, PROBABLE, CONFIRMED, FALSIFIED). VTR-RES-004 asks whether that model is sufficient to preserve O/H independence in practice. |
| VTR-RES-003 | Mathematical formalization of evidence-centered auditing. VTR-RES-004 is a precursor: it must establish that epistemic state separation is a valid research object before it can be formalized mathematically. |
| Lakatos (1976) — *Proofs and Refutations* | Historical reconstruction showing how counterexamples can modify a conjecture's definitions and scope rather than simply refuting its conclusion. Compatible with the ¬H ⊬ ¬O principle under restricted conditions. |
| Popper (1959) — *The Logic of Scientific Discovery* | Falsificationism as the methodological foundation. VTR-RES-004 applies Popperian falsification to the investigative process itself, not only to its object. |
| VTR-METH-001 v5.1 | Operational methodology. VTR-RES-004 does not modify it. It investigates whether METH-001's epistemic categories are sufficient to prevent conflation of observation and attribution during application. |

---

## 10. What This Research Program Does Not Claim

- That VTR discovers theorems.
- That D59140 validates VTR-RES-004-H1 or H2.
- That the state model `{O, H, F, R, E, V}` is complete or formally derived.
- That the linear sequence `O → H → F → R → E → V` describes how all discoveries occur.
- That explicit state separation is sufficient to prevent all epistemic errors.
- That this document constitutes a contribution to formal epistemology.

The document proposes a hypothesis about investigation practice. The cases of VTR can
provide evidence for, against, or insufficient to evaluate that hypothesis. The results
will determine how far the hypothesis can be sustained.

---

## 11. The Central Question

VTR-RES-004 does not ask:

> "Did VTR find a bug in FreeBSD?"

It asks:

> "When a causal attribution is falsified during a computational investigation, under what
> conditions does the originating observation remain a valid, independent epistemic object —
> and how can that independence be preserved, recorded, and verified by a third party?"

That question is investigable. Its answer is not assumed.

---

## References

Lakatos, I. (1976). *Proofs and Refutations: The Logic of Mathematical Discovery*.
Cambridge University Press.

Popper, K. (1959). *The Logic of Scientific Discovery*. Hutchinson.

Gilbert, S., & Lynch, N. (2002). Brewer's conjecture and the feasibility of consistent,
available, partition-tolerant web services. *ACM SIGACT News*, 33(2), 51–59.

VTR-CE-001 (2026). Computational Epistemic Auditing: A Framework for Evidence-Bounded
Claims About Computational Systems. Vector Telemetry Research.

VTR-RES-003 (2026). Mathematical Formalization of Evidence-Centered Auditing.
Vector Telemetry Research.

---

*Vector Telemetry Research — Applied Cryptography and Systems Engineering*  
*Status: PROPOSED. This document proposes a falsifiable methodological hypothesis.*  
*It does not modify normative methodology. CC BY 4.0*
