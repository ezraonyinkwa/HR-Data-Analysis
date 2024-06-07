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

#### Removing unnecessary columns
We are going to remove unnecessary columns that we are not going to use for our analysis.

```sql
ALTER TABLE Hr_data
DROP COLUMN MarriedID,MaritalStatusID,GenderID,EmpstatusID,Termd,FromDiversityJobFairID
```

#### Removing nulls
Removing nulls is essential in the data cleaning process that significantly impacts the quality, accuracy and reliability of data analysis.

```sql
SELECT *--Checking for nulls
FROM Hr_data
WHERE Employee_Name IS NULL AND EmpID IS NULL --No nulls were found except for the columns which allowed nulls
```
#### Removing Duplicates
Removing duplicates is an important step in Data cleaning since it helps maintain the integrity, accuracy and reliability of a dataset.

```sql
select *,
ROW_NUMBER() OVER(PARTITION BY Employee_Name,EmpID ORDER BY EmpID)row_num
FROM Hr_data --based on the employee name and employee id there were no duplicates found
```

### Exploratory Data Analysis
1. General Employee Information
   - How many employees have we employed?
   - What is the gender distribution of employees?
   - What are the different job roles and their counts based on gender?

2. Salary and Compensation
   - What is the average salary of employees per gender?
   - What is the distribution(Avg,Max,Min Salary)and our total expenditure of salaries among different departments,highlight the number of employees in each department before termination and after 
     termination?

3. Employee/Department Performance
   - How many employees have received a performance score of above or equal to 4 and which deparment has the highest number of employee with the perfomance score?
   - What is the average performance score for each department?
   - Identify the most common reasons for termination

4. Employee Tenure and Turnover
   - Calculate the total number of employees hired each year.
   - What is the tenure by job of each employee Create a stored procedure for this ?
   - What is the turnover rate by department?
   - How many employees have left the company by department?

5. Diversity and Inclusion
   - What is the age distribution of employees who are still with us and there job ?
   - What is the distribution of employees by ethnicity?
   - How does diversity vary across different departments?


#### General Employee Information



