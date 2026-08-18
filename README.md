# walmart_sales
Walmart Data Analysis: End-to-End SQL + Python Project P-9
Project Overview
Project Pipeline

<img width="1598" height="753" alt="image" src="https://github.com/user-attachments/assets/f084067e-2ee4-482f-bd3b-3afbe169c6b5" />
This project is an end-to-end data analysis solution designed to extract critical business insights from Walmart sales data. We utilize Python for data processing and analysis, SQL for advanced querying, and structured problem-solving techniques to solve key business questions. The project is ideal for data analysts looking to develop skills in data manipulation, SQL querying, and data pipeline creation.

Project Steps
1. Set Up the Environment
Tools Used: Visual Studio Code (VS Code), Python, SQL (MySQL and PostgreSQL)
Goal: Create a structured workspace within VS Code and organize project folders for smooth development and data handling.
2. Set Up Kaggle API
API Setup: Obtain your Kaggle API token from Kaggle by navigating to your profile settings and downloading the JSON file.
Configure Kaggle:
Place the downloaded kaggle.json file in your local .kaggle folder.
Use the command kaggle datasets download -d <dataset-path> to pull datasets directly into your project.
3. Download Walmart Sales Data
Data Source: Use the Kaggle API to download the Walmart sales datasets from Kaggle.
Dataset Link: Walmart Sales Dataset
Storage: Save the data in the data/ folder for easy reference and access.
4. Install Required Libraries and Load Data
Libraries: Install necessary Python libraries using:
pip install pandas numpy sqlalchemy mysql-connector-python psycopg2
Loading Data: Read the data into a Pandas DataFrame for initial analysis and transformations.
5. Explore the Data
Goal: Conduct an initial data exploration to understand data distribution, check column names, types, and identify potential issues.
Analysis: Use functions like .info(), .describe(), and .head() to get a quick overview of the data structure and statistics.
6. Data Cleaning
Remove Duplicates: Identify and remove duplicate entries to avoid skewed results.
Handle Missing Values: Drop rows or columns with missing values if they are insignificant; fill values where essential.
Fix Data Types: Ensure all columns have consistent data types (e.g., dates as datetime, prices as float).
Currency Formatting: Use .replace() to handle and format currency values for analysis.
Validation: Check for any remaining inconsistencies and verify the cleaned data.
7. Feature Engineering
Create New Columns: Calculate the Total Amount for each transaction by multiplying unit_price by quantity and adding this as a new column.
Enhance Dataset: Adding this calculated field will streamline further SQL analysis and aggregation tasks.
8. Load Data into MySQL and PostgreSQL
Set Up Connections: Connect to MySQL and PostgreSQL using sqlalchemy and load the cleaned data into each database.
Table Creation: Set up tables in both MySQL and PostgreSQL using Python SQLAlchemy to automate table creation and data insertion.
Verification: Run initial SQL queries to confirm that the data has been loaded accurately.
9. SQL Analysis: Complex Queries and Business Problem Solving
Business Problem-Solving: Write and execute complex SQL queries to answer critical business questions, such as:
Revenue trends across branches and categories.

BUSSINESS QUESTIONS
Q1 find different payment method and number of transactions , number of qty sold 

```sq
select payment_method, count(*),sum(quantity)as no_qty_sold from walmart group by payment_method;
```


Q2 Identify the highest_rated category in the each branch, displaying the branch , category AVG rating
```sq
select * from(select branch, category,AVG(rating)as avg_rating rank() over(partition by branch order by AVG(rating) desc)from walmart group by 1,2)where rank=1
```
Q3 Identify the busiest day for each branch based on the number of transactions

Q4 calculate the total quantity of items sold per payment 
```sq
 select payment_method, count(*),sum(quantity)as no_qty_sold from walmart group by payment_method;
```
+----------------+----------+-------------+
| payment_method | count(*) | no_qty_sold |
+----------------+----------+-------------+
| Ewallet        |     3881 |        8932 |
| Cash           |     1832 |        4984 |
| Credit card    |     4256 |        9567 |
+----------------+----------+-------------+

Q5 --determine the vg, min,max rating of the category for the each city
-- list the city avg rating , min_rating a nd max_rating 
```sq
select 
    city,
    category,
    min(rating) as min_rating,
    max(rating) as max_rating,
    avg(rating) as avg_rating

from walmart
group by 1,2
```
+----------------------+------------------------+------------+------------+--------------------+
| city                 | category               | min_rating | max_rating | avg_rating         |
+----------------------+------------------------+------------+------------+--------------------+
| San Antonio          | Health and beauty      |          5 |        9.1 |               7.05 |
| Harlingen            | Electronic accessories |        9.6 |        9.6 |                9.6 |
| Haltom City          | Home and lifestyle     |          3 |        9.5 |  6.227777777777778 |
| Bedford              | Health and beauty      |        6.1 |        9.3 |               8.15 |
| Irving               | Sports and travel      |        5.3 |        5.3 |                5.3 |
| Denton               | Electronic accessories |        4.1 |          9 |                6.7 |
| Cleburne             | Electronic accessories |        5.8 |        7.8 |               7.25 |
| Canyon               | Home and lifestyle     |          3 |          9 |               6.25 |
| Grapevine            | Health and beauty      |        7.2 |        7.2 |                7.2 |
| Texas City           | Food and beverages     |        5.5 |        5.9 |                5.7 |
| Irving               | Fashion accessories    |          3 |        9.8 |  6.206896551724138 |
| San Angelo           | Electronic accessories |          3 |          7 | 5.8307692307692305 |
| Abilene              | Electronic accessories |        7.1 |        8.8 |  7.966666666666666 |


Q6
--calculate the total profit for each category by considering total_profit
as (unit_price* qty *profit_margin).
list catergory and total_profit, ordered from high to low
```sq
select
category,
sum(total_price) as total_revenue,
sum(total_price*profit_margin) as profit
from walmart
group by 1;
```
+------------------------+--------------------+--------------------+
| category               | total_revenue      | profit             |
+------------------------+--------------------+--------------------+
| Health and beauty      |  46851.17999999998 |         18671.7345 |
| Electronic accessories |  78175.02999999998 | 30772.489499999978 |
| Home and lifestyle     |          489250.06 |  192213.6380999999 |
| Sports and travel      |  52497.93000000002 | 20613.808199999996 |
| Food and beverages     |  53471.28000000006 | 21552.862200000003 |
| Fashion accessories    | 489480.89999999997 | 192314.89320000037 |
+------------------------+--------------------+--------------------+
6 rows in set (0.03 sec)

Q7 determine the most common payment method fro each branch, 
display branch and the preferrd_payment_method
```sq
 WITH cte AS (
    ->     SELECT
    ->         branch,
    ->         payment_method,
    ->         COUNT(*) AS total_trans,
    ->         RANK() OVER (
    ->             PARTITION BY branch
    ->             ORDER BY COUNT(*) DESC
    ->         ) AS payment_rank
    ->     FROM walmart
    ->     GROUP BY branch, payment_method
    -> )
    -> SELECT *
    -> FROM cte
    -> WHERE payment_rank = 1;
```
+---------+----------------+-------------+--------------+
| branch  | payment_method | total_trans | payment_rank |
+---------+----------------+-------------+--------------+
| WALM001 | Ewallet        |          45 |            1 |
| WALM002 | Ewallet        |          37 |            1 |
| WALM003 | Credit card    |         115 |            1 |
| WALM004 | Ewallet        |          44 |            1 |
| WALM005 | Ewallet        |          56 |            1 |
| WALM006 | Ewallet        |          50 |            1 |
| WALM007 | Ewallet        |          52 |            1 |
| WALM008 | Ewallet        |          39 |            1 |
| WALM009 | Credit card    |         139 |            1 |
| WALM010 | Ewallet        |          47 |            1 |
| WALM011 | Ewallet        |          39 |            1 |
| WALM012 | Ewallet        |          52 |            1 |
| WALM013 | Ewallet        |          44 |            1 |
| WALM014 | Ewallet        |          28 |            1 |
| WALM015 | Ewallet        |          57 |            1 |


Q8 categorize sales into 3 group moring,afternoon, evening,
find out which of the shif and number of invoices
```sq
SELECT *,
       CASE
           WHEN HOUR(CAST(time AS TIME)) < 12 THEN 'morning'
           WHEN HOUR(CAST(time AS TIME)) BETWEEN 12 AND 17 THEN 'afternoon'
           ELSE 'evening'
       END AS day_time
       COUNT(*)
FROM walmart;
GROUP BY 1
```
 SELECT
    ->     CASE
    ->         WHEN HOUR(CAST(time AS TIME)) < 12 THEN 'morning'
    ->         WHEN HOUR(CAST(time AS TIME)) BETWEEN 12 AND 17 THEN 'afternoon'
    ->         ELSE 'evening'
    ->     END AS day_time,
    ->     COUNT(*) AS total_transactions
    -> FROM walmart
    -> GROUP BY day_time,;
+-----------+--------------------+
| day_time  | total_transactions |
+-----------+--------------------+
| afternoon |               4636 |
| morning   |               2087 |
| evening   |               3246 |
+-----------+--------------------+
3 rows in set (0.03 sec)


Q9 Identify 5 branch with highest decrese ratio in revenue 
compare to last year(current yr 2023 and last year 2022)

rdr == last_rev-cr_rev/ls_rev*100 
```sq
WITH branch_revenue AS (
    SELECT
        branch,
        SUM(CASE 
            WHEN YEAR(date) = 2022 THEN total_price
            ELSE 0 
        END) AS last_year_revenue,
        SUM(CASE 
            WHEN YEAR(date) = 2023 THEN total_price
            ELSE 0 
        END) AS current_year_revenue
    FROM walmart
    GROUP BY branch
),
revenue_decrease AS (
    SELECT
        branch,
        last_year_revenue,
        current_year_revenue,
        ROUND(
            (last_year_revenue - current_year_revenue)
            / NULLIF(last_year_revenue, 0) * 100,
            2
        ) AS decrease_ratio
    FROM branch_revenue
)
SELECT *
FROM revenue_decrease
WHERE decrease_ratio > 0
ORDER BY decrease_ratio DESC
LIMIT 5;
```
Identifying best-selling product categories.
Sales performance by time, city, and payment method.
Analyzing peak sales periods and customer buying patterns.
Profit margin analysis by branch and category.
Documentation: Keep clear notes of each query's objective, approach, and results.
11. Project Publishing and Documentation
Documentation: Maintain well-structured documentation of the entire process in Markdown or a Jupyter Notebook.
Project Publishing: Publish the completed project on GitHub or any other version control platform, including:
The README.md file (this document).
Jupyter Notebooks (if applicable).
SQL query scripts.
Data files (if possible) or steps to access them.
Requirements
Python 3.8+
SQL Databases: MySQL, PostgreSQL
Python Libraries:
pandas, numpy, sqlalchemy, mysql-connector-python, psycopg2
Kaggle API Key (for data downloading)
Getting Started
Clone the repository:
git clone <repo-url>
Install Python libraries:
pip install -r requirements.txt
Set up your Kaggle API, download the data, and follow the steps to load and analyze.
Project Structure
|-- data/                     # Raw data and transformed data
|-- sql_queries/              # SQL scripts for analysis and queries
|-- notebooks/                # Jupyter notebooks for Python analysis
|-- README.md                 # Project documentation
|-- requirements.txt          # List of required Python libraries
|-- main.py                   # Main script for loading, cleaning, and processing data
Results and Insights
This section will include your analysis findings:

Sales Insights: Key categories, branches with highest sales, and preferred payment methods.
Profitability: Insights into the most profitable product categories and locations.
Customer Behavior: Trends in ratings, payment preferences, and peak shopping hours.
Future Enhancements
Possible extensions to this project:

Integration with a dashboard tool (e.g., Power BI or Tableau) for interactive visualization.
Additional data sources to enhance analysis depth.
Automation of the data pipeline for real-time data ingestion and analysis.
License
This project is licensed under the MIT License.

Acknowledgments
Data Source: Kaggle’s Walmart Sales Dataset
Inspiration: Walmart’s business case studies on sales and supply chain optimization.
About
No description, website, or topics provided.
Resources
Readme
Activity
Stars
45 stars
Watchers
1 watching
Forks
90 forks
Report repository
Releases
No releases published
Contributors
1
 (1)
@najirh
najirhNajir H.
Languages
Jupyter Notebook
100%
Footer
© 2026 GitHub, Inc.
Footer navigation
Terms
Privacy
Security
Stat
