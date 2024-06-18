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
**Data Source**

https://www.kaggle.com/datasets/arashnic/fitbit

*This Kaggle data set contains personal fitness tracker from thirty fitbit users. 
Thirty eligible Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring.
It includes information about daily activity, steps, and heart rate that can be used to explore users’ habits*
  

**Dataset Limitations**

* Time Frame: The data was collected from March 12, 2016, to May 12, 2016.
  
* Sample Size: Only 30 participants, which is too small for a comprehensive analysis.
  
* Outdated Data: The data is quite old and lacks current relevance.
  
* Lack of External Comparison: No external data is available for market comparison.
  
  *This study aims to demonstrate my analytical abilities and It offers general insights rather than detailed conclusions, considering the dataset's limitations.*
  
* I  looked at Four of the tables from this dataset

  * dailyActivity_merged,
  *  hourlyIntensities_merged,
  *  hourlySteps_merged,
  *   hourlyCalories_Merged.

# Process:
I choose to work in BigQuery.

**Uploaded the datasets to BigQuery under the project “mturkfitbit_export”.**

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

# Using Inner Join and adding a New column day_of_week.

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

# How often individual user are using the tracker

*INSIGHTS: I wanted to see how many times each user uses the tracker

 SELECT Id, COUNT (Id) AS Total_Id FROM `mturkfitbit_export.combined table1`
  GROUP BY Id ORDER BY Total_Id DESC;

# This query categorizes users based on their usage frequency, counts the number of users in each category, and provides a summarized view of the data for further analysis

* Create table `mturkfitbit_export.use distribution`as
  
  SELECT
  
  CASE
  
  WHEN Total_Id < 5 THEN 'uses less than 5'
  
  WHEN Total_Id >= 5 AND Total_Id < 10 THEN 'uses between 5 and 10'
  
  WHEN Total_Id >= 10 AND Total_Id < 15 THEN 'uses between 10 and 15'
  
  WHEN Total_Id >= 15 AND Total_Id < 20 THEN 'uses between 15 and 20'
  
  WHEN Total_Id >= 20 AND Total_Id < 25 THEN 'uses between 20 and 25'
  
  ELSE 'uses greater than or equal to 25'
  
  END AS interval1,
  
  COUNT(Total_Id) AS uses_count
  
  FROM (
  
  SELECT
  
  Id,
  
  COUNT(Id) AS Total_Id
  
  FROM
  
  `mturkfitbit_export.combined table1`
  
  GROUP BY
  
  Id)
  
  GROUP BY
  
  interval1
  
  ORDER BY
  
  interval1;


# Average, Min , Max steps of individual person

* select
  
  ActivityDate,
  
  min(TotalSteps) as min_step,
  
  max(TotalSteps) as max_step,
  
  avg(TotalSteps) as avg_steps
  
  from `mturkfitbit_export.Dailyactivity_3_12`
  
  group by
  
  ActivityDate;

# Extracting day-of week from date 1 column

* SELECT
  
  FORMAT_DATE('%A', date1) AS day_of_week,
  
  COUNT(*) AS total_count
  
  FROM
  
  `mturkfitbit_export.combined table1`
  
  GROUP BY
  
  day_of_week
  
  ORDER BY
  
  day_of_week;

  # Insights: I wanted to see what is daily average intensities*

* select
  
  FORMAT_DATE('%A', date1) AS day_of_week,
  
  avg(sum_Inten) as Total_intensity
  
  FROM
  
  `mturkfitbit_export.combined table1`
  
  GROUP BY
  
  day_of_week
  
  order by
  
  Total_intensity desc; 

# created new table adding days as days of week in dailyactivity.

* Create table `mturkfitbit_export.Dailyactivity_3_12_days`as

  SELECT *, FORMAT_DATE('%A', ActivityDate) AS day_of_week,
  
  FROM
  
  `mturkfitbit_export.Dailyactivity_3_12`

  
  # Insights: I wanted to determine if people were more active on a certain day of the week.*
  
  
* SELECT  day_of_week,

  ROUND (avg(VeryActiveMinutes), 2) AS Avg_Very_Active_Minutes,
  
  ROUND (avg(FairlyActiveMinutes), 2) AS Avg_Fairly_Active_Minutes,

  ROUND (avg(LightlyActiveMinutes), 2) AS Avg_Lightly_Active_Minutes,

  ROUND (avg(SedentaryMinutes), 2) AS Avg_Sedentary_Minutes,

  FROM

  `mturkfitbit_export.Dailyactivity_3_12_days`

  GROUP BY

  day_of_week;




# Relationship between Steps and cCalories burned

* SELECT
  
  date1,

  AVG(daily_Steps) AS Avg_Total_Steps,
  
   FROM

  `mturkfitbit_export.combined table1`
 
   group by

  date1;
# Relationship between Total distance and Calories burned

* SELECT
 
 date1,
 
 AVG(TotalDistance) AS Avg_Total_Distance,
 
 FROM
 
 `mturkfitbit_export.combined table1`
 
 group by
 
 date1;

# Share:
https://public.tableau.com/authoring/Bellabeat_Visualization_17187237941670/Dashboard1#3

# Act
**Through my analysis I was able to uncover the following:**

* Sedentary Behavior Dominance:
  
The data reveals that users predominantly spend most of their time in the sedentary category, indicating a potential lack of physical activity among the user population.

* Daily Usage Frequency:
  
Only two out of 34 users are consistently using the device every day, suggesting that daily usage is not common among the majority of users.

* Monthly Usage Duration:
  
A significant portion, 85% of users, utilize the device for less than 15 days in a month. This indicates that a large proportion of users may not be consistently engaging with the device or maintaining a regular exercise routine.

* Steps and Calorie Burn:
  
There is a positive correlation between the number of steps taken and the calories burned. This emphasizes the importance of physical activity in calorie expenditure and overall health.

* Weekday vs. Weekend Activity Patterns:
  
Weekdays, from Monday to Friday, exhibit lower levels of very active, fairly active, and lightly active minutes compared to weekends (Saturday and Sunday). This shift suggests that users may have more leisure time and engage in higher levels of physical activity during weekends.


# Recommendations:

* Promoting Physical Activity:

  Encouraging users to reduce sedentary behavior and increase physical activity levels can have significant health benefits. Implementing reminders or challenges to increase movement throughout the day may be beneficial.

* Enhancing User Engagement:

  Strategies to increase daily usage frequency and promote consistent use of the device should be explored. Providing incentives or rewards for regular use may help in sustaining user engagement.

* Targeted Interventions:

  Understanding user demographics and preferences can inform targeted interventions aimed at improving device usage patterns. Tailored approaches to address specific user needs and preferences may lead to better outcomes.








