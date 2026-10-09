# **Introduction**

This project explores the 2023 Data Analyst job market using SQL. I wanted to find out which roles pay the most, which skills employers ask for most often, and which skills are linked to higher salaries.

Using a series of PostgreSQL queries on job posting data, I looked at the top-paying Data Analyst roles, the skills they require, the most in-demand skills, and the skills that are both in demand and well paid.

🔍 SQL queries? Check them out here: [SQL_Portfolio](./SQL_Portfolio)

# **Background**

As an aspiring Data Analyst, I wanted to understand the current job market and see which technical skills open the most opportunities. Instead of relying on assumptions, I analyzed real job posting data from 2023.

The dataset contains job titles, salaries, locations, remote work availability and required technical skills. Using SQL, I looked for trends that could help future Data Analysts decide which skills to prioritize. 

The questions I wanted to answer through my SQL queries were:

* What are the top-paying data analyst jobs?

* What skills are required for these top-paying jobs?

* What skills are most in demand for data analysts?

* Which skills are associated with higher salaries?

* What are the most optimal skills to learn?

# 🛠️ Tools & Technologies

This project relied on several tools and technologies to perform data analysis and document the results.

| Tool | Purpose |
|:------|:--------|
| **SQL** | Queried and analyzed the job posting dataset. |
| **PostgreSQL** | Managed the relational database containing the job data. |
| **Visual Studio Code** | Developed and executed SQL queries. |
| **Git** | Tracked project changes using version control. |
| **GitHub** | Published the project and documented the analysis. |
# 📊 The Analysis

This project consists of several SQL queries designed to explore different aspects of the 2023 Data Analyst job market. Each query answers a specific business question and uncovers insights about salaries, skill demand, and career opportunities.

## 1. Top Paying Data Analyst Jobs

The first analysis focuses on identifying the highest-paying remote Data Analyst positions. I filtered the dataset to include only remote Data Analyst roles with a reported annual salary, then sorted the results from highest to lowest salary. This provides a clear view of the most lucrative opportunities available in the job market.

```sql
SELECT 
     job_id,
     job_title,
     job_location,
     job_schedule_type,
     job_posted_date, 
     salary_year_avg,
     name AS company_name
FROM job_postings_fact
LEFT JOIN company_dim
    ON job_postings_fact.company_id = company_dim.company_id
WHERE job_title_short = 'Data Analyst'
  AND job_location = 'Anywhere'
  AND salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC
LIMIT 10;
```

### 📌 Key Insights

- **High Salary Potential:** The top 10 remote Data Analyst positions offer annual salaries ranging from **$184,000 to over $650,000**, highlighting the strong earning potential for experienced professionals.
- **Opportunities Across Industries:** High-paying roles are available at companies from different sectors, demonstrating that demand for data analysts extends beyond the technology industry.
- **Career Growth:** The results include a variety of positions, from **Data Analyst** to senior leadership roles such as **Director of Analytics**, illustrating multiple career advancement paths within analytics.
- **Remote Work Advantage:** All roles in this analysis are remote, showing that remote opportunities can offer highly competitive compensation.

![Top Paying Data Analyst Jobs](/SQL_Portfolio/Images/Top%20paying%20jobs.png)

*Figure 1. Bar chart showing the annual salaries of the top 10 highest-paying remote Data Analyst jobs in 2023. The visualization was created from the SQL query results.*
## 2. Skills Required for the Top Paying Jobs

After identifying the highest-paying Data Analyst positions, I wanted to determine which technical skills employers expect for these roles. To answer this, I combined the top-paying job listings with the skills tables using SQL joins. This analysis reveals the technologies and tools most frequently requested by employers offering the highest salaries.

```sql
WITH top_paying_jobs AS (
SELECT 
     job_id,
     job_title,
     salary_year_avg,
     name AS company_name
FROM job_postings_fact
LEFT JOIN company_dim
    ON job_postings_fact.company_id = company_dim.company_id
WHERE job_title_short = 'Data Analyst'
  AND job_location = 'Anywhere'
  AND salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC
LIMIT 10
)

SELECT
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim
    ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim
    ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY salary_year_avg DESC;
```
### 📌 Key Insights

- **SQL** appeared as one of the most frequently requested skills among the highest-paying Data Analyst positions.
- **Python** and **Tableau** were also common requirements, emphasizing the importance of programming and data visualization.
- Cloud technologies and analytical tools were present in several job postings, suggesting that employers value modern data platforms alongside traditional analytics skills.
- The results show that high-paying Data Analyst roles typically require a combination of database, programming, and visualization skills rather than expertise in a single tool.
![Skills Required for Top Paying Jobs](/SQL_Portfolio/Images/Top%20paying%20skill_count.png)
*Figure 2. Most frequently requested skills across the top 10 highest-paying remote Data Analyst jobs in 2023.*
 
## 3. Most In-Demand Skills for Data Analysts

To identify the skills employers request most frequently, I analyzed all Data Analyst job postings and counted how often each skill appeared. This analysis highlights the core technical skills that are consistently sought after in the job market.

```sql
SELECT
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim
    ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim
    ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst'
GROUP BY skills
ORDER BY demand_count DESC;
```
### 📌 Key Insights

- **SQL** ranked as the most requested skill, confirming its importance as the foundation of data analysis.
- **Excel** remains one of the most valuable tools, showing that spreadsheet-based analysis is still widely used across organizations.
- **Python** continues to be highly demanded, reflecting the growing need for automation, data manipulation, and advanced analytics.
- **Tableau** and **Power BI** demonstrate the increasing importance of data visualization and business intelligence for communicating insights.
- Overall, employers seek Data Analysts who combine strong database skills, programming knowledge, and visualization expertise.

| Skills | Demand Count |
|:------ | -----------:|
| SQL | **7,291** |
| Excel | **4,611** |
| Python | **4,330** |
| Tableau | **3,745** |
| Power BI | **2,609** |

 Table of the demand for the top 5 skills in data analyst job postings
 ## 4. Highest-Paying Skills for Data Analysts

To determine which technical skills are associated with the highest salaries, I calculated the average annual salary for each skill mentioned in remote Data Analyst job postings. This analysis highlights the technologies that provide the greatest earning potential.

```sql
SELECT
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim
    ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim
    ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst'
  AND salary_year_avg IS NOT NULL
  AND job_work_from_home = TRUE
GROUP BY skills
ORDER BY avg_salary DESC
LIMIT 25;
```
### 📌 Key Insights

- **Big Data technologies** such as **PySpark** and **Databricks** are associated with some of the highest average salaries, reflecting the increasing demand for professionals who can process and analyze large-scale datasets.
- **Python-based tools** including **Pandas**, **NumPy**, and **Jupyter** appear among the highest-paying skills, highlighting the value of programming and advanced data analysis capabilities.
- **Cloud and data engineering platforms** such as **Kubernetes**, **Airflow**, and **GCP** indicate that experience with modern data infrastructure can significantly increase earning potential.
- **Machine learning and AI technologies**, including **DataRobot** and **Watson**, also rank among the highest-paying skills, showing that employers reward analysts who can contribute to predictive analytics and intelligent decision-making.
- Overall, the highest-paying Data Analyst roles favor professionals with a combination of **analytics, programming, cloud computing, and data engineering** skills.

| Skill | Average Salary ($) |
|:----------------|-------------------:|
| PySpark | $208,172 |
| Bitbucket | $189,155 |
| Couchbase | $160,515 |
| Watson | $160,515 |
| DataRobot | $155,486 |
| GitLab | $154,500 |
| Swift | $153,750 |
| Jupyter | $152,777 |
| Pandas | $151,821 |
| Elasticsearch | $145,000 |
 
 Table of the average salary for the top 10 paying skills for data analysts
## 5. Most Optimal Skills to Learn

For the final analysis, I combined skill demand with average salary to identify the most valuable skills for Data Analysts. By comparing how frequently each skill appears in job postings alongside its average salary, this analysis highlights the technologies that offer both strong market demand and competitive compensation.

```sql
WITH skills_demand AS (
    SELECT
        skills_dim.skill_id,
        skills_dim.skills,
        COUNT(skills_job_dim.job_id) AS demand_count
    FROM job_postings_fact
    INNER JOIN skills_job_dim
        ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim
        ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE job_title_short = 'Data Analyst'
      AND salary_year_avg IS NOT NULL
      AND job_work_from_home = TRUE
    GROUP BY skills_dim.skill_id
),

average_salary AS (
    SELECT
        skills_dim.skill_id,
        ROUND(AVG(salary_year_avg), 0) AS avg_salary
    FROM job_postings_fact
    INNER JOIN skills_job_dim
        ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim
        ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE job_title_short = 'Data Analyst'
      AND salary_year_avg IS NOT NULL
      AND job_work_from_home = TRUE
    GROUP BY skills_dim.skill_id
)

SELECT
    skills_demand.skill_id,
    skills_demand.skills,
    demand_count,
    avg_salary
FROM skills_demand
INNER JOIN average_salary
    ON skills_demand.skill_id = average_salary.skill_id
WHERE demand_count > 10
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 25;
```

| Skill | Demand Count | Average Salary ($) |
|:--------------|-------------:|-------------------:|
| Go | 27 | $115,320 |
| Confluence | 11 | $114,210 |
| Hadoop | 22 | $113,193 |
| Snowflake | 37 | $112,948 |
| Azure | 34 | $111,225 |
| BigQuery | 13 | $109,654 |
| AWS | 32 | $108,317 |
| Java | 17 | $106,906 |
| SSIS | 12 | $106,683 |
| Jira | 20 | $104,918 |

Table of the most optimal skills for data analyst sorted by salary

### 📌 Key Insights

- **Python**, **SQL**, and **Cloud technologies** continue to offer an excellent balance between employer demand and earning potential.
- Skills related to **big data platforms**, including **Snowflake**, **Azure**, **AWS**, and **BigQuery**, provide strong salaries while remaining consistently requested by employers.
- Tools such as **Hadoop**, **Go**, and **Java** demonstrate that technical and programming skills can significantly increase career opportunities for Data Analysts.
- Focusing on skills that are both highly demanded and well paid is a practical strategy for long-term career growth and increased earning potential.

# 📚 What I Learned

Working on this project strengthened both my SQL skills and my understanding of real-world data analysis. Some of the key concepts and techniques I practiced include:

- **Writing Complex SQL Queries:** Improved my ability to combine multiple tables using different types of joins and organize complex logic with Common Table Expressions (CTEs).
- **Data Aggregation & Analysis:** Gained hands-on experience using aggregate functions such as `COUNT()`, `AVG()`, and `ROUND()`, along with `GROUP BY` to summarize and analyze large datasets.
- **Query Optimization:** Learned how to structure SQL queries more efficiently to answer business questions clearly and accurately.
- **Business-Oriented Problem Solving:** Developed the ability to translate business questions into SQL queries and interpret the results to generate meaningful insights.
- **Data Storytelling:** Improved my ability to communicate findings through tables, visualizations, and concise written insights suitable for technical documentation.

# ✅ Conclusions

This project provided valuable insights into the 2023 Data Analyst job market and highlighted the relationship between technical skills, employer demand, and salary potential.

### Key Takeaways

- **Remote Data Analyst roles** offer highly competitive salaries, with top positions exceeding **$650,000** per year.
- **SQL** remains the most essential skill, consistently appearing as both the most requested skill and a requirement for many high-paying roles.
- **Python, Tableau, and Excel** continue to be core technologies that employers expect from Data Analysts.
- **Big data and cloud technologies**, including **PySpark**, **Databricks**, **Snowflake**, and **AWS**, are associated with higher average salaries, reflecting the industry's shift toward modern data platforms.
- The most valuable career strategy is to focus on skills that combine **strong market demand** with **high earning potential**, as these provide the greatest long-term opportunities for Data Analysts.

Overall, this project demonstrates how SQL can be used to transform raw job posting data into actionable insights that support career planning and data-driven decision-making.

# 💭 Closing Thoughts
This project was a valuable opportunity to practice SQL using real-world job market data. It strengthened my technical skills while improving my ability to analyze data and communicate insights effectively. I look forward to continuing to build projects that solve real business problems and showcase my growth as a Data Analyst.



---

## 👨‍💻 Author

- **Developer:** Abdulkadir Nor Salah
- **Learning Resource:** Based on the SQL for Data Analytics course by Luke Barousse.
