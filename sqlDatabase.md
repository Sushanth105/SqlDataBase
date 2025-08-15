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