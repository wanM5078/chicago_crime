# Chicago Crime Dataset

## Introduction

This dataset reflects reported incidents of crime that occurred in the City of Chicago from 2001 to present, with the exception of murders where data exists for each victim. The data is extracted from the Chicago Police Department's Citizen Law Enforcement Analysis and Reporting system.

The dataset is publicly available for anyone to use under the following terms provided by the Dataset Source —https://data.cityofchicago.org — and is provided "AS IS" without any warranty, express or implied, from Google. Google disclaims all liability for any damages, direct or indirect, resulting from the use of the dataset.

## Exploratory Data Analysis

### Date range of dataset

```sql
SELECT
  MIN(date) AS earliest_date,
  MAX(date) AS latest_date
FROM
  `bigquery-public-data.chicago_crime.crime`;
```


### Case trend over the years

```sql
SELECT
  year,
  COUNT(*)
FROM
  `bigquery-public-data.chicago_crime.crime`
GROUP BY
  year
ORDER BY
  year;
```


### Total criminal cases grouped by its primary type

```sql
SELECT
  primary_type,
  COUNT(*) AS case_count,
  ROUND(COUNT(*)/SUM(COUNT(*)) OVER () * 100, 2) AS percentage
FROM
  `bigquery-public-data.chicago_crime.crime`
GROUP BY
  primary_type
ORDER BY
  case_count DESC;
 ```


### Most frequent crime locations, shown in total count and percentage

```sql
SELECT
  location_description,
  COUNT(*) AS case_count,
  ROUND(COUNT(*)/SUM(COUNT(*)) OVER () * 100, 2) AS percentage
FROM
  `bigquery-public-data.chicago_crime.crime`
WHERE
  location_description IS NOT NULL
GROUP BY
  location_description
ORDER BY
  case_count DESC;
 ```


### Time of day when crime mostly happens

```sql
SELECT
  EXTRACT(hour
  FROM
    date) AS hour_of_day,
  COUNT(*) AS crime_count
FROM
  `bigquery-public-data.chicago_crime.crime`
GROUP BY
  hour_of_day
ORDER BY
  crime_count DESC;
```


### Criminal case count by location

```sql
SELECT
  latitude,
  longitude,
  COUNT(*) AS num_crimes
FROM
  `bigquery-public-data.chicago_crime.crime`
WHERE
  latitude IS NOT NULL
  AND longitude IS NOT NULL
GROUP BY
  latitude,
  longitude
ORDER BY
  num_crimes DESC;
```

### Daily rate over multiple years for each of the top 3 crime types

```sql
SELECT
  EXTRACT(YEAR FROM date) AS year,
  COUNT(*) AS theft_count,
  COUNT(*) / 365 AS theft_daily_rate
FROM
  `bigquery-public-data.chicago_crime.crime`
WHERE
  primary_type = 'THEFT'
GROUP BY
  year
ORDER BY
  year ASC;


SELECT
  EXTRACT(YEAR FROM date) AS year,
  COUNT(*) AS battery_count,
  COUNT(*) / 365 AS battery_daily_rate
FROM
  `bigquery-public-data.chicago_crime.crime`
WHERE
  primary_type = 'BATTERY'
GROUP BY
  year
ORDER BY
  year ASC;


SELECT
  EXTRACT(YEAR FROM date) AS year,
  COUNT(*) AS battery_count,
  COUNT(*) / 365 AS battery_daily_rate
FROM
  `bigquery-public-data.chicago_crime.crime`
WHERE
  primary_type = 'CRIMINAL DAMAGE'
GROUP BY
  year
ORDER BY
  year ASC;
```



