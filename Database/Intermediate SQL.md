# Intermediate SQL

## Subqueries

__Subqueries in WHERE__

```
SELECT c1
FROM t1
WHERE c1> (SELECT c2 FROM t2 WHERE condition);
```

__Subqueries in FROM__

+ restructure and transform your data from long to wide
+ prefiltering data
+ calculating aggregates of aggregates
```
SELECT c1
FROM (SELECT c2 from t2) AS a;
```

__Subqueries in SELECT__

```
SELECT
    -- Select the league name and average goals scored
    l.name AS league,
    ROUND(AVG(m.home_goal + m.away_goal),2) AS avg_goals,
    -- Subtract the overall average from the league average
    ROUND(AVG(m.home_goal + m.away_goal) -
        (SELECT AVG(home_goal + away_goal)
         FROM match
         WHERE season = '2013/2014'),2) AS diff
FROM league AS l
LEFT JOIN match AS m
ON l.country_id = m.country_id
-- Only include 2013/2014 results
WHERE m.season = '2013/2014'
GROUP BY l.name;
```

## Common Table Expressions (CTE)

Correlated queries/Nested queries performance is bad. Solution: CTEs.

```
WITH name AS (
SELECT c1
FROM t1
), ...;
```

## Window functions

```
SELECT
    id,
    RANK() OVER(ORDER BY c1 DESC) AS rank
FROM t;
```

```
SELECT AVG(c1) OVER(PARTITION BY c2, ...)
```

Sliding window:
```
ROWS BETWEEN <start> AND <finish>
PRECEDING
FOLLOWING
UNBOUNDED PRECEDING
UNBOUNDED FOLLOWING
CURRENT ROW
```

Sliding windows allow you to create running calculations between any two points in a window using functions such as PRECEDING, FOLLOWING, and CURRENT ROW. You can calculate running counts, sums, averages, and other aggregate functions between any two points you specify in the data set.

Example:
```
SELECT
    date,
    home_goal,
    away_goal,
    -- Create a running total and running average of home goals
    SUM(home_goal) OVER(ORDER BY date
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total,
    AVG(home_goal) OVER(ORDER BY date
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_avg
FROM match
WHERE
    hometeam_id = 9908
    AND season = '2011/2012';
```



## Iteration, Recursion and CTEs:
```
WITH ITjobs (ID, Name, Position) AS (
    SELECT
          ID,
          Name,
          Position
    FROM employee
    WHERE Position like 'IT%'),
-- Define the second CTE table ITSalary with the fields ID and Salary
ITsalary (ID, Salary) AS (
    SELECT
        ID,
        Salary
    FROM Salary
    WHERE Salary > 3000)
SELECT
    ITjobs.NAME,
    ITjobs.POSITION,
    ITsalary.Salary
FROM ITjobs
    -- Combine the two CTE tables the correct join variant
    INNER JOIN ITsalary
    -- Execute the join on the ID of the tables
    ON ITjobs.ID = ITsalary.ID;
```

__Iteration: calculating factorial__
```
-- Define the target factorial number
DECLARE @target float = 5
-- Initialization of the factorial result
DECLARE @factorial float = 1

WHILE @target > 0
BEGIN
    -- Calculate the factorial number
    SET @factorial = @factorial * @target
    -- Reduce the termination condition  
    SET @target = @target - 1
END
```
__Recursion: calculating factorial__
```
WITH calculate_factorial AS (
    SELECT
        -- Initialize step and the factorial number      
        1 AS step,
        1 AS factorial
    UNION ALL
    SELECT
         step + 1,
        -- Calculate the recursive part by n!*(n+1)
        factorial * (step + 1)
    FROM calculate_factorial        
    -- Stop the recursion reaching the wanted factorial number
    WHERE step < 5)
 ```
__CTE recursion__
```
with CTE_name as (
-- Anchor member
-- initial query
    SELECT ... 
      UNION ALL
-- recursive member
-- recursive query
    --  stop criterion)
```

__Guide to use CTE:__
+ For more than 200 recursion steps, increase the number of recursion steps by 
`Set option(MAXRECURSION=32767);`

+ The following SQL queries are not allowed:
```
GROUP BY, HAVING, LEFT JOIN, RIGHT JOIN, OUTER JOIN, SELECT DISTINCT, Subqueries, TOP
```
+ The number of columns for anchor and recursive members must be the same.
+ The data types of columns for anchor and recursive members must be the same.



## Hierarchical and Recursive Queries
Hierarchical queries example
```
-- Create the CTE employee_hierarchy
with employee_hierarchy as(
    SELECT
        ID,
          NAME,
          Supervisor,
            1 as LEVEL
    FROM employee
      -- Start with the supervisor ID of the IT Director
    WHERE Supervisor = 0
    UNION ALL
    SELECT
          emp.ID,
          emp.NAME,
          emp.Supervisor,
            LEVEL + 1
    FROM employee emp
          JOIN employee_hierarchy
          ON emp.Supervisor = employee_hierarchy.ID)
    
SELECT
    cte.Name, cte.Level,
    emp.Name as ManagerID
FROM employee_hierarchy as cte
    JOIN employee as emp
    ON cte.Supervisor = emp.ID
ORDER BY Level;

```
