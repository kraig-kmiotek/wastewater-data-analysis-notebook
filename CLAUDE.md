# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Jupyter notebook project for analyzing NPDES (National Pollutant Discharge Elimination System) effluent violations in Arizona using EPA data.

## Data Architecture

Two data sources, used in distinct stages:

1. **`AZ_NPDES_EFF_VIOLATIONS.csv`** — Primary violations dataset, downloaded from the EPA ECHO effluent violations download tool. Loaded directly with pandas in the Data Loading section. Not fetched from an API.

2. **ECHO DFR API** (`https://echodata.epa.gov/echo/dfr_rest_services.get_dfr`) — Used only in Facility Enrichment to look up metadata (facility name, SIC code, city, state, lat/long) for each unique `NPDES_ID` found in the CSV. Results are cached to **`facility_lookup.csv`** so the API is only called once.

   > **Note:** The older `cwa_rest_services.get_facility_info` endpoint (on `echo.epa.gov`) is a geographic cluster search and does not support single-permit lookup by `p_id` — it ignores the parameter and returns all ~1.1M facilities. The correct single-permit endpoint is `dfr_rest_services.get_dfr` on `echodata.epa.gov`, called with `?p_id={NPDES_ID}&output=JSON`. Facility name and coordinates are extracted from `Results.Permits[]`; SIC code from `Results.SIC.Sources[]` matching `SourceID == NPDES_ID`.

## Running the Notebook

```bash
jupyter lab npdes_violation_analysis.ipynb
# or
jupyter notebook npdes_violation_analysis.ipynb
```

## Dependencies

```bash
pip install jupyter pandas requests matplotlib seaborn plotly ipywidgets
```

## Notebook Structure

`npdes_violation_analysis.ipynb` is organized into these sections:

1. **Environment Setup** — installs/imports libraries, sets matplotlib/seaborn/pandas display defaults
2. **Configuration** — single cell defining `DATA_FILES` (list of state CSVs), `FACILITY_CACHE_FILE`, and `ECHO_FACILITY_ENDPOINT`. **This is the only place filenames are defined.** To add a state, append its CSV path here.
3. **Data Loading** — loops over `DATA_FILES`, tags each row with `SOURCE_STATE` (parsed from filename), concatenates into a single `violations` DataFrame
4. **Facility Enrichment** — ECHO API lookups cached to `facility_lookup.csv`, joined on `NPDES_ID`
5. **Analysis** — violation trends, repeat offenders, compliance rates
6. **Visualizations** — interactive Plotly charts (line trend, top facilities bar, parameter bar colored by measurement technique, facility scatter map)

## Adding a New State

1. Download the state's effluent violations CSV from [ECHO data downloads](https://echo.epa.gov/tools/data-downloads) — filename must follow the pattern `{STATE_ABBR}_NPDES_EFF_VIOLATIONS.csv`.
2. Place the file in the `data/` folder.
3. Add `'data/{STATE_ABBR}_NPDES_EFF_VIOLATIONS.csv'` to `DATA_FILES` in the Configuration cell.
4. Re-run the notebook. The facility cache (`data/facility_lookup.csv`) will be extended automatically for any new `NPDES_ID` values.

## Data Files

All CSV files live in `data/` and are excluded from git (see `.gitignore`). The folder is tracked via `data/.gitkeep`.

## GitHub Pages Compatibility

The goal is to eventually publish this as a GitHub Page. Keep this in mind when building interactive features:

- **Plotly** — fully interactive on static GitHub Pages (pure JavaScript). Use Plotly for all filters, dropdowns, and sliders intended for the published page.
- **ipywidgets** — requires a live Python kernel; widgets will be non-functional on a static page. Limit ipywidgets to local/exploratory use only, never for features meant to work on the webpage.

To publish: `jupyter nbconvert --to html --execute npdes_violation_analysis.ipynb`, then commit the HTML to a `gh-pages` branch or `docs/` folder.
