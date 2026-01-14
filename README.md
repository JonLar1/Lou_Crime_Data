# Louisville Crime Report Analysis

Analysis of crime data from Louisville Metro Police Department (2003-2025).

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
# venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Install Jupyter kernel (if using VS Code or Jupyter)
python -m ipykernel install --user --name=lou_crime_report --display-name="Python (lou_crime_report)"

# Start Jupyter Notebook
jupyter notebook
```

### Running the Analysis

1. After activating the virtual environment, open the notebook:
   ```bash
   jupyter notebook notebooks/lou_crime_report.ipynb
   ```

2. In VS Code:
   - Open `notebooks/lou_crime_report.ipynb`
   - Select the kernel: `Python (lou_crime_report)`
   - Run all cells

## Project Structure

```
lou_crime_report/
├── data/                       # Crime data CSV files (2003-2025)
├── notebooks/                  # Jupyter notebooks with analysis
│   └── lou_crime_report.ipynb
├── venv/                       # Virtual environment (not in git)
├── requirements.txt            # Python dependencies
└── README.md                   # This file
```

## Data Source

Crime data obtained from [Louisville Metro Open Data Portal](https://data.louisvilleky.gov/)

## Dependencies

- Python 3.9+
- pandas 1.3.4
- numpy 1.20.3
- matplotlib 3.4.3
- jupyter
- notebook

See `requirements.txt` for complete list.

## Deactivating the Environment

When you're done working:
```bash
deactivate
```
