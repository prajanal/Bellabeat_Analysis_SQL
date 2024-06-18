# Case Study: Bellabeat Analysis
# Author: Prajana Luitel
# Date: 6-17-2024
# Emmail: Prajana.luitel@gmail.com

# Introduction:

In this case study, I analyzed data from Bellabeat, a smart device company based in San Francisco. I looked at the data to answer key business questions and developed actionable insights.

# Ask:
* How can we better market our smart devices based on user trends?
* What are some trends in smart device usage?
* How could these trends apply to Bellabeat customers?

  # Prepare:
This data set does have its limitations. It was collected from 2016-03-12 to 2016-05-12 and had a participant base of 30. 
* I  looked at Four of the tables from this dataset
  * dailyActivity_merged,
  *  hourlyIntensities_merged,
  *  hourlySteps_merged,
  *   hourlyCalories_Merged.

# Process:
I choose to work in BigQuery.

**uploaded the datasets to BigQuery under the project “mturkfitbit_export”.**

I uploaded four datasets into BigQuery and renamed the dataset as:
* dailyActivity_merged as Dailyactivity_3_12,
* hourlyCalories_merged as hourly_calories_merged,
 * hourlyIntensities_merged as hourly_intensities, 
* hourlySteps_merged as hourly_steps_merged

# Analyze and Share
**Identifying distinct id in different dataset**

*Insight: It is aimed at providing a clear count of unique users in the specified dataset, which is essential for understanding user engagement, ensuring data quality.*

* SELECT count (DISTINCT I
 FROM `mturkfitbit_export.Dailyactivity_3_12`;


* SELECT count (DISTINCT Id)
 FROM `mturkfitbit_export.hourly_calories_merged`;


* SELECT count (DISTINCT Id)
  FROM `mturkfitbit_export.hourly_intensities`;


* SELECT count (DISTINCT Id)
  FROM `mturkfitbit_export.hourly_steps_merged`;
  
# Transforming hourly data to daily data and creating a new table.
**From Hourly_step_merged to Daily_steps_merged.**

*Insight: It is designed to aggregate hourly step data into daily summaries, creating a new table that facilitates easier and more meaningful analysis of user activity patterns.*

* Create table `mturkfitbit_export.Daily_steps_merged` as
  SELECT
  Id,
  DATE(ActivityHour) AS date,
  SUM(StepTotal) AS daily_sum_StepTotal,
  AVG(StepTotal) AS daily_avg_StepTotal,
   MAX(StepTotal) AS daily_max_StepTotal,
   MIN(StepTotal) AS daily_min_StepTotal
  FROM
   `mturkfitbit_export.hourly_steps_merged`  GROUP BY
  Id, DATE(ActivityHour)
  ORDER BY
  date, Id;

**From Hourly_calories_merged to Daily_calories_merged.**
* Create table `mturkfitbit_export.daily_calories_merged` as
  SELECT
  Id,
  DATE(ActivityHour) AS date,
  SUM(Calories) AS daily_sum_daily_calories,
  AVG(Calories) AS daily_avg_calories,
  MAX(Calories) AS daily_max_calories,
  MIN(Calories) AS daily_min_caloriesl
  FROM
  `mturkfitbit_export.hourly_calories_merged`
  GROUP BY
 Id, DATE(ActivityHour)
  ORDER BY
  date, Id;
  
**From Hourly_intensities to daily_intensities.**
* Create table `mturkfitbit_export.daily_intensities` as
    SELECT
    Id,
    DATE(ActivityHour) AS date,
    SUM(TotalIntensity) AS daily_sum_daily_TotalIntensity,
     AVG(TotalIntensity) AS daily_avg_TotalIntensity,
     MAX(TotalIntensity) AS daily_max_TotalIntensity,
     MIN(TotalIntensity) AS daily_min_TotalIntensity
     FROM
      `mturkfitbit_export.hourly_intensities`
     GROUP BY
      Id, DATE(ActivityHour)
      ORDER BY
      date, Id;

# using inner join and adding a new column day_of_week.

**Joining all four files together to get a new table with sum_calories daily_steps and sum of Intensities.**

  Create table `mturkfitbit_export.combined table1` as
SELECT
t1.Id,TotalDistance,TrackerDistance,

t1.ActivityDate as date1,

FORMAT_DATE('%A', t1.ActivityDate) AS day_of_week,

t2.daily_sum_daily_calories as sum_calories,

t3.daily_sum_StepTotal as daily_steps,

t4.daily_sum_daily_TotalIntensity as sum_inten

FROM
   `mturkfitbit_export.Dailyactivity_3_12` AS t1
   
INNER JOIN
   `mturkfitbit_export.daily_calories_merged` AS t2
   
ON
  t1.id = t2.id and t1.ActivityDate=t2.date
  
INNER JOIN
  `mturkfitbit_export.Daily_steps_merged` AS t3
  
ON
  t1.id = t3.id and t1.ActivityDate=t3.date
INNER JOIN
`mturkfitbit_export.daily_intensities` AS t4

on
  t1.id =t4.id and t1.ActivityDate=t4.date













