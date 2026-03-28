# Louisville Crime Analysis 2004–2025

Analysis of crime data from the Louisville Metro Police Department combined with 
U.S. Census Bureau American Community Survey (ACS) socioeconomic data to identify 
key drivers of crime trends across Louisville ZIP codes over a 20-year period.

## Project Overview

In 2003 Jefferson County merged with the City of Louisville to become Louisville 
Metro Government, combining police forces into one department reporting standardized 
crime data across the entire county. This project leverages that unified dataset 
to investigate the relationship between crime trends and socioeconomic conditions 
across Louisville ZIP codes from 2004–2025.

**Primary Research Questions:**
- What socioeconomic factors are most strongly associated with crime rates across 
  Louisville ZIP codes over time?
- Are there ZIP codes where crime is declining despite unfavorable socioeconomic 
  indicators — or vice versa?
- Are national or local policies and events key drivers of changes in crime rates?

---

## Key Findings

- Total reported crime has decreased since 2004, driven primarily by declines in 
  Drug/Alcohol Violations and Burglary
- Weapons-related crime rose consistently from 2004 to 2021
- Poverty rate and single parent households show the strongest positive correlation 
  with crime rate per 1,000 people across ZIP codes
- Several ZIP codes exhibit a High Poverty / Low Crime profile, suggesting factors 
  beyond socioeconomic conditions influence local crime rates
- The analytical universe covers 32 Louisville ZCTAs with matching Census demographic 
  data — approximately 250 additional ZIP values in the raw data were excluded as 
  invalid, out-of-jurisdiction, or data entry errors

---

## Data Sources

| Dataset | Source | Years | Granularity |
|---|---|---|---|
| Louisville Metro Crime Data | [Louisville Metro Open Data](https://data.louisvilleky.gov/) | 2004–2025 | One row per incident |
| U.S. Census ACS 5-Year Estimates | [Census Bureau API](https://www.census.gov/data/developers.html) | 2012–2023 | One row per ZIP per year |

**Notes:**
- ACS 5-year estimates are rolling averages — a 2022 estimate covers 2018–2022.
  ACS ZIP-level data is only available from 2012 onward, limiting the joined 
  analysis to 2012–2023.
- Socioeconomic data was available for 2012–2023 only. Crime data spans 2004–2025 
  but socioeconomic context is limited to the overlapping years.
- Offense Classification remapping: In 2022 Louisville Metro expanded crime 
  classifications from 16 to 47 categories. To maintain continuity across the full 
  2004–2025 timeline, the 47 post-2022 classifications were remapped back to the 
  original 16 standardized categories.

---

## Repository Structure
```
lou_crime_report/
├── initial_lou_crime_data/     # Raw yearly crime CSVs from Louisville Metro
│   ├── Crime_Data_2004.csv
│   ├── ...
│   └── Crime_Data_2025.csv
├── notebooks/
│   ├── lou_crime_capstone.ipynb    # Main analysis notebook
│   └── data/
│       └── louisville_demographics.csv  # ACS data output
├── data/
│   └── erd.png                 # Entity relationship diagram
├── .env                        # Census API key (not committed)
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
jupyter notebook notebooks/lou_crime_capstone.ipynb

# Option 2: VS Code
# Open notebooks/lou_crime_capstone.ipynb and select the venv Python kernel
```

Run all cells top to bottom. The notebook will:
1. Load and combine all 22 yearly crime CSVs
2. Standardize headers and remap crime classifications
3. Pull ACS demographic data from the Census API
4. Build a SQLite database with 6 normalized tables
5. Run 3 SQL queries producing analytical DataFrames
6. Generate visualizations and correlation/regression models

---

## Python Functions

| Function | Description |
|---|---|
| `combine_crime_csvs(data_path)` | Combines 22 yearly CSVs into one DataFrame, extracts year from filename |
| `zip_code_lookup(zip_code)` | Returns crime and demographic data for a given Louisville ZIP code |
| `crime_rate_calculator(zip_code, classification, compare_zip)` | Calculates crime rate per 1,000 people with optional classification filter and ZIP comparison chart |

---

## Database Schema

The SQLite database (`lou_crime.db`) contains 6 tables:

| Table | Rows | Description |
|---|---|---|
| `crime_incidents` | 1,715,620 | Fact table — one row per incident |
| `crime_summary` | 11,992 | Aggregated incidents by ZIP, year, classification |
| `zip_code_demographics` | 387 | ACS data — one row per ZIP per year |
| `crime_type` | 16 | Lookup — standardized classifications |
| `crime_location` | 128 | Lookup — location categories |
| `lmpd_division` | 252 | Lookup — LMPD divisions and beats |

### Entity Relationship Diagram

![ERD](data/erd.png)

**Note:** `crime_summary` joins to `zip_code_demographics` on both `zip_code` 
and `year` as a composite key. The ERD shows the `zip_code` relationship only 
due to diagramming tool limitations.

---

## SQL Queries

Three intermediate SQL queries are documented in the notebook:

1. **Core Analytical Table** — Aggregation + Join — crime rate per 1,000 joined 
   with all socioeconomic indicators by ZIP and year
2. **Crime Trends by Classification** — Aggregation + Join + Window Function — 
   incident counts by crime type compared against citywide averages
3. **ZIP Socioeconomic Profiles** — CTE + Join — assigns each ZIP a profile label 
   (High poverty/High crime, High poverty/Low crime, etc.) based on comparison 
   to citywide averages

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

AI tools (Claude by Anthropic) were used as a learning resource and supplemental 
tool throughout this project. AI assisted with debugging code errors, explaining 
concepts, suggesting approaches for Census API integration, SQLite schema design, 
and SQL query construction. All analytical decisions, research questions, data 
interpretation, and conclusions are the author's own work.

---

## Author

**Jon Larson** — Data Analyst  
[GitHub](https://github.com/JonLar1/Lou_Crime_Data)