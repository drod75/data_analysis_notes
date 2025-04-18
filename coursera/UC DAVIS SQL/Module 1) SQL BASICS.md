#SQL
## What is it?
SQL is a <mark style="background: #BBFABBA6;">Structured Query Language</mark> for relational database management and data manipulation, SQL can be used to query, insert, update, and modify data, it is a non procedural database and is made of key words. SQL is all about data, it can be used to <mark style="background: #BBFABBA6;">read, retrieve, write, and update</mark> data. SQL has many different dialects across Database Management Systems.

## Basic SQL
### Selecting
The <mark style="background: #BBFABBA6;">SELECT</mark> can be used to select what columns you want from your table, and <mark style="background: #BBFABBA6;">FROM</mark> can be used to say from what table you want them from. You can select more columns by just <mark style="background: #BBFABBA6;">separating their names by a comma</mark>, you can do this with tables also in from. The wildcard \* can be used to select all columns from a table. Select and From are necessary, when you are selecting you can limit the amount of entries by specifying <mark style="background: #BBFABBA6;">LIMIT X</mark> at the very end.
```SQL
SELECT * FROM users
LIMIT 5;
```
This code <mark style="background: #BBFABBA6;">selects all columns from users and limits it to 5.</mark> 

### Creating Tables
Tables are useful to make models and predictions, its useful to store data in a particular format for analysis. A Simple table creating structure is as so:
```SQL
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
One way to <mark style="background: #BBFABBA6;">insert data into an SQL table is by the following code</mark>:
```SQL
INSERT INTO shoes
(COLUMNS)
VALUES (...,...);
```
This is the ideal way to insert values, by specifying the columns you can control what values are inserted into where and prevent the wrong data from being inserted.

### Temporary Tables
Temporary table is good to simplify queries and is faster than creating actual tables, the way to do it is:
```SQL
CREATE TEMPORARY TABLE Sandles as (
	SELECT ...
)
```
Temporary Table key words and creation can differ based on what dialect of SQL you are using.

## Filtering
Its useful to reduce the number of records before hand to reduce the strain on the system and later processes. To specify what to select based on parameters use <mark style="background: #BBFABBA6;">WHERE</mark>, using where you could filter for rows where a column has a specific value, some examples are
```SQL
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
```SQL
WHERE X LIKE '%PIZZA'
```

### Ordering
<mark style="background: #BBFABBA6;">ORDER BY</mark> can be useful to sort data by a particular column, its very useful to view data in a specific way, DESC and ASC are used to sort by descending and ascending respectively, order by has to be last and can use multiple columns

## Column manipulation
### Math
SQL has math operations also, PEMDAS is the default and you can use AS to give something a name, like so:
```SQL
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
```SQL
-- counts distinct aka unique values of x
COUNT(DISTINCT X)
```

### Group BY
Group by can be useful if you wanted to see the total spending and average spending of every customer, you can do this:
```SQL
SELECT CUSTOMER, AVG(SPEND), SUM(SPEND) FROM CUSTOMERS
GROUP BY CUSTOMER
```
Group BY groups all the rows that have a matching customer together, this is useful to get info for every value in a column. One thing to note is since that where filters based on rows, it will not work, so what yuo need to do is use HAVING.
```SQL
-- use above code
HAVING SPEND > 0
-- FILTERS BEFORE HAND BASED ON IF SPEND IS GREATER THAN 0
```


### ORDER OF OPERATIONS
The order of operations in SQL(SQLite) is as follows:

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
```SQL
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
```SQL
SELECT X, Y FROM SHOES 
CROSS JOIN WAREHOUSE
```

### Inner Join
Inner joins are used to select records that have matching values in both tables, so its a <mark style="background: #BBFABBA6;">one to one join</mark>, the syntax is:
```SQL
SELECT suppliers.CompanyName
       ,ProductName
       ,UnitPrice
FROM Suppliers INNER JOIN Products
ON Suppliers.supplierid = Products.supplierid;
```
This matches the rows of the two tables based on a column from both tables, so the rows will only match if they have the same values for the columns they are joined on. Inner joins can be done on multiple tables, the thing is this is taxing so be aware of the cost you may get when you do this, an example is:
```SQL
SELECT o.OrderID, c.CompanyName, e.LastName
FROM ((Orders o INNER JOIN Customers c ON o.CustomerID = c.CustomerID)
INNER JOIN Employees e ON o.EmployeeID = e.EmployeeID);
```

### Self Joins
If there are two columns you want to join in the same table <mark style="background: #BBFABBA6;">self joins</mark> are an option, it works the same and can be useful to help you understand a table more.

### Left, Right, and Outer
Left joins and right joins are a<mark style="background: #BBFABBA6;"> many to one and one to many join</mark> respectively, while outer is everything. Some example left join code is:
```SQL
SELECT C.CustomerName, O.OrderID
FROM Customers C
LEFT JOIN Orders O ON C.CustomerID = O.CustomerID
ORDER BY C.CustomerName;
```
This gets all the rows from customers and if possible provides a matching row from orders based on the join clause.

Some right join code is:
```SQL
SELECT Orders.OrderID,
       Employees.LastName,
       Employees.FirstName
FROM Orders
RIGHT JOIN Employees ON Orders.EmployeeID = Employees.EmployeeID
ORDER BY Orders.OrderID;
```
This is the same thing as left join but has a different order, it doesnt really matter and isn’t a big deal if one dbms supports one and not the other.

Some outer join code is:
```SQL
SELECT Customers.CustomerName,
       Orders.OrderID
FROM Customers
FULL OUTER JOIN Orders ON Customers.CustomerID = Orders.CustomerID
ORDER BY Customers.CustomerName;
```
Remember that outer joins match either or if there is any or not.

### Unions
They may or may not be used much but when you do use them they are extremely useful. In a union each select statement gets stacked on top of each other, so they must have the same amount of columns to do this and the columns need to be in the same order. Some examples:
```SQL
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

### Joins List
#### Left Join
````SQL
SELECT <select_list>
FROM TableA A
LEFT JOIN TableB B
ON A.Key = B.Key
````

This join returns all rows from TableA, and the matching rows from TableB. If there is no match in TableB, NULL values are returned for the columns of TableB. The Venn diagram shows all of A and the intersection of A and B.

---
#### INNER JOIN
```SQL
SELECT <select_list>
FROM TableA A
INNER JOIN TableB B
ON A.Key = B.Key
```

This join returns only the rows where there is a match in both TableA and TableB based on the specified condition. The Venn diagram shows the intersection of A and B.

---
#### RIGHT JOIN

```SQL
SELECT <select_list>
FROM TableA A
RIGHT JOIN TableB B
ON A.Key = B.Key
```

This join returns all rows from TableB, and the matching rows from TableA. If there is no match in TableA, NULL values are returned for the columns of TableA. The Venn diagram shows all of B and the intersection of A and B.

---
#### FULL OUTER JOIN

```SQL
SELECT <select_list>
FROM TableA A
FULL OUTER JOIN TableB B
ON A.Key = B.Key
```

This join returns all rows from both TableA and TableB. If there is no match in either table, NULL values are returned for the columns of the non-matching table. The Venn diagram shows the union of A and B (all parts of both circles).

--- 
#### FULL OUTER JOIN with no matching rows

```SQL
SELECT <select_list>
FROM TableA A
FULL OUTER JOIN TableB B
ON A.Key = B.Key
WHERE A.Key IS NULL
```

This join returns only the rows from TableB where there is no matching row in TableA. This effectively gives you the rows that are _only_ in TableB. The Venn diagram shows the part of B that does not overlap with A.

---
#### Only LEFT no matching right rows

```SQL
SELECT <select_list>
FROM TableA A
LEFT JOIN TableB B
ON A.Key = B.Key
WHERE B.Key IS NULL
```

This join returns only the rows from TableA where there is no matching row in TableB. This effectively gives you the rows that are _only_ in TableA. The Venn diagram shows the part of A that does not overlap with B.

---
#### Only RIGHT where no matching left rows

```SQL
SELECT <select_list>
FROM TableA A
RIGHT JOIN TableB B
ON A.Key = B.Key
WHERE A.Key IS NULL
```

This join returns only the rows from TableB where there is no matching row in TableA. This is equivalent to the "FULL OUTER JOIN with WHERE A.Key IS NULL" and effectively gives you the rows that are _only_ in TableB. The Venn diagram shows the part of B that does not overlap with A.

## Data Manipulation
### Strings
 Strings have a couple of different functions in SQL, these allow for formatting, examples are:
 ```SQL
-- Concatenate the company name and contact name with parenthesis as well
SELECT
    CompanyName,
    ContactName,
    CompanyName || ' (' || ContactName || ')'
FROM
    customers;

-- Trim leading and ending spaces
SELECT TRIM("   You the best.   ") AS TrimmedString;

-- Substring allows you to pull a certain part of a string
-- SUBSTR(WHAT, WHERE TO START(1-N), AMOUNT OF CHARACTERS TO PULL)
SELECT first_name, SUBSTR(first_name, 2, 3)
FROM employees
WHERE department_id = 60;

-- TO UPPER AND LOWER CASE DATA RESPECTIVELY
SELECT UPPER(column_name) FROM table_name;
SELECT LOWER(column_name) FROM table_name;

```

### Date/Time with Strings
Working with dates in different DBMS can be difficult as different databases have different ways they handle it, as well as different variety of data types.

The following code wont work to query a datetime column:
```SQL
WHERE PurchaseDate = '2016-12-12
```
To make it work you need to use either of these functions to get what you want:
```SQL
DATE(timestring, modifier, modifier, ...)
TIME(timestring, modifier, modifier, ...)
DATETIME(timestring, modifier, modifier, ...)
JULIANDAY(timestring, modifier, modifier, ...)
STRFTIME(format, timestring, modifier, modifier, ...)
```
The format for the full time strings is as so, <mark style="background: #BBFABBA6;">YYYY-MM-DD HH:MM:SS.SSS</mark>.

STRFTIME can be used to get a string representation of a time column based on what you provide, an example is:
```SQL
SELECT
    Birthdate,
    STRFTIME('%Y', Birthdate) AS Year,
    STRFTIME('%m', Birthdate) AS Month,
    STRFTIME('%d', Birthdate) AS Day
FROM
    employees;
```

Some common datetime examples are:
```SQL
-- gets nows date
SELECT DATE('now')

-- this example gets nows date and represents it as year to month to day
SELECT STRFTIME('%Y %m %d', 'now')

-- same but with hour minute and seconds
SELECT STRFTIME('%H %M %S %s', 'now');

-- calculates age
SELECT
    Birthdate,
    STRFTIME('%Y', Birthdate) AS Year,
    STRFTIME('%m', Birthdate) AS Month,
    STRFTIME('%d', Birthdate) AS Day,
    (STRFTIME('now') - Birthdate) AS Age
FROM
    employees;
```

### Case Statements
The <mark style="background: #BBFABBA6;">case statement mimics an if and else statement found in other languages</mark>, it can be used in <mark style="background: #BBFABBA6;">SELECT, INSERT, UPDATE, DELETE</mark> statements, a simple example is:
```SQL
SELECT
    employeeid,
    firstname,
    lastname,
    city,
    CASE City
        WHEN 'Calgary' THEN 'Calgary'
        ELSE 'Other'
    END AS calgary
FROM
    Employees
ORDER BY
    LastName, FirstName;
```

| employeeid | firstname | lastname  | city      | calgary |
|------------|-----------|-----------|-----------|---------|
| 1          | Andrew    | Adams     | Edmonton  | Other   |
| 2          | Laura     | Callahan  | Lethbridge| Other   |
| 3          | Nancy     | Edwards   | Calgary   | Calgary |
| 5          | Robert    | King      | Lethbridge| Other   |
| 6          | Michael   | Mitchell  | Calgary   | Calgary |
| 7          | Margaret  | Park      | Calgary   | Calgary |
| 8          | Jane      | Peacock   | Calgary   | Calgary |
| 4          | Steve     | Johnson   | Calgary   | Calgary |

As per the code and table, when the city is Calgary the column value is Calgary else it is other, this is saved in a column titled Calgary, you can do this for one hot encoding.

Another example with multiple ‘ifs’ and conditions is:
```SQL
SELECT
    trackid,
    name,
    bytes,
    CASE
        WHEN bytes < 300000 THEN 'small'
        WHEN bytes >= 300001 AND bytes <= 500000 THEN 'medium'
        WHEN bytes >= 500001 THEN 'large'
        ELSE 'Other'
    END AS bytescategory
FROM
    tracks;
```

| trackid | name                                         | bytes   | bytescategory |
|---------|----------------------------------------------|---------|---------------|
| 2461    | E Uma Partida De Futebol                    | 38747   | small         |
| 168     | New Sports                                   | 161266  | small         |
| 170     | A Statistic                                  | 211997  | small         |
| 178     | Oprah                                        | 224313  | small         |
| 3304    | Commercial 1                                 | 319080  | medium        |
| 172     | The Real Problem                             | 387360  | medium        |
| 3310    | Commercial 2                                 | 850090  | large         |
| 2241    | Bossa                                        | 967098  | large         |
| 1006    | Casinha Feliz                                | 1039615 | large         |
| 975     | Deixa Entrar                                 | 1095412 | large         |
| 246     | Mateus Enter                                 | 1103013 | large         |
| 2797    | Homem Primata (Vinheta)                      | 1124809 | large         |
| 1287    | Intro - Churchill's Speech                   | 1154488 | large         |
| 3501    | L'orfeo, Act 3: Sinfonia (Orchestra)         | 1189062 | large         |
| 3448    | Lamentations of Jeremiah, First Set: Incipit Lamentatio | 1200000 | large         |
| 2793    | Cabeça Dinossauro                            | 1220930 | large         |
| 2993    | Freedom For My People                        | 1249764 | large         |
| 1968    | Demorou Em Mim                               | 1287403 | large         |
As per the code and table this SQL query creates a categorical column based on a numerical one and uses case statements to assign the categorical value.


### Views
Sometimes views can help you simplify queries to make things easier, instead of creating a whole new table you can store your query to use at any time, an example is:
```SQL
CREATE VIEW my_view AS
SELECT
    r.regiondescription,
    t.territorydescription,
    e.LastName,
    e.FirstName,
    e.HireDate,
    e.ReportsTo
FROM
    Region r
INNER JOIN
    Territories t ON r.regionid = t.regionid
INNER JOIN
    Employeeterritories et ON t.TerritoryID = et.TerritoryID
INNER JOIN
    Employees e ON et.employeeid = e.EmployeeID;
```

| regiondescription | territorydescription | LastName  | FirstName | HireDate               | ReportsTo |
| ----------------- | -------------------- | --------- | --------- | ---------------------- | --------- |
| Eastern           | Wilton               | Davolio   | Nancy     | 5/1/1992 12:00:00 AM   | 2         |
| Eastern           | Stamford             | Fuller    | Andrew    | 8/14/1992 12:00:00 AM  | 2         |
| Eastern           | Westboro             | Fuller    | Andrew    | 8/14/1992 12:00:00 AM  |           |
| Eastern           | Georgetown           | Fuller    | Andrew    | 8/14/1992 12:00:00 AM  |           |
| Eastern           | Boston               | Fuller    | Andrew    | 8/14/1992 12:00:00 AM  |           |
| Eastern           | Easton               | Fuller    | Andrew    | 8/14/1992 12:00:00 AM  |           |
| Eastern           | Cambridge            | Fuller    | Andrew    | 8/14/1992 12:00:00 AM  |           |
| Eastern           | Bramtree             | Fuller    | Andrew    | 8/14/1992 12:00:00 AM  |           |
| Eastern           | Louisville           | Fuller    | Andrew    | 8/14/1992 12:00:00 AM  |           |
| Southern          | Atlanta              | Leverling | Janet     | 4/1/1992 12:00:00 AM   | 2         |
| Southern          | Charlotte            | Leverling | Janet     | 4/1/1992 12:00:00 AM   | 2         |
| Southern          | Orlando              | Leverling | Janet     | 4/1/1992 12:00:00 AM   | 2         |
| Southern          | Tampa                | Leverling | Janet     | 4/1/1992 12:00:00 AM   | 2         |
| Eastern           | Buckville            | Peacock   | Margaret  | 5/3/1993 12:00:00 AM   | 2         |
| Eastern           | Greensboro           | Peacock   | Margaret  | 5/3/1993 12:00:00 AM   | 2         |
| Eastern           | Cary                 | Peacock   | Margaret  | 5/3/1993 12:00:00 AM   | 2         |
| Eastern           | Providence           | Buchanan  | Steven    | 10/17/1993 12:00:00 AM | 2         |
| Eastern           | Manhasset            | Buchanan  | Steven    | 10/17/1993 12:00:00 AM | 2         |
| Eastern           | Edison               | Buchanan  | Steven    | 10/17/1993 12:00:00 AM | 2         |
| Eastern           | New York             | Buchanan  | Steven    | 10/17/1993 12:00:00 AM | 2         |
| Eastern           | New York             | Buchanan  | Steven    | 12/15/1993 12:00:00 AM | 2         |
| Eastern           | Melville             | Buchanan  | Steven    | 10/17/1993 12:00:00 AM | 2         |
| Eastern           | Medford              | Buchanan  | Steven    | 10/17/1993 12:00:00 AM | 2         |
| Western           | Phoenix              | Suyama    | Michael   | 10/17/1993 12:00:00 AM | 5         |
| Western           | Scottsdale           | Suyama    | Michael   | 10/17/1993 12:00:00 AM | 5         |
This table and code shows a normal complex join, and saves it as a view, the main part of the creation of a view is the first two lines. Some important code to take notice is this:
```SQL
-- selects from view
SELECT *
FROM my_view;

-- drops the view
DROP VIEW my_view;
```
By using a view you can perform another query on this ‘table’ to do more complex work that you would be a pain to do without.
