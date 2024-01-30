# Functions for Manipulating Data in PostgreSQL

**Course Description:**

This course will provide you an understanding of how to use built-in PostgreSQL functions in your SQL queries to manipulate different types of data including strings, character, numeric and date/time. We'll travel back to a time where Blockbuster video stores were on every corner and if you wanted to
watch a movie, you actually had to leave your house to rent a DVD! You'll also get an introduction into the robust full-text search capabilities which provides a powerful tool for indexing and matching keywords in a PostgreSQL document. And finally, you'll learn how to extend these features by using PostgreSQL extensions.

# Overview of Common Data Types

**Chapter description:**

Learn about the properties and characteristics of common data types including strings, numerics and arrays and how to retrieve information about your database.

# Welcome!

**Personal notes:**

**Overview of Common Data Types*

In this chapter, we will learn about built-in functions and operators to extend the capabilities of your PostgreSQL database. We will use the Sakila database, a well-known sample database that models a fictional DVD rental store. The chosen database is highly normalized, allowing for significant sample queries and providing an excellent representation of PostgreSQL data types and custom functions.

**Common Data Types*

PostgreSQL has a robust set of native data types. Text data types such as CHAR, VARCHAR, and TEXT, numeric data types like INT and DECIMAL, date and time types like DATE, TIME, TIMESTAMP, INTERVAL, and even arrays.

It is important to have an understanding of the properties and characteristics of various data types whenever you are working with a relational database like PostgreSQL. Text data types like CHAR and VARCHAR allow for a fixed or variable number of characters and string data. Numeric data types enable you to store integers like 'payment_ID' and decimal numbers with variable precision, such as the 'amount' in the payment table.

**Determining Data Type from Existing Tables*

When working with existing databases, there will be times when you need to determine the data types of the columns you will be working with. PostgreSQL stores information about all database objects in a database system called INFORMATION SCHEMA. By querying certain tables in this database, you can determine information about the database, including column data types.

The following query will return the column_name and data type for the columns we saw in the previous slide. By executing this query, you will get a result where you can see that the title and description columns are indeed text data types, specifically VARCHAR and TEXT. However, you will notice that special_features is actually an ARRAY.

```sql
SELECT
    column_name,
    data_type
FROM INFORMATION_SCHEMA.COLUMNS
WHERE column_name IN ('title', 'description', 'special_features')
    AND table_name = 'film';
```

**Exercises:**

**Text data types**

You learned about some of the common data types that you'll work within PostgreSQL, some characteristics of these types, and how to determine the data type of a column in an existing table. Think back to the video and answer the following question:

Which of the following is not a valid text data type in PostgreSQL?

        "TEXT"

**Getting information about your database**

As we saw in the video, PostgreSQL has a system database called INFORMATION_SCHEMA that allows us to extract information about objects, including tables, in our database.

In this exercise we will look at how to query the tables table of the INFORMATION_SCHEMA database to discover information about tables in the DVD Rentals database including the name, type, schema, and catalog of all tables and views and then how to use the results to get additional information about columns in our tables.

**Instructions:**

- Select all columns from the INFORMATION_SCHEMA.TABLES system database. Limit results that have a public table_schema.

```sql    
-- Select all columns from the TABLES system database
SELECT * 
FROM INFORMATION_SCHEMA.TABLES
-- Filter by schema
WHERE table_schema = 'public';
``` 

- Select all columns from the INFORMATION_SCHEMA.COLUMNS system database. Limit by table_name to actor

```sql    
-- Select all columns from the COLUMNS system database
SELECT * 
FROM INFORMATION_SCHEMA.COLUMNS
WHERE table_name = 'actor';
```   

**Determining data types**

The columns table of the INFORMATION_SCHEMA database also allows us to extract information about the data types of columns in a table. We can extract information like the character or string length of a CHAR or VARCHAR column or the precision of a DECIMAL or NUMERIC floating point type.

Using the techniques you learned in the lesson, let's explore the customer table of our DVD Rental database.

**Instructions:**

- Select the column name and data type from the INFORMATION_SCHEMA.COLUMNS system database.

- Limit results to only include the customer table.

```sql   
-- Get the column name and data type
SELECT
    column_name, 
    data_type
-- From the system database information schema
FROM INFORMATION_SCHEMA.COLUMNS 
-- For the customer table
WHERE table_name = 'customer';
```   

# Date and time data types

**Personal notes:**

Understanding how to work with these data types is crucial for preparing and extracting data for machine learning and data science. We will learn about the precision and features of timestamp data types, intervals, and date and time types.

**TIMESTAMP Data Types*

Most of the date and time data you will work with in SQL will have a TIMESTAMP data type. Timestamps contain both a date and a time value with microsecond precision. These data types are very common because they can be used to record an exact moment in time, such as when a payment was made or a record was last updated. PostgreSQL timestamps use the ISO 8601 format, which is a four-digit year, followed by a month and day with two digits each, separated by dashes.

**DATE and TIME Data Types*

When you need to store only a part of the TIMESTAMP in your database, the DATE and TIME types might be better options. The DATE and TIME types are essentially the date and time values of the TIMESTAMP.

**INTERVAL Data Types*

Finally, INTERVAL data types store date and time data as a period of time in years, months, days, hours, seconds, etc. Intervals are useful when you want to perform arithmetic on date and time columns.

**Looking at Date and Time Types*

We can use the same technique we learned in the previous lesson to determine information about a column with a date and time data type by querying the INFORMATION_SCHEMA system database.

PostgreSQL provides the ability to store TIMESTAMP and TIME data types with or without time zones. Although this is useful in certain situations, most of the time, you will work with timestamp values without time zones, which is the default behavior.

**Exercises:**

**Properties of date and time data types**

Which of the following is NOT correct?

       " TIMESTAMP data types contain both date and time values."

**Interval data types**

INTERVAL data types provide you with a very useful tool for performing arithmetic on date and time data types. For example, let's say our rental policy requires a DVD to be returned within 3 days. We can calculate the expected_return_date for a given DVD rental by adding an INTERVAL of 3 days to the rental_date from the rental table. We can then compare this result to the actual return_date to determine if the DVD was returned late.

Let's try this example in the exercise.

**Instructions:**

- Select the rental date and return date from the rental table.

- Add an INTERVAL of 3 days to the rental_date to calculate the expected return date`.

```sql
SELECT
-- Select the rental and return dates
rental_date,
return_date,
-- Calculate the expected_return_date
rental_date + INTERVAL '3 days' AS expected_return_date
FROM rental;
```

# Working with ARRAYs

**Personal notes:**

Arrays in PostgreSQL are very similar to arrays in most programming languages. You can create multi-dimensional arrays of varying lengths for any native data type in PostgreSQL.

**CREATE TABLE Example*

Most data science tasks involve getting data from a database using SQL queries with SELECT statements. Before you can extract data, someone needs to create the database, add at least one table with at least one column, and then insert some records. The CREATE TABLE statement, as seen in this example, will create an empty table called my_first_table with columns first_column and second_column defined as text and integer data types.

```sql
CREATE TABLE my_first_table (
    first_column text, 
    second_column integer
);
```

The following INSERT statement example will add a record to the my_first_table table with "text value" as the value for the first column and 12 as the value for the second column.

```sql
INSERT INTO my_first_table
    (first_column, second_column) VALUES ('text value', 12);
```

**ARRAY: A Special Type*

To create an ARRAY type, simply add "[ ]" to the end of the data type you want to make an array. Let's create a simple table with two array columns to illustrate how this is done. This table has an email column that will be a nested array of text data to store the email type and address for a given student_id. The "text_scores" column will contain an array of integer values representing test numerical scores.

```sql
CREATE TABLE grades (
    student_id INT,
    email text[][],
    text_scores INT[]
);
```

**INSERT Statements with ARRAYS*

Once the table is created, we can use INSERT statements to add some records to the table. Note how arrays are represented in SQL with brackets and quotes for the email and a comma-separated list of integers for the text_scores.

```sql
INSERT INTO grades
VALUES (1, '{{"work", "work1@datacamp.com"}, {"other","other1@datacamp.com"}}', '{92, 85, 96, 88}');
```

**Accessing ARRAYS*

Now that we have data in our table, let's see how you access array data in a SELECT statement. Accessing arrays in PostgreSQL is very similar to accessing arrays in other programming languages. For email, you can get the first element of the array using the array notation you see below with index values starting from 1.

```sql
SELECT
    email[1][1] AS type,
    email[1][2] AS address,
    test_scores[1]
FROM grades;
```

Note that array indices in PostgreSQL start with 1, not 0 as in Python.

**Searching ARRAYS*

The same notation used to access arrays in the SELECT statement can also be used in the WHERE clause as a filter. In the example below, we look for records that have 'work' as the value in the first index of the email array. Using the standard syntax for non-array columns like WHERE email='work' would result in an error.

```sql
SELECT
    email[1][1] AS type,
    email[1][2] AS address,
    test_scores[1]
FROM grades
WHERE email[1][1] = 'work';
```

**ARRAY Function and Operators*

The ANY function allows you to search for a value in an array and return a record if it finds a match. In the example below, we want to query all records where the email address contains "other" in any array value.

```sql
SELECT
    email[1][1] AS type,
    email[1][2] AS address,
    test_scores[1]
FROM grades
WHERE 'other' = ANY(email);
```

An alternative to the ANY function is the contains operator (@>). The syntax for this operator is a bit more complex but returns the same results as the ANY function, as shown below.

```sql
SELECT
    email[1][1] AS type,
    email[1][2] AS address,
    test_scores[1]
FROM grades
WHERE email @> ARRAY['other'];
```

**Exercises:**

**Accessing data in an ARRAY**

In our DVD Rentals database, the film table contains an ARRAY for special_features which has a type of TEXT[]. Much like any ARRAY data type in PostgreSQL, a TEXT[] array can store an array of TEXT values. This comes in handy when you want to store things like phone numbers or email addresses as we saw in the lesson.

Let's take a look at the special_features column and also practice accessing data in the ARRAY.

**Instructions:**

- Select the title and special features from the film table and compare the results between the two columns.

```sql    
-- Select the title and special features column 
SELECT 
title, 
special_features 
FROM film;
```

- Select all films that have a special feature Trailers by filtering on the first index of the special_features ARRAY.

```sql  
-- Select the title and special features column 
SELECT 
title, 
special_features 
FROM film
-- Use the array index of the special_features column
WHERE special_features[1] = 'Trailers';
```

- Now let's select all films that have Deleted Scenes in the second index of the special_features ARRAY.

```sql    
-- Select the title and special features column 
SELECT 
title, 
special_features 
FROM film
-- Use the array index of the special_features column
WHERE special_features[2] = 'Deleted Scenes';
```  

**Searching an ARRAY with ANY**

As we saw in the video, PostgreSQL also provides the ability to filter results by searching for values in an ARRAY. The ANY function allows you to search for a value in any index position of an ARRAY. Here's an example.

WHERE 'search text' = ANY(array_name)
When using the ANY function, the value you are filtering on appears on the left side of the equation with the name of the ARRAY column as the parameter in the ANY function.

**Instruction:**

- Match 'Trailers' in any index of the special_features ARRAY regardless of position.

```sql    
SELECT
title, 
special_features 
FROM film 
-- Modify the query to use the ANY function 
WHERE 'Trailers' = ANY(special_features);
```

**Searching an ARRAY with @>**

The contains operator @> operator is alternative syntax to the ANY function and matches data in an ARRAY using the following syntax.

    WHERE array_name @> ARRAY['search text'] :: type[]

So let's practice using this operator in the exercise.

**Instruction:**

- Use the contains operator to match the text Deleted Scenes in the special_features column.

```sql
SELECT 
title, 
special_features 
FROM film 
-- Filter where special_features contains 'Deleted Scenes'
WHERE special_features @> ARRAY['Deleted Scenes'];
```
