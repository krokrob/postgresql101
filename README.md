# PostgreSQL 101

How to create a production-ready PostgreSQL database.

## Setup

### Heroku application

1. Create project
```bash
cd ~/code/<user.github_nickname>
mkdir postgresql101
cd postgresql101
```
2. Start versioning with Git
```bash
git init
git add .
git commit -m 'kickstart PostgreSQL project'
```
3. Create Heroku app (in Europe for instance)
```bash
heroku create --region eu
```
4. Add a **free** PostgreSQL database to the Heroku app - [Plans & Pricing](https://elements.heroku.com/addons/heroku-postgresql#pricing)
```bash
heroku addons:downgrade heroku-postgresql:hobby-dev
```
**Wait for 3-5 min** then execute the following command (try again until you get the expected output):
```bash
heroku config
```
returning the `DATABASE_URL`:
```bash
DATABASE_URL: postgres://******************:*********************@**********.eu-west-1.compute.amazonaws.com:5432/***********
```
Keep this URL for the next steps.

🚀 Congrats! Your PostgreSQL production database is ready!

### The `psycopg2` library

You need a specific connector to communicate with the PostgreSQL database:

```bash
pip install psycopg2-binary
```

## Build your database

### Connect to the DB

Open a Jupyter notebook then:

```python
import os
import psycopg2


DATABASE_URL = "replace_this_with_your_DATABASE_URL"

conn = psycopg2.connect(DATABASE_URL, sslmode='require') # Instantiate a connector with the production DB
```

### Create your first table `films`

Create a new cell then:
```python
c = conn.cursor() # Instantiate a cursor to execute queries

create_films = '''
CREATE TABLE films (
    id        char(5) CONSTRAINT firstkey PRIMARY KEY ,
    title       varchar(40) NOT NULL,
    rating        integer NOT NULL
);
'''

c.execute(create_films) # Execute the query
c.close() # Close cursor ⚠️
conn.commit() # Commit table creation ⚠️
```

### Seed the table `films`

Create a new cell then:
```python
c = conn.cursor()
seed_film = '''
INSERT INTO films (id,title, rating)
VALUES(1,'The Matrix', 5);
'''
c.execute(seed_film)
c.close()
conn.commit()
```

### Query your DB

Create a new cell then:
```python
c = conn.cursor()
fetch_films = '''
SELECT * FROM films;
'''
c.execute(fetch_films)
c.fetchall()
```

This should output:
```python
[('1    ', 'The Matrix', 5)]
```

## 🚀 Your turn!
