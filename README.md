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
/*
Before we begin lets create a new table with the same dataset, this helps in having a back-up
dataset incase we make a mistake in our data cleaning process
*/

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
SELECT *  --Checking for nulls
FROM Hr_data
WHERE Employee_Name IS NULL AND EmpID IS NULL    --No nulls were found except for the columns which allowed nulls
```
#### Removing Duplicates
Removing duplicates is an important step in Data cleaning since it helps maintain the integrity, accuracy and reliability of a dataset.

```sql
/*
We are going to partition by the unique keys i.e Employee_Name,Emp_ID.
If there is going to be any duplicates it will return a row number that is above 1
*/

select *,
ROW_NUMBER() OVER(PARTITION BY Employee_Name,EmpID ORDER BY EmpID)row_num
FROM Hr_data 
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


### General Employee Information
In this section, we will examine the general information of our employees to gain a deeper understanding of our workforce. This analysis encompasses a range of employee attributes, including demographics, job roles, and performance metrics. By studying these details, we aim to uncover insights that will inform our HR strategies, enhance employee satisfaction, and improve overall organizational performance.

#### How many employees have we employed?
```sql
/*
This Query is going to count all rows in the dataset/table and return them as the count of number of employees
*/
SELECT
	COUNT(*) Total_Employees
FROM Hr_data;

```
![Total Employees SQL](https://github.com/ezraonyinkwa/HR-Data-Analysis/assets/139281995/9026997b-de8a-47ac-b9c6-21997b867b48)

#### Findings
We have a total number of 311 employees who we have employed over the years.

#### What is the gender distribution of employees?
```SQL
/*
The query is going to select the sex column of the employees, count all rows in the (as we have aliased it as total employees),
it is going to return the total number of employees and where it found the sex being male it counts as one and vice versa thus return
the number of both the female and male employees.
*/ 
SELECT 
	Sex,
	COUNT(*) AS Total_Employees
FROM Hr_data
GROUP BY Sex;
```
![Gender of employees SQL](https://github.com/ezraonyinkwa/HR-Data-Analysis/assets/139281995/eccca46a-7560-461e-b854-f625e7b4deb8)

#### Findings

From our analysis, it is evident that our workforce comprises a higher number of female employees, totaling 176, compared to 135 male employees. This gender distribution indicates a female-majority workforce.

#### What are the different job roles and their counts based on gender?

```sql
SELECT  
	Position,
	Sex,
	COUNT(*) AS Total_Employees
FROM Hr_data
GROUP BY Position,Sex
ORDER BY Total_Employees DESC;

```
![Position of Employees sql](https://github.com/ezraonyinkwa/HR-Data-Analysis/assets/139281995/d5c82ece-af57-4bd9-95b9-ffdbb0d4facb)

#### Findings
From our analysis, it is evident that the job role most occupied by female employees is Production Technician I, with 83 female employees, while the same role is also the most occupied by male employees, with 54 male employees. Additionally, the top job roles are predominantly held by males, with 11 top positions occupied by male employees.The significant presence of both female and male employees in the Production Technician I role indicates that this position attracts a diverse pool of talent. However, there is a notable gender disparity in higher-ranking positions, with males occupying a majority of these roles.The dominance of males in top positions suggests a potential imbalance in career advancement opportunities. This highlights the need to ensure equal access to career development and leadership training for all employees, regardless of gender.   

### Salary and Compensation
```sql
/*
In this query we are going to select Average salaries in terms of gender and format the salary into USD currency
*/

SELECT
	sex, 
	FORMAT(AVG(salary),'c') as avg_salary
FROM Hr_data
GROUP BY Sex
ORDER BY 2 DESC;
```
![Avg salary of Employees](https://github.com/ezraonyinkwa/HR-Data-Analysis/assets/139281995/7e29dff9-21d3-430e-82e7-4464cbf66902)

#### Findings
From the analysis, it is evident that our male employees receive a higher average salary of $70,629 compared to female employees, who have an average salary of $67,786. This indicates a gender pay disparity within our organization.The difference in average salaries between male and female employees highlights the presence of a gender pay gap. This disparity underscores the need to review our compensation policies to ensure they are equitable and fair.

#### What is the distribution(Avg,Max,Min Salary)and our total expenditure of salaries among different departments,highlight the number of employees in each department before and after termination ?
![Expenditure sql](https://github.com/ezraonyinkwa/HR-Data-Analysis/assets/139281995/c476f2e9-76d6-449e-95db-df1a6f6428ca)

```sql
/*
We created a CTE which selects the avg,min,max salaries and sums up salaries as expenditure and formats the salaries into USD currency.
i CREATED A CTE so I can be able to see the difference in salaries before termination and after termination how our expenditure and salaries varies 
*/
WITH CTE_Expenditure AS(
SELECT
	Department,
	FORMAT(AVG(Salary),'c')Avg_Salary, 
	FORMAT(MIN(Salary),'c') Min_Salary,
	FORMAT(MAX(Salary),'c')Max_Salary,
	FORMAT(SUM(Salary),'c')Expenditure,
	COUNT(EmpID) AS Total_Employees
FROM Hr_data
GROUP BY Department
),

CTE_Expenditure_after_Termination AS (
SELECT
	Department,
	FORMAT(AVG(Salary),'c')Avg_Salary, 
	FORMAT(MIN(Salary),'c') Min_Salary,
	FORMAT(MAX(Salary),'c')Max_Salary,
	FORMAT(SUM(Salary),'c')Expenditure,
	COUNT(EmpID) AS Employees_After_Termination
FROM Hr_data
WHERE Dateoftermination IS NULL
GROUP BY Department
)

SELECT *
FROM CTE_Expenditure CTE_E
JOIN CTE_Expenditure_after_Termination CTE_Eat 
ON CTE_E.Department=CTE_Eat.Department 
ORDER BY 6 DESC;

```
#### Findings








