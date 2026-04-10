# Louisville Crime Analysis 2004–2025

Analysis of crime data from the Louisville Metro Police Department combined with
U.S. Census Bureau American Community Survey (ACS) socioeconomic data to identify
key drivers of crime trends across Louisville ZIP codes over a 20-year period.

---

## Project Overview

In 2003 Jefferson County merged with the City of Louisville to become Louisville
Metro Government, combining police forces into one department reporting standardized
crime data across the entire county. This project leverages that unified dataset
to investigate the relationship between crime trends and socioeconomic conditions
across Louisville ZIP codes from 2004–2025.

**Primary Research Questions:**
- What socioeconomic factors are most strongly associated with crime rates across Louisville ZIP codes over time?
- Are there ZIP codes where crime is declining despite unfavorable socioeconomic indicators — or vice versa?
- Are national or local policies and events key drivers of changes in crime rates?

---

## Key Findings

- Total reported crime peaked around 2007–2008 and has declined significantly since 2016
- The downward trend beginning in 2016 mirrors national patterns documented in FBI data
- Drug/alcohol violations and burglary show the steepest declines in the 2016–2025 period vs. 2006–2015
- Weapons-related crime rose consistently from 2004 to 2021 before declining
- Louisville was identified in national research as one of eight U.S. cities that experienced a 100%+ increase in homicides in 2021 relative to the pre-pandemic five-year average, visible in the data
- **Regression model (R² = 0.482):** Five socioeconomic variables explain 48% of the variation in crime rate across ZIP codes
- **Poverty rate** is the strongest positive predictor of crime (coefficient: +15.2 per 1,000 per percentage point)
- **Education level** (% with bachelor's degree or higher) is the strongest negative predictor (coefficient: -9.7 per 1,000)
- Several ZIP codes exhibit a High Poverty / Low Crime profile, suggesting factors beyond socioeconomic conditions influence local crime rates
- The analytical universe covers 32 Louisville ZCTAs with matching Census demographic data — approximately 250 additional ZIP values in the raw data were excluded as invalid, out-of-jurisdiction, or data entry errors

### Methodology Note — Residential Population Bias
Crime rate per 1,000 people uses residential population as the denominator sourced from ACS 5-year estimates. ZIP codes with low residential populations but high daytime or event-driven foot traffic — particularly downtown Louisville (40202) and the expo/stadium area — may show artificially elevated crime rates per 1,000 residents. These ZIPs are noted as potential outliers in the per-capita analysis and should be interpreted with caution.

---

## Data Sources

| Dataset | Source | Years | Granularity |
|---|---|---|---|
| Louisville Metro Crime Data | [Louisville Metro Open Data](https://data.louisvilleky.gov/) | 2004–2025 | One row per incident |
| U.S. Census ACS 5-Year Estimates | [Census Bureau API](https://www.census.gov/data/developers.html) | 2012–2023 | One row per ZIP per year |

**Notes:**
- ACS 5-year estimates are rolling averages — a 2022 estimate covers 2018–2022. ACS ZIP-level data is only available from 2012 onward, limiting the joined analysis to 2012–2023.
- Socioeconomic data was available for 2012–2023 only. Crime data spans 2004–2025 but socioeconomic context is limited to the overlapping years.
- Offense Classification remapping: In 2022 Louisville Metro expanded crime classifications from 16 to 47 categories. To maintain continuity across the full 2004–2025 timeline, the 47 post-2022 classifications were remapped back to the original 16 standardized categories.

---

## Repository Structure

```
lou_crime_report/
├── initial_lou_crime_data/         # Raw yearly crime CSVs from Louisville Metro
│   ├── Crime_Data_2004.csv
│   ├── ...
│   └── Crime_Data_2025.csv
├── notebooks/
│   ├── lou_crime_analysis.ipynb    # Main analysis notebook
│   └── data/
│       └── louisville_demographics.csv  # ACS data output
├── data/
│   └── erd.png                     # Entity relationship diagram
├── plots/                          # Saved visualization PNGs (generated on run)
├── .env                            # Census API key (not committed)
├── .gitignore
├── README.md
└── requirements.txt
```

---

## Setup Instructions

### Prerequisites
- Python 3.9 or higher
- pip
- Census API key — register free at [api.census.gov](https://api.census.gov/metadocs)

### Installation

```bash
# Clone the repository
git clone https://github.com/JonLar1/Lou_Crime_Data
cd lou_crime_report

# Create a virtual environment
python3 -m venv venv

# Activate the virtual environment
# On macOS/Linux:
source venv/bin/activate
# On Windows:
venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Environment Setup

Create a `.env` file in the project root with your Census API key:

```
CENSUS_API_KEY=your_census_api_key_here
```

### Running the Notebook

```bash
# Option 1: Jupyter Notebook
jupyter notebook notebooks/lou_crime_analysis.ipynb

# Option 2: VS Code
# Open notebooks/lou_crime_analysis.ipynb and select the venv Python kernel
```

Run all cells top to bottom. The notebook will:
1. Load and combine all 22 yearly crime CSVs into a single 1.7M row DataFrame
2. Standardize headers across datasets (column names changed between 2004–2022 and 2023–2025)
3. Remap 2022+ crime classifications (47 categories → 16 standardized categories)
4. Pull ACS demographic data from the Census API for 2012–2023
5. Standardize ZIP code formats for joining
6. Build a SQLite database with 6 normalized tables
7. Run 3 SQL queries producing analytical DataFrames
8. Generate visualizations saved to the `plots/` folder
9. Run correlation matrix and linear regression models

---

## Python Functions

| Function | Description |
|---|---|
| `combine_crime_csvs(data_path)` | Combines 22 yearly CSVs into one DataFrame, extracts year from filename |
| `zip_code_lookup(zip_code)` | Returns crime and demographic data for a given Louisville ZIP code — prints summary stats and returns 3 DataFrames |
| `crime_rate_calculator(zip_code, classification, compare_zip)` | Returns total incident counts over time with optional classification filter and ZIP comparison line chart |

---

## Database Schema

The SQLite database (`lou_crime.db`) contains 6 tables. It is not committed to GitHub due to file size (316MB) but is generated by running the notebook.

| Table | Rows | Description |
|---|---|---|
| `crime_incidents` | 1,715,620 | Fact table — one row per incident |
| `crime_summary` | 11,992 | Aggregated incidents by ZIP, year, classification |
| `zip_code_demographics` | 387 | ACS data — one row per ZIP per year |
| `crime_type` | 16 | Lookup — standardized classifications |
| `crime_location` | 128 | Lookup — location categories |
| `lmpd_division` | 252 | Lookup — LMPD divisions and beats |

The database is normalized to eliminate redundancy — lookup tables store unique string values once and the fact table references them by integer ID. `crime_summary` pre-aggregates the 1.7M incident rows into counts by ZIP/year/classification, making analytical queries fast.

### Entity Relationship Diagram

![ERD](data/erd.png)

**Note:** `crime_summary` joins to `zip_code_demographics` on both `zip_code` and `year` as a composite key. The ERD shows the `zip_code` relationship only due to diagramming tool limitations.

---

## SQL Queries

Three intermediate SQL queries are documented in the notebook:

1. **Core Analytical Table** — Aggregation + Join — joins `crime_summary` to `zip_code_demographics`, calculates crime rate per 1,000 people per ZIP per year. This is the foundation DataFrame for all modeling and visualization.

2. **Crime Trends by Classification** — Aggregation + Join + Window Function — breaks crime down by offense type per ZIP per year. A window function (`PARTITION BY year`) calculates the citywide average crime rate alongside each row without collapsing the data like a GROUP BY would.

3. **ZIP Socioeconomic Profiles** — CTE + Join — uses two Common Table Expressions to calculate ZIP-level metrics and citywide averages, then assigns each ZIP a profile label for each year:
   - **High poverty / High crime** — above average on both metrics
   - **High poverty / Low crime** — elevated poverty but below average crime (opportunity zone)
   - **Low poverty / High crime** — low poverty but elevated crime (outlier)
   - **Low poverty / Low crime** — below average on both metrics

---

## Visualizations

All visualizations are saved as PNG files in the `plots/` folder when the notebook is run.

| File | Description |
|---|---|
| `01_overall_crime_trend.png` | Total Louisville crime incidents 2004–2025 |
| `02_top5_offenses_by_year.png` | Top 5 offense classifications trend lines 2004–2025 |
| `03_weapons_homicide_trend.png` | Weapons and homicide incidents by year |
| `04_crime_by_40202.png` | Total incidents by ZIP code 40202 |
| `04_crime_by_40211.png` | Total incidents by ZIP code 40211 |
| `05_total_crime_incidents.png` | Top 5 ZIP codes by total incidents 2012–2023 |
| `06_correlation_matrix.png` | Correlation heatmap of crime rate vs socioeconomic indicators |

---

## Analysis Results

### Correlation Matrix Highlights
- Poverty rate (+0.54) — strongest positive predictor of crime rate
- Mobility rate (−0.55) — strongest negative predictor (likely reflects residential population bias)
- Median household income (−0.40) and education (−0.32) show moderate negative correlations
- Unemployment rate (+0.18) — surprisingly weak relationship, suggesting poverty is a better predictor than employment status alone
- Single parent households and rent burden (0.78) are strongly correlated with each other, suggesting housing cost stress and family structure cluster in the same ZIP codes

### Regression Model
- **R² = 0.482** — five socioeconomic variables explain 48% of crime rate variation across ZIP codes
- Poverty rate and education level are the two meaningful independent predictors
- Unemployment and income show multicollinearity artifacts — they are too correlated with poverty rate to contribute independent signal in the model
- The remaining 52% of variation is attributed to unmeasured factors including policing levels, neighborhood history, community programs, and built environment

### National Context
Louisville's crime trends mirror national patterns documented in FBI data. The 1994 Violent Crime Control Act, the end of the crack cocaine epidemic, and income growth drove the long-term national decline beginning in the 1990s. Louisville's post-2016 decline aligns with a broader national trend. The 2020–2021 homicide spike was a national pattern driven by COVID-19 pandemic disruption rather than local policy failures. National data shows murder fell nearly 15% in 2024 — the largest single-year decline ever recorded — suggesting the post-COVID spike has largely reversed.

---

## Dependencies

- Python 3.9+
- pandas
- numpy
- matplotlib
- seaborn
- scikit-learn
- requests
- python-dotenv
- sqlite3 (built-in)
- jupyter

See `requirements.txt` for complete list with versions.

---

## AI Acknowledgement

AI tools (Claude by Anthropic) were used as a learning resource and supplemental tool throughout this project. AI assisted with debugging code errors, explaining concepts, suggesting approaches for Census API integration, SQLite schema design, SQL query construction, and interpretation of statistical results. All analytical decisions, research questions, data interpretation, and conclusions are the author's own work.

---

## Author

**Jon Larson** — Data Analyst
[GitHub](https://github.com/JonLar1/Lou_Crime_Data)