# SP-G0 — Signal-Processing Contract Freeze

**Gate:** SP-G0
**Status:** PASS
**Parent system gates:** SYS-G0 PASS, SYS-G1 PASS
**Downstream state:** SP-G1 remains PENDING until this SP-G0 closure is integrated into main.

---

## 1. Purpose

SP-G0 freezes the authoritative signal-processing conventions required by all
later floating-point, fixed-point, HLS/RTL, FPGA, board, and downstream DOA
work.

The purpose is not to implement a new algorithm.

The purpose is to remove ambiguity from the project before implementation
continues.

The controlled chain is:

    Signal / Array Contract
        |
        v
    Floating-Point Reference
        |
        v
    Fixed-Point Reference
        |
        v
    HLS / RTL
        |
        v
    FPGA Board
        |
        v
    Software DOA

No downstream layer may silently introduce an alternative convention.

---

## 2. Engineering / Scientific Question

What exact dimensions, array conventions, angle conventions, steering-vector
definition, covariance definition, complex-data semantics, deterministic test
case, regression policy, and logical frame organization define the canonical
input and output of this project?

SP-G1 must be able to generate the canonical array/snapshot case without
making any undocumented convention choice.

---

## 3. Inputs and Already-Frozen Assumptions

The following are inherited from the approved project scope:

- number of sensor channels:

      N = 8

- default number of snapshots:

      M = 128

- logical snapshot matrix:

      X in C^(N x M)

- snapshots are columns of X;

- sensor/channel index is the first matrix dimension;

- the FPGA baseline responsibility is sample-covariance-matrix formation;

- downstream DOA processing remains in software;

- the baseline physical geometry is a ULA;

- controlled synthetic data are used;

- at least one deterministic reference case is required;

- later regression testing shall use controlled reproducible cases.

---

## 4. Evidence Classification

Every SP-G0 conclusion shall be classified as one of the following.

### 4.1 SOURCE EVIDENCE

A convention or behavior explicitly present in the authoritative predecessor
implementation or its controlled evidence.

### 4.2 DERIVED CONSISTENCY CHECK

A mathematical consequence derived from source evidence.

A derived consistency check may demonstrate compatibility but shall not be
misrepresented as an explicit historical source convention.

### 4.3 CROSS-PROJECT PRECEDENT

A convention already formally established in another controlled project.

Cross-project precedent may inform a new decision, but it does not
automatically become authoritative for this repository.

### 4.4 NEW PROJECT DECISION

A convention not adequately defined by predecessor evidence and therefore
explicitly selected for this project.

Such a decision shall be documented rather than silently inferred.

---

## 5. Source-of-Truth Audit Inputs

### 5.1 Predecessor FPGA/HLS implementation

Repository:

    hmolhem/fpga-hls-doa-scm-accelerator

Primary source files:

    src/hls/top.h
    src/hls/top.cpp
    src/hls/main.cpp

The predecessor implementation provides direct evidence for the implemented
matrix dimensions, SCM arithmetic, deterministic test vector, and HLS
conjugation behavior.

### 5.2 GeoDOA scientific convention precedent

Repository:

    hmolhem/geodoa-studio

Relevant accepted scientific decision:

    docs/adr/ADR-001-coordinate-direction-spatial-conventions.md

This source is a CROSS-PROJECT PRECEDENT, not predecessor FPGA source
evidence.

### 5.3 Historical MIMO/DOA implementation

Repository:

    hmolhem/MIMO_GEOMETRY_ANALYSIS

Relevant files include historical steering-vector implementations using a
different complex-exponential sign convention.

These files are treated as historical comparison evidence and shall not
silently override the contract selected for this project.

---

## 6. Convention Audit Matrix

| ID | Convention | Evidence Class | Current Finding | Status |
|---|---|---|---|---|
| SP0-C01 | Sensor count and snapshot count | SOURCE EVIDENCE | N=8, M=128 | RESOLVED |
| SP0-C02 | Matrix dimensions and logical indexing | SOURCE EVIDENCE | X[i][m], sensor first dimension, snapshot second dimension | RESOLVED |
| SP0-C03 | SCM normalization | SOURCE EVIDENCE | R = (1/M) X X^H | RESOLVED |
| SP0-C04 | Complex conjugation | SOURCE EVIDENCE | R_ij uses X[i][m] * conj(X[j][m]) | RESOLVED |
| SP0-C05 | Mean treatment | SOURCE EVIDENCE | No mean subtraction is performed before SCM formation | RESOLVED |
| SP0-C06 | ULA spacing and physical coordinates | NEW PROJECT DECISION + DERIVED CONSISTENCY CHECK | Canonical d/lambda=0.5 and xi_n=n/2; predecessor deterministic vector is consistent with this choice | RESOLVED |
| SP0-C07 | Angular reference | NEW PROJECT DECISION + CROSS-PROJECT PRECEDENT | theta=0 is broadside; positive theta rotates toward +x; visible sector is [-90,+90] deg | RESOLVED |
| SP0-C08 | Steering trigonometric law | NEW PROJECT DECISION + DERIVED CONSISTENCY CHECK | Canonical spatial projection uses sin(theta) | RESOLVED |
| SP0-C09 | Steering exponential sign | NEW PROJECT DECISION | Canonical receive steering law uses exp[-j 2 pi xi_n sin(theta)]; historical positive-sign code is non-canonical unless explicitly adapted | RESOLVED |
| SP0-C10 | Deterministic reference vector | SOURCE EVIDENCE + NEW PROJECT INTERPRETATION | theta=+30 deg; vector [1,-j,-1,+j,1,-j,-1,+j]; repeated for all M=128 snapshots | RESOLVED |
| SP0-C11 | Deterministic source/noise parameterization | NEW PROJECT DECISION + SOURCE CONSISTENCY | source amplitude=1, source phase=0, additive noise=0, s[m]=1+j0 | RESOLVED |
| SP0-C12 | Regression random-seed policy | NEW PROJECT DECISION | NumPy Generator with PCG64, root seed=0, deterministic SeedSequence-derived substreams, explicit manifests | RESOLVED |
| SP0-C13 | Logical frame organization | SOURCE EVIDENCE | X[n,m] identifies sensor n and snapshot m | RESOLVED |
| SP0-C14 | Bus serialization / real-imag packing | SCOPE BOUNDARY | To be frozen by INT-G0; SP-G0 freezes logical complex samples only | DEFERRED TO INT-G0 |

---

## 7. Covariance Contract Already Supported by Source Evidence

The predecessor implementation directly supports

    R_hat[i,j]
        =
        (1/M) sum_m X[i,m] conj(X[j,m]).

Therefore,

    R_hat = (1/M) X X^H.

For

    X[i,m] = a + j b

and

    X[j,m] = c + j d,

the conjugate product is

    (a + j b)(c - j d)
        =
        (ac + bd) + j(bc - ad).

This arithmetic convention shall be preserved through floating-point,
fixed-point, HLS/RTL, and board verification.

---

## 8. Mean-Treatment Clarification

The predecessor reference does not subtract the sample mean.

Therefore the operational project definition is the uncentered array-processing
SCM

    R_hat = (1/M) X X^H.

This is mathematically an uncentered sample second-moment matrix when the
sample mean is nonzero.

The project shall not silently replace this operation with

    (1/M) X_centered X_centered^H

or with an unbiased statistical covariance estimator.

Any later change to mean treatment requires an explicit engineering decision.

---

## 9. Deterministic Predecessor Evidence

The predecessor HLS testbench explicitly defines the vector

    a_test =
    [1, -j, -1, +j, 1, -j, -1, +j]^T

and copies this same vector into all M=128 snapshot columns.

No random noise is added in this deterministic testbench.

The testbench labels the case as

    theta = 30 deg.

However, the HLS source does not independently define all physical conventions
required to derive this vector from geometry.

Consequently:

- the vector itself is SOURCE EVIDENCE;
- its theta=30 deg label is SOURCE EVIDENCE;
- a particular physical derivation of that vector is not automatically SOURCE
  EVIDENCE unless independently documented.

---

## 10. Derived Consistency Check

Consider the candidate convention

    x_n = n d,
    d = lambda / 2,
    theta = 0 at broadside,

with steering law

    a_n(theta)
        =
        exp[-j k x_n sin(theta)].

For

    theta = 30 deg

and

    k = 2 pi / lambda,

the phase becomes

    -k n d sin(theta)
        =
        -2 pi / lambda
         * n lambda/2
         * 1/2
        =
        -n pi/2.

Hence

    a_n
        =
        exp(-j n pi/2)

and therefore

    [1, -j, -1, +j, 1, -j, -1, +j]^T.

This exactly matches the predecessor deterministic vector.

This result establishes CONSISTENCY.

It does not by itself prove that the predecessor source formally defined all
of these physical conventions.

---

## 11. Historical Sign-Convention Conflict

A historical Python DOA implementation exists using

    a(theta) = exp(+j k x sin(theta)).

GeoDOA ADR-001 formally establishes a receive-manifold convention based on a
declared temporal phasor and look-direction convention that produces

    a(theta) = exp[-j k (x - x_ref) sin(theta)].

The predecessor HLS deterministic vector is also compatible with the
negative-sign form under lambda/2 spacing and first-sensor phase reference.

Therefore the existence of the historical positive-sign implementation is a
real convention conflict.

SP-G0 resolved this conflict explicitly by adopting the canonical negative-sign receive steering convention.

The historical conflict remains explicitly documented rather than being hidden or treated as though all predecessor software used one convention.

---

## 12. Logical Frame Boundary

SP-G0 freezes the logical matrix interpretation:

    X[n,m]

means:

    n = sensor/channel index
    m = snapshot index.

The exact physical serialization of these samples across AXI, including:

- byte order;
- real/imaginary field order;
- word width;
- sign extension;
- stream traversal;
- TLAST behavior;
- DMA frame representation;

belongs to INT-G0 and shall not be prematurely frozen by SP-G0.

This separation prevents signal-model semantics from being confused with
transport-format semantics.

---

## 13. Findings Closure

### SP0-F01 — ULA spacing was not explicit in predecessor HLS source

The predecessor HLS source does not explicitly define the physical ULA
spacing.

The project therefore adopts the following explicit canonical baseline:

    d/lambda = 0.5
    xi_n = n/2
    n = 0,1,...,7

This is a NEW PROJECT DECISION.

It is independently supported by a DERIVED CONSISTENCY CHECK because this
geometry, together with the adopted steering convention, reproduces the
predecessor deterministic vector exactly.

Status:

    RESOLVED

### SP0-F02 — Physical steering convention required explicit adoption

The project now freezes the canonical physical steering convention as:

    array axis = +x
    theta = 0 deg at broadside
    positive theta rotates toward +x
    visible sector = [-90 deg,+90 deg]
    spatial projection = sin(theta)
    baseline phase reference = x_ref = x_0 = 0
    temporal phasor convention = exp(-j omega t)

The canonical receive steering law is therefore

    a_n(theta)
        =
        exp[-j 2 pi xi_n sin(theta)].

Status:

    RESOLVED

### SP0-F03 — Historical steering-sign conflict existed

Historical controlled code exists using the positive-sign spatial exponential

    exp(+j k x sin(theta)).

The existence of that implementation is retained as historical evidence and
is not hidden.

For this repository, the authoritative convention is

    a_n(theta)
        =
        exp[-j 2 pi xi_n sin(theta)].

A positive-sign implementation is not declared universally incorrect because
the sign depends on the complete temporal-phasor, direction, and receive-model
convention.

However, positive-sign historical software is NON-CANONICAL for this project
unless an explicit convention-adaptation and verification step is performed.

Status:

    RESOLVED

### SP0-F04 — Random regression policy was absent

The predecessor deterministic HLS test does not provide a controlled
randomized regression policy.

The project therefore adopts:

    NumPy Generator API
    bit generator = PCG64
    root seed = 0

Independent randomized cases shall use deterministic SeedSequence-derived
substreams rather than uncontrolled global random state.

Verification evidence shall record:

    root seed
    generator identity
    test-case parameters
    vector provenance

Once a generated regression vector is accepted as controlled evidence, the
exact vector values or a reproducible manifest shall become part of the
verification record.

Status:

    RESOLVED

## 14. Acceptance Criteria

SP-G0 may reach PASS only when:

1. N, M, and logical X indexing are frozen;
2. covariance normalization and conjugation are frozen;
3. mean treatment is explicit;
4. ULA spacing and sensor coordinates are explicit;
5. angle reference and positive-angle direction are explicit;
6. steering trigonometric law is explicit;
7. steering exponential sign is explicit;
8. deterministic reference-case semantics are explicit;
9. random regression policy is explicit;
10. the SP-G0 / INT-G0 boundary is explicit;
11. historical convention conflicts are documented;
12. SP-G1 can generate its canonical test case without an undocumented choice.

---

## 15. Evidence Produced

SP-G0 closure evidence includes:

- predecessor HLS source audit;
- predecessor deterministic-vector audit;
- covariance/conjugation audit;
- mean-treatment audit;
- cross-project steering-convention comparison;
- derived deterministic-vector consistency check;
- explicit resolution of the identified contract decisions.

The required closure evidence is complete and traceably recorded in the SP-G0 gate, decision, acceptance-audit, report, registry, and handoff records.

---

## 16. Engineering Interpretation

The predecessor HLS kernel is sufficient evidence for the numerical SCM
operation but is not sufficient by itself to define every physical
array-manifold convention required by the new engineering project.

This distinction matters because an FPGA accelerator can reproduce the
covariance arithmetic exactly while the surrounding software uses a mirrored
or otherwise incompatible steering convention.

SP-G0 therefore separates:

    historical implemented behavior

from

    mathematical consistency

from

    new authoritative project decisions.

This separation is required before SP-G1 begins.

---

## 17. Gate Decision

**Current Decision: PASS**

SP-G0 is closed.

The source audit, derived consistency checks, explicit project decisions, and
pre-closure acceptance audit resolved all required signal-processing
conventions.

The authoritative SP-G0 contract now freezes:

- N = 8 sensors;
- M = 128 snapshots;
- logical X[n,m] sensor/snapshot semantics;
- uncentered SCM formation using (1/M) X X^H;
- complex conjugation behavior;
- d/lambda = 0.5 ULA geometry;
- broadside-referenced angular convention;
- sin(theta) spatial projection;
- negative-sign receive steering convention;
- deterministic theta = +30 deg golden case;
- source amplitude, phase, and zero-noise assumptions;
- controlled randomized-regression policy;
- the logical-frame versus AXI-transport responsibility boundary.

All twelve SP-G0 acceptance criteria passed.

Repository diff hygiene, report alignment, LaTeX smoke compilation, Gate
Registry synchronization, and continuation-handoff synchronization also
passed.

SP-G1 remains PENDING and becomes the next permitted signal-processing gate
after this focused SP-G0 closure is integrated into `main`.
