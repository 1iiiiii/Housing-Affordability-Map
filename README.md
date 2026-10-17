# Where home prices outran incomes since the pandemic

Between the 2015–19 and 2020–24 American Community Survey windows, where in the United States did the number of years of household income needed to buy the median home rise the most, and what moved with it? This repository maps the change in the county price-to-income ratio and sets it against county population growth.

Published post: https://1iiiiii.github.io/Personal-Website/blog/posts/post5/

## Data

All data are downloaded by `code/01_fetch_census.ipynb` and cached in `data/raw/` (the access date is recorded in `data/raw/fetch_log.json`).

| Source | File / variables | What it contains | Period |
|---|---|---|---|
| U.S. Census Bureau, cartographic boundary files | `cb_2024_us_county_500k.zip` (shapefile, 1:500,000) | County polygons with `GEOID`, `STATEFP`, `NAME`, `STATE_NAME` | 2024 vintage |
| U.S. Census Bureau, ACS 5-year estimates via the Census API (`/data/2019/acs/acs5`) | `B25077_001E`, `B19013_001E`, `B01003_001E` | Median home value, median household income (2019 dollars), total population, by county | 2015–19 |
| U.S. Census Bureau, ACS 5-year estimates via the Census API (`/data/2024/acs/acs5`) | `B25077_001E`, `B19013_001E`, `B01003_001E`, `B08006_001E`, `B08006_017E` | Same three in 2024 dollars, plus workers 16+ and workers who worked from home, by county | 2020–24 |

Median home value is top-coded at $2,000,001. Connecticut's 2020–24 estimates use planning regions rather than counties, so they have no 2015–19 match and are left missing; the unmatched identifiers are listed in `results/tables/unmatched_geoids.csv`. Alaska, Hawaii and Puerto Rico are in the dataset but not on the continental map.

## Setup

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env   # then paste your Census API key (required: keyless requests are redirected to an error page)
```

Open the notebooks in VS Code with the `.venv` kernel selected.

## Replication

Run the notebooks in `code/` in order; each reads the previous one's output files.

| Notebook | Reads | Writes |
|---|---|---|
| `01_fetch_census.ipynb` | Census API, census.gov | `data/raw/cb_2024_us_county_500k.zip` and its extracted folder, `data/raw/acs5_2019_county.csv`, `data/raw/acs5_2024_county.csv`, `data/raw/fetch_log.json` |
| `02_build.ipynb` | `data/raw/` | `data/processed/counties.csv`, `data/processed/counties.gpkg` (EPSG:5070), `results/tables/headline.csv`, `extremes.csv`, `why_bins.csv`, `why_corr.csv`, `unmatched_geoids.csv` |
| `03_figures.ipynb` | `data/processed/`, `results/tables/` | `results/figures/fig1_map.png`, `results/figures/fig2_why.png` |

Measures (all in `02_build.ipynb`): price-to-income ratio = median home value ÷ median household income, in years of income; the map variable is its change between the two windows. Percentage changes in home value, income and population are computed between the same windows. Tables and Fig 2 use counties with a 2020–24 population of at least 50,000, excluding Puerto Rico; the map shows every continental county.

## Folder layout

```
code/               notebooks in run order, plus plot_style.py (shared figure style)
data/raw/           downloads from the Census (boundaries are git-ignored; ACS CSVs are committed)
data/processed/     merged county dataset, CSV and GeoPackage
results/figures/    the two figures embedded in the post
results/tables/     every number quoted in the post
```
