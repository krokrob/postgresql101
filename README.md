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
4. Add a PostgreSQL database to the Heroku app matching your needs (choose one), this can take a while (3-5min)
```bash
# Free - 10k rows
heroku addons:wait heroku-postgresql:hobby-dev
# 9$/momth - 10M rows
heroku addons:wait heroku-postgresql:hobby-basic
# 50$/month - 64Go no row limit
heroku addons:wait heroku-postgresql:standard-0
```
ℹ️ [Plans & Pricing](https://elements.heroku.com/addons/heroku-postgresql#pricing)

5. Get your `DATABASE_URL`
```bash
heroku config:get DATABASE_URL
```
which looks like:
```bash
postgres://******************:*********************@**********.eu-west-1.compute.amazonaws.com:5432/***********
```
6. That URL is **a secret**, keep it safe!

Create a safe place for your secret:
```bash
touch .env #
```
Copy the `DATABASE_URL` in the `.env` file:
```txt
DATABASE_URL="YOUR_DATABASE_URL"
```
Keep the `.env` file away from `git`:
```bash
touch .gitignore
echo ".env" >> .gitignore
```
7. Save your setup
```bash
git status # .env should be ignored
git add .
git commit -m 'setup the postgresql database'
```

🚀 Congrats! Your PostgreSQL production database is ready!

### Minimal requirements

Install the minimal requirement packages with:

```bash
pip install -r requirements.txt
```

Installed packages:

- [psycopg](https://www.psycopg.org/) a specific connector for PostgreSQL database
- [SQLAlchemy](https://www.sqlalchemy.org/) a Pytrhon SQL object relational mapper
- [python-dotenv](https://github.com/theskumar/python-dotenv) a secret manager
- The good old pandas

## Build your database

### Connect to the DB

Open a Jupyter notebook then:

```python
import os
from sqlalchemy import create_engine


DATABASE_URL = os.getenv("DATABASE_URL")
engine = create_engine(DATABASE_URL)
```

### Create your first table `films`

Create a new cell then:
```python
films_df = pd.DataFrame([
  {"movie_id":1, "title": "The Dark Knight", "rating": 4},
  {"movie_id":2,"title": "The Dark Knight Rises", "rating": 5}
])
films_df.to_sql("movies", engine, if_exists='replace', index=False)
```

### Query your DB

Create a new cell then:

```python
# The SQLAlchemy way
pd.read_sql("movies", engine).head()
```

You can also query your DB the good old Python way with `psycopg2`:
```python
conn = psycopg2.connect(DATABASE_URL, sslmode='require')
c = conn.cursor()
fetch_films = '''
SELECT * FROM films;
'''
c.execute(fetch_films)
c.fetchall()
```

## 🚀 Your turn!
