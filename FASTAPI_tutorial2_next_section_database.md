# 🗄️ FastAPI Tutorial - Section 4: Database (PostgreSQL & pgAdmin)

<div align="center">

**PostgreSQL Database Setup - A Hands-On Step-by-Step Guide**

*Learn Database Management with pgAdmin 4 for Your FastAPI Application*

![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16+-336791?style=flat-square&logo=postgresql)
![pgAdmin](https://img.shields.io/badge/pgAdmin-4-FF6600?style=flat-square&logo=pgadmin)
![FastAPI](https://img.shields.io/badge/FastAPI-0.95+-00C7B7?style=flat-square&logo=fastapi)
![Python](https://img.shields.io/badge/Python-3.7+-3776AB?style=flat-square&logo=python)

</div>

---

## 📚 Course Information

| Property | Details |
|----------|---------|
| **Source** | [FreeCodeCamp FastAPI Course](https://www.youtube.com/watch?v=0sOvCWBance) |
| **Section** | Section 4 - Database |
| **Previous Section** | [FastAPI_tutorial.md](./FastAPI_tutorial.md) - Topics 1–15 |
| **Compiled By** | Personal Learning Notes |
| **Learning Style** | Hands-on, Step-by-step |

> 📌 **Note**: This guide documents the pgAdmin 4 workflow for PostgreSQL database management as used in the FreeCodeCamp FastAPI course. Personal insights and gotchas are included throughout.

---

## 📖 Table of Contents

<table>
<tr>
<th>Topic Number</th>
<th>Topic Title</th>
<th>Difficulty</th>
<th>Time</th>
</tr>
<tr>
<td>1️⃣</td>
<td><a href="#1-connecting-to-the-server-in-pgadmin">Connect to Server in pgAdmin</a></td>
<td>🟢 Beginner</td>
<td>5 min</td>
</tr>
<tr>
<td>2️⃣</td>
<td><a href="#2-creating-a-database">Creating a Database</a></td>
<td>🟢 Beginner</td>
<td>5 min</td>
</tr>
<tr>
<td>3️⃣</td>
<td><a href="#3-creating-a-table-under-schemas">Creating a Table Under Schemas</a></td>
<td>🟡 Intermediate</td>
<td>10 min</td>
</tr>
<tr>
<td>4️⃣</td>
<td><a href="#4-adding-columns-via-properties">Adding Columns via Properties</a></td>
<td>🟡 Intermediate</td>
<td>10 min</td>
</tr>
<tr>
<td>5️⃣</td>
<td><a href="#5-viewing-and-editing-data-viewedit-data">Viewing & Editing Data (View/Edit)</a></td>
<td>🟡 Intermediate</td>
<td>8 min</td>
</tr>
<tr>
<td>6️⃣</td>
<td><a href="#6-sql-queries-from-query-tool">SQL Queries from Query Tool</a></td>
<td>🟡 Intermediate</td>
<td>20 min</td>
</tr>
</table>

---

## 1️⃣ Connecting to the Server in pgAdmin

### 🤔 What is pgAdmin?

**pgAdmin 4** is the official open-source GUI (Graphical User Interface) administration tool for PostgreSQL. It allows you to:
- 🔹 Manage PostgreSQL servers and databases visually
- 🔹 Create, edit, and delete databases and tables
- 🔹 Write and execute SQL queries
- 🔹 View and edit data without writing SQL
- 🔹 Monitor server performance

### 🚀 Step-by-Step: Connect to Server

**Step 1: Open pgAdmin 4**

```
Start Menu → Search "pgAdmin 4" → Open
```

> ℹ️ pgAdmin opens in your default web browser (localhost). It is a web-based app.

**Step 2: Locate the "Servers" Panel (Left Sidebar)**

```
Browser Panel (left side):
  └── Servers
        └── PostgreSQL 16  ← Click to expand
```

**Step 3: Connect to the Server**

```
Right-click on "PostgreSQL 16"
  → Select "Connect Server"
  → Enter your password (set during PostgreSQL installation)
  → Click "OK"
```

### ✅ How to Verify Connection

Once connected, the server icon changes to show a green plug symbol (**🔌**), and you can expand it to see:

```
PostgreSQL 16 (connected ✅)
  ├── Databases
  │     └── postgres  ← default database
  ├── Login/Group Roles
  └── Tablespaces
```

### 📌 Important Notes

- If pgAdmin asks for a **Master Password**, this is pgAdmin's own password (not PostgreSQL's)
- The **postgres** database shown is the default system database — don't delete it
- Default port is **5432**, default user is **postgres**

---

## 2️⃣ Creating a Database

### 🎯 Why Create a New Database?

Instead of using the default `postgres` database, you should **create a dedicated database** for your FastAPI application. This keeps things organized and isolated.

```
Rule of thumb:
  One Project = One Database
```

### 🔧 Step-by-Step: Create a Database

**Step 1: Right-click on "Databases"**

```
Servers
  └── PostgreSQL 16
        └── Databases  ← Right-click here
              → Create
              → Database...
```

**Step 2: Fill in Database Properties**

In the **Create - Database** dialog:

| Field | Value | Notes |
|-------|-------|-------|
| **Database** | `fastapi_db` | Your database name |
| **Owner** | `postgres` | Default superuser |
| **Encoding** | `UTF8` | Supports all characters |
| **Template** | `template1` | Default template |
| **Tablespace** | `pg_default` | Default storage |

**Step 3: Save the Database**

```
Click "Save" button
```

### ✅ Result

Your new database appears in the left panel:

```
Databases
  ├── fastapi_db  ← New database! ✅
  └── postgres    ← System default
```

### 📋 Naming Conventions

```
✅ Good names:
   fastapi_db
   fastapi_project
   myapp_database

❌ Bad names:
   db 1        (spaces not allowed)
   my-database (hyphens cause issues)
   DATABASE    (avoid all caps)
```

---

## 3️⃣ Creating a Table Under Schemas

### 🗂️ Understanding the Schema Structure

Before creating a table, understand the hierarchy:

```
Database (fastapi_db)
  └── Schemas
        └── public  ← Default schema
              ├── Tables        ← We create tables here
              ├── Views
              ├── Functions
              └── Sequences
```

> 📌 **Schema** = A namespace/container inside a database. The `public` schema is the default and is where you'll create your tables.

### 🔧 Step-by-Step: Create a Table

**Step 1: Navigate to Tables**

```
fastapi_db
  └── Schemas
        └── public
              └── Tables  ← Right-click here
                    → Create
                    → Table...
```

**Step 2: Fill in Table Properties**

In the **Create - Table** dialog, go to the **General** tab:

| Field | Value | Notes |
|-------|-------|-------|
| **Name** | `posts` | Table name |
| **Owner** | `postgres` | Default owner |
| **Schema** | `public` | Default schema |
| **Tablespace** | `pg_default` | Default |
| **Comment** | `Stores blog posts` | Optional description |

**Step 3: Save the Table**

```
Click "Save"
```

### ✅ Result

```
Tables
  └── posts  ← New table created! ✅
```

> ⚠️ **Note**: The table is empty with no columns yet. You will add columns in the next step.

---

## 4️⃣ Adding Columns via Properties

### 🎯 What are Columns?

Columns define the **structure** of your table — what kind of data each row will store.

```
posts table:
  ┌──────┬───────────────┬─────────────────┬───────────┬───────────────────┐
  │  id  │     title     │     content     │ published │    created_at     │
  ├──────┼───────────────┼─────────────────┼───────────┼───────────────────┤
  │  1   │ First Post    │ Hello World     │   true    │ 2025-01-01 10:00  │
  │  2   │ Second Post   │ FastAPI is cool │   false   │ 2025-01-02 12:30  │
  └──────┴───────────────┴─────────────────┴───────────┴───────────────────┘
```

### 🔧 Step-by-Step: Add Columns via Properties

**Step 1: Open Table Properties**

```
Tables
  └── posts  ← Right-click
              → Properties
```

**Step 2: Navigate to Columns Tab**

In the **posts - Table** dialog:

```
Tabs: General | Columns | Constraints | Advanced | Parameters | Security | SQL
                 ↑
           Click "Columns"
```

**Step 3: Add Each Column**

Click the **➕ (Add)** button to add a column. Fill in the details:

#### Column 1: `id` (Primary Key)

| Field | Value |
|-------|-------|
| **Name** | `id` |
| **Data type** | `integer` |
| **Not NULL?** | ✅ Yes |
| **Primary key?** | ✅ Yes |

> 💡 Tip: Use `SERIAL` (auto-increment integer) or `BIGSERIAL` as the data type so IDs are assigned automatically.

#### Column 2: `title`

| Field | Value |
|-------|-------|
| **Name** | `title` |
| **Data type** | `character varying` (VARCHAR) |
| **Length** | `255` |
| **Not NULL?** | ✅ Yes |

#### Column 3: `content`

| Field | Value |
|-------|-------|
| **Name** | `content` |
| **Data type** | `text` |
| **Not NULL?** | ✅ Yes |

#### Column 4: `published`

| Field | Value |
|-------|-------|
| **Name** | `published` |
| **Data type** | `boolean` |
| **Not NULL?** | ✅ Yes |
| **Default value** | `true` |

#### Column 5: `created_at`

| Field | Value |
|-------|-------|
| **Name** | `created_at` |
| **Data type** | `timestamp with time zone` |
| **Not NULL?** | ✅ Yes |
| **Default value** | `now()` |

**Step 4: Save Columns**

```
Click "Save"
```

### 📊 Data Types Quick Reference

| PostgreSQL Type | Python Equivalent | Use Case |
|----------------|-------------------|----------|
| `SERIAL` / `BIGSERIAL` | `int` (auto) | Auto-increment ID |
| `INTEGER` | `int` | Whole numbers |
| `VARCHAR(n)` | `str` | Short text with limit |
| `TEXT` | `str` | Long, unlimited text |
| `BOOLEAN` | `bool` | True / False |
| `TIMESTAMP WITH TIME ZONE` | `datetime` | Date and time |
| `NUMERIC` / `DECIMAL` | `float` | Precise decimals |

### ✅ Final Table Structure

After adding all columns, your table looks like:

```sql
posts
  ├── id          → SERIAL         NOT NULL  (Primary Key ✅)
  ├── title       → VARCHAR(255)   NOT NULL
  ├── content     → TEXT           NOT NULL
  ├── published   → BOOLEAN        NOT NULL  DEFAULT true
  └── created_at  → TIMESTAMPTZ    NOT NULL  DEFAULT now()
```

### 📌 Important Notes

- **Primary Key** guarantees every row has a unique identifier
- **NOT NULL** means the field is required — it cannot be left empty
- **DEFAULT** values are automatically inserted if not provided
- `SERIAL` = PostgreSQL's auto-incrementing integer (like `id` in Django models)

---

## 5️⃣ Viewing and Editing Data (View/Edit Data)

### 🎯 What is View/Edit Data?

pgAdmin's **View/Edit Data** feature lets you:
- 👁️ See all records in the table
- ✏️ Add new rows manually (without writing SQL)
- 🗑️ Delete rows
- 📝 Edit existing records directly

### 🔧 Step-by-Step: Open View/Edit Data

**Step 1: Right-click on the Table**

```
Tables
  └── posts  ← Right-click
              → View/Edit Data
              → All Rows         ← Click this
```

**Step 2: The Data Grid Opens**

You'll see a spreadsheet-like interface:

```
┌──────┬──────────────┬─────────────────┬───────────┬──────────────────────┐
│  id  │    title     │     content     │ published │      created_at      │
├──────┼──────────────┼─────────────────┼───────────┼──────────────────────┤
│ (empty table — no rows yet)                                               │
└──────┴──────────────┴─────────────────┴───────────┴──────────────────────┘
```

### ✏️ Adding Data Manually

**Step 1: Click the empty row at the bottom**

```
Click the blank row → Start typing
```

**Step 2: Fill in each column**

| Column | Example Value |
|--------|---------------|
| `title` | `My First Post` |
| `content` | `This is my first FastAPI post!` |
| `published` | `true` |
| `created_at` | *(leave blank — uses `now()` default)* |

> ⚠️ **Note**: The `id` column is auto-filled by `SERIAL` — **don't type anything into it**.

**Step 3: Save the Data**

```
Click the 💾 Save (disk icon) button
OR
Press F6
```

### ✅ Result

```
┌────┬────────────────┬──────────────────────────────────┬───────────┬─────────────────────────┐
│ id │     title      │            content               │ published │       created_at        │
├────┼────────────────┼──────────────────────────────────┼───────────┼─────────────────────────┤
│  1 │ My First Post  │ This is my first FastAPI post!   │   true    │ 2025-12-25 10:30:00+06  │
└────┴────────────────┴──────────────────────────────────┴───────────┴─────────────────────────┘
```

### 📌 Key Notes

- `id` is assigned **automatically** — never set it manually
- `created_at` uses `now()` default — PostgreSQL fills it automatically
- You can edit any cell by **double-clicking** it
- Changes are **not saved** until you click the Save button

---

## 6️⃣ SQL Queries from Query Tool

### 🎯 What is the Query Tool?

The **Query Tool** is a SQL editor built into pgAdmin. It lets you:
- 📝 Write raw SQL commands
- ⚡ Execute queries instantly
- 📊 View results in a table
- 💾 Save query history

### 🔧 Step-by-Step: Open Query Tool

**Method 1: From the Database**

```
fastapi_db  ← Right-click (or click)
  → Query Tool
```

**Method 2: From the Menu Bar**

```
Top Menu → Tools → Query Tool
```

**Method 3: Keyboard Shortcut**

```
Alt + Shift + Q
```

---

### 🧪 Essential SQL Operations

#### ➕ INSERT — Add New Records

```sql
-- Insert a single post
INSERT INTO posts (title, content, published)
VALUES ('My First Post', 'This is my first FastAPI post!', true);

-- Insert multiple posts at once
INSERT INTO posts (title, content, published)
VALUES 
    ('Post Two', 'Content of post two', true),
    ('Post Three', 'Content of post three', false),
    ('Draft Post', 'This is a draft', false);
```

> 💡 We don't insert `id` and `created_at` because they have defaults (`SERIAL` and `now()`)

---

#### 🔍 SELECT — Read / Retrieve Records

```sql
-- Get ALL columns from ALL rows
SELECT * FROM posts;

-- Get specific columns only
SELECT id, title, published FROM posts;

-- Filter rows with WHERE
SELECT * FROM posts WHERE published = true;

-- Get a single post by ID
SELECT * FROM posts WHERE id = 1;

-- Sort by newest first
SELECT * FROM posts ORDER BY created_at DESC;

-- Limit results (pagination)
SELECT * FROM posts LIMIT 5;

-- Limit with offset (page 2, 5 per page)
SELECT * FROM posts LIMIT 5 OFFSET 5;
```

---

#### ✏️ UPDATE — Modify Existing Records

```sql
-- Update a specific post's title
UPDATE posts
SET title = 'Updated Title'
WHERE id = 1;

-- Update multiple columns at once
UPDATE posts
SET title = 'New Title', published = false
WHERE id = 2;

-- Publish all draft posts
UPDATE posts
SET published = true
WHERE published = false;
```

> ⚠️ **DANGER**: Always use `WHERE` with UPDATE! Without it, ALL rows are updated!

```sql
-- ❌ NEVER DO THIS (updates ALL rows!)
UPDATE posts SET published = false;

-- ✅ Always target specific rows
UPDATE posts SET published = false WHERE id = 3;
```

---

#### 🗑️ DELETE — Remove Records

```sql
-- Delete a specific post
DELETE FROM posts WHERE id = 3;

-- Delete all unpublished posts
DELETE FROM posts WHERE published = false;
```

> ⚠️ **DANGER**: Always use `WHERE` with DELETE! Without it, ALL rows are deleted!

```sql
-- ❌ NEVER DO THIS (deletes ALL rows!)
DELETE FROM posts;

-- ✅ Always target specific rows
DELETE FROM posts WHERE id = 3;
```

---

#### 🔢 COUNT, ORDER, FILTER

```sql
-- Count total posts
SELECT COUNT(*) FROM posts;

-- Count only published posts
SELECT COUNT(*) FROM posts WHERE published = true;

-- Get posts ordered alphabetically by title
SELECT * FROM posts ORDER BY title ASC;

-- Search posts containing a keyword (LIKE)
SELECT * FROM posts WHERE title LIKE '%FastAPI%';

-- Case-insensitive search (ILIKE)
SELECT * FROM posts WHERE title ILIKE '%fastapi%';
```

---

### ▶️ How to Run a Query

**Step 1: Type your SQL in the editor**

```sql
SELECT * FROM posts;
```

**Step 2: Run the query**

```
Click the ▶️ "Execute/Run" button
OR
Press F5
```

**Step 3: View results**

Results appear in the **Data Output** panel below the editor:

```
Query returned successfully: 3 rows affected, 12 ms execution time.

 id │     title      │ content         │ published │       created_at
────┼────────────────┼─────────────────┼───────────┼────────────────────────
  1 │ My First Post  │ Hello World!    │ t         │ 2025-12-25 10:00:00+06
  2 │ Post Two       │ Content here    │ t         │ 2025-12-25 10:01:00+06
  3 │ Draft Post     │ This is draft   │ f         │ 2025-12-25 10:02:00+06
```

---

### 📊 CRUD Operations Summary

| Operation | SQL Command | HTTP Method (FastAPI) |
|-----------|-------------|----------------------|
| **Create** | `INSERT INTO` | `POST` |
| **Read All** | `SELECT * FROM` | `GET /posts` |
| **Read One** | `SELECT * FROM WHERE id = ?` | `GET /posts/{id}` |
| **Update** | `UPDATE SET WHERE` | `PUT /posts/{id}` |
| **Delete** | `DELETE FROM WHERE` | `DELETE /posts/{id}` |

---

### 🔧 Useful pgAdmin Query Tool Shortcuts

| Shortcut | Action |
|----------|--------|
| `F5` | Execute / Run Query |
| `F6` | Save Data (in View/Edit) |
| `Ctrl + S` | Save Query File |
| `Ctrl + Z` | Undo |
| `Ctrl + /` | Comment/Uncomment Line |
| `Ctrl + Space` | Auto-complete |
| `F7` | Open Explain Plan |

---

## 📌 Quick Reference

### Full Workflow Summary

```
1. Open pgAdmin 4
        ↓
2. Connect to Server (PostgreSQL 16)
        ↓
3. Create Database (fastapi_db)
        ↓
4. Navigate: fastapi_db → Schemas → public → Tables
        ↓
5. Right-click Tables → Create → Table → Name it "posts"
        ↓
6. Right-click "posts" → Properties → Columns Tab → Add Columns
        ↓
7. Right-click "posts" → View/Edit Data → All Rows → Add rows manually
        ↓
8. Open Query Tool → Write SQL → Press F5 to Run
        ↓
✅ Database is ready!
```

### Essential SQL Commands

```sql
-- SELECT
SELECT * FROM posts;
SELECT * FROM posts WHERE id = 1;

-- INSERT
INSERT INTO posts (title, content, published) VALUES ('Title', 'Content', true);

-- UPDATE
UPDATE posts SET title = 'New Title' WHERE id = 1;

-- DELETE
DELETE FROM posts WHERE id = 1;

-- COUNT
SELECT COUNT(*) FROM posts;
```

---

## 🔗 Useful Links

<table>
<tr>
<th>Resource</th>
<th>Link</th>
<th>Purpose</th>
</tr>
<tr>
<td>PostgreSQL Official</td>
<td><a href="https://www.postgresql.org/">postgresql.org</a></td>
<td>Database system</td>
</tr>
<tr>
<td>pgAdmin Official</td>
<td><a href="https://www.pgadmin.org/">pgadmin.org</a></td>
<td>GUI for PostgreSQL</td>
</tr>
<tr>
<td>PostgreSQL Data Types</td>
<td><a href="https://www.postgresql.org/docs/current/datatype.html">PostgreSQL Docs - Data Types</a></td>
<td>All available column types</td>
</tr>
<tr>
<td>SQL Tutorial</td>
<td><a href="https://www.w3schools.com/sql/">w3schools.com/sql</a></td>
<td>SQL basics reference</td>
</tr>
<tr>
<td>FastAPI SQL Databases</td>
<td><a href="https://fastapi.tiangolo.com/tutorial/sql-databases/">fastapi.tiangolo.com/tutorial/sql-databases</a></td>
<td>Official FastAPI + DB guide</td>
</tr>
</table>

---

## 🎓 Learning Path Checklist

- [ ] Topic 1: Connect to Server in pgAdmin ✅
- [ ] Topic 2: Create a Database ✅
- [ ] Topic 3: Create a Table under Schemas ✅
- [ ] Topic 4: Add Columns via Properties ✅
- [ ] Topic 5: View/Edit Data — Add rows manually ✅
- [ ] Topic 6: SQL Queries from Query Tool ✅

---

## 💪 Next Steps

After completing this section, you're ready for:

1. **Connecting FastAPI to PostgreSQL** — Using `psycopg2` driver
2. **SQLAlchemy ORM** — Python-based database interaction
3. **Database Models** — Defining tables in Python code
4. **CRUD with Database** — Real persistent data instead of in-memory list
5. **Database Migrations** — Using `Alembic` to manage schema changes

---

> 📝 **Note**: More content will be added as the course progresses.

<div align="center">

---

### 🌟 Section 4: Database — Complete! 🌟

**Generated**: February 2026  
**Course**: FreeCodeCamp FastAPI Tutorial (15 hours)  
**Section**: 4 - PostgreSQL & pgAdmin

---

## 7️⃣ Advanced SQL Queries

### 🧠 Deep Dive into SQL Operations

Now we move onto more advanced querying beyond basic CRUD.

#### 🎯 Filtering Data (WHERE and Operators)

**1. Greater Than / Less Than**
```sql
-- Find all products priced strictly over 80
SELECT * FROM product WHERE price > 80;

-- (Other comparisons: <, >=, <=)
```

**2. Not Equal (!= or <>)**
```sql
-- Both mean the same thing: find products that DO NOT have 0 inventory (meaning they are in stock)
SELECT * FROM product WHERE inventory != 0;
SELECT * FROM product WHERE inventory <> 0;
```

**3. Multiple Conditions (AND / OR)**
```sql
-- AND: Both conditions must be true
-- Find products in stock AND costing more than 10
SELECT * FROM product WHERE inventory > 0 AND price > 10;

-- OR: At least one condition must be true
-- Find products that are either in stock OR cost more than 10
SELECT * FROM product WHERE inventory > 0 OR price > 10;

-- Find cheap products (<20) or very expensive ones (>100)
SELECT * FROM product WHERE Price < 20 OR price > 100;
```

**4. The IN Operator**
```sql
-- The long way (repetitive and prone to typos):
SELECT * FROM product WHERE id = 1 OR id = 2 OR id = 3;

-- The better way using IN:
-- Checks if the value matches any value in the provided list
SELECT * FROM product WHERE id IN (1, 2, 3);
```

---

#### 🔍 Pattern Matching (LIKE)

The `LIKE` operator is used to search for a specified pattern in a column.
- `%` represents zero, one, or multiple characters.
- `_` represents a single character (not used in examples but good to know).

```sql
-- Find products whose name starts with 'TV'
SELECT * FROM product WHERE name LIKE 'TV%';

-- Find products whose name ends with 'e'
SELECT * FROM product WHERE name LIKE '%e';

-- Find products whose name DOES NOT end with 'e'
SELECT * FROM product WHERE name NOT LIKE '%e';

-- Find products containing the letters 'en' anywhere in the name
SELECT * FROM product WHERE name LIKE '%en%';
```

---

#### ↕️ Sorting Data (ORDER BY)

```sql
-- Sort by price ascending (default behavior)
SELECT * FROM product ORDER BY price;
-- Explicitly stating ASC (Ascending: smallest to largest)
SELECT * FROM product ORDER BY price ASC;

-- Sort by price descending (DESC: largest to smallest)
SELECT * FROM product ORDER BY price DESC;

-- Sort by inventory highest to lowest
SELECT * FROM product ORDER BY inventory DESC;

-- Multiple Column Sorting (Tie-breakers)
-- If two items have the SAME inventory, THEN sort those by price ascending
SELECT * FROM product ORDER BY inventory DESC, price;

-- Three-level sorting
SELECT * FROM product ORDER BY inventory DESC, price ASC, created_at ASC;

-- Combining WHERE with ORDER BY
-- Always put WHERE before ORDER BY!
SELECT * FROM product WHERE price > 20 ORDER BY created_at DESC;
```

---

#### ✂️ Limiting Results (LIMIT and OFFSET)

Used primarily for **Pagination** (e.g., "Page 1 of 10").

```sql
-- Only return the first 5 records
SELECT * FROM product LIMIT 5;

-- Combine WHERE with LIMIT (Get exactly 5 expensive products)
SELECT * FROM product WHERE price > 10 LIMIT 5;

-- Get the first 5 products by ID
SELECT * FROM product ORDER BY id LIMIT 5;

-- Pagination example: Skip the first 2 records, then take the next 5
-- Use case: "Page 2" where each page shows 5 items, and we skipped the first 2 from page 1.
SELECT * FROM product ORDER BY id LIMIT 5 OFFSET 2;
```

---

#### ➕ Advanced INSERT

```sql
-- Basic Insert (specifying columns is best practice)
INSERT INTO product (name, price, inventory) 
VALUES ('tortilla', 4, 1000);

-- Bulk Insert & RETURNING clause
-- RETURNING * tells PostgreSQL to immediately send back the data it just inserted
-- This is incredibly useful in FastAPI so you get the auto-generated ID back immediately!
INSERT INTO product (price, name, inventory) 
VALUES
    (10000, 'car', 1000), 
    (50, 'laptop', 25),
    (60, 'monitor', 50) 
RETURNING *;
```

---

#### ✏️ Advanced UPDATE

```sql
-- Update multiple specific columns for a specific row
UPDATE product 
SET name = 'flour tortilla', price = 40  
WHERE id = 21;

-- Update and immediately return the updated data using RETURNING *
-- Very useful in APIs so you don't have to do a second SELECT query to get the updated row
UPDATE product 
SET is_sale = true 
WHERE id = 27 
RETURNING *;
```

---

#### 🗑️ Advanced DELETE

```sql
-- Delete a specific row
DELETE FROM product WHERE id = 25;

-- (Optional but helpful: you can also use RETURNING * with DELETE 
-- to get the data of the row you just removed)
DELETE FROM product WHERE id = 25 RETURNING *;
```

---

### 📊 CRUD Operations Summary

| Operation | SQL Command | HTTP Method (FastAPI) |
|-----------|-------------|----------------------|
| **Create** | `INSERT INTO` | `POST` |
| **Read All** | `SELECT * FROM` | `GET /posts` |
| **Read One** | `SELECT * FROM WHERE id = ?` | `GET /posts/{id}` |
| **Update** | `UPDATE SET WHERE` | `PUT /posts/{id}` |
| **Delete** | `DELETE FROM WHERE` | `DELETE /posts/{id}` |

---

### 🔧 Useful pgAdmin Query Tool Shortcuts

| Shortcut | Action |
|----------|--------|
| `F5` | Execute / Run Query |
| `F6` | Save Data (in View/Edit) |
| `Ctrl + S` | Save Query File |
| `Ctrl + Z` | Undo |
| `Ctrl + /` | Comment/Uncomment Line |
| `Ctrl + Space` | Auto-complete |
| `F7` | Open Explain Plan |

---

## 📌 Quick Reference

### Full Workflow Summary

```
1. Open pgAdmin 4
        ↓
2. Connect to Server (PostgreSQL 16)
        ↓
3. Create Database (fastapi_db)
        ↓
4. Navigate: fastapi_db → Schemas → public → Tables
        ↓
5. Right-click Tables → Create → Table → Name it "posts"
        ↓
6. Right-click "posts" → Properties → Columns Tab → Add Columns
        ↓
7. Right-click "posts" → View/Edit Data → All Rows → Add rows manually
        ↓
8. Open Query Tool → Write SQL → Press F5 to Run
        ↓
✅ Database is ready!
```

### Essential SQL Commands

```sql
-- SELECT
SELECT * FROM posts;
SELECT * FROM posts WHERE id = 1;

-- INSERT
INSERT INTO posts (title, content, published) VALUES ('Title', 'Content', true);

-- UPDATE
UPDATE posts SET title = 'New Title' WHERE id = 1;

-- DELETE
DELETE FROM posts WHERE id = 1;

-- COUNT
SELECT COUNT(*) FROM posts;
```

---

## 🐍 Section 5: Python + Raw SQL

### 8️⃣ Installing and Connecting Psycopg

#### 📦 What is Psycopg?

**Psycopg** is the most popular PostgreSQL database adapter for the Python programming language. It allows your Python code (and FastAPI) to talk directly to your PostgreSQL database.

**Fun fact:** There is no `psycopg3` package name to install, the package is just called `psycopg`! The older version was `psycopg2`.

#### 💻 Installation

```bash
# Update pip first
pip install --upgrade pip

# Install psycopg with binary dependencies (easier installation)
pip install "psycopg[binary]"

# Verify installation
pip freeze
```

#### 🔌 Connecting to the Database

You want your API to retrieve rows as **Python Dictionaries** (like JSON) rather than a list of tuples. 

**For Psycopg 2 (Older way):**
```python
import psycopg2
from psycopg2.extras import RealDictCursor

conn = psycopg2.connect(
    host="localhost",
    port="5432",
    dbname="your_db",
    user="postgres",
    password="your_password"
)

# Use RealDictCursor to get dictionary-like rows
cur = conn.cursor(cursor_factory=RealDictCursor)

cur.execute("SELECT id, name FROM users;")
rows = cur.fetchall()

for row in rows:
    print(row) # This prints a dictionary!

# ALWAYS close connections to prevent memory/connection leaks
cur.close()
conn.close()
```

**For Psycopg 3 (Modern way - WE ARE USING THIS!):**
```python
import psycopg
from psycopg.rows import dict_row
import time

# Best Practice: Try to connect in a loop when the app starts
while True:
    try:
        conn = psycopg.connect(
            host="localhost",
            port="5432", # Change if your port is 5433
            dbname="Fastapi",
            user="postgres",
            password="your_password"
        )
        # Use row_factory to make all results behave like dictionaries
        conn.row_factory = dict_row
        cursor = conn.cursor()
        print("Database connection successful")
        break
    except Exception as error:
        print("Database connection failed")
        print("Error: ", error)
        time.sleep(2) # Wait 2 seconds before retrying
```

#### 📌 Psycopg Key Notes
1. `RealDictCursor` is from `psycopg2.extras` (older version).
2. In `psycopg3`, use `conn.row_factory = dict_row` instead.
3. Using these methods lets you access query results easily: `row['id']`, `row['name']`.
4. **Always close** cursors and connections to prevent database connection leaks!

---

### 9️⃣ FastAPI Raw SQL CRUD Endpoints

Here is how we integrate our `cursor` directly into FastAPI routes.

#### 📖 Read All Posts (GET)
```python
@app.get("/posts")
def get_posts():
    cursor.execute("""SELECT * FROM posts""")
    posts = cursor.fetchall()
    
    # Returns the list of dictionaries straight to the user
    return {"data": posts}
```

#### 📖 Read One Post (GET)
Notice the `(str(id),)` syntax. When passing variables into psycopg `execute()`, it requires a tuple. Even if there is only one variable, you must write `(value,)` with a trailing comma.

```python
from fastapi import Response, status, HTTPException

@app.get("/posts/{id}")
def get_post(id: int):
    # Pass the ID as a parameter to prevent SQL injection
    cursor.execute("""SELECT * FROM posts WHERE id = %s""", (str(id),))
    post = cursor.fetchone()
    
    if not post:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND, 
            detail=f"post with id {id} not found"
        )
        
    return {"post_detail": post}
```

#### ➕ Create a Post (POST)
Whenever you modify the database (`INSERT`, `UPDATE`, `DELETE`), you must run `conn.commit()` to save the changes permanently!

```python
@app.post("/posts", status_code=status.HTTP_201_CREATED)
def create_posts(post: Post):
    # Use RETURNING * to immediately get the auto-generated ID back
    cursor.execute("""
        INSERT INTO posts(title, content, published)
        VALUES(%s, %s, %s) 
        RETURNING *
        """,
        (post.title, post.content, post.published)
    )
    
    new_post = cursor.fetchone()
    
    # IMPORTANT: Save changes to the database!
    conn.commit()
    
    return {"data": new_post}
```

#### ✏️ Update a Post (PUT)
```python
@app.put("/posts/{id}")
def update_post(id: int, post: Post):
    cursor.execute("""
        UPDATE posts 
        SET title = %s, content = %s, published = %s 
        WHERE id = %s 
        RETURNING *
        """,
        (post.title, post.content, post.published, str(id))
    )
    
    updated_post = cursor.fetchone()
    conn.commit()

    if updated_post is None:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail=f"post with id {id} not found"
        )
    
    return {"data": updated_post}
```

#### 🗑️ Delete a Post (DELETE)
```python
@app.delete("/posts/{id}", status_code=status.HTTP_204_NO_CONTENT)
def delete_post(id: int):
    cursor.execute("""DELETE FROM posts WHERE id = %s RETURNING *""", (str(id),))
    deleted_post = cursor.fetchone()
    conn.commit()
    
    if deleted_post is None:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail=f"post with id {id} not found"
        )

    # 204 No Content shouldn't return a JSON body
    return Response(status_code=status.HTTP_204_NO_CONTENT)
```

---

### 🔟 SQL Injection & Best Practices

#### ❌ The Vulnerable Way (DO NOT USE)
```python
# 🚨 DANGER: SQL INJECTION VULNERABILITY! 🚨
# Using f-strings or .format() to insert user data directly into a query
cursor.execute(f"""
    INSERT INTO posts(title, content, published)
    VALUES('{post.title}', '{post.content}', {post.published}) 
    RETURNING *""")
```
**Why is this bad?** If a user passes `"'); DROP TABLE posts; --"` as their title, the database will literally execute that text and delete your entire table.

#### ✅ The Better Way (Parameterized Queries)
```python
# Safe from SQL injection!
cursor.execute("""
    INSERT INTO posts(title, content, published)
    VALUES(%s, %s, %s) 
    RETURNING *""",
    (post.title, post.content, post.published)
)
```
**Why is this better?** By separating the query text from the data tuple, the psycopg driver automatically sanitizes the inputs for you so they are treated strictly as text, never as executable code.

#### 🚀 The BEST Way (ORMs / Connection Pools)
While parameterized queries (`%s`) protect against SQL injection, writing raw SQL strings everywhere is **still not industry best practice.**

1. **Use an ORM (Object Relational Mapper) like SQLAlchemy.**
   - Provides a higher level of abstraction.
   - You interact with Python objects instead of raw SQL strings.
   - Refactoring is much easier, and you get IDE auto-complete.
2. **Use a Connection Pool.**
   - Instead of keeping exactly 1 connection open per app, a connection pool creates a small cache of connections.
   - Every time a user requests data, the app borrows a connection, uses it, and returns it. This handles thousands of concurrent users safely.

---

## 🔗 Useful Links

<table>
<tr>
<th>Resource</th>
<th>Link</th>
<th>Purpose</th>
</tr>
<tr>
<td>PostgreSQL Official</td>
<td><a href="https://www.postgresql.org/">postgresql.org</a></td>
<td>Database system</td>
</tr>
<tr>
<td>pgAdmin Official</td>
<td><a href="https://www.pgadmin.org/">pgadmin.org</a></td>
<td>GUI for PostgreSQL</td>
</tr>
<tr>
<td>Psycopg 3 Docs</td>
<td><a href="https://www.psycopg.org/psycopg3/docs/">psycopg.org/psycopg3/docs</a></td>
<td>Python PostgreSQL Driver</td>
</tr>
<tr>
<td>FastAPI SQL Databases</td>
<td><a href="https://fastapi.tiangolo.com/tutorial/sql-databases/">fastapi.tiangolo.com/tutorial/sql-databases</a></td>
<td>Official FastAPI + DB guide</td>
</tr>
</table>

---

## 🎓 Learning Path Checklist

- [ ] Topic 1: Connect to Server in pgAdmin ✅
- [ ] Topic 2: Create a Database ✅
- [ ] Topic 3: Create a Table under Schemas ✅
- [ ] Topic 4: Add Columns via Properties ✅
- [ ] Topic 5: View/Edit Data — Add rows manually ✅
- [ ] Topic 6: SQL Queries from Query Tool ✅
- [ ] Topic 7: Advanced SQL Queries ✅
- [ ] Topic 8: Installing and Connecting Psycopg ✅
- [ ] Topic 9: FastAPI Raw SQL CRUD Endpoints ✅
- [ ] Topic 10: SQL Injection & Best Practices ✅

---

## 💪 Next Steps

After completing this section, you're ready for:

1. **SQLAlchemy ORM** — Replacing Raw SQL with Python classes
2. **Database Models** — Defining tables via Pydantic + SQLAlchemy
3. **Database Migrations** — Using `Alembic` to manage schema changes

---

> 📝 **Note**: More content will be added as the course progresses. Share your additional notes and they will be appended to this document.

---

<div align="center">

---

### 🌟 Section 5: Python + Raw SQL — Complete! 🌟

**Generated**: February 2026  
**Course**: FreeCodeCamp FastAPI Tutorial (15 hours)  
**Section**: 5 - Psycopg & CRUD Endpoints

---

### ✨ *Keep Learning, Keep Building, Keep Growing!* ✨

---

**Happy Coding! 🚀**

</div>


### ✨ *Keep Learning, Keep Building, Keep Growing!* ✨


**Happy Coding! 🚀**

</div>
