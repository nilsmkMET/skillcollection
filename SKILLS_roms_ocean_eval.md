---
name: roms-ocean-eval
description: >-
  Methodology, diagnostic formulas, and verification standards for Regional Ocean Modeling System (ROMS)
  hydrodynamic simulations. Use this skill when evaluating ROMS hindcasts or forecasts, verifying CPP options
  (SSH_TIDES, UV_TIDES), calculating non-linear tide-surge interactions, comparing models against tide gauge observations,
  or performing tidal harmonic constituent decomposition (utide).
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
   - ROMS history and average NetCDF files stored on read-only HPC file systems (e.g. `/lustre/storeB/`) must be read with file locking disabled:
     ```python
     import os
     os.environ["HDF5_USE_FILE_LOCKING"] = "FALSE"
     ```

---

## 3. Harmonic Constituent Decomposition Standards

Use `utide` (Python implementation of CODAG / Pawlowicz et al.) for tidal harmonic analysis:

1. **Target Constituents**:
   - Primary semi-diurnals: $M_2$ (12.42 h), $S_2$ (12.00 h), $N_2$ (12.66 h).
   - Primary diurnals: $K_1$ (23.93 h), $O_1$ (25.82 h).
   - Shallow-water non-linear overtides: $M_4$ (6.21 h), $MS_4$ (6.10 h).
2. **Decomposition Protocol**:
   ```python
   import utide

   coef = utide.solve(
       time_series, 
       elev_series, 
       lat=station_lat, 
       constit=['M2', 'S2', 'N2', 'K1', 'O1'], 
       conf_int='linear',
       method='ols'
   )
   ```
3. **Validation Metrics**:
   - Amplitude error: $\Delta A = A_{\text{mod}} - A_{\text{obs}}$ (m).
   - Phase error: $\Delta g = g_{\text{mod}} - g_{\text{obs}}$ (degrees), accounting for circular wrapping ($[-180^\circ, 180^\circ]$).
   - Vector error (complex difference):
     $$D = \sqrt{\frac{1}{2} \left[ (A_{\text{mod}} \cos g_{\text{mod}} - A_{\text{obs}} \cos g_{\text{obs}})^2 + (A_{\text{mod}} \sin g_{\text{mod}} - A_{\text{obs}} \sin g_{\text{obs}})^2 \right]}$$

---

## 4. Extreme Threshold & Exceedance Verification

Evaluating average RMSE across an entire year masks critical errors during extreme events. Always compute threshold-stratified metrics:

| Metric | Condition | Diagnostic Purpose |
| :--- | :--- | :--- |
| **Severe Positive Surge** | $\zeta_{\text{surge}} > +2\sigma_{\text{surge}}$ | Flood risk, coastal defense overflow, peak gale surge |
| **Moderate Positive Surge** | $\zeta_{\text{surge}} > +1\sigma_{\text{surge}}$ | Elevated coastal water level validation |
| **Severe Negative Surge** | $\zeta_{\text{surge}} < -2\sigma_{\text{surge}}$ | Navigation depth hazards, port grounding risk |
| **Peak Interaction Timing** | $|\zeta_{\text{int}}| > 0.30$ m | Phase relationship between high water and surge peaks |

---

## 5. Standard Output Deliverables

When evaluating ROMS runs:
1. Generate station comparison tables with Pearson $r$, bias, RMSE, and vector difference $D$.
2. Produce spatial 2D contour maps of $\max(|\zeta_{\text{int}}|)$ and RMS interaction $\sqrt{\langle \zeta_{\text{int}}^2 \rangle}$.
3. Create storm closeup time-series decomposing total water, tide, surge, and interaction.
4. Save all figures at 300 dpi and export summary statistics to CSV/Parquet in `data/`.
