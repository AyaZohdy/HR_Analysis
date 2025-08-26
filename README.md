# HR_Analysis
Small project to practice SQL

Project goals
The goal of this project is to gives HR departments the power to design Organizational development interventions by uncovering actual things you can do to increase employee satisfaction, reduce turnover, and enhance performance.

Sources
1. https://www.hracuity.com/blog/hr-data-analytics/
2. https://www.linkedin.com/posts/abdus-samad-rahman_hr-analytics-activity-7245406442474594305-hw1D/
Business Continuity Disclosure
3. https://www.performyard.com/articles/hr-data-analytics
4. https://www.performyard.com/articles/od-interventions


Analysis

/* Reading the data and determining the important points and questions that we can deal with. */

select * from employee;

/* 
-- Questions to answer them. 

1. What is the most continuousness gender?
=> For Business Continuity Disclosure.

2. How many employees are hired per month and which gender and ethnic?
=> For balance
=> Fast hiring reduce talent gaps.

3. What is the most separation reason and in which gender?
=> To understand how employees feel and why they leave.
=> Uncover hidden issues.
*/

-- Know the amount of missing data.

SELECT COUNT(*)
FROM employee
WHERE employee IS NULL;

-- There is no Null data.

/* Cleaning, filtering and know more about the data */

-- 1. The gender

select DISTINCT gender from employee;

/* 
-- D and C gender  
"D" represents Male (D for "Dhomme" - French for "Man") 
"C" represents Female (C for "Cfemme" - French for "Woman").

https://translate.google.com/?sl=fr&tl=en&text=Dhomme%0ACfemme&op=translate
*/

-- 2. The ethnic group

select DISTINCT ethnicgroup from employee;

select * from ethnicity;

/*
Code	Ethnicity Group
1 (A) - Hispanic or Latino
2 (B) - White (Not Hispanic or Latino)
3 (C) - Black or African American (Not Hispanic or Latino)
4 (D) - Native Hawaiian or Other Pacific Islander (Not Hispanic or Latino)
5 (E) - Asian (Not Hispanic or Latino)
6 (F) - American Indian or Alaska Native (Not Hispanic or Latino)
7 (G) - Two or More Races (Not Hispanic or Latino

https://www.sco.ca.gov/ppsd_hr_emp_race_or_ethnicity_faq.html
*/

-- 3. The Fb group

select DISTINCT fp from employee;

select * from employeetype;

/* 
-- F and P type  
"F" represents Full time.
"P" represents Part time.
*/


/* The answer of the questions */

-- 1. What is the most continuousness gender?

Select DISTINCT termdate From employee; -- To read the column

Select  
	  gender
	  , Sum((termdate) - (hiredate) + 1) AS "The total service day" -- Add 1 to Include the Start Day
	  , Round((Avg((termdate) - (hiredate) + 1)/365),2) AS "The average of total service by year"
From employee
Group by 1;

/* 
The most continuously gender is the male gender but with very low difference.
So in general there is no gap difference between the two gender in continuity.
But the average is two years and this considered short period and need to know the reasons.

The goal of the question => For Business Continuity Disclosure.
*/

-- 2. How many employees are hired per month and which gender and ethnic?
Select DISTINCT hiredate From employee; -- To read the column

-- The hiring last 3 years

-- Per month

Select Round(Avg(employees_count),2) As "The average of hiring last 3 years"
	, "Hired per month"
From (
Select count (emplid) As employees_count
	, Date_part('Month', hiredate) As "Hired per month"
	, Date_part('Year', hiredate) As "Hired per year"
From employee
Group by 3,2
Order by 3 DESC,2
Limit 36) As Employee
Group by 2
Order by 2;

-- The gender and ethnic group

Select count (emplid) As employees_count
	, gender
	, ethnicgroup
From employee
Where hiredate Between '2012-1-1' and '2014-12-31'
Group by 2,3
order by 1;

/* 
1. In general the hiring rate move regularly and continuous last 3 years.
2. The hiring increase in August.

3. The hiring signifyingly reduce in December.
This is often due to the timing of fiscal year-end in December. 
Therefore, reflects year-end budgets and resource allocation.
For many organizations, this period involves 
- Closing out the current year’s finances. 
- Planning for the next year.
- Allowing staff to take holiday breaks. 
As a result, job listings may decrease during November and December, and the general pace of hiring could seem slower.

-- https://www.linkedin.com/pulse/hiring-almost-frozen-during-month-nov-dec-myth-fact-sudheer-s-ghtwe/
-- https://perspectiveblue.com/why-is-it-harder-to-hire-in-december/

4. The most hiring ethnic group is 1 (A) - Hispanic or Latino with huge gap difference with others groupes.

Thus there is Fast hiring that reduce talent gaps. But there is no balance between the employees ethnic. 
*/

-- 3. What is the most separation reason and in which gender?

select DISTINCT termreason from employee; -- To read the column

select DISTINCT separationreason from separationreason; -- To read the column

Select  count (emplid)
	  , termreason
	  , gender
From employee
Where termreason IS NOT NULL
	AND hiredate IS NOT NULL
    AND termdate IS NOT NULL
Group by 2,3;

/*
The mean separation reason is voluntary and this maybe due to
1. Low salaries.
2. Uncomfortable work environment.
3. The employees didn't get the good opportunities.

As mention before the average of total service by year is two years. 
This considered short period and need to know the reasons.

The goals of the question
=> To understand how employees feel and why they leave.
=> Uncover hidden issues.
*/
