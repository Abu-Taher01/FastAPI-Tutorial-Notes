# 🚀 FastAPI Tutorial

---

## 📚 Course Information
- **Source**: [FreeCodeCamp FastAPI Course](https://www.youtube.com/watch?v=0sOvCWBance) (15 hours)
- **Coverage**: First 2 hours of the course
- **Compiled by**: My personal notes and learning experience while watching the video
- **Note**: This guide includes personal insights and problems faced during coding along with the course content

---

## 📖 Table of Contents

| # | Topic |
|---|-------|
| 1 | [Creating Python Virtual Environment](#1-creating-python-virtual-environment) |
| 2 | [Set the Python (venv as main interpreter)](#2-set-the-python-venv-as-main-interpreter) |
| 3 | [Make sure our terminal also using virtual environment](#3-make-sure-our-terminal-also-using-virtual-environment) |
| 4 | [Installing FastAPI](#4-installing-fastapi) |
| 5 | [Read the official Documentation (tutorial)](#5-read-the-official-documentation-tutorial) |
| 6 | [Run the server](#6-run-the-server) |
| 7 | [Postman](#7-postman) |
| 8 | [Using POST Method](#8-using-post-method) |
| 9 | [Why we need schema](#9-why-we-need-schema) |
| 10 | [Using Pydantic](#10-using-pydantic) |
| 11 | [Code with explanations](#11-code-with-explanations) |
| 12 | [HTTPException or Response](#12-httpexception-or-response) |
| 13 | [Documentation](#13-documentation) |
| 14 | [Package structure](#14-package-structure) |
| 15 | [Relational Database and SQL](#15-relational-database-and-sql) |

---

## 1) Creating Python Virtual Environment

### Command

```bash
py -3  -m venv <name>   # (py launcher should have to be already installed)
```

**OR**

```bash
python -m venv <name>
```

---

## 2) Set the Python (venv as main interpreter)

### Steps:

- Set from **View → Command Palette → Select Python Interpreter** and paste the venv path:

```
(E:\FastAPI\)venv\Scripts\python.exe
```

---

## 3) Make sure our terminal also using virtual environment

### Steps:

- Select the path from **venv → scripts → activate.bat** and paste it in the terminal:

```bash
(E:\FastAPI\)venv\Scripts\activate.bat
```

> **Note**: `*()` not needed, just for understanding. But if folder names are correct, adding it will not cause any trouble.

---

## 4) Installing FastAPI

```bash
pip install fastapi[all]
```

---

## 5) Read the official Documentation (tutorial)

📌 **Official Documentation**: https://fastapi.tiangolo.com/tutorial/#install-fastapi

### View all packages after install:

```bash
pip freeze
```

---

## 6) Run the server

### Basic Command:

```bash
(venv) E:\FastAPI> uvicorn main:app
```

> Replace `main` with whatever your main file name is.

### Using --reload (Auto-reload during development):

By using `--reload` you don't have to stop the server and run again after every edit:

```bash
uvicorn <module>:<attribute> --reload
```

> ⚠️ **Important**: Only in development environment, NOT in production

---

## 7) Postman

Till now, we've been using our web-browser to generate HTTP requests to test out API. And that's fine for now. However, once we start getting into more complex path operations and routes - things that involve having to send an HTTP POST or PATCH, or any of the other methods and having to send data to our API - it gets very complicated, because there's no way to natively do that in the browser, without building out a complete full frontend application. 

And to test an API, you shouldn't have to build an entire frontend application to do that - that would be unmanageable and unscalable. 

There's a lot of different tools that we can use to test our API. One of these tools is called **Postman**. If you go to [postman.com/downloads](https://postman.com/downloads), you can download this app.

---

## 8) Using POST Method

We can collect the data using POST method (payload is just a variable which stores the content):

```python
@app.post("/createposts")
def create_posts(payload: dict = Body(...)):
    print(payload)
    return {"new_post": f"title:{payload['title']}, content:{payload['content']}"}
```

---

## 9) Why we need schema

- ❌ It's a pain to get all the values from the body
- ❌ The client can send whatever data they want
- ❌ The data isn't getting validated
- ✅ We ultimately want to force the client to send data in a schema that we expect

---

## 10) Using Pydantic

We use Pydantic to validate the structure of content from the user to our desired structure.

---

## 11) Code with explanations

```python
from random import randrange
from typing import Optional
from fastapi import FastAPI
from fastapi.params import Body
from pydantic import BaseModel

app = FastAPI()

my_posts = [{"title": "title of post 1", "content":"content of post 1", "id": 1},
            {"title": "Favorite food", "content":"I like Pizza", "id": 2}]

# print(my_posts[0])

# pydantic model -> schema of data validation
class Post(BaseModel):
    title: str
    content: str
    published: bool = True
    rating : Optional[int] = None

# path operations -> endpoints
@app.get("/") # reference.http_methods
def root(): # path operation function
    return {"message": "welcome to our api!!!"}

# if multiple path operations for same path 
# first one matches the request will be executed and others will be ignored  
# so, orders really matter here

@app.get("/posts")
def get_posts():
    return {"data": my_posts}

# @app.post("/createposts")
# def create_posts(payload: dict = Body(...)):
#     print(payload)
#     return {"new_post": f"title:{payload['title']}, content:{payload['content']}"}

# title str, content str
@app.post("/posts")
# post is a pydantic model, not a dictionary, we can convert it into 
# dictionary using .dict() method
def create_posts(post: Post):
    # print(post)
    # print(post.dict())
    post_dict = post.dict()
    post_dict['id']=randrange(0,1000000)
    my_posts.append(post_dict)
    return {"data": post_dict}

def find_post(id):
    for post in my_posts:
        if post['id'] == id:
            return post

# IMPORTANT: remember the extracted id from path is always string
# you need to convert it into integer otherwise comparison will fail and you will always get post not found
# you can do it by specifying the type in the path operation function parameter

@app.get("/posts/{id}")
# path operation function parameter id is integer

def get_post(id: int):
    post = find_post(id)
    if post:
        return {"post_detail": post}
    return {"message": "post not found"}
```

---

## 12) HTTPException or Response

**Use HTTPException to raise error with status code and detail message. Or, you can also use response object to set status code and return message.**

```python
@app.get("/posts/{id}")

# path operation function parameter id is integer

def get_post(id: int, response: Response):
    
    # we convert with int(id) also but better to specify type in parameter
    
    post = find_post(id)
    if not post:
        
        # use HTTPException to raise error with status code and detail message
        # you can also use response object to set status code and return message
       
        raise HTTPException(status_code = status.HTTP_404_NOT_FOUND,
                            detail = f"post with id: {id} was not found")
        
        # alternative
        # response.status_code = status.HTTP_404_NOT_FOUND
        # return {"message": "post not found"}

    return {"post_detail": post}
```

---

## 13) Documentation

### FastAPI provides automatic Documentation:

1. **Swagger UI**: http://127.0.0.1:8000/docs#/ 
2. **ReDoc**: http://127.0.0.1:8000/redoc#/

---

## 14) Package structure

### Best Practice Folder Organization:

We should use proper structure for best practice:

1. We can move the main to a new folder named `app` (whatever you want, using matched with attribute is better)
2. To make the folder a Python package, we should create a file named `__init__.py`
3. Command should be a little changed:

```bash
uvicorn <folder_name>.main:<attribute> --reload
```

**Example:**
```bash
uvicorn app.main:app --reload
```

---

## 15) Relational Database and SQL

### SQL (Structured Query Language)

**Structured Query Language (SQL)** - Language used to communicate with DBMS:

```
user → sending SQL request → DBMS → database
```

---

### PostgreSQL

- Each instance of PostgreSQL can be carved into multiple separate databases
- By default, every PostgreSQL installation comes with one database already created called `postgres`
- This is important because PostgreSQL requires you to specify the name of the database to make a connection. So there needs to always be one database.

---

### Tables

A **table** represents a subject or event in an application.

---

### Columns vs Rows

- A table is made up of columns and rows
- Each **column** represents a different **attribute**
- Each **row** represents a different **entry** in the table

---

### Data Types Reference

| Data Type | PostgreSQL                | Python |
|-----------|---------------------------|--------|
| Numeric   | int, decimal, precision   | int, float |
| Text      | varchar, text             | string |
| Bool      | boolean                   | boolean |
| Sequence  | array                     | list   |

---

### Primary Key

- Is a column or group of columns that uniquely identifies each row in a table
- A table can have one and only one primary key
- We can have only one primary key for each column
- The primary key does not have to be the ID column always. It's up to you to decide which column uniquely defines each record
- In this example, since an email can only be registered once, the email column can also be used as the primary key

---

### Unique Constraints

A **UNIQUE** constraint can be applied to any column to make sure every record has a unique value for that column.

---

### NULL Constraints

- By default, when adding a new entry to a database, any column can be left blank. When a column is left blank, it has a **NULL** value.
- If you need a column to be properly filled in to create a new record, a **NOT NULL** constraint can be added to the column to ensure that the column is never left blank.

---

## 📌 Quick Reference

### Essential Commands

```bash
# Create virtual environment
python -m venv venv

# Activate virtual environment (Windows)
venv\Scripts\activate

# Install FastAPI
pip install fastapi[all]

# Run development server with auto-reload
uvicorn main:app --reload

# Check all installed packages
pip freeze
```

---

## 🔗 Useful Links

- [FastAPI Official Documentation](https://fastapi.tiangolo.com/)
- [FastAPI Tutorial](https://fastapi.tiangolo.com/tutorial/#install-fastapi)
- [Postman Downloads](https://postman.com/downloads)
- [Pydantic Documentation](https://docs.pydantic.dev/)

---

<div align="center">

**Generated on December 25, 2025**

From FreeCodeCamp FastAPI Course (First 2 Hours)

✨ *Keep learning and coding!* ✨

</div>
