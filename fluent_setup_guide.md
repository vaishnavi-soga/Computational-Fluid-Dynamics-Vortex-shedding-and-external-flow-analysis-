# ⚙️ ANSYS Fluent Setup Guide — MAE 560 Project 3

Step-by-step guide to reproduce all four tasks from scratch in ANSYS Fluent.

---

## General Workflow (All Tasks)

```
Geometry (SpaceClaim) → Mesh (ANSYS Meshing) → Fluent Setup → Run → Post-Process
```

---

## Task 1 — 2D Cylinder Vortex Shedding

### Mesh Settings
| Setting | Value |
|---|---|
| Global element size | 0.004 m |
| Refinement near cylinder | ~0.001 m |
| Type | Structured quad (far field) + tri near body |

### Fluent — General
| Setting | Value |
|---|---|
| Solver | Pressure-based, **Transient** |
| Viscous model | **Laminar** (Re = 608) |

### Fluid Properties
| Property | Value |
|---|---|
| Density ρ | 730 kg/m³ |
| Viscosity μ | 0.0024 kg/(m·s) |

### Boundary Conditions
| Boundary | Type | Value |
|---|---|---|
| Left edge | Velocity Inlet | U = 0.05 m/s |
| Right edge | Pressure Outlet | 0 Pa gauge |
| Cylinder surface | Wall | No-slip |
| Top/Bottom | Symmetry | — |

### Run Controls
| Setting | Value |
|---|---|
| Time step Δt | **0.03 s** |
| Max iterations/step | **20** |
| Total steps | **2000** (= 60 s) |

### Monitors to Create
- Lift force on cylinder: direction (0,1,0)
- Drag force on cylinder: direction (1,0,0)
- Write to file every time step

> **Tip:** Ignore first ~30 s (spin-up). Measure amplitude and period from t = 30–60 s only.

### CFL Check
$$CFL = \frac{U_\infty \cdot \Delta t}{\Delta x} = \frac{0.05 \times 0.03}{0.004} = 0.375 \quad \checkmark$$

---

## Task 2 — 3D Flying Saucer (Steady)

### Key Differences from Task 1
- 3D unstructured tetrahedral mesh with surface refinement
- **Steady-state** solver (no time dependence)
- Run three separate cases: θ = 0°, 25°, 50°

### Force Reports
`Results → Reports → Forces`
- Direction (1,0,0) = drag, (0,1,0) = lift
- Record: Total AND pressure + viscous individually

---

## Task 3 — Building Wind Loads (Steady 3D)

### Key Setting: Analysis Plane
Create horizontal slice at z = 1.25 m:
`Results → Surface → Plane → Normal: z, Offset: 1.25`

### Run 1 vs Run 2
Simply **swap inlet and outlet** boundary assignments — same mesh, reversed flow direction.

### Force Sign Convention
Run 2 gives negative drag — this means force acts in **−y direction** (same as flow), not a simulation error.

---

## Task 4 — Asymmetric Cylinder (MAE 560)

### Key Differences from Task 1
- Custom geometry fitting within 4 cm × 4 cm box
- Extend simulation to **120 seconds** (4000 steps) — asymmetric shapes need longer spin-up
- Use t > 60 s data for amplitude measurement

---

## Common Issues

| Problem | Fix |
|---|---|
| Residuals not converging | Reduce Δt to 0.01 s |
| Lift stays at zero | Refine mesh near cylinder |
| Oscillations never start | Extend outlet domain, or add tiny geometric perturbation |
| Solution blows up | CFL > 1 — reduce time step |
| Force report = 0 | Wrong wall zone selected |
