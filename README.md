# HR Data Analysis
## Table of Contents
- [Project Overview](project-overview)
- [Data Sources](data-source)

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
   - Which recruitment source do we hire the most employees from ?

2. Salary and Compensation
   - What is the average salary of employees per gender?
   - What is the distribution(Avg,Max,Min Salary)and our total expenditure of salaries among different departments,highlight the number of employees in each 
     department before and after termination ?

3. Employee/Department Performance
   - How many employees have received a performance score of above or equal to 4 and which deparment has the highest number of employee with the perfomance 
score?
   - What is the average performance score for each department?
   - Identify the most common reasons for termination

4. Employee Tenure and Turnover
   - Calculate the total number of employees hired each year.
   - Find employees who have been with the company for more than 5 years
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

#### Which recruitment source do we hire the most employees from ?
```sql
SELECT 
	RecruitmentSource,
	count (*) Employees
	FROM Hr_data
	GROUP BY RecruitmentSource
	ORDER BY 2 DESC;
```
![Recruitment source](https://github.com/ezraonyinkwa/HR-Data-Analysis/assets/139281995/2dc9b9ae-fea9-466a-a318-a7a9556d5928)
#### Findings
The fact that Indeed has been the most successful recruitment source, providing 87 employees, indicates its effectiveness in attracting candidates. This could be due to its wide reach, user-friendly interface, and the large number of job seekers who use it.
LinkedIn has also proven to be a valuable recruitment source, contributing 76 employees. This platform is particularly beneficial for professional and specialized roles, as it allows for networking and direct engagement with potential candidates.
The on-line web application method has only yielded one employee, suggesting that this recruitment channel is not currently effective. This may be due to lack of visibility, user experience issues, or insufficient promotion of job openings through this medium.




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
I created a CTE so I can be able to see the difference in salaries before termination and after termination how our expenditure and salaries varies 
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
The Production department has the lowest average salary, with employees earning an average of $59,953. This indicates that roles within this department are compensated at a lower rate compared to other departments.The lowest individual salary is also found in the Production department, at $45,046. This further emphasizes the lower compensation levels within this department.The Executive Officer department has the highest average salary, with an average compensation of $250,000. This significant difference highlights the disparity in salary levels between executive roles and other departments.Despite having the lowest average salary, the Production department has the highest total salary expenditure before termination, amounting to $12,530,291. This is attributable to the high number of employees within the department, leading to a larger overall payroll expenditure.
After termination of employees the departmnt with the highest Saary expenditure was Production with a total salary amount of $7,612,827.he high salary expenditure in the Production department, driven by its large workforce, suggests that it plays a crucial role in the organization’s operations. 

### Employee/Department Performance
In this section we are going to look at employees perfomance by ratings and also the perfomance of departments

####  How many employees have received a performance score of above or equal to 4 and which deparment has the highest number of employee with the perfomance 
score?

```sql
SELECT 
	Department,
	COUNT(EmpID) Employees
FROM Hr_data
WHERE PerfScoreID >=4
GROUP BY Department
ORDER BY 2 DESC;
```
![employee perfomance sql](https://github.com/ezraonyinkwa/HR-Data-Analysis/assets/139281995/8b623927-a2aa-4f78-859b-310d14b53d1d)
#### Findings
The Production department has the highest number of employees who received a performance rating above 4, with a total of 27 employees. This indicates a strong performance culture within the Production department, highlighting the effectiveness of the team and potentially strong leadership and management practices in this area.The IT/IS department follows with 6 employees receiving a rating above 4. This suggests a solid performance in the IT/IS department, though on a smaller scale compared to the Production department.
 The Sales and Software Engineering departments have the lowest number of employees with a performance rating above 4, each with only 2 employees. This may indicate areas where performance improvement initiatives could be beneficial.

#### What is the average performance score for each department ?
```SQL
SELECT 
	Department,
	AVG(PerfScoreID)Avg_Score
FROM Hr_data
GROUP BY Department
ORDER BY 2 DESC;
```
![Overall perfomance  SQL](https://github.com/ezraonyinkwa/HR-Data-Analysis/assets/139281995/c598496d-bde7-49c8-8e1f-ca3495633535)

#### Findings
The Admin Office ranks first with the highest average performance score of 3. This suggests that employees in the Admin Office consistently perform at a higher level, indicating effective management, strong work ethic, and a productive work environment.Both the Executive Office and IT/IS departments also have an average performance score of 3, demonstrating solid performance within these departments. This reflects well on the leadership and technical proficiency in these areas.The Production and Sales departments recorded the lowest average performance scores, each with an average score of 2. This indicates potential challenges in these departments that may be affecting overall performance levels.

#### Identify the most common reasons for termination

```sql
SELECT 
	TermReason,
	count(*) Employees
FROM Hr_data
WHERE TermReason <> 'N/A-StillEmployed'
GROUP BY TermReason
ORDER BY 2 DESC;

```
![Employee TERMINATION REASON SQL](https://github.com/ezraonyinkwa/HR-Data-Analysis/assets/139281995/ef727064-ef13-4606-8356-8388d7cdd5ae)

#### Findings
The most common reason for employee termination is moving to another position, with a total of 20 employees leaving for this reason. This suggests that employees are finding better opportunities elsewhere, indicating a potential issue with job satisfaction, career growth, or competitive compensation within the organization.The second most common reason for termination is employee unhappiness, accounting for 14 employees. This highlights dissatisfaction with certain aspects of their job, which could be related to work environment, management practices, or job role expectations.The least common reason for termination is gross misconduct, with only 1 employee terminated for this reason. This indicates that severe disciplinary issues are rare within the organization, reflecting positively on overall employee behavior and adherence to company policies.

### Employee Tenure and Turnover
Understanding employee tenure and turnover is crucial for any organization aiming to optimize its workforce management and improve overall productivity.

#### Calculate the total number of employees hired each year.
```sql
SELECT 
	YEAR(DateofHire)HireYear,
	count(*)Employees_Hired
FROM Hr_data
GROUP BY YEAR(DateofHire)
ORDER BY 2 DESC;
```
![Employees Hired sql](https://github.com/ezraonyinkwa/HR-Data-Analysis/assets/139281995/245a6b69-f4e2-4cb1-9211-be81442711d1)

#### Findings
The year with the highest number of hires was 2011, during which 83 employees were recruited. This peak in hiring may indicate a period of expansion, significant projects, or increased demand for workforce resources within the organization. Understanding the factors contributing to this surge can provide insights into successful recruitment strategies or organizational growth phases.The year 2014 saw the second-highest number of hires, with 60 employees joining the organization. This suggests another period of considerable growth or the launch of new initiatives requiring additional human resources.Both 2006 and 2018 recorded the lowest number of hires, with only 1 employee recruited each year. These years of minimal hiring may reflect economic downturns, hiring freezes, or a stable workforce with low turnover, indicating a period of organizational stability or strategic adjustments in recruitment policies.

#### What is the tenure by job of each employee Create a stored procedure for this ?

```sql
/*
The below query will calculate the difference between the date of termination and date of hire, if there is no termination date (did not get terminated)
It will get the current date /(year) and get the diference  with the date of hire. 
*/
 SELECT
		Employee_Name,
		DATEDIFF(YEAR,DateofHire,
		ISNULL(DateofTermination,GETDATE())) Tenure_years
FROM Hr_data
WHERE DATEDIFF(YEAR,DateofHire,
		ISNULL(DateofTermination,GETDATE())) >5
ORDER BY 2 DESC;
```
![Employee Tenure sql](https://github.com/ezraonyinkwa/HR-Data-Analysis/assets/139281995/be58b6e4-2c64-4bdd-8f75-956473f7718f)

#### Findings
There are a total of 223 employees who served more tan 5 years ,Torrence Jack stands out as the longest-serving employee, having dedicated 18 years to the organization. This long tenure reflects a high level of commitment and satisfaction, likely indicating positive aspects of the work environment, career growth opportunities, and alignment with organizational values.Following closely, Pitt Brad has served for 17 years, further emphasizing a trend of long-term employee retention within the organization. Such extended tenures suggest the presence of effective retention strategies, a supportive work culture, and meaningful career development pathways.At the other end of the spectrum, the shortest-serving employees have a tenure of 6 years, with six employees falling into this category. While 6 years is still a substantial period, understanding why these employees did not stay longer could provide insights into potential areas for improvement in retention strategies or work environment factors that may influence employee decisions to leave after this period.The presence of employees with long tenures, such as Torrence Jack and Pitt Brad, indicates that the organization has successfully fostered an environment where employees feel valued and motivated to stay long-term.

#### What is the tenure by job of each employee Create a stored procedure for this ?

```sql
CREATE PROCEDURE Tenure AS (
 SELECT
		Employee_Name,
		Position,
		DateofHire,
		DateofTermination,
		DATEDIFF(YEAR,DateofHire,
		ISNULL(DateofTermination,GETDATE())) Tenure_years,

		DATEDIFF(MONTH,DateofHire,
		ISNULL(DateofTermination,GETDATE())) % 12 Tenure_Month,  /*Calculates the difference in months between the hire date and the termination 
		date or current date and takes the remainder when divided by 12 to get the number of months after accounting for full years. */

		DATEDIFF(DAY,DATEADD(MONTH,DATEDIFF(MONTH,DateofHire,
		ISNULL(DateofTermination,GETDATE())),DateofHire),ISNULL(DateofTermination,GETDATE())) Tenure_Days
		 /*Calculates the number of days after accounting for full years and full months*/
		

		FROM Hr_data
	)
	 
	 EXEC Tenure;

/*
After accounting for full years and full months,the query is going to determine the remaining days between the dates.
The Tenure_days will return a negative number/day , where the negative days signifies the number of remaining
days for full month
 */
```
![Tenure sql2](https://github.com/ezraonyinkwa/HR-Data-Analysis/assets/139281995/1175b6cf-fa60-4d4d-8c38-13f24fcd50a3)

#### Findings
From the analysis the highest serving tenure was Brown Mia who worked for 16 years 8 months and 12 days as  an accountant 1.Brown Mia's long tenure as an Accountant 1 indicates a stable and potentially satisfied employee in this role. This tenure could reflect positively on the company’s retention strategies for accounting positions.Alagbe Trina has also worked for 16 years 5 months and 2 days as a Production Technician 1 , this highlights significant experience and likely high proficiency in production processes. This long service duration suggests a strong alignment with job role and work environment.The employee who had the lowest tenure was Goble Taisha who worked for 30 days as a Database Administrator.Taisha’s very short tenure of 30 days as a Database Administrator points to potential issues such as mismatch in job expectations, inadequate onboarding, or personal reasons.

#### What is the turnover rate by department?
```sql
SELECT 
		Department,
		COUNT(*) Eployees_ID,
		(SUM (CASE WHEN DateofTermination IS NOT NULL THEN 1 ELSE 0 END )*1.0/COUNT(*))* 100 Turn_Over_Rate
	FROM Hr_data
	GROUP BY Department;
/*
The above query is going to Calculate the total number of people terminated divided by
the total employees
*/
```

![Turnover rate by dep sql](https://github.com/ezraonyinkwa/HR-Data-Analysis/assets/139281995/9f81fdc2-812b-4af7-8212-55ffa9d35f20)

#### Findings
The high turnover rate in the Production department of 39.71% suggests potential issues such as job dissatisfaction, challenging work conditions, or inadequate compensation. This department may benefit from a detailed analysis to identify specific factors contributing to high turnover and implement targeted strategies to improve employee retention. This could include better working conditions, enhanced training programs, and competitive compensation packages.
The Software Engineering department also experiences a high turnover rate of 36.36%, which could be due to factors such as high demand for software engineers in the job market, stress, or lack of career advancement opportunities. Addressing these issues could involve offering professional development opportunities, ensuring competitive salaries, and creating a supportive work environment to retain talent.
The Executive Officer department's zero turnover rate indicates high job satisfaction, strong job security or effective leadership retention strategies. This stability can be leveraged to understand best practices and policies that might be replicated in other departments to reduce turnover. It could also reflect the strategic importance and careful selection process for these roles, ensuring a good fit and high job satisfaction.


#### How many employees have left the company by department?

```sql
SELECT 
	Department,
	 COUNT(*)AS Employees
FROM Hr_data
WHERE DateofTermination IS NOT NULL
GROUP BY Department
ORDER BY 2 desc;
/*
The query is going to count all employees but it will filter and return only employees who were terminated
*/
```
![Employees Terminated sql](https://github.com/ezraonyinkwa/HR-Data-Analysis/assets/139281995/cf0d99f7-384c-4a2d-aacf-3a6de0c14498)

#### Findings
The high number of terminations in the Production department with 83 employees suggests underlying issues such as job dissatisfaction, high job demands or inadequate working conditions. This trend indicates a need for a detailed review of the department's work environment, management practices and employee engagement strategies.The IT/IS department also shows a notable number of terminations with a total of 10 employees. This could be attributed to factors such as rapid technological changes, high stress levels, or better opportunities elsewhere.The low number of terminations in the Admin Offices with 2 employees terminated suggests a stable and potentially well-managed work environment. This stability may be due to effective leadership, job satisfaction or strong employee engagement initiatives.

### Diversity and Inclusion
In this section we are going to look at the diversity and inclusion,Diversity and inclusion are critical components of a progressive and dynamic workforce, contributing significantly to the overall success and sustainability of an organization.

#### What is the age distribution of employees who are still with us and there job ?
```sql
SELECT 
	Employee_Name,
	Position,
	DATEDIFF(YEAR,DOB,GETDATE()) Age
FROM Hr_data
WHERE DateofTermination is null
ORDER BY 3 DESC;
/*
This query helps in calculating the age of our employees where its going to find the difference
between the year born subtracted from the current year in order to get there current age
*/  
```
![Employee Age](https://github.com/ezraonyinkwa/HR-Data-Analysis/assets/139281995/e3c6a0ad-9030-40c8-b35d-23bb6e7ab861)

#### Findings
As the oldest employee, Chace Beatrice of age 73 years,her extensive experience likely brings valuable skills and knowledge to the Production department. Her longevity in the role suggests a high level of job satisfaction and commitment, which can serve as an example for other employees.Daniel Ann the secod oldest employee with 72 years,her role as a Sr. Network Engineer indicates a high level of expertise in a critical and technical position.
As the youngest employee (32 years), Monkfish Erasmus represents the newer generation of the workforce. His role as a Production Technician II suggests that younger employees are actively contributing to core production activities.

#### What is the distribution of employees by ethnicity ?

```sql
SELECT 
	Race,
	COUNT(*)Employees
FROM Hr_data
GROUP BY Race
ORDER BY 2 DESC;
```
![Employee ethinicity sql](https://github.com/ezraonyinkwa/HR-Data-Analysis/assets/139281995/f9a5c8ea-e93e-47d9-aca0-91bf86dbf40f)

#### Findings
 The majority of the workforce is White with a total of 187 employees, which indicates a lack of ethnic diversity within the organization. .Black or African American employees form the second-largest ethnic group within the organization with a total of 80 employees. The Hispanic ethnicity is significantly underrepresented, with only one employee in this category. This underrepresentation signals a need for targeted recruitment and retention strategies to attract and support Hispanic employees.

#### How does diversity vary across different departments?
```sql
SELECT 
	Department,
	Race,
	COUNT(*)Employees
FROM Hr_data
GROUP BY Department,Race
ORDER BY 3 DESC;

```
![Ethnicity by department](https://github.com/ezraonyinkwa/HR-Data-Analysis/assets/139281995/a0c096ce-e36f-40e7-b30d-748531ceb62e)

#### Findings
The data reveals a significant concentration of White employees in the Production department with 134 employees. This suggests that the department heavily relies on this demographic, which may reflect broader hiring trends or availability of talent in the region.
Black or African American employees represent the second-largest ethnic group with 45 employees who work within the Production department. 
The Hispanic ethnicity , being the lowest represented group overall with only one employee, is also minimally present in the Production department. 

### Recommendations
 - Implement targeted recruitment campaigns to attract a broader range of ethnicities . Collaborate with community organizations and educational institutions 
   to reach a more diverse talent pool.Foster an inclusive culture by promoting diversity awareness and cultural competence among all employees. Regular 
   training sessions, workshops, and open forums can help build an environment where all employees feel valued and included.
 - Instituting regular performance reviews and feedback sessions can help identify and address performance issues promptly. This approach ensures that employees receive the necessary guidance and support to improve their performance.Encouraging a culture of continuous improvement by setting clear performance expectations, providing ongoing training, and recognizing achievements can help maintain high performance levels across all departments.
 - Creating an inclusive workplace culture that supports and encourages the career progression of female employees is essential. This includes fostering an environment where female employees feel empowered to pursue higher-level positions and are provided with the necessary resources to succeed.
 - Implementing transparent salary practices can help in mitigating gender pay disparities. Clear communication about how salaries are determined and providing equal opportunities for salary negotiations can contribute to more equitable compensation.Conducting a comprehensive review of our compensation structures is crucial. This should include analyzing factors such as job roles, experience, qualifications, and performance to identify and address any unjustified pay discrepancies.
 - Conduct exit interviews to understand the reasons behind high termination rates, particularly in departments like Production. Use this data to implement targeted retention strategies, such as improving working conditions, offering competitive compensation, and providing clear career advancement paths.Apply best practices from departments with low termination rates (e.g., Executive Officer) to other departments. This might include leadership development programs, enhanced communication channels, and employee engagement initiatives.

## Reference
[W3schools](https://www.w3schools.com/sql/)
[Google](https://www.google.com/search?q=sql+query+that+finds+the+difference+in+years+from+two+dates&rlz=1C1CHBD_enKE1097KE1097&oq=sql+query+that+finds+the+difference+in+years++from+two+dates&gs_lcrp=EgZjaHJvbWUyBggAEEUYOTIKCAEQABiiBBiJBTIKCAIQABiABBiiBDIKCAMQABiiBBiJBTIKCAQQABiABBiiBNIBCTI2NjgwajBqN6gCCLACAQ&sourceid=chrome&ie=UTF-8)


  



