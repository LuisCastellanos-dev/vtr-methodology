# VTR-CE-001: Computational Epistemic Auditing
## A Framework for Evidence-Bounded Claims About Computational Systems

**Status:** PROPOSED — NOT VALIDATED  
**Date:** 2026-09-11  
**Author:** Vector Telemetry Research (VTR)  
**Depends on:** VTR-METH-001 v5.1, VTR-RES-003  
**Classification:** Foundational — precedes VTR-METH-001 conceptually

---

## 1. Scope and Terminology

This document uses the term *Computational Epistemic Auditing* rather than the established
*Computational Epistemology*, which refers to the study of inductive complexity for bounded
agents (Osherson, Stob & Weinstein, 1985). What is described here is distinct: the study of
how claims about the behavior of computational systems are generated, verified, reproduced,
and bounded by evidence obtained through explicit procedures.

The central object of study is not the agent that reasons, but the claim that is asserted and
the evidence that is offered in support of it.

---

## 2. The Problem

Security auditing produces claims. A claim may assert that a vulnerability exists, that a
fix is correct, that a behavior is reproducible, or that a system satisfies a property. Each
of these claims has an evidential structure — a set of observations, procedures, and
conditions under which the claim was produced.

The problem is that the relationship between observation and claim is rarely made explicit.
A system produces a result. A researcher observes the result. A report asserts a conclusion.
Each step involves a transition that is frequently left unexamined.

This gap has been identified in adjacent fields. Hocquet and Wieber (2021) argue that
computational reproducibility "suffers from a naive expectancy of total reproducibility."
A recent formalization in AI safety introduces *fragile assurance* to describe cases where
"the evidential structure does not support the asserted safety claim" — a claim may be true
and useful, but is "routinely treated as more evidentially grounded than its supporting
evidence warrants" (2026).

The Stanford Encyclopedia of Philosophy notes that software testing is "akin to a scientific
experiment which tries to falsify the hypothesis that the program is correct," but immediately
qualifies that "other methodological and epistemological traits characterizing scientific
experiments are not shared by software tests" (Turner & Eden, 2020).

Computational Epistemic Auditing is the study of that gap.

---

## 3. The Central Distinction

The foundational claim of this framework is that four operations are distinct and must not
be conflated:

```
observe ≠ verify ≠ attribute ≠ confirm
```

**Observe:** A result is produced and recorded under specified conditions.

**Verify:** The result is checked against a criterion or specification.
Verification requires a procedure, not merely an observation.

**Attribute:** The result is assigned a cause or explanation.
Attribution requires isolation of variables, not merely correlation.

**Confirm:** The attributed cause is established as the operative explanation
under the conditions defined by the experimental scope.
Confirmation requires that alternative explanations have been ruled out
within the defined boundary.

Each transition requires additional epistemic work. A result can be observed without being
verified. A result can be verified without its cause being attributed. A cause can be
attributed without being confirmed. None of these transitions is automatic.

This structure is consistent with Popperian falsificationism applied to computational
systems: a test can falsify a hypothesis, but no finite number of tests can confirm it
universally. What confirmation means in this framework is always scope-bounded.

The transition Attribute → Confirm deserves particular attention. The general conditions
for that transition are stated in Section 5 (variable isolation, documented reproduction,
scope specification, falsification record). What remains an open research question is:

> **What sufficient and falsifiable conditions distinguish attribution from confirmation
> in computational systems?**

This is not a gap in the framework — it is a central question the framework makes
investigable. The distinction between stating general transition requirements and defining
a formal transition operator is intentional: the operator should be derived from observing
what structure the evidence actually requires, not stipulated in advance.

---

## 4. The Claim as a Traceable Object

A claim is not a statement. It is a structured object with the following components:

```
Claim = (assertion, procedure, evidence, reproduction, scope)
```

**Assertion:** The statement being made about the system.

**Procedure:** The explicit steps by which the evidence was obtained.
Without a procedure, evidence cannot be evaluated or reproduced.

**Evidence:** The observable outputs of the procedure.
Evidence is not self-interpreting. Its strength depends on the procedure
that produced it and the scope within which it applies.

**Reproduction:** The independent repetition of the procedure under comparable conditions.
Reproduction is not replication. Reproduction requires that a third party
can execute the procedure without access to the original researcher's judgment.

**Scope:** The boundary within which the assertion holds.
A claim without a defined scope is not falsifiable, because it cannot be
specified under what conditions it would fail.

This structure parallels the claim-evidence alignment approach formalized in EvidenceLens
(2025), which distinguishes "grounded claims" from "overconfident synthesis" by making the
evidential structure explicit and auditable.

---

## 5. Epistemic States and Transitions

A claim transitions between epistemic states as evidence accumulates. The states used in
VTR-METH-001 can be mapped onto this framework:

| State | Epistemic Meaning |
|---|---|
| OBSERVED | A result has been recorded. The procedure is documented. No attribution has been made. |
| PROBABLE | The result is consistent with a hypothesis. Alternative explanations have not been ruled out. |
| CONFIRMED | The attributed cause has been isolated within the defined scope. Reproduction is documented. |
| REFUTED | The evidence contradicts the hypothesis within the defined scope. |

**Critical constraint:** CONFIRMED does not mean universally true. It means:

> "The evidence available under the conditions specified, obtained through the procedure
> documented, supports the assertion within the scope defined."

This constraint is not a weakness. It is the property that makes the claim auditable.
A claim that does not specify its scope cannot be evaluated by an independent party.

The transition PROBABLE → CONFIRMED requires:
1. Variable isolation: the operative cause has been separated from confounds.
2. Documented reproduction: the procedure has been executed independently.
3. Scope specification: the boundary within which the claim holds is explicit.
4. Falsification record: the conditions under which the claim would fail are stated.

---

## 6. The Complexity Analogy

The P vs NP distinction separates problems whose solutions can be found efficiently from
problems whose solutions can only be verified efficiently. This separation is heuristically
useful as an analogy, not as a structural correspondence.

The analogous question in Computational Epistemic Auditing is:

> What is the relationship between the difficulty of producing a claim and
> the difficulty of verifying, reproducing, or attributing it?

In practice, finding a vulnerability may be computationally or observationally hard, while
verifying a proposed fix may be tractable given access to the same environment. Conversely,
reproducing a claimed behavior may be hard not because of computational complexity but
because of environmental access, variable control, or artifact availability.

This suggests that the epistemic difficulty of a claim is not purely computational.
It depends on:

- **Procedural accessibility:** Can the procedure be executed by a third party?
- **Environmental reproducibility:** Can the conditions be reconstructed?
- **Variable isolation:** Can confounding causes be separated?
- **Artifact availability:** Is the original system state accessible?
- **Temporal stability:** Does the result persist under repeated execution?

No formal correspondence between complexity classes and epistemic states is asserted here.
The analogy points to a research question, not a theorem.

---

## 7. Fragile Assurance and the Audit Gap

Two concepts from adjacent literature are directly applicable:

**Fragile assurance** (2026): A claim is fragile when it cannot be reproducibly checked by
an independent party under comparable conditions, or when the inferential gap between the
evidence and the claim is not supported by the evidence's structure. Fragility is not
falsehood — a fragile claim may be true, but it is treated as more evidentially grounded
than its supporting structure warrants.

**Audit gap** (2026): The divergence between required and achievable verification access.
In security auditing, the audit gap appears when a claim requires evidence that the
auditor cannot independently obtain — because the environment is unavailable, the artifact
has changed, or the procedure is underdocumented.

VTR-METH-001's eight-dimension coverage matrix is a partial response to the audit gap:
it makes explicit which dimensions of a claim have been examined and which have not.
The NOT-COVERED state is not a failure — it is the honest record of the audit gap for
a specific claim under specific conditions.

---

## 8. Implications for Security Auditing Practice

The framework has direct operational implications:

**1. Document the procedure, not just the result.**
A result without a procedure is an observation, not evidence. The procedure must be
explicit enough that a third party can execute it without access to the original
researcher's judgment.

**2. State the scope before claiming confirmation.**
CONFIRMED without a scope is not confirmation. The conditions under which the claim
holds — and the conditions under which it would fail — must be stated before the
claim transitions to CONFIRMED.

**3. Preserve refutation records.**
When evidence refutes a hypothesis, that refutation is information. A methodology that
discards refuted hypotheses loses the record of what was ruled out, which is part of
the evidential structure of any surviving claim.

**4. Separate discovery from validation.**
The evidence that generated a hypothesis cannot serve as independent validation of
that hypothesis. This is the anti-circularity constraint central to VTR's architecture:

```
auto-generation of hypotheses + hetero-validation
```

**5. Treat the claim as an artifact with provenance.**
A claim has an origin, a procedure, a timestamp, and a scope. These are not metadata —
they are constitutive of what the claim means. A claim without provenance is not
auditable.

---

## 9. Relationship to Adjacent Work

| Work | Relationship |
|---|---|
| Hocquet & Wieber (2021) — *Epistemic issues in computational reproducibility* | Identifies the epistemic characteristics of reproducibility (transparency, consistency, sustainability, inclusivity). VTR-CE-001 operationalizes these as procedural requirements for claim transitions. |
| Turner & Eden (2020) — *Philosophy of Computer Science* (Stanford Encyclopedia) | Establishes that software testing is falsificationist in structure but not equivalent to scientific experiment. VTR-CE-001 proposes explicit conditions for evaluating whether and to what extent the gap can be bridged. |
| Fragile Assurance / Audit Gap (2026) | Introduces the concepts directly applicable to security claim evaluation. VTR-CE-001 maps these onto VTR's epistemic state model. |
| EvidenceLens (2025) — *Claim-Evidence Matrix* | Treats financial QA as a claim-evidence alignment problem. VTR-CE-001 extends this structure to security auditing with explicit scope and falsification requirements. |
| Popper — Falsificationism | The foundational philosophical structure. Confirmation in VTR-CE-001 is always scope-bounded and falsifiable in principle. |
| Nancy Cartwright — *Ceteris Paribus* laws | The scope boundary in VTR-CE-001 is structurally equivalent to Cartwright's ceteris paribus conditions: a claim holds within a defined boundary, not universally. |
| VTR-METH-001 v5.1 | The operational methodology that VTR-CE-001 provides the philosophical foundation for. VTR-CE-001 does not modify VTR-METH-001 — it explains why it works. |

---

## 10. What This Framework Does Not Claim

This framework does not claim that:

- All security findings can be fully confirmed. Some claims remain PROBABLE or NOT-COVERED
  indefinitely due to environmental or procedural constraints.

- The epistemic states map formally to complexity classes. The P vs NP analogy is
  heuristic, not structural.

- Following this framework eliminates uncertainty. The goal is to make uncertainty
  explicit and bounded, not to eliminate it.

- Confirmation is achievable for all claims. The framework is designed to handle the
  case where confirmation is not achievable and to record that limitation honestly.

---

## 11. The Central Question

Computational Epistemic Auditing does not ask only:

> "Did the system produce this result?"

It asks:

> "What evidence permits the assertion, under what conditions can it be reproduced,
> what part of the explanation remains unestablished, and within what scope does
> the claim hold?"

The value of preserving this boundary is not philosophical caution. It is operational
precision. A claim bounded by its evidence is auditable. A claim that exceeds its
evidence is fragile. The distinction matters when an independent party must evaluate,
reproduce, or act on the claim.

---

## 12. Falsifiability Matrix

VTR-CE-001 is itself subject to its own epistemic standard. Each principle asserted by
this framework must generate a testable prediction — an observable condition under which
the principle would be contradicted. The following matrix makes this explicit.

This matrix converts VTR-CE-001 from a conceptual framework into an experimental program.
Until these cells are populated with results, the document's status remains PROPOSED.

| Principle | Observable Prediction | Candidate Experiment | Falsifying Result |
|---|---|---|---|
| **observe ≠ verify** | Claims labeled CONFIRMED without documented procedures cannot be independently reproduced at higher rates than those with explicit procedures. | Audit corpus study: compare reproduction rates of claims with and without documented procedures. | Claims without procedures reproduce at equal or higher rates than those with explicit procedures. |
| **verify ≠ attribute** | Verification of a result does not predict attribution accuracy without variable isolation. | Introduce controlled confounds into a reproduced finding; measure whether verification alone detects them. | Verification alone reliably identifies the operative cause without explicit isolation steps. |
| **attribute ≠ confirm** | Attribution claims that lack scope specification fail independent replication at higher rates than scoped attributions. | Cross-environment reproduction study: replicate attributed findings with and without explicit scope definitions. | Unscoped attributions replicate as reliably as scoped ones across environments. |
| **Claim = (assertion, procedure, evidence, reproduction, scope)** | Claims structured according to this tuple are rated as more auditable by independent evaluators than unstructured claims asserting equivalent content. | Blind evaluation study: present structured and unstructured claims to independent auditors; measure auditability ratings. | Independent evaluators rate structured and unstructured claims as equally auditable. |
| **CONFIRMED is scope-bounded** | A CONFIRMED finding in environment E₁ that lacks explicit scope fails to hold in environment E₂ at higher rates than a scoped CONFIRMED finding. | Cross-environment transfer study: move confirmed findings across system configurations with and without scope definitions. | Unscoped CONFIRMED findings transfer as reliably as scoped ones. |
| **Falsification record preserves information** | Audits that retain REFUTED hypotheses produce more accurate final claim sets than audits that discard them. | Controlled audit comparison: two teams audit the same system, one retaining refutation records, one discarding; compare final claim accuracy. | Retaining refutation records does not improve final claim accuracy. |
| **Audit gap is measurable** | The proportion of NOT-COVERED dimensions in VTR-METH-001's coverage matrix correlates with post-audit incident rates. | Retrospective study: correlate coverage matrix completeness against post-engagement findings from clients. | Coverage matrix completeness shows no correlation with post-audit incident rates. |
| **Attribute → Confirm requires a formal transition operator** | Analysts applying only informal transition criteria disagree on whether a claim has reached CONFIRMED at higher rates than those applying explicit criteria. | Inter-rater reliability study: two analysts evaluate the same evidence set using informal vs. explicit transition criteria. | Informal and explicit criteria produce equivalent inter-rater agreement on CONFIRMED status. |

**Note on matrix status:** All cells in the Falsifying Result column describe conditions
that would require revision of this framework. No cell has been tested. This matrix is
the next step — not a completed record.

---

## References

Hocquet, A., & Wieber, F. (2021). Epistemic issues in computational reproducibility:
software as the elephant in the room. *European Journal for Philosophy of Science*, 11, 38.
https://doi.org/10.1007/s13194-021-00362-9

Turner, R., & Eden, A. (2020). The Philosophy of Computer Science.
*Stanford Encyclopedia of Philosophy*.
https://plato.stanford.edu/entries/computer-science/

Osherson, D., Stob, M., & Weinstein, S. (1985). *Systems that Learn*.
MIT Press. [Origin of Computational Epistemology as formal epistemology subdiscipline]

Popper, K. (1959). *The Logic of Scientific Discovery*. Hutchinson.

Cartwright, N. (1983). *How the Laws of Physics Lie*. Oxford University Press.

[Fragile Assurance / Audit Gap] (2026). Position: Behavioural Assurance Cannot Verify
the Safety Claims Governance Now Demands. arXiv:2605.15164.

[EvidenceLens] (2025). EvidenceLens: A Claim-Evidence Matrix for Auditing Financial
Question Answering. arXiv:2606.23724.

---

*Vector Telemetry Research — Applied Cryptography and Systems Engineering*  
*Status: PROPOSED. This document provides the philosophical foundation for VTR-METH-001.*  
*It does not modify normative methodology. CC BY 4.0*
