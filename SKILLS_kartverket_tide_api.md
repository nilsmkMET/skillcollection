---
name: kartverket-tide-api
description: >-
  Protocol, endpoint specifications, and client architecture for the Norwegian Mapping Authority (Kartverket)
  Water Level and Tide API (tideapi.php / Se Havnivå). Use this skill when querying Norwegian coastal tide gauges,
  fetching observed water levels, tidal predictions, weather effects (surge residuals), or station metadata.
---

# Kartverket Tide & Water Level API – Developer & Agent Manual

This skill specifies the query protocols, XML payload parsing, data cleaning, and caching standards for querying the Norwegian Mapping Authority (*Kartverket*) Water Level API (**tideapi.php** / *Se Havnivå*).

---

## 1. API Endpoints & Core URL Structure

Base Endpoint:
`https://vannstand.kartverket.no/tideapi.php`

### 1.1 Station List Query (`tide_request=stationlist`)
Used to retrieve all registered tide gauge stations and their geographical coordinates:
```text
https://vannstand.kartverket.no/tideapi.php?tide_request=stationlist&type=perm
```
- `type=perm`: Returns all permanent tide gauges (33 standard permanent stations along the mainland and Svalbard).
- Output: XML document with `<stationinfo>` elements containing attributes:
  - `code`: 3-letter station identifier (e.g. `OSL`, `BGO`, `TRD`, `VAW`).
  - `name`: Official Norwegian station name (e.g. `Oslo`, `Bergen`, `Trondheim`, `Vardø`).
  - `latitude`, `longitude`: WGS84 decimal degrees.

### 1.2 Time Series Data Query (`tide_request=locationdata`)
Used to fetch water level observations, predictions, and weather surge residuals for a coordinate pair:
```text
https://vannstand.kartverket.no/tideapi.php?tide_request=locationdata&lat={LAT}&lon={LON}&refcode={REF}&dst=0&fromtime={FROM}&totime={TO}&interval={INT}&datatype={TYPE}
```

#### Query Parameters:
| Parameter | Permitted Values | Default / Recommended | Purpose |
| :--- | :--- | :--- | :--- |
| `lat`, `lon` | Decimal degrees | Required | Station coordinates |
| `refcode` | `msl`, `cd`, `nn2000`, `lat` | `msl` | Reference vertical datum (Mean Sea Level, Chart Datum, NN2000, Lowest Astronomical Tide) |
| `dst` | `0`, `1` | `0` | Daylight Saving Time flag (`0` = UTC time, essential for science) |
| `fromtime` | `YYYY-MM-DDTHH:MM` | Required | Start datetime in ISO 8601 format |
| `totime` | `YYYY-MM-DDTHH:MM` | Required | End datetime in ISO 8601 format |
| `interval` | `10`, `60` | `60` | Time step in minutes (10-min or hourly) |
| `datatype` | `all`, `obs`, `pre`, `wea` | `all` | `obs`: observation, `pre`: harmonic prediction, `wea`: surge/weather residual, `all`: all available |

---

## 2. XML Response Structure & Units

The API returns an XML payload structured into `<data type="...">` sections:
- `type="observation"`: In-situ measured water level.
- `type="prediction"`: Astronomical tidal prediction from harmonic constituents.
- `type="weathereffect"`: Meteorologically driven residual ($\zeta_{\text{wea}} = \zeta_{\text{obs}} - \zeta_{\text{pred}}$).

### Unit Conversion Warning
The XML `<waterlevel>` elements report raw elevation values in **centimeters (cm)**:
```xml
<waterlevel value="142" time="1990-01-01T00:00:00+00:00" flag="..."/>
```
**Mandatory Transformation**: Convert to SI **meters (m)** immediately upon ingestion:
$$\text{elevation}_{\text{meters}} = \frac{\text{value}}{100.0}$$

---

## 3. Standard Robust Fetching & Caching Architecture

Always implement local CSV or Parquet caching so repeated runs do not saturate Kartverket's servers:

```python
import os
import xml.etree.ElementTree as ET
import pandas as pd
import requests

def fetch_kartverket_station(
    code: str, 
    lat: float, 
    lon: float, 
    year: int, 
    cache_dir: str = "data"
) -> pd.DataFrame:
    """Fetch hourly station data with local disk caching."""
    os.makedirs(cache_dir, exist_ok=True)
    cache_path = os.path.join(cache_dir, f"kartverket_{code}_{year}.csv")
    
    if os.path.exists(cache_path):
        return pd.read_csv(cache_path, parse_dates=["time"])
        
    url = (
        f"https://vannstand.kartverket.no/tideapi.php?tide_request=locationdata"
        f"&lat={lat}&lon={lon}&refcode=msl&dst=0"
        f"&fromtime={year}-01-01T00:00&totime={year+1}-01-01T00:00&interval=60&datatype=all"
    )
    
    response = requests.get(url, timeout=60)
    response.raise_for_status()
    root = ET.fromstring(response.content)
    
    obs_dict, pred_dict, wea_dict = {}, {}, {}
    for d in root.findall(".//data"):
        dtype = d.get("type")
        for wl in d.findall(".//waterlevel"):
            t = wl.get("time")
            try:
                val = float(wl.get("value")) / 100.0  # cm to m
            except (ValueError, TypeError):
                continue
            if dtype == "observation":
                obs_dict[t] = val
            elif dtype == "prediction":
                pred_dict[t] = val
            elif dtype == "weathereffect":
                wea_dict[t] = val

    all_times = sorted(set(list(obs_dict.keys()) + list(pred_dict.keys()) + list(wea_dict.keys())))
    records = []
    for t in all_times:
        records.append({
            "time": t,
            "obs_m": obs_dict.get(t, None),
            "pred_m": pred_dict.get(t, None),
            "surge_obs_m": wea_dict.get(t, None),
        })
        
    df = pd.DataFrame(records)
    df["time"] = pd.to_datetime(df["time"])
    df.to_csv(cache_path, index=False)
    return df
```

---

## 4. Historical Data Caveats & Quality Control

1. **Digital Record Coverage**:
   - Continuous digital observations begin in different years depending on the station. 
   - Stations such as Bergen (`BGO`), Stavanger (`SVG`), and Oslo (`OSL`) have digital series stretching back before 1960.
   - Other stations only have digitized observations from the late 1980s or 1990 onwards.
   - When observations are absent (`obs_m` is null), `pred_m` (astronomical predictions) can still be retrieved across all permanent stations for validation of astronomical tide models.
2. **Missing Value Flags**:
   - Inspect the `flag` attribute if present in the XML. Flagged or physically impossible values ($|\zeta| > 5.0$ m on the Norwegian coast) must be masked to `NaN`.
