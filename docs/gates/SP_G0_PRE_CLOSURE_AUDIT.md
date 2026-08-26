# SP-G0 — Pre-Closure Acceptance Audit

**Gate:** SP-G0 — Signal-Processing Contract Freeze
**Audit Type:** Pre-Closure Acceptance Audit
**Gate Status During Audit:** ACTIVE
**Audit Verdict:** PASS - CONTRACT AND PROVENANCE CLOSURE COMPLETE

---

## 1. Purpose

This audit evaluates whether the scientific and engineering contract required
by SP-G0 is sufficiently complete for gate closure.

The audit distinguishes two questions:

1. Are the signal-processing conventions themselves fully defined?
2. Have all repository-level closure actions been completed?

The first question is evaluated by the twelve SP-G0 acceptance criteria.

The second question includes report compilation, authoritative gate-status
synchronization, handoff synchronization, and traceable commit evidence.

SP-G0 shall remain ACTIVE until both layers are complete.

---

## 2. Authoritative Audit Inputs

Primary SP-G0 records:

    docs/gates/SP_G0_SIGNAL_PROCESSING_CONTRACT.md
    docs/decisions/SP_G0_CANONICAL_SIGNAL_CONVENTIONS.md
    report/latex/content/03_signal_processing_and_numerical_model.tex

Predecessor implementation evidence:

    hmolhem/fpga-hls-doa-scm-accelerator
        src/hls/top.h
        src/hls/top.cpp
        src/hls/main.cpp

Cross-project convention evidence:

    hmolhem/geodoa-studio
        docs/adr/ADR-001-coordinate-direction-spatial-conventions.md

Historical convention comparison:

    hmolhem/MIMO_GEOMETRY_ANALYSIS

---

## 3. Acceptance-Criterion Audit

### AC-01 — Dimensions and logical X indexing

Requirement:

    N, M, and logical X indexing are frozen.

Evidence:

    N = 8
    M = 128
    X in C^(8 x 128)
    X[n,m] = sensor n, snapshot m
    sensors are rows
    snapshots are columns

Evidence class:

    SOURCE EVIDENCE + PROJECT CONTRACT

Result:

    PASS

Rationale:

The predecessor HLS implementation and the SP-G0 contract agree on matrix
dimensions and sensor/snapshot indexing.

---

### AC-02 — Covariance normalization and conjugation

Requirement:

    Covariance normalization and complex conjugation are frozen.

Evidence:

    R_hat = (1/M) X X^H

Elementwise:

    R_hat[i,j]
        =
        (1/M) sum_m X[i,m] conj(X[j,m])

Complex product:

    (a+jb)(c-jd)
        =
        (ac+bd) + j(bc-ad)

Result:

    PASS

Rationale:

The arithmetic definition is explicitly present in predecessor source evidence
and has been adopted unchanged by the project contract.

---

### AC-03 — Mean treatment

Requirement:

    Mean treatment is explicit.

Evidence:

No sample-mean subtraction is performed before SCM formation.

Canonical operation:

    R_hat = (1/M) X X^H

The project explicitly identifies this as the uncentered array-processing SCM /
sample second-moment operation.

Result:

    PASS

---

### AC-04 — ULA spacing and sensor coordinates

Requirement:

    ULA spacing and sensor coordinates are explicit.

Canonical decision:

    d/lambda = 0.5

    xi_n = x_n/lambda = n/2

    n = 0,...,7

Result:

    PASS

Rationale:

The spacing is an explicit NEW PROJECT DECISION and is independently consistent
with the predecessor deterministic steering vector.

---

### AC-05 — Angular reference and positive-angle direction

Requirement:

    Angular reference and positive-angle direction are explicit.

Canonical convention:

    array axis = +x
    theta = 0 deg = broadside
    theta > 0 rotates toward +x
    visible sector = [-90 deg,+90 deg]

Result:

    PASS

---

### AC-06 — Steering trigonometric convention

Requirement:

    The steering trigonometric law is explicit.

Canonical spatial projection:

    sin(theta)

Canonical normalized phase:

    2 pi xi_n sin(theta)

Result:

    PASS

---

### AC-07 — Steering exponential sign

Requirement:

    Steering exponential sign is explicit.

Canonical receive steering law:

    a_n(theta)
        =
        exp[-j 2 pi xi_n sin(theta)]

Temporal phasor convention:

    exp(-j omega t)

Result:

    PASS

Rationale:

The negative-sign convention is explicitly authoritative for this repository.
Historical positive-sign implementations remain documented but are
non-canonical unless explicitly adapted and verified.

---

### AC-08 — Deterministic golden reference case

Requirement:

    Deterministic reference-case semantics are explicit.

Canonical case:

    N = 8
    M = 128
    theta = +30 deg
    source amplitude = 1
    source phase = 0
    additive noise = 0
    s[m] = 1 + j0

Steering vector:

    a =
    [1,-j,-1,+j,1,-j,-1,+j]^T

All M snapshots are identical:

    X[:,m] = a

Analytic covariance:

    R_hat = a a^H

Result:

    PASS

Rationale:

The deterministic vector is predecessor SOURCE EVIDENCE and its complete
physical interpretation is now explicitly frozen by the new project contract.

---

### AC-09 — Randomized regression policy

Requirement:

    Random regression policy is explicit.

Canonical policy:

    NumPy Generator API
    bit generator = PCG64
    root seed = 0
    deterministic SeedSequence-derived substreams
    explicit regression-vector provenance

Implicit global random state is prohibited for controlled evidence generation.

Result:

    PASS

---

### AC-10 — SP-G0 / INT-G0 responsibility boundary

Requirement:

    The signal-processing versus transport-interface boundary is explicit.

SP-G0 freezes:

    logical complex sample X[n,m]
    sensor/snapshot meaning
    mathematical frame dimensions

INT-G0 owns:

    real/imaginary packing
    AXI word width
    byte order
    sign extension
    stream traversal
    DMA framing
    TLAST behavior

Result:

    PASS

Rationale:

No physical AXI serialization assumption is silently inferred from the
logical matrix representation.

---

### AC-11 — Historical convention conflicts

Requirement:

    Historical convention conflicts are documented rather than hidden.

Evidence:

Historical controlled software exists with positive-sign steering
exponentials.

Canonical project convention:

    exp[-j 2 pi xi_n sin(theta)]

Historical positive-sign implementations are classified as non-canonical for
this repository unless explicitly adapted and verified.

Result:

    PASS

---

### AC-12 — SP-G1 can begin without undocumented convention choices

Requirement:

    SP-G1 must be able to generate the canonical array/snapshot model without
    making a new undocumented signal-processing convention choice.

SP-G1 now receives explicit definitions for:

    dimensions
    sensor coordinates
    wavelength-normalized spacing
    angle convention
    steering law
    phase reference
    source amplitude
    source phase
    deterministic noise condition
    mean treatment
    covariance normalization
    random regression policy
    logical sample indexing

Result:

    PASS

Rationale:

No unresolved SP-G0 physical, mathematical, numerical, or logical-frame
convention is required in order to implement the SP-G1 canonical reference
case.

---

## 4. Acceptance Summary

| Criterion | Result |
|---|---|
| AC-01 Dimensions and indexing | PASS |
| AC-02 Covariance and conjugation | PASS |
| AC-03 Mean treatment | PASS |
| AC-04 ULA geometry | PASS |
| AC-05 Angle convention | PASS |
| AC-06 Trigonometric convention | PASS |
| AC-07 Exponential sign | PASS |
| AC-08 Deterministic reference case | PASS |
| AC-09 Random regression policy | PASS |
| AC-10 SP-G0 / INT-G0 boundary | PASS |
| AC-11 Historical conflict documentation | PASS |
| AC-12 SP-G1 completeness | PASS |

Scientific / engineering contract acceptance:

    12 / 12 PASS

---

## 5. Repository Closure Checks

### RC-01 — Diff hygiene

Requirement:

    git diff --cached --check

Status:

    PASS

### RC-02 — Report alignment

Requirement:

The SP-G0 report section contains the frozen contract and does not retain stale
statements claiming resolved conventions remain open.

Status:

    PASS

### RC-03 — LaTeX smoke compilation

Requirement:

The complete report shall compile after SP-G0 report alignment.

Evidence:

    pdflatex -interaction=nonstopmode -halt-on-error main.tex
    biber main
    pdflatex -interaction=nonstopmode -halt-on-error main.tex
    pdflatex -interaction=nonstopmode -halt-on-error main.tex

The build completed successfully and generated `report/latex/main.pdf`.

The generated PDF was treated as build output and was not retained as a
version-controlled source artifact.

Status:

    PASS

### RC-04 — Gate Registry synchronization

The authoritative Gate Registry now records:

    SP-G0 = PASS
    SP-G1 = PENDING

Status:

    PASS

### RC-05 — Continuation handoff synchronization

The continuation state identifies SP-G1 as the next permitted
signal-processing gate after PR #3 is integrated into `main`.

Status:

    PASS

### RC-06 - Traceable Git / PR evidence

Requirement:

The approved SP-G0 closure package shall be committed on the focused SP-G0
branch and reviewed through PR #3.

Evidence:

    Branch:
    docs/sp-g0-signal-processing-contract

    Initial closure commit:
    0e17bc31bb7f6acad8675b8a739906ae9cd3fc17

    Pull request:
    PR #3 - Close SP-G0 signal-processing contract

    Base:
    main

    Pull-request state at provenance closure:
    OPEN

The pull request contains only the bounded SP-G0 closure package and no SP-G1
implementation.

Status:

    PASS
## 6. Engineering Interpretation

SP-G0 has succeeded at the contract level.

The project no longer depends on implicit assumptions concerning:

    ULA spacing
    sensor coordinates
    angular reference
    steering sign
    steering trigonometric law
    deterministic source semantics
    mean treatment
    regression randomness
    logical frame indexing
    signal/interface responsibility boundary

The main remaining risks are therefore no longer ambiguity in the signal
contract.

They are implementation and verification risks belonging to later gates.

This is the intended purpose of SP-G0.

---

## 7. Closure Decision

Scientific / engineering acceptance:

    PASS

Acceptance criteria:

    12 / 12 PASS

Repository closure checks:

    RC-01 through RC-06 PASS

Repository contract state:

    SP-G0 = PASS
    SP-G1 = PENDING

Traceable provenance:

    Initial closure commit:
    0e17bc31bb7f6acad8675b8a739906ae9cd3fc17

    Pull request:
    PR #3 - Close SP-G0 signal-processing contract

SP-G0 is closed.

PR #3 contains the focused SP-G0 closure package. SP-G1 remains PENDING and
shall begin only after this pull request is reviewed, merged into `main`, and
the repository returns to a clean synchronized main-branch state.
