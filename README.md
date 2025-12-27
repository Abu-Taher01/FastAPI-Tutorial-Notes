# FastAPI Tutorial

---

> A beautifully formatted guide based on my personal learning notes(for easy revision) from the FreeCodeCamp FastAPI tutorial.

---

## 📚 Course Information
- **Source**: [FreeCodeCamp FastAPI Course]([https://www.youtube.com/watch?v=0sOvCWFmrtA]) (19 hours)
- **Coverage**: First 2 hours of the course
- **Compiled by**: My personal notes and learning experience while watching the video
- **Note**: This guide includes personal insights(more focused) and problems faced during coding, along with the course content.

---

## Table of Contents
1. [Creating a Python Virtual Environment](#creating-a-python-virtual-environment)
2. [Setting the Python Interpreter](#setting-the-python-interpreter)
3. [Activating the Virtual Environment](#activating-the-virtual-environment)
4. [Installing FastAPI](#installing-fastapi)
5. [Official Documentation](#official-documentation)
6. [Running the Server](#running-the-server)
7. [Using Postman](#using-postman)
8. [Using POST Method](#using-post-method)
9. [Why Use Schemas?](#why-use-schemas)
10. [Using Pydantic](#using-pydantic)
11. [Example Code](#example-code)
12. [HTTPException & Response](#httpexception--response)
13. [Documentation](#documentation)
14. [Package Structure](#package-structure)
15. [Relational Database & SQL](#relational-database--sql)

---

## Creating a Python Virtual Environment
```sh
py -3 -m venv <name>
# or
python -m venv <name>
```
> _Requires the Python launcher to be installed._

---

## Setting the Python Interpreter
- Open **View → Command Palette → Select Python Interpreter**
- Paste the venv path, e.g.:
  ```
  (E:\FastAPI\)venv\Scripts\python.exe
  ```

---

## Activating the Virtual Environment
- In the terminal, run:
  ```sh
  (E:\FastAPI\)venv\Scripts\activate.bat
  ```
> _Note: Parentheses are for clarity only._

---

## Installing FastAPI
```sh
pip install fastapi[all]
```

---

## Official Documentation
- [FastAPI Tutorial](https://fastapi.tiangolo.com/tutorial/#install-fastapi)
- To see all installed packages:
  ```sh
  pip freeze
  ```

---

## Running the Server
```sh
uvicorn main:app
# For auto-reload during development:
uvicorn <module>:<attribute> --reload
```

---

## Using Postman
- Browsers are limited for complex API testing.
- Use [Postman](https://postman.com/downloads) for advanced HTTP requests.

---

## Using POST Method
```python
@app.post("/createposts")
def create_posts(payload: dict = Body(...)):
    print(payload)
    return {"new_post": f"title:{payload['title']}, content:{payload['content']}"}
```

---

## Why Use Schemas?
- Painful to extract all values from the body manually
- Clients can send any data
- No validation by default
- Schemas enforce structure and validation

---

## Using Pydantic
- Use Pydantic to validate and structure incoming data.

---

## Example Code
```python
from random import randrange
from typing import Optional
from fastapi import FastAPI
from fastapi.params import Body
from pydantic import BaseModel

app = FastAPI()

my_posts = [
    {"title": "title of post 1", "content": "content of post 1", "id": 1},
    {"title": "Favorite food", "content": "I like Pizza", "id": 2}
]

class Post(BaseModel):
    title: str
    content: str
    published: bool = True
    rating: Optional[int] = None

@app.get("/")
def root():
    return {"message": "welcome to our api!!!"}

@app.get("/posts")
def get_posts():
    return {"data": my_posts}

@app.post("/posts")
def create_posts(post: Post):
    post_dict = post.dict()
    post_dict['id'] = randrange(0, 1000000)
    my_posts.append(post_dict)
    return {"data": post_dict}

def find_post(id):
    for post in my_posts:
        if post['id'] == id:
            return post

@app.get("/posts/{id}")
def get_post(id: int):
    post = find_post(id)
    if post:
        return {"post_detail": post}
    return {"message": "post not found"}
```

---

## HTTPException & Response
```python
from fastapi import HTTPException, status, Response

@app.get("/posts/{id}")
def get_post(id: int, response: Response):
    post = find_post(id)
    if not post:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail=f"post with id: {id} was not found"
        )
    # Alternative:
    # response.status_code = status.HTTP_404_NOT_FOUND
    # return {"message": "post not found"}
    return {"post_detail": post}
```

---

## Documentation
- FastAPI provides automatic docs:
  - [Swagger UI](http://127.0.0.1:8000/docs#/)
  - [ReDoc](http://127.0.0.1:8000/redoc#/)

---

## Package Structure
- Move `main.py` to a folder (e.g., `app/`).
- Add `__init__.py` to make it a package.
- Run with:
  ```sh
  uvicorn app.main:app --reload
  ```

---

## Relational Database & SQL
- **SQL**: Structured Query Language for DBMS
- **Postgres**: Each instance can have multiple databases. Default: `postgres`.
- **Tables**: Represent subjects/events.
- **Columns vs Rows**: Columns = attributes, Rows = entries.

| Data Type | Postgres                | Python   |
|-----------|-------------------------|----------|
| Numeric   | int, decimal, precision | int, float |
| Text      | varchar, text           | string   |
| Bool      | boolean                 | boolean  |
| Sequence  | array                   | list     |

- **Primary Key**: Uniquely identifies each row. Only one per table.
- **Unique Constraint**: Ensures unique values in a column.
- **Null Constraint**: Use `NOT NULL` to require a value.

---

<sub>Generated on December 25, 2025</sub>
