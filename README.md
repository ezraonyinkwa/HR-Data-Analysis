# HR Data Analysis

## Project Overview

The goal of this project is to perform an Exploratory Data Analysis (EDA) on an HR dataset to uncover key insights and trends related to employee demographics, performance, retention and other critical HR metrics. The analysis will help identify patterns and relationships within the data that can inform HR strategies and decision-making processes.

#### Data Sources
The HR dataset  obtained from Kaggle encompasses a comprehensive collection of employee-related data, capturing various aspects of the workforce within an organization. This dataset includes critical information pertaining to employee demographics, job roles, performance metrics and other HR-related attributes. The data is structured to provide a holistic view of the employees' profiles, enabling a thorough analysis of factors influencing employee behavior, performance and retention.

Tools
- Microsoft SQL Server

#### Data cleaning/Preparation
In this section we utilised SQL to perform our data preparation process, we perfomed the following task;
- Renaming columns
- Standardizing data
- Removing unnecessary columns
- Removing nulls
- Removing Duplicates


```sql

--Before we begin lets create a new table with the same dataset, this helps in 
--having a back-up dataset incase we make a mistake in our data cleaning process
SELECT *
INTO Hr_data
FROM HRDataset_v14_Raw;
```
####  Renaming Columns
  
We renamed the following columns to a way that the columns are more and easily understandable. 
  
```sql
EXEC sp_rename 'Hr_data.MaritalDesc','Marital_Status'
EXEC sp_rename 'Hr_data.CitizenDesc','Citizenship'
EXEC sp_rename 'Hr_data.RaceDesc','Race' 
EXEC sp_rename 'Hr_data.HispanicLatino','Hispanic/Latino'
EXEC sp_rename 'Hr_data.EmpSatisfaction','EmployeeSatisfaction'
```
####  Standardizing Data

Standardizing data is a vital step in the data cleaning process that ensures consistency, accuracy, and usability of the dataset.

```SQL
SELECT 
CASE
WHEN Sex = 'M' THEN 'Male'
WHEN Sex ='F' THEN 'Female'
END
FROM Hr_data

UPDATE Hr_data
Set Sex = CASE
WHEN Sex = 'M' THEN 'Male'
WHEN Sex ='F' THEN 'Female'
END

SELECT ROUND(EngagementSurvey,2)
FROM Hr_data


UPDATE Hr_data
SET EngagementSurvey = ROUND(EngagementSurvey,2)
```

