# 📐 Theory & Equations — MAE 560 Project 3: External Flow

This document covers all theoretical background, governing equations, and derived quantities used in this project. A standalone reference for understanding the physics behind the ANSYS Fluent simulations.

---

## Table of Contents

1. [Governing Equations — Navier-Stokes](#1-governing-equations)
2. [Reynolds Number](#2-reynolds-number)
3. [Strouhal Number & Vortex Shedding](#3-strouhal-number)
4. [Lift & Drag Forces](#4-lift--drag-forces)
5. [Lift Amplitude](#5-lift-amplitude)
6. [Stream Function](#6-stream-function)
7. [Vorticity](#7-vorticity)
8. [Pressure Coefficient](#8-pressure-coefficient)
9. [Lift-to-Drag Ratio](#9-lift-to-drag-ratio)
10. [Drag Decomposition](#10-drag-decomposition)
11. [All Parameter Values](#11-all-parameter-values)

---

## 1. Governing Equations

All CFD simulations solve the **incompressible Navier-Stokes equations** — conservation of mass and momentum.

### Continuity (Mass Conservation)

$$\nabla \cdot \mathbf{V} = 0$$

Fluid flowing into any control volume must equal fluid flowing out. No creation or destruction of mass.

### Momentum Conservation

$$\rho \left( \frac{\partial \mathbf{V}}{\partial t} + \mathbf{V} \cdot \nabla \mathbf{V} \right) = -\nabla p + \mu \, \nabla^2 \mathbf{V}$$

| Term | Name | Physical Meaning |
|---|---|---|
| $\rho \frac{\partial \mathbf{V}}{\partial t}$ | Unsteady | Velocity change over time at a fixed point |
| $\rho \mathbf{V} \cdot \nabla \mathbf{V}$ | Convective (inertial) | Fluid carrying its own momentum — source of nonlinearity |
| $-\nabla p$ | Pressure gradient | Pressure difference drives flow from high to low |
| $\mu \nabla^2 \mathbf{V}$ | Viscous diffusion | Friction between fluid layers — smooths velocity gradients |

In 2D Cartesian, expanded:

**x-momentum:**
$$\rho \left(\frac{\partial u}{\partial t} + u\frac{\partial u}{\partial x} + v\frac{\partial u}{\partial y}\right) = -\frac{\partial p}{\partial x} + \mu\left(\frac{\partial^2 u}{\partial x^2} + \frac{\partial^2 u}{\partial y^2}\right)$$

**y-momentum:**
$$\rho \left(\frac{\partial v}{\partial t} + u\frac{\partial v}{\partial x} + v\frac{\partial v}{\partial y}\right) = -\frac{\partial p}{\partial y} + \mu\left(\frac{\partial^2 v}{\partial x^2} + \frac{\partial^2 v}{\partial y^2}\right)$$

> **Why transient?** At Re ≈ 608, vortices shed periodically. The time-derivative term $\frac{\partial \mathbf{V}}{\partial t}$ must be retained — a steady-state solver cannot capture the oscillating lift.

---

## 2. Reynolds Number

$$\boxed{Re = \frac{\rho \, v \, D}{\mu}}$$

The ratio of **inertial forces** to **viscous forces**. The single most important parameter governing the flow regime.

| Symbol | Quantity | Value | Units |
|---|---|---|---|
| $\rho$ | Fluid density | 730 | kg/m³ |
| $v$ | Inlet velocity | 0.05 | m/s |
| $D$ | Cylinder diameter | 0.04 | m |
| $\mu$ | Dynamic viscosity | 0.0024 | kg/(m·s) |
| **Re** | **Reynolds number** | **608.33** | — |

$$Re = \frac{730 \times 0.05 \times 0.04}{0.0024} = 608.33$$

### Flow Regime Guide

| Re Range | Regime | What Happens |
|---|---|---|
| Re < 5 | Creeping (Stokes) | Symmetric, no separation, no wake |
| 5 – 40 | Steady separated | Fixed symmetric recirculation bubble behind body |
| **40 – 200** | **Laminar vortex shedding** | **Periodic Kármán vortex street ← our regime** |
| 200 – 300 | Transitional | 3D instabilities begin |
| Re > 300 | Turbulent wake | Irregular shedding, requires turbulence model |

---

## 3. Strouhal Number

$$\boxed{St = \frac{f \cdot D}{U_\infty}}$$

Dimensionless vortex shedding frequency. For a circular cylinder, **St ≈ 0.2** across Re = 40–1000.

| Symbol | Quantity |
|---|---|
| $f = 1/T$ | Shedding frequency [Hz] |
| $D$ | Cylinder diameter [m] |
| $U_\infty$ | Free-stream velocity [m/s] |

**Computed from measured period T = 3.60 s:**

$$f = \frac{1}{3.60} = 0.278 \text{ Hz} \qquad St = \frac{0.278 \times 0.04}{0.05} = \mathbf{0.222}$$

Expected theoretical value at Re = 608: **St ≈ 0.21–0.22** ✓ Excellent agreement.

### Strouhal Numbers — All Cases

| Shape | Period T (s) | f (Hz) | St |
|---|---|---|---|
| Circular cylinder | 3.60 | 0.278 | 0.222 |
| Ellipse (Y-elongated / bluffer) | 3.92 | 0.255 | 0.204 |
| Ellipse (X-elongated / streamlined) | 3.37 | 0.297 | 0.237 |
| Asymmetric cylinder | 4.04 | 0.248 | 0.198 |

---

## 4. Lift & Drag Forces

ANSYS Fluent computes forces by integrating surface stresses over the body:

### Drag (parallel to flow direction)

$$\boxed{F_{drag} = \oint_S \Big(-p\,\hat{n}_x + \tau_{wx}\Big)\, dA}$$

### Lift (perpendicular to flow direction)

$$\boxed{F_{lift} = \oint_S \Big(-p\,\hat{n}_y + \tau_{wy}\Big)\, dA}$$

Where:
- $p$ = local static pressure on surface
- $\hat{n}_{x,y}$ = components of outward unit normal vector
- $\tau_w$ = wall shear stress (viscous contribution)

**Two contributions to each force:**

$$F_{drag} = \underbrace{F_{drag,\,pressure}}_{\text{form drag}} + \underbrace{F_{drag,\,viscous}}_{\text{skin friction}}$$

> **Pressure drag:** Caused by high stagnation pressure on the front face and low (separated) pressure on the rear. Dominates for bluff bodies.

> **Viscous drag:** Wall shear stress from the no-slip condition. Scales as $\sim \mu U_\infty / D$ — small at Re ~ 600.

---

## 5. Lift Amplitude

$$\boxed{A = \frac{F_{lift,\,max} - F_{lift,\,min}}{2}}$$

Half the peak-to-peak oscillation — the amplitude of the sinusoidal lift signal.

| Shape | $F_{max}$ (N) | $F_{min}$ (N) | **Amplitude (N)** | vs. Circle |
|---|---|---|---|---|
| Circular | +0.0400 | −0.0410 | **0.0405** | baseline |
| Ellipse Y-elongated (bluffer) | +0.0750 | −0.0780 | **0.0765** | +89% |
| Ellipse X-elongated (streamlined) | +0.0148 | −0.0150 | **0.0149** | −63% |
| Asymmetric cylinder | +0.0605 | −0.0743 | **0.0674** | +66% |

**Why streamlining reduces amplitude so dramatically:**

The lift amplitude is driven by the **strength of the shed vortices**, which depends on:
1. The width of the separated shear layers (wider → stronger vortices)
2. The length of the separated region behind the body

A streamlined (X-elongated) shape has thin shear layers and minimal separation → weak vortices → low amplitude.
A bluff (Y-elongated) shape has wide separation → strong vortices → high amplitude.

---

## 6. Stream Function

For 2D incompressible flow, the stream function $\psi$ is defined by:

$$u = \frac{\partial \psi}{\partial y}, \qquad v = -\frac{\partial \psi}{\partial x}$$

This form **automatically satisfies** the continuity equation $\nabla \cdot \mathbf{V} = 0$.

**Key properties:**
- Lines of constant $\psi$ = **streamlines** (paths of fluid particles)
- $\Delta \psi$ between two streamlines = **volumetric flow rate** per unit depth between them
- Units in Fluent: kg/(m·s) — mass stream function = $\rho\psi$

**Reading the contour plot:**

The smooth gradient from $\psi = 0$ (bottom wall) to $\psi_{max}$ (top wall) represents uniform inlet flow. Distortions near the cylinder show fluid being forced around the obstacle. The value of $\psi$ at the cylinder surface is constant (no-slip → no flow through the body).

---

## 7. Vorticity

Vorticity measures the **local rotation rate** of fluid elements:

$$\boldsymbol{\omega} = \nabla \times \mathbf{V}$$

In 2D, only the z-component is non-zero:

$$\omega_z = \frac{\partial v}{\partial x} - \frac{\partial u}{\partial y}$$

**Vorticity magnitude (what Fluent plots):**

$$|\boldsymbol{\omega}| = |\omega_z| = \left|\frac{\partial v}{\partial x} - \frac{\partial u}{\partial y}\right| \quad [\text{s}^{-1}]$$

> ⚠️ **Vorticity ≠ Velocity magnitude.** Vorticity measures spinning rate, not speed.

**Reading the vorticity contour:**

| Color | Vorticity Value | Physical Location |
|---|---|---|
| Red / Orange | High (~10 s⁻¹) | Shear layers directly behind cylinder, vortex cores |
| Green | Medium | Transitional regions |
| Blue | Near zero | Undisturbed far-field flow |

The **Kármán vortex street** appears as alternating blobs of opposite-sign vorticity — one column of clockwise (+ω) vortices and one of counterclockwise (−ω) vortices traveling downstream.

---

## 8. Pressure Coefficient

$$\boxed{C_p = \frac{p - p_\infty}{\frac{1}{2} \rho U_\infty^2}}$$

| Value | Location | Physical Meaning |
|---|---|---|
| $C_p = +1$ | Stagnation point | All kinetic energy converts to pressure |
| $C_p = 0$ | Local velocity = free-stream | No speedup or slowdown |
| $C_p < 0$ | Accelerated region / suction | Flow faster than free-stream |
| $C_p \ll 0$ | Separation / wake | Strong suction — large drag contribution |

**For the building (Task 3):**
- Windward face: $C_p \approx +1$ (stagnation — high positive pressure pushes building backward)
- Building corners: $C_p < 0$ (flow accelerates around corners)
- Leeward face / wake: $C_p$ significantly negative (suction pulls building forward)
- Net drag = integral of $(C_p,\,front - C_p,\,rear)$ × dynamic pressure × area

---

## 9. Lift-to-Drag Ratio

$$\boxed{\frac{L}{D} = \frac{F_{lift}}{F_{drag}}}$$

Primary measure of aerodynamic efficiency — how much useful lift is generated per unit of drag penalty.

### Flying Saucer Results

| θ | Lift (N) | Drag (N) | **L/D** | Interpretation |
|---|---|---|---|---|
| 0° | 3.4225 | 2.7200 | 1.26 | Low-efficiency level flight |
| **25°** | **37.754** | **12.727** | **2.97** | **Optimal — best efficiency** |
| 50° | 25.136 | 40.591 | 0.62 | Stalled — drag-dominated |

**Physical explanation by angle:**

- **θ = 0°:** Symmetric flow, small net lift from minor asymmetry. Comparable lift and drag.
- **θ = 25°:** Upper surface sees accelerated flow → Bernoulli → low pressure → suction lift. Lower surface sees stagnation → high pressure → pushes up. Separation still manageable. **Maximum L/D.**
- **θ = 50°:** Massive separated wake on leeward face. Drag ∝ frontal area × $C_D$ — both increase sharply. Lift actually decreases because the separated region is disorganized. Equivalent to an **airfoil stall**.

---

## 10. Drag Decomposition

$$F_{drag,total} = F_{drag,pressure} + F_{drag,viscous}$$

### Building Simulation (Task 3)

| | Total (N) | Pressure (N) | Viscous (N) | Viscous % |
|---|---|---|---|---|
| Run 1 | 3.3537 | 3.5379 | 0.02889 | 0.86% |
| Run 2 | −4.7407 | −4.7240 | −0.01676 | 0.35% |

**Why is pressure drag ~99% of total?**

For bluff bodies at Re ~ 600, the boundary layer separates and creates a large low-pressure wake. Pressure drag scales as:

$$F_{drag,pressure} \sim \frac{1}{2}\rho U_\infty^2 \cdot A_{frontal}$$

Viscous drag scales as:

$$F_{drag,viscous} \sim \frac{\mu U_\infty}{D} \cdot A_{wetted} = \frac{1}{Re} \times F_{inertial}$$

At Re = 600, the viscous contribution is $\sim 1/600 \approx 0.17\%$ of the inertial scale — consistent with our ~0.5–0.9% measured values.

**Sign convention in Run 2:** The negative sign indicates the force acts in the **−y direction**, consistent with the reversed inlet direction. The force magnitude (4.74 N) is ~41% larger than Run 1 (3.35 N) — the building geometry is less streamlined from this direction, creating more drag.

---

## 11. All Parameter Values

### Tasks 1 & 4 — 2D Transient Cylinder Flow

| Parameter | Symbol | Value | Units |
|---|---|---|---|
| Fluid density | ρ | 730 | kg/m³ |
| Dynamic viscosity | μ | 0.0024 | kg/(m·s) |
| Inlet velocity | U∞ | 0.05 | m/s |
| Cylinder diameter | D | 0.04 | m |
| **Reynolds number** | **Re** | **608.33** | — |
| Mesh resolution | Δx | 0.004 | m |
| Time step size | Δt | 0.03 | s |
| Max iterations/step | — | 20 | — |
| Total time steps | — | 2000 | — |
| Simulation duration | T_sim | 60 s (Task 1), 120 s (Task 4) | s |

### Task 2 — 3D Steady Flying Saucer

| Parameter | Value |
|---|---|
| Tilt angles | θ = 0°, 25°, 50° |
| Solver | Steady-state (SIMPLE pressure-velocity coupling) |
| Mesh type | Unstructured with surface refinement |

### Task 3 — 3D Steady Building Wind Load

| Parameter | Value |
|---|---|
| Analysis plane | z = 1.25 m (building mid-height) |
| Run 1 | Wind in +y direction |
| Run 2 | Wind in −y direction (reversed) |
| Solver | Steady-state |
