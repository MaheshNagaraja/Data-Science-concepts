# Pandas vs SQL

Pandas and SQL are distinctly used across industries. Both have their own strengths and purposes.

__SQL__ - Querying and Managing relational database.  
__Pandas__ - Data manupilation and analysis in python.

Here are key points of both SQL and Pandas:  

__1)__ __Data storage__ :  
Pandas work with data load into memory (RMA).  
SQL data is stored in a database, which can handle much larger datasets compared to Pandas. SQL queries operate on this stored data without needing to load it all into memory at once.

__2__ __Querying and Speed__ :
SQL and Pandas can perform operation like filtering, sorting, joining tables, and performing aggregations and grouping operations. But SQL is more optimized compared to pandas when it comes to large datasets.  

*Speed* : Pandas is suitable for small to medium datasets. Operations on large datasets can become slower as data grows beyond memory.
        SQL databases are highly optimized for querying large datasets.  

__3__ __Ease of use__ :  
SQL is eaiser compared to Pandas because pandas needs python knowledge as it offers a rich set of functions for data manipulation, but mastering them takes time.  

__4__ __Performance__ :  
Pandas works well on smaller datasets but when data grows, memory usage and performance may degrade. Wherease, SQL is designed for large-scale data processing with query optimization engines that allows to handel much larger datasets efficiently.  

__5__ __Environment__ :  
SQL is typically used in DBMS or command-line interfaces, GUI's or python libraries like sqlite3 and SQLAlchemy. Pandas need python environment with integrated visualization libraries like Matplotlib or seaborn.  

Here is a small case study of time execution of pandas and SQL. I have used kaggle data set - [Filed of Study vs Occupation](https://www.kaggle.com/datasets/jahnavipaliwal/field-of-study-vs-occupation) for analysis.

### Prerequisites  
MySQL Workbench, Python, Jypter Notebook, Pandas, sqlite3 and mysql.connector  

### Steps to upload CSV into Mysql
Note: After downloading  [Filed of Study vs Occupation](https://www.kaggle.com/datasets/jahnavipaliwal/field-of-study-vs-occupation) dataset edit file columns with '_'(Field of Study ==> Field_of_Study), once all column names are edited then it's ready to upload into MySQL workbench. Refer [Import CSV file into MySQL](https://www.databasestar.com/mysql-workbench-import-csv/).

[Google colab link](https://colab.research.google.com/drive/1F68tc144MLI8-gu3ZlkXW970-QQbNKgw?usp=sharing)

### Steps to connect Mysql server in pandas and create DataFrame  

```python
import pandas as pd
import mysql.connector as mysql
from sqlalchemy import create_engine
import time
import sqlite3 as sq
```
```python
#connection and create cursor for sql execution

mydbcon = mysql.connect(
    host = "localhost",
    user = "root",
    password = "root123"
)
if mydbcon.is_connected:
    print("Conncetion established sucessfully")
else:
    print("Connection faled, please check retry ")

cursor=mydbcon.cursor()
```
### Output:
```
Conncetion established sucessfully
```
### Connect database and create dataframe
```python
#using database 
cursor.execute("USE Career;")

#dataframe creation using pandas
df = pd.read_sql("SELECT * FROM CAREER_DATA_NEW;",mydbcon)
df
```
### Output:
```
38444 rows × 23 columns
```
### Time execution analysis - Pandas vs SQL

#### 1) Print first 1000 records
```python
#SQL
start = time.time()
cursor.execute("SELECT * FROM CAREER_DATA_NEW LIMIT 1000;")
data=cursor.fetchall()
for rows in data:
    print(rows)
end = time.time()
print("======",round((end-start),5)," ms======")
```
### Output:
```
1000 rows
====== 0.02516  ms======
```

```python
#Pandas
start = time.time()
print(df.head(1000))
end = time.time()
print("======",round((end-start),5)," ms======")
```
### Output:
```
[1000 rows x 23 columns]
====== 0.04585  ms======
```

#### 2) Print count values for field_of_study 
```python
#SQL
start = time.time()
cursor.execute("select  field_of_study,count(*) as 'Count' from career_data_new group by field_of_study order by count(*) desc;")
res = cursor.fetchall()
for value in res:
    print(value)
end = time.time()
print("======",round((end-start),5)," ms======")
```
### Output:
```
====== 0.10466  ms======
```

```python
#Pandas
start = time.time()
res  = df.Field_of_Study.value_counts()
print(res)
end = time.time()
print("======",round((end-start),5)," ms======")
```
### Output:
```
====== 0.03302  ms======
```

#### 3) Print distinct field_of_study values
```python
# SQL
start = time.time()
cursor.execute("Select distinct Field_of_Study from career_data_new;")
res = cursor.fetchall()
print(res)
end = time.time()
print("======",round((end-start),5)," ms======")
```
### Output:
```
[('Medicine',), ('Education',), ('Arts',), ('Computer Science',), ('Business',), ('Mechanical Engineering',), ('Biology',), ('Law',), ('Economics',), ('Psychology',)]
====== 0.08325  ms======
```

```python
start = time.time()
res = df.Field_of_Study.unique()
print(res)
end = time.time()
print("======",round((end-start),5)," ms======")
```
### Output:
```
['Medicine' 'Education' 'Arts' 'Computer Science' 'Business' 'Mechanical Engineering' 'Biology' 'Law' 'Economics' 'Psychology']
====== 0.00818  ms======
```

#### 4) Fetch the data of people whose current occupation is Software Developer, job sat < 5,, age < 24 and salary > 100000
```python
#SQL
start = time.time()
cursor.execute("""select * from career_data_new where 
job_satisfaction < 5 
and current_occupation = 'Software Developer' 
and salary>100000 and age < 24 
and Industry_Growth_Rate='High';""")
res = cursor.fetchall()
for rows in res:
    print(rows)
end = time.time()
print("======",round((end-start),5)," ms======")

#Pandas
start = time.time()
kd = df[(df['Current_Occupation'] == 'Software Developer') & (df['Industry_Growth_Rate'] == 'High') & (df['Job_Satisfaction'] < 5) & (df['Age'] < 24) & (df['Salary'] > 10000)]
print(kd)
end = time.time()
print("======",round((end-start),5)," ms======")
```

### Output:
```
54 rows
====== 0.07644  ms======
[54 rows x 23 columns]
====== 0.04197  ms======
```

#### 5) which occupation has high job security?
```python
#SQL
start = time.time()
cursor.execute("""with ex as (
SELECT 
    current_occupation,
    js,
    DENSE_RANK() OVER (ORDER BY js DESC) AS rk
FROM (
    SELECT 
        current_occupation,
        ROUND(AVG(job_security), 2) AS js
    FROM career_data_new
    GROUP BY current_occupation
) AS avg_job_security
) select * from ex where rk=1;
""")
res = cursor.fetchall()
print(res)
end=time.time()
print("======",round((end-start),5)," ms======")
```
### Output:
```
[('Software Developer', Decimal('5.53'), 1), ('Artist', Decimal('5.53'), 1)]
====== 0.26329  ms======
```

```python
#Pandas
start = time.time()
grouped_mean = df.groupby(by=['Current_Occupation'])['Job_Satisfaction'].mean()
grouped_mean = grouped_mean.sort_values(ascending=False)
grouped_mean = round(grouped_mean,2)
print(grouped_mean.head(1))
end=time.time()
print("======",round((end-start),5)," ms======")
```
### Output:
```
Current_Occupation
Psychologist    5.56
Name: Job_Satisfaction, dtype: float64
====== 0.01796  ms======
```

#### 6)  using joins fetch data of people who got high influenced by family and choose to get into software development occupation whose filed of study is medicine
I will be using row_number and inner join query, since there is no primary key I have used row_number in this query for joins.
```python
start = time.time()
cursor.execute("""WITH ex1 AS (
    SELECT  
        gender, 
        Family_Influence,
        field_of_study,
        current_occupation,
        row_number() OVER() AS serial_number
    FROM career_data_new
) 
SELECT 
    COUNT(*) AS Count_of_people, 
    t1.gender, 
    t1.field_of_study, 
    t1.current_occupation
FROM ex1 t1
INNER JOIN ex1 t2 
    ON t2.serial_number = t1.serial_number where t1.field_of_study = 'Medicine' and t1.current_occupation='Software Developer' and t1.Family_Influence='High'
GROUP BY t1.gender, t1.field_of_study, t1.current_occupation;""")
res = cursor.fetchall()
print(res)

end=time.time()
print("======",round((end-start),5)," ms======")
```
### Output:
```
[(49, 'Female', 'Medicine', 'Software Developer'), (46, 'Male', 'Medicine', 'Software Developer')]
====== 0.23358  ms======
```

```python
start = time.time()
filtered_df = df[
(df['Field_of_Study'] == 'Medicine') & 
(df['Current_Occupation'] == 'Software Developer') & 
(df['Family_Influence'] == 'High')]
result = filtered_df.groupby(['Gender', 'Field_of_Study', 'Current_Occupation']).size().reset_index(name='Count_of_people')
print(result)
end=time.time()
print("======",round((end-start),5)," ms======")
```
### Output:
```
 Gender Field_of_Study  Current_Occupation  Count_of_people
0  Female       Medicine  Software Developer               49
1    Male       Medicine  Software Developer               46
====== 0.02325  ms======
```

## What I have noticed?
Both Pandas and SQL are powerful tools for data manipulation and analysis, but they are suited for different use cases and workflows.

* __Pandas__ is ideal for small to medium-sized datasets that can fit into memory and require complex data manipulation within a Python environment.
* __SQL__ is better for large-scale datasets that need to be stored in relational databases, where data querying and management efficiency are key.

#### When to use?
* Use Pandas if you're working within a Python environment, need to perform detailed transformations or analysis, and your data can be handled in memory.
* Use SQL when working with large, persistent datasets stored in databases, and you need to efficiently perform queries, joins, and aggregations.

#### Hybrid Approach:
In many real-world scenarios, a hybrid approach is often the best solution. For example, you might use SQL to extract large datasets from a relational database and then load them into Pandas for further manipulation and analysis. This combines the best of both worlds—SQL for data retrieval and Pandas for complex data processing.
