# VTR-COMP-001 — Compilation Context as a Security Boundary
## Vector Telemetry Research
### Version: 0.1.0-draft · Date: 2026-09-03 · Extends: VTR-DEV-001 R-09

---

> "An artifact is not its source code. An artifact is source code +
> compilation context + toolchain. Without all three, identity is
> incomplete and evidence is PROBABLE."

---

## Purpose

VTR-DEV-001 defines the evidence chain:

    observed event
        -> kernel artifact (SHA-256 of .ko)
        -> device boundary (/dev/vtr0)
        -> userspace record
        -> classification
        -> evidence with hash chain

VTR-COMP-001 adds the missing boundary before that chain begins:

    source -> toolchain + flags + env -> artifact

If that transition is not verifiable, the SHA-256 of the .ko is an orphan.
You cannot reproduce from which source and how it was produced.

This document formalizes R-09 from VTR-DEV-001 and establishes
VTR-COMP-001 as the methodology for treating compilation context as
a first-class security boundary — not metadata.

---

## Central Principle

The compilation context of a program — the flags, format settings, feature
selections, and diagnostic instrumentation used at build time — is not
necessarily represented by the source file itself. These elements may be
defined or resolved through build configuration, feature selection, toolchain
settings, package metadata, CI configuration, or other components of the
surrounding compilation environment.

Changing them, without modifying a single line of source, can change:
- what the program does
- what code it contains
- what its test suite exercises
- what failures become visible

**This is not a build system problem. It is a security observability problem.**

---

## R-09 · Compilation Context Reproducible and Recorded

**Statement:** No own artifact can be declared CONFIRMED by its SHA-256
alone. Its complete identity is:

    SHA-256(source tree) + SHA-256(toolchain) + canonical build flags + host env hash

### Scope of R-09

**What it covers:**
- Which exact compiler produced the binary — not "clang 18" but
  "clang 18.1.6 SHA-256: a3f..."
- Exact flags affecting layout, optimization, hardening:
  CFLAGS, KCFLAGS, Kconfig, make.conf
- Environment variables affecting build:
  CC, LD, SOURCE_DATE_EPOCH, toolchain PATH

**What it does NOT cover:**
- Does not guarantee absence of backdoor in toolchain
  (that is a bootstrap problem, out of scope)
- Does not guarantee bit-for-bit determinism if the toolchain is not
  reproducible. Guarantees that you can know WHY it is not reproducible.

---

## Build Context Manifest

Before compiling any artifact that will be used as evidence input,
a build.manifest.json must exist:

    {
      "artifact": "vtr_sentinel.ko",
      "artifact_sha256": "PENDING — calculated after build",
      "source_tree_sha256": "git rev-parse HEAD + git diff hash",
      "toolchain": {
        "cc": "clang",
        "version": "18.1.6",
        "path": "/usr/bin/clang",
        "sha256": "..."
      },
      "flags": {
        "KCFLAGS": "-O2 -fstack-protector-strong -D_FORTIFY_SOURCE=2",
        "kconfig_sha256": "sha256 of .config used"
      },
      "env": {
        "SOURCE_DATE_EPOCH": "1725400000",
        "host": "FreeBSD 14.4-RELEASE-p8 — uname -a hash"
      },
      "builder": "luis@vtr-dev-01",
      "timestamp_utc": "2026-09-03T11:00:00Z"
    }

The manifest is written BEFORE the build. The artifact_sha256 field
is populated AFTER the build and recorded in the same manifest.

## Evidence States Under R-09

| State | Meaning |
|-------|---------|
| **CONFIRMED** | Build reproducible: same manifest produces same SHA-256 in clean snapshot |
| **PROBABLE** | Manifest complete, built once, reproducibility not verified |
| **OBSERVED** | SHA-256 of artifact recorded, no toolchain manifest |
| **REFUTED** | Two builds with same manifest produce different SHA-256 without explanation |

---

## Extended Evidence Chain

The complete chain from source to evidence:

    source tree (git HEAD + diff SHA)
        -> toolchain (cc SHA-256 + version)
        -> flags + env (build.manifest.json)
        -> artifact (vtr_sentinel.ko SHA-256)
        -> kldload (dmesg + sha256 verified)
        -> /dev/vtr0 boundary (wire format with offsetof/sizeof)
        -> userspace record (parser SHA-256)
        -> evidence with hash chain (SHA-256 chain)

Each -> is a boundary where a hash can be placed before and after.
If one breaks, the subsequent chain is PROBABLE, not CONFIRMED.

---

## Validation Procedure

**Minimum evidence to declare R-09 CONFIRMED:**

1. Manifest written BEFORE build with toolchain SHA-256 pre-calculated
2. Complete build log saved with UTC timestamp
3. sha256 artifact post-build recorded in manifest
4. Reproducibility proof: clean snapshot + make clean && make produces
   same SHA-256 -> CONFIRMED
   If different SHA-256 -> PROBABLE + reason documented

**Clean build requirement:**

Validation build must run in a clean snapshot with no ccache, no
inherited environment:

    env -i make

If reproducibility depends on your local environment, the artifact
is OBSERVED, not CONFIRMED.

---

## Reference Case: Why R-09 Was Necessary

During Phase 2 of vtr-sentinel-kmod, the module produced events that were
verified via hexdump -C. The SHA-256 of the .ko was recorded. But:

- The toolchain version was known, not hashed
- The KCFLAGS were documented in the Makefile, not in a manifest
- The build environment (jail, ccache state, make.conf) was not recorded

This means the Phase 2 evidence is PROBABLE under R-09, not CONFIRMED:
the events observed are real, but a third party cannot reproduce exactly
the artifact that produced them using only the published information.

A separate finding confirms why this matters: CONFIG_* flags in the
Raspberry Pi kernel changed observable security behavior without modifying
a single line of source. Without R-09, the only way to explain the
difference between two artifacts with the same source SHA-256 is to have
the toolchain and flags recorded.

---

## Relationship to Existing Work

**Reproducible Builds (reproducible-builds.org):** verifies that a given
source produces a bit-identical binary across environments. VTR-COMP-001
addresses a different question: whether different build configurations of
the same source produce semantically different programs, and whether audits
against one configuration miss defects visible only in another. Complementary,
not competing.

**VTR-RES-002 (DOI: 10.5281/zenodo.22063208):** documents the empirical
basis for VTR-COMP-001 across three language ecosystems (COBOL, Rust, C).
VTR-COMP-001 formalizes the methodology implied by that research.

---

## Version History

| Version | Date | Change |
|---------|------|--------|
| 0.1.0-draft | 2026-09-03 | Initial version — R-09 formalization and Build Context Manifest |

---

*Vector Telemetry Research — Tampico, Tamaulipas, Mexico*
*SIGNAL. VECTOR. INTELLIGENCE.*

All content released under CC BY 4.0.
DOI: pending — to be registered on Zenodo upon stable release.
