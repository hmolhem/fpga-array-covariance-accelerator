# Handoff Index

## Purpose

This directory contains project continuation records for the
FPGA Array Covariance Accelerator project.

Handoffs preserve the state required to continue engineering work across
development sessions.

They summarize project state but do not redefine the authoritative project
contracts.

---

## Authoritative Project Documents

The primary project state is governed by:

- `../PROJECT_CHARTER.md`
- `../GATE_REGISTRY.md`
- `../REPOSITORY_STRUCTURE.md`

The Gate Registry is the authoritative source for gate identity and gate status.

---

## Active Handoffs

### `MASTER_PROJECT_ARCHITECTURE_HANDOFF.md`

Purpose:

Preserves the master A-to-Z engineering architecture of the project, including:

- controlled project scope;
- hardware/software partition;
- gate hierarchy;
- verification hierarchy;
- evidence model;
- numerical reference hierarchy;
- report architecture;
- engineering interpretation requirements;
- pull-request discipline;
- permitted continuation sequence.

Current continuation sequence:

    Complete final review of PR #3
        |
        v
    Merge SP-G0 Signal-Processing Contract Closure
        |
        v
    Verify clean main / origin-main synchronization
        |
        v
    Delete the completed SP-G0 branch when appropriate
        |
        v
    Create focused SP-G1 branch
        |
        v
    Begin SP-G1 — Array Signal and Snapshot Model

---

## Handoff Rule

When project state changes:

1. Update the authoritative engineering document first.
2. Update gate status in `GATE_REGISTRY.md` when applicable.
3. Record supporting evidence in the appropriate repository location.
4. Update or create a handoff only after the authoritative state is established.

A handoff shall never silently override the Project Charter, Gate Registry,
repository architecture, or detailed gate evidence.

---

## Current Handoff Set

| Handoff | Purpose | Status |
|---|---|---|
| `MASTER_PROJECT_ARCHITECTURE_HANDOFF.md` | Master project continuation architecture | ACTIVE |

Additional handoffs shall be added to this index as the project progresses.
