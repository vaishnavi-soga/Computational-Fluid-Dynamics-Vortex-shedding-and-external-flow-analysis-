# 📊 Results Summary — MAE 560 Project 3

All numerical results from all four tasks in one place.

---

## Task 1 — Cylinder Vortex Shedding (Re = 608)

### Simulation Parameters
| μ | ρ | U∞ | D | Re | Δt | Steps |
|---|---|---|---|---|---|---|
| 0.0024 kg/m·s | 730 kg/m³ | 0.05 m/s | 0.04 m | **608.33** | 0.03 s | 2000 |

### Lift Force Results
| Shape | F_max (N) | F_min (N) | Amplitude (N) | Period (s) | Strouhal St |
|---|---|---|---|---|---|
| Circular cylinder | +0.0400 | −0.0410 | **0.0405** | 3.60 | 0.222 |
| Ellipse Y-axis (bluffer) | +0.0750 | −0.0780 | **0.0765** | 3.92 | 0.204 |
| Ellipse X-axis (streamlined) | +0.0148 | −0.0150 | **0.0149** | 3.37 | 0.237 |

---

## Task 2 — Flying Saucer (3D Steady)

| Tilt θ | Lift (N) | Drag (N) | L/D Ratio |
|---|---|---|---|
| 0° | 3.4225364 | 2.720025 | 1.26 |
| **25°** | **37.75393** | **12.727015** | **2.97** ← optimal |
| 50° | 25.136353 | 40.59115 | 0.62 |

---

## Task 3 — Building Wind Loads (3D Steady)

| | Total Drag (N) | Pressure Drag (N) | Viscous Drag (N) | Viscous % |
|---|---|---|---|---|
| Run 1 (+y flow) | 3.3536802 | 3.537944 | 0.028886 | 0.86% |
| Run 2 (−y flow) | −4.740668 | −4.724006 | −0.016759 | 0.35% |

> Run 2 negative sign = force acts in −y direction (consistent with reversed flow).

---

## Task 4 — Asymmetric Cylinder (MAE 560, 120 s)

| F_max (N) | F_min (N) | Amplitude (N) | Period (s) |
|---|---|---|---|
| +0.0605 | −0.0743 | **0.0674** | 4.04 |

**vs. Circular:** +66.4% higher amplitude — geometric asymmetry intensifies vortex shedding.
