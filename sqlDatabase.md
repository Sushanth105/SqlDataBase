# SQL Database

## DataBase Operations
### Create a New Database
```sql
CREATE DATABASE my_database;
```
#### MySQL database names:
- Cannot contain spaces.
- Should be lowercase for consistency.
- Use underscores _ instead of spaces.
---

### View All Databases
```sql
SHOW DATABASES;
```
---
### Select the Database to Use
```sql
USE my_database;
```
---
### Drop Database
```sql
DROP DATABASE my_database;
```
---
## Table Operations
### Create a new Table
#### Basic Syntax
```sql
CREATE TABLE table_name (
    column_name data_type constraints,
    column_name data_type constraints,
    ...
);
```
#### Data Types
| Category      | Examples                                                        | Notes                                               |
| ------------- | --------------------------------------------------------------- | --------------------------------------------------- |
| **Numeric**   | `INT`, `BIGINT`, `SMALLINT`, `DECIMAL(10,2)`, `FLOAT`, `DOUBLE` | Use `DECIMAL` for money (exact precision).          |
| **String**    | `CHAR(n)`, `VARCHAR(n)`, `TEXT`                                 | `CHAR` = fixed length, `VARCHAR` = variable length. |
| **Date/Time** | `DATE`, `DATETIME`, `TIMESTAMP`, `TIME`, `YEAR`                 | Use `TIMESTAMP` for auto date tracking.             |
| **Other**     | `BOOLEAN` (alias of `TINYINT(1)`), `JSON`, `BLOB`               | `JSON` for structured text, `BLOB` for binary data. |

#### Common Constraints
| Constraint       | Description                                       |
| ---------------- | ------------------------------------------------- |
| `PRIMARY KEY`    | Uniquely identifies each row.                     |
| `AUTO_INCREMENT` | Automatically generates sequential numbers.       |
| `NOT NULL`       | Column cannot have `NULL` values.                 |
| `UNIQUE`         | Ensures all values are different.                 |
| `DEFAULT`        | Sets a default value if none is provided.         |
| `CHECK`          | Validates values based on a condition (MySQL 8+). |
| `FOREIGN KEY`    | Links a column to another table’s primary key.    |

#### Example Table
```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,           -- Unique ID for each user
    name VARCHAR(100) NOT NULL,                  -- Cannot be NULL
    email VARCHAR(100) UNIQUE,                   -- Must be unique
    age INT CHECK (age >= 18),                   -- Minimum age restriction
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP, -- Auto-filled with current time
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

#### Viewing Table Structure
```sql
DESCRIBE users;
-- or
DESC users;
```

#### Creating a Table with a Foreign Key
```sql
CREATE TABLE orders (
    order_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,
    product_name VARCHAR(100),
    order_date DATE,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

#### One-to-One (1:1)
```sql
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL
);

CREATE TABLE user_profiles (
    user_id INT PRIMARY KEY,  -- Also the foreign key
    bio TEXT,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

#### One-to-Many (1:N)
```sql
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100)
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    product_name VARCHAR(100),
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

#### Many-to-One (N:1)
This is just the reverse view of One-to-Many.
- In the above example, many `orders` belong to one `user`.

#### Many-to-Many (M:N)
```sql
CREATE TABLE students (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100)
);

CREATE TABLE courses (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(100)
);

CREATE TABLE student_courses (
    student_id INT,
    course_id INT,
    PRIMARY KEY (student_id, course_id),
    FOREIGN KEY (student_id) REFERENCES students(id) ON DELETE CASCADE,
    FOREIGN KEY (course_id) REFERENCES courses(id) ON DELETE CASCADE
);
```
---
### Insert value To Table
#### Basic Syntax
```sql
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);
```
- The column list specifies which columns you’re inserting into.
- The VALUES list must match the column list in number and order.
- Strings are wrapped in single quotes `'...'`.
- Numbers are written without quotes.

#### Example Using Users Table
```sql
INSERT INTO Users (name, email, password)
VALUES ('Sushanth', 'sushanth@example.com', 'mypassword');
```

#### Inserting Multiple Rows at Once
```sql
INSERT INTO Users (name, email, password)
VALUES
('Alice', 'alice@example.com', 'alice123'),
('Bob', 'bob@example.com', 'bob123'),
('Charlie', 'charlie@example.com', 'charlie123');
```
#### Insert Without Column List (Not Recommended)
```sql
INSERT INTO Users
VALUES (NULL, 'David', 'david@example.com', 'david123');
```

#### Handling `UNIQUE` and Duplicate Values
You can avoid errors with:
```sql
INSERT IGNORE INTO Users (name, email, password)
VALUES ('Alice', 'alice@example.com', 'newpass'); -- Will skip if duplicate
```
Or update on duplicate:
```sql
INSERT INTO Users (name, email, password)
VALUES ('Alice', 'alice@example.com', 'newpass')
ON DUPLICATE KEY UPDATE password = 'newpass';
```
---
### Update values of Table
#### Basic Syntax
```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```
- `SET` → specifies which columns to change.
- `WHERE` → determines which rows are updated. Without it, all rows get updated.

#### Example Using `Users` Table
```sql
UPDATE Users
SET name = 'Sushanth Kumar', password = 'newpassword123'
WHERE id = 1;
```
#### Updating Multiple Rows
```sql
UPDATE Users
SET password = 'default123'
WHERE email LIKE '%@example.com';
```
- Updates all users whose email ends with `@example.com`.

#### Updating All Rows (Use Carefully)
```sql
UPDATE Users
SET password = 'temporaryPass';
```
- Changes the password for all users.  
⚠ Dangerous unless intentional.

#### Conditional Updates with Expressions
```sql
UPDATE Users
SET name = CONCAT(name, ' (verified)')
WHERE id IN (2, 3, 4);
```

#### Using UPDATE with JOIN
```sql
UPDATE orders
JOIN Users ON orders.user_id = Users.id
SET orders.status = 'Cancelled'
WHERE Users.email = 'alice@example.com';
```
---
### Delete the Data from the Table
#### Syntax
```sql
DELETE FROM table_name
WHERE condition;
```

#### Delete a Single Row
```sql
DELETE FROM Users
WHERE id = 3;
```

#### Delete Multiple Rows
```sql
DELETE FROM Users
WHERE email LIKE '%@example.com';
```

#### Delete All Rows (Dangerous)
```sql
DELETE FROM Users;
```

#### Deleting the Entire Table
```sql
DROP TABLE table_name;
```
---
### Select
#### Basic Syntax
```sql
SELECT column1, column2, ...
FROM table_name
[WHERE condition]
[GROUP BY column_list]
[HAVING condition]
[ORDER BY column_list]
[LIMIT number];
```
- `SELECT` → specifies which columns to retrieve.
- `FROM` → specifies the table(s).
- `WHERE` → filters rows.
- `GROUP BY` → groups rows for aggregation.
- `HAVING` → filters aggregated results.
- `ORDER BY` → sorts results.
- `LIMIT` → restricts number of rows returned.

#### Select All Columns
```sql
SELECT * FROM Users;
```

#### Select Specific Columns
```sql
SELECT name, email FROM Users;
```

#### Using Aliases
```sql
SELECT name AS full_name, email AS user_email
FROM Users;
```

#### Filtering with `WHERE`
```sql
SELECT * FROM Users
WHERE id = 1;
```

#### Pattern Matching with `LIKE`
```sql
SELECT * FROM Users
WHERE email LIKE '%@example.com';
```

#### Sorting Results
```sql
SELECT * FROM Users
ORDER BY name ASC;   -- ASC = ascending, DESC = descending
```
#### Limiting Results
```sql
SELECT * FROM Users
LIMIT 5;
```

#### Aggregation Functions
```sql
SELECT COUNT(*) AS total_users FROM Users;
SELECT AVG(age) AS average_age FROM Users;
SELECT MAX(age) AS oldest FROM Users;
```

#### Grouping Data
```sql
SELECT email, COUNT(*) AS duplicates
FROM Users
GROUP BY email
HAVING COUNT(*) > 1;
```

#### Joins Between Tables
```sql
SELECT Users.name, Orders.product_name
FROM Users
JOIN Orders ON Users.id = Orders.user_id;
```

#### Special Variations
`DISTINCT` → removes duplicates
```sql
SELECT DISTINCT email FROM Users;
```

Subqueries → query inside another query
```sql
SELECT name FROM Users
WHERE id IN (SELECT user_id FROM Orders);
```

Calculated Columns
```sql
SELECT name, (price * quantity) AS total_cost FROM Orders;
```

#### `BETWEEN`
```sql
SELECT column_name(s)
FROM table_name
WHERE column_name BETWEEN value1 AND value2;
```

1. Numeric Range
```sql
SELECT * 
FROM Employees
WHERE salary BETWEEN 30000 AND 50000;
```
2. Date Range
```sql
SELECT * 
FROM Orders
WHERE order_date BETWEEN '2025-01-01' AND '2025-01-31';
```
---
### `HAVING`

- `WHERE` → Filters rows before grouping.
- `HAVING` → Filters groups after aggregation.
- Used with aggregate functions (although MySQL does allow it without aggregates).
- Typically comes after `GROUP BY` in the query.

#### Using `HAVING` with Aggregates
```sql
SELECT customer_id, SUM(amount) AS total_amount
FROM Orders
GROUP BY customer_id
HAVING SUM(amount) > 700;
```

#### `WHERE` vs `HAVING`
Using `WHERE`:
```sql
SELECT customer_id, SUM(amount) AS total_amount
FROM Orders
WHERE amount > 300
GROUP BY customer_id;
```
Filters rows first (only orders > 300 considered before grouping).

Using `HAVING`:
```sql
SELECT customer_id, SUM(amount) AS total_amount
FROM Orders
GROUP BY customer_id
HAVING SUM(amount) > 700;
```
Groups all rows first, then filters totals.

#### Multiple Conditions in HAVING
```sql
SELECT customer_id, COUNT(order_id) AS total_orders, SUM(amount) AS total_amount
FROM Orders
GROUP BY customer_id
HAVING COUNT(order_id) >= 2 AND SUM(amount) > 900;
```
---
### `CASE` ... `THEN`
#### Simple CASE (compares to a single expression)
```sql
CASE expression
    WHEN value1 THEN result1
    WHEN value2 THEN result2
    ...
    ELSE default_result
END
```
example
```sql
SELECT name, marks,
CASE marks
    WHEN 85 THEN 'A'
    WHEN 60 THEN 'B'
    ELSE 'C'
END AS grade
FROM Students;
```
#### Searched CASE (uses logical conditions)
```sql
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    ...
    ELSE default_result
END
```
example
```sql
SELECT name, marks,
CASE
    WHEN marks >= 80 THEN 'A'
    WHEN marks >= 60 THEN 'B'
    ELSE 'C'
END AS grade
FROM Students;
```
#### Column-wise CASE
```sql
SELECT
    SUM(CASE WHEN marks >= 80 THEN 1 ELSE 0 END) AS grade_A_count,
    SUM(CASE WHEN marks >= 60 AND marks < 80 THEN 1 ELSE 0 END) AS grade_B_count,
    SUM(CASE WHEN marks < 60 THEN 1 ELSE 0 END) AS grade_C_count
FROM Students;
```
---
### `OFFSET`
`OFFSET` in MySQL is used to skip a certain number of rows before starting to return the result set.

It is always used with `LIMIT` or inside window functions.

#### Syntax
```sql
SELECT column_list
FROM table_name
LIMIT row_count OFFSET offset_value;
```
- `row_count` → Number of rows to return.
- `offset_value` → Number of rows to skip before starting to return rows.

#### 👉 You can also write:
```sql
LIMIT offset_value, row_count;
```
(both mean the same thing, but `LIMIT ... OFFSET ...` is more readable).

#### Example Table: `Employees`
| id | name    | salary |
| -- | ------- | ------ |
| 1  | Alice   | 50000  |
| 2  | Bob     | 60000  |
| 3  | Charlie | 55000  |
| 4  | David   | 70000  |
| 5  | Eva     | 65000  |

#### Example 1: Basic Pagination
Return 2 rows starting from the first row:
```sql
SELECT * FROM Employees
LIMIT 2 OFFSET 0;
```
Result:

| id | name  | salary |
| -- | ----- | ------ |
| 1  | Alice | 50000  |
| 2  | Bob   | 60000  |

Return 2 rows after skipping the first 2 rows:

```sql
SELECT * FROM Employees
LIMIT 2 OFFSET 2;
```
Result:

| id | name    | salary |
| -- | ------- | ------ |
| 3  | Charlie | 55000  |
| 4  | David   | 70000  |

Return next 2 rows (page 3):
```sql
SELECT * FROM Employees
LIMIT 2 OFFSET 4;
```
Result:
| id | name | salary |
| -- | ---- | ------ |
| 5  | Eva  | 65000  |

#### Example 2: Alternate Syntax
Instead of LIMIT 2 OFFSET 2, you can write:
```sql
SELECT * FROM Employees
LIMIT 2, 2;
```
Here:
- First number = `OFFSET` (2 → skip first 2).
- Second number = `LIMIT` (2 → return 2 rows).

#### Example 3: With `ORDER` BY (Recommended)
`OFFSET` makes the most sense when rows are ordered.
```sql
SELECT * FROM Employees
ORDER BY salary DESC
LIMIT 2 OFFSET 2;
```
- Skips top 2 highest salaries.
- Returns next 2 employees by salary.
---
