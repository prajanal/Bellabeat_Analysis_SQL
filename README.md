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
