# NPDES Effluent Violation Analysis

An interactive data analysis project exploring wastewater discharge violations reported under the
**National Pollutant Discharge Elimination System (NPDES)**, the federal permitting program that
regulates what facilities can release into US waterways.

**[View the live report](https://kraig-kmiotek.github.io/wastewater-data-analysis-notebook/)**

---

## What this is

Facilities that discharge wastewater (treatment plants, industrial sites, mines) must hold an
NPDES permit and file regular Discharge Monitoring Reports (DMRs) with the EPA. When a facility
misses a report or exceeds a permitted pollutant limit, that's recorded as a violation.

This project pulls that violation data from the EPA's [ECHO database](https://echo.epa.gov),
enriches it with facility metadata, and produces an interactive report covering:

- Violation trends over time
- The facilities with the most violations
- Which pollutants are most frequently exceeded
- Geographic distribution of permitted facilities

## Data sources

| Source | Description |
|--------|-------------|
| [EPA ECHO Effluent Downloads](https://echo.epa.gov/tools/data-downloads) | Pre-downloaded violation records by state |
| [EPA ECHO DFR API](https://echodata.epa.gov/echo/dfr_rest_services.get_dfr) | Facility metadata (name, location, industry) fetched by permit ID |

## Current coverage

- **Arizona** — full violation history
