# Introduction
    📊 Dive into the data job market! Focusing on data analysis roles, this project explores 💰 top-paying jobs, 🔥 in-demand skills, and 📈 where high demand meets high salary in data analytics.
    📌 SQL Queries? Check them out here: [project_sql folder](/project_sql/)
# Background
Driven by a quest to navigate the data analyst job market more effectively, this project was born from a desire to pinpoint top-paid and in-demand skills, streamlining others work to find optimal jobs.

Data hails from [lukeb drive](https://drive.google.com/drive/folders/1moeWYoUtUklJO6NJdWo9OV8zWjRn0rjN). It's packed with insights on job titles, salaries, locations, and essential skills.

### The questions I wanted to answer through my SQl Queries were:
1. What are the top-paying data analyst jobs?
2. Whst skills are required for these top-paying jobs?
3. What skills are most in demand for data nalaysts?
4. Which skills are associated with higher salaried?
5. What are the most optimal skills to learn?

# Tools I Used
 For my deep dive into the data analyst job market, I hardnessed the power of several key tools:
 - **SQL**: The backbone of my analysis, allowing me to query the database and unearth critical insights.
 - **PostgreSQL**: the chosen database management system, ideal for handling the job posting dtaa.
 - **Visual Stuido Code**: My go-to for database management and excuting SQL quires.

 - **Git & GitHub**: Essential for version control and collaboration and project tracking.
# The Analysis
Each query for this project aimed at investigating specific aspects of the data analyst job market. Here's hiw I approached each question:

### 1. Top Paying Data Analyst Jobs
To identify the highest-paying roles, I filtered data analyst positions by avaerage yearly salary and location. This query highlights the high paying opportunities in the field.

``` sql
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


### 2. Skills for Top Paying Jobs
To understand what skills are required for the top-paying jobs, I joined the job postings with the skills data, providing insights into what employers value for high-compensation roles.

``` sql
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
INNER JOIN skills_job_dim ON top_paying_jobs.job_id= skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    salary_year_avg DESC 
```
- SQL and Python are the most in-demand skills, appearing in nearly all high-paying data analyst roles.
- Tableau is the leading visualization tool, ranking third.
- R is still relevant, though less demanded than Python.
- Cloud & Database technologies like Snowflake and Azure are becoming more popular.


### 3. In-Demand Skills for Data Analysis
This query helped identify the skills most frequently reuested in job postings, directing focus to areas with high demand.
``` sql 
SELECT 
    skills, 
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short ='Data Analyst'
GROUP BY
    skills
ORDER BY
    demand_count DESC
LIMIT 5 
```

### 4. Skills Based on Salary
Exploring the average salaries associated with different skills revealed which skills are the highest paying.
``` sql
SELECT 
    skills, 
    ROUND (AVG(salary_year_avg),0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short ='Data Analyst' AND
    salary_year_avg IS NOT NULL
GROUP BY
    skills
ORDER BY
    avg_salary DESC
LIMIT 50 
```

### 5. Most Optimal Skills to Learn
Combining insights from demand and salary data, this query aimed to pinpoint skills that are both in high demand and have high salaries, offering a stratagic focus for skills development.
``` sql
WITH skills_demand AS (
    SELECT 
        skills_dim.skill_id,
        skills_dim.skills, 
        COUNT(skills_job_dim.job_id) AS demand_count
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short ='Data Analyst' AND
        salary_year_avg IS NOT NULL
    GROUP BY
        skills_dim.skill_id
), average_salary AS (
    SELECT 
        skills_job_dim.skill_id,
        ROUND (AVG(salary_year_avg),0) AS avg_salary
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short ='Data Analyst' AND
        salary_year_avg IS NOT NULL
    GROUP BY
        skills_job_dim.skill_id
)
SELECT 
    skills_demand.skill_id,
    skills_demand.skills,
    demand_count,
    avg_salary
FROM 
    skills_demand
INNER JOIN average_salary ON skills_demand.skill_id = average_salary.skill_id 
WHERE
    demand_count > 10
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 25;
```
# What I Learned

Throughout this adventure, I've turbocharged any SQL toolkit with some serious firepower:

- **Complex Query Crafting:** Mastering the art of advanced SQL, merging tables like a pro and wiekding WITH clauses for ninja-level temp table maneuvers.
- **Analytical Wizardry:** Leveled up my real-world puzzle-solving skills, turning questions into actionable, insightful SQL queries. 
# Conclusions

From the analysis, several general insights emerged:

1. **Top-paying Data Analyst Jobs:**
The highest-paying jobs for data analysis that allow remote work offer a wide range of salaries, the highest at $650,000!
2. **Skills for Top-Paying Jobs**: High-paying data analyst jobs require advanced proficiency in SQL, suggesting it's a critical skill for earning a top salary.
3. **Most In-Demand Skills**: SQL is also the most demanded skill in the data analyst job markey, thus making it essential for job seekers.
4. **Skills with Higher Salary**: Specialized skills, such as SVN and solidity, are associated with the highest average salaries, indicating a premium on niche expertise.
5. **Optimal Skills for Job Market Value**: SQL leads in demand and offers for a high average salary, positioning it as one of the most optimal skills for data analysts to learn to maximize their market value. 
