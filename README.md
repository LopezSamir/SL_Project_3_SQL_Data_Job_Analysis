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

## 4: Top Skills Based on Salary

In order to find which skills come with the highest salaries, I created the following query:

```sql

SELECT
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE
GROUP BY
    skills
ORDER BY
    avg_salary DESC
LIMIT 25

```

### Quick Explanation of SQL Query

First I selected that I wanted the skills. Then I also wanted the rounded average of the salary_year_avg labled as avg_salary. The FROM, INNER JOIN, WHERE, and GROUP BY parts were almost the exact same as the last query. I just added that I only wanted the salary_year_avg where the value is not NULL, meaning no salary given. Lastly, I ordered it by the avg_salary in descending order and limited the results to the top 25

### Breakdown of the top paying skills for data analysts:

* **High Demand for Big Data & ML Skills**: Top salaries are commanded by analysts skilled in big data technologies (PySpark, Couchbase), machine learning tools (DataRobot, Jupyter), and Python libraries (Pandas, NumPy), reflecting the industry's high valuation of data processing and predictive modeling capabilities.
* **Software Development & Deployment Proficiency:** Knowledge in development and deployment tools (GitLab, Kubernetes, Airflow) indicates a lucrative crossover between data analysis and engineering, with a premium on skills that facilitate automation and efficient data pipeline management.
* **Cloud Computing Expertise:** Familiarity with cloud and data engineering tools (Elasticsearch, Databricks, GCP) underscores the growing importance of cloud-based analytics environments, suggesting that cloud proficiency significantly boosts earning potential in data analytics.

## 5: Most Optimal Skills to Learn

In order to find the most optimal skills to learn, I created the following query:

```sql

SELECT
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count,
    ROUND(AVG(job_postings_fact.salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
Where
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE
GROUP BY
    skills_dim.skill_id
HAVING
    COUNT(skills_job_dim.job_id) > 10
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 25

```

### Quick Explanation of SQL Query

First I selected that I wanted the skill_id and skills columns from the skills_dim table. I also wanted the demand_count and avg_salary from the previous queries. The FROM, INNER JOIN, and WHERE sections were kept the same as last query. This time it is being grouped by the skill_id column from the skills_dim table. The HAVING section makes it so our results when run only gives us back skills that have a count of at least 10. Then, the results are grouped by avg_salary in descending order followed by demand_count in descending order. Lastly, the results are limited to just the top 25.

### Breakdown of the most optimal skills for data analysts:

* **High-Demand Programming Languages**: Python and R stand out for their high demand, with demand counts of 236 and 148 respectively. Despite their high demand, their average salaries are around $101,397 for Python and $100,499 for R, indicating that proficiency in these languages is highly valued but also widely available.
* **Cloud Tools and Technologies**: Skills in specialized technologies such as Snowflake, Azure, AWS, and BigQuery show significant demand with relatively high average salaries, pointing towards the growing importance of cloud platforms and big data technologies in data analysis.
* **Business Intelligence and Visualization Tools**: Tableau and Looker, with demand counts of 230 and 49 respectively, and average salaries around $99,288 and $103,795, highlight the critical role of data visualization and business intelligence in deriving actionable insights from data.
* **Database Technologies**: The demand for skills in traditional and NoSQL databases (Oracle, SQL Server, NoSQL) with average salaries ranging from $97,786 to $104,534, reflects the enduring need for data storage, retrieval, and management expertise.

# Conclusions

## Insights

1. **Top-Paying Data Analyst Jobs**: The highest-paying jobs for data analysts that allow remote work offer a wide range of salaries.
2. **Skills for Top-Paying Jobs**: High-paying data analyst jobs require advanced proficiency in SQL, making it a necessary skill for me to improve.
3. **Most In-Demand Skills**: SQL is also the most demanded skill in the data analyst job market, thus making it important for me.
4. **Skills with Higher Salaries**: Specialized skills, such as SVN and Solidity, are associated with the highest average salaries, indicating a premium on niche expertise. These are skills to learn after I am an experienced data analyst.
5. **Optimal Skills for Job Market Value**: SQL leads in demand and offers for a high average salary, positioning it as one of the most optimal skills for data analysts to learn to maximize their market value.

## Closing Thoughts

Working on this project allowed me to learn a lot about SQL while also showcasing those skills to anyone viewing this README. It provided me with experience working with tables, CTE, and other notable SQL aspects that I didn't know a lot about beforehand. I was also able to answer the 5 questions I had at the start. My main takeaway from this project is that I need to continue to develop my SQL skills in order to be a successful data analyst. I enjoyed it a lot and I look forward to working more with SQL in the future.



