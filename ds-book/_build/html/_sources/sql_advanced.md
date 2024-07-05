# SQL Advanced 

```{note}
The following SQL syntax are based on **PostgreSQL** 
```
## Aggregation, Group by and Having

**Aggregation functions are as follow**.
- `COUNT()`: return the number of rows in a group
- `AVG()`: return the mean of a group
- `MIN()`: return the minumum value in a group
- `MAX()`: return the maximum value in a group
- `SUM()`: return the sum of a group

**Usage rules**:
- **Grouping columns**: All non-aggregated columns in SELECT clause must be in the GROUP BY clause.
- **Aliases**: Because GROUP BY is evaluated before SELECT, you cannot put aliases or expression used in the SELECT clause within the GROUP BY clause.
- **Filter conditions**: Use HAVING clause to filter grouped data on aggregated values. Unlike the WHERE clause, which filters individual rows, the HAVING clause operates on groups of rows.
- **NULL values and data types**: Can Use COALESCE to replace NULL values with something else. Use CAST operator to convert data type for aggregations.
- **Multiple grouping level**: The order of multiple columns used in GROUP BY is in hierarchical order.

**Examples**

We want to calculate the average salary for each deparment, but only for departments where the average salary is greater than $5000. Additionally, we want to display the department name and the number of employees in each department. However, some departments may have missing values for the number of employees, so we want to handle those null values as well.
```{code-block} sql
SELECT deparment, 
    COALESCE(COUNT(employee_id), 0) AS number_of_employee,
    CAST(AVG(salary) AS DECIMAL(10,2)) AS average_salary
FROM employee
GROUP BY deparment
HAVING avg(salary) > 5000
ORDER BY deparment;
```

In the above SQL script, we use COALESCE to handel missing valuse (make missing values as 0). We also use CAST to convert average of salary with two decimal places.

## Window functions
Unlike aggregate functions, which output a single calculated value for a group of data, window functions output resutls on a per-row basis.
```{code-block} sql

-- GROUP BY col_2 
SELECT MAX(col_1), col_2 FROM table GROUP BY col_2;

 col_1 | col_2          col_1 | col_2  
 ------+-------         ------+-------
   1   |   A      ==>     2   |   A 
   2   |   A              4   |   B 
   3   |   B 
   4   |   B

-- OVER (PARTITION BY col_2)
SELECT col_1, col_2, 
MAX(col_1) OVER (PARTITION BY col_2) AS max_val, 
FROM table;

 col_1 | col_2          col_1 | col_2 | max_val 
 ------+-------         ------+-------+---------
   1   |   A      ==>     1   |   A   |   2
   2   |   A              2   |   A   |   2
   3   |   B              3   |   B   |   4
   4   |   B              4   |   B   |   4
                        
```
**Basic syntax**
`SELECT func() OVER (PARTITION BY ... ORDER BY ...) FROM table;`
Window functions are used in conjunction with OVER clause. Use OVER to define a window of data.

### OVER clause
Use OVER clause to define the window frame of data. Sub-clause (used within OVER) include:

- `PARTITION BY`: Grouping for the same value
- `ORDER BY`: Order data by valuse
- `ROWS BETWEEN`: Used with ORDER BY and define a number of rows or frame size in sequence to be grouped
  - `ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`: Includes all rows from the start of the partition up to and including the current row.
  - `ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING`: Includes the current row and all rows after it until the end of the partition.
  - `ROWS BETWEEN value PRECEDING AND value FOLLOWING`: Includes a specified number of rows before and after the current row, where the values can be positive integers or expressions.
```{code-block} sql
-- calculate moving average (3-day) of daily stock price
SELECT date, price,
    AVG(price) OVER(ORDER BY date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS moving_avg
FROM stock_table;

     date  |  price             date  |  price  | moving_avg
-----------+---------      -----------+---------+-----------
2022-01-01 |   10          2022-01-01 |   10    |   NULL
2022-01-02 |   12      =>  2022-01-02 |   12    |   NULL
2022-01-03 |   13          2022-01-03 |   13    |  11.67
2022-01-04 |   11          2022-01-04 |   11    |  12.0
2022-01-05 |   15          2022-01-05 |   15    |  13.0
```

### Assign numbers
- `ROW_NUMBER()` - Assigns a unique sequential number to each row within the partition.
- `RANK()` - Assigns a unique rank to each row within the partition, considering ties. Rows with the same values receive the same rank, and the next rank is skipped.
- `DENSE_RANK()` - similar to RANK(), but the next rank is not skipped.
- `NTILE(n)` - Divides the partition into "n" buckets or groups and assigns a bucket number to each row. It ensures that each bucket has a similar number of rows, as evenly as possible.
```{code-block} sql
-- assign each employee a bucket number based on their salary (high to low)
-- divide them into 4 buckets
SELECT employee_name, salary,
    NTILE(4) OVER (ORDER BY salary DESC) AS bucket_num
FROM employee_table;

```

### Retrieve values
- LEAD(column, offset_rows) - Retrieve values that follow the current row at an offset; shift up the specified column by offset_rows as a new column.
- LAG(column, offset_rows) - Retrieve values that come before the current row at an offset; shift down the specified column by offset_rows as a new column.
- FIRST_VALUE(column) - Retrieve first row value of the specified column within the partition.
- LAST_VALUE(column) - Retrieve last row value of the specified column within the partition.
```{code-block} sql
-- lead and lag examples
SELECT id,
    LEAD(id, 1) OVER(ORDER BY id) AS id_lead1,
    LAG(id, 2) OVER(ORDER BY id) AS id_lag2
FROM table;

  id  | id_lead1 | id_lag2
------+----------+--------- 
 101  |   102    |  NULL  
 102  |   103    |  NULL
 103  |   104    |  101
 104  |   105    |  102
 105  |   NULL   |  103
```
**Aggregate**
- AVG(column) OVER (...)
- SUM(column) OVER (...)
- MIN(column) OVER (...)
- MAX(column) OVER (...)

See above exmples for use cases.

## Join tables
Simple explanation and examples can be found [here](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-joins/).

Example tables for demonstrating table join below.
```
-- table_1     -- table_2 
  id | col      id | col 
-----+-----   -----+-----
  1  |  A       3  |  E 
  2  |  B       4  |  F
  3  |  C       5  |  G 
  4  |  D       6  |  H 
```

### Inner join
Join tables by column values occurring in both tables.
```{code-block} sql
SELECT t1.id, t1.col, t2.col 
FROM table_1 AS t1
INNER JOIN table_2 AS t2 
ON t1.id = t2.id;

  id | col | col 
-----+-----+-----
  3  |  C  |  E 
  4  |  D  |  F
```

### Left join / Right join
Join tables by column values in one of tables.
```{code-block} sql
-- left join 
SELECT t1.id, t1.col, t2.col 
FROM table_1 AS t1
LEFT JOIN table_2 AS t2
ON t1.id = t2.id;

  id | col | col 
-----+-----+-----
  1  |  A  | NULL 
  2  |  B  | NULL
  3  |  C  |  E 
  4  |  D  |  F 

-- right join 
SELECT t2.id, t1.col, t2.col 
FROM table_1 AS t1
RIGHT JOIN table_2 AS t2 
ON t1.id = t2.id;

  id | col  | col 
-----+------+-----
  3  |  C   |  E 
  4  |  D   |  F
  5  | NULL |  G 
  6  | NULL |  H       
```
          
### Full outer join
Full outer join or full join is a combination of left and right join. It joins tables by column values occurring in both or one of the tables.
```{code-block} sql
SELECT *
FROM table_1 AS t1
FULL [OUTER] JOIN table_2 AS t2
ON t1.id = t2.id; 

  id  |  col |  id  | col 
------+------+------+-----
  1   |   A  | NULL | NULL
  2   |   B  | NULL | NULL 
  3   |   C  |  3   |  E
  4   |   D  |  4   |  F 
 NULL | NULL |  5   |  G
 NULL | NULL |  6   |  H
```

### Cross join
Create a Cartesian Product of rows in two or more tables. Do not require to use ON clause.
```{code-block} sql
SELECT * FROM table_1
CROSS JOIN table_2;

-- the above example will result in a 4 x 4 rows of joined table.
```
                  
### Union
Concatenate similar tables vertically. Combine multiple SELECT results into a single set. The column data type and the number of columns must be matched from tables to be joined.
```{code-block} sql
SELECT id, col FROM table_1
UNION 
SELECT id, col FROM table_2;

-- it will results in a 8 rows by 2 columns table.
```
            
### Self-join
Some good examples can be found [here](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-self-join/).

## Subquery
Embed a query inside a query.

Subquery in `WHERE` clause
```{code-block} sql
SELECT *
FROM employees
WHERE department_id IN (
    SELECT department_id
    FROM departments
    WHERE location = 'New York'
);          
       
```
                 
Subquery in `FROM` clause
```{code-block} sql
SELECT department_id, AVG(salary) AS average_salary
FROM (
    SELECT department_id, salary
    FROM employees
    WHERE hire_date >= '2022-01-01'
) AS recent_employees
GROUP BY department_id;
```

Subquery in `SELECT` clause
```{code-block} sql
SELECT employee_name, (
    SELECT department_name
    FROM departments
    WHERE department_id = employees.department_id
) AS department_name
FROM employees;
```
## Common table expression (CTE)
A temporary named result sets within SQL statement
```{code-block} sql
-- a single CTE
WITH my_cte AS (
    SELECT id, name, number 
    FROM my_table 
    WHERE id < 10
)
SELECT *
FROM my_cte;

-- multiple CTEs
WITH cte_1 AS (
    SELECT *
    FROM table_1
    WHERE column IN ("A","B","C")
), cte_2 AS (
    SELECT * 
    FROM table_2
    WHERE column IN ("C","D","E")
)
SELECT * FROM cte_1
INNER JOIN cte_2
ON cte_1.column = cte_2.column;
```