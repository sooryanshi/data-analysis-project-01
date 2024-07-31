# Overview

Welcome to my analysis of the data job market, focusing on data analyst roles. This project was created out of a desire to navigate and understand the job market more effectively. It delves into the top-paying and in-demand skills to help find optimal job opportunities for data analysts.

The data sourced from [Luke Barousse's Python Course](https://lukebarousse.com/python) which provides a foundation for my analysis, containing detailed information on job titles, salaries, locations, and essential skills. Through a series of Python scripts, I explore key questions such as the most demanded skills, salary trends, and the intersection of demand and salary in data analytics.

# The Questions

Below are the questions I want to answer in my project:

1. What are the skills most in demand for the top 3 most popular data roles?
2. How are in-demand skills trending for Data Analysts?
3. How well do jobs and skills pay for Data Analysts?
4. What are the optimal skills for data analysts to learn? (High Demand AND High Paying) 

# Tools I Used

For my deep dive into the data analyst job market, I harnessed the power of several key tools:

- **Python:** The backbone of my analysis, allowing me to analyze the data and find critical insights.I also used the following Python libraries:
    - **Pandas Library:** This was used to analyze the data. 
    - **Matplotlib Library:** I visualized the data.
    - **Seaborn Library:** Helped me create more advanced visuals. 
- **Jupyter Notebooks:** The tool I used to run my Python scripts which let me easily include my notes and analysis.
- **Git & GitHub:** Essential for version control and sharing my Python code and analysis, ensuring collaboration and project tracking.
  View my notebook with detailed steps here: (https://github.com/sooryanshi/data-analysis-project-01/blob/main/project.ipynb)

# Data Preparation and Cleanup

This section outlines the steps taken to prepare the data for analysis, ensuring accuracy and usability.

## Import & Clean Up Data

I start by importing necessary libraries and loading the dataset, followed by initial data cleaning tasks to ensure data quality.

```python
# Importing Libraries
import ast
import pandas as pd
import seaborn as sns
from datasets import load_dataset
import matplotlib.pyplot as plt  

# Loading Data
dataset = load_dataset('lukebarousse/data_jobs')
df = dataset['train'].to_pandas()

# Data Cleanup
df['job_posted_date'] = pd.to_datetime(df['job_posted_date'])
df['job_skills'] = df['job_skills'].apply(lambda x: ast.literal_eval(x) if pd.notna(x) else x)
```


# The Analysis

Each Jupyter notebook for this project aimed at investigating specific aspects of the data job market. Here’s how I approached each question:

## 1. What are the most demanded skills for the top 3 most popular data roles?

To find the most demanded skills for the top 3 most popular data roles. I filtered out those positions by which ones were the most popular, and got the top 5 skills for these top 3 roles. This query highlights the most popular job titles and their top skills, showing which skills I should pay attention to depending on the role I'm targeting. 


### Visualize Data

```python
def skills_per_job(job):
    dataset_per_job=df[(df.job_title_short==job)].copy()
    da.dropna(subset=['job_skills'],inplace = True)
    skills=[]
    for i in da.job_skills:
        for j in i:
            skills.append(j)
    ser1=pd.Series(skills)
    d1=pd.DataFrame(ser1.value_counts().sort_values(ascending=False).head(5))
    sns.barplot(x=d1['count'],y=d1.index,color='violet',palette='muted',hue=d1.index)
    plt.xticks(ticks= [i for i in range (0,100000,10000)])
    plt.title('Skills for '+ job)
    plt.ylabel("Skills")
    
plt.subplot(3,1,1)  
skills_per_job('Data Analyst')
plt.subplot(3,1,2)  
skills_per_job('Data Engineer')
plt.subplot(3,1,3)  
skills_per_job('Data Scientist')
plt.tight_layout()
plt.show()
```

### Results


![Likelihood of Skills Requested in Job Postings](https://github.com/sooryanshi/data-analysis-project-01/blob/main/graph01.docx)

*Bar graph visualizing the salary for the top 3 data roles and their top 5 skills associated with each.*

### Insights:

>>>>>>> main
- SQL is the most requested skill for Data Analysts and Data Engineers, with it in over half the job postings for both roles. For Data Scientists, Python is the most sought-after skill, with it in over half the job postings for Data Scientists. 
- Data Engineers require more specialized technical skills (AWS, Azure, Spark) compared to Data Analysts and Data Scientists who are expected to be proficient in more general data management and analysis tools (Excel, Tableau).
- Python is a versatile skill, highly demanded across all three roles, but most prominently for Data Scientists and Data Engineers .

## 2. How are in-demand skills trending for Data Analysts?

To find how skills are trending in 2023 for Data Analysts, I filtered data analyst positions and grouped the skills by the month of the job postings. This got me the top 5 skills of data analysts by month, showing how popular skills were throughout 2023.

### Visualize Data

```python
#finding month part of each datetime object:
month=[]
for dt in df.job_posted_date:
    m=dt.month
    month.append(m)
df['job_month']=month
da=df[df.job_title_short=='Data Analyst'].copy()
da.reset_index(inplace=True)
def skills_in_month(month):
    skills=[]
    for i in range (0, len(da)):
         if (da['job_month'][i]==month) and (da['job_skills'][i] != None):
            for j in da.job_skills[i]:
                skills.append(j)
    skills_series=pd.Series(skills)
    return skills_series.value_counts().sort_values(ascending=False).head(5)
dict={}
for i in range(1,13):
    dict[i]=skills_in_month(i)

monthskill=pd.DataFrame(dict)
monthskill=monthskill.transpose()
print(monthskill)
plt.plot()
sns.lineplot(monthskill)
plt.xticks(ticks= [i for i in range (1,13)],labels=["Jan", "Feb", "Mar", "Apr", "May", "Jun",  "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"])
plt.show()

```

### Results

![Trending Top Skills for Data Analysts](https://github.com/sooryanshi/data-analysis-project-01/blob/main/graph2.docx)
*Bar graph visualizing the trending top skills for data analysts.*

### Insights:
- SQL remains the most consistently demanded skill throughout the year, although it shows a gradual decrease in demand.
- Excel experienced a significant increase in demand starting around September, surpassing both Python and Tableau by the end of the year.
- Both Python and Tableau and Power BI show relatively stable demand throughout the year with some fluctuations, more for Python, but remain essential skills for data analysts.

- 
## 3. How well do jobs and skills pay for Data Analysts?

To identify the highest-paying roles and skills, I looked at their median salary. But first I looked at the salary distributions of common data jobs like Data Scientist, Data Engineer, and Data Analyst, to get an idea of which jobs are paid the most. 


#### Visualize Data 

```python
top_jobs=df.job_title_short.value_counts().sort_values(ascending=False).head(6).index
sample=df[df.job_title_short.isin(top_jobs)].dropna(subset=['salary_year_avg']).reset_index()
salary_sorted=sample.groupby('job_title_short')['salary_year_avg'].median().sort_values(ascending=False)
plt.plot()
sns.boxplot(y=sample.job_title_short,x=sample.salary_year_avg,color='lime')
plt.xticks(ticks=[0,200000,400000,600000,800000],labels=['0k','200k','400k','600k','800k'])
plt.show()
```

#### Results

![Salary Distributions of Data Jobs](https://github.com/sooryanshi/data-analysis-project-01/blob/main/graph3.docx)  
*Box plot visualizing the salary distributions for the top 6 data job titles.*

#### Insights

- There's a significant variation in salary ranges across different job titles. Data Scientist positions tend to have the highest salary potential, with up to $600K, indicating the high value placed on advanced data skills and experience in the industry.

- Data Analyst and  Data Scientist roles show a considerable number of outliers on the higher end of the salary spectrum, suggesting that exceptional skills or circumstances can lead to high pay in these roles. In contrast, Data Engineer roles demonstrate more consistency in salary, with fewer outliers.

- The median salaries increase with the seniority and specialization of the roles. Senior roles ( Senior Data Engineer) not only have higher median salaries but also larger differences in typical salaries, reflecting greater variance in compensation as responsibilities increase.

### Highest Paid & Most Demanded Skills for Data Analysts

Next, I narrowed my analysis and focused only on data analyst roles. I looked at the highest-paid skills and the most in-demand skills. I used two bar charts to showcase these.

#### Visualize Data

```python

sample=sample[sample.job_title_short=='Data Analyst']
sample=sample.sort_values(by='salary_year_avg',ascending=False).dropna(subset=['job_skills'])
med_sal=sample.salary_year_avg.median()
sample=sample[sample.salary_year_avg>med_sal]
sample=sample.explode('job_skills')
high_payed=sample.groupby('job_skills')['salary_year_avg'].agg(['median','count']).sort_values(by='median',ascending=False).head(10)
high_demand=sample.groupby('job_skills')['salary_year_avg'].agg(['median','count']).sort_values(by='count',ascending=False).head(10)
plt.subplot(2,1,1)
sns.barplot(x=high_payed['median'] ,y=high_payed.index,color='pink',palette='pastel',hue=high_payed['count'])
plt.title('Highest paying jobs')
plt.xlabel('Salary')
plt.ylabel('Job Skills')
plt.show()
plt.subplot(2,1,2)
sns.barplot(x=high_demand['median'] ,y=high_demand.index,color='pink',palette='pastel',hue=high_demand['count'])
plt.title('Highest demanded jobs')
plt.xlabel('Salary')
plt.ylabel('Job Skills')
plt.tight_layout()
plt.show()

```

#### Results
Here's the breakdown of the highest-paid & most in-demand skills for data analysts:

![The Highest Paid & Most In-Demand Skills for Data Analysts ](https://github.com/sooryanshi/data-analysis-project-01/blob/main/graph04.docx)
*Two separate bar graphs visualizing the highest paid skills and most in-demand skills for data analysts.*

#### Insights:

- The top graph shows specialized technical skills like `SVN`, `dplyr`, and `Node` are associated with higher salaries, some reaching up to $400K, suggesting that advanced technical proficiency can increase earning potential.

- The bottom graph highlights that foundational skills like `SQL`, `Python`, and `Tableau` are the most in-demand, even though they may not offer the highest salaries. This demonstrates the importance of these core skills for employability in data analysis roles.

- There's a clear distinction between the skills that are highest paid and those that are most in-demand. Data analysts aiming to maximize their career potential should consider developing a diverse skill set that includes both high-paying specialized skills and widely demanded foundational skills.

## 4. What are the most optimal skills to learn for Data Analysts?

To identify the most optimal skills to learn ( the ones that are the highest paid and highest in demand) I calculated the percent of skill demand and the median salary of these skills. To easily identify which are the most optimal skills to learn. 


#### Visualize Data

```python
sample2=df[(df.job_title_short=='Data Analyst')].copy()
sample2.dropna(subset=['salary_year_avg'],inplace=True)
sample2=sample2.explode('job_skills')
optimal=sample2.groupby('job_skills')['salary_year_avg'].agg(['median','count']).sort_values(by='count',ascending=False)
optimal['skill_percent']=(optimal['count']/len(sample2))*100
optimal=optimal[optimal['skill_percent']>2]
optimal=optimal.reset_index()
optimal.drop(optimal.columns[0:1], axis=1)
from adjustText import adjust_text
plt.scatter(x=optimal.skill_percent,y=optimal['median'])
plt.xlabel('Percent of Data Analyst Jobs')
plt.ylabel('Median Salary ($USD)')
plt.title('Most Optimal Skills for Data Analysts in the US')
labels=optimal.job_skills.copy()
texts = [plt.text(optimal.skill_percent[i], optimal['median'][i], labels[i], fontsize=12,color='green') for i in range(len(labels))]

adjust_text(texts, arrowprops={ 'arrowstyle': '->',  'color': 'gray'})

plt.show()

```

#### Results

![Most Optimal Skills for Data Analysts ](https://github.com/sooryanshi/data-analysis-project-01/blob/main/graph5.docx)    
*A scatter plot visualizing the most optimal skills (high paying & high demand) for data analysts.*

#### Insights:

- The skill `Python` appears to have the highest median salary of nearly $97K, also being quite common in job postings. This suggests a high value placed on specialized programming skills within the data analyst profession.

- More commonly required skills like `SQL` and `Excel` have a large presence in job listings but lower median salaries compared to specialized skills like `Python` and `Tableau`, which not only have higher salaries but are also moderately prevalent in job listings.

- Skills such as `Python`, `Tableau`, and `SQL Server` are towards the higher end of the salary spectrum while also being fairly common in job listings, indicating that proficiency in these tools can lead to good opportunities in data analytics.


# What I Learned

Throughout this project, I deepened my understanding of the data analyst job market and enhanced my technical skills in Python, especially in data manipulation and visualization. Here are a few specific things I learned:

- **Advanced Python Usage**: Utilizing libraries such as Pandas for data manipulation, Seaborn and Matplotlib for data visualization, and other libraries helped me perform complex data analysis tasks more efficiently.
- **Data Cleaning Importance**: I learned that thorough data cleaning and preparation are crucial before any analysis can be conducted, ensuring the accuracy of insights derived from the data.
- **Strategic Skill Analysis**: The project emphasized the importance of aligning one's skills with market demand. Understanding the relationship between skill demand, salary, and job availability allows for more strategic career planning in the tech industry.


# Insights

This project provided several general insights into the data job market for analysts:

- **Skill Demand and Salary Correlation**: There is a clear correlation between the demand for specific skills and the salaries these skills command. Advanced and specialized skills like Python and Oracle often lead to higher salaries.
- **Market Trends**: There are changing trends in skill demand, highlighting the dynamic nature of the data job market. Keeping up with these trends is essential for career growth in data analytics.
- **Economic Value of Skills**: Understanding which skills are both in-demand and well-compensated can guide data analysts in prioritizing learning to maximize their economic returns.


# Challenges I Faced

This project was not without its challenges, but it provided good learning opportunities:

- **Data Inconsistencies**: Handling missing or inconsistent data entries requires careful consideration and thorough data-cleaning techniques to ensure the integrity of the analysis.
- **Complex Data Visualization**: Designing effective visual representations of complex datasets was challenging but critical for conveying insights clearly and compellingly.
- **Balancing Breadth and Depth**: Deciding how deeply to dive into each analysis while maintaining a broad overview of the data landscape required constant balancing to ensure comprehensive coverage without getting lost in details.


# Conclusion

This exploration into the data analyst job market has been incredibly informative, highlighting the critical skills and trends that shape this evolving field. The insights I got enhance my understanding and provide actionable guidance for anyone looking to advance their career in data analytics. As the market continues to change, ongoing analysis will be essential to stay ahead in data analytics. This project is a good foundation for future explorations and underscores the importance of continuous learning and adaptation in the data field.


