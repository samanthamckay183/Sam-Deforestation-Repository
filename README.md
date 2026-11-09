# Sam Deforestation Repository

Panama-focused geospatial workflow for exploring landscape predictors of forest loss. Notebooks import and process remote sensing and ancillary data in [Google Earth Engine (GEE)](https://earthengine.google.com/), visualize layers with [geemap](https://geemap.org/), and build toward combined predictor stacks and tabular outputs for modeling deforestation (and related one-health analyses at district scale).

A separate `6_barbados` folder reuses the same GEE patterns on Barbados as a smaller test case.

## Repository layout

```
Sam-Deforestation-Repository
├── 1_ana_gee/                    # Exploratory GEE notebooks (Panama)
├── 2_ana_datasets_combined/      # Multi-layer map of chosen predictors
├── 3_ana_reprojected/            # Align layers to a common projection / grid
├── 4_ana_response_variable/      # Forest loss & external driver products
├── 5_javier/                     # Protected areas, land cover, district dataframe
├── 6_barbados/                   # Barbados pilot notebooks
└── README.md
```

## Workflow (recommended order)

1. **`1_ana_gee`** — Compare candidate datasets and derive distance / climate / terrain layers for Panama.
2. **`2_ana_datasets_combined`** — Stack the selected predictors on one interactive map.
3. **`3_ana_reprojected`** — Reproject layers so resolution, CRS, and extent match for export or modeling.
4. **`4_ana_response_variable`** — Define and visualize the response (Hansen loss, WRI–Google DeepMind drivers).
5. **`5_javier`** — Site-level and district-level tabular work (WDPA, SINIA land use, WaPOR, health outcomes).
6. **`6_barbados`** — Optional: repeat distance / land-cover / soil workflows for Barbados.

---

## `1_ana_gee`

Initial GEE exploration for Panama (boundary from FAO GAUL or LSIB). Each notebook compares sources where relevant and documents the layer chosen for downstream work.

| Notebook | Purpose |
|----------|---------|
| `DEM.ipynb` | Elevation: Copernicus GLO30 (project standard), MERIT, SRTM; slope from project asset |
| `Dry_vs_Wet_Forest.ipynb` | Wet vs dry forest mask from ESA WorldCover tree cover + WWF Panamanian dry forests ecoregion |
| `Hydrographic_Basins_Dist.ipynb` | Distance to SINIA hydrographic basins (PRISM-derived asset in GEE) |
| `LULC_DynamicWorld.ipynb` | Land use / land cover from Google Dynamic World + Sentinel-2 context |
| `Precipitation.ipynb` | CHIRPS daily (selected), GPM IMERG comparison |
| `Protected_Areas_Dist.ipynb` | WDPA polygons → distance to protected areas |
| `Roads_Dist.ipynb` | STRI OpenStreetMap Panama roads → distance to roads |
| `Soil.ipynb` | SoilGrids properties (organic C, N, sand, pH) via project assets |
| `Temp.ipynb` | Temperature: ERA5-Land monthly (selected), ERA5 hourly, TerraClimate, CPC |
| `Urban_Rural_Dist.ipynb` | Distance to urban/rural extent from GHSL SMOD |

Custom or uploaded assets are referenced under the GEE project `deforestation-495419` (e.g. roads, slope, soils, basins).

---

## `2_ana_datasets_combined`

**`Datasets_Combined.ipynb`** — Single geemap view layering the chosen predictors: Dynamic World, DEM (MERIT in this notebook), slope, soils, distances (roads, deforestation, urban/rural), dry/wet forest, monthly precipitation and temperature, hydrographic basins, and protected areas.

Use this notebook to confirm symbology and spatial alignment before reprojection.

---

## `3_ana_reprojected`

**`1_reprojections.ipynb`** — Compare projections for elevation (Copernicus DEM standard), Dynamic World, distance to protected areas, roads, urban/rural, and dry vs wet forest; composite map at the end.

**`2_reprojections.ipynb`** — Part 2: elevation, slope, soils, distance to deforested areas, monthly precipitation and temperature, distance to hydrographic basins; composite map.

These notebooks support exporting or modeling on a consistent grid.

---

## `4_ana_response_variable`

| Notebook | Purpose |
|----------|---------|
| `Global_Forest_Change.ipynb` | Hansen Global Forest Change (e.g. tree cover, `lossyear`); recent loss and distance-to-loss surfaces for Panama |
| `Dominant_Drivers_of_Deforestation_WRI_GoogleDeepMind.ipynb` | WRI / Google DeepMind drivers of forest loss (classification and probability bands), clipped to Panama |

---

## `5_javier`

Collaboration / analysis notebooks for protected areas, land-cover typing, evapotranspiration, and district-level one-health dataframe construction.

| Notebook | Purpose |
|----------|---------|
| `PA_Types.ipynb` | Compare land-cover / forest products: SINIA 2021, PRISM ESA PFT, ESA CCI, STRI GIS forest cover |
| `PA_Testfile.ipynb` | WDPA buffering experiments, PA metadata, provinces, areas, land-use classes |
| `Dataframe_PAs.ipynb` | WDPA buffers, land-use aggregation, climate stacks (2014–2024), WaPOR AETI, tabular export |
| `Evapotranspiration.ipynb` | FAO WaPOR actual evapotranspiration and interception v3 |
| `Temp.ipynb` | Temperature workflow (Javier copy) |
| `Honours_TestFile.ipynb` / `Honours_TestFile_Complete.ipynb` | District-level **one health** dataframe: ANATI 2025 districts, health facilities, SINIA LULC, WaPOR trend, purchasing power, PA coverage, mortality (requires local CSV inputs listed in the notebook) |

---

## `6_barbados`

Pilot notebooks applying the same GEE patterns to **Barbados** (GAUL boundary): ESA WorldCover classes, WorldCereal crops, road distance exports, and SoilGrids-derived soil properties.

---

## Prerequisites

- Python 3 with Jupyter (notebooks reference a `deforestation_env` kernel in metadata).
- Packages used across notebooks: `earthengine-api`, `geemap`, `pandas`, `numpy`, `geopandas` (district workflow), `requests`.
- A GEE account with access to the project assets noted above (authenticate once per environment):

```python
import ee
ee.Authenticate()
ee.Initialize(project="deforestation-495419")  # or your project ID
```

- For `5_javier/Honours_TestFile_Complete.ipynb`, place the CSV files named in the notebook (population, purchasing power, mortality) in the working directory before running.

---

## Data sources (non-exhaustive)

| Theme | Examples in repo |
|-------|------------------|
| Forest loss | Hansen GFC, WRI GDM drivers |
| LULC | Dynamic World, ESA WorldCover, SINIA, STRI GIS |
| Terrain | Copernicus DEM, MERIT, SRTM, slope |
| Climate | CHIRPS, ERA5-Land, TerraClimate, WaPOR AETI |
| Soils | SoilGrids (via GEE assets) |
| Accessibility | WDPA, OSM roads (STRI), GHSL SMOD |
| Hydrology | SINIA basins (PRISM) |
| Admin | FAO GAUL, ANATI districts (honours workflow) |

---

## Notes

- Notebooks are research artifacts: run order, asset IDs, and scale factors should be verified before production exports.
- Large exports are triggered via GEE `Export` tasks in several notebooks; monitor tasks in the [Earth Engine Code Editor](https://code.earthengine.google.com/) or your GEE project console.
