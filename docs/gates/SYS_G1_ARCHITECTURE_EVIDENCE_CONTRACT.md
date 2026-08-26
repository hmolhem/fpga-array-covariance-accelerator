# SYS-G1 — Architecture and Evidence Contract

## Gate Identity

- Gate ID: `SYS-G1`
- Gate Title: Architecture and Evidence Contract
- Current Status: `PASS`
- Parent Contract: `SYS-G0 — Project Contract`
- Governing Registry: `docs/GATE_REGISTRY.md`

---

## 1. Purpose

SYS-G1 freezes the repository-level engineering architecture before
signal-processing implementation, fixed-point design, FPGA implementation,
and board integration proceed.

The gate establishes where engineering truth is stored, how evidence is
classified, how verification results are traced, and how project claims
propagate into the final report.

The required architecture is:

    Project Requirement
        |
        v
    Engineering / Scientific Gate
        |
        v
    Source or Analysis
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
        |
        v
    Reproducible Release

SYS-G1 is an architecture and evidence-control gate.

It does not establish signal-processing correctness, fixed-point correctness,
HLS correctness, or board correctness.

---

## 2. Engineering Question

The engineering question is:

**Does every major engineering layer of the approved project have an explicit
repository location, verification path, evidence class, report destination,
and gate ownership such that future results can be traced without ambiguity?**

SYS-G1 shall not close merely because the repository contains directories.

The architecture must support actual engineering traceability.

---

## 3. Inputs and Frozen Assumptions

SYS-G1 inherits the following baseline assumptions from SYS-G0:

- project identity:
  `FPGA Array Covariance Accelerator`;

- baseline sensor count:
  `N = 8`;

- baseline snapshot count:
  `M = 128`;

- primary hardware kernel:
  Sample Covariance Matrix formation;

- covariance definition:

      R_hat = (1/M) X X^H

- FPGA / PL responsibility:
  SCM formation;

- processor / host responsibility:
  snapshot generation, configuration, data movement, numerical comparison,
  and downstream software DOA estimation;

- baseline target platform:
  PYNQ-Z2 / Zynq-7000;

- engineering implementation chain:

      Floating-Point Reference
          ->
      Fixed-Point Numerical Reference
          ->
      HLS
          ->
      Vivado Implementation
          ->
      PYNQ / Board Verification
          ->
      Software DOA Evaluation

No SYS-G1 action may silently change these project-level assumptions.

---

## 4. Theory and Design Basis

The architecture follows four separation principles.

### 4.1 Source versus Generated Artifact

Source-controlled engineering assets include:

- source code;
- configuration;
- test logic;
- reproducibility scripts;
- controlled constraints;
- controlled build scripts;
- verification vectors;
- reviewed evidence records;
- report source.

Routine tool-generated build trees are not engineering source.

---

### 4.2 Test Logic versus Evidence

Executable verification logic belongs in locations such as:

- `tests/`
- subsystem testbenches;
- reproducibility scripts.

Observed and reviewed engineering evidence belongs in:

- `verification/vectors/`
- `verification/manifests/`
- `verification/records/`

The test and the evidence produced by the test are not interchangeable.

---

### 4.3 Evidence versus Reviewed Result

Verification evidence records what occurred.

Reviewed results distill accepted evidence into engineering comparisons.

Examples:

- evidence:
  HLS synthesis report;

- reviewed result:
  resource-comparison table;

- interpretation:
  explanation of the resource / latency trade-off.

---

### 4.4 Report versus Numerical Source of Truth

The engineering report communicates verified project state.

The report shall not independently create numerical truth.

A report claim should trace back through:

    Report Claim
        |
        v
    Reviewed Result
        |
        v
    Verification Record
        |
        v
    Test / Configuration
        |
        v
    Source Implementation
        |
        v
    Git Revision

---

## 5. Required Engineering Layers

SYS-G1 requires repository ownership for the following layers.

| Layer | Required Repository Ownership | Current State |
|---|---|---|
| Project governance | `docs/` | DEFINED |
| Configuration | `configs/` | DEFINED |
| Floating-point reference | `software/floating_point/` | DEFINED |
| Fixed-point reference | `software/fixed_point/` | DEFINED |
| HLS source | `hls/src/` | DEFINED |
| HLS testbench | `hls/tb/` | DEFINED |
| HLS automation | `hls/scripts/` | DEFINED |
| Vivado implementation source | `vivado/` | DEFINED |
| PYNQ notebooks | `pynq/notebooks/` | DEFINED |
| PYNQ reusable code | `pynq/scripts/` | DEFINED |
| Unit tests | `tests/unit/` | DEFINED |
| Regression tests | `tests/regression/` | DEFINED |
| Integration tests | `tests/integration/` | DEFINED |
| Golden / verification vectors | `verification/vectors/` | DEFINED |
| Run manifests | `verification/manifests/` | DEFINED |
| Verification records | `verification/records/` | DEFINED |
| Reviewed tables | `results/tables/` | DEFINED |
| Reviewed figures | `results/figures/` | DEFINED |
| Engineering report | `report/latex/` | DEFINED |
| Project automation | `scripts/` | DEFINED |

---

## 6. Verification Architecture

The project verification identifiers are frozen at system level as follows.

| Verification ID | Verification Function |
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

These identifiers describe verification responsibilities.

They do not imply that the corresponding verification has already been
executed or passed.

---

## 7. Evidence Provenance Contract

The following evidence levels shall remain distinct.

### Level 1 — Software Evidence

Examples:

- floating-point SCM;
- bit-accurate fixed-point SCM;
- numerical regression results.

### Level 2 — HLS / RTL Evidence

Examples:

- HLS C simulation;
- synthesis;
- C/RTL co-simulation;
- HLS latency;
- initiation interval;
- HLS resource estimate.

### Level 3 — Implemented FPGA Evidence

Examples:

- Vivado implementation timing;
- WNS;
- implemented resource utilization;
- critical-path information.

### Level 4 — Board Evidence

Examples:

- PYNQ execution;
- DMA transaction;
- board-returned covariance;
- host-observed execution time.

### Level 5 — Application Evidence

Examples:

- covariance fidelity;
- downstream software DOA difference.

Measurements from different evidence levels shall not be silently compared as
though they were obtained from the same measurement boundary.

---

## 8. Performance Provenance Contract

The following quantities are separate engineering measurements:

- HLS estimated cycles;
- HLS initiation interval;
- HLS estimated clock;
- Vivado implemented timing;
- Vivado WNS;
- software runtime;
- host-observed DMA-plus-accelerator time;
- end-to-end application time.

A speedup claim is prohibited unless measurement boundaries are explicitly
comparable.

---

## 9. Report Traceability Contract

The repository engineering layers map to the report as follows.

| Engineering Area | Primary Report Location |
|---|---|
| Project requirements and baseline | Chapter 1 |
| Architecture and verification strategy | Chapter 2 |
| Signal-processing model and SCM reference | Chapter 3 |
| Fixed-point numerical design | Chapter 4 |
| HLS and FPGA implementation | Chapter 5 |
| PYNQ / PS-PL integration | Chapter 6 |
| Verification and experimental evidence | Chapter 7 |
| Engineering interpretation and trade study | Chapter 8 |
| Conclusions, limitations, and future work | Chapter 9 |
| Signal and interface contracts | Appendix A |
| Fixed-point format records | Appendix B |
| Golden vectors / regression set | Appendix C |
| Selected HLS / Vivado evidence | Appendix D |
| Reproducibility manifest | Appendix E |

A report chapter is not itself verification evidence.

---

## 10. Gate-to-Repository Traceability

Representative mappings are:

| Gate | Primary Source / Analysis | Verification / Evidence | Report |
|---|---|---|---|
| SP-G0 | `docs/gates/`, `configs/` | contract audit | Ch. 3 / App. A |
| SP-G1 | `software/floating_point/` | `tests/`, `verification/` | Ch. 3 |
| SP-G2 | `software/floating_point/` | V-01, V-03 | Ch. 3 |
| SP-G3 | `software/floating_point/` | controlled statistical/numerical comparison | Ch. 3 |
| SP-G4 | `software/fixed_point/`, `configs/` | numeric-range records | Ch. 4 / App. B |
| SP-G5 | `software/fixed_point/` | V-02, V-03 | Ch. 4 / Ch. 7 |
| SP-G6 | software DOA path | V-07 | Ch. 4 / Ch. 7 |
| FPGA-G1 | `hls/src/`, fixed-point reference | regression comparison | Ch. 5 |
| FPGA-G2 | `hls/` | HLS C simulation | Ch. 5 / Ch. 7 |
| FPGA-G3 | `hls/` | synthesis reports | Ch. 5 / App. D |
| FPGA-G4 | `hls/` | V-04 | Ch. 5 / App. D |
| FPGA-G5 | HLS + Vivado implementation | V-08 | Ch. 5 / Ch. 8 |
| INT-G0 | interface contract | interface review | Ch. 6 / App. A |
| INT-G1 | packing / loopback logic | V-05 | Ch. 6 / Ch. 7 |
| INT-G2 | Vivado implementation | implementation evidence | Ch. 6 / App. D |
| INT-G3 | `pynq/` | board transaction evidence | Ch. 6 / Ch. 7 |
| INT-G4 | `pynq/` + golden reference | V-06 | Ch. 7 |
| E2E-G0 | board + software DOA | V-07 | Ch. 7 |
| E2E-G1 | system measurement | V-08 | Ch. 7 / Ch. 8 |
| E2E-G2 | reviewed project evidence | engineering trade study | Ch. 8 |
| REL-G0 | complete repository | V-09 | Ch. 9 / App. E |

This table freezes architectural ownership only.

It does not mark any downstream gate as complete.

---

## 11. Acceptance Criteria

SYS-G1 may be marked `PASS` only when all of the following are satisfied.

### SYS1-A1 — Hardware / Software Boundary

The PS / PL boundary is explicitly documented and consistent across project
governance and report architecture.

Status: SATISFIED.

### SYS1-A2 — Gate Ownership

The project gate hierarchy and status authority are explicitly assigned to
`docs/GATE_REGISTRY.md`.

Status: SATISFIED.

### SYS1-A3 — Repository Ownership

Every major project engineering layer has an explicit repository location.

Status: SATISFIED.

Resolution:
A controlled `vivado/` repository area now owns reproducible Vivado Tcl
automation and implementation constraints while generated Vivado build
products remain excluded from source control.

### SYS1-A4 — Verification Identity

Verification responsibilities V-01 through V-09 are explicitly defined without
claiming premature completion.

Status: SATISFIED.

### SYS1-A5 — Evidence Provenance

Software, HLS/RTL, Vivado implementation, board, and application evidence are
kept distinct.

Status: SATISFIED.

### SYS1-A6 — Report Traceability

Every major engineering layer has an identified report destination.

Status: SATISFIED.

### SYS1-A7 — Generated Artifact Policy

Generated tool products are separated from controlled engineering source.

Status: SATISFIED.

### SYS1-A8 — Closure Consistency

Gate Registry, repository structure, report architecture, and this gate record
contain no unresolved architectural contradiction.

Status: SATISFIED.

The final architecture consistency audit completed with all required checks
passing.

---

## 12. Closure Findings

### SYS1-F01 — Missing Vivado Source Location

**Resolution Status: RESOLVED**

Severity:
Architecture closure blocker.

Observation:

The approved project includes Vivado implementation, implemented timing,
resource utilization, AXI integration, and overlay construction.

At the initial SYS-G1 audit, the repository structure did not define a dedicated
tracked location for reproducible Vivado implementation assets.

Required resolution:

Introduce a controlled `vivado/` repository area for source-like implementation
assets while continuing to exclude generated Vivado build products.

Suggested ownership:

    vivado/
    |-- scripts/
    |-- constraints/
    `-- README.md

Possible future additions shall be justified by actual implementation needs.

---

### SYS1-F02 — Verification Traceability Freeze

**Resolution Status: RESOLVED**

Severity:
Architecture closure blocker.

Observation:

Verification responsibilities exist conceptually, but SYS-G1 requires an
explicit frozen mapping among verification IDs, gates, evidence locations,
and report destinations.

Required resolution:

Create a repository-level verification / evidence traceability matrix or
equivalent authoritative architecture record based on V-01 through V-09.

---

### SYS1-F03 — Report SYS-G1 Status Text

**Resolution Status: RESOLVED**

Severity:
Closure action.

Observation:

Before closure, Chapter 2 recorded that the architecture and evidence contract
was not yet closed because repository-specific identifiers still required
freezing.

Required resolution:

After SYS1-F01 and SYS1-F02 were resolved and the final consistency audit
passed, the engineering report was synchronized to record SYS-G1 as `PASS`.

---

## 13. Evidence Produced

Current SYS-G1 evidence includes:

- `docs/PROJECT_CHARTER.md`
- `docs/GATE_REGISTRY.md`
- `docs/REPOSITORY_STRUCTURE.md`
- `docs/handoffs/MASTER_PROJECT_ARCHITECTURE_HANDOFF.md`
- `report/latex/content/02_system_architecture_verification.tex`
- this gate record

Closure evidence additionally includes:

- `vivado/README.md`
- `vivado/scripts/`
- `vivado/constraints/`
- `docs/architecture/VERIFICATION_EVIDENCE_TRACEABILITY.md`
- final SYS-G1 consistency audit

---

## 14. Engineering Interpretation

The SYS-G1 audit demonstrates why repository architecture is treated as an
engineering control rather than cosmetic organization.

The project already distinguishes software, HLS, board verification, results,
and report assets. However, the absence of an explicit Vivado source location
would create an architectural break between HLS synthesis and board integration.

Without closing that gap, future timing, constraint, block-design, or overlay
evidence could become dependent on an undocumented local Vivado project.

Similarly, verification identifiers are useful only when their ownership and
evidence destinations are frozen.

The identified architecture gaps have now been resolved and SYS-G1 is accepted.

---

## 15. Gate Decision

**Decision: PASS**

SYS-G1 is accepted.

Acceptance basis:

- the PS / PL hardware-software boundary is frozen;
- the repository gate hierarchy is authoritative and traceable;
- every major engineering layer has repository ownership;
- reproducible Vivado source ownership is explicitly defined;
- verification responsibilities V-01 through V-09 are frozen;
- evidence provenance rules are defined;
- report destinations are mapped;
- generated artifacts remain separated from controlled source;
- SYS1-F01 is RESOLVED;
- SYS1-F02 is RESOLVED;
- SYS1-F03 is RESOLVED;
- the final architecture consistency audit passed without unresolved checks.

No downstream engineering gate is implicitly accepted by this decision.

`SP-G0` remains `ACTIVE`.

`SP-G1` remains `PENDING`.
