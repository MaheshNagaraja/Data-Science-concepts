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

### Steps to connect Mysql server in pandas and create DataFrame  

```python
import pandas as pd
import mysql.connector as mysql
from sqlalchemy import create_engine
import time
import sqlite3 as sq

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

