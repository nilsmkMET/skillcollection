---
name: roms-ocean-eval
description: >-
  Methodology, diagnostic formulas, and verification standards for Regional Ocean Modeling System (ROMS)
  hydrodynamic simulations. Use this skill when evaluating ROMS hindcasts or forecasts, verifying CPP options
  (SSH_TIDES, UV_TIDES, PRESS_COMPENSATE), calculating non-linear tide-surge interactions, comparing models against
  tide gauge observations, performing tidal harmonic decomposition (utide), computing total tidal error vectors,
  or diagnosing Inverse Barometer (IB) under-response and boundary dynamics.
---

# ROMS Ocean Model Evaluation – Guidelines & Diagnostic Standards

This skill provides the theoretical framework, diagnostic definitions, and automated verification protocols for Regional Ocean Modeling System (ROMS) barotropic (2D) and baroclinic (3D) simulations. It encapsulates proven evaluation practices established across Nordic Seas hindcasts, storm surge modeling, and coastal tide gauge validation.

---

## 1. Core Hydrodynamic Formulations & Interaction Physics

In coastal and shelf modeling, total water elevation $\zeta(\mathbf{x}, t)$ is forced by astronomical gravitational potential and meteorological surface forcing (wind stress $\boldsymbol{\tau}_s$ and atmospheric pressure $P_a$). 

### 1.1 Non-Linear Tide-Surge Coupling
In traditional decoupled modeling, total water elevation is approximated as linear superposition:
$$\zeta_{\text{linear}}(\mathbf{x}, t) = \zeta_{\text{TO}}(\mathbf{x}, t) + \zeta_{\text{SO}}(\mathbf{x}, t)$$
where $\zeta_{\text{TO}}$ is tide-only elevation and $\zeta_{\text{SO}}$ is uncoupled surge-only elevation.

In shallow shelf waters, estuaries, and constricted fjords, quadratic bottom friction:
$$\boldsymbol{\tau}_b = \rho_0 C_d |\bar{\mathbf{u}}_{\text{tide}} + \bar{\mathbf{u}}_{\text{surge}}| (\bar{\mathbf{u}}_{\text{tide}} + \bar{\mathbf{u}}_{\text{surge}})$$
and non-linear advection $(\bar{\mathbf{u}} \cdot \nabla) \bar{\mathbf{u}}$ strongly couple the two signals.

The **non-linear tide-surge interaction term** $\zeta_{\text{int}}$ is defined as:
$$\zeta_{\text{int}}(\mathbf{x}, t) = \zeta_{\text{TW}}(\mathbf{x}, t) - \left[\zeta_{\text{TO}}(\mathbf{x}, t) + \zeta_{\text{SO}}(\mathbf{x}, t)\right] \equiv \left(\zeta_{\text{TW}} - \zeta_{\text{TO}}\right) - \zeta_{\text{SO}}$$
where $\zeta_{\text{TW}}$ is the fully coupled total water run.

### 1.2 Two Types of Storm Surge & Tides
When validating simulations against observational tide gauges ($\zeta_{\text{obs}}$) and astronomical predictions ($\zeta_{\text{pred}}$):
1. **Coupled Surge (Type 1)**: $\zeta_{\text{surge, TW}} = \zeta_{\text{TW}} - \zeta_{\text{TO}}$ (includes interaction modulation).
2. **Uncoupled Surge (Type 2)**: $\zeta_{\text{SO}}$ (pure atmospheric response without tidal dynamics).
3. **Observed Residual**: $\zeta_{\text{res, obs}} = \zeta_{\text{obs}} - \zeta_{\text{pred}}$.
4. **Pure Astronomical Tide**: $\zeta_{\text{TO}}$ vs $\zeta_{\text{pred}}$.
5. **Coupled Tidal Signal**: $\zeta_{\text{TW}} - \zeta_{\text{SO}}$ vs $\zeta_{\text{pred}}$.

---

## 2. ROMS Simulation Verification Checklist

Before performing scientific analysis, verify simulation setup integrity:

1. **Check Preprocessor CPP Options**:
   - `SSH_TIDES` and `UV_TIDES`: Must be defined if astronomical tidal elevation and tidal currents are introduced at the open boundaries. If disabled, a `totalwater` run reduces to an uncoupled atmospheric surge run ($r > 0.99$ with `surgeonly`).
   - `RAMP_TIDES`: Check if tidal ramp-up was applied during model initialization to eliminate spurious transients.
2. **Verify Vertical & Horizontal Grids**:
   - Confirm angle rotation $\theta$ (`angle` variable in ROMS grid) when rotating $u, v$ currents between grid-oriented $(\xi, \eta)$ and true geographic (East, North) coordinates.
   - Land mask (`mask_rho`): Ensure target stations are not placed in masked cells. Snap observations to the nearest wet sea cell.
3. **Lustre Storage Optimization**:
   - ROMS history and average NetCDF files stored on read-only HPC file systems (e.g. `/lustre/<archive>/`) must be read with file locking disabled:
     ```python
     import os
     os.environ["HDF5_USE_FILE_LOCKING"] = "FALSE"
     ```

---

## 3. Harmonic Constituent Decomposition Standards

Use `utide` (Python implementation of CODAG / Pawlowicz et al.) for tidal harmonic analysis:

1. **Target Constituents**:
   - Primary semi-diurnals: $M_2$ (12.42 h), $S_2$ (12.00 h), $N_2$ (12.66 h), $K_2$ (11.97 h), $2N_2$, $\mu_2$, $\nu_2$, $L_2$, $T_2$.
   - Primary diurnals: $K_1$ (23.93 h), $O_1$ (25.82 h), $P_1$ (24.07 h), $Q_1$ (26.87 h).
   - Shallow-water non-linear overtides: $M_4$ (6.21 h), $MS_4$ (6.10 h), $MN_4$ (6.27 h), $M_6$ (4.14 h).
   - For annual or multi-month records ($> 1$ year), decompose into at least 18 standard constituents.
2. **Decomposition Protocol**:
   ```python
   import utide

   coef = utide.solve(
       time_series, 
       elev_series, 
       lat=station_lat, 
       constit=['M2', 'S2', 'N2', 'K2', 'K1', 'O1', 'P1', 'Q1', 'M4', 'MS4'], 
       conf_int='linear',
       method='ols'
   )
   ```
3. **Single-Constituent Vector Error ($D_k$)**:
   - Complex plane absolute distance (Euclidean difference in phasor space):
     $$D_k = \sqrt{(A_{\text{mod}} \cos g_{\text{mod}} - A_{\text{obs}} \cos g_{\text{obs}})^2 + (A_{\text{mod}} \sin g_{\text{mod}} - A_{\text{obs}} \sin g_{\text{obs}})^2}$$
   - Cycle-averaged root-mean-square time-domain difference:
     $$D_{k, \text{RMS}} = \frac{D_k}{\sqrt{2}}$$
   - Amplitude error: $\Delta A = A_{\text{mod}} - A_{\text{obs}}$ (m).
   - Phase error: $\Delta g = g_{\text{mod}} - g_{\text{obs}}$ (degrees), accounting for circular wrapping ($[-180^\circ, 180^\circ]$).

4. **Total Tidal Error Vectors ($D_{\text{total}}$ and $D_4$)**:
   - Combined multi-constituent error across all $K$ evaluated constituents:
     $$D_{\text{total}} = \sqrt{\sum_{k=1}^K D_k^2}$$
   - Four principal constituent error metric (Foreman et al., 1993; Cummins et al., 2000):
     $$D_4 = \sqrt{D_{M_2}^2 + D_{S_2}^2 + D_{N_2}^2 + D_{K_1}^2}$$

5. **Phasor Geometry: 1D Scalar Elevation vs. 2D Current Velocity Ellipses**:
   - **Scalar Sea Surface Elevation $\zeta(t)$**: Harmonic components are 1D scalar oscillations. When represented in the complex plane $(\text{Re}, \text{Im})$, the geometric locus across phase $\phi \in [0, 2\pi)$ is strictly a **circle** of radius $r = A$. The static phasor vector points to $(A \cos g, A \sin g)$. Do not describe elevation phasors as "ellipses".
   - **Horizontal Current Velocities $(u(t), v(t))$**: In contrast, horizontal tidal currents are 2D vectors tracing true **elliptical hodographs** in physical space, defined by semi-major axis $a$, semi-minor axis $b$, inclination angle $\theta$, and phase $g$.

6. **Boundary Deficit Diagnostics**:
   - When a single constituent (e.g., $N_2$) exhibits a uniform, large amplitude deficit across the entire coastline (e.g., $-80\%$ underestimation coast-wide) while dominant constituents ($M_2, S_2$) have small vector errors ($< 3\text{ cm}$), the deficit indicates omission, attenuation, or inadequate resolution of that constituent in the open boundary forcing dataset (e.g. TPXO9), rather than local bathymetric or frictional damping.

---

## 4. Inverse Barometer (IB) Dynamics in Limited-Area Ocean Models

Regional ocean models often exhibit an empirical under-response to atmospheric pressure variations when compared to in-situ tide gauge observations.

### 4.1 Theoretical Mechanism of IB Under-Response
1. **2D Depth-Averaged Barotropic Momentum Equation**:
   $$\frac{\partial \bar{\mathbf{u}}}{\partial t} + (\bar{\mathbf{u}} \cdot \nabla)\bar{\mathbf{u}} + f\mathbf{k} \times \bar{\mathbf{u}} = -g \nabla \zeta - \frac{1}{\rho_0} \nabla P_a + \frac{\boldsymbol{\tau}_s - \boldsymbol{\tau}_b}{\rho_0 H}$$
   The barotropic ocean responds strictly to **spatial gradients** of atmospheric pressure ($\nabla P_a$).
2. **Domain-Mean Pressure Gradient Blindness**:
   $$\nabla \bar{P}_{\text{domain}}(t) \equiv \mathbf{0}$$
   Extratropical storms and synoptic pressure systems have horizontal scales ($L \sim 1{,}000\text{--}2{,}000\text{ km}$) comparable to or larger than regional model domains (e.g. $1{,}500 \times 1{,}000\text{ km}$). The uniform, domain-wide temporal pressure drop $\partial \bar{P}_{\text{domain}}/\partial t$ produces zero horizontal pressure gradient force inside the domain.
3. **Barotropic Continuity Equation**:
   $$\frac{\partial \zeta}{\partial t} = -\nabla \cdot (H \bar{\mathbf{u}})$$
   The continuity equation contains no direct atmospheric pressure tendency term. The sea surface elevation $\zeta$ can only change if barotropic volume flux $H \bar{\mathbf{u}}$ crosses the open boundaries.

### 4.2 Open Boundary Conditions & the `PRESS_COMPENSATE` CPP Option
- **Radiation Boundary Conditions**: Standard Flather radiation conditions assume external sea level $\zeta_{\text{ext}} = 0$. When internal atmospheric pressure drops, the interior ocean attempts to rise due to local pressure gradients, but the unforced boundaries hold $\zeta$ fixed, driving unphysical outward pressure gradients and artificial rim currents along the domain perimeter.
- **The `PRESS_COMPENSATE` Preprocessor Macro**:
  In ROMS, activating `PRESS_COMPENSATE` modifies the external boundary water level by subtracting the static IB anomaly:
  $$\Delta \zeta_{\text{bc}} = -\frac{P_{\text{ext}} - P_0}{\rho_0 g}$$
  This successfully neutralizes artificial boundary rim currents. However, because Flather radiation conditions radiate wave energy outward without supplying external barotropic mass flux, the model interior remains starved of the mass influx needed to establish the full static equilibrium water level.
- **Empirical Under-Response**:
  Regional ROMS setups without dynamic nesting or volume-conserving boundary flux typically produce an empirical IB response of only $\sim 30\%$ ($\beta \approx -0.30\text{ cm/hPa}$), compared to the theoretical static response ($\beta_{\text{stat}} = -0.993\text{ cm/hPa}$) and observed coastal tide gauge response ($\beta_{\text{obs}} \approx -0.85\text{ to }-0.95\text{ cm/hPa}$).

### 4.3 Diagnostic Blending Formulation
To restore the missing static mass balance in operational surge forecasts and residual evaluations:
$$\zeta_{\text{res}}^{\text{blend}} = \zeta_{\text{res}}^{\text{ROMS}} + \alpha \zeta_{\text{IB}}$$
where:
$$\zeta_{\text{IB}}(\mathbf{x}, t) = -\frac{P_{\text{msl}}(\mathbf{x}, t) - P_0}{\rho_0 g}, \quad \alpha = 1 - \frac{\beta_{\text{ROMS}}}{\beta_{\text{stat}}} \approx 0.70$$
This hybrid blending recovers the full synoptic barotropic mass balance while preserving ROMS's superior skill in capturing shallow-water wind set-up, fjord resonance, and non-linear coastal interaction dynamics.

---

## 5. Extreme Threshold & Exceedance Verification

Evaluating average RMSE across an entire year masks critical errors during extreme events. Always compute threshold-stratified metrics:

| Metric | Condition | Diagnostic Purpose |
| :--- | :--- | :--- |
| **Severe Positive Surge** | $\zeta_{\text{surge}} > +2\sigma_{\text{surge}}$ | Flood risk, coastal defense overflow, peak gale surge |
| **Moderate Positive Surge** | $\zeta_{\text{surge}} > +1\sigma_{\text{surge}}$ | Elevated coastal water level validation |
| **Severe Negative Surge** | $\zeta_{\text{surge}} < -2\sigma_{\text{surge}}$ | Navigation depth hazards, port grounding risk |
| **Peak Interaction Timing** | $|\zeta_{\text{int}}| > 0.30$ m | Phase relationship between high water and surge peaks |

---

## 6. Standard Output Deliverables

When evaluating ROMS runs:
1. Generate station comparison tables with Pearson $r$, bias, RMSE, single vector differences $D_k$, and total tidal error vectors $D_{\text{total}}$ and $D_4$.
2. Produce spatial 2D contour maps of $\max(|\zeta_{\text{int}}|)$ and RMS interaction $\sqrt{\langle \zeta_{\text{int}}^2 \rangle}$.
3. Create storm closeup time-series decomposing total water, tide, surge, and interaction.
4. Export polar/circular phasor diagrams in the complex plane for principal constituents across all stations.
5. Save all figures at 300 dpi and export summary statistics to CSV/Parquet in `data/`.
