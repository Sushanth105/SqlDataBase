# ALTER TABLE in MySQL

The `ALTER TABLE` statement is used to **modify an existing table structure** without dropping/recreating it. It allows adding, deleting, or modifying columns, keys, constraints, and table options.

---

## 1. Add a Column
```sql
ALTER TABLE table_name
ADD column_name datatype;
```
- Adds a new column to the table.  
- You can also define position:
```sql
ALTER TABLE employees
ADD salary DECIMAL(10,2) AFTER name;
```
- `AFTER col_name` → place after a column  
- `FIRST` → place at the beginning  

---

## 2. Add Multiple Columns
```sql
ALTER TABLE employees
ADD age INT,
ADD department VARCHAR(50);
```

---

## 3. Drop (Delete) a Column
```sql
ALTER TABLE employees
DROP COLUMN age;
```

---

## 4. Rename a Column
```sql
ALTER TABLE employees
RENAME COLUMN old_name TO new_name;
```
(MySQL 8.0+)

---

## 5. Modify Column (Change Data Type/Attributes)
```sql
ALTER TABLE employees
MODIFY salary FLOAT NOT NULL;
```

---

## 6. Change Column (Rename + Modify)
```sql
ALTER TABLE employees
CHANGE old_column new_column VARCHAR(100) NOT NULL;
```

---

## 7. Add Primary Key
```sql
ALTER TABLE employees
ADD PRIMARY KEY (id);
```

---

## 8. Drop Primary Key
```sql
ALTER TABLE employees
DROP PRIMARY KEY;
```

---

## 9. Add Unique Key
```sql
ALTER TABLE employees
ADD CONSTRAINT unique_email UNIQUE (email);
```

---

## 10. Drop Unique Key
```sql
ALTER TABLE employees
DROP INDEX unique_email;
```

---

## 11. Add Foreign Key
```sql
ALTER TABLE orders
ADD CONSTRAINT fk_customer
FOREIGN KEY (customer_id) REFERENCES customers(id)
ON DELETE CASCADE ON UPDATE CASCADE;
```

---

## 12. Drop Foreign Key
```sql
ALTER TABLE orders
DROP FOREIGN KEY fk_customer;
```

---

## 13. Add Index
```sql
ALTER TABLE employees
ADD INDEX idx_name (name);
```

---

## 14. Drop Index
```sql
ALTER TABLE employees
DROP INDEX idx_name;
```

---

## 15. Add/Drop Default Value
- **Add Default**
```sql
ALTER TABLE employees
ALTER COLUMN department SET DEFAULT 'General';
```
- **Drop Default**
```sql
ALTER TABLE employees
ALTER COLUMN department DROP DEFAULT;
```

---

## 16. Rename Table
```sql
ALTER TABLE employees
RENAME TO staff;
```

---

## 17. Auto Increment
```sql
ALTER TABLE employees
MODIFY id INT AUTO_INCREMENT;
```
- Restart auto increment:
```sql
ALTER TABLE employees AUTO_INCREMENT = 100;
```

---

## 18. Character Set & Collation
```sql
ALTER TABLE employees
CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

---

## 19. Add/Drop Check Constraint
- **Add Check**
```sql
ALTER TABLE employees
ADD CONSTRAINT chk_salary CHECK (salary >= 0);
```
- **Drop Check**
```sql
ALTER TABLE employees
DROP CHECK chk_salary;
```

---

## 20. Add/Drop Generated Column
```sql
ALTER TABLE employees
ADD full_name VARCHAR(200) GENERATED ALWAYS AS (CONCAT(first_name, ' ', last_name)) STORED;
```

---

# ✅ Summary
`ALTER TABLE` in MySQL can be used to:
- Add, drop, rename, or modify columns  
- Manage keys & constraints  
- Change character sets  
- Rename tables  
- Adjust auto-increment  
- Add computed columns  

This makes it one of the most powerful DDL commands in MySQL.
