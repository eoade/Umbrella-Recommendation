## Umbrella Decision Assistant & Weather Tracker

A lightweight Python project that analyzes weather data, imputes missing rainfall metrics using linear interpolation, and integrates with the OpenWeather API to provide daily umbrella recommendations.

# Overview

This project consists of two core components:
 * Missing Data Handling & Decision Logic: Simulates a weekly weather dataset with missing values (NaN), estimates missing precipitation levels via linear interpolation, and generates a rule-based decision on whether to carry an umbrella.
 * Real-Time Weather Ingestion: Connects to the OpenWeatherMap API to fetch live weather metrics (temperature, cloud cover, 1-hour rainfall volume) for Osogbo, Nigeria, and runs the same recommendation rule on current conditions.

# Recommendation Rules

An umbrella recommendation evaluates to "Yes (Carry Umbrella)" if either of the following conditions is met:
 * Estimated or observed rainfall is strictly greater than 1.0 mm.
 * Cloud cover is 80% or higher.
Otherwise, the recommendation returns "No".

# Features

 * Linear Interpolation: Uses Pandas' .interpolate(method='linear') to estimate missing rainfall values based on surrounding observations, falling back to 0.0 mm where necessary.
 * Live API Integration: Fetches live weather conditions via OpenWeather's Current Weather Data API using standard metric units.
 * Secure Credential Handling: Utilizes Google Colab's userdata module to load API keys securely without exposing them in plaintext.

# Requirements
Ensure you have Python 3 installed along with the following libraries:
pip install pandas numpy requests

# Getting Started
1. Running the Imputation & Recommendation Model
To run the baseline dataset and see how missing rainfall values are filled and evaluated:
import pandas as pd
import numpy as np

# Sample weekly data with missing precipitation
data = {
    'Day': ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'],
    'Cloud_Cover_%': [80, 90, 30, 75, 85, 20, 95],
    'Rainfall_mm': [5.2, np.nan, 0.0, np.nan, 12.0, 0.0, np.nan]
}

df = pd.DataFrame(data)

# Estimate gaps using linear interpolation
df['Estimated_Rainfall_mm'] = df['Rainfall_mm'].interpolate(method='linear').fillna(0.0).round(2)

# Recommendation rule
def recommend_umbrella(row):
    if row['Estimated_Rainfall_mm'] > 1.0 or row['Cloud_Cover_%'] >= 80:
        return "Yes (Carry Umbrella)"
    return "No"

df['Umbrella_Recommendation'] = df.apply(recommend_umbrella, axis=1)
print(df)

2. Fetching Live Data (Google Colab)
 * Obtain a free API key from OpenWeatherMap.
 * In Google Colab, open the Secrets panel (the 🔑 icon in the sidebar).
 * Add a new secret with:
   * Name: OPENWEATHER_API_KEY
   * Value: <your_actual_api_key>
 * Toggle notebook access on for this key.
 * Run the live query script to fetch data and generate a recommendation for Osogbo, Nigeria.
Sample Output
Processed data with estimates and recommendations:
         Day  Cloud_Cover_%  Rainfall_mm  Estimated_Rainfall_mm Umbrella_Recommendation
0     Monday             80          5.2                    5.2    Yes (Carry Umbrella)
1    Tuesday             90          NaN                    2.6    Yes (Carry Umbrella)
2  Wednesday             30          0.0                    0.0                      No
3   Thursday             75          NaN                    6.0    Yes (Carry Umbrella)
4     Friday             85         12.0                   12.0    Yes (Carry Umbrella)
5   Saturday             20          0.0                    0.0                      No
6     Sunday             95          NaN                    0.0    Yes (Carry Umbrella)
