
# Introduction
📊 Dive into the data job market in Singapore 🇸🇬! Focusing on data analyst roles, this project explores top-paying jobs 💰, in-demand skills 🔥, and where high demand meets high salary in data analytics 📈.

🔍 SQL queries? Check them out here: [project_sql folder](/project_sql/)

# Background
Driven by a quest to navigate the data analyst job market more effectively, this project was born from a desire to pinpoint top-paid and in-demand skills, helping myself and others to find optimal jobs.

Data hails from [Luke Barousse's SQL Course](https://lukebarousse.com/sql). It's packed with insights on job titles, salaries, locations, and essential skills.

### The questions I wanted to answer through my SQL queries were:

1. What are the top-paying data analyst jobs in Singapore?
2. What skills are required for these top-paying jobs in Singapore?
3. What skills are most in demand for data analysts in Singapore?
4. Which skills are associated with higher salaries in Singapore?
5. What are the most optimal skills to learn in Singapore?

# Tools I Used
For my deep dive into the data analyst job market, I harnessed the power of several key tools:

- **SQL:** The backbone of my analysis, allowing me to query the database and unearth critical insights.
- **PostgreSQL:** The chosen database management system, ideal for handling the job posting data.
- **Visual Studio Code:** My go-to for database management and executing SQL queries.
- **Git & GitHub:** Essential for version control and sharing my SQL scripts and analysis, ensuring collaboration and project tracking.

# The Analysis
Each query for this project aimed at investigating specific aspects of the data analyst job market. Here’s how I approached each question:

### 1. Top Paying Data Analyst Jobs in Singapore
To identify the highest-paying roles, I filtered data analyst positions by average yearly salary and location, focusing in Singapore. This query highlights the high paying opportunities in the field.

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
LEFT JOIN 
    company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE 
    job_title_short='Data Analyst' AND
    job_location = 'Singapore' AND
    salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
LIMIT 10;
```

Here's the breakdown of the top data analyst jobs in 2023:
- **Annual Salary Range:** Top 10 paying data analyst roles span from $105,000 to $149,653, indicating significant salary potential in the field.
- **Diverse Employers:** Companies like Bosch Group, FiscalNote, and ADDX are among those offering high salaries, showing a broad interest across different industries.
- **Job Title Variety:** There's a high diversity in job titles, from Data Analyst to Research Scientist, reflecting varied roles and specializations within data analytics.

### 2. Skills for Top Paying Jobs in Singapore
To understand what skills are required for the top-paying jobs, I joined the job postings with the skills data, providing insights into what employers value for high-compensation roles.
```sql
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        job_location,
        salary_year_avg,
        job_posted_date,
        name AS company_name
    FROM 
        job_postings_fact
    LEFT JOIN 
        company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE 
        job_title_short='Data Analyst' AND
        job_location = 'Singapore' AND
        salary_year_avg IS NOT NULL
    ORDER BY
        salary_year_avg DESC
    LIMIT 10
)

SELECT 
    top_paying_jobs.*, 
    skills
FROM 
    top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    salary_year_avg DESC;
```
Here's the breakdown of the most demanded skills for the top 10 highest paying data analyst jobs in 2023:
- **Python** is leading with a bold count of 6.
- **SQL**, **Spark**, and **Tableau** follow closely with a bold count of 4.
- **Excel** is also highly sought after, with a bold count of 3.
Other skills like **Flow**, **Power BI**, **R**, and **AWS** show varying degrees of demand.

```sql
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        job_location,
        salary_year_avg,
        job_posted_date,
        name AS company_name
    FROM 
        job_postings_fact
    LEFT JOIN 
        company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE 
        job_title_short='Data Analyst' AND
        job_location = 'Singapore' AND
        salary_year_avg IS NOT NULL
    ORDER BY
        salary_year_avg DESC
    LIMIT 10
)

SELECT 
    skills,
    COUNT(*)
FROM 
    top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
GROUP BY skills
ORDER BY COUNT(*) DESC;
```
| Skills   | Demand Count |
|----------|--------------|
| python   | 6   |
| sql      | 4   |
| tableau  | 4   |
| spark    | 4   |
| excel    | 3   |
| aws      | 2   |
| r        | 2   |
| power bi | 2   |
| flow     | 2   |
| word     | 1   |
| hadoop   | 1   |
| linux    | 1   |
| looker   | 1   |
| powerpoint| 1  |

### 3. In-Demand Skills for Data Analysts in Singapore

This query helped identify the skills most frequently requested in job postings, directing focus to areas with high demand.

```sql
SELECT
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND 
    job_location = 'Singapore'
GROUP BY
    skills
ORDER BY demand_count desc
LIMIT 10;
```
Here's the breakdown of the most demanded skills for data analysts in 2023
- **SQL**, **Python**, and **Excel** remain fundamental, emphasizing the need for strong foundational skills in data processing, spreadsheet manipulation, and programming.
- **Visualization Tools** like **Tableau**, **Power BI** and **R** are essential, pointing towards the increasing importance of technical skills in data storytelling, statistics, and decision support.

| Skills   | Demand Count |
|----------|--------------|
| SQL      | 3635         |
| Python   | 3080         |
| Excel    | 2294         |
| Tableau  | 2199         |
| R        | 1315         |
| Power BI      | 1268         |
| SAS    | 726          |
| AWS   | 475          |
| Powerpoint  | 444          |
| Word | 430          |

*Table of the demand for the top 10 skills in data analyst job postings*

### 4. Skills Based on Salary in Singapore
Exploring the average salaries associated with different skills revealed which skills are the highest paying.
```sql
SELECT
    skills,
    ROUND(AVG(salary_year_avg), 0) AS average_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND 
    salary_year_avg IS NOT NULL AND
    job_location = 'Singapore'
GROUP BY
    skills
ORDER BY
    average_salary DESC;
```
| Skills   | Average Salary ($) |
|----------|--------------|
| spark      | 121,027         |
| looker   | 111,175         |
| linux    | 109,500         |
| word  | 105,838         |
| powerpoint        | 105,838         |
| flow      | 105,558         |
| python    | 103,713          |
| excel   | 100,500          |
| qlik  | 100,500          |
| zoom | 100,500          |


### 5. Most Optimal Skills to Learn in Singapore

Combining insights from demand and salary data, this query aimed to pinpoint skills that are both in high demand and have high salaries, offering a strategic focus for skill development.

```sql
SELECT
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count,
    ROUND(AVG(salary_year_avg), 0) AS average_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND 
    salary_year_avg IS NOT NULL AND
    job_location = 'Singapore'
GROUP BY
    skills_dim.skill_id
ORDER BY 
    demand_count DESC,
    average_salary DESC;
```

| Skill ID | Skills     | Demand Count | Average Salary ($) |
|----------|------------|--------------|-------------------:|
| 0        | sql         | 15           |            87,644 |
| 1      | pyton | 12           |            103,713 |
| 181       | excel     | 8           |            100,569 |
| 182       | tableau  | 8           |            95,088 |
| 5       | r      | 6           |            94,676 |

*Table of the most optimal skills for data analyst sorted by salary*

Here's a breakdown of the most optimal skills for Data Analysts in 2023: 
- **Database Technologies:** The demand for skills in traditional database like SQL is the greatest, with an average salary at $87,644, reflecting the enduring need for data storage, retrieval, and management expertise.
- **High-Demand Programming Languages:** Python and R stand out for their high demand, with demand counts of 12 and 6 respectively. Despite their high demand, their average salaries are around $103,713 for Python and $94,676 for R, indicating that proficiency in these languages is highly valued but also widely available.
- **Spreadsheet:** Excel skills has a high demand count of 8, emphasizing the need for strong foundational skills in data processing and spreadsheet manipulation.
- **Business Intelligence and Visualization Tools:** Tableau has a high demand counts of 8 with an average salary of around $95,088, highlighting the critical role of data visualization and business intelligence in deriving actionable insights from data.


# What I Learned

Throughout this adventure, I've turbocharged my SQL toolkit with some serious firepower:

- **🧩 Complex Query Crafting:** Mastered the art of advanced SQL, merging tables like a pro and wielding WITH clauses for ninja-level temp table maneuvers.
- **📊 Data Aggregation:** Got cozy with GROUP BY and turned aggregate functions like COUNT() and AVG() into my data-summarizing sidekicks.
- **💡 Analytical Wizardry:** Leveled up my real-world puzzle-solving skills, turning questions into actionable, insightful SQL queries.

# Conclusions

### Insights
From the analysis, several general insights emerged:

1. **Top-Paying Data Analyst Jobs**: The highest-paying jobs for data analysts in Singapoe is at $149,653!
2. **Skills for Top-Paying Jobs**: High-paying data analyst jobs require advanced proficiency in Python, suggesting it’s a critical skill for earning a top salary.
3. **Most In-Demand Skills**: SQL is the most demanded skill in the data analyst job market, thus making it essential for job seekers.
4. **Skills with Higher Salaries**: Specialized skills, such as Spark and Looker, are associated with the highest average salaries, indicating a premium on niche expertise.
5. **Optimal Skills for Job Market Value**: SQL leads in demand and offers for a high average salary, positioning it as one of the most optimal skills for data analysts to learn to maximize their market value.

### Closing Thoughts

This project enhanced my SQL skills and provided valuable insights into the data analyst job market. The findings from the analysis serve as a guide to prioritizing skill development and job search efforts. Aspiring data analysts in Singapore can better position themselves in a competitive job market by focusing on high-demand, high-salary skills. This exploration highlights the importance of continuous learning and adaptation to emerging trends in the field of data analytics.