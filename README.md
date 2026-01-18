# Louisville Crime Report Analysis

Analysis of crime data from Louisville Metro Police Department (2004-2025). The goal of the project is to understand reported crime trends within the Louisville community by zip code and offense classifications. Further contextual analysis will determine key drivers of crime increases and decreases such as events, policies, and hiring.

## Setup Instructions

### Prerequisites
- Python 3.9 or higher
- pip

### Installation

```bash
# Clone the repository
git clone <https://github.com/JonLar1/Lou_Crime_Data>
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

## Objectives
In 2003 Jefferson County merged with the city of Louisville, KY to become Louisville Metro Government resulting in the police force combining into one department across the entire county thus reporting all crime in standardized output.

I'd like to understand in depth how crime has changed over time, what the key drivers are, how has crime impacted the community, and determine what are the most effective policies for crime deterrence.

## Continuing Questions
* What is the most dangerous zip codes for Weapons and Drug/Alcohol Violations? Show on a map.
* What are the key drivers of crime detterence and impacts on community? (correlations between count of police officers, events/gov't policies. Join census data by zip code)

## Conclusions
* Total reported crime has decreased since 2004 driven primarily by a decrease in Drug/Alcohol Violations and Burglary while Weapons related crime continued to rise from 2004 to 2021. 


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

## Author
Jon Larson - Data Analyst
