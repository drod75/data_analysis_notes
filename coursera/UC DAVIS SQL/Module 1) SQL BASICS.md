#sql
## What is it?
SQL is a <mark style="background: #BBFABBA6;">Structured Query Language</mark> for relational database management and data manipulation, SQL can be used to query, insert, update, and modify data, it is a non procedural database and is made of key words. SQL is all about data, it can be used to <mark style="background: #BBFABBA6;">read, retrieve, write, and update</mark> data. SQL has many different dialects across Database Management Systems.

## Basic SQL
### Selecting
The <mark style="background: #BBFABBA6;">SELECT</mark> can be used to select what columns you want from your table, and <mark style="background: #BBFABBA6;">FROM</mark> can be used to say from what table you want them from. You can select more columns by just <mark style="background: #BBFABBA6;">separating their names by a comma</mark>, you can do this with tables also in from. The wildcard \* can be used to select all columns from a table. Select and From are necessary, when you are selecting you can limit the amount of entries by specifying <mark style="background: #BBFABBA6;">LIMIT X</mark> at the very end.
```sql
SELECT * FROM users
LIMIT 5;
```
This code <mark style="background: #BBFABBA6;">selects all columns from users and limits it to 5.</mark> 

### Creating Tables
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

### Table Insertion
One way to <mark style="background: #BBFABBA6;">insert data into an sql table is by the following code</mark>:
```sql
INSERT INTO shoes
(COLUMNS)
VALUES (...,...);
```
This is the ideal way to insert values, by specifying the columns you can control what values are inserted into where and prevent the wrong data from being inserted.

### Temporary Tables
Temporary table is good to simplify queries and is faster than creating actual tables, the way to do it is:
```sql
CREATE TEMPORARY TABLE Sandles as (
	SELECT ...
)
```
Temporary Table key words and creation can differ based on what dialect of sql you are using.

## Filtering
Its useful to reduce the number of records before hand to reduce the strain on the system and later processes. To specify what to select based on parameters use <mark style="background: #BBFABBA6;">WHERE</mark>, using where you could filter for rows where a column has a specific value, some examples are
```sql
-- between two numbers
WHERE x BETWEEN 15 and 20

-- WHERE X IS A NULL VALUE
WHERE X IS NULL

-- WHERE X IS IN A CERTAIN GROUP OF VALUES
WHERE X IN (...)

-- WHERE X IS EITHER OF TWO VALUES
WHERE X = 'A' OR X = 'B'

-- WHERE X IS 5 AND Y IS ABC
WHERE X = 5 AND Y = 'ABC'

-- WHERE X IS NOT 5
WHERE NOT X = 5
```

### Wildcards
Wildcards are good for string or text data, its good to search for text that matches a certain pattern.
- % is used to indicate when a line begins or ends, so can be useful to get rows where a column ends with x or begins with x.
- % can be also be used like so: s%e so any word that begins with s and ends with e, you can also use it as so: %pizza%
```sql
WHERE X LIKE '%PIZZA'
```

### Ordering
<mark style="background: #BBFABBA6;">ORDER BY</mark> can be useful to sort data by a particular column, its very useful to view data in a specific way, DESC and ASC are used to sort by descending and ascending respectively, order by has to be last and can use multiple columns

## Column manipulation
### Math
SQL has math operations also, PEMDAS is the default and you can use AS to give something a name, like so:
```sql
-- SELECTS THE MULTIPLICATION OF X AND Y AND SHOWS IT AS COLUMN Z
SELECT X * Y AS Z FROM SHOES
```

### Aggregation
SQL has aggregate functions, the ones it has are as follows:
- AVG()
- COUNT()
- MIN()
- MAX()
- SUM()

One useful way to use count is:
```sql
-- counts distinct aka unique values of x
COUNT(DISTINCT X)
```

### Group BY
Group by can be useful if you wanted to see the total spending and average spending of every customer, you can do this:
```sql
SELECT CUSTOMER, AVG(SPEND), SUM(SPEND) FROM CUSTOMERS
GROUP BY CUSTOMER
```
Group BY groups all the rows that have a matching customer together, this is useful to get info for every value in a column. One thing to note is since that where filters based on rows, it will not work, so what yuo need to do is use HAVING.
```sql
-- use above code
HAVING SPEND > 0
-- FILTERS BEFORE HAND BASED ON IF SPEND IS GREATER THAN 0
```


### ORDER OF OPERATIONS
The order of operations in SQL(sqlite) is as follows:

| Clause   | Description                           | Required                                |
| :------- | :------------------------------------ | :-------------------------------------- |
| SELECT   | Columns or expressions to be returned | Yes                                     |
| FROM     | Table from which to retrieve data     | Only if selecting data from a table     |
| WHERE    | Row-level filtering                   | No                                      |
| GROUP BY | Group specification                   | Only if calculating aggregates by group |
| HAVING   | Group-level filter                    | No                                      |
| ORDER BY | Output sort order                     | No                                      |


## Subqueries
Subqueries and Joins are good to combine and merge data from two different places, both have advantages and disadvantages for what they allow you to do. An example of a query is:
```sql
SELECT
    CustomerID,
    CompanyName,
    Region
FROM
    Customers
WHERE
    customerID IN (
        SELECT
            customerID
        FROM
            Orders
        WHERE
            Freight > 100
    );
```
This selects fields from one table based on when one field is in a particular filter.

There is no limit to the amount of subs you can have so this can slow down performance, you can also only retrieve a single column in a sub, this can cause you to have to have multiple selected subqueries which can become a pain due to the sheer mess of code you will have.

## Joins
### Cartesian (Cross) Join
These allow us to match every row from table to a row from another table, so this is basically <mark style="background: #BBFABBA6;">Many to Many</mark>, an example of cross joins is:
```sql
SELECT X, Y FROM SHOES 
CROSS JOIN WAREHOUSE
```

### Inner Join
Inner joins are used to select records that have matching values in both tables, so its a <mark style="background: #BBFABBA6;">one to one join</mark>, the syntax is:
```sql
SELECT suppliers.CompanyName
       ,ProductName
       ,UnitPrice
FROM Suppliers INNER JOIN Products
ON Suppliers.supplierid = Products.supplierid;
```
This matches the rows of the two tables based on a column from both tables, so the rows will only match if they have the same values for the columns they are joined on. Inner joins can be done on multiple tables, the thing is this is taxing so be aware of the cost you may get when you do this, an example is:
```sql
SELECT o.OrderID, c.CompanyName, e.LastName
FROM ((Orders o INNER JOIN Customers c ON o.CustomerID = c.CustomerID)
INNER JOIN Employees e ON o.EmployeeID = e.EmployeeID);
```

### Self Joins
If there are two columns you want to join in the same table <mark style="background: #BBFABBA6;">self joins</mark> are an option, it works the same and can be useful to help you understand a table more.

### Left, Right, and Outer
Left joins and right joins are a<mark style="background: #BBFABBA6;"> many to one and one to many join</mark> respectively, while outer is everything. Some example left join code is:
```sql
SELECT C.CustomerName, O.OrderID
FROM Customers C
LEFT JOIN Orders O ON C.CustomerID = O.CustomerID
ORDER BY C.CustomerName;
```
This gets all the rows from customers and if possible provides a matching row from orders based on the join clause.

Some right join code is:
```sql
SELECT Orders.OrderID,
       Employees.LastName,
       Employees.FirstName
FROM Orders
RIGHT JOIN Employees ON Orders.EmployeeID = Employees.EmployeeID
ORDER BY Orders.OrderID;
```
This is the same thing as left join but has a different order, it doesnt really matter and isn’t a big deal if one dbms supports one and not the other.

Some outer join code is:
```sql
SELECT Customers.CustomerName,
       Orders.OrderID
FROM Customers
FULL OUTER JOIN Orders ON Customers.CustomerID = Orders.CustomerID
ORDER BY Customers.CustomerName;
```
Remember that outer joins match either or if there is any or not.

### Unions
They may or may not be used much but when you do use them they are extremely useful. In a union each select statement gets stacked on top of each other, so they must have the same amount of columns to do this and the columns need to be in the same order. Some examples:
```sql
-- one
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2;

--two
SELECT City, Country FROM Customers
WHERE Country='Germany'
UNION
SELECT City, Country FROM Suppliers
WHERE Country='Germany'
ORDER BY City;
```
