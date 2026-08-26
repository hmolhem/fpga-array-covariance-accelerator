# Engineering Gate Registry

## Purpose

This document is the authoritative registry for project gate identity,
scope, status, and closure evidence.

A gate may be marked `PASS` only when its acceptance criteria are satisfied
and its evidence is committed or otherwise traceably recorded.

---

## Status Definitions

| Status | Meaning |
|---|---|
| PENDING | Gate has been defined but execution has not started |
| ACTIVE | Gate is currently under analysis or implementation |
| PASS | Acceptance criteria are satisfied with recorded evidence |
| HOLD | Work is intentionally paused because of a dependency or decision |
| FAIL | Acceptance criteria were evaluated and not satisfied |

---

# System Gates

| Gate | Title | Status |
|---|---|---|
| SYS-G0 | Project Contract | PASS |
| SYS-G1 | Architecture and Evidence Contract | PASS |

### SYS-G0 — Project Contract

Purpose:
Freeze the baseline project objective, scope, exclusions, evidence
philosophy, and hardware/software partition.

Primary evidence:
- `docs/PROJECT_CHARTER.md`
- approved project proposal

---

### SYS-G1 — Architecture and Evidence Contract

Purpose:
Freeze the repository architecture, report architecture, gate hierarchy,
verification structure, and evidence locations.

Exit condition:
All major engineering layers have defined repository and report locations
before algorithmic implementation begins.

---

# Signal Processing Gates

| Gate | Title | Status |
|---|---|---|
| SP-G0 | Signal-Processing Contract Freeze | ACTIVE |
| SP-G1 | Array Signal and Snapshot Model | PENDING |
| SP-G2 | Floating-Point SCM Golden Reference | PENDING |
| SP-G3 | Statistical vs Numerical Error Separation | PENDING |
| SP-G4 | Numerical Range and Fixed-Point Requirements | PENDING |
| SP-G5 | Fixed-Point Covariance Fidelity | PENDING |
| SP-G6 | Downstream DOA Consistency | PENDING |
| SP-G7 | Signal-Processing Acceptance | PENDING |

### SP-G0
Freeze dimensions, indexing, ULA convention, steering convention,
normalization, complex representation, frame layout, and deterministic
reference scenario.

### SP-G1
Establish the array signal model and verify snapshot generation against the
frozen steering-vector convention.

### SP-G2
Establish the authoritative floating-point SCM reference and structural
covariance checks.

### SP-G3
Explicitly separate finite-snapshot estimation error from implementation /
finite-word-length error.

### SP-G4
Derive fixed-point numeric requirements including scaling, product growth,
accumulator growth, rounding, saturation, and overflow behavior.

### SP-G5
Quantify fixed-point covariance error against the floating-point reference.

### SP-G6
Evaluate downstream DOA consistency using floating-point and fixed-point
covariance matrices.

### SP-G7
Accept or reject the selected numerical representation from the
signal-processing perspective.

---

# FPGA / HLS Gates

| Gate | Title | Status |
|---|---|---|
| FPGA-G0 | Numerical Format Handoff | PENDING |
| FPGA-G1 | Fixed-Point C++ Reference | PENDING |
| FPGA-G2 | HLS C Simulation | PENDING |
| FPGA-G3 | HLS Synthesis and Architecture | PENDING |
| FPGA-G4 | C/RTL Co-Simulation | PENDING |
| FPGA-G5 | Timing and Resource Freeze | PENDING |

### FPGA-G0
Receive a selected and justified numerical format from the signal-processing
study.

### FPGA-G1
Demonstrate bit-oriented agreement between the fixed-point C++ model and
the approved numerical reference.

### FPGA-G2
Demonstrate functional correctness in HLS C simulation.

### FPGA-G3
Synthesize the selected architecture and characterize latency, initiation
interval, and resource use.

### FPGA-G4
Verify RTL behavior against common vectors using C/RTL co-simulation.

### FPGA-G5
Record achieved timing, critical path, resource utilization, and the
selected architecture.

---

# Integration Gates

| Gate | Title | Status |
|---|---|---|
| INT-G0 | AXI Stream Contract | PENDING |
| INT-G1 | Packing and Loopback Verification | PENDING |
| INT-G2 | Vivado Overlay Integration | PENDING |
| INT-G3 | PS-PL-PS Board Transaction | PENDING |
| INT-G4 | Board-vs-Golden Covariance | PENDING |

---

# End-to-End Gates

| Gate | Title | Status |
|---|---|---|
| E2E-G0 | Board Covariance to Software DOA | PENDING |
| E2E-G1 | Performance Characterization | PENDING |
| E2E-G2 | Engineering Interpretation | PENDING |

---

# Release Gate

| Gate | Title | Status |
|---|---|---|
| REL-G0 | Reproducibility and Final Release | PENDING |

REL-G0 requires:

- clean-checkout reproducibility;
- configuration manifest;
- final evidence matrix;
- final report;
- limitations;
- engineering interpretation;
- traceability from major claims to evidence.

---

# Gate Closure Template

Every detailed gate record shall contain:

1. Purpose
2. Engineering / Scientific Question
3. Inputs and Frozen Assumptions
4. Theory or Design Basis
5. Implementation / Evaluation Method
6. Verification and Acceptance Criteria
7. Evidence Produced
8. Engineering Interpretation
9. Gate Decision

No section may be silently omitted when a gate is closed.


