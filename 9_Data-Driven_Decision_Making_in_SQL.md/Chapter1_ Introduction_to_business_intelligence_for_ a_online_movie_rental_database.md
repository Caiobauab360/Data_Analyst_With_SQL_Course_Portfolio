# Data-Driven Decision Making in SQL

**Course Description**

In this course, you will learn how to use SQL to support decision making. It is based on a case study about an online movie rental company with a database about customer information, movie ratings, background information on actors and more. You will learn to apply SQL queries to study for example customer preferences, customer engagement, and sales development. This course also covers SQL extensions for online analytical processing (OLAP), which makes it easier to obtain key insights from multidimensional aggregated data.

# Introduction to business intelligence for a online movie rental database

**Chapter description:**

The first chapter is an introduction to the use case of an online movie rental company, called MovieNow and focuses on using simple SQL queries to extract and aggregated data from its database.

# Introduction to data driven decision making

**Personal notes:**

Throughout this course, we will be using a PostgreSQL database from a fictional movie streaming platform called MovieNow. MovieNow offers an online streaming service for movies.

*Objectives of Data-Driven Decision Making*

The primary goal of data-driven decision-making is to achieve both short-term and long-term objectives. Valuable insights can be extracted from data to support short-term operational decisions. For instance, understanding the popularity of certain actors helps MovieNow decide whether to acquire specific movies for the platform. Additionally, last month's revenue might be crucial information for short-term investment decisions.

For long-term decisions, data-driven support can provide insights into customer growth and success in specific regions in the past. This information can inform the company's decisions about when and where to expand its market in the future. Moreover, understanding the long-term development of overall revenue helps MovieNow plan for long-term investments.

*KPIs: Key Performance Indicators*

Key Performance Indicators (KPIs) assist a company (or its subdivisions) in defining and monitoring success. Revenue is a straightforward indicator of success. For the MovieNow database, this is calculated as the sum of the prices of rented movies. For customer relationship management within the subdivision, KPIs such as 'customer satisfaction' could be quantified by the average rating of all movies, or 'customer engagement' as the number of active customers in a specific period.

**Exercises:**

**Exploring the database**

Explore the tables and its columns. Which of the following quantities can't be computed?

        "The number of movies with an international award."

**Exploring the table renting**

The table renting includes all records of movie rentals. Each record has a unique ID renting_id. It also contains information about customers (customer_id) and which movies they watched (movie_id). Furthermore, customers can give a rating after watching the movie, and the day the movie was rented is recorded.

**Instructions:**

- Select all columns from renting.

```sql
SELECT *  -- Select all
FROM renting;        -- From table renting
```

- Now select only those columns from renting which are needed to calculate the average rating per movie.

```sql
SELECT rating, movie_id  -- Select all columns needed to compute the average rating per movie
FROM renting;
```

- Question: In SQL missing values are coded with null. In which column of renting did you notice null values?

        "rating"

# Filtering and ordering

**Personal notes:**

*Filtering and Ordering in SQL*

A crucial aspect of querying data involves filtering records that are relevant to a specific research question. Additionally, arranging tables in a particular order is essential for future applications.

*WHERE Clause*

The `WHERE` clause in SQL is used to filter records based on a specified condition. It allows you to extract only those records that fulfill a particular criterion. A basic structure of the `WHERE` clause looks like this:

```sql
SELECT column1, column2, ...
FROM table
WHERE condition;
```

For example, to retrieve all movies with a rating higher than 8, you might use:

```sql
SELECT *
FROM movies
WHERE rating > 8;
```

This would return all rows from the "movies" table where the rating is greater than 8.

*ORDER BY Clause*

The `ORDER BY` clause in SQL is used to sort the result set based on one or more columns. It allows you to specify the order in which the rows should appear. The basic syntax is:

```sql
SELECT column1, column2, ...
FROM table
ORDER BY column1 [ASC | DESC], column2 [ASC | DESC], ...;
```

For example, to retrieve all movies sorted by release year in descending order, you might use:

```sql
SELECT *
FROM movies
ORDER BY release_year DESC;
```

This would return all rows from the "movies" table, sorted by the "release_year" column in descending order.

Combining `WHERE` and `ORDER BY` allows you to filter and sort your data simultaneously. For instance, if you want to retrieve movies released after 2000 and order them by rating in descending order:

```sql
SELECT *
FROM movies
WHERE release_year > 2000
ORDER BY rating DESC;
```

This would return movies released after 2000, sorted by rating in descending order.

**Exercises:**

**Working with dates**

For the analysis of monthly or annual changes, it is important to select data from specific time periods. You will select records from the table renting of movie rentals. The format of dates is 'YYYY-MM-DD'.

**Instructions:**

- Select all movies rented on October 9th, 2018.

```sql
SELECT *
FROM renting
WHERE date_renting = '2018-10-09'; -- Movies rented on October 9th, 2018
```

- Select all records of movie rentals between beginning of April 2018 till end of August 2018.

```sql
SELECT *
FROM renting
WHERE date_renting BETWEEN '2018-04-01' AND '2018-08-31' ; -- from beginning April 2018 to end August 2018
```

- Put the most recent records of movie rentals on top of the resulting table and order them in decreasing order.

```sql
SELECT *
FROM renting
WHERE date_renting BETWEEN '2018-04-01' AND '2018-08-31'
ORDER BY renting DESC; -- Order by recency in decreasing order
```

**Selecting movies**

The table movies contains all movies available on the online platform.

**Instructions:**

- Select all movies which are not dramas.

```sql
SELECT *
FROM movies
WHERE genre <> 'Drama'; -- All genres except drama
```

- Select the movies 'Showtime', 'Love Actually' and 'The Fighter'.

```sql
SELECT *
FROM movies
WHERE title IN ('Showtime', 'Love Actually', 'The Fighter'); -- Select all movies with the given titles
```

- Order the movies by increasing renting price.

```sql
SELECT *
FROM movies
ORDER BY renting_price DESC ; -- Order the movies by increasing renting price
```

**Select from renting**

Only some users give a rating after watching a movie. Sometimes it is interesting to explore only those movie rentals where a rating was provided.

**Instructions:**

- Select from table renting all movie rentals from 2018.

- Filter only those records which have a movie rating.

```sql
SELECT *
FROM renting
WHERE date_renting BETWEEN '2018-01-01' AND '2018-12-31' -- Renting in 2018
AND rating IS NOT NULL; -- Rating exists
```

# Aggregations - summarizing data

**Personal notes:**

In decision-making, it is often crucial to focus on summaries of specific groups rather than individual records. SQL provides powerful aggregation functions to help in summarizing and analyzing data.

*Overview of Aggregation*

Aggregation functions in SQL are used to perform calculations on a set of values to return a single value. Common aggregation functions include:

- *COUNT*: Counts the number of rows.
- *SUM*: Calculates the sum of numeric values.
- *AVG*: Computes the average of numeric values.
- *MIN*: Finds the minimum value in a set.
- *MAX*: Finds the maximum value in a set.

Here's a basic structure of an aggregation query:

```sql
SELECT AGGREGATE_FUNCTION(column) AS alias
FROM table
WHERE condition
GROUP BY column;
```

For example, to find the average rating for each genre in the "movies" table:

```sql
SELECT genre, AVG(rating) AS avg_rating
FROM movies
GROUP BY genre;
```

This would return the average rating for each unique genre.

*Aggregation with NULL Values*

Dealing with NULL values is a common challenge in data analysis. To identify NULL values in a specific column, you can use the `COUNT` function along with `DISTINCT` and compare it with the total number of rows in the table.

```sql
SELECT COUNT(*) AS total_rows, COUNT(DISTINCT column_with_nulls) AS non_null_rows
FROM your_table;
```

If `total_rows` and `non_null_rows` are different, it indicates the presence of NULL values in the specified column.

*DISTINCT and Aliases*

- **DISTINCT*: The `DISTINCT` keyword is used to return unique values in the result set. It eliminates duplicate values.

  ```sql
  SELECT DISTINCT column
  FROM table;
  ```

- **Aliases*: Aliases are used to give a column or a table a temporary name. They make the output more readable and can be helpful when using aggregate functions.

  ```sql
  SELECT AVG(column) AS average_value
  FROM table;
  ```

The `AS` keyword is optional when creating aliases.

By mastering these aggregation techniques, you can gain valuable insights from your data, making informed decisions based on summarized information.

**Exercises:**

**Summarizing customer information**

In most business decisions customers are analyzed in groups, such as customers per country or customers per age group.

**Instructions:**

- Count the number of customers born in the 80s.

```sql
SELECT COUNT(*) -- Count the total number of customers
FROM customers
WHERE date_of_birth BETWEEN '1980-01-01' AND '1989-12-31'; -- Select customers born between 1980-01-01 and 1989-12-31
```

- Count the number of customers from Germany.

```sql
SELECT count(*)   -- Count the total number of customers
FROM customers
WHERE country = 'Germany'; -- Select all customers from Germany
```

- Count the number of countries where MovieNow has customers.

```sql
SELECT count(DISTINCT country) -- Count the number of countries
FROM customers;
```

**Ratings of movie 25**

The movie ratings give us insight into the preferences of our customers. Report summary statistics, such as the minimum, maximum, average, and count, of ratings for the movie with ID 25.

**Instructions:**

- Select all movie rentals of the movie with movie_id 25 from the table renting.

- For those records, calculate the minimum, maximum and average rating and count the number of ratings 
for this movie.

```sql
SELECT MIN(rating) AS min_rating, -- Calculate the minimum rating and use alias min_rating
	   MAX(rating) AS max_rating, -- Calculate the maximum rating and use alias max_rating
	   AVG(rating)  AS avg_rating, -- Calculate the average rating and use alias avg_rating
	   COUNT(rating) number_ratings -- Count the number of ratings and use alias number_ratings
FROM renting
WHERE movie_id = '25'; -- Select all records of the movie with ID 25
```

**Examining annual rentals**

You are asked to provide a report about the development of the company. Specifically, your manager is interested in the total number of movie rentals, the total number of ratings and the average rating of all movies since the beginning of 2019.

**Instructions:**

- First, select all records of movie rentals since January 1st 2019.

```sql
SELECT *-- Select all records of movie rentals since January 1st 2019
FROM renting
WHERE date_renting BETWEEN '2019-01-01' AND now(); 
```

- Now, count the number of movie rentals and calculate the average rating since the beginning of 2019.

```sql
SELECT 
	COUNT(*), -- Count the total number of rented movies
	AVG(rating) -- Add the average rating
FROM renting
WHERE date_renting >= '2019-01-01';
```

- Use as alias column names number_renting and average_rating respectively.

```sql
SELECT 
	COUNT(*) AS number_renting, -- Give it the column name number_renting
	AVG(rating) as average_rating  -- Give it the column name average_rating
FROM renting
WHERE date_renting >= '2019-01-01';
```

- Finally, count how many ratings exist since 2019-01-01.

```sql
SELECT 
	COUNT(*) AS number_renting,
	AVG(rating) AS average_rating, 
    COUNT(rating) AS number_ratings -- Add the total number of ratings here.
FROM renting
WHERE date_renting >= '2019-01-01';
```