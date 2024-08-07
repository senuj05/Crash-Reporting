# Data Science DISCOVERY Final Project - README

Welcome to the Data Science DISCOVERY Project2! This project focuses on analyzing the "Crash Reporting - Drivers Data" dataset from Montgomery County, Maryland. This README file will guide you through the project overview, features, setup instructions, and the various analyses conducted.

## Table of Contents

1. [Project Overview](#project-overview)
2. [Data Source](#data-source)
3. [Dataset](#dataset)
4. [Installation](#installation)
5. [Loading the Dataset](#loading-the-dataset)
6. [Exploratory Data Analysis](#exploratory-data-analysis)
7. [Exploratory Data Visualization](#exploratory-data-visualization)
8. [Data Science Question](#data-science-question)
9. [Overall Summary](#overall-summary)
10. [Contact](#contact)

## Project Overview

In this project, we analyze the "Crash Reporting - Drivers Data" dataset from Montgomery County, Maryland. The goal is to understand the factors that contribute to road accidents, which can help in finding measures to reduce them. This dataset provides valuable information on various aspects of road accidents, including details about drivers, vehicles, and the circumstances surrounding each accident.

## Data Source

**Data Source**: [Crash Reporting - Drivers Data](https://data.montgomerycountymd.gov/api/views/mmzv-x632/rows.csv?accessType=DOWNLOAD)

This dataset provides details on the drivers of motor vehicles involved in crashes on county and municipal roads. The data is gathered by the Maryland State Police's Automated Crash Reporting System (ACRS) and reported by the Montgomery County, Gaithersburg, Rockville, and Maryland-National Capital Park police departments.

## Dataset

This dataset is of particular interest because of its relevance to current road safety concerns. It contains detailed information about traffic collisions on local roads and in Montgomery County, providing insights into factors that contribute to accidents. The dataset is updated weekly and is considered reliable as it is gathered by multiple police departments.

## Installation

To set up the project, follow these steps:

1. **Clone the Repository**
    ```bash
    git clone https://github.com/yourusername/data-science-discovery-project2.git
    cd data-science-discovery-project2
    ```

2. **Create a Virtual Environment and Activate It**
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows use `venv\Scripts\activate`
    ```

3. **Install Required Python Packages**
    ```bash
    pip install -r requirements.txt
    ```

## Loading the Dataset

To load the dataset into a DataFrame for analysis, use the following code:

```python
import pandas as pd

crash_data = 'Crash_Reporting_-_Drivers_Data.csv'
df = pd.read_csv(crash_data, low_memory=False)
df.head()
```

## Exploratory Data Analysis

### Time Analysis

We analyze the 'Crash Date/Time' data to determine the most common times for crashes.

#### AM Crashes

```python
df['Crash Date/Time'] = pd.to_datetime(df['Crash Date/Time'])
crash_date_time_am_df = df.loc[df['Crash Date/Time'].dt.hour < 12, ['Crash Date/Time']]
crash_date_time_am_df.head()
```

#### PM Crashes

```python
df['Crash Date/Time'] = pd.to_datetime(df['Crash Date/Time'])
crash_date_time_pm_df = df.loc[df['Crash Date/Time'].dt.hour >= 12, ['Crash Date/Time']]
crash_date_time_pm_df.head()
```

### Speed Limit Analysis

We examine the relationship between reported crashes and speed limits.

#### High-Speed Crashes

```python
high_speed_crashes_df = df[df['Speed Limit'] > 35]
high_speed_crashes_df.head()
```

#### Low-Speed Crashes

```python
low_speed_crashes_df = df[df['Speed Limit'] <= 35]
low_speed_crashes_df.head()
```

### Vehicle Make Analysis

We analyze the frequency of crashes by 'Vehicle Make'.

```python
vehicle_make_counts = df['Vehicle Make'].value_counts()
vehicle_make_counts_df = vehicle_make_counts.reset_index()
vehicle_make_counts_df.columns = ['Vehicle Make', 'Number of Crashes']
vehicle_make_counts_df.head()
```

### Vehicle Year Analysis

#### Older Vehicles (Before 2013)

```python
df['Vehicle Year'] = pd.to_numeric(df['Vehicle Year'], errors='coerce')
older_vehicles_df = df[df['Vehicle Year'] < 2013]
older_vehicle_crashes = older_vehicles_df['Vehicle Year'].value_counts()
older_vehicle_crashes_df = older_vehicle_crashes.reset_index()
older_vehicle_crashes_df.columns = ['Vehicle Year', 'Number of Crashes']
older_vehicle_crashes_df.head()
```

#### Newer Vehicles (2013 and After)

```python
newer_vehicles_df = df[(df['Vehicle Year'] >= 2013) & (df['Vehicle Year'] <= 2023)]
newer_vehicle_crashes = newer_vehicles_df['Vehicle Year'].value_counts()
newer_vehicle_crashes_df = newer_vehicle_crashes.reset_index()
newer_vehicle_crashes_df.columns = ['Vehicle Year', 'Number of Crashes']
newer_vehicle_crashes_df.head()
```

## Exploratory Data Visualization

### AM vs. PM Crashes

```python
df['AM/PM'] = df['Crash Date/Time'].dt.hour.apply(lambda x: 'AM' if x < 12 else 'PM')
am_pm_counts = df['AM/PM'].value_counts()
am_pm_counts_df = am_pm_counts.reset_index()
am_pm_counts_df.columns = ['AM/PM', 'Number of Crashes']
am_pm_counts_df.plot.bar(x='AM/PM', y='Number of Crashes', figsize=(10, 6))
```

### Crashes by Vehicle Year (2013-2023)

```python
newer_vehicle_crashes_df.plot.bar(x='Vehicle Year', y='Number of Crashes', figsize=(14, 7))
```

## Data Science Question

An intriguing question we explore is whether there is a significant correlation between speed limits and the number of accidents.

### Hypothesis

- **Null Hypothesis (H0)**: There is no correlation between the speed limit and the frequency of accidents.
- **Alternative Hypothesis (H1)**: There is a significant correlation between the speed limit and the frequency of accidents.

### Analysis

```python
import scipy.stats as stats

crash_counts_by_speed = df.groupby('Speed Limit').size().reset_index(name='Crash Counts')
crash_counts_by_speed['Speed Limit'] = pd.to_numeric(crash_counts_by_speed['Speed Limit'], errors='coerce')
crash_counts_by_speed['Crash Counts'] = pd.to_numeric(crash_counts_by_speed['Crash Counts'], errors='coerce')
crash_counts_by_speed.dropna(subset=['Speed Limit', 'Crash Counts'], inplace=True)

correlation_coefficient, p_value = stats.pearsonr(crash_counts_by_speed['Speed Limit'], crash_counts_by_speed['Crash Counts'])
print(f"Correlation Coefficient: {correlation_coefficient}")
print(f"P-value: {p_value}")

alpha = 0.05
if p_value < alpha:
    print("We reject the null hypothesis and conclude that there is a significant correlation between speed limit and the frequency of accidents.")
else:
    print("We fail to reject the null hypothesis and conclude that there is no significant correlation between speed limit and the frequency of accidents.")
```

### Results

We fail to reject the null hypothesis and conclude that there is no significant correlation between speed limit and the frequency of accidents.

## Overall Summary

In this project, we analyzed the "Crash Reporting - Drivers Data" dataset from Montgomery County, Maryland, to examine various factors affecting traffic accidents. The analysis revealed no significant correlation between speed limits and the frequency of accidents. Additionally, we found that newer vehicle models are involved in fewer accidents compared to older models, and there is a higher chance of accidents occurring during the PM compared to AM. The data visualization highlighted these trends, providing valuable insights for future policy decisions and public awareness initiatives.

## Contact

For any questions or feedback, please contact:

- Name: Senuvi Jayasinghe
- Email: senuj2@illinois.edu

Thank you for exploring the Data Science DISCOVERY Project2! We hope this analysis provides valuable insights into road safety.
