# Louisville Crime Report Analysis

Analysis of crime data from Louisville Metro Police Department (2004-2025). The goal of the project is to understand reported crime trends within the Louisville community by zip code and offense classifications. Further contextual analysis will determine key drivers of crime increases and decreases such as events, policies, and hiring.

## Setup Instructions

### Prerequisites
- Python 3.9 or higher
- pip

### Installation

```bash
# Clone the repository
git clone <your-repo-url>
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

### Running the Notebook

```bash
# Option 1: Using Jupyter Notebook
jupyter notebook notebooks/lou_crime_report.ipynb

# Option 2: In VS Code
# Open notebooks/lou_crime_report.ipynb and select the Python kernel
```

## Project Structure

```
lou_crime_report/
├── data/                       # Crime data CSV files (2004-2025)
│   ├── Crime_Data_2004.csv
│   ├── Crime_Data_2005.csv
│   └── ...
├── notebooks/                  # Jupyter notebooks
│   └── lou_crime_report.ipynb
├── venv/                       # Virtual environment (not in git)
├── .gitignore
├── requirements.txt
└── README.md
```

## Data Source

Crime data obtained from [Louisville Metro Open Data Portal](https://data.louisvilleky.gov/)

## Dependencies

- Python 3.9+
- pandas
- numpy
- matplotlib
- seaborn
- jupyter
- notebook

See `requirements.txt` for complete list.
