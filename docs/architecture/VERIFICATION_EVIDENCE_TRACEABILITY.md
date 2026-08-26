# Verification and Evidence Traceability Architecture

## 1. Purpose

This document defines the authoritative system-level traceability contract
between verification responsibilities, engineering gates, implementation
layers, evidence locations, reviewed results, and report destinations.

The objective is to ensure that every major engineering claim can be traced
through a controlled chain:

    Engineering Gate
        |
        v
    Source / Analysis
        |
        v
    Executable Verification
        |
        v
    Verification Evidence
        |
        v
    Reviewed Result
        |
        v
    Engineering Interpretation
        |
        v
    Report Claim

The existence of a verification identifier does not imply that the
verification has been executed or passed.

---

## 2. Verification Identifier Contract

The project uses the following system-level verification identifiers.

| ID | Verification Responsibility |
|---|---|
| V-01 | Floating-point SCM reference verification |
| V-02 | Fixed-point numerical fidelity |
| V-03 | Covariance structural verification |
| V-04 | HLS / RTL agreement |
| V-05 | Interface and packing verification |
| V-06 | Board-versus-golden covariance verification |
| V-07 | Downstream DOA consistency |
| V-08 | Performance characterization with explicit provenance |
| V-09 | Fresh-checkout reproducibility |

These identifiers are stable architectural labels.

Detailed test IDs may later exist below each verification responsibility.

---

## 3. Traceability Matrix

| Verification ID | Primary Gate Ownership | Source / Analysis | Test Logic | Evidence Location | Reviewed Result | Report Destination |
|---|---|---|---|---|---|---|
| V-01 | SP-G2 | `software/floating_point/` | `tests/unit/`, `tests/regression/` | `verification/vectors/`, `verification/records/` | covariance-reference tables | Chapter 3, Chapter 7 |
| V-02 | SP-G5 | `software/fixed_point/`, `configs/` | `tests/unit/`, `tests/regression/` | `verification/records/`, `verification/manifests/` | fixed-point error tables / figures | Chapter 4, Chapter 7 |
| V-03 | SP-G2, SP-G5 | floating-point and fixed-point covariance models | `tests/unit/`, `tests/regression/` | `verification/records/` | Hermitian / structural diagnostics | Chapter 3, Chapter 4, Chapter 7 |
| V-04 | FPGA-G4 | `hls/src/`, `hls/tb/` | HLS testbench / integration tests | `verification/records/` | HLS/RTL agreement summary | Chapter 5, Appendix D |
| V-05 | INT-G1 | packing logic, interface contract | `tests/integration/`, known-pattern tests | `verification/vectors/`, `verification/records/` | interface validation summary | Chapter 6, Appendix A |
| V-06 | INT-G4 | `pynq/`, bit-accurate golden reference | `tests/integration/`, board comparison scripts | `verification/manifests/`, `verification/records/` | board-versus-golden comparison | Chapter 7 |
| V-07 | SP-G6, E2E-G0 | software DOA path + approved covariance inputs | regression / end-to-end tests | `verification/records/` | DOA difference tables / spectra | Chapter 4, Chapter 7 |
| V-08 | FPGA-G3, FPGA-G5, E2E-G1 | HLS, `vivado/`, PYNQ timing path | subsystem measurement scripts / tool reports | `verification/manifests/`, `verification/records/` | latency, II, timing, utilization, host timing | Chapter 5, Chapter 7, Chapter 8 |
| V-09 | REL-G0 | complete controlled repository | project reproducibility scripts | `verification/manifests/`, `verification/records/` | reproducibility result | Chapter 9, Appendix E |

---

## 4. V-01 — Floating-Point SCM Reference Verification

### Purpose

Verify that the authoritative floating-point SCM implementation reproduces
the approved mathematical covariance definition.

Primary relationship:

    Snapshot Matrix X
        |
        v
    Floating-Point SCM
        |
        v
    R_float

Expected verification includes:

- dimensional correctness;
- element-wise covariance correctness;
- conjugation correctness;
- normalization correctness;
- deterministic reference reproduction.

Primary gate:

`SP-G2`

Primary evidence:

- approved input vectors;
- approved floating-point covariance;
- verification record.

No fixed-point or hardware behavior is evaluated by V-01.

---

## 5. V-02 — Fixed-Point Numerical Fidelity

### Purpose

Quantify finite-word-length effects relative to the approved floating-point
reference.

Primary relationship:

    R_float
        <->
    R_fixed_bit_accurate

Expected metrics may include:

- normalized Frobenius covariance error;
- maximum element-wise error;
- overflow count;
- saturation count;
- candidate-format comparison.

Primary gate:

`SP-G5`

V-02 does not validate RTL or board behavior.

---

## 6. V-03 — Covariance Structural Verification

### Purpose

Verify structural properties expected of the covariance result.

Potential diagnostics include:

- Hermitian residual;
- real-valued diagonal consistency;
- covariance symmetry relationships;
- positive-semidefinite diagnostics where appropriate.

V-03 may be applied at multiple numerical layers, but the numerical layer
must always be explicitly identified.

Primary gates:

- `SP-G2`
- `SP-G5`

---

## 7. V-04 — HLS / RTL Agreement

### Purpose

Verify agreement between the approved numerical reference and HLS / RTL
behavior using common controlled vectors.

Primary relationship:

    Bit-Accurate Fixed-Point Reference
        <->
    HLS C / RTL Result

Primary gate:

`FPGA-G4`

Evidence may include:

- C/RTL co-simulation results;
- mismatch counts;
- exact or tolerance-based comparison;
- testbench logs;
- approved verification record.

A mismatch at this level shall not initially be classified as ordinary
quantization error because quantization is already represented by the
bit-accurate fixed-point reference.

---

## 8. V-05 — Interface and Packing Verification

### Purpose

Verify that data representation and transport conventions are correct before
complex algorithmic board results are trusted.

Expected checks include:

- real / imaginary ordering;
- sensor / snapshot ordering;
- word width;
- sign extension;
- endianness;
- frame length;
- TLAST behavior;
- deterministic known-pattern loopback.

Primary gate:

`INT-G1`

Known integer patterns should be used before complex signal-processing data
where practical.

---

## 9. V-06 — Board-versus-Golden Covariance Verification

### Purpose

Verify that the covariance returned from the implemented board path agrees
with the approved bit-accurate reference.

Primary relationship:

    R_fixed_bit_accurate
        <->
    R_board

Primary gate:

`INT-G4`

A disagreement shall initially be treated as an implementation, interface,
packing, transport, or configuration discrepancy until evidence identifies
another cause.

---

## 10. V-07 — Downstream DOA Consistency

### Purpose

Determine whether the approved covariance implementation preserves useful
downstream DOA behavior.

Primary relationship:

    R_float
        |
        v
    Software DOA_float

and

    R_fixed / R_board
        |
        v
    Software DOA_test

A primary application metric may be:

    Delta_theta =
        theta_hat_test - theta_hat_float

Primary gates:

- `SP-G6`
- `E2E-G0`

The software DOA estimator remains an application-level verification layer,
not an FPGA implementation objective.

---

## 11. V-08 — Performance Characterization

### Purpose

Characterize implementation performance while preserving measurement
provenance.

Potential quantities include:

- HLS latency in cycles;
- HLS initiation interval;
- HLS estimated clock;
- Vivado implemented timing;
- WNS;
- DSP utilization;
- LUT utilization;
- FF utilization;
- BRAM utilization;
- host-observed DMA-plus-accelerator time.

Primary gates:

- `FPGA-G3`
- `FPGA-G5`
- `E2E-G1`

The following measurements shall not be silently combined:

    HLS cycle estimate
    !=
    implemented FPGA timing
    !=
    software runtime
    !=
    host-observed board transaction time

Any speedup claim requires comparable timing boundaries.

---

## 12. V-09 — Fresh-Checkout Reproducibility

### Purpose

Verify that the controlled project can be reproduced from a clean repository
state using documented dependencies and configurations.

Primary gate:

`REL-G0`

Expected evidence may include:

- Git revision;
- environment / tool versions;
- configuration manifest;
- deterministic seeds;
- reproduction commands;
- verification outcome;
- known limitations.

Fresh-checkout reproduction is a release-level verification and is not implied
by successful execution on the original development machine.

---

## 13. Evidence Record Minimum Metadata

A controlled verification record should identify, where applicable:

- Verification ID;
- Gate ID;
- Git commit;
- implementation under test;
- reference implementation;
- configuration;
- test-vector identifier;
- random seed;
- numerical format;
- target device;
- tool version;
- requested clock;
- measured metric;
- observed result;
- acceptance criterion;
- acceptance decision;
- engineering interpretation;
- evidence paths.

Not every field applies to every test.

Non-applicable fields should be omitted or explicitly marked rather than
invented.

---

## 14. Evidence Layer Separation

Evidence levels remain distinct.

### Software

    Floating-point reference
    Bit-accurate fixed-point reference

### HLS / RTL

    C simulation
    synthesis
    C/RTL co-simulation

### Vivado Implementation

    implemented timing
    resource utilization
    timing paths
    implementation reports

### Board

    PYNQ execution
    DMA transaction
    board-returned covariance
    host timing

### Application

    covariance fidelity
    downstream DOA behavior

A result shall identify which evidence layer produced it.

---

## 15. Reviewed Result Ownership

Verification evidence is distilled into reviewed results only after the
relevant evidence has been examined.

Reviewed outputs belong in:

    results/tables/
    results/figures/

A result file should not replace its underlying verification evidence.

---

## 16. Report Consumption Rule

The report consumes reviewed, traceable evidence.

The preferred chain is:

    Source
        |
        v
    Test
        |
        v
    Verification Record
        |
        v
    Reviewed Result
        |
        v
    Report Table / Figure / Claim

The report is not permitted to become an independent numerical authority.

---

## 17. Status

This document defines system-level verification ownership only.

It does not mark V-01 through V-09 as executed or passed.

Execution and acceptance remain controlled by their corresponding engineering
gates.
