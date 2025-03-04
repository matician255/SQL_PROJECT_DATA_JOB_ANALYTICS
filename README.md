# Introduction
Welcome to the data job market analysis, focusing on Data Scientist roles, in this project i explored top paying jobs, indemand skills and where high demand meets high salary in Data Science  
SQL Queries? Check them out here: [project_sql folder](/project_sql/)

# Background
Driven by a quest to navigate the data analyst job market more effectively, this project was born from a desire to pinpoint top-paid and in-demand skills, streamlining others work to find optimal jobs.

### The questions I wanted to answer through my SQL queries were:
1. What are the top-paying data analyst jobs?
2. What skills are required for these top-paying jobs?
3. What skills are most in demand for data analysts?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn?

# Tools I used 
For my deep dive into the data analyst job market, I harnessed the power of several key tools:

- **SQL:** The backbone of my analysis, allowing me to query the database and unearth critical insights.
- **PostgreSQL:** The chosen database management system, ideal for handling the job posting data.
- **Visual Studio Code:** My go-to for database management and executing SQL queries.
- **Git & GitHub:** Essential for version control and sharing my SQL scripts and analysis, ensuring collaboration and project tracking.
# The Analysis
Each query for this project aimed at investigating specific aspects of the data analyst job market. Here’s how I approached each question:

### 1. Top Paying Data Scientist Jobs
To identify the highest-paying roles, I filtered data analyst positions by average yearly salary and location, focusing on remote jobs. This query highlights the high paying opportunities in the field.

```sql
SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name as company_name

FROM 
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id=company_dim.company_id

WHERE
    job_title_short = 'Data Scientist' AND
    job_location = 'Anywhere' AND
    salary_year_avg IS NOT NULL

ORDER BY
    salary_year_avg DESC

LIMIT 10

```

### Key Insights:
- Staff Data Scientist roles command the highest salaries, with Quant Researcher positions topping the list at $550,000
- Selby Jennings and Demandbase appear twice each in the top 10 paying jobs
- All top 10 positions offer remote work ("Anywhere" location)
- Director and Head of Data Science roles dominate the high-paying positions
- There's a significant drop between the top 2 salaries ($525K+) and the rest ($375K and below)


![Top paying jobs](<project_sql/assets/top 10 jobs.png>)
*Bargraph showing the top paying data scientist jobs*


### 2. Skills for Top Paying Jobs
To understand what skills are required for the top-paying jobs, I joined the job postings with the skills data, providing insights into what employers value for high-compensation roles.

```sql
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        salary_year_avg,
        name as company_name

    FROM 
        job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id=company_dim.company_id

    WHERE
        job_title_short = 'Data Scientist' AND
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
INNER JOIN skills_job_dim ON top_paying_jobs.job_id=skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id=skills_dim.skill_id
ORDER BY
    salary_year_avg DESC
```


### Insights

- SQL and Python appear the most frequently (4 times each), highlighting their importance for data science roles.
AWS and Java are also highly sought after (3 times each), suggesting a demand for cloud and software engineering skills.

- Popular Machine Learning & AI Tools:
PyTorch, TensorFlow, and Scikit-learn are mentioned, emphasizing the need for deep learning and machine learning expertise.
Keras also appears, reinforcing the relevance of neural networks.

- Big Data & Cloud Technologies:
Spark, Hadoop, and Cassandra are listed, indicating that experience with big data frameworks is valuable.
GCP, Azure, and AWS suggest that companies are looking for cloud computing experience.
Visualization & Business Intelligence:

- Tableau is included, highlighting the importance of data visualization in data science roles.
DevOps & Deployment:

- Kubernetes suggests that containerization and model deployment are relevant skills for some roles.

![common skills](<project_sql/assets/common skills.png>)
*Bargraph showing skills for top paying jobs*

### 3. In-Demand Skills for Data Scientist
This query helped identify the skills most frequently requested in job postings, directing focus to areas with high demand.

```sql
SELECT
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id=skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id=skills_dim.skill_id
WHERE
    job_title_short = 'Data Scientist' AND
    job_work_from_home = TRUE
GROUP BY
    skills
ORDER BY
    demand_count DESC
LIMIT 5
```
### Key insight
- The dataset shows that **Python** leads in demand with a count of 10,390, which is significantly higher than the next highest skill (**SQL** at 7,488). This suggests that Python is in particularly high demand compared to other skills listed.


 | skills  | demand_count |  
 |---------|--------------|  
 | python  | 10390        |  
 | sql     | 7488         |  
 | r       | 4674         |  
 | aws     | 2593         |  
 | tableau | 2458         |  
*Table of the demand of the top 5 skills in data science postings*

### 4. Skills Based on Salary
Exploring the average salaries associated with different skills revealed which skills are the highest paying.

```sql
SELECT
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id=skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id=skills_dim.skill_id
WHERE
    job_title_short = 'Data Scientist' AND
    salary_year_avg IS NOT NULL AND
    job_work_from_home = TRUE
GROUP BY
    skills
ORDER BY
    avg_salary DESC
LIMIT 25
```
### Insights
- Regulatory and privacy expertise (GDPR) is highly valued.
- Programming languages like Golang, Rust, and Julia command high salaries.
- Cloud and data engineering skills (DynamoDB, Redis, Cassandra) are lucrative.
- Machine learning automation and computer vision (DataRobot, OpenCV) are in high demand.
- Business intelligence tools still offer strong earning potential.

![alt text](<project_sql/assets/top 10 skills.png>)
*Bargraph showing the highest paying skills*


### 5. Most Optimal Skills to Learn
Combining insights from demand and salary data, this query aimed to pinpoint skills that are both in high demand and have high salaries, offering a strategic focus for skill development.

```sql
SELECT
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
   
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id=skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id=skills_dim.skill_id
WHERE
    job_title_short = 'Data Scientist' AND
    salary_year_avg IS NOT NULL AND
    job_work_from_home = TRUE
GROUP BY
    skills_dim.skill_id
HAVING 
    COUNT(skills_job_dim.job_id) > 10
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 25;
```
### Insight
- The focus on opportunity skills reveals particular skills that, despite a lower demand count, command high salaries and might represent specialized areas to target.

| Skill ID | Skill | Demand Count | Average Salary ($) |
|-|-|-|-|
| 26 | c | 48 | 164865 |
| 8 | go | 57 | 164691 |
| 187 | qlik | 15 | 164485 |
| 185 | looker | 57 | 158715 |
| 96 | airflow | 23 | 157414 |
| 77 | bigquery | 36 | 157142 |
| 3 | scala | 56 | 156702 |
| 81 | gcp | 59 | 155811 |
| 80 | snowflake | 72 | 152687 |
| 101 | pytorch | 115 | 152603 |

*Table of the most optimal skills for data scientist sorted by salary*

# What I learned
Throughout this adventure, I've turbocharged my SQL toolkit with some serious firepower:

🧩 Complex Query Crafting: Mastered the art of advanced SQL, merging tables like a pro and wielding WITH clauses for ninja-level temp table maneuvers.
📊 Data Aggregation: Got cozy with GROUP BY and turned aggregate functions like COUNT() and AVG() into my data-summarizing sidekicks.
💡 Analytical Wizardry: Leveled up my real-world puzzle-solving skills, turning questions into actionable, insightful SQL queries.

# Conclusion
### Insights
1. Staff Data Scientist roles command the highest salaries, with Quant Researcher positions topping the list at $550,000
2. SQL and Python appear the most frequently (4 times each), highlighting their importance for data science roles.
3. The dataset shows that **Python** leads in demand with a count of 10,390, which is significantly higher than the next highest skill (**SQL** at 7,488). This suggests that Python is in particularly high demand compared to other skills listed.
4. Regulatory and privacy expertise (GDPR) is highly valued.
5. The focus on opportunity skills reveals particular skills that, despite a lower demand count, command high salaries and might represent specialized areas to target.

### Closing Thoughts
 This project enhanced my SQL skills and provided valuable insights into the data science job market. The findings from the analysis serve as a guide to prioritizing skill development and job search efforts. Aspiring data scientists can better position themselves in a competitive job market by focusing on high-demand, high-salary skills. This exploration highlights the importance of continuous learning and adaptation to emerging trends in the field of data science.
