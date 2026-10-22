---
name: grib-netcdf-wrangler
description: >-
  High-performance reading, slicing, conversion, and spatial reprojection of large meteorological and oceanographic
  datasets in NetCDF, HDF5, and GRIB formats. Use this skill when dealing with HPC storage (Lustre file locking),
  xarray/dask out-of-core operations, eccodes/cfgrib data handling, or grid interpolations.
---

# GRIB & NetCDF Data Wrangling – Performance & Storage Guidelines

This skill provides operational patterns for efficiently processing massive atmospheric, oceanic, and wave model archives (e.g. NORA3, ECMWF ERA5/IFS, ROMS, and AROME-Arctic) without running into memory exhaustion, file locking errors, or process stalls.

---

## 1. HPC Lustre Storage & File Locking Protocols

On shared scientific computing clusters and Lustre filesystems (such as `/lustre/storeB/...`):
- Read-only directories trigger `OSError: [Errno 30] Read-only file system` or HDF5 library hangs when standard POSIX file locking is attempted.
- **Mandatory Pre-condition**: Disable HDF5 file locking in the runtime environment before invoking `xarray`, `netCDF4`, or `h5py`.

```bash
# In shell scripts or before running python
export HDF5_USE_FILE_LOCKING=FALSE
```

Or within Python code before importing data libraries:
```python
import os
os.environ["HDF5_USE_FILE_LOCKING"] = "FALSE"

import xarray as xr
import netCDF4 as nc
import h5py
```

---

## 2. Memory-Safe Hyperslab Extraction

Avoid loading full 3D/4D domain arrays into RAM when evaluating point locations (stations, buoys, or transects):

### 2.1 Coordinate Index Pre-Matching (Nearest Neighbor)
Extract the coordinate arrays first, identify the target indices $(j, i)$ or $(\eta, \xi)$, and slice with hyperslabs:

```python
import numpy as np
import xarray as xr

# Load only 2D coordinates once
with xr.open_dataset(grid_or_sample_file) as ds:
    lats = ds["lat_rho"].values
    lons = ds["lon_rho"].values

# Efficient 2D spatial distance calculation
def find_nearest_indices(target_lat: float, target_lon: float, lats: np.ndarray, lons: np.ndarray):
    # Euclidean approximation in small area or Haversine for broad area
    dist_sq = (lats - target_lat)**2 + ((lons - target_lon) * np.cos(np.radians(target_lat)))**2
    return np.unravel_index(np.argmin(dist_sq), dist_sq.shape)

j_idx, i_idx = find_nearest_indices(target_lat, target_lon, lats, lons)

# Slice target time-series across multiple history files without full load
with xr.open_mfdataset(file_pattern, parallel=True) as ds:
    station_series = ds["zeta"].isel(eta_rho=j_idx, xi_rho=i_idx).load()
```

### 2.2 Chunking and Dask Best Practices
- Specify spatial chunks when analyzing time-series: `chunks={"time": -1, "eta_rho": 50, "xi_rho": 50}`.
- Specify temporal chunks when computing spatial maps: `chunks={"time": 100, "eta_rho": -1, "xi_rho": -1}`.

---

## 3. GRIB File Ingestion (`cfgrib` / `eccodes`)

Atmospheric wind, pressure, and wave fields frequently arrive in GRIB1 or GRIB2 formats.

### 3.1 Resolving Heterogeneous GRIB Messages
GRIB files often store multiple vertical levels or parameter types in the same file, causing `xarray.open_dataset` to fail with `dataset has multiple values for key`.
Solve this using `filter_by_keys`:

```python
import xarray as xr

# Load 10m Wind U and V components specifically
ds_wind = xr.open_dataset(
    "forecast.grib",
    engine="cfgrib",
    backend_kwargs={
        "filter_by_keys": {
            "typeOfLevel": "heightAboveGround",
            "level": 10
        }
    }
)
```

### 3.2 Cleaning Temporary GRIB Index Files
`cfgrib` automatically generates `.idx` files alongside GRIB files. 
- In read-only directories, this raises permission errors.
- Pass `indexpath=""` in `backend_kwargs` to force in-memory indexing when reading from read-only storage:
  ```python
  xr.open_dataset("archive.grib", engine="cfgrib", backend_kwargs={"indexpath": ""})
  ```

---

## 4. Reprojection & Coordinate Transformations

1. **Polar Stereographic / Lambert Conformal Grids**:
   - ROMS and meteorological models frequently utilize non-regular projections.
   - For plotting with Cartopy:
     ```python
     import cartopy.crs as ccrs
     import matplotlib.pyplot as plt

     proj = ccrs.NorthPolarStereo(central_longitude=15.0)
     fig, ax = plt.subplots(subplot_kw={"projection": proj})
     ax.pcolormesh(lon_2d, lat_2d, data_2d, transform=ccrs.PlateCarree(), shading="auto")
     ax.coastlines(resolution="10m")
     ```
2. **Rotating Wind / Current Vectors**:
   - Ocean models provide velocities aligned to grid coordinates $(u_{\xi}, v_{\eta})$.
   - Always rotate using the local grid angle $\theta$ before comparing with true North/East observations:
     $$u_{\text{east}} = u_{\xi} \cos\theta - v_{\eta} \sin\theta$$
     $$v_{\text{north}} = u_{\xi} \sin\theta + v_{\eta} \cos\theta$$
