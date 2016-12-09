# Intro to SQL

## Learning Objectives

#### Basics of Databases and SQL

**Concepts**
- Explain what a database is and why you would use one
- Explain how a DBMS, a database, and SQL relate to one another
- Describe a database schema and how it relates to tables, rows and columns

**Mechanics**
- Create a new PostgreSQL database
- Set up a PostgreSQL database schema with a saved SQL file
- Seed a PostgreSQL database with a saved SQL file
- Execute basic SQL commands to execute CRUD actions in a database

#### Relationships in SQL / SQL JOINs

- Define what a foreign key is
- Describe how to represent a one-to-many relationship in SQL database
- Explain how to represent one-to-one and many-to-many relationships in a SQL DB
- Distinguish between keys, foreign keys, and indexes
- Describe the purpose of the JOIN
- Use JOIN to combine tables in a SELECT
- Describe what it means for a database to be normalized


# Databases


## Framing

What's the main problem with our programs right now, in terms of user
experience?

When we quit them, any data / progress is lost! Right now, we can only store
information in memory, which is wiped when a program is quit. We definitely
need a way to fix this.

Enter databases...

## Databases

A database is a tool for storing data. There are many ways to store data on a computer (e.g., writing to a text file, a binary file). Databases, however, offer a number of advantages...

**Permanence**: Once we write data to our database, we can be pretty sure it
won't be lost (unless the server catches on fire).

**Speed**: Databases are generally optimized to be fast at retrieving and updating information. Literally, DBs can be 100,000x faster than reading from a file.

**Consistency** - Databases can enforce rules regarding consistency of data, especially when handling simultaneous requests to update information.

**Scalability** - Databases can lots of requests per second, and many DBs have ways to scale to massive loads by replicating / syncing information across multiple DBs.

**Querying** - DBs make it easy to search, sort, filter and combine related data using a Query Language.

> Note: There's an acronym in computer science [ACID](https://en.wikipedia.org/wiki/ACID), which is a set of properties that ensure data is reliably stored. You can read the wiki article for more info but, in short, a lot of the properties mentioned
above make a database ACID-compliant.

## What's a Relational Database?

The most popular type of database is a **relational** database. How do they work?

**Data is stored in tables.**
- These tables are organized by columns and rows, much like a spreadsheet.
- Tables are named according to what they model (e.g., `artists`, `songs`).
- In the case of `artists`, each row represents one artist.
- Each column is called an **attribute** or **field**, such as `id`, `title`,
  `birth_year`.

**Communicate via SQL (Structured Query Language)**
- SQL is a database language used specifically for relational databases.
- This is in contrast to non-relational databases, which we will use later in the course.

**Can relate data between tables**
- Hence the name *relational* database.
- We can relate rows in the `songs` table to rows in the `artists` table.
- We use a `key` to do this, which is a field that is unique for each row in a table.
- The key is often represented using an `id`, which is a unique identifier for each entry in a table.

### Types of Relational Databases

There are lots of relational databases, such as PostgreSQL, MySQL and SQLite. They are all similar in that they use SQL, but they do have different features.

MySQL claims to be "the most popular open-source database," while PostgreSQL claims in response to be "the most advanced open-source database". It's true that in most tests, Postgres comes out ahead in terms of speed and has many features MySQL lacks, such as true full-text search and handling geo-data.

> If you're interested in pros and cons, check out this [article comparing MySQL, Postgres, and SQLite](https://www.digitalocean.com/community/tutorials/sqlite-vs-mysql-vs-postgresql-a-comparison-of-relational-database-management-systems).

### Terminology

While this is a bit technical, it's worth clarifying some terminology...

* **Database**: The actual set of data being stored. We may create multiple databases on our computer, usually one for each application.
* **Database Language**: The language used to interact with a database. With relational databases, that is SQL.
* **Database Management System**: The software that lets a user interact (query) the data in a database. Examples are PostgreSQL, MySQL, MongoDB, etc.
* **Database CLI**: A tool offered by most DBMSs that allows us to query the database from the command line. For PostgreSQL, we'll use `psql`.

## Exploring Postgres

We are learning in order to be able to read it. We'll look stuff up when we want to write it.

Start by "spotlight searching" (`command-space`) for Postgres and launching `Postgres.app`. Once you see the elephant in your Mac menu bar, you'll know Postgres is running.

### psql commands

We'll use `psql` as our primary means of interacting with our databases. Later on we'll use ruby or server-side JS programs to do so in our programs.

Here's a quick demo. Following along is optional.

```sql
help -- general help
\?   -- help with psql commands
\h   -- help with SQL commands
\l   -- Lists all databases

CREATE DATABASE wdi12; -- Don't forget the semicolon!
\l -- What changed?

\c wdi12 -- Connect to wdi8 database

\d -- Lists all tables

CREATE TABLE students (
  id SERIAL PRIMARY KEY,
  first_name VARCHAR NOT NULL,
  last_name VARCHAR NOT NULL,
  quote TEXT,
  birthday VARCHAR,
  ssn INT NOT NULL UNIQUE
); -- Here we are defining a schema. More on this in just a bit...

\d

SELECT * FROM students;

INSERT INTO students (first_name, last_name) VALUES ('Andy', 'Kim');
-- This won't work!

INSERT INTO students (first_name, last_name, quote, birthday, ssn) VALUES ('Andy', 'Kim', 'Two goldfish are in a tank. One says, "Know how to drive this thing?"', 'April 7', 8675309);
SELECT * FROM students;

UPDATE students SET first_name = 'Andrew' WHERE first_name = 'Andy';
SELECT * FROM students;

DELETE FROM students WHERE first_name = 'Andy';
DELETE FROM students WHERE first_name = 'Andrew';

SELECT * FROM students;

DROP TABLE students; -- "drop" means to delete an entire table or database

DROP DATABASE wdi8;

\q --quits
```

In short...
- Backslash commands (e.g. `\l` ) are commands to navigate psql. These are psql-specific.
- Everything else is SQL. The SQL is what actually interacts with the database.

> If you're curious as to where exactly your databases are being stored locally, enter `SHOW data_directory;` while in psql.

### SQL Syntax

- All statements end in a semicolon.
- Whitespace doesn't matter.
- Uppercasing!
- Always use single quotes when typing out string values.
- [Ye olde style guide.](http://leshazlewood.com/software-engineering/sql-style-guide/)

## Schema

Every application's database will have one or more tables. You will recall, each table stores information about a particular model (e.g., `artists`, `songs`).

Each table has a **schema**, which defines what columns it has. For each column the schema defines...

- The column's name.
- the column's data type.
- Any constraints for that column.

### Common Data Types

Here are some common data types for SQL DBs. They are all, for the most part, things you've seen before...

- Boolean
- Integer
- Float
- Text / VARCHAR
  - VARCHAR is short, TEXT is long
- NULL
- Date
- Time

> [And many more...](http://www.postgresql.org/docs/9.3/interactive/datatype.html)

### Constraints

Constraints act as limits on the data that can go in a column.
- e.g., `NOT NULL` and `UNIQUE`.

> [And many more...](http://www.postgresql.org/docs/8.1/static/ddl-constraints.html)

### Example: Garnet

[GA DC's super scary schema used for attendance tracking and grading](https://github.com/ga-dc/garnet/blob/master/db/schema.rb) 😱

### Defining a Schema

Next we're going to build a schema for a database in a sample application. It can change later on if we need to add / remove tables or columns, but we'll start with something simple.

Instead of typing this into psql, we're going to do so by saving the schema to a `.sql` file and run it, just like we have with `.js` and `.rb` files.

## We Do: Building Our Database

1. Clone this
[library SQL Exercise repo](https://github.com/ga-wdi-exercises/library_sql).
2. Open it in Atom.
3. Follow along with the directions below, running commands in the terminal.

#### Creating our Database

```bash
$ createdb library
```

Note that this is a command-line utility that ships with Postgres, as an alternate to using the SQL command `CREATE DATABASE library;` inside `psql`.

#### Inspecting our Schema

Look critically at each line of the provided `schema.sql` file. Here's how one row breaks down...

`id SERIAL PRIMARY KEY`
- `id`: column name, how we will refer to this column
- `SERIAL` is the data type (similar to integer or string).  It's a special datatype for unique identifier columns, which the db auto-increments.
- `PRIMARY KEY`: a special constraint which indicates a unique identifier for each row.

Take a few minutes to research the other rows.

#### Loading Our Schema

To load our schema, we need to run our `schema.sql` file via the Terminal...

```bash
$ psql -d library < schema.sql
```

This means: "run the psql program, connect to the database called 'library', then run the `schema.sql` file in that database".

#### Loading a Seed File

We've provided a sql file that adds sample data into our `library` database.

Load that in so we can practice interacting with our data:

```bash
$ psql -d library < seed.sql
```

## Performing CRUD actions with SQL

CRUD stands for the most basic interactions we want to have with any database: **Create, Read, Update and Destroy**.

The most common SQL commands correspond to these 4 actions...

* `INSERT` -> Create a row
* `SELECT` -> Read / get information for rows
* `UPDATE` -> Update a row
* `DELETE` -> Destroy a row

First, enter into the library DB...

```bash
$ psql
$ \c library
```

### INSERT

`INSERT` adds a row to a table...

```sql
INSERT INTO authors(name, nationality, birth_year) VALUES ('Adam Bray', 'United States of America', 1985);
```

### SELECT

`SELECT` returns rows from a table...

```sql
-- select all columns from all rows
SELECT * FROM authors;

-- select only some columns, from all rows
SELECT name, birth_year FROM authors;

-- select rows that meet certain criteria
SELECT * FROM authors WHERE name = 'James Baldwin';
```

### UPDATE

`UPDATE` updates values for one or more rows...

```sql
UPDATE authors SET name = 'Adam B.', birth_year = 1986 WHERE name = 'Adam Bray';
```

### DELETE

`DELETE` removes rows from a table...

```sql
DELETE FROM authors WHERE name = 'Adam B.';
```

## Exercise!

Complete the queries in `basic_queries.sql` in the library_sql repo.


# Relationships in SQL / SQL JOINs

One of the key features of relational databases is that they can represent relationships between rows in different tables.

Going back to our library example, we have two tables: `books` and `authors`. Our goal now is to somehow indicate the relationship between a book and an author. In this case, that relationship indicates who wrote the book.

You can imagine that we'd like to use this information in a number of ways, such as...
- Getting the author information for a given book.
- Getting all books written by a given author.
- Searching for books based on attributes of the author (e.g., all books written by a Chinese author).

## One-to-Many

How might we represent this information in our database? For this example,
let's assume that each book has only one author (even though that's not always
the case in the real world.)

#### Option 1 - Duplicate Info (Wrong :x:)

**authors**
- name
- nationality
- birth_year

**books**
- title
- pub_date
- author_name
- author_nationality
- author_birth_year

<details>
  <summary><strong>What's the problem here?</strong></summary>

  > Duplication, difficult to keep data in sync.

</details>

#### Option 2 - Array of IDs (Wrong :x:)

**authors**
- name
- nationality
- book_ids

**books**
- title
- pub_date

<details>
  <summary><strong>What's the problem here?</strong></summary>

  > Parsing list, can't index (for speed!)

</details>

#### Option 3 (Correct! :white_check_mark:)

**authors**
- name
- nationality

**books**
- title
- pub_date
- author_id

![one_to_many](images/one_to_many.png)

## You Do: Books and Authors (Optional)

See advanced_queries.sql in the [library_sql](https://github.com/ga-dc/library_sql)
exercise.

------

## Bonus: Joins

To SELECT information on two or more tables at ones, we can use a JOIN clause.
This will produce rows that contain information from both tables. When JOINing
two or more tables, we have to tell the database how to 'match up' the rows.
(e.g. to make sure the author information is correct for each book).

This is done using the ON clause, which specifies which properties to match.

### Writing SQL JOINS

```sql
SELECT id FROM authors where name = 'J.K. Rowling';
SELECT * FROM books where author_id = 2;

SELECT * FROM books JOIN authors ON books.author_id = authors.id;
SELECT * FROM books JOIN authors ON books.author_id = authors.id WHERE authors.nationality = 'United States of America';
```

## Aside: Less Common Joins

There are actually a number of ways to join multiple tables with `JOIN`, if
you're really curious, check out this article:

[A visual explanation of SQL Joins](http://blog.codinghorror.com/a-visual-explanation-of-sql-joins/)

## Bonus: Many-to-Many Relationships

We're not going to go in-depth with many-to-many relationships today, but lets go over a simple example...

Consider if we wanted to add a categories model (e.g. fiction, non-fiction,
sci-fi, romance, etc). Books can belong to many categories (i.e. a book might be
a fiction/romance, or a history/non-fiction). And a given category might have
many books.

Because of this, we can't put a book_id column on categories, nor a category_id
column on books, either way, we might have more than one value in that field
(a no-no in terms of performance).

To solution is to create an additional table, which stores just the
relationships between the two tables. Such a table is called a join table, and
contains two foreign key columns.

In our example, we might create a table called 'categorizations', and it would
have a book_id and category_id. Each row would represent a specific book's
association with a specific category.

![many_to_many](images/many_to_many.png)

## Closing/Questions

## Sample Quiz Questions

* What is the distinctive feature of a relational database?
* How is information stored in a relational database?
* What are the different types of relations that exist in a relational database?
* How do we indicate a one-to-many relationship in a database?

## Homework: [NBA Stats](https://github.com/ga-wdi-pvd/nba_stats)

## Resources
* [GA DC Three Part Lesson](https://github.com/ga-wdi-lessons/sql-intro/blob/master/readme.md)
* Screencasts
    - GA DC WDI7 (Matt Scilipoti): [Part 1](https://youtu.be/cuPXKDMEhKw), [Part 2](https://youtu.be/wQtBDerdyKw)
    - ERDs: GA DC WDI7 (Matt Scilipoti): https://youtu.be/IDnOMi9jHgo
