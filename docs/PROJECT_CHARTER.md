# Project Charter

## Project

**FPGA Array Covariance Accelerator**

Engineering implementation of a fixed-point FPGA/SoC sample covariance
matrix front end for array processing and direction-of-arrival estimation.

---

## 1. Engineering Objective

The project shall translate a verified floating-point sample covariance
matrix (SCM) computation into a traceable fixed-point FPGA/SoC
implementation and evaluate the resulting numerical, architectural,
timing, resource, interface, and downstream DOA behavior.

The engineering chain is:

Theory
→ Floating-Point Reference
→ Fixed-Point Numerical Model
→ HLS Implementation
→ RTL Verification
→ Vivado Implementation
→ PYNQ-Z2 Integration
→ Board-Level Verification
→ Downstream DOA Evaluation
→ Engineering Interpretation

---

## 2. Baseline Processing Contract

The initial controlled baseline is:

- Number of sensor channels: `N = 8`
- Number of snapshots per frame: `M = 128`
- Input data: complex array snapshots
- Primary hardware kernel: Sample Covariance Matrix formation
- SCM definition:

  R_hat = (1/M) X X^H

- FPGA responsibility: SCM formation
- ARM/host responsibility:
  - test-vector generation
  - fixed-point packing/unpacking
  - DMA control
  - software DOA estimation
  - numerical comparison
- Baseline geometry: one ULA reference case
- Data source: controlled synthetic test vectors

Any change to these baseline assumptions requires an explicit engineering
decision record and must not occur silently.

---

## 3. Primary Engineering Question

How can the existing floating-point SCM front end be translated into a
fixed-point FPGA/SoC implementation while preserving sufficient numerical
fidelity for downstream array-processing / DOA use and documenting the
associated hardware cost?

---

## 4. Evidence Principle

No engineering claim is accepted without traceable evidence.

Evidence classes are:

1. **Analytical evidence**
   - equations
   - dimensional analysis
   - numerical range derivation
   - theoretical limiting cases

2. **Software evidence**
   - floating-point reference
   - bit-accurate fixed-point model
   - unit/regression tests
   - golden vectors

3. **HLS / RTL evidence**
   - C simulation
   - synthesis
   - C/RTL co-simulation
   - latency / II / resource reports

4. **Implementation evidence**
   - Vivado implementation
   - timing closure
   - utilization
   - interface verification

5. **Board evidence**
   - PYNQ execution
   - DMA transaction
   - board-versus-golden comparison
   - host-observed timing

6. **Application evidence**
   - covariance fidelity
   - downstream DOA consistency

Evidence from different levels shall not be presented as equivalent.

---

## 5. Engineering Completion Principle

A component is not complete merely because it runs.

A gate closes only when:

Requirement
+ Implementation or Analysis
+ Verification
+ Evidence
+ Engineering Interpretation
= Gate Closure

---

## 6. Baseline Scope

Included:

- array snapshot model
- SCM theory
- floating-point SCM reference
- fixed-point analysis
- two candidate numerical formats
- selected fixed-point implementation
- HLS verification
- timing and resource analysis
- AXI4-Stream / DMA integration
- PYNQ-Z2 PS-PL-PS transaction
- board-versus-golden comparison
- downstream software DOA verification
- reproducible engineering report

---

## 7. Explicit Non-Baseline Scope

The following are not required for the baseline project:

- full MUSIC eigendecomposition in FPGA
- new DOA algorithm development
- sparse-array algorithm development
- full FMCW radar chain
- RF/ADC acquisition
- CST electromagnetic modeling
- robotics or ROS integration
- machine-learning inference
- detailed production power optimization

These may be considered only after the baseline gates are closed.

---

## 8. Repository Governance

- `main` is the integration branch.
- Engineering work is developed on focused branches.
- Changes enter `main` through pull requests.
- Each PR shall represent a coherent engineering slice.
- Generated tool products shall not be treated as source.
- Results appearing in the report must be traceable to source,
  configuration, test, or verification evidence.
- Negative results and failed experiments shall not be hidden.
- A gate shall never be marked `PASS` without evidence.

---

## 9. Gate Status Vocabulary

Only the following status values are permitted:

- `PENDING`
- `ACTIVE`
- `PASS`
- `HOLD`
- `FAIL`

`PENDING` is a valid engineering state and is preferable to an unsupported
claim.

---

## 10. Public Portfolio Intent

The repository is currently private.

The engineering architecture shall nevertheless be developed so that a
future public release can present:

- technically defensible signal-processing work;
- FPGA/SoC hardware-software co-design;
- numerical verification;
- timing/resource trade studies;
- reproducible evidence;
- documented failure analysis;
- engineering decision rationale.

Course-specific provenance may remain in internal documentation while the
repository identity remains technically general.
