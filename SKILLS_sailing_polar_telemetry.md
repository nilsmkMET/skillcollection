---
name: sailing-polar-telemetry
description: >-
  Vectorized telemetry analysis, ORC polar file parsing, maneuver extraction, and tactical weather routing algorithms.
  Use this skill when analyzing sailing yacht log data, boat speed vs target polar performance, VMG optimization,
  tacking losses, start line geometry, or integrating with Dash-Plotly dashboards.
---

# Sailing Polar & Telemetry Processing – Protocols & Architecture

This skill provides data standards, physical bounds, polar interpolation routines, and performance metrics for competitive sailing telemetry and tactical weather routing.

---

## 1. Canonical Telemetry Channels & Physical Bounds

All data processing pipelines must adhere to standard channel naming, engineering units, and physical validity boundaries:

| Channel | Description | Canonical Unit | Valid Physical Bounds |
| :--- | :--- | :--- | :--- |
| `bsp` | Boat Speed (through water) | Knots (kts) | $0.0 \le \text{BSP} \le 35.0$ |
| `sog` | Speed Over Ground (GPS) | Knots (kts) | $0.0 \le \text{SOG} \le 35.0$ |
| `tws` | True Wind Speed | Knots (kts) | $0.0 \le \text{TWS} \le 60.0$ |
| `twa` | True Wind Angle | Degrees ($\pm 180^\circ$) | $-180.0^\circ \le \text{TWA} \le +180.0^\circ$ (neg: port, pos: stbd) |
| `awa` | Apparent Wind Angle | Degrees ($\pm 180^\circ$) | $-180.0^\circ \le \text{AWA} \le +180.0^\circ$ |
| `hdg` | Compass Heading | Degrees ($0^\circ - 360^\circ$) | $0.0^\circ \le \text{HDG} < 360.0^\circ$ |
| `cog` | Course Over Ground | Degrees ($0^\circ - 360^\circ$) | $0.0^\circ \le \text{COG} < 360.0^\circ$ |
| `twd` | True Wind Direction | Degrees ($0^\circ - 360^\circ$) | Direction wind blows FROM |
| `hel` | Heel Angle | Degrees ($\pm 90^\circ$) | $-60.0^\circ \le \text{Heel} \le +60.0^\circ$ |
| `dtl` | Distance to Start Line | Meters (m) | Signed distance (+ outside, - over early) |
| `ttg` | Time to Gun (Countdown) | Seconds (s) | Decreasing to $0$ at start gun |

---

## 2. ORC Polar Specification & Parsing

ORC (Offshore Racing Congress) performance polars define expected boat speed as a function of True Wind Speed ($V_{\text{TWS}}$) and True Wind Angle ($\theta_{\text{TWA}}$).

### 2.1 File Format (`.pol`)
Standard `.pol` files format columns as wind speeds and rows as wind angles:
```text
TWA\TWS  6.0   8.0  10.0  12.0  14.0  16.0  20.0
52.0     5.12  6.31  7.02  7.35  7.51  7.60  7.72
60.0     5.51  6.72  7.35  7.68  7.85  7.97  8.12
...
Beat_VMG 3.12  4.15  4.78  5.12  5.26  5.33  5.40
Beat_Ang 43.2  41.5  39.8  38.5  38.1  38.0  38.2
Run_VMG  3.50  4.62  5.40  6.10  6.82  7.45  8.10
Run_Ang 142.1 144.5 148.2 153.0 160.2 168.0 172.5
```

### 2.2 2D Vectorized Polar Interpolation (`SciPy`)
Never interpolate per row inside loops. Use `scipy.interpolate.RegularGridInterpolator` for vectorized lookups:

```python
import numpy as np
from scipy.interpolate import RegularGridInterpolator

class PolarTable:
    def __init__(self, tws_grid: np.ndarray, twa_grid: np.ndarray, bsp_matrix: np.ndarray):
        # bsp_matrix shape: (len(twa_grid), len(tws_grid))
        self.interp = RegularGridInterpolator(
            (twa_grid, tws_grid), 
            bsp_matrix, 
            bounds_error=False, 
            fill_value=None
        )

    def get_target_bsp(self, twa: np.ndarray, tws: np.ndarray) -> np.ndarray:
        """Vectorized target boat speed lookup."""
        abs_twa = np.abs(twa)
        pts = np.column_stack([abs_twa, tws])
        return self.interp(pts)
```

---

## 3. VMG & Maneuver Analysis Rules

### 3.1 Velocity Made Good (VMG)
- **Upwind VMG**: $\text{VMG}_{\text{up}} = \text{BSP} \cdot \cos(\text{TWA})$
- **Downwind VMG**: $\text{VMG}_{\text{down}} = \text{BSP} \cdot \cos(180^\circ - \text{TWA}) = -\text{BSP} \cdot \cos(\text{TWA})$

### 3.2 Tacking & Gybing Loss Decomposition
Segment maneuvers whenever heading changes exceed $60^\circ$ through the eye of the wind (tack) or away from the wind (gybe):
1. **Entry Speed**: Average BSP 5 seconds prior to turn initiation.
2. **Minimum Speed**: Lowest instantaneous speed recorded during turn.
3. **Exit Speed / Recovery Time**: Time elapsed until BSP re-attains 90% of target polar speed.
4. **Distance Loss**: Integral difference between actual distance covered and theoretical distance at steady target polar speed:
   $$\Delta S_{\text{loss}} = \int_{t_{\text{start}}}^{t_{\text{recov}}} \left(V_{\text{target}}(t) - V_{\text{actual}}(t)\right) dt$$

---

## 4. Dash-Plotly Visual Standards

When implementing interactive dashboards (such as in `DumbSail`):
- **Theme**: Dark theme (`#0f172a` primary background, `#1e293b` cards, `#38bdf8` cyan accent, `#22c55e` emerald success).
- **State Persistence**: Set `uirevision="persistent-user-state"` on all `go.Figure` layouts to preserve zoom and pan positions across reactive callback cycles.
- **Fast Plotting**: Use `go.Scattergl` instead of standard SVG `go.Scatter` when plotting dense high-frequency telemetry ($> 10,000$ points).
