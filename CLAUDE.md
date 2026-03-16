# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Jupyter notebook project for analyzing NPDES (National Pollutant Discharge Elimination System) effluent violations in Arizona using EPA data.

## Data Architecture

Two data sources, used in distinct stages:

1. **`AZ_NPDES_EFF_VIOLATIONS.csv`** — Primary violations dataset, downloaded from the EPA ECHO effluent violations download tool. Loaded directly with pandas in the Data Loading section. Not fetched from an API.

2. **ECHO API** (`https://echo.epa.gov/api/echo/cwa_rest_services.get_facility_info`) — Used only in Facility Enrichment to look up metadata (facility name, SIC code, city, state, lat/long) for each unique `NPDES_ID` found in the CSV. Results are cached to **`facility_lookup.csv`** so the API is only called once.

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
6. **Visualizations** — static matplotlib/seaborn charts and interactive Plotly figures

## Adding a New State

1. Download the state's effluent violations CSV from [ECHO data downloads](https://echo.epa.gov/tools/data-downloads) — filename must follow the pattern `{STATE_ABBR}_NPDES_EFF_VIOLATIONS.csv`.
2. Add the filename to `DATA_FILES` in the Configuration cell.
3. Re-run the notebook. The facility cache will be extended automatically for any new `NPDES_ID` values.
