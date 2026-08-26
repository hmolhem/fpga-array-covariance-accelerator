# Vivado Implementation Source

## Purpose

This directory owns the reproducible source-like assets required to construct
and implement the FPGA design in Vivado.

The directory stores controlled implementation inputs.

It does not store routine generated Vivado project output.

---

## Repository Ownership

The controlled structure is:

    vivado/
    |-- scripts/
    |-- constraints/
    `-- README.md

---

## `vivado/scripts/`

Contains reproducible Vivado Tcl automation.

Expected future responsibilities may include:

- project creation;
- target-device configuration;
- IP instantiation;
- block-design construction;
- AXI interconnect creation;
- AXI DMA integration;
- clock and reset configuration;
- HLS IP integration;
- synthesis launch;
- implementation launch;
- bitstream generation;
- selected report export.

The preferred principle is:

    Reproducible Tcl Source
        |
        v
    Generated Vivado Project

rather than treating a manually created local `.xpr` project as the only
source of implementation truth.

---

## `vivado/constraints/`

Contains controlled implementation constraints.

Expected future content may include:

- XDC timing constraints;
- clock definitions;
- interface constraints;
- board-specific constraints where justified.

Constraints affecting timing conclusions shall be version controlled.

---

## Generated Artifact Policy

Routine generated Vivado artifacts shall not be committed.

Examples include:

- `.Xil/`
- `*.xpr`
- `*.runs/`
- `*.cache/`
- `*.gen/`
- `*.sim/`
- `*.hw/`
- `*.ip_user_files/`
- temporary logs;
- implementation databases;
- intermediate checkpoints.

Generated bitstreams and hardware handoff files are also excluded by default.

A final approved hardware artifact may later be preserved only through an
explicit release or evidence decision.

---

## Evidence Boundary

Vivado source and Vivado evidence are distinct.

Controlled source belongs here:

    vivado/

Reviewed implementation evidence belongs under:

    verification/records/

Reviewed result summaries belong under:

    results/

Selected report evidence may be referenced from:

    report/
    Appendix D

For example:

    vivado/constraints/
        ->
    Vivado implementation
        ->
    timing/utilization report
        ->
    verification record
        ->
    reviewed result
        ->
    engineering interpretation

---

## Timing Provenance

Vivado implemented timing is not interchangeable with:

- HLS estimated clock period;
- HLS cycle latency;
- software runtime;
- host-observed DMA-plus-accelerator time.

Vivado timing evidence shall identify the implemented design and the
constraints under which the result was obtained.

---

## Current Status

This directory currently defines implementation-source ownership only.

No Vivado implementation result is claimed by the existence of this
directory.
