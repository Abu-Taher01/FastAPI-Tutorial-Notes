# ⚙️ FastAPI Tutorial - Section 6: SQLAlchemy (ORM)

<div align="center">

**Mastering Databases with Object Relational Mapping**

*Transitioning from Raw SQL to Python Objects*

![FastAPI](https://img.shields.io/badge/FastAPI-0.95+-00C7B7?style=flat-square&logo=fastapi)
![Python](https://img.shields.io/badge/Python-3.7+-3776AB?style=flat-square&logo=python)
![SQLAlchemy](https://img.shields.io/badge/SQLAlchemy-2.0+-D71F00?style=flat-square&logo=sqlalchemy)

</div>

---

## 📚 Course Information

| Property | Details |
|----------|---------|
| **Source** | [FreeCodeCamp FastAPI Course](https://www.youtube.com/watch?v=0sOvCWBance) |
| **Section** | Section 6 - SQLAlchemy (ORM) |
| **Previous Sections** | [Database Setup](./FASTAPI_tutorial2_next_section_database.md) |
| **Compiled By** | Personal Learning Notes |

> 📌 **Note**: This section marks the transition from writing manual SQL queries (psycopg) to using an Object Relational Mapper (SQLAlchemy) to interact with Postgres natively through Python.

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
<td><a href="#1-what-is-an-orm-and-installation">What is an ORM & Installation</a></td>
<td>🟢 Beginner</td>
<td>5 min</td>
</tr>
<tr>
<td>2️⃣</td>
<td><a href="#2-database-connection-setup-databasepy">Database Connection Setup</a></td>
<td>🟡 Intermediate</td>
<td>10 min</td>
</tr>
<tr>
<td>3️⃣</td>
<td><a href="#3-creating-database-models-modelspy">Creating Models (Tables in Python)</a></td>
<td>🔴 Advanced</td>
<td>15 min</td>
</tr>
<tr>
<td>4️⃣</td>
<td><a href="#4-database-dependencies-in-fastapi">Database Dependencies (get_db)</a></td>
<td>🟡 Intermediate</td>
<td>10 min</td>
</tr>
<tr>
<td>5️⃣</td>
<td><a href="#5-sqlalchemy-crud-vs-raw-sql">SQLAlchemy CRUD vs Raw SQL</a></td>
<td>🔴 Advanced</td>
<td>25 min</td>
</tr>
<tr>
<td>6️⃣</td>
<td><a href="#6-is-this-the-standard-fastapi-way">Is this the Standard FastAPI Way?</a></td>
<td>🟢 Beginner</td>
<td>5 min</td>
</tr>
</table>

---

## 1️⃣ What is an ORM and Installation

### 🤔 What is an ORM?
**ORM** stands for **Object Relational Mapper**. It's a library that translates Python classes into database tables, and Python objects into database rows. 

**Why use it?**
- You write **Python code**, not SQL strings.
- You get **IDE auto-completion** and type checking.
- It prevents **SQL Injection** automatically.
- It makes refactoring drastically easier.

### 💻 Installation

```bash
# Install SQLAlchemy
pip install sqlalchemy
```

---

## 2️⃣ Database Connection Setup (`database.py`)

To keep our code clean, we create a dedicated `database.py` file inside our `app` folder.

### 📄 `app/database.py`
```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

# The connection string to your database
SQLALCHEMY_DATABASE_URL = "postgresql+psycopg://postgres:password@localhost:5433/Fastapi"
# ⚠️ WARNING: Hardcoding passwords like this is NOT best practice! 
# We cover environment variables later to hide passwords.

# The Engine is responsible for actually connecting to the database
engine = create_engine(SQLALCHEMY_DATABASE_URL)

# SessionLocal is what we use to talk to the database (run queries)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

# Base is a class we will use to create our Database Models
Base = declarative_base()
```

### 🔗 Connection String Formatting
Make sure your connection string uses the exact driver you installed:

```text
# General Format:
driver://user:password@hostname:port/database_name

# For modern psycopg (psycopg3 - WHAT WE USE):
postgresql+psycopg://postgres:password@localhost:5433/Fastapi

# For older psycopg2:
postgresql://postgres:password@localhost:5433/Fastapi
```

---

## 3️⃣ Creating Database Models (`models.py`)

A **Model** in SQLAlchemy represents a **Table** in your database.

### 📄 `app/models.py`
```python
from sqlalchemy import Boolean, Column, Integer, String
from sqlalchemy.sql.sqltypes import TIMESTAMP
from sqlalchemy.sql.expression import text
from .database import Base

class Post(Base):
    # This dictates the actual name of the table in PostgreSQL
    __tablename__ = "posts"
    
    # Each Column represents a column in the database table
    id = Column(Integer, primary_key=True, nullable=False, index=True)
    title = Column(String, nullable=False)
    content = Column(String, nullable=False)
    published = Column(Boolean, nullable=False, server_default='TRUE')
    created_at = Column(TIMESTAMP(timezone=True), nullable=False, server_default=text('now()'))
```

### ⚠️ A Major Limitation of SQLAlchemy `Base.metadata.create_all`
When you start your FastAPI app, SQLAlchemy can generate this table for you automatically. **HOWEVER:**
- It **CAN** create a table if it doesn't exist.
- It **CANNOT** update an existing table (e.g., if you add a new column to your python code, SQLAlchemy will **NOT** add it to Postgres).
- For making changes to existing tables (Realtime Migration), we must use a tool called **Alembic** later in the course.

---

## 4️⃣ Database Dependencies in FastAPI (`main.py`)

Now we need to wire our database into FastAPI.

### 📄 `app/main.py` (Setup)
```python
from fastapi import FastAPI, Depends
from sqlalchemy.orm import Session
from . import models
from .database import engine, SessionLocal

# This line tells SQLAlchemy to create all tables based on our models
# (Only works if the tables don't already exist!)
models.Base.metadata.create_all(bind=engine)

app = FastAPI()

# Database Dependency
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

### 🧠 Explaining the Dependency (`get_db`)
In FastAPI, a **Dependency** is a function that runs *before* your endpoint, provides something your endpoint needs, and cleans up afterwards.

1. `db = SessionLocal()`: Opens a connection to the database.
2. `yield db`: Pauses this function and hands the database connection to your API endpoint.
3. Your endpoint runs, queries the database, and finishes.
4. `finally: db.close()`: The dependency resumes and **closes** the connection. This prevents database connection leaks!

---

## 5️⃣ SQLAlchemy CRUD vs Raw SQL

Now we rewrite our routes using the `db: Session` object instead of a raw `cursor`.

### 📖 GET All Posts (Read)

**How to test queries to see what SQLAlchemy does:**
```python
@app.get("/sqlalchemy")
def test_posts(db: Session = Depends(get_db)):
    # If you print just the query, SQLAlchemy prints the RAW SQL it generated!
    posts = db.query(models.Post)
    print(posts) # Prints: "SELECT posts.id, posts.title ... FROM posts"
    
    # .all() tells SQLAlchemy to actually execute the query and fetch the results
    posts = db.query(models.Post).all()
    print(posts) # Prints: A list of Python Post objects
    
    return {"data": posts}
```

### ➕ POST (Create a Post)

```python
@app.post("/posts", status_code=status.HTTP_201_CREATED)
def create_posts(post: Post, db: Session = Depends(get_db)):
    
    # ❌ INEFFICIENT WAY: Manually typing each field
    # new_post = models.Post(title=post.title, content=post.content, published=post.published)
    
    # ✅ EFFICIENT WAY: Unpacking the Pydantic Dictionary
    # post.model_dump() converts Pydantic to a dict. ** unpacks the dict into kwargs!
    new_post = models.Post(**post.model_dump())
    
    # 1. Add it to the SQLAlchemy session
    db.add(new_post)
    # 2. Commit it to the database (saves it)
    db.commit()
    # 3. Retrieve the newly generated ID back into the new_post object
    db.refresh(new_post) 

    return {"data": new_post}
```
> **🧠 Concept:** `db.refresh(new_post)` is the SQLAlchemy equivalent of `RETURNING *` in raw SQL!

### 📖 GET One Post (Read)

```python
@app.get("/posts/{id}")
def get_post(id: int, db: Session = Depends(get_db)):
    # .filter() is the WHERE clause in SQL
    # .first() grabs the first result and stops searching (more efficient than .all())
    post = db.query(models.Post).filter(models.Post.id == id).first()
    
    if not post:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, 
                            detail=f"post with id {id} not found")
        
    return {"post_detail": post}
```

### 🗑️ DELETE a Post

```python
@app.delete("/posts/{id}", status_code=status.HTTP_204_NO_CONTENT)
def delete_post(id: int, db: Session = Depends(get_db)):
    # Build the query WITHOUT executing it yet
    post_query = db.query(models.Post).filter(models.Post.id == id)

    # Execute it to check if it exists
    if post_query.first() is None:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND,
                            detail=f"post with id {id} not found")
                            
    # Execute the actual deletion
    post_query.delete(synchronize_session=False) 
    
    # Save the deletion to the database
    db.commit()
    return Response(status_code=status.HTTP_204_NO_CONTENT)
```
> **🧠 Concept: `synchronize_session=False`**
> When you delete or update multiple rows, SQLAlchemy normally tries to evaluate what you changed and update the Python objects in your current session to match. By passing `synchronize_session=False`, you tell SQLAlchemy: *"Don't waste calculation time trying to update my Python variables, just execute the raw query on Postgres immediately."* It's much faster.

### ✏️ PUT (Update a Post)

```python
@app.put("/posts/{id}")
def update_post(id: int, post: Post, db: Session = Depends(get_db)):
    # Build the query
    post_query = db.query(models.Post).filter(models.Post.id == id)
    post_ = post_query.first()

    # If it doesn't exist, throw 404
    if post_ is None:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND,
                            detail=f"post with id {id} not found")
    
    # Execute the update using the Pydantic dictionary
    post_query.update(post.model_dump(), synchronize_session=False)
    db.commit()

    # Query it again (or return `post_query.first()`) to get the updated data
    return {"data": post_query.first()}
```

---

## 6️⃣ Is this the Standard FastAPI Way?

### Are these CRUD methods standard for all FastAPI websites?
**Yes, absolutely.**

If you look at production codebases for companies using FastAPI, the pattern you just learned is the gold standard:

1. **Routing:** `@app.get()`, `@app.post()`, etc.
2. **Data Validation:** Pydantic Models (`post: Post`) in the function parameters.
3. **Database Dependency:** `db: Session = Depends(get_db)` so routes can talk to Postgres.
4. **ORM Querying:** `db.query(Model).filter(...).first()`.
5. **Exception Handling:** `raise HTTPException()` for 404s.

**Will it get harder?** 
The only things that change as apps get bigger are:
- Moving the CRUD logic *out* of `main.py` and into separate `router` files and `repository` files.
- Adding complex **Joins** between multiple tables (e.g., Users joining with Posts).
- Switching from synchronous SQLAlchemy (the current version) to **Async SQLAlchemy** for massive scaling.

But the core logic of exactly how a record is updated, fetched, and deleted remains identical to what you just wrote.

---

<div align="center">

### 🌟 Section 6: SQLAlchemy ORM — Complete! 🌟

**Generated**: March 2026  
**Course**: FreeCodeCamp FastAPI Tutorial (15 hours)  
**Section**: 6 - SQLAlchemy

---

### ✨ *Keep Learning, Keep Building, Keep Growing!* ✨

</div>
