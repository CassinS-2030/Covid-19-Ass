# Covid-19-Ass
Data Loading and Initial Exploration
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# Load the dataset
df = pd.read_csv('owid-covid-data.csv')

# Initial exploration
print(f"Dataset shape: {df.shape}")
print("\nFirst 5 rows:")
display(df.head())
print("\nColumns:")
print(df.columns.tolist())
print("\nMissing values:")
print(df.isnull().sum().sort_values(ascending=False).head(20))
print("\nData types:")
print(df.dtypes)


#Data Cleaning
# Convert date to datetime
df['date'] = pd.to_datetime(df['date'])

# Select key columns and countries of interest
key_columns = ['date', 'location', 'total_cases', 'new_cases', 'total_deaths', 
               'new_deaths', 'total_vaccinations', 'people_vaccinated', 
               'population', 'life_expectancy']

countries = ['Kenya', 'United States', 'India', 'Brazil', 'Germany', 'South Africa']

# Filter data
clean_df = df[df['location'].isin(countries)][key_columns].copy()

# Handle missing values
for col in ['total_cases', 'total_deaths', 'total_vaccinations']:
    clean_df[col] = clean_df.groupby('location')[col].ffill().fillna(0)
    
# Calculate derived metrics
clean_df['death_rate'] = clean_df['total_deaths'] / clean_df['total_cases']
clean_df['cases_per_million'] = (clean_df['total_cases'] / clean_df['population']) * 1e6
clean_df['vaccination_rate'] = (clean_df['people_vaccinated'] / clean_df['population']) * 100

# Remove early dates with no cases
clean_df = clean_df[clean_df['date'] >= '2020-02-01']

