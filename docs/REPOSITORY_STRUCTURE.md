# Repository Structure

## 1. Purpose

This document defines the authoritative repository organization for the
**FPGA Array Covariance Accelerator** project.

The repository architecture is designed to preserve a strict separation among:

- signal-processing and numerical reference models;
- fixed-point numerical design;
- HLS and FPGA implementation;
- PYNQ / PS-PL integration;
- executable tests;
- verification evidence;
- reviewed engineering results;
- project documentation;
- final report artifacts.

The repository shall support traceability from an engineering claim back to
the configuration, test, implementation, and Git revision that produced it.

---

## 2. Repository Design Principles

The repository follows this engineering chain:

    Requirements
        |
        v
    Signal-Processing Theory
        |
        v
    Floating-Point Reference
        |
        v
    Fixed-Point Numerical Model
        |
        v
    HLS / RTL Implementation
        |
        v
    Vivado / PYNQ Integration
        |
        v
    Verification Evidence
        |
        v
    Engineering Results
        |
        v
    Engineering Interpretation
        |
        v
    Final Report / Release

The following rules apply:

1. Source code and generated tool artifacts are not treated as equivalent.
2. Verification logic and verification evidence are stored separately.
3. Results used in the engineering report must be traceable.
4. Tool-generated Vivado and Vitis build trees are not committed as source.
5. Experimental assumptions should be configuration-controlled whenever practical.
6. Gate-closure evidence must have a defined repository location.
7. The report must consume verified evidence rather than become an independent numerical source of truth.
8. A gate shall never be marked `PASS` without traceable acceptance evidence.
9. Negative or unexpected engineering results shall not be silently removed.
10. Repository structure changes affecting project traceability shall be made through focused pull requests.

---

## 3. Top-Level Repository Structure

    fpga-array-covariance-accelerator/
    |
    |-- .github/
    |   `-- pull_request_template.md
    |
    |-- configs/
    |
    |-- docs/
    |   |-- architecture/
    |   |-- gates/
    |   |-- handoffs/
    |   |-- decisions/
    |   |-- PROJECT_CHARTER.md
    |   |-- GATE_REGISTRY.md
    |   `-- REPOSITORY_STRUCTURE.md
    |
    |-- software/
    |   |-- floating_point/
    |   `-- fixed_point/
    |
    |-- hls/
    |   |-- src/
    |   |-- tb/
    |   `-- scripts/
    |
    |-- pynq/
    |   |-- notebooks/
    |   `-- scripts/
    |
    |-- tests/
    |   |-- unit/
    |   |-- regression/
    |   `-- integration/
    |
    |-- verification/
    |   |-- vectors/
    |   |-- manifests/
    |   `-- records/
    |
    |-- results/
    |   |-- tables/
    |   `-- figures/
    |
    |-- report/
    |   |-- latex/
    |   |-- figures/
    |   `-- generated/
    |
    |-- scripts/
    |
    |-- README.md
    `-- .gitignore

---

## 4. `.github/`

### Purpose

The `.github/` directory contains repository workflow and engineering-governance assets.

Initial content:

    .github/
    `-- pull_request_template.md

The pull-request template shall require:

- explicit engineering purpose;
- included and excluded scope;
- gate relationship;
- verification performed;
- evidence produced;
- engineering interpretation;
- reproducibility checks;
- documentation updates;
- gate decision where applicable.

Future GitHub Actions workflows may be added only when they provide meaningful
verification or reproducibility value.

---

## 5. `configs/`

### Purpose

The `configs/` directory contains authoritative machine-readable configurations.

Potential future content includes:

    configs/
    |-- baseline_frame.*
    |-- signal_model.*
    |-- fixed_point_candidates.*
    |-- regression_cases.*
    `-- board_configuration.*

Configuration files may record parameters such as:

- sensor count;
- snapshot count;
- ULA assumptions;
- steering-vector conventions;
- deterministic test-case parameters;
- random seeds;
- fixed-point formats;
- rounding modes;
- saturation modes;
- accumulator widths;
- interface word widths;
- HLS settings;
- board-test settings.

Important engineering assumptions should not remain silently embedded in scripts.

---

## 6. `docs/`

### Purpose

The `docs/` directory contains project governance, architecture, gate documentation,
handoffs, and engineering decisions.

    docs/
    |-- architecture/
    |-- gates/
    |-- handoffs/
    |-- decisions/
    |-- PROJECT_CHARTER.md
    |-- GATE_REGISTRY.md
    `-- REPOSITORY_STRUCTURE.md

### 6.1 `docs/architecture/`

Contains system and subsystem architecture documents.

Potential future documents:

    docs/architecture/
    |-- system_architecture.md
    |-- signal_processing_architecture.md
    |-- hls_architecture.md
    `-- pynq_integration_architecture.md

Architecture documents define responsibilities, boundaries, interfaces, and data flow.
They are not substitutes for verification evidence.

### 6.2 `docs/gates/`

Contains detailed engineering gate records.

Every gate record shall use the following structure:

1. Purpose
2. Engineering / Scientific Question
3. Inputs and Frozen Assumptions
4. Theory or Design Basis
5. Implementation / Evaluation Method
6. Verification and Acceptance Criteria
7. Evidence Produced
8. Engineering Interpretation
9. Gate Decision

Example future files:

    docs/gates/
    |-- SYS_G1_ARCHITECTURE_EVIDENCE_CONTRACT.md
    |-- SP_G0_SIGNAL_PROCESSING_CONTRACT.md
    |-- SP_G1_ARRAY_SIGNAL_MODEL.md
    |-- FPGA_G2_HLS_CSIM.md
    `-- INT_G4_BOARD_VS_GOLDEN.md

A gate document may exist while its status remains `PENDING`, `ACTIVE`, `HOLD`,
or `FAIL`.

### 6.3 `docs/handoffs/`

Contains continuation records for transferring project state across engineering sessions.

Handoffs may preserve:

- repository state;
- current branch or merged PR;
- completed gates;
- active gates;
- frozen decisions;
- known blockers;
- evidence already produced;
- next permitted work.

Expected structure:

    docs/handoffs/
    |-- HANDOFF_INDEX.md
    `-- MASTER_PROJECT_ARCHITECTURE_HANDOFF.md

A handoff is not the authoritative gate-status registry.
Gate status remains governed by `docs/GATE_REGISTRY.md`.

### 6.4 `docs/decisions/`

Contains explicit engineering decision records.

This directory is used when multiple defensible alternatives exist and the selected
choice requires traceable rationale.

Possible future examples:

    docs/decisions/
    |-- fixed_point_format_selection.md
    |-- hls_architecture_selection.md
    |-- axi_word_format_selection.md
    `-- achieved_clock_decision.md

An engineering decision record should identify:

- alternatives considered;
- evidence used;
- trade-offs;
- selected option;
- consequences;
- remaining limitations.

---

## 7. `software/`

### Purpose

The `software/` directory contains numerical reference implementations.

    software/
    |-- floating_point/
    `-- fixed_point/

These two layers must remain conceptually distinct.

### 7.1 `software/floating_point/`

Contains the authoritative floating-point signal-processing reference.

Expected responsibilities may include:

- deterministic snapshot generation;
- array steering-vector model;
- floating-point SCM computation;
- covariance structural checks;
- software DOA evaluation;
- generation of approved golden outputs.

Conceptual processing chain:

    X
    |
    v
    Floating-Point SCM
    |
    v
    R_float
    |
    v
    Software DOA

This layer represents the approved algorithmic numerical reference.

### 7.2 `software/fixed_point/`

Contains the independent bit-oriented fixed-point numerical reference.

Expected responsibilities may include:

- explicit input quantization;
- fixed-point scaling;
- rounding;
- saturation;
- overflow detection;
- product-width modeling;
- accumulator-width modeling;
- fixed-point SCM computation;
- comparison against floating-point output.

Conceptual processing chain:

    X_float
        |
        v
    Quantization
        |
        v
    X_fixed
        |
        v
    Bit-Accurate SCM
        |
        v
    R_fixed

This layer is critical because it separates expected finite-word-length effects from
HLS, RTL, AXI, DMA, packing, or board-integration errors.

---

## 8. `hls/`

### Purpose

The `hls/` directory contains synthesizable FPGA kernel source, testbench code,
and reproducible HLS execution scripts.

    hls/
    |-- src/
    |-- tb/
    `-- scripts/

### 8.1 `hls/src/`

Contains synthesizable C/C++ source.

The baseline hardware responsibility is:

**Sample Covariance Matrix formation**

Generated Vitis HLS solution directories shall not be committed here.

### 8.2 `hls/tb/`

Contains HLS testbench code and adapters for approved verification vectors.

Preferred verification relationship:

    Approved Verification Vector
              |
              +----------------------+
              |                      |
              v                      v
    Bit-Accurate Reference       HLS Kernel
              |                      |
              +------ compare -------+

### 8.3 `hls/scripts/`

Contains reproducible HLS automation where practical.

Potential responsibilities:

- create HLS project;
- execute C simulation;
- execute synthesis;
- execute C/RTL co-simulation;
- export selected reports;
- capture tool settings.

Generated HLS project and solution directories remain ignored by Git.

---

## 9. `pynq/`

### Purpose

The `pynq/` directory contains processor-side PYNQ integration code.

    pynq/
    |-- notebooks/
    `-- scripts/

### 9.1 `pynq/notebooks/`

Contains reviewed Jupyter notebooks used for board interaction and demonstration.

Potential responsibilities:

- overlay loading;
- contiguous-buffer allocation;
- complex-sample packing;
- DMA transaction;
- covariance retrieval;
- unpacking;
- board-versus-golden comparison;
- downstream software DOA execution;
- host-observed timing.

Critical reusable logic should not exist only inside notebooks when a reusable module
is more appropriate.

### 9.2 `pynq/scripts/`

Contains reusable Python scripts or modules for PYNQ integration.

Possible responsibilities:

- data packing;
- data unpacking;
- DMA helpers;
- board-test automation;
- timing utilities;
- output comparison.

---

## 10. `tests/`

### Purpose

The `tests/` directory contains executable verification logic.

    tests/
    |-- unit/
    |-- regression/
    `-- integration/

### 10.1 `tests/unit/`

Tests individual mathematical or software components.

Possible examples:

- steering-vector convention;
- conjugation behavior;
- SCM element calculation;
- Hermitian structure;
- fixed-point quantization;
- packing and unpacking utilities.

### 10.2 `tests/regression/`

Protects previously accepted behavior from silent change.

Potential regression assets:

- deterministic SCM result;
- approved fixed-point result;
- frozen configuration;
- approved downstream DOA result.

Regression tests should preferentially consume controlled verification vectors.

### 10.3 `tests/integration/`

Tests behavior across engineering boundaries.

Examples:

    Floating-Point Reference
              vs
    Fixed-Point Reference

    Fixed-Point Reference
              vs
    HLS C Simulation

    HLS / Fixed-Point Expected Output
              vs
    Board-Returned Covariance

Integration tests help localize failures to the correct engineering layer.

---

## 11. `verification/`

### Purpose

The `verification/` directory stores traceable engineering evidence and metadata.

    verification/
    |-- vectors/
    |-- manifests/
    `-- records/

Verification evidence is not interchangeable with implementation source.

### 11.1 `verification/vectors/`

Contains approved deterministic or regression test vectors.

Potential future organization:

    verification/vectors/
    |-- sp_g1_single_source/
    |-- scm_reference_case/
    `-- fixed_point_regression/

A vector set should preserve sufficient metadata to determine:

- dimensions;
- ordering;
- complex convention;
- generating configuration;
- expected outputs.

### 11.2 `verification/manifests/`

Contains metadata associated with controlled engineering runs.

A manifest may record:

- Test ID
- Gate ID
- Git commit
- Configuration
- Tool version
- Random seed
- FPGA target
- Requested clock
- Numerical format
- Evidence path
- Date / timestamp where useful

The purpose is reproducibility and evidence integrity.

### 11.3 `verification/records/`

Contains reviewed verification records.

Possible future files:

    verification/records/
    |-- V01_FLOAT_SCM.md
    |-- V02_FIXED_POINT_ERROR.md
    |-- V04_HLS_COSIM.md
    `-- V06_BOARD_COMPARISON.md

A verification record should identify:

- what was tested;
- test configuration;
- reference used;
- implementation under test;
- metric;
- observed result;
- acceptance decision;
- engineering interpretation.

---

## 12. `results/`

### Purpose

The `results/` directory contains reviewed outputs suitable for engineering analysis
and final reporting.

    results/
    |-- tables/
    `-- figures/

### 12.1 `results/tables/`

Potential reviewed tables include:

- fixed-point candidate comparison;
- covariance-error metrics;
- HLS latency and II;
- FPGA resource utilization;
- timing results;
- board-versus-golden comparison;
- DOA consistency comparison.

Machine-readable CSV data should be retained where appropriate.

### 12.2 `results/figures/`

Contains reviewed figures generated from traceable evidence.

Possible examples:

- covariance numerical error;
- fixed-point trade study;
- latency/resource comparison;
- MUSIC spectrum comparison;
- timing/resource summary.

A final report figure should ideally have:

- identifiable source data;
- generating script;
- associated configuration;
- corresponding verification record.

---

## 13. `report/`

### Purpose

The `report/` directory contains the authoritative engineering report source and
controlled report outputs.

    report/
    |-- latex/
    |-- figures/
    `-- generated/

### 13.1 `report/latex/`

Contains authoritative LaTeX source.

The planned report architecture is:

1. Project Definition and Engineering Requirements
2. System Architecture and Verification Strategy
3. Signal Processing and Numerical Foundation
4. Fixed-Point Numerical Design
5. HLS and FPGA Architecture
6. PYNQ-Z2 PS-PL Integration
7. Verification, Experimental Results, and Evidence
8. Engineering Interpretation and Trade Study
9. Conclusions and Future Work

The report shall clearly distinguish:

- planned work;
- implemented work;
- verified results;
- pending results;
- engineering interpretation;
- limitations.

### 13.2 `report/figures/`

Contains report-specific figures when separation from general project-result figures is
useful.

The report figure itself shall not become an independent source of numerical truth.

### 13.3 `report/generated/`

Contains generated report outputs such as compiled PDF files.

Routine generated output is ignored by Git by default.

A controlled final release PDF may later be preserved through an explicit release
decision.

---

## 14. `scripts/`

### Purpose

The `scripts/` directory contains project-level automation that does not naturally
belong to a more specific subsystem.

Possible future examples:

    scripts/
    |-- check_environment.*
    |-- run_regression.*
    |-- build_report.*
    `-- package_evidence.*

Automation should reduce manual ambiguity and improve reproducibility.

---

## 15. Source, Evidence, and Result Separation

Three concepts must remain distinct.

### Source

Defines what should execute.

Examples:

- `software/`
- `hls/src/`
- `pynq/scripts/`
- `configs/`

### Verification Evidence

Demonstrates what actually occurred during a controlled test.

Examples:

- `verification/vectors/`
- `verification/manifests/`
- `verification/records/`

### Reviewed Result

Distills verified evidence into an engineering comparison.

Examples:

- `results/tables/`
- `results/figures/`

The relationship is:

    Source
       |
       v
    Execution / Test
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

---

## 16. Traceability Model

A major engineering claim should ideally be traceable through:

    Report Claim
        |
        v
    Table / Figure / Engineering Statement
        |
        v
    Verification Record
        |
        v
    Test ID
        |
        v
    Configuration + Verification Vector
        |
        v
    Source Implementation
        |
        v
    Git Commit

If this trace cannot be established, the claim is not yet considered
portfolio-grade engineering evidence.

---

## 17. Generated Artifact Policy

Routine generated artifacts shall not be committed.

Examples include:

- Vivado build trees;
- Vitis HLS solution directories;
- `.Xil` directories;
- simulator caches;
- temporary synthesis products;
- temporary logs;
- waveform databases;
- LaTeX auxiliary files;
- Jupyter checkpoints.

Final artifacts such as:

- bitstreams;
- hardware handoff files;
- final PDFs;
- archived verification packages;

may be retained only through an explicit engineering or release decision.

The repository shall not become uncontrolled build storage.

---

## 18. Repository Evolution Rule

The repository structure may evolve when implementation evidence demonstrates a
real engineering need.

However, structural changes affecting:

- ownership of engineering truth;
- verification locations;
- gate traceability;
- report architecture;
- hardware/software partition;
- evidence interpretation;

shall be introduced through focused pull requests and, where appropriate,
documented engineering decisions.

Silent structural drift is not permitted.

---

## 19. Final Repository Principle

This repository is not intended to be merely a collection of source code.

It is intended to preserve the complete engineering chain:

    Problem
       |
       v
    Requirement
       |
       v
    Theory
       |
       v
    Numerical Reference
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
    Result
       |
       v
    Engineering Interpretation
       |
       v
    Decision
       |
       v
    Reproducible Release

That chain defines the organizational standard for this project.
