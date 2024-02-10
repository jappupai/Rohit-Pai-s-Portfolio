# Rohit-Pai-s-Portfolio
# Analytics Hr Report Portfolio
# HR DATA ANALYSIS - SQL SERVER 2022 / POWER BI
This project dives deep into the realm of data analysis using SQL and Power BI to uncover important human resource insights that can greatly benefit the company. Featuring eye-catching dashboards offer crucial HR metrics like employee turnover, diversity, recruitment efficacy and performance evaluations. These help HR professionals make informed decisions and strategic workforce planning.

Source Data:
The source data contained Human Resource 22000 records from 2000 to 2020. This is included in the repository.

Data Cleaning & Analysis:

This was done on SQL server 2022 involving
1. Data loading & inspection
2. Handling missing values
3. Data cleaning and analysis
4. Data Visualization: Power BI Desktop In a corporate setting, results can be shared online on www.powerbi.com

Exploratory Data Analysis Questions:

1. What's the age distribution in the company?
2. What's the gender breakdown in the company?
3. How does gender vary across departments and job titles?
4. What's the race distribution in the company?
5. What's the average length of employment in the company?
6. Which department has the highest turnover rate?
7. What is the tenure distribution for each department?
8. How many employees work remotely for each department?
9. What's the distribution of employees across different states?
10. How are job titles distributed in the company?
11. How have employee hire counts varied over time?
    
Findings:

1. There are more male employees than female or non-conforming employees.
2. The genders are fairly evenly distributed across departments. There are slightly more male employees overall.
3. Employees 21-30 years old are the fewest in the company. Most employees are 31-50 years old. Surprisingly, the age group 50+ have the most employees in the company.
   Caucasian employees are the majority in the company, followed by mixed race, black, Asian, Hispanic, and native Americans.
4. The average length of employment is 7 years.
5. Auditing has the highest turnover rate, followed by Legal, Research & Development and Training. Business Development & Marketing have the lowest turnover rates.
6. Employees tend to stay with the company for 6-8 years. Tenure is quite evenly distributed across departments.
7. About 25% of employees work remotely.
8. Most employees are in Ohio (14,788) followed distantly by Pennsylvania (930) and Illinois (730), Indiana (572), Michigan (569), Kentucky (375) and Wisconsin (321).
9. There are 182 job titles in the company, with Research Assistant II taking most of the employees (634) and Assistant Professor, Marketing Manager, Office Assistant IV, Associate Professor and VP of Training and Development taking the 
   just 1 employee each.
10. Employee hire counts have increased over the years

1) Create Database

CREATE DATABASE Hr;

2) Import Data to SQL Server

Right-click on Human_Resources > Tasks > Import Data
Use import wizard to import HR Data.csv to hr table. 
Verify that the import worked:
use Hr;
SELECT *
FROM Hr_data;

3) DATA CLEANING

The termdate was imported as nvarchar(50). This column contains termination dates, hence it needs to be converted to the date format.

Update date/time to date
format-termdate-1

Update termdate date/time to date

convert dates to yyyy-MM-dd
create new column new_termdate
copy converted time values from termdate to new_termdate
convert dates to yyyy-MM-dd

UPDATE Hr_data
SET termdate = FORMAT(CONVERT(DATETIME, LEFT(termdate, 19), 120), 'yyyy-MM-dd');
create new column new_termdate
ALTER TABLE Hr_data
ADD new_termdate DATE;
copy converted time values from termdate to new_termdate
UPDATE Hr_data
SET new_termdate = CASE
 WHEN termdate IS NOT NULL AND ISDATE(termdate) = 1 THEN CAST(termdate AS DATETIME) ELSE NULL
 END;
check results
SELECT new_termdate
FROM Hr_data;
create new column "age"
ALTER TABLE Hr_data
ADD age nvarchar(50)
populate new column with age
UPDATE Hr_data
SET age = DATEDIFF(YEAR, birthdate, GETDATE());

QUESTIONS TO ANSWER FROM THE DATA
1) What's the age distribution in the company?
age distribution
SELECT
 MIN(age) AS youngest,
 MAX(age) AS OLDEST
FROM Hr_data;
age group count
SELECT age_group,
count(*) AS count
FROM
(SELECT 
 CASE
  WHEN age <= 21 AND age <= 30 THEN '21 to 30'
  WHEN age <= 31 AND age <= 40 THEN '31 to 40'
  WHEN age <= 41 AND age <= 50 THEN '41 to 50'
  ELSE '50+'
  END AS age_group
 FROM Hr_data
 WHERE new_termdate IS NULL
 ) AS subquery
GROUP BY age_group
ORDER BY age_group;
Age group by gender
SELECT age_group,
gender,
count(*) AS count
FROM
(SELECT 
 CASE
  WHEN age <= 21 AND age <= 30 THEN '21 to 30'
  WHEN age <= 31 AND age <= 40 THEN '31 to 40'
  WHEN age <= 41 AND age <= 50 THEN '41 to 50'
  ELSE '50+'
  END AS age_group,
  gender
 FROM Hr_data
 WHERE new_termdate IS NULL
 ) AS subquery
GROUP BY age_group, gender
ORDER BY age_group, gender;
2) What's the gender breakdown in the company?
SELECT
 gender,
 COUNT(gender) AS count
FROM hr_data
WHERE new_termdate IS NULL
GROUP BY gender
ORDER BY gender ASC;
3) How does gender vary across departments and job titles?
SELECT 
department,
gender,
count(gender) AS count
FROM Hr_data
WHERE new_termdate IS NULL
GROUP BY department, gender,
ORDER BY department, gender ASC;
job titles
SELECT 
department, jobtitle,
gender,
count(gender) AS count
FROM Hr_data
WHERE new_termdate IS NULL
GROUP BY department, jobtitle, gender
ORDER BY department, jobtitle, gender ASC;
4) What's the race distribution in the company?
SELECT
race,
count(*) AS count
FROM Hr_data
WHERE new_termdate IS NULL 
GROUP BY race
ORDER BY count DESC;
5) What's the average length of employment in the company?
SELECT 
AVG(DATEDIFF(year, hire_date, new_termdate)) AS tenure
FROM Hr_data
WHERE new_termdate IS NOT NULL AND new_termdate <= GETDATE();
6) Which department has the highest turnover rate?
get total count
get terminated count
terminated count/total count
SELECT
 department,
 total_count,
 terminated_count,
 (round((CAST(terminated_count AS FLOAT)/total_count), 2)) * 100 AS turnover_rate
 FROM
	(SELECT 
	 department,
	 count(*) AS total_count,
	 SUM(CASE
		WHEN new_termdate IS NOT NULL AND new_termdate <= GETDATE() THEN 1 ELSE 0
		END
		) AS terminated_count
	FROM Hr_data
	GROUP BY department
	) AS subquery
ORDER BY turnover_rate DESC;
7) What is the tenure distribution for each department?
SELECT 
    department,
    AVG(DATEDIFF(year, hire_date, new_termdate)) AS tenure
FROM Hr_data
WHERE new_termdate IS NOT NULL AND new_termdate <= GETDATE()
GROUP BY department;
8) How many employees work remotely for each department?
SELECT
 location,
 count(*) as count
FROM Hr_data
WHERE new_termdate IS NULL
GROUP BY location;
9) What's the distribution of employees across different states?
SELECT 
 location_state,
 count(*) AS count
FROM Hr_data
WHERE new_termdate IS NULL
GROUP BY location_state
ORDER BY count DESC;
10) How are job titles distributed in the company?
SELECT 
 jobtitle,
 count(*) AS count
 FROM Hr_data
 WHERE new_termdate IS NULL
 GROUP BY jobtitle
 ORDER BY count DESC;
11) How have employee hire counts varied over time?
calculate hires
calculate terminations
(hires-terminations)/hires percent hire change
SELECT
 hire_year,
 hires,
 terminations,
 hires - terminations AS net_change,
 (round(CAST(hires-terminations AS FLOAT)/hires, 2)) * 100 AS percent_hire_change
 FROM
	(SELECT 
	 YEAR(hire_date) AS hire_year,
	 count(*) AS hires,
	 SUM(CASE
			WHEN new_termdate is not null and new_termdate <= GETDATE() THEN 1 ELSE 0
			END
			) AS terminations
	FROM Hr_data
	GROUP BY YEAR(hire_date)
	) AS subquery
ORDER BY percent_hire_change ASC;
