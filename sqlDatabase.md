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
### `JOIN`
#### Example Tables
Table: `Customers`
| customer\_id | name    |
| ------------ | ------- |
| 1            | Alice   |
| 2            | Bob     |
| 3            | Charlie |

Table: `Orders`

| order\_id | customer\_id | amount |
| --------- | ------------ | ------ |
| 101       | 1            | 500    |
| 102       | 2            | 300    |
| 103       | 2            | 700    |
| 104       | 4            | 200    |

#### INNER JOIN
- Returns only matching rows between tables.
- Excludes non-matching rows.

```sql
SELECT Customers.name, Orders.amount
FROM Customers
INNER JOIN Orders
ON Customers.customer_id = Orders.customer_id;
```
Result : 
| name  | amount |
| ----- | ------ |
| Alice | 500    |
| Bob   | 300    |
| Bob   | 700    |

#### LEFT JOIN (LEFT OUTER JOIN)

- Returns all rows from the left table (`Customers`).
- If no match, `NULL` is returned for right table columns.
```sql
SELECT Customers.name, Orders.amount
FROM Customers
LEFT JOIN Orders
ON Customers.customer_id = Orders.customer_id;
```
Result : 
| name    | amount |
| ------- | ------ |
| Alice   | 500    |
| Bob     | 300    |
| Bob     | 700    |
| Charlie | NULL   |

#### RIGHT JOIN (RIGHT OUTER JOIN)
- Opposite of LEFT JOIN.
- Returns all rows from the right table (`Orders`).
- If no match, `NULL` for left table columns.
```sql
SELECT Customers.name, Orders.amount
FROM Customers
RIGHT JOIN Orders
ON Customers.customer_id = Orders.customer_id;
```
Result : 
| name  | amount |
| ----- | ------ |
| Alice | 500    |
| Bob   | 300    |
| Bob   | 700    |
| NULL  | 200    |

#### FULL JOIN (FULL OUTER JOIN)
- Returns all rows from both tables.
- If no match, NULL is filled.
- MySQL does not directly support FULL JOIN, but you can simulate it with UNION:

```sql
SELECT Customers.name, Orders.amount
FROM Customers
LEFT JOIN Orders
ON Customers.customer_id = Orders.customer_id
UNION
SELECT Customers.name, Orders.amount
FROM Customers
RIGHT JOIN Orders
ON Customers.customer_id = Orders.customer_id;
```

Result : 
| name    | amount |
| ------- | ------ |
| Alice   | 500    |
| Bob     | 300    |
| Bob     | 700    |
| Charlie | NULL   |
| NULL    | 200    |

#### CROSS JOIN
- Cartesian product: all combinations of rows.
- If 3 customers × 4 orders → 12 rows.

```sql
SELECT Customers.name, Orders.amount
FROM Customers
CROSS JOIN Orders;
```

Result : 
| name  | amount |
| ----- | ------ |
| Alice | 500    |
| Alice | 300    |
| Alice | 700    |
| Alice | 200    |
| Bob   | 500    |
| ...   | ...    |

#### SELF JOIN
- Table joins with itself.
- Useful for hierarchical data (like employees and managers).

Example Table: `Employees`
| emp\_id | name    | manager\_id |
| ------- | ------- | ----------- |
| 1       | Alice   | NULL        |
| 2       | Bob     | 1           |
| 3       | Charlie | 1           |
| 4       | David   | 2           |

Find employees and their managers:
```sql
SELECT e1.name AS employee, e2.name AS manager
FROM Employees e1
LEFT JOIN Employees e2
ON e1.manager_id = e2.emp_id;
```
Result : 
| employee | manager |
| -------- | ------- |
| Alice    | NULL    |
| Bob      | Alice   |
| Charlie  | Alice   |
| David    | Bob     |

#### Quick Comparison
CASE
| JOIN Type | Returns                                                              |
| --------- | -------------------------------------------------------------------- |
| **INNER** | Only matching rows                                                   |
| **LEFT**  | All from left + matches from right                                   |
| **RIGHT** | All from right + matches from left                                   |
| **FULL**  | All rows from both, match or not                                     |
| **CROSS** | All combinations (Cartesian product)                                 |
| **SELF**  | A table joined with itself (hierarchical or relational within table) |
---

### Views
#### Creating Views in MySQL
```sql
CREATE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```
Example : 
```sql
CREATE VIEW Dept10Employees AS
SELECT emp_id, emp_name, salary
FROM Employees
WHERE dept_id = 10;
```
Now you can query the view just like a table:
```sql
SELECT * FROM Dept10Employees;
```

Key points:
- Views simplify complex queries.
- They provide security by exposing only certain columns/rows.
- They act as an abstraction layer over raw tables.

#### Updating Views
```sql
UPDATE Dept10Employees
SET salary = salary + 1000
WHERE emp_id = 5
```

Rules for Updatable Views:
- View must be based on a single table.
- Must not use aggregate functions (SUM, AVG…), DISTINCT, GROUP BY, or subqueries.
- Must not contain UNION or HAVING.

#### Dropping Views
```sql
DROP VIEW view_name;
```
---

### Stored Procedure
A **Stored Procedure** in MySQL is a set of SQL **statements stored** in the database that can be executed repeatedly by just calling it.

#### Why Use Stored Procedures?
- Reusability – Write once, use many times.
- Performance – SQL execution plan is precompiled and stored.
- Security – Can hide complex queries; users can just call the procedure instead of accessing tables directly.
- Maintainability – Business logic is centralized in the database.
- Reduced Network Traffic – Instead of sending multiple SQL queries from application → database, just call one stored procedure.

#### Syntax
```sql
DELIMITER $$

CREATE PROCEDURE procedure_name (parameters)
BEGIN
    -- SQL statements
END $$

DELIMITER ;
```
#### Breakdown:

- DELIMITER – By default, MySQL ends statements with ;. But inside procedures, we need multiple SQL statements.  
So, we temporarily change the delimiter (e.g., $$).
- CREATE PROCEDURE – Creates the procedure.
- (parameters) – Can be IN, OUT, or INOUT parameters.
- BEGIN ... END – Block that holds SQL statements.

#### Calling a Stored Procedure
```sql
CALL procedure_name(arguments);
```

#### Parameters in Stored Procedures
There are 3 types of parameters:

1. **IN (default): Input value (like function arguments).**

Example: Take a customer ID and fetch their details.
```sql
DELIMITER $$
CREATE PROCEDURE GetCustomer(IN cust_id INT)
BEGIN
    SELECT * FROM Customers WHERE CustomerID = cust_id;
END $$
DELIMITER ;

CALL GetCustomer(3);
```

2. **OUT: Returns a value back.**
```sql
DELIMITER $$
CREATE PROCEDURE GetTotalCustomers(OUT total INT)
BEGIN
    SELECT COUNT(*) INTO total FROM Customers;
END $$
DELIMITER ;

CALL GetTotalCustomers(@count);
SELECT @count;
```

3. **INOUT: Both input and output.**
```sql
DELIMITER $$
CREATE PROCEDURE DoubleValue(INOUT num INT)
BEGIN
    SET num = num * 2;
END $$
DELIMITER ;

SET @x = 5;
CALL DoubleValue(@x);
SELECT @x;  -- Result: 10
```

### Control Structures inside Procedures
Stored procedures support programming-like constructs:

**IF ... ELSE**
```sql
IF condition THEN
    -- statements
ELSE
    -- statements
END IF;
```

**CASE (like switch)**
```sql
CASE var
    WHEN 1 THEN SET result = 'One';
    WHEN 2 THEN SET result = 'Two';
    ELSE SET result = 'Other';
END CASE;
```

**LOOP / WHILE / REPEAT**
```sql
WHILE i <= 10 DO
    SET i = i + 1;
END WHILE;
```

#### Drop procedure:
```sql
DROP PROCEDURE procedure_name;
```

#### To check all stored procedures in current database:
```sql
SHOW PROCEDURE STATUS WHERE Db = 'your_database';
```

#### Local Variable
`DECLARE` can only be used inside a stored procedure, function, trigger, or event, and always inside a `BEGIN ... END` block.
```sql
DELIMITER $$
CREATE PROCEDURE TestProc()
BEGIN
    DECLARE local_var INT;
    SET local_var = 5;
    SELECT local_var;
END $$
DELIMITER ;
```

#### Real-World Example
```sql
DELIMITER $$

CREATE PROCEDURE AddProduct(
    IN p_name VARCHAR(100),
    IN p_price DECIMAL(10,2),
    OUT new_id INT
)
BEGIN
    INSERT INTO Products(name, price) VALUES(p_name, p_price);
    SET new_id = LAST_INSERT_ID();
END $$

DELIMITER ;

-- Calling it
CALL AddProduct('Laptop', 75000, @id);
SELECT @id;
```
---

### Functions
#### What is a Function in MySQL?
- A Function in MySQL is like a Stored Procedure, but with some differences.
- It is a stored program that returns a single value and can be used directly in SQL statements.
- Think of it like functions in programming languages: you give input (parameters), it processes something, and returns a value.

#### Functions vs Stored Procedures
| Feature                                 | Function                                           | Stored Procedure                                                      |
| --------------------------------------- | -------------------------------------------------- | --------------------------------------------------------------------- |
| Return value                            | Must return **exactly one value** (using `RETURN`) | Can return **none, one, or many values** (using `OUT` or result sets) |
| Use in SQL                              | Can be used in `SELECT`, `WHERE`, `ORDER BY`, etc. | Cannot be used directly in SQL statements (must use `CALL`)           |
| Parameters                              | Only `IN` parameters                               | Supports `IN`, `OUT`, `INOUT`                                         |
| DML statements (`INSERT/UPDATE/DELETE`) | Generally not recommended inside functions         | Allowed inside procedures                                             |
| Invocation                              | Called in SQL queries                              | Called with `CALL`                                                    |

#### Syntax
```sql
DELIMITER $$

CREATE FUNCTION function_name (parameters)
RETURNS datatype
DETERMINISTIC
BEGIN
    -- variable declarations
    -- SQL statements
    RETURN value;
END $$

DELIMITER ;
```
**Keywords**:
- **RETURNS** datatype → must specify what type of value is returned (e.g., INT, VARCHAR).
- **DETERMINISTIC** → means that given the same input, the function will always return the same output (important for replication).
- **RETURN** → used to return the final value.

#### Creating and Using a Function
**Simple Function (Square of a Number)**
```sql
DELIMITER $$

CREATE FUNCTION SquareNum(x INT)
RETURNS INT
DETERMINISTIC
BEGIN
    RETURN x * x;
END $$

DELIMITER ;

-- Usage:
SELECT SquareNum(5);  -- Output: 25
```

**Function with String**
```sql
DELIMITER $$

CREATE FUNCTION GetInitial(name VARCHAR(50))
RETURNS CHAR(1)
DETERMINISTIC
BEGIN
    RETURN LEFT(name, 1);
END $$

DELIMITER ;

-- Usage:
SELECT GetInitial('Sushanth');  -- Output: 'S'
```

#### Example of a NON-DETERMINISTIC Function
```sql
DELIMITER $$

CREATE FUNCTION GetRandom()
RETURNS INT
NOT DETERMINISTIC
BEGIN
    RETURN FLOOR(RAND() * 100);
END $$

DELIMITER ;

-- Usage
SELECT GetRandom();   -- Different result each time
```

**Function with Table Data**
```sql
DELIMITER $$

CREATE FUNCTION TotalOrders(cust_id INT)
RETURNS INT
DETERMINISTIC
BEGIN
    DECLARE total INT;
    SELECT COUNT(*) INTO total FROM Orders WHERE customer_id = cust_id;
    RETURN total;
END $$

DELIMITER ;

-- Usage:
SELECT TotalOrders(101);
```

#### Dropping a Function
```sql
DROP FUNCTION function_name;
```
#### Viewing Functions
```sql
SHOW FUNCTION STATUS WHERE Db = 'your_database';
```

#### Built-in Functions in MySQL

MySQL already provides hundreds of built-in functions. They fall into categories:

1. **String Functions**

- `CONCAT(s1, s2, …)` → Concatenate strings
- `LENGTH(s)` → Length of a string
- `UPPER(s)` / `LOWER(s)` → Convert case
- `SUBSTRING(s, start, length)`→ Extract substring

2. **Numeric Functions**

- `ABS(x)` → Absolute value
- `ROUND(x, d)` → Round to d decimal places
- `CEIL(x)` / `FLOOR(x)` → Ceiling/Floor value
- `MOD(a, b)` → Remainder

3. **Date and Time Functions**

- `NOW()` → Current timestamp
- `CURDATE()` / `CURTIME()` → Current date/time
- `DATEDIFF(d1, d2)` → Difference in days
- `DATE_ADD(date, INTERVAL x DAY)` → Add interval

4. **Aggregate Functions (used with `GROUP BY`)**

- `COUNT(col)` → Number of rows
- `SUM(col)` → Total sum
- `AVG(col)` → Average
- `MIN(col)` / MAX(col) → Min/Max value

5. **Control Flow Functions**

- `IF(condition, true_value, false_value)`
- `CASE WHEN … THEN … ELSE … END`
- `NULLIF(a, b)` → Returns NULL if a = b
---

### Trigger

A trigger is a special type of stored program in a database that is automatically executed (fired) when a specified event occurs on a particular table or view.

- Unlike stored procedures, triggers are not called explicitly — the database runs them automatically when the event occurs.
- They are mostly used for **enforcing rules, auditing changes, maintaining logs, and ensuring data integrity.**

#### Events That Can Fire a Trigger
In MySQL, triggers are associated with DML events (Data Manipulation Language):

- `INSERT` – fires when a new row is inserted.
- `UPDATE` – fires when a row is modified.
- `DELETE` – fires when a row is deleted.

⚡ Note: Triggers in MySQL cannot be defined on SELECT, CREATE, DROP, etc.

#### When Does a Trigger Fire? (Timing)

For each event, you can define whether the trigger executes:
- `BEFORE` – executes before the event.
- `AFTER` – executes after the event.

So possible triggers are:

- `BEFORE` `INSERT`
- `AFTER` `INSERT`
- `BEFORE` ` UPDATE`
- `AFTER` `UPDATE`
- `BEFORE` `DELETE`
- `AFTER` `DELETE`

#### `OLD` and `NEW` Keywords
Inside a trigger, you can access the row values using these pseudorecords:
- **NEW.column_name** → the new value (for `INSERT`/`UPDATE`).
- **OLD.column_name** → the old value (for `UPDATE`/`DELETE`).

| Event  | OLD           | NEW           |
| ------ | ------------- | ------------- |
| INSERT | Not available | Available     |
| UPDATE | Available     | Available     |
| DELETE | Available     | Not available |

#### Syntax
```sql
CREATE TRIGGER trigger_name
{BEFORE | AFTER} {INSERT | UPDATE | DELETE}
ON table_name
FOR EACH ROW
BEGIN
    -- Trigger logic here
END;
```

#### Logging Inserts
```sql
-- Log table
CREATE TABLE employee_log (
    log_id INT AUTO_INCREMENT PRIMARY KEY,
    emp_id INT,
    action VARCHAR(50),
    action_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Trigger
DELIMITER $$
CREATE TRIGGER after_employee_insert
AFTER INSERT ON employees
FOR EACH ROW
BEGIN
    INSERT INTO employee_log (emp_id, action)
    VALUES (NEW.id, 'INSERT');
END$$
DELIMITER ;
```

#### Prevent Negative Salary
```sql
DELIMITER $$
CREATE TRIGGER check_salary
BEFORE INSERT ON employees
FOR EACH ROW
BEGIN
    IF NEW.salary < 0 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Salary cannot be negative!';
    END IF;
END$$
DELIMITER ;
```

#### Audit Updates
```sql
CREATE TABLE salary_audit (
    audit_id INT AUTO_INCREMENT PRIMARY KEY,
    emp_id INT,
    old_salary DECIMAL(10,2),
    new_salary DECIMAL(10,2),
    changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

DELIMITER $$
CREATE TRIGGER after_salary_update
AFTER UPDATE ON employees
FOR EACH ROW
BEGIN
    IF OLD.salary <> NEW.salary THEN
        INSERT INTO salary_audit (emp_id, old_salary, new_salary)
        VALUES (OLD.id, OLD.salary, NEW.salary);
    END IF;
END$$
DELIMITER ;
```

#### Show triggers
```sql
SHOW TRIGGERS;
```

#### Drop a trigger
```sql
DROP TRIGGER IF EXISTS trigger_name;
```

#### Check trigger details
```sql
SELECT * FROM information_schema.triggers
WHERE trigger_schema = 'your_database';
```