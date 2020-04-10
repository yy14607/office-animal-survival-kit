# Introduction to SQL

SQL: Structured Query Language, a language for interacting with data stored in something called a relational database.

This note summarizes the most basic syntax in SQL shared by many types of databases, such as PostgreSQL, MySQL, SQL Server, and Oracle. 

## SELECT from a single table


Select all rows and columns from table t:

```
SELECT * FROM t;
```
Select data in columns c1, c2 from table t under a condition:

```
SELECT c1, c2 FROM t
WHERE condition;
```

Select distinct rows from table t:

```
SELECT DISTINCT c1 FROM t;
```

Sort the result in ascending (default) or descending order:

```
SELECT c1, c2 FROM t
ORDER BY c1 ASC [DESC];
```

Skip m rows ("OFFSET") and return the next n rows:

```
SELECT c1, c2 FROM t
ORDER BY c1
LIMIT n OFFSET m
```

Group table t by column c1 and return some aggregate functions, and filter groups with "HAVING"onditions:
```
SELECT c1, aggregate(c2)
FROM t
GROUP BY c1
HAVING condition;
```

Aggregate functions include:
```
AVG;
COUNT;
SUM;
MAX;
MIN;...
```



## Mutating tables

Create a new table:
```
CREATE TABLE t(
    id INT PRIMARY KEY,
    name VARCHAR NOT NULL,
    price INT DEFAULT 0
);
```

Constraints: 
+ Set column c2 as a foreign key:
```
CREATE TABLE t1(
    c1 INT PRIMARY KEY,
    c2 INT,
    FOREIGN KEY (c2) REFERENCES t2(c2)
);
```
+ Make columns c1, c2 unique:
```
CREATE TABLE t1(
    c1 INT,
    c2 INT,
    UNIQUE(c1, c2)
);
```
+ Check if some constraints hold:
```
CREATE TABLE t1(
    c1 INT,
    c2 INT,
    CHECK(c1>0 AND c1 >= c2)
);
```




Delete table t from the database:
```
DROP TABLE t;
```

Add a new column to table t:
```
ALTER TABLE t ADD column data_type column_constraint;
```

Drop column c from table t:
```
ALTER TABLE t DROP COLUMN C
```

Add/drop a constraint:
```
ALTER TABLE t ADD/DROP constraint;
```

Rename table t1 to t2:
```
ALTER TABLE t RENAME TO t2;
```

Remove all data in a table:
```
TRUNCATE TABLE t;
```




## Modifying rows or columns

Insert a row into table t:
```
INSERT INTO t(column_list)
VALUES(value_list);
```

Insert multiple rows into table t:
```
INSERT INTO t(column_list)
VALUES(value_list), ....;
```

Insert rows from t2 into t1:
```
INSERT INTO t1(column_list)
SELECT column_list
FROM t2;
```

Update new values in the columns c1, c2 for rows that match the condition:
```
UPDATE t 
SET c1 = new_value, 
    c2 = new_value
WHERE condition;
```

Delete a subset of rows from table t:
```
DELETE FROM t
WHERE condition;
```






## JOIN multiple tables


```
SELECT c1, c2
FROM t1 
INNER JOIN/LEFT JOIN/RIGHT JOIN/FULL OUTER JOIN/CROSS JOIN t2 ON conditions; 

```

Another way of cross join:
```
SELECT c1, c2 
FROM t1, t2
```

Join table t to itself:
```
SELECT c1, c2
FROM t a
INNER JOIN t b ON conditions;
```





## Operators

Combine rows from two queries:

```
SELECT c1, c2 FROM t1
UNION [ALL]
SELECT c1, c2 FROM t2
```

Select the intersection of two sets:
```
SELECT c1, c2 FROM t1
INTERSECT
SELECT c1, c2 FROM t2
```

Subtract set:
```
SELECT c1, c2 FROM t1
MINUS
SELECT c1, c2 FROM t2
```

Pattern matching:
```
SELECT c1, c2 FROM t
WHERE c1 [NOT] LIKE pattern;
```

Query rows in a list:
```
SELECT c1, c2 FROM t
WHERE c1 [NOT] IN value_list;
```

Query rows between a range:
```
SELECT c1, c2 FROM t
WHERE c1 BETWEEN low AND high;
```

Query rows which is (not) null:
```
SELECT c1, c2 FROM t
WHERE c1 IS [NOT] NULL;
```

Case statement:
```
CASE WHEN ... THEN, WHEN ... THEN,
ELSE ... END AS ...
```

Case statements with aggregating functions:
```
SUM/COUNT/AVG(CASE WHEN ... THEN, 
WHEN ... THEN,
ELSE ... END) AS ...
```



## Indexes

Create a (unique) index on c1 and c2 of table t:
```
CREATE (UNIQUE) INDEX idx_name
ON t(c1, c2);
```

Drop an index:
```
DROP INDEX idx_name;
```



## Views 

Create a view (with check options):
```
CREATE VIEW v(c1, c2)
AS 
SELECT c1, c2
FROM t;
WITH [CASCADED|LOCAL] CHECK OPTION;
```

Create a recursive view:
```
CREATE RECURSIVE VIEW v
AS
select-statement -- anchor part
UNION [ALL]
select-statement -- recursive part
```

Create a temporary view:
```
CREATE TEMPORARY VIEW v
AS
SELECT c1, c2
FROM t;
```

Delete a view:
```
DROP VIEW view_name;
```




## Triggers

Create or modify a trigger:
```
CREATE OR MODIFY TRIGGER trigger_name
WHEN EVENT
ON table_name TRIGGER_TYPE
EXECUTE stored_procedure;
```

WHEN: BEFORE/AFTER

EVENT: INSERT/UPDATE/DELETE

TRIGGER_TYPE: FOR EACH ROW/FOR EACH STATEMENT

Create a trigger invoked before a new row is inserted into table t:
```
CREATE TRIGGER before_insert_table
BEFORE INSERT
ON t FOR EACH ROW
EXECUTE stored_procedure;

```

Delete a trigger:
```
DROP TRIGGER trigger_name;
```








