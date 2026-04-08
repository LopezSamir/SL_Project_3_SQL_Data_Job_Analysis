TODO: Project currently being worked on. Queries are done, but README still needs to be done
to Explain the results and answer the following questions


# Introduction

This project uses SQL to analyze the data job market. It focuses primarily on remote Data Analyst roles. From looking at top-paying jobs, in-demand skills, and the demand of said skills; this project will allow me to get a better understanding of the market I am attempting to get into.

Here are the SQL queries used for this project: [SL_Project_3_SQL](https://github.com/LopezSamir/SL_Project_3_SQL_Data_Job_Analysis/tree/main/SL_Project_3_SQL)

# Background

As someone looking to become a Data Analyst, I needed a project that could showcase some of my SQL knowledge and skills. On top of that, I also wanted to know
more about what it takes to become one. As such, I created this project to serve both purposes. I used detailed data that I found online from 2023 which included things like job titles, skills, and other information I could use for this project.

## 5 Questions I set out to answer through my SQL queries

1. What are the top-paying data analyst jobs?
2. What skills are required for these top-paying jobs?
3. What skills are most in demand for data analysts?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn?

# Tools Used For the Project

* **SQL**: The most important tool for this project. It allowed me to create queries that I used to analyze the data.
* **PostgreSQL**: The database management system I chose for this project
* **Visual Studio Code**: The tool I used to actually write and execute my queries.
* **Git & GitHub**: Used to share my SQL project, queries, and analysis. It is also what I used to write this README file

# Analysis

The queries for this project each aim to answer one of the 5 questions I started with. Here is how I approached each question:

## 1. Top Paying Data Analyst Jobs

In order to find the highest paying remote data analyst jobs, I created the following query:

```sql

SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Analyst' AND
    job_location = 'Anywhere' AND
    salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
LIMIT 10 

```

### Quick Explanation of SQL Query

The SELECT part is what I wanted to get FROM the job_postings_fact table in the data. Then I did a LEFT JOIN on the company_dim table using the company_id columns in the two tables. The WHERE part just filters what I want to see by making it only give me
the data on Data Analyst roles that are remote and have a given salary. Lastly, the results are limited to the top 10 jobs shown in descending order.

### Breakdown of the top data analyst jobs:

* **Wide Salary Range**: Top 10 paying data analyst roles span from $184,000 to $650,000, indicating significant salary potential in the field.
* **Diverse Employers**: Companies like SmartAsset, Meta, and AT&T are among those offering high salaries, showing a broad interest across different industries.
* **Job Title Variety**: There's a high diversity in job titles, from Data Analyst to Director of Analytics, reflecting varied roles and specializations within data analytics.

## 2: Skills for Top Paying Jobs

In order to find the skills required for the top paying jobs, I created the following query:

```sql

WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM
        job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_title_short = 'Data Analyst' AND
        job_location = 'Anywhere' AND
        salary_year_avg IS NOT NULL
    ORDER BY
        salary_year_avg DESC
    LIMIT 10 
)

SELECT 
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    salary_year_avg DESC

```
### Quick Explanation of SQL Query

For this query, I made a Common Table Expression (CTE) called top_paying_jobs and then put the query from question 1 inside it. I deleted a couple of lines from the SELECT part as I don't need them for this CTE. Then I selected that I wanted everything from the CTE called top_paying_jobs as well as that I wanted skills. After that, I used INNER JOIN twice. The first joined the skills_job_dim table and the top_paying_jobs table (CTE) on the job_id columns. The second joined the skills_dim table with the skills_job_dim table on the skill_id columns. Lastly, I ordered it by the salary year average in descending order.

### Breakdown of the most demanded skills for the top 10 highest paying data analyst jobs:

* **SQL** is leading with a count of 8.
* **Python** follows closely with a count of 7.
* **Tableau** is also highly sought after, with a count of 6.
* Other skills like **R**, **Snowflake**, **Pandas**, and **Excel** show varying degrees of demand.

## 3: In-Demand Skills for Data Analysts

In order to find the most demanded skills for data analysts, I created the following query:

```sql

SELECT
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND
    job_work_from_home = TRUE
GROUP BY
    skills
ORDER BY
    demand_count DESC
LIMIT 5

```

### Quick Explanation of SQL Query

First I selected that I wanted the skills. I also want the count(skills_job_dim.job_id) but I want to call it the demand_count. The FROM is the job_postings_fact table. Then I used the same two inner joins as the last query. The WHERE part filters the results to only show me remote data analyst jobs. After that, I grouped it by skills, ordered it by the demand_count in descending order, and limited the results to the top 5.

### Breakdown of the top 5 most demanded skills for data analyst jobs:

* **SQL** and **Excel** remain fundamental, emphasizing the need for strong foundational skills in data processing and spreadsheet manipulation.
* **Programming** and **Visualization Tools** like **Python**, **Tableau**, and **Power BI** are essential, pointing towards the increasing importance of technical skills in data storytelling and decision support.

















