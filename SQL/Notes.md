# Chapter 1: Creating Tables, Data Types & Constraints (PostgreSQL)

## What is a Table?

A **table** is a database object used to store related data in **rows and columns**.

Example:
- Employee
- Student
- Product

---

# CREATE TABLE

Used to create a new table in the database.

## Syntax

```sql
CREATE TABLE table_name (
    column_name data_type constraint,
    ...
);
```

## Example

```sql
CREATE TABLE employee(
    employee_id SERIAL PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    position VARCHAR(30) NOT NULL,
    department VARCHAR(20),
    hire_date DATE,
    salary NUMERIC(10,2)
);
```

---

# Data Types

A **data type** defines what kind of values a column can store.

| Data Type | Purpose |
|-----------|---------|
| SERIAL | Auto-increment integer (PostgreSQL) |
| VARCHAR(n) | Variable-length text (maximum n characters) |
| DATE | Stores dates |
| NUMERIC(p,s) | Stores exact decimal numbers |

## SERIAL

Automatically generates sequential integer values.

```sql
employee_id SERIAL
```

Equivalent to:

- Integer column
- Sequence
- Auto Increment

---

## VARCHAR(n)

Stores text up to **n characters**.

```sql
name VARCHAR(50)
```

- 10 characters → Stored
- 30 characters → Stored
- 51 characters → Error

Unlike `CHAR`, it **does not reserve the full space**.

---

## DATE

Stores only date values.

```sql
hire_date DATE
```

Example

```
2026-07-20
```

---

## NUMERIC(p,s)

Stores exact decimal values.

```sql
salary NUMERIC(10,2)
```

Where

- p = Total digits
- s = Digits after decimal

Example

```
99999999.99 ✔
100000000.00 ❌
```

---

# Constraints

Constraints are rules that maintain data integrity.

## PRIMARY KEY

- Unique
- Cannot be NULL
- One per table

```sql
employee_id SERIAL PRIMARY KEY
```

---

## NOT NULL

Ensures a value must always be provided.

```sql
name VARCHAR(50) NOT NULL
```

---

# Viewing Data

Display all rows

```sql
SELECT * FROM employee;
```

Display table structure (PostgreSQL)

```sql
\d employee
```

---

# Best Practices

- Use **snake_case** (`employee_name`)
- Always create a Primary Key
- Use `NOT NULL` for mandatory columns
- Choose appropriate data types
- Don't use unnecessarily large `VARCHAR` sizes

---

# Interview Questions

### What is a table?

A collection of related data stored in rows and columns.

---

### What is SERIAL?

A PostgreSQL pseudo-type that automatically generates sequential integer values.

---

### Difference between CHAR and VARCHAR?

| CHAR | VARCHAR |
|------|----------|
| Fixed length | Variable length |
| Pads extra spaces | Stores only actual characters |
| Wastes space | Saves space |

---

### What is a constraint?

A rule applied to columns to maintain data integrity.

---

### Difference between PRIMARY KEY and NOT NULL?

| PRIMARY KEY | NOT NULL |
|-------------|----------|
| Unique | Can contain duplicates |
| Cannot be NULL | Cannot be NULL |
| One per table | Multiple allowed |

---

### What does NUMERIC(10,2) mean?

- 10 total digits
- 2 digits after the decimal

---

# Quick Revision

- Table → Stores data
- CREATE TABLE → Creates a table
- SERIAL → Auto Increment
- VARCHAR → Variable-length text
- DATE → Stores dates
- NUMERIC → Exact decimal values
- PRIMARY KEY → Unique + NOT NULL
- NOT NULL → Mandatory value
- `SELECT *` → View all data
- `\d table_name` → View table structure
---
# Chapter 1: Creating Tables, Data Types & Constraints (PostgreSQL)

## What is a Table?

A **table** is a database object used to store related data in **rows and columns**.

Example:
- Employee
- Student
- Product

---

# CREATE TABLE

Used to create a new table in the database.

## Syntax

```sql
CREATE TABLE table_name (
    column_name data_type constraint,
    ...
);
```

## Example

```sql
CREATE TABLE employee(
    employee_id SERIAL PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    position VARCHAR(30) NOT NULL,
    department VARCHAR(20),
    hire_date DATE,
    salary NUMERIC(10,2)
);
```

---

# Data Types

A **data type** defines what kind of values a column can store.

| Data Type | Purpose |
|-----------|---------|
| SERIAL | Auto-increment integer (PostgreSQL) |
| VARCHAR(n) | Variable-length text (maximum n characters) |
| DATE | Stores dates |
| NUMERIC(p,s) | Stores exact decimal numbers |

## SERIAL

Automatically generates sequential integer values.

```sql
employee_id SERIAL
```

Equivalent to:

- Integer column
- Sequence
- Auto Increment

---

## VARCHAR(n)

Stores text up to **n characters**.

```sql
name VARCHAR(50)
```

- 10 characters → Stored
- 30 characters → Stored
- 51 characters → Error

Unlike `CHAR`, it **does not reserve the full space**.

---

## DATE

Stores only date values.

```sql
hire_date DATE
```

Example

```
2026-07-20
```

---

## NUMERIC(p,s)

Stores exact decimal values.

```sql
salary NUMERIC(10,2)
```

Where

- p = Total digits
- s = Digits after decimal

Example

```
99999999.99 ✔
100000000.00 ❌
```

---

# Constraints

Constraints are rules that maintain data integrity.

## PRIMARY KEY

- Unique
- Cannot be NULL
- One per table

```sql
employee_id SERIAL PRIMARY KEY
```

---

## NOT NULL

Ensures a value must always be provided.

```sql
name VARCHAR(50) NOT NULL
```

---

# Viewing Data

Display all rows

```sql
SELECT * FROM employee;
```

Display table structure (PostgreSQL)

```sql
\d employee
```

---

# Best Practices

- Use **snake_case** (`employee_name`)
- Always create a Primary Key
- Use `NOT NULL` for mandatory columns
- Choose appropriate data types
- Don't use unnecessarily large `VARCHAR` sizes

---

# Interview Questions

### What is a table?

A collection of related data stored in rows and columns.

---

### What is SERIAL?

A PostgreSQL pseudo-type that automatically generates sequential integer values.

---

### Difference between CHAR and VARCHAR?

| CHAR | VARCHAR |
|------|----------|
| Fixed length | Variable length |
| Pads extra spaces | Stores only actual characters |
| Wastes space | Saves space |

---

### What is a constraint?

A rule applied to columns to maintain data integrity.

---

### Difference between PRIMARY KEY and NOT NULL?

| PRIMARY KEY | NOT NULL |
|-------------|----------|
| Unique | Can contain duplicates |
| Cannot be NULL | Cannot be NULL |
| One per table | Multiple allowed |

---

### What does NUMERIC(10,2) mean?

- 10 total digits
- 2 digits after the decimal

---

# Quick Revision

- Table → Stores data
- CREATE TABLE → Creates a table
- SERIAL → Auto Increment
- VARCHAR → Variable-length text
- DATE → Stores dates
- NUMERIC → Exact decimal values
- PRIMARY KEY → Unique + NOT NULL
- NOT NULL → Mandatory value
- `SELECT *` → View all data
- `\d table_name` → View table structure