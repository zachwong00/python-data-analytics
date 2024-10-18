# Overview
This project analyzes the 2023 data job market, mainly focusing on data analyst roles.

**Purpose:** Navigate and understand the job market more effectively for the data-related roles in the US and Malaysian job market. Delves into top-paying and in-demand skills to help find optimal job opportunities for data analysts.

**Data Source**: The dataset for this project originates from [Luke Barousse's repository](https://huggingface.co/datasets/lukebarousse/data_jobs), data is collected from Google's search results for the year 2023 around the globe.

*Caveats about the data for this project is reported [here](#caveats).*

# The Questions
Below are the questions I want to answer in my project:

1. What are the skills most in demand for the top 3 most popular data roles?
2. How are in-demand skills trending for Data Analysts?
3. How well do jobs and skills pay for Data Analysts?
4. What are the optimal skills for data analysts to learn? (High Demand AND High Paying)

## Tools
- **Python:**: Conducting data processing and analysis to find insights with **pandas, matplotlib** and **seaborn** libraries.
- **Jupyter Notebooks**: Running the python scripts to include notes and analysis.
- **Visual Studio Code**: Program to run the python environment.

# Data Preparation & Cleanup
### Import & Clean-up Data
I start by importing the libraries and loading the dataset, followed by initial data cleaning tasks to ensure data quality.
```python
# Import Libraries
import ast
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
from datasets import load_dataset

# Load data
dataset = load_dataset('lukebarousse/data_jobs')
df = dataset['train'].to_pandas()

# Initial data cleaning
df['job_posted_date'] = pd.to_datetime(df['job_posted_date'])
df['job_skills'] = df['job_skills'].apply(lambda x: ast.literal_eval(x) if pd.notna(x) else x)
```

### Filtering US and Malaysia Job Market
I focused my analysis on two job markets of interest, narrowing down to data roles in the US and Malaysia.
```python
df_us = df[(df['job_country'] == 'United States')]
df_my = df[(df['job_country'] == 'Malaysia')]
```

#### Limitations in mind
The filtered US dataset contains a larger sample **(20.6k observations)** while the Malaysian dataset has a more limited representation **(~9.2k observations)**. Although the difference in sample size presents some limitations in direct comparison, insights from both regions provide valuable perspectives on market trends and skills demand.

# Analysis
## 1. What are the skills most in demand for the top 3 most popular data roles?
Highlighting the most popular job titles for hiring and their associated top demanded skills to be paid attention to.

View my notebook for detailed steps [here](https://github.com/zachwong00/python-data-analytics/blob/main/Notebooks/02_SkillDemand.ipynb).

#### Visualizing the Data
```python
for i, job_title in enumerate(job_titles):
    # Create plot df based on job title filtered from job_titles & top 5 values
    df_usplot = df_usskills_perc[df_usskills_perc['job_title_short'] == job_title].head(5)

    # Subplots for each job titles
    plt.subplot(len(job_titles), 1, i + 1)  # i + 1 because subplot index starts from 1
    sns.barplot(data=df_usplot, 
                x='skills_percent', 
                y='job_skills', 
                hue = 'skills_percent', 
                palette='crest', 
                legend = False)
```
![image-2](https://github.com/user-attachments/assets/937bb78e-bc33-4b37-a2ab-1e65f1a47a97)
*![image-3](https://github.com/user-attachments/assets/d4d34abd-d698-494d-8527-e585677ba4c5)

Bar graph to visualize the top 3 data roles and top 5 most in-demand skills for each role.*
### Insights
- For Data Analysts and Data Engineers, SQL is the most requested skill to have. Data Scientist has Python as its most-demanded skill instead.
- Python is still versatile, appearing in the top 5 skills in all top 3 roles, indicating high demand by organizations to have python knowledge.
- Data Analysts and Data Scientist in both job markets are expected to be proficient in general data management and analytics tools (SQL, Python, Tableau), compared to Data Engineers that demand more specialized skills (AWS, Azure, Spark).

## 2. How are in-demand skills trending for Data Analysts?
Highlighting the top 5 skills of data analysts over time by month, displaying how popular the skills were throughout 2023. The job market datasets are further filtered to only contain data analyst positions.

View my notebook for detailed steps [here](https://github.com/zachwong00/python-data-analytics/blob/main/Notebooks/03_SkillTrends.ipynb).

#### Visualizing the Data
```python
df_usplot = df_usperc.iloc[:, :5] # All the rows, and first 5 columns

sns.set_theme(style='ticks')
sns.lineplot(df_usplot, dashes = False, palette = 'tab10', legend = False)
sns.despine()

from matplotlib.ticker import PercentFormatter
ax = plt.gca()
ax.yaxis.set_major_formatter(PercentFormatter(decimals = 0))
```
![image-4](https://github.com/user-attachments/assets/1fcf8f41-f81c-4af2-b007-8221d4ede438)
![image-5](https://github.com/user-attachments/assets/aa88e1a7-ae95-4a29-8e2a-19e914ed83f9)

*Line graph to visualize the top skills for Data Analysts over time.*
### Insights
-  SQL is consistenly the cornerstone, top-demand skill throughout the year in both markets, although the US market seen a gradual decrease due to slow hiring approaching to the end of 2023. 
- Interestingly, Excel maintains a strong presence, coming in second for both markets.
- Both Tableau and Python are contending skills required for Data Analysts, albeit the Malaysian market demands greater need for Python throughout the year.
- The Malaysian market appears to have a very volatile trend, but this is partially due to the limited sample compared to the US market dataset.

## 3. How well do jobs and skills pay for Data Analysts?
This analysis attempts to examine which data analytics skills are the highest-paid and most in-demand for the US and Malaysia job market.

View my notebook for detailed steps [here](https://github.com/zachwong00/python-data-analytics/blob/main/Notebooks/04_SalaryAnalysis.ipynb).

#### Visualizing the Data
```python
fig, ax = plt.subplots(2,1)

# US Graph - Highest Paid
sns.barplot(data=df_us_top10pay, x = 'median', y = df_us_top10pay.index, ax=ax[0], hue='median', palette='dark:g_r')

# US Graph - In-Demand
sns.barplot(data=df_us_topskills, x = 'median', y = df_us_topskills.index, ax=ax[1], hue='median', palette='light:g')
```
![image-13](https://github.com/user-attachments/assets/e9f108cd-3055-4209-86b6-5732333134be)
![image-12](https://github.com/user-attachments/assets/cb9aad02-f4b7-46f8-b6ed-4b1b1f310a32)

### Insights
- The graphs show differences in the two markets, US favors niched specialized skills such as `dplyr`/`Bitbucket` that are associated with higher pay while Malaysia suggests a higher pay focusing on core data analytics skills like `azure`/`BigQuery`/`Spark`. 
- As for in-demand skills, while the orders are different, both US and Malaysia job markets have overlap in requesting the core, foundational skills such as  `Excel`, `SQL`, `Tableau` and `Python` from data analysts.
- While not lucrative in pay, they indicate the importance to pick up these foundational skills for employability in data analysis roles.

## 4. What are the optimal skills for data analysts to learn?
Identifying which skills are the most optimal to learn, these balances both in salary and demand. I calculated the skill demand and median salary of these skills, grouped by their respective technology.

View my notebook for detailed steps [here](https://github.com/zachwong00/python-data-analytics/blob/main/Notebooks/05_OptimalSkill.ipynb).

#### Visualizing the Data
```python
sns.scatterplot(
    data=df_us_skills_tech_filter, 
    x = 'skill_percent', 
    y = 'median_salary',
    hue = 'technology')
sns.despine()
```
![image](https://github.com/user-attachments/assets/805e80ef-a760-4ef8-aec4-c8134c5f99fe)

*Scatterplots to illustrating optimal skills to learn*

### Insights
- The scatter plot shows that most of the `programming` skills (colored blue) tend to cluster at higher salary levels compared to other categories, indicating that programming expertise might offer greater salary benefits within the data analytics field.

- The `database` skills (colored orange), such as Oracle and SQL Server, are associated with some of the highest salaries among data analyst tools. This indicates a significant demand and valuation for data management and manipulation expertise in the industry.
- `Analyst tools` (colored green), including Tableau and Power BI, are prevalent in job postings and offer competitive salaries, showing that visualization and data analysis software are crucial for current data roles. This category not only has good salaries but is also versatile across different types of data tasks.

*The Malaysian data cannot be compared for this question as the data has insufficient size and has extreme outliers.*

# Overall Insights
This project provided several general insights into the data job market for analysts:

- **Skill Demand and Salary Correlation:** There is a clear correlation between the demand for specific skills and the salaries these skills command. Advanced and specialized skills like Python and Oracle often lead to higher salaries.
- **Market Trends:** There are changing trends in skill demand, highlighting the dynamic nature of the data job market. Keeping up with these trends is essential for career growth in data analytics.
-  **Economic Value of Skills:** Understanding which skills are both in-demand and well-compensated can guide data analysts in prioritizing learning to maximize their economic returns.


# Caveats 
- **Malaysia Salary Information:** Analysis in the Malaysian job market using salary values is limited and may not be representative of the actual industry, since salary figures are not publicly disclosed in job postings (many missing values).
- **Data Inconsistencies:** An equal comparison and analysis between both markets is not possible due to large differences between both data's sample size. Hence, the Malaysian market can only be taken as a preliminary, surface-leveled analysis. 
- **Insufficient Data:** Due to lower sample size for the Malaysia job market, there are more issues arise when futher filtered down, leading to certain analyses that cannot be performed with confidence. 
