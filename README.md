# Chicago Crime Dataset

## Introduction

This dataset reflects reported incidents of crime that occurred in the City of Chicago from 2001 to present, with the exception of murders where data exists for each victim. The data is extracted from the Chicago Police Department's Citizen Law Enforcement Analysis and Reporting system.

The dataset is publicly available for anyone to use under the following terms provided by the Dataset Source —https://data.cityofchicago.org — and is provided "AS IS" without any warranty, express or implied, from Google. Google disclaims all liability for any damages, direct or indirect, resulting from the use of the dataset.

The following are the columns available in the dataset:

| Column Name          | Description  |
|----------------------|--------------|
| unique_key           | Unique identifier for the record.                                                                                                                                                                                                                                                                                                                               |
| case_number          | The Chicago Police Department RD Number (Records Division Number), which is unique to the incident.                                                                                                                                                                                                                                                             |
| date                 | Date when the incident occurred. this is sometimes a best estimate.                                                                                                                                                                                                                                                                                             |
| block                | The partially redacted address where the incident occurred, placing it on the same block as the actual address.                                                                                                                                                                                                                                                 |
| iucr                 | The Illinois Unifrom Crime Reporting code. This is directly linked to the Primary Type and Description. See the list of IUCR codes at https://data.cityofchicago.org/d/c7ck-438e.                                                                                                                                                                               |
| primary_type         | The primary description of the IUCR code.                                                                                                                                                                                                                                                                                                                       |
| description          | The secondary description of the IUCR code, a subcategory of the primary description.                                                                                                                                                                                                                                                                           |
| location_description | Description of the location where the incident occurred.                                                                                                                                                                                                                                                                                                        |
| arrest               | Indicates whether an arrest was made.                                                                                                                                                                                                                                                                                                                           |
| domestic             | Indicates whether the incident was domestic-related as defined by the Illinois Domestic Violence Act.                                                                                                                                                                                                                                                           |
| beat                 | Indicates the beat where the incident occurred. A beat is the smallest police geographic area – each beat has a dedicated police beat car. Three to five beats make up a police sector, and three sectors make up a police district. The Chicago Police Department has 22 police districts. See the beats at https://data.cityofchicago.org/d/aerh-rz74. |
| district             | Indicates the police district where the incident occurred. See the districts at https://data.cityofchicago.org/d/fthy-xz3r.                                                                                                                                                                                                                                     |
| ward                 | The ward (City Council district) where the incident occurred. See the wards at https://data.cityofchicago.org/d/sp34-6z76.                                                                                                                                                                                                                                     |
| community_area       | Indicates the community area where the incident occurred. Chicago has 77 community areas. See the community areas at https://data.cityofchicago.org/d/cauq-8yn6.                                                                                                                                                                                               |
| fbi_code             | Indicates the crime classification as outlined in the FBI's National Incident-Based Reporting System (NIBRS). See the Chicago Police Department listing of these classifications at http://gis.chicagopolice.org/clearmap_crime_sums/crime_types.html.                                                                                                          |
| x_coordinate        | The x coordinate of the location where the incident occurred in State Plane Illinois East NAD 1983 projection. This location is shifted from the actual location for partial redaction but falls on the same block.                                                                                                                                               |
| y_coordinate        | The y coordinate of the location where the incident occurred in State Plane Illinois East NAD 1983 projection. This location is shifted from the actual location for partial redaction but falls on the same block.                                                                                                                                               |
| year                 | Year the incident occurred.                                                                                                                                                                                                                                                                                                                                     |
| updated_on           | Date and time the record was last updated.                                                                                                                                                                                                                                                                                                                      |
| latitude             | The latitude of the location where the incident occurred. This location is shifted from the actual location for partial redaction but falls on the same block.                                                                                                                                                                                                 |
| longitude            | The longitude of the location where the incident occurred. This location is shifted from the actual location for partial redaction but falls on the same block.                                                                                                                                                                                                |
| location             | The location where the incident occurred in a format that allows for creation of maps and other geographic


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



