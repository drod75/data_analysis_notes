#sql
## What is it?
SQL is a <mark style="background: #BBFABBA6;">Structured Query Language</mark> for relational database management and data manipulation, SQL can be used to query, insert, update, and modify data, it is a non procedural database and is made of key words. SQL is all about data, it can be used to <mark style="background: #BBFABBA6;">read, retrieve, write, and update</mark> data. SQL has many different dialects across Database Management Systems.

## Basic SQL
The <mark style="background: #BBFABBA6;">SELECT</mark> can be used to select what columns you want from your table, and <mark style="background: #BBFABBA6;">FROM</mark> can be used to say from what table you want them from. You can select more columns by just <mark style="background: #BBFABBA6;">separating their names by a comma</mark>, you can do this with tables also in from. The wildcard \* can be used to select all columns from a table. Select and From are necessary, when you are selecting you can limit the amount of entries by specifying <mark style="background: #BBFABBA6;">LIMIT X</mark> at the very end.
```sql
SELECT * FROM users
LIMIT 5;
```
This code <mark style="background: #BBFABBA6;">selects all columns from users and limits it to 5.</mark> 

Tables are useful to make models and predictions, its useful to store data in a particular format for analysis. A Simple table creating structure is as so:
```sql
CREATE TABLE shoes
(
Id char(10) PRIMARY KEY,
Brand char(10) NOT NULL,
Price decimal(10, 2) NOT NULL
Desc Varchar(750) NULL)
);
```
This code creates a table called shoes which has various data types <mark style="background: #BBFABBA6;">primary key</mark> is something that specifies the main important value of a table, the variable that matters most and is used often in other tables. 

One way to <mark style="background: #BBFABBA6;">insert data into an sql table is by the following code</mark>:
```sql
INSERT INTO shoes
(COLUMNS)
VALUES (...,...);
```
This is the ideal way to insert values, by specifying the columns you can control what values are inserted into where and prevent the wrong data from being inserted.

Temporary table is good to simplify queries and is faster than creating actual tables, the way to do it is:
```sql
CREATE TEMPORARY TABLE Sandles as (
	SELECT ...
)
```
Temporary Table key words and creation can differ based on what dialect of sql you are using.