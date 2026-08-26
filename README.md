# FPGA Array Covariance Accelerator

Engineering implementation and verification of a fixed-point FPGA/SoC
sample covariance matrix accelerator for array processing and
direction-of-arrival estimation.

The project studies the complete hardware-algorithm co-design path from a
floating-point signal-processing reference to a numerically controlled
fixed-point FPGA implementation, followed by HLS verification, PYNQ-Z2
integration, board-level validation, and downstream DOA evaluation.

---

## Project Objective

The central engineering objective is to determine how a sample covariance
matrix front end can be implemented efficiently on FPGA hardware while
preserving sufficient numerical fidelity for downstream array-processing
and DOA applications.

The governing engineering question is:

**What FPGA implementation cost is required to preserve useful downstream
DOA fidelity for the approved array-covariance baseline?**

This requires simultaneous consideration of:

- signal-processing correctness;
- finite-snapshot behavior;
- fixed-point numerical effects;
- word length and accumulator growth;
- FPGA resource utilization;
- latency and throughput;
- timing closure;
- AXI / DMA data movement;
- board-versus-reference agreement;
- downstream DOA sensitivity.

---

## Baseline Processing Model

The controlled baseline uses:

- 8 complex sensor channels;
- 128 snapshots per default frame;
- one ULA reference configuration;
- controlled synthetic input data;
- sample covariance matrix formation

      R_hat = (1/M) X X^H

where:

- `X` is the complex snapshot matrix;
- `M` is the number of snapshots;
- `R_hat` is the estimated sample covariance matrix.

The baseline hardware/software partition is:

    Host / Processing System
        |
        | snapshot generation
        | configuration
        | packing
        | DMA control
        v
    FPGA Programmable Logic
        |
        | Sample Covariance Matrix formation
        v
    Host / Processing System
        |
        | unpacking
        | numerical verification
        | software DOA estimation
        v
    Engineering Evaluation

The baseline does not require full MUSIC eigendecomposition in the FPGA.

---

## Engineering Workflow

The project follows the engineering chain:

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
    HLS Implementation
        |
        v
    RTL Verification
        |
        v
    Vivado Implementation
        |
        v
    PYNQ-Z2 Integration
        |
        v
    Board Verification
        |
        v
    Downstream DOA Evaluation
        |
        v
    Engineering Interpretation
        |
        v
    Reproducible Release

The repository is structured around this chain rather than around isolated
demonstrations.

---

## Verification Philosophy

A result is not considered complete merely because code executes.

Engineering conclusions require:

    Requirement
    + Analysis / Implementation
    + Verification
    + Evidence
    + Engineering Interpretation
    = Gate Closure

The numerical verification hierarchy is:

    Floating-Point SCM
        <->
    Bit-Accurate Fixed-Point SCM
        <->
    HLS / RTL SCM
        <->
    Board-Returned SCM
        |
        v
    Software DOA

This hierarchy is intended to distinguish:

- finite-snapshot statistical error;
- expected fixed-point quantization error;
- HLS / RTL implementation error;
- interface or packing error;
- board-integration error.

These error sources are not treated as interchangeable.

---

## Repository Structure

The main engineering areas are:

    configs/          Controlled experiment and implementation configurations

    docs/             Project contracts, architecture, gates, handoffs,
                      and engineering decisions

    software/         Floating-point and bit-accurate fixed-point references

    hls/              Synthesizable HLS source, testbenches, and scripts

    pynq/             PYNQ host-side integration and board-control code

    tests/            Unit, regression, and integration verification

    verification/     Golden vectors, manifests, and evidence records

    results/          Reviewed engineering tables and figures

    report/           Engineering report source and report assets

    scripts/          Project-level reproducibility and automation utilities

Generated Vivado and Vitis build directories are intentionally excluded from
source control.

See:

- `docs/PROJECT_CHARTER.md`
- `docs/GATE_REGISTRY.md`
- `docs/REPOSITORY_STRUCTURE.md`
- `docs/handoffs/MASTER_PROJECT_ARCHITECTURE_HANDOFF.md`

for the governing project architecture.

---

## Engineering Gates

Development is controlled through explicit engineering gates covering:

### System

- project contract;
- architecture and evidence contract.

### Signal Processing

- signal-processing contract;
- array and snapshot model;
- floating-point SCM golden reference;
- statistical versus numerical error separation;
- fixed-point numerical requirements;
- covariance fidelity;
- downstream DOA consistency.

### FPGA / HLS

- numerical-format handoff;
- fixed-point C++ reference;
- HLS C simulation;
- synthesis and architecture characterization;
- C/RTL co-simulation;
- timing and resource closure.

### Integration

- AXI stream contract;
- packing and loopback verification;
- Vivado overlay integration;
- PS-PL-PS board transaction;
- board-versus-golden verification.

### End-to-End

- board covariance to software DOA;
- performance characterization;
- engineering interpretation;
- reproducibility and final release.

Gate state is tracked in `docs/GATE_REGISTRY.md`.

---

## Evidence Model

Major project claims should be traceable through:

    Report Claim
        |
        v
    Result / Table / Figure
        |
        v
    Verification Record
        |
        v
    Test + Configuration
        |
        v
    Source Implementation
        |
        v
    Git Revision

The project intentionally preserves both successful and unsuccessful
engineering results when they contribute to design understanding.

---

## Current Project Status

The repository is currently in the engineering-foundation phase.

Current high-level status:

- project scope and baseline architecture defined;
- repository governance being established;
- engineering gate hierarchy defined;
- verification and evidence architecture defined;
- report architecture established;
- signal-processing contract partially established;
- numerical implementation work has not yet been accepted as complete;
- FPGA implementation and board integration remain controlled future stages.

No performance, timing, resource, or numerical-fidelity result should be
interpreted as a final project result until its corresponding engineering gate
has been closed with traceable evidence.

---

## Target Platform

Primary hardware platform:

**PYNQ-Z2 / Xilinx Zynq-7000 SoC**

Expected implementation workflow includes:

- Python / NumPy reference modeling;
- bit-accurate fixed-point modeling;
- Vitis HLS;
- Vivado;
- AXI4-Stream;
- AXI DMA;
- PYNQ Python integration.

---

## Scope Boundaries

The baseline project focuses on the covariance-processing front end.

The following are intentionally outside the required baseline scope:

- full FPGA MUSIC eigendecomposition;
- new DOA algorithm development;
- sparse-array algorithm development;
- complete FMCW radar processing;
- RF or ADC hardware acquisition;
- electromagnetic antenna modeling;
- machine-learning inference;
- robotics integration.

These topics may be considered later only after the controlled baseline is
closed.

---

## Project Principles

This repository follows several engineering principles:

1. Do not hide negative results.
2. Do not mark a gate `PASS` without evidence.
3. Do not confuse simulation estimates with implemented hardware results.
4. Do not confuse fixed-point error with interface or hardware error.
5. Keep latency and throughput as separate performance quantities.
6. Record configuration and tool provenance for important results.
7. Prefer reproducible evidence over screenshots or undocumented manual runs.
8. Keep pull requests focused on coherent engineering slices.
9. Keep the engineering report synchronized with verified project state.
10. Avoid unsupported public-facing claims.

---

## Development Status

This repository is under active engineering development.

The source tree, interfaces, numerical formats, HLS architecture, and
verification assets will evolve through controlled pull requests as project
gates are completed.
