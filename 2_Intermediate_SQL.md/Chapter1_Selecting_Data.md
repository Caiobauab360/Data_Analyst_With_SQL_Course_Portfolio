# Intermediate SQL

**Course description:**

SQL is the most popular language for turning raw data stored in a database into actionable insights. Using a database of films made around the world, this course covers:

✓ How to filter and compare data

✓ How to use aggregate functions to summarize data

✓ How to sort and group your data

✓ How to present your data cleanly using tools such as rounding and aliasing

Accompanied at every step with hands-on practice queries, this course teaches you everything you need to know to analyze data using your own SQL code today!

# Selecting Data

**Chapter description:**

In this first chapter, you’ll learn how to query a films database and select the data needed to answer questions about the movies and actors. You'll also understand how SQL code is executed and formatted.

# Querying a database

**Personal notes:**

While SQL can be used to create and modify databases, the focus of the course will be on querying databases. We will learn how to execute a query on a database using keywords that allow us to count and view all or a specific number of records. We'll also cover common SQL errors, style guidelines, and the order in which our code will be executed. We will be using PostgreSQL throughout.

We'll use a movie database with four tables: movies, reviews, people, and roles.

**COUNT()*

COUNT() can be used to count something in the tables. For example, to count the number of birthdates present in the people table, we'll use:
```sql
SELECT COUNT(birthdate) AS count_birthdates
FROM people;
```
If we want to count more than one field, we need to use COUNT multiple times. For example, to count the number of names and birthdates in the people table:
```sql
SELECT COUNT(name) AS count_names, COUNT(birthdate) AS count_birthdates
FROM people;
```
Using COUNT with a field name tells us how many values exist in a field. However, if we want to count the number of records in a table, we can use COUNT*; for example, this code provides the total number of records in the people table:
```sql
SELECT COUNT(*) AS total_records
FROM people;
```

**DISTINCT*

We can use the DISTINCT keyword to select all unique values from a field. For example, if we are interested in knowing which languages are represented in the films table, adding DISTINCT to our query will remove all duplicates:
```sql
SELECT DISTINCT language
FROM films;
```

Combining COUNT with DISTINCT is also common to count the number of unique values in a field. For example, this query counts the number of distinct birthdates in the people table:
```sql
SELECT COUNT(DISTINCT birthdate) AS count_distinct_birthdates
FROM people;
```

**Exercises:**

**Learning to COUNT()**

You saw how to use COUNT() in the video. Do you remember what it returns?

Here is a query counting film_id. Select the answer below that correctly describes what the query will return.

    SELECT COUNT(film_id) AS count_film_id
    FROM reviews;

Run the query in the console to test your theory!

        "The number of records containing a film_id."

**Practice with COUNT()**

As you've seen, COUNT(*) tells you how many records are in a table. However, if you want to count the number of non-missing values in a particular field, you can call COUNT() on just that field.

Let's get some practice with COUNT()! You can look at the data in the tables throughout these exercises by clicking on the table name in the console.

**Instructions:**

- Count the total number of records in the people table, aliasing the result as count_records.

        -- Count the number of records in the people table
        SELECT COUNT(id) AS count_records
        FROM people;

- Count the number of records with a birthdate in the people table, aliasing the result as count_birthdate.

        -- Count the number of birthdates in the people table
        SELECT COUNT(birthdate) AS count_birthdate
        FROM people; 

- Count the records for languages and countries in the films table; alias as count_languages and count_countries.

        -- Count the records for languages and countries represented in the films table
        SELECT COUNT(language) AS count_languages, COUNT(country) AS count_countries
        FROM films;

**SELECT DISTINCT**

Often query results will include many duplicate values. You can use the DISTINCT keyword to select the unique values from a field.

This might be useful if, for example, you're interested in knowing which languages are represented in the films table. See if you can find out what countries are represented in this table with the following exercises.

**Instructions:**

- Return the unique countries represented in the films table using DISTINCT.

        -- Return the unique countries from the films table
        SELECT DISTINCT country 
        FROM films;

- Return the number of unique countries represented in the films table, aliased as count_distinct_countries.

        -- Count the distinct countries from the films table
        SELECT COUNT(DISTINCT country) AS count_distinct_countries
        FROM films;

# Query execution

**Personal notes:**

Unlike many programming languages, SQL code is not processed in the order it is written. The first line to be processed is the FROM statement. Before any data can be selected, the table from which the data will be selected needs to be indicated. Next is the SELECT statement. Finally, we can use the LIMIT keyword to limit the results to a specified number of records. For example, if we want to return only the first ten names from the people table:
```sql
SELECT name
FROM people
LIMIT 10;
```

Knowing the processing order is especially useful when debugging and creating aliases for fields and tables. Suppose we need to refer to an alias later in our code. In that case, this alias only makes sense to a processor when its declaration in the SELECT statement is processed before the alias reference is made elsewhere in the query.

**Debugging SQL*

Let's delve into SQL code debugging and how to read error messages. Some messages are extremely helpful, identifying and even suggesting a solution for the error.

**Exercises:**

**Order of execution**

SQL code is processed differently than other programming languages in that you need to let the processor know where to pull the data from before making selections.

It's essential to know your code's order of execution compared to the order it is written in to understand what results you'll get from your query and how to fix any errors that may come up.

        FROM
        SELECT
        LIMIT

**Debugging errors**

Debugging is an essential skill for all coders, and it comes from making many mistakes and learning from them.

In this exercise, you'll be given some buggy code that you'll need to fix.

**Instructions:**

- Debug and fix the SQL query provided.

        -- Debug this code
        SELECT certification
        FROM films
        LIMIT 5;

- Find the two errors in this code; the same error has been repeated twice.

        -- Debug this code
        SELECT film_id, imdb_score, num_votes
        FROM reviews;

- Find the two bugs in this final query.

        -- Debug this code
        SELECT COUNT(birthdate) AS count_birthdays
        FROM people;

# SQL style

**Personal notes:**

SQL code doesn't need to be organized for the processor to execute, but an organized code format is a universal standard to make the code more readable among different people worldwide.

Due to different formatting styles, it is helpful to follow an SQL style guide, such as Holywell's, which outlines best practices for indentation, capitalization, and naming conventions for tables, fields, and aliases: SQL Style Guide. Including a semicolon at the end of the query is considered a best practice for several reasons. First, some types of SQL require it, so it's a good habit to have. Also, the semicolon usage indicates its end, which is useful in a file containing multiple queries.

Adhering to SQL style guides allows for easier collaboration among peers. Having clean and readable code is highly valued in the community and is a professional setting that will make things easier for anyone wanting to understand or debug our queries.

**Exercises:**

**Formatting**

Readable code is highly valued in the coding community and professional settings. Without proper formatting, code and results can be difficult to interpret. You'll often be working with other people that need to understand your code or be able to explain your results, so having a solid formatting habit is essential.

In this exercise, you'll correct poorly written code to better adhere to SQL style standards.

**Instruction:**

- Adjust the sample code so that it is in line with standard practices.

        -- Rewrite this query
        SELECT person_id, role 
        FROM roles 
        LIMIT 10;

**Non-standard fields**

You may occasionally receive a dataset with poorly named fields. Ideally, you would fix these, but you can work around it with some added punctuation in this instance.

A sample query and schema have been provided; imagine you need to be able to run it with a non-standard field name. Select the multiple-choice answer that would correctly fill in the blank to return both a film's id and its number of Facebook likes for all reviews:

SELECT film_id, ___
FROM reviews;

        facebook likes

