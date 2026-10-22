# skillcollection

A curated repository of writing, editorial, and scientific domain engineering skills based on international standards, physical models, and domain-specific best practices.

## Available Skills

### Writing & Language Standards

| File | Skill Name | Scope & Language | Focus Area | Core Sources & Authorities |
| :--- | :--- | :--- | :--- | :--- |
| [SKILLS_klarspraak_norsk.md](./SKILLS_klarspraak_norsk.md) | `klarsprak-norsk` | Norwegian (Bokmål / Nynorsk) | Public sector & general plain language (*kansellistil* eradication, administrative clarity) | Språkrådet (*sprakradet.no*), Språklova § 9, ISO 24495-1, Digdir, Finn-Erik Vinje |
| [SKILLS_clear_academic_english.md](./SKILLS_clear_academic_english.md) | `clear-academic-english` | English (Academic & Scholarly) | Journal manuscripts, theses, grants, peer reviews; conceptual precision, zombie nouns, hedge calibration | ISO 24495-1, APA 7th, Chicago, *Nature Portfolio*, Helen Sword, Joseph Williams (*Style*), Day & Gastel, Gopen & Swan |
| [SKILLS_plain_english_broad_audience.md](./SKILLS_plain_english_broad_audience.md) | `plain-english` | English (General & Broad Audience) | Web copy, public notices, guides, policies; readability (7–9th grade), scannability, BLUF, eliminating gobbledygook | ISO 24495-1, US PLAIN / Plain Writing Act (*plainlanguage.gov*), UK GDS (*gov.uk*), Plain English Campaign, Martin Cutts |

### Oceanography, Meteorology & Engineering Skills

| File | Skill Name | Scope & Domain | Focus Area | Core Standards & Libraries |
| :--- | :--- | :--- | :--- | :--- |
| [SKILLS_roms_ocean_eval.md](./SKILLS_roms_ocean_eval.md) | `roms-ocean-eval` | Physical Oceanography | ROMS simulation verification, non-linear tide-surge interaction ($\zeta_{\text{int}}$), harmonic constituent analysis | `utide`, ROMS CPP flags (`SSH_TIDES`, `UV_TIDES`), CODAG harmonic analysis |
| [SKILLS_kartverket_tide_api.md](./SKILLS_kartverket_tide_api.md) | `kartverket-tide-api` | Coastal Hydrodynamics | Kartverket Water Level API (`tideapi.php`), station resolution, unit conversion (cm to m), robust caching | Kartverket Tide API, *Se Havnivå*, XML parsing, MSL/LAT/CD datums |
| [SKILLS_scientific_latex_report.md](./SKILLS_scientific_latex_report.md) | `scientific-latex-report` | Scientific Publishing | Publication-ready LaTeX report templates, booktabs tables, multi-pass PDF compilation | `pdflatex`, `booktabs`, `amsmath`, AMS math typography |
| [SKILLS_grib_netcdf_wrangler.md](./SKILLS_grib_netcdf_wrangler.md) | `grib-netcdf-wrangler` | HPC Geospatial Data | Lustre storage file locking, memory-safe hyperslabs, GRIB message filtering, grid rotations | `xarray`, `netCDF4`, `cfgrib`, `eccodes`, `HDF5_USE_FILE_LOCKING=FALSE` |
| [SKILLS_sailing_polar_telemetry.md](./SKILLS_sailing_polar_telemetry.md) | `sailing-polar-telemetry` | Sailing Telemetry & Routing | ORC `.pol` parsing, 2D polar interpolation, VMG, tacking loss metrics, Dash-Plotly standards | ORC VPP standards, `scipy.interpolate`, Dash-Plotly, Cartopy |

## Sources and Authority Overview

Each skill file includes an explicit **Sources and References** section providing full citations and external documentation links:
1. **[Klarspråk (Norsk)](./SKILLS_klarspraak_norsk.md#6-kilder-og-referanser):** Grounded in the statutory mandates of Norway's Language Act (*Språklova* § 9), the official guidelines of the Language Council of Norway (*Språkrådet*), and ISO 24495-1.
2. **[Clear Academic English](./SKILLS_clear_academic_english.md#6-sources-and-references):** Synthesizes ISO 24495-1 with major academic style manuals (APA 7th, Chicago, *Nature Portfolio*), linguistic scholarship on sentence architecture (Williams & Bizup's *Style*), and research on scholarly clarity (Helen Sword's *Stylish Academic Writing*, Gopen & Swan's *The Science of Scientific Writing*).
3. **[Plain English for a Broad Audience](./SKILLS_plain_english_broad_audience.md#6-sources-and-references):** Anchored in the ISO 24495-1:2023 international standard, statutory US plain language guidelines (*plainlanguage.gov* via the Plain Writing Act of 2010), the UK Government Digital Service (GDS) content principles, and standard manuals by the Plain English Campaign and Oxford University Press (Cutts).


