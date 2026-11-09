# Delhivery Feature Engineering

## Project Overview

Delhivery is one of India's leading logistics and supply chain services providers, using technology and data-driven solutions to optimize transportation and delivery operations.

This project focuses on analysing Delhivery's logistics data to understand trip-level operations, identify transportation patterns, and engineer meaningful features from raw trip and segment-level records.

The primary objective is to transform granular logistics data into a structured trip-level dataset, compare actual transportation metrics with OSRM (Open Source Routing Machine) estimates, and generate business insights that can help improve route planning and operational efficiency.

## Business Objectives

- Understand the structure and characteristics of the logistics dataset.
- Analyse trips involving multiple delivery segments and relay points.
- Aggregate segment-level information into complete trip-level records.
- Engineer meaningful features from source and destination locations.
- Extract geographical and time-based information from trip records.
- Identify and handle outliers in numerical variables.
- Compare actual transportation metrics with OSRM estimates.
- Analyse route types, geographical distribution, and trip patterns.
- Generate actionable recommendations to improve logistics operations.

## Dataset Overview

The dataset contains **144,867 records and 24 columns** representing logistics trips and their individual segments.

The dataset includes two types of records:

| Data Type | Records | Share |
|---|---:|---:|
| Training | 104,858 | 71.50% |
| Test | 40,009 | 28.50% |
| **Total** | **144,867** | **100%** |

### Important Features

| Feature | Description |
|---|---|
| data | Identifies training or test records |
| trip_uuid | Unique identifier for a trip |
| trip_creation_time | Timestamp when the trip was created |
| route_schedule_uuid | Unique route schedule identifier |
| route_type | Transportation route type (FTL or Carting) |
| source_name | Origin location |
| destination_name | Destination location |
| od_start_time | Start time of an operational delivery segment |
| od_end_time | End time of an operational delivery segment |
| start_scan_to_end_scan | Time between the first and last scan |
| actual_distance_to_destination | Actual distance travelled towards the destination |
| actual_time | Actual transportation time |
| osrm_distance | Distance estimated by OSRM |
| osrm_time | Time estimated by OSRM |
| segment_actual_time | Actual time taken for an individual segment |
| segment_osrm_distance | OSRM-estimated distance for an individual segment |
| segment_osrm_time | OSRM-estimated time for an individual segment |

## Tools & Technologies

- **Python**
- **Pandas & NumPy** – Data manipulation and feature engineering
- **Matplotlib & Seaborn** – Data visualization
- **SciPy** – Statistical and numerical operations
- **Scikit-learn** – StandardScaler for feature standardization
- **Jupyter Notebook** – Analysis and experimentation

## Project Workflow

### 1. Data Loading & Inspection

- Imported the required Python libraries.
- Loaded the Delhivery logistics dataset.
- Examined dataset dimensions, data types, and statistical summaries.
- Analysed training and test data distribution.
- Inspected numerical and categorical variables.
- Identified missing values and unnecessary columns.

### 2. Data Cleaning

- Removed records containing missing values.
- Removed columns containing unknown or non-useful information:
  - is_cutoff
  - cutoff_factor
  - cutoff_timestamp
  - factor
  - segment_factor
- Reset the dataframe index after cleaning.

### 3. Trip Segmentation & Aggregation

One of the key challenges in the dataset was that a single trip could contain multiple delivery segments or relay points.

To address this:

- Created a unique segment key using trip UUID, source center, and destination center.
- Calculated cumulative segment-level actual time, OSRM distance, and OSRM time.
- Aggregated segment-level information using appropriate aggregation functions.
- Used `first()` and `last()` to preserve the starting and ending locations and corresponding trip attributes.
- Grouped the records by `trip_uuid` to create a consolidated trip-level dataset.

**Dataset transformation:**

| Processing Stage | Records |
|---|---:|
| Original dataset | 144,867 |
| After removing missing records | 144,316 |
| After segment-level aggregation | 26,222 |
| After trip-level aggregation | 14,787 |

This transformation significantly reduced the dataset size while retaining the relevant trip-level information.

### 4. Time-Based Feature Engineering

Converted operational timestamp columns into Pandas datetime format.

Created the following feature:

- `od_time_diff_hour` – Time difference between operational start and end timestamps, calculated in minutes in the notebook.

Aggregated the time differences at trip level to represent the total duration across segments.

### 5. Location-Based Feature Engineering

The original source and destination fields contained a combination of city, state, and location codes.

Created custom Python functions to extract meaningful geographical information.

**Engineered features:**

| Feature | Description |
|---|---|
| source_state | State from which the trip originates |
| source_city | Origin city |
| source_place | Specific origin location |
| source_code | Origin location code |
| destination_state | Destination state |
| destination_city | Destination city |
| destination_place | Specific destination location |
| destination_code | Destination location code |

This enabled geographical analysis at city, state, and location-code levels.

### 6. Date & Time Feature Engineering

Extracted useful time-based features from `trip_creation_time`:

- `trip_year`
- `trip_month`
- `trip_hour`
- `trip_day`
- `trip_week`
- `trip_dayofweek`

These features helped analyse trip creation patterns across different time periods.

### 7. Outlier Detection & Treatment

- Identified numerical variables for outlier analysis.
- Used boxplots to visualize the distribution of numerical features.
- Applied the Interquartile Range (IQR) method to identify outliers.
- Removed records containing outliers in the selected numerical variables.
- Re-examined the distributions after treatment.

### 8. Categorical Encoding & Feature Scaling

**Categorical Encoding**
- Converted `route_type` into numerical values:
  - FTL = 0
  - Carting = 1

**Feature Standardization**
- Applied Scikit-learn's `StandardScaler` to the selected numerical features.
- Standardized the numerical variables to have comparable scales.

### 9. Exploratory Data Analysis & Business Insights

Analysed the processed trip-level dataset to understand:

- Distribution of FTL and Carting routes.
- Training and test data composition.
- Top source and destination cities.
- Most frequent source and destination location codes.
- State-wise distribution of trips.
- Trip creation patterns across different days.
- Route-type distribution across states and location codes.
- Differences between actual and OSRM-estimated transportation metrics.

## Key Findings

### 1. Route Type Distribution

- FTL accounts for **30.74%** of the processed trips.
- Carting accounts for **69.26%** of the processed trips.
- Carting is the dominant route type in the analysed dataset.

### 2. Relay Points & Trip Structure

- Individual trips can contain multiple segments and relay points.
- FTL trips show a greater number of relay points in the analysed data.
- Segment-level aggregation is important for understanding complete trip operations.

### 3. Geographical Distribution

The cities with the highest number of trips include:

- Bengaluru
- Mumbai
- Hyderabad
- Delhi
- Chennai
- Pune

The analysis also identified significant activity across major states, particularly:

- Maharashtra
- Karnataka
- Haryana

### 4. Source & Destination Locations

- Source codes D and HB account for a substantial share of originating trips.
- Destination codes D, HB, and H are among the most frequent destinations.
- Route-type distribution varies across different source and destination locations.

### 5. Trip Creation Patterns

- Trip creation activity is distributed relatively consistently throughout the month.
- Date and time-based features provide additional dimensions for analysing operational patterns.

### 6. Actual vs OSRM Transportation Metrics

- Actual distance and OSRM-estimated distance show noticeable differences.
- The analysis suggests that OSRM estimates do not always align with actual transportation metrics.
- This highlights an opportunity to review routing inputs and transportation assumptions.

## Business Recommendations

Based on the analysis, the following recommendations were identified:

1. **Review Routing Engine Accuracy:** Investigate discrepancies between actual transportation distance and OSRM-estimated distance to identify potential routing inefficiencies.

2. **Optimize Route Planning:** Review the information provided to the routing engine and verify whether route configurations are optimized for actual transportation conditions.

3. **Strengthen State-Level Operations:** Prioritize operational planning and resource allocation in Maharashtra and Karnataka, which show substantial trip activity.

4. **Improve Relay Point Management:** Analyse the number of relay points involved in FTL trips and identify opportunities to streamline transportation operations.

5. **City-Level Resource Planning:** Focus on high-activity cities such as Bengaluru, Mumbai, Hyderabad, Delhi, Chennai, and Pune when planning logistics resources.

6. **Route-Type Optimization:** Evaluate the operational differences between FTL and Carting routes to identify opportunities for improving resource utilization.

7. **Seasonal Resource Planning:** Consider festive-season demand and historical geographical activity when planning transportation capacity and ground resources.

8. **Continuous Performance Monitoring:** Regularly compare actual trip performance against routing estimates to identify deviations and improve operational planning.

## Key Learnings

Through this project, I strengthened my understanding of:

- Data cleaning and preprocessing.
- Handling missing values and unnecessary columns.
- GroupBy and aggregation techniques in Pandas.
- Cumulative aggregations using `cumsum()`.
- Converting segment-level data into trip-level records.
- Feature engineering using date, time, and geographical information.
- Custom Python functions for extracting structured location attributes.
- Outlier detection and treatment using the IQR method.
- Categorical variable encoding.
- Numerical feature standardization using StandardScaler.
- Exploratory Data Analysis and business insight generation.
- Translating logistics data into actionable operational recommendations.

## Repository Structure

```text
Delhivery-Feature-Engineering/
│
├── Business_Case_Delhivery_Feature_Engineering.ipynb
├── delhivery_data.csv
└── README.md
```

*Note: Include the dataset in the repository only if its distribution is permitted.*

## Conclusion

This project demonstrates how raw logistics data can be transformed into meaningful trip-level information through data cleaning, aggregation, and feature engineering.

The analysis highlights the importance of understanding multi-segment trips, geographical distribution, route types, and the differences between actual transportation metrics and routing-engine estimates.

The findings provide a foundation for improving route planning, optimizing resource allocation, and strengthening logistics operations through data-driven decision-making.

---

## Author

**Umar Reyaz**

Data Analyst | Data Science & AI/ML Enthusiast

- **LinkedIn:** https://www.linkedin.com/in/umar-reyaz-55a164272
- **GitHub:** https://github.com/umar16257-design
- **Email:** umar16257@gmail.com
