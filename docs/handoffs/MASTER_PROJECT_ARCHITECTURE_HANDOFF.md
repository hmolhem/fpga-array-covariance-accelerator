# Master Project Architecture Handoff

## Project Identity

**FPGA Array Covariance Accelerator**

Repository:

    hmolhem/fpga-array-covariance-accelerator

Current project origin:

- Master's Project engineering work
- Kennesaw State University
- Fall 2026
- PYNQ-Z2 / Zynq-7000 target platform

The repository identity is intentionally technical and independent of a course number
so that the project can later evolve into a public engineering portfolio repository.

---

## 1. Purpose

This handoff freezes the master engineering architecture before scientific and FPGA
implementation proceeds.

The project shall not be developed as a sequence of unrelated experiments.

Every major technical result shall connect through the following chain:

    Requirement
        |
        v
    Theory / Design Basis
        |
        v
    Numerical Reference
        |
        v
    Numerical / Hardware Design
        |
        v
    Implementation
        |
        v
    Verification
        |
        v
    Evidence
        |
        v
    Engineering Interpretation
        |
        v
    Decision
        |
        v
    Reproducible Release

---

## 2. Baseline Engineering Scope

The controlled baseline is:

- one ULA reference case;
- N = 8 complex sensor channels;
- M = 128 snapshots per default frame;
- input snapshot matrix X;
- sample covariance matrix formation defined by

      R_hat = (1/M) X X^H

- SCM computation implemented in FPGA programmable logic;
- software-side control and downstream DOA processing;
- fixed-point numerical analysis;
- floating-point and bit-accurate references;
- Vitis HLS verification;
- Vivado implementation and timing/resource characterization;
- AXI / DMA integration;
- PYNQ-Z2 board verification;
- downstream software DOA consistency testing;
- reproducible engineering evidence and report.

---

## 3. Hardware / Software Boundary

The baseline partition is:

    Processor System / Host
        |
        |  snapshot generation
        |  configuration
        |  packing
        |  DMA control
        v
    FPGA Programmable Logic
        |
        |  Sample Covariance Matrix formation
        v
    Processor System / Host
        |
        |  unpacking
        |  numerical comparison
        |  software DOA estimation
        |  result interpretation
        v
    Engineering Evidence

Full MUSIC eigendecomposition is not part of the baseline FPGA responsibility.

---

## 4. Explicit Non-Baseline Scope

The following are not required for baseline completion:

- new DOA algorithm development;
- sparse-array or coarray algorithm development;
- full MUSIC eigendecomposition in FPGA;
- full FMCW radar processing chain;
- RF or raw ADC acquisition;
- CST electromagnetic modeling;
- antenna design;
- robotics integration;
- machine-learning inference;
- product-level power optimization;
- production certification.

These topics may be considered only after baseline closure and through explicit scope
decisions.

---

## 5. Source-of-Truth Hierarchy

Repository documents have distinct responsibilities.

The authority order is:

    docs/PROJECT_CHARTER.md
        |
        v
    docs/GATE_REGISTRY.md
        |
        v
    docs/REPOSITORY_STRUCTURE.md
        |
        v
    docs/gates/
        |
        v
    docs/decisions/
        |
        v
    docs/handoffs/
        |
        v
    report/

Meaning:

- `PROJECT_CHARTER.md` defines project purpose and baseline scope.
- `GATE_REGISTRY.md` owns gate identity and current gate status.
- `REPOSITORY_STRUCTURE.md` defines repository organization.
- detailed gate documents define gate-specific requirements and evidence.
- decision records preserve explicit engineering choices.
- handoffs preserve continuation state.
- the report communicates verified engineering work.

A handoff shall not silently override the Gate Registry.

---

## 6. Gate Status Vocabulary

Permitted gate states are:

- `PENDING`
- `ACTIVE`
- `PASS`
- `HOLD`
- `FAIL`

A gate may be marked `PASS` only when acceptance criteria are satisfied and traceable
evidence exists.

Running code alone is not gate closure.

Writing documentation alone is not gate closure.

---

## 7. Current Master Gate State

### System

    SYS-G0  Project Contract                         PASS
    SYS-G1  Architecture and Evidence Contract      PASS

### Signal Processing

    SP-G0   Signal-Processing Contract Freeze       PASS
    SP-G1   Array Signal and Snapshot Model         PENDING
    SP-G2   Floating-Point SCM Golden Reference     PENDING
    SP-G3   Statistical vs Numerical Error          PENDING
    SP-G4   Numerical / Fixed-Point Requirements    PENDING
    SP-G5   Fixed-Point Covariance Fidelity         PENDING
    SP-G6   Downstream DOA Consistency              PENDING
    SP-G7   Signal-Processing Acceptance            PENDING

### FPGA / HLS

    FPGA-G0 Numerical Format Handoff                 PENDING
    FPGA-G1 Fixed-Point C++ Reference                PENDING
    FPGA-G2 HLS C Simulation                         PENDING
    FPGA-G3 HLS Synthesis / Architecture             PENDING
    FPGA-G4 C/RTL Co-Simulation                      PENDING
    FPGA-G5 Timing and Resource Freeze               PENDING

### Integration

    INT-G0  AXI Stream Contract                      PENDING
    INT-G1  Packing / Loopback Verification          PENDING
    INT-G2  Vivado Overlay Integration               PENDING
    INT-G3  PS-PL-PS Board Transaction               PENDING
    INT-G4  Board-vs-Golden Covariance               PENDING

### End-to-End

    E2E-G0  Board Covariance to Software DOA         PENDING
    E2E-G1  Performance Characterization             PENDING
    E2E-G2  Engineering Interpretation               PENDING

### Release

    REL-G0  Reproducibility and Final Release        PENDING

The authoritative current status remains `docs/GATE_REGISTRY.md`.

---

## 8. Mandatory Gate Record

Every detailed gate shall contain:

1. Purpose
2. Engineering / Scientific Question
3. Inputs and Frozen Assumptions
4. Theory or Design Basis
5. Implementation / Evaluation Method
6. Verification and Acceptance Criteria
7. Evidence Produced
8. Engineering Interpretation
9. Gate Decision

No gate may close by silently omitting one of these responsibilities.

---

## 9. Signal-Processing Engineering Chain

The central numerical chain is:

    Population Covariance
        |
        v
    Finite-Snapshot Floating-Point SCM
        |
        v
    Fixed-Point SCM
        |
        v
    FPGA / RTL SCM
        |
        v
    Board SCM
        |
        v
    Software DOA

A key scientific distinction shall be preserved:

    finite-snapshot statistical error

is not the same quantity as

    finite-word-length numerical error.

The project must not combine these two error sources into an ambiguous single metric.

---

## 10. Numerical Reference Hierarchy

The verification hierarchy is:

    R_float
        <->
    R_fixed_bit_accurate
        <->
    R_HLS_RTL
        <->
    R_board
        |
        v
    Software DOA

Interpretation:

- difference between `R_float` and `R_fixed_bit_accurate` may represent expected
  quantization / finite-word-length behavior;
- disagreement between hardware and the approved bit-accurate reference must initially
  be treated as an implementation or interface defect until explained.

---

## 11. Baseline Numerical Metrics

Primary covariance error:

    epsilon_R =
        ||R_fixed - R_float||_F
        --------------------------------
              ||R_float||_F

Structural Hermitian residual:

    epsilon_H =
        ||R - R^H||_F
        ----------------
             ||R||_F

Additional metrics may include:

- maximum element-wise covariance error;
- diagonal consistency;
- overflow count;
- saturation count;
- eigenvalue diagnostics where justified;
- eigengap diagnostics where justified;
- downstream DOA difference.

Downstream DOA difference:

    Delta_theta =
        theta_hat_fixed - theta_hat_float

The software DOA estimator is an application-level validation mechanism, not an
additional FPGA implementation objective.

---

## 12. Verification Matrix

The baseline verification identifiers are:

    V-01  Floating-point SCM reference
    V-02  Fixed-point numerical fidelity
    V-03  Covariance structural checks
    V-04  HLS / RTL agreement
    V-05  AXI / interface pattern verification
    V-06  Board-vs-golden covariance
    V-07  Downstream DOA consistency
    V-08  Performance characterization with explicit provenance
    V-09  Fresh-checkout reproducibility

A verification result must identify the reference against which it is evaluated.

---

## 13. Evidence Hierarchy

Evidence layers are:

    Level 1 - Software
        floating-point reference
        bit-accurate fixed-point reference

    Level 2 - HLS / RTL
        C simulation
        synthesis
        C/RTL co-simulation

    Level 3 - Vivado Implementation
        implemented timing
        utilization
        timing paths

    Level 4 - Board
        PYNQ execution
        DMA transaction
        board-returned covariance
        host-observed timing

These measurements shall not be mixed as though they have identical provenance.

For example:

- HLS estimated latency;
- implemented FPGA timing;
- software execution time;
- host-observed DMA-plus-accelerator time;

are distinct measurements.

---

## 14. Master Report Architecture

The engineering report is organized as:

1. Project Definition and Engineering Requirements
2. System Architecture and Verification Strategy
3. Signal Processing and Numerical Foundation
4. Fixed-Point Numerical Design
5. HLS and FPGA Architecture
6. PYNQ-Z2 PS-PL Integration
7. Verification, Experimental Results, and Evidence
8. Engineering Interpretation and Trade Study
9. Conclusions and Future Work

Planned appendices include:

- signal and interface contracts;
- fixed-point format records;
- golden vectors and regression set;
- selected HLS / Vivado evidence;
- reproducibility manifest.

Sections not yet executed shall remain explicitly pending rather than being filled with
unsupported results.

---

## 15. Engineering Interpretation Requirement

The project shall interpret engineering results rather than merely list them.

Required interpretation topics include:

- numerical accuracy versus word length;
- numerical fidelity versus hardware resources;
- DSP / LUT / FF / BRAM consequences;
- latency versus throughput;
- initiation interval versus latency;
- pipeline and unroll consequences;
- timing closure and critical-path behavior;
- packing / ordering / sign-extension / TLAST faults;
- board-versus-reference discrepancies;
- downstream DOA impact.

A missed timing target is a valid engineering result and shall not be hidden.

---

## 16. Governing Engineering Question

The final bounded engineering question is:

**What FPGA implementation cost is required to preserve useful downstream DOA fidelity
for the approved array-covariance baseline?**

This question keeps the project centered on hardware-algorithm co-design rather than
scope expansion.

---

## 17. Pull Request Discipline

The repository uses focused engineering pull requests.

The current planned sequence begins with:

    PR #1  Repository foundation, governance, and report skeleton
    PR #2  SYS-G1 architecture / evidence contract closure
    PR #3  SP-G0 signal-processing contract closure
    PR #4  SP-G1 array / snapshot model
    PR #5  SP-G2 floating-point SCM golden reference

Later PRs shall continue as focused gates or tightly bounded vertical engineering
slices.

A typical technical PR should connect:

    Requirement / Contract
        |
        v
    Implementation / Evaluation
        |
        v
    Test / Evidence
        |
        v
    Documentation / Report
        |
        v
    Gate Decision

Large PRs containing unrelated gates shall be avoided.

---

## 18. Current Repository State

At the final PR #3 review checkpoint:

- PR #1 — repository foundation and governance — is merged;
- PR #2 — SYS-G1 architecture / evidence contract closure — is merged;
- PR #3 is open and contains the focused SP-G0 signal-processing contract closure;
- the active PR #3 branch is
  `docs/sp-g0-signal-processing-contract`;
- SYS-G0 is `PASS`;
- SYS-G1 is `PASS`;
- SP-G0 is `PASS`;
- SP-G1 remains `PENDING`;
- all twelve SP-G0 acceptance criteria passed;
- the canonical signal-processing conventions are frozen;
- report alignment passed;
- the complete LaTeX smoke build passed;
- SP-G0 provenance closure is complete;
- SP-G0 provenance closure is complete;
- no SP-G1 implementation is included in PR #3.

The SP-G0 closure package freezes the authoritative baseline for:

- N = 8 sensors;
- M = 128 snapshots;
- logical X[n,m] indexing;
- uncentered SCM formation using (1/M) X X^H;
- complex conjugation behavior;
- d/lambda = 0.5 ULA geometry;
- broadside-referenced angular convention;
- sin(theta) spatial projection;
- negative-sign receive steering convention;
- deterministic theta = +30 deg reference case;
- source amplitude, phase, and noise assumptions;
- controlled randomized-regression policy;
- separation between logical signal semantics and future AXI transport
  semantics.

The next implementation work shall occur only after PR #3 is merged into
`main`.

---

## 19. Immediate Next Action

Do not begin SP-G1 inside PR #3.

The permitted continuation sequence is:

    Complete final review of PR #3
        |
        v
    Merge SP-G0 closure into main
        |
        v
    Verify clean main / origin-main synchronization
        |
        v
    Delete the completed SP-G0 branch when appropriate
        |
        v
    Create focused SP-G1 development branch
        |
        v
    Begin SP-G1 — Array Signal and Snapshot Model

SP-G1 shall consume the frozen SP-G0 contract.

SP-G1 shall not redefine the frozen geometry, steering, indexing, covariance,
deterministic-case, random-regression, or logical-frame conventions unless a
future explicit engineering decision formally revises the contract.

---
## 20. Handoff Principle

This handoff captures project state for continuation.

When project state changes:

- the Gate Registry shall be updated first when gate status changes;
- detailed evidence shall be placed in its proper repository location;
- handoffs shall summarize, not redefine, the engineering truth.

The repository itself remains the persistent source of truth.
