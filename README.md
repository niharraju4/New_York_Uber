
---

# Uber Data Analysis Documentation

## Table of Contents
1. [Introduction](#introduction)
2. [Data Loading and Preprocessing](#data-loading-and-preprocessing)
3. [Data Cleaning](#data-cleaning)
4. [Data Analysis](#data-analysis)
5. [Visualization](#visualization)
6. [Combining Datasets](#combining-datasets)
7. [Additional Analysis](#additional-analysis)
8. [Results](#results)
9. [Conclusion](#conclusion)

## Introduction
This documentation provides a comprehensive analysis of Uber trip data in New York City. The analysis includes data loading, preprocessing, cleaning, and various visualizations to gain insights into the data.

## Data Loading and Preprocessing

### Loading Data
```python
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
import chart_studio.plotly as py
import plotly.graph_objs as go
import plotly.express as px
import folium
from plotly.offline import download_plotlyjs, init_notebook_mode, plot, iplot
import os

os.listdir(r"N:\datasets\Datasets")
df = pd.read_csv(r"N:\datasets\Datasets/uber-raw-data-janjune-15_sample.csv")
df
```

### Data Preprocessing
```python
df.duplicated()
df.duplicated().sum()
df.drop_duplicates(inplace=True)
df1 = df.copy()
df1.drop_duplicates(inplace=True)
df1.shape
df1
```

### Data Types and Null Values
```python
df1.dtypes
df1.isnull().sum()
df1.isnull()
```

### Converting Date and Time
```python
df1['Pickup_date'][0]
type(df1['Pickup_date'][0])
df1['Pickup_date'] = pd.to_datetime(df1['Pickup_date'])
df1['Pickup_date'].dtype
df1['Pickup_date'][0]
type(df1['Pickup_date'][0])
df1.dtypes
```

## Data Cleaning

### Removing Duplicates
```python
df1.drop_duplicates(inplace=True)
df1.shape
```

### Checking for Null Values
```python
df1.isnull().sum()
```

## Data Analysis

### Monthly Pickup Analysis
```python
df1['Pickup_date'].dt.month
df1['month'] = df1['Pickup_date'].dt.month_name()
df1['month'].value_counts()
df1['month'].value_counts().plot(kind='bar')
```

### Day and Hour Analysis
```python
df1['Pickup_date'].dt.day_name()
df1['Pickup_date'].dt.hour
df1['Pickup_date'].dt.minute
df1.head(5)
df1.tail(5)
df1['Day_of_Week'] = df1['Pickup_date'].dt.day_name()
df1['weekday'] = df1['Pickup_date'].dt.day_name()
pivot = pd.crosstab(index=df1['month'], columns=df1['weekday'])
pivot
pivot.plot(kind='bar', figsize=(8,6))
pivot.plot(kind='line', figsize=(8,6))
```

### Rush Hour Analysis
```python
df1['hour'] = df1['Pickup_date'].dt.hour
summary = df1.groupby(['weekday', 'hour'], as_index=False).size()
plt.figure(figsize=(8, 6))
sns.pointplot(x="hour", y="size", hue="weekday", data=summary)
plt.show()
```

## Visualization

### Box Plot and Violin Plot
```python
df_foil = pd.read_csv(r'N:\datasets\Datasets\Uber-Jan-Feb-FOIL.csv')
init_notebook_mode(connected=True)
px.box(x='dispatching_base_number', y='active_vehicles', data_frame=df_foil)
px.violin(x='dispatching_base_number', y='active_vehicles', data_frame=df_foil)
```

## Combining Datasets

### Concatenating Files
```python
files = os.listdir(r"N:\datasets\Datasets")[-9:-1]
files.remove('uber-raw-data-janjune-15.csv')
files.remove('uber-raw-data-janjune-15_sample.csv')
final_df = pd.DataFrame()
path = r"N:\datasets\Datasets"

for file in files:
    current_df = pd.read_csv(path+'/'+file)
    final_df = pd.concat([current_df, final_df])
final_df.shape
```

### Cleaning Combined Data
```python
final_df.duplicated().sum()
final_df.drop_duplicates(inplace=True)
final_df.shape
```

## Additional Analysis

### Rush Locations in New York City
```python
rush_uber = final_df.groupby(['Lat','Lon'], as_index=False).size()
basemap = folium.Map()
from folium.plugins import HeatMap
HeatMap(rush_uber).add_to(basemap)
basemap
```

### Rush Hour Analysis by Day and Hour
```python
final_df['Date/Time'] = pd.to_datetime(final_df['Date/Time'], format="%m/%d/%Y %H:%M:%S")
final_df['day'] = final_df['Date/Time'].dt.day
final_df['hour'] = final_df['Date/Time'].dt.hour
pivot1 = final_df.groupby(['day','hour']).size().unstack()
pivot1.style.background_gradient()
```

### Function for Pivot Table
```python
def get_pivot_table(df, col1, col2):
    pivot1 = final_df.groupby([col1, col2]).size().unstack()
    return pivot1.style.background_gradient()
```

## Results

### Data Loading and Preprocessing Results
- Loaded dataset from `N:\datasets\Datasets/uber-raw-data-janjune-15_sample.csv`.
- Initial shape of the dataset: `df.shape`.

### Data Cleaning Results
- Removed duplicates: `df1.shape`.
- Checked for null values: `df1.isnull().sum()`.

### Data Analysis Results
- Monthly pickup analysis:
  ```python
  df1['month'].value_counts().plot(kind='bar')
  ```
- Day and hour analysis:
  ```python
  pivot.plot(kind='bar', figsize=(8,6))
  pivot.plot(kind='line', figsize=(8,6))
  ```
- Rush hour analysis:
  ```python
  plt.figure(figsize=(8, 6))
  sns.pointplot(x="hour", y="size", hue="weekday", data=summary)
  plt.show()
  ```

### Visualization Results
- Box plot and violin plot for active vehicles:
  ```python
  px.box(x='dispatching_base_number', y='active_vehicles', data_frame=df_foil)
  px.violin(x='dispatching_base_number', y='active_vehicles', data_frame=df_foil)
  ```

### Combining Datasets Results
- Concatenated multiple files: `final_df.shape`.
- Removed duplicates from the combined dataset: `final_df.shape`.

### Additional Analysis Results
- Rush locations in New York City:
  ```python
  basemap = folium.Map()
  HeatMap(rush_uber).add_to(basemap)
  basemap
  ```
- Rush hour analysis by day and hour:
  ```python
  pivot1.style.background_gradient()
  ```

## Conclusion
This analysis provides insights into Uber trip data in New York City, including monthly pickup trends, rush hour analysis, and visualizations of active vehicles. The combined dataset allows for a more comprehensive analysis of rush locations and times.

---
