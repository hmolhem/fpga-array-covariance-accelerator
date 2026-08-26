# SP-G0 — Canonical Signal and Array Convention Decisions

**Related Gate:** SP-G0
**Decision Status:** ACCEPTED FOR SP-G0 CLOSURE AUDIT

## 1. Purpose

This record freezes the project-specific decisions required to resolve the
remaining physical and reproducibility ambiguities identified during the
SP-G0 source audit.

These decisions do not rewrite predecessor history.

They distinguish:

- predecessor source evidence;
- mathematical consistency;
- cross-project precedent;
- new authoritative decisions for this repository.

---

## 2. Canonical Baseline Geometry

The baseline array is an eight-element Uniform Linear Array.

Sensor indexing is

    n = 0, 1, ..., 7.

The wavelength-normalized sensor coordinate is

    xi_n = x_n / lambda = n / 2.

Therefore

    d / lambda = 0.5.

Equivalently, for any chosen reference wavelength,

    x_n = n lambda / 2.

The covariance accelerator itself does not require a physical carrier
frequency or wavelength in meters.

The wavelength-normalized geometry is sufficient for the controlled
synthetic signal model.

---

## 3. Coordinate and Angle Convention

The canonical linear-array axis is +x.

The baseline one-parameter visible sector is

    -90 deg <= theta <= +90 deg.

The angular reference is

    theta = 0 deg  -> broadside.

Positive theta rotates toward +x.

The project adopts the temporal phasor convention

    exp(-j omega t)

for the physical receive-manifold derivation.

---

## 4. Canonical Steering Vector

The canonical baseline receive steering coefficient is

    a_n(theta)
        =
        exp[-j 2 pi xi_n sin(theta)].

Using physical coordinates,

    a_n(theta)
        =
        exp[-j k (x_n - x_ref) sin(theta)].

For the baseline golden case,

    x_ref = x_0 = 0.

This first-sensor phase reference is selected because it reproduces the
verified predecessor deterministic vector exactly and provides a simple,
explicit baseline convention.

This is a project-specific phase-reference decision.

It shall not be confused with phase-reference defaults used by other
repositories.

---

## 5. Deterministic Golden Signal Case

The canonical deterministic signal case is:

    N = 8
    M = 128
    theta = +30 deg
    source amplitude = 1
    source phase = 0
    additive noise = 0

The source waveform is

    s[m] = 1 + j0

for every

    m = 0, ..., 127.

Therefore

    X[:,m] = a(+30 deg)

for every snapshot.

With

    d/lambda = 0.5,

the steering vector becomes

    a_n
        =
        exp(-j n pi/2),

which gives

    a =
    [1, -j, -1, +j, 1, -j, -1, +j]^T.

This exactly matches the predecessor HLS deterministic test vector.

---

## 6. Analytic Golden Covariance Consequence

Because all deterministic snapshots are identical,

    X = a 1_M^T,

and because

    |s[m]| = 1,

the operational SCM is

    R_hat
        =
        (1/M) X X^H
        =
        a a^H.

Therefore

    R_hat[i,j]
        =
        exp[-j pi (i-j)/2].

The diagonal entries are exactly

    R_hat[i,i] = 1.

The matrix is Hermitian:

    R_hat = R_hat^H.

This analytic result provides an independent consistency target for later
SP-G1 and SP-G2 verification.

---

## 7. Mean Treatment

The canonical SCM operation is

    R_hat = (1/M) X X^H.

No sample-mean subtraction is performed.

Therefore the implementation computes the uncentered array-processing SCM
(second-moment estimate).

A mean-centered statistical covariance estimator is not the baseline
operation.

---

## 8. Historical Steering-Sign Conflict

Historical project code exists using a positive spatial exponential of the
form

    exp(+j k x sin(theta)).

That convention is not declared mathematically incorrect in isolation,
because steering-vector sign depends on the complete temporal-phasor,
direction, and receive-model convention.

However, it is not the canonical convention for this repository.

The authoritative project convention is

    exp(-j k x sin(theta)).

Historical positive-sign software shall not be connected to this project
without an explicit convention-adaptation and verification step.

---

## 9. Logical Snapshot Matrix

The canonical logical sample is

    X[n,m],

where

    n = sensor/channel index
    m = snapshot index.

Thus

    X in C^(8 x 128),

with sensors as rows and snapshots as columns.

This is a signal-processing contract.

Physical AXI serialization is not defined here.

---

## 10. Regression Randomness Policy

Randomized regression shall use NumPy's explicit Generator API.

The canonical policy is:

    Bit generator: PCG64
    Root seed: 0

Implementations shall not depend on implicit global NumPy random state.

Where multiple independent randomized cases are required, deterministic
substreams shall be derived from the root seed using SeedSequence-based
spawning.

The exact seed, generator identity, case parameters, and generated-vector
provenance shall be recorded in the verification manifest.

Once a regression vector is accepted as evidence, its exact values rather
than only its seed become the authoritative regression artifact.

The numerical value of the root seed has no scientific significance; it is
only a stable reproducibility constant.

---

## 11. Interface Boundary

SP-G0 defines logical complex samples only.

The following belong to INT-G0:

- real/imaginary packing order;
- AXI word width;
- byte order;
- sign extension;
- stream traversal order;
- DMA framing;
- TLAST behavior.

No transport-format convention is implied by the logical X[n,m] definition.

---

## 12. Resolution of SP-G0 Findings

### SP0-F01 — ULA spacing

RESOLVED.

Canonical project decision:

    d/lambda = 0.5
    xi_n = n/2.

### SP0-F02 — Physical steering convention

RESOLVED.

Canonical project decision:

    +x array axis
    broadside theta = 0
    positive theta toward +x
    sin(theta) spatial projection
    first-sensor phase reference for the baseline case.

### SP0-F03 — Steering-sign conflict

RESOLVED.

Canonical project decision:

    a_n(theta)
        =
        exp[-j 2 pi xi_n sin(theta)].

Historical positive-sign implementations require explicit adaptation before
use.

### SP0-F04 — Random regression policy

RESOLVED.

Canonical project decision:

    NumPy Generator / PCG64
    root seed = 0
    deterministic substream derivation
    explicit regression manifests.

---

## 13. Engineering Consequence

After these decisions are accepted by SP-G0, SP-G1 shall not choose a new
geometry, steering sign, angle reference, phase reference, mean-treatment
rule, or random-generation policy.

SP-G1 shall implement and verify the frozen contract.

Any later change requires an explicit controlled decision rather than a
silent implementation change.
