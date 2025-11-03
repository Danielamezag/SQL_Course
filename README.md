# Introduction
Dive into the 2023 data job market! Focusing on data analyst roles, this project explores top-paying jobs, in-demand skills, and where high demand meets high salary in data analytics. 

Check out the SQL queries here: [project_sql folder](/project_sql/)
# Background 
Motivated by a desire to better understand and navigate the data analyst job market, this project was created to identify the most in-demand and highest-paying skills, ultimately helping others streamline their job search. The dataset, sourced from my SQL course, contains valuable insights such as job titles, salaries, locations, and essential skills within the field. 

The data originates from my [SQL Course](https://lukebarousse.com/sql) It's packed with insights on job titles, salaries, locations, and essentail skills. 

### The questions I wanted to answer through my SQL queries were: 
1. What are the top-paying data analyst jobs?
2. What skills are required for these top-paying jobs? 
3. What skills are most in demand for data analysts? 
4. Which skills are associated with higher salaries? 
5. What are the most optimal skills to learn? 
# Tools I used 
For my deep dive into the data analyst job market, I utilized essential tools that supported my workflow:

- **SQL** : Served as my foundation of my analysis, allowing me to extract, filter, and analyze key data insights.
- **PostgreSQL** : Acted as the database management system, ideal for storing and managing job posting information.  
- **Visual Studio Code** : Used for database management and executing SQL queries. 
- **Git and Github** Provided version control and collaboration capabilities, allowing me to track changes, manage code versions, and share my SQL projects effortlessly. 
# The Analysis 
Each query in this project was designed to target a specific area of the data analyst job market. Here's how I tackled each one: 

### 1. Top Paying Data Analysts Jobs 
I was first interested in knowing the highest-paying roles as a data analyst. I filtered the positions by average yearly salary. This query highlights the high paying opportunities in the field. 

```sql
SELECT 
    job_id, 
    job_title, 
    job_location, 
    job_schedule_type,
    salary_year_avg,
    name as company_name
FROM 
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Analyst' AND
    salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC
LIMIT 10;
```
Here's the breakdown of the top data analyst jobs in 2023:

- **Wide Salary Range:** The top ten paying data analyst roles go from $285,000 to $650,000 yearly.
- **Diverse Employers:** There are varies companies that offer high paying salaries like Mantys, NAHNT, Illuminate Mission Solutions, Citigroup Inc, Meta, OpenAI, showing a broad interest across different countries.
- **Job Title Variety:** There's a high diversity in job titles, from data analyst to Director of Safety Data Analysis, reflecting varied roles and specializations within data analytics. 

### 2. Skills for Top Paying Jobs 
Based on the information gathered in the first query, I wanted to know what skills were necessary to land these roles. I joined the job postings with the skills data, providing insights into what employeers value.

```sql
WITH top_paying_jobs AS (
    SELECT 
        job_id, 
        job_title, 
        salary_year_avg,
        name as company_name
    FROM 
        job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_title_short = 'Data Analyst' AND
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
Here's a breakdown of the most demanded skills for the top 10 highest paying data analyst jobs in 2023:
- **Python** is leading with a count of 4 mentions 
- **SQL, R, Excel, and Tableau** follow closely with a count of 3 mentions

### 3. In-Demand Skills for Data Analysts

```sql
    SELECT 
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE 
    job_title_short = 'Data Analyst' 
GROUP BY
    skills
ORDER BY
    demand_count DESC
LIMIT 5;
```

### 4. Skills based on Salary

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
LIMIT 25;
```
### 5. Most Optimal Skills to Learn

```sql
SELECT 
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count,
    ROUND(AVG(job_postings_fact.salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE 
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
GROUP BY
    skills_dim.skill_id
HAVING 
    COUNT(skills_job_dim.job_id) > 10
ORDER BY 
    avg_salary DESC, 
    demand_count DESC
LIMIT 25; 
```
# What I learned 
# Conclusions 