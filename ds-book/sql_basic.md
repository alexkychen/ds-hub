# SQL Basics

```{note}
The following SQL syntax are based on **PostgreSQL** 
```

## Show existing tables

List all existing tables in a database 
```{code-block} sql
SELECT tablename FROM pg_catalog.pg_tables WHERE schemaname = 'public';
```

## Data type and constraints

Data type:
- String: VARCHAR, CHAR, TEXT ([docs](https://www.postgresql.org/docs/current/datatype-character.html))
- Number: INTEGER, NUMERIC, REAL ([docs](https://www.postgresql.org/docs/current/datatype-numeric.html))
- Datetime: DATE, TIME, TIMESTAMP ([docs](https://www.postgresql.org/docs/current/datatype-datetime.html))
- Other: ([docs](https://www.postgresql.org/docs/current/datatype.html))

Data constraint:
- `NOT NULL`
- `UNIQUE`
- `PRIMARY KEY` & `FOREIGN KEY`
- `SERIAL` (like `auto-increment` in sqlite)

## Drop table 
```{note}
`Drop` will remove the table entirely; `Delete` will remove data but the table still exists.
```

```{code-block} sql
-- remove a table if it exists
DROP TABLE IF EXISTS table_name;

-- or when the table does exist
DROP TABLE table_name;
```
## Create table 
```{code-block} sql
CREATE TABLE table_name_1 (
    profile_id SERIAL PRIMARY KEY,
    name VARCHAR(20) NOT NULL,
    email VARCHAR(50) UNIQUE NOT NULL,
    phone_num VARCHAR(20) 
);

CREATE TABLE table_name_2 (
    buy_id SERIAL PRIMARY KEY,
    profile_id INTEGER REFERENCES table_name_1 (profile_id),
    prod_name VARCHAR NOT NULL,
    cost REAL NOT NULL,
    buy_date DATE
);
```

## Insert data
Insert or add data in a row
```{code-block} sql
-- insert data for every column without specifying column names
INSERT INTO table_name_1 VALUES(1, 'Joe', 'joesmith@email.com', '123-456-7890');

-- or insert data for certain columns
INSERT INTO table_name_2(profile_id, prod_name, cost) VALUES(1, 'gold', 888.88);
```

## Select clause
Select data from table.
```{code-block} sql
-- select all data 
SELECT * FROM table_name;

-- count number of rows
SELECT COUNT(*) FROM table_name;

-- select certain columns of data 
SELECT name, email FROM table_name_1;

-- get unique values of a column 
SELECT DISTINCT(name) FROM table_name_1;
```

## Order by and limit
Sort data by column values. Limit is used for outputing a certain number of first few rows.
```{code-block} sql
-- order by col_1 in ascending order and then col_2 in descending order.
SELECT * FROM table_name
ORDER BY col_1 ASC, col_2 DESC;

-- output first 3 rows of data 
SELECT * FROM table_name
LIMIT 3;

-- combination
SELECT * FROM table_name
ORDER BY col_1 ASC
LIMIT 5;
```

## Where clause 
Select data with conditions

Condition operators used in the `WHERE` clause

- `AND, OR, NOT, =, !=, >, >=, <, <=, BETWEEN, LIKE, ILIKE, and IN(...)`
- `BETWEEN value1 AND value2` is inclusive.
- Use `NOT BETWEEN value1 AND value2` for exclusive.
- `IN(...)` is used for a list of values; use `NOT IN(...)` to deselect the values.
- `LIKE` is case sensitive; `ILIKE` is case insensitive.

Wildcard symbols used in conjunction with `LIKE` or `ILIKE` operator.

- `%` for zero or more characters. 'bl%' finds bl, black, blue, blob, etc.
- `_` for a single character. `h_t` finds hat, hot, hit, etc.
- `[]` for any single character within brackets. `h[oa]t` finds hot and hat
- `[^...]` for any character not in brackets. `h[^oa]t` finds hit, but not hot and hat
- `[.-.]` for any single characters within the specified range. `c[a-c]t` finds cat, cbt, and cct

```{code-block} sql
-- example 1
SELECT * FROM table_name 
WHERE column_name != 'value1' AND column_name > 'value2';

-- example 2
SELECT * FROM table_name
WHERE column_name BETWEEN 10 AND 20;

-- example 3
SELECT * FROM table_name
WHERE column_name IN ('val1', 'val2', 'val3');

-- example 4
SELECT * FROM table_name
WHERE column_name LIKE 'pattern%';
```

## Condition expression

### Case statement
Create a new column data based on the values of another column. Similar to if-else statement in MySQL and others.
```{code-block} sql
-- basic example
SELECT column_name, 
  CASE
    WHEN column_name = 'value1' THEN 'Result 1'
    WHEN column_name = 'value2' THEN 'Result 2'
    ELSE 'Default Result'
  END AS new_column
FROM table_name;

-- complex example
SELECT id, name,
  CASE 
    WHEN (id < 100) THEN 'Premium'
    WHEN (id BTWEEN 100 AND 200) THEN 'Plus'
    ELSE 'Normal'
  END AS membership
FROM mytable;
```

### Coalesce
Use COALESCE function to select the first non-null value from a list. It is used when a column might have null values, and we need to return something to replace a null value. In the following example we have a table called "employees" with columns "first_name", "middle_name", and "last_name". However, some employees may not have a middle name recorded, resulting in a NULL value. In such cases, we want to replace a null middle name with an empty string '', so we can use a COALESCE function to do so.
```{code-block} sql
-- return an empty string '' when middle_name column is null
SELECT first_name, COALESCE(middle_name, '') AS middle_name, last_name
FROM employee;
```

### NULLIF
Use NULLIF function to return a null value when two inputs are equal. The 1st example returns NULL values if the value of first_name column is a 'none' string. Otherwise, it returns its original value. The 2nd example return NULL values in name_check if first and last names are identical. The 3rd example set salary column to NULL if it is 0.
```{code-block} sql
-- 1st example
SELECT NULLIF(first_name, 'none') AS first_name_corrected
FROM employee;

-- 2nd example
SELECT NULLIF(first_name, last_name) AS name_check
FROM employee;

-- 3rd example
UPDATE employee 
SET salary = NULLIF(salary, 0);
```

### IS NULL
Use `IS NULL` or `IS NOT NULL` operators to check for null or non-null values.
```{code-block} sql
-- select data when column_name's value is null
SELECT * FROM table_name
WHERE column_name IS NULL;

-- select data when column_name's value is not null
SELECT * FROM table_name 
WHERE column_name IS NOT NULL;
```

### EXISTS
Use EXISTS to check for the existence of related records. In the following example, we want to return customer id and name from a customer table whom have purchasing records in the order table.
```{code-block} sql
SELECT id, name
FROM customer
WHERE EXISTS (SELECT 1 FROM order WHERE order.customer_id = customer.id)
```

## Cast operator
Use CAST to convert data type
```{code-block} sql
-- convert string to integer
SELECT CAST('123' AS INTEGER);

-- convert string to timestamp
SELECT CAST('2011-01-01' AS TIMESTAMP);

-- convert number to text
SELECT CAST(123.4 AS TEXT);
```

## Timestamp operation
```{code-block} sql
-- output current time, date and timestamp 
SELECT now(), timeofday(), current_time, current_date, current_timestamp;

-- insert current timestamp to my_table
INSERT INTO my_table(created_at) VALUES(current_timestamp);

-- use EXTRACT() to get year, month, day, hour, minute, etc. from timestamp 
SELECT EXTRACT(year from timestamp_column) AS year FROM my_table;
SELECT EXTRACT(quarter from timestamp_column) AS quarter FROM my_table;

-- use AGE() to calculate the duration between now and a timestamp 
SELECT AGE(timestamp_col) AS duration FROM my_table; 

-- use TO_CHAR() to reformat a timestamp to text 
SELECT TO_CHAR(timestamp_col, 'YYYY-MM-DD') AS date FROM my_table;
```
Ref: [Date/Time format pattern](https://www.postgresql.org/docs/current/functions-formatting.html)