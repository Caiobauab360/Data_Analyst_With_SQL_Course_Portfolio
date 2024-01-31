# Data Driven Decision Making with OLAP SQL queries

**Chapter description:**

The OLAP extensions in SQL are introduced and applied to aggregated data on multiple levels. These extensions are the CUBE, ROLLUP and GROUPING SETS operators.

# OLAP: CUBE operator

**Personal notes:**

*Introduction to OLAP*

OLAP, or Online Analytical Processing, is a category of tools and techniques that allow users to interactively analyze and explore data to gain insights. OLAP operations often involve aggregating and summarizing data to provide a better overview. Three key OLAP operators for this purpose are `CUBE`, `ROLLUP`, and `GROUPING SETS`.

*CUBE Operator*

The `CUBE` operator is used in SQL for OLAP operations to generate all possible subtotals and grand totals for a set of columns. It produces a result set that represents data at different levels of aggregation. The `CUBE` operator is typically used with the `GROUP BY` clause.

**Syntax:*

```sql
SELECT column1, column2, ..., aggregate_function(column)
FROM table
GROUP BY CUBE (column1, column2, ...);
```

**Example:*

```sql
-- Example using CUBE to generate subtotals and grand totals
SELECT category, city, SUM(sales)
FROM sales_data
GROUP BY CUBE (category, city);
```

In this example, the `CUBE` operator generates a result set with subtotals and grand totals for both the `category` and `city` columns, giving a comprehensive view of sales data.

The `CUBE` operator is useful for multidimensional analysis, allowing users to explore data at various levels of granularity. It's a powerful tool for business intelligence and reporting, providing a holistic view of aggregated data.

**Exercises:**

**Groups of customers**

Use the CUBE operator to extract the content of a pivot table from the database. Create a table with the total number of male and female customers from each country.

**Instruction:**

- Create a table with the total number of customers, of all female and male customers, of the number of customers for each country and the number of men and women from each country.

```sql
SELECT country, -- Extract information of a pivot table of gender and country for the number of customers
	   gender,
	   COUNT(*)
FROM customers
GROUP BY CUBE (gender, country)
ORDER BY country;
```

**Categories of movies**

Give an overview on the movies available on MovieNow. List the number of movies for different genres and release years.

**Instructions:**

- List the number of movies for different genres and the year of release on all aggregation levels by using the CUBE operator.

```sql
SELECT genre,
       year_of_release	,
       COUNT(*)
FROM movies
GROUP BY CUBE (genre, year_of_release)
ORDER BY year_of_release;
```

- Question: Which statement is NOT correct about the result table?

        "The year of release with most movies is 2014."

**Analyzing average ratings**

Prepare a table for a report about the national preferences of the customers from MovieNow comparing the average rating of movies across countries and genres.

**Instructions:**

- Augment the records of movie rentals with information about movies and customers, in this order. Use the first letter of the table names as alias.

```sql
-- Augment the records of movie rentals with information about movies and customers
SELECT *
FROM renting AS r
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id;
```

- Calculate the average rating for each country.

```sql
-- Calculate the average rating for each country
SELECT 
	c.country,
    AVG(r.rating)
FROM renting AS r
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
GROUP BY c.country;
```

- Calculate the average rating for all aggregation levels of country and genre.

```sql
SELECT 
	c.country, 
	m.genre, 
	AVG(r.rating) AS avg_rating -- Calculate the average rating 
FROM renting AS r
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
GROUP BY CUBE (m.genre, c.country); -- For all aggregation levels of country and genre
```

- Question: What is the average rating over all records, rounded to two digits?

        "7.94"

# ROLLUP

**Personal notes:**

The `ROLLUP` operator is another OLAP operator used in SQL to perform aggregations and generate subtotals and grand totals. Like the `CUBE` operator, `ROLLUP` is used in conjunction with the `GROUP BY` clause. However, it produces a more limited set of aggregations compared to `CUBE`.

**Syntax:*

```sql
SELECT column1, column2, ..., aggregate_function(column)
FROM table
GROUP BY ROLLUP (column1, column2, ...);
```

**Example:*

```sql
-- Example using ROLLUP to generate subtotals and grand totals
SELECT category, city, SUM(sales)
FROM sales_data
GROUP BY ROLLUP (category, city);
```

In this example, the `ROLLUP` operator generates a result set with subtotals and grand totals for the `category` and `city` columns. It produces a hierarchy of aggregations, creating a subtotal for each level of the specified columns.

The key difference between `CUBE` and `ROLLUP` is that `CUBE` generates all possible combinations of subtotals, while `ROLLUP` generates a more structured set of aggregations, following the specified hierarchy.

Both `CUBE` and `ROLLUP` are valuable tools for multidimensional analysis in business intelligence and reporting. They allow users to explore data at different levels of granularity and obtain insights into various aspects of the data.

**Exercises:**

**Number of customers**

You have to give an overview of the number of customers for a presentation.

**Instructions:**

- Generate a table with the total number of customers, the number of customers for each country, and the number of female and male customers for each country.

- Order the result by country and gender.

```sql
-- Count the total number of customers, the number of customers for each country, and the number of female and male customers for each country
SELECT country,
       gender,
	   COUNT(*)
FROM customers
GROUP BY ROLLUP (country, gender)
ORDER BY country, gender; -- Order the result by country and gender
```

**Analyzing preferences of genres across countries**

You are asked to study the preferences of genres across countries. Are there particular genres which are more popular in specific countries? Evaluate the preferences of customers by averaging their ratings and counting the number of movies rented from each genre.

**Instructions:**

- Augment the renting records with information about movies and customers.

```sql
-- Join the tables
SELECT *
FROM renting AS r
LEFT JOIN movies AS m
ON r.movie_id = m.movie_id
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id;
```

- Calculate the average ratings and the number of ratings for each country and each genre. Include the columns country and genre in the SELECT clause.

```sql
SELECT 
	c.country, -- Select country
	m.genre, -- Select genre
	AVG(r.rating), -- Average ratings
	COUNT(*)  -- Count number of movie rentals
FROM renting AS r
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
GROUP BY (country, genre) -- Aggregate for each country and each genre
ORDER BY c.country, m.genre;
```

- Finally, calculate the average ratings and the number of ratings for each country and genre, as well as an aggregation over all genres for each country and the overall average and total number.

```sql
-- Group by each county and genre with OLAP extension
SELECT 
	c.country, 
	m.genre, 
	AVG(r.rating) AS avg_rating, 
	COUNT(*) AS num_rating
FROM renting AS r
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
GROUP BY ROLLUP (country, genre)
ORDER BY c.country, m.genre;
```

# GROUPING SETS

**Personal notes:**

*GROUPING SETS in OLAP*

The `GROUPING SETS` clause is a powerful and flexible OLAP feature in SQL that allows you to specify multiple grouping sets within a single `GROUP BY` query. It provides a way to define several levels of aggregation in a single query, enabling a more customized and efficient approach to summarizing data.

**Syntax:*

```sql
SELECT column1, column2, ..., aggregate_function(column)
FROM table
GROUP BY GROUPING SETS ((column1, column2, ...), (column1), (column2), ...);
```

**Example:*

```sql
-- Example using GROUPING SETS to specify multiple levels of aggregation
SELECT country, genre, COUNT(*)
FROM rentings_extended
GROUP BY GROUPING SETS ((country, genre), (country), (genre), ());
```

In this example, the `GROUPING SETS` clause is used to define four groups: one for the combination of `country` and `genre`, one for `country` only, one for `genre` only, and an empty grouping set (representing the grand total). The result is a combination of the aggregations for each specified grouping set.

The `GROUPING SETS` clause is particularly useful when you want to aggregate data at various levels within the same query, avoiding the need to execute multiple separate queries or use complex `UNION` operations.

It provides a more efficient way to obtain aggregated results for different dimensions, making it a valuable tool for data analysis and reporting in OLAP scenarios.

**Exercises:**

**Queries with GROUPING SETS**

What question CANNOT be answered by the following query?
```sql
SELECT 
  r.customer_id, 
  m.genre, 
  AVG(r.rating), 
  COUNT(*)
FROM renting AS r
LEFT JOIN movies AS m
ON r.movie_id = m.movie_id
GROUP BY GROUPING SETS ((r.customer_id, m.genre), (r.customer_id), ());
```

        Answer: "What is the average rating for each genre?"

**Exploring nationality and gender of actors**

For each movie in the database, the three most important actors are identified and listed in the table actors. This table includes the nationality and gender of the actors. We are interested in how much diversity there is in the nationalities of the actors and how many actors and actresses are in the list.

**Instruction:**

- Count the number of actors in the table actors from each country, the number of male and female actors and the total number of actors.

```sql
SELECT 
	nationality, -- Select nationality of the actors
    gender, -- Select gender of the actors
    COUNT(*) -- Count the number of actors
FROM actors
GROUP BY GROUPING SETS ((nationality), (gender), ()); -- Use the correct GROUPING SETS operation
```

**Exploring rating by country and gender**

Now you will investigate the average rating of customers aggregated by country and gender.

**Instructions:**

- Select the columns country, gender, and rating and use the correct join to combine the table renting with customer.

```sql
SELECT 
	c.country, -- Select country, gender and rating
    c.gender,
    r.rating
FROM renting AS r
LEFT JOIN customers AS c -- Use the correct join
ON r.customer_id = c.customer_id;
```

- Use GROUP BY to calculate the average rating over country and gender. Order the table by country and gender.

```sql
SELECT 
	c.country, 
    c.gender,
	AVG(r.rating) -- Calculate average rating
FROM renting AS r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
GROUP BY country, gender -- Order and group by country and gender
ORDER BY country, gender;
```

- Now, use GROUPING SETS to get the same result, i.e. the average rating over country and gender.

```sql
SELECT 
	c.country, 
    c.gender,
	AVG(r.rating)
FROM renting AS r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
GROUP BY GROUPING SETS ((country, gender)); -- Group by country and gender with GROUPING SETS
```

- Report all information that is included in a pivot table for country and gender in one SQL table.

```sql
SELECT 
	c.country, 
    c.gender,
	AVG(r.rating)
FROM renting AS r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
-- Report all info from a Pivot table for country and gender
GROUP BY GROUPING SETS ((country, gender), (country), (gender), ());
```

# Bringing it all together

**Exercises:**

**Customer preference for genres**

You just saw that customers have no clear preference for more recent movies over older ones. Now the management considers investing money in movies of the best rated genres.

- Augment the records of movie rentals with information about movies. Use the first letter of the table as alias.

```sql
SELECT *
FROM renting AS r
LEFT JOIN movies AS m -- Augment the table with information about movies
ON r.movie_id = m.movie_id;
```

- Select records of movies with at least 4 ratings, starting from 2018-04-01.

```sql
SELECT *
FROM renting AS r
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE r.movie_id IN ( -- Select records of movies with at least 4 ratings
	SELECT movie_id
	FROM renting
	GROUP BY movie_id
	HAVING COUNT(rating) >= 4 )
AND r.date_renting >= '2018-04-01'; -- Select records of movie rentals since 2018-04-01
```

- For each genre, calculate the average rating (use the alias avg_rating), the number of ratings (use the alias n_rating), the number of movie rentals (use the alias n_rentals), and the number of distinct movies (use the alias n_movies).

```sql
SELECT m.genre, -- For each genre, calculate:
	   AVG(r.rating) AS avg_rating,-- The average rating and use the alias avg_rating
	   COUNT(r.rating) AS n_rating,-- The number of ratings and use the alias n_rating
	   COUNT(*) AS n_rentals,-- The number of movie rentals and use the alias n_rentals
	   COUNT (DISTINCT r.movie_id) AS n_movies -- The number of distinct movies and use the alias n_movies
FROM renting AS r
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE r.movie_id IN ( 
	SELECT movie_id
	FROM renting
	GROUP BY movie_id
	HAVING COUNT(rating) >= 3)
AND r.date_renting >= '2018-01-01'
GROUP BY m.genre;
```

- Order the table by decreasing average rating.

```sql
SELECT genre,
	   AVG(rating) AS avg_rating,
	   COUNT(rating) AS n_rating,
       COUNT(*) AS n_rentals,     
	   COUNT(DISTINCT m.movie_id) AS n_movies 
FROM renting AS r
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE r.movie_id IN ( 
	SELECT movie_id
	FROM renting
	GROUP BY movie_id
	HAVING COUNT(rating) >= 3 )
AND r.date_renting >= '2018-01-01'
GROUP BY genre
ORDER BY avg_rating DESC; -- Order the table by decreasing average rating
```

**Customer preference for actors**

The last aspect you have to analyze are customer preferences for certain actors.

**Instructions:**

- Join the tables.

```sql
-- Join the tables
SELECT *
FROM renting AS r
LEFT JOIN actsin AS ai
ON r.movie_id = ai.movie_id
LEFT JOIN actors AS a
ON ai.actor_id = a.actor_id;
```

- For each combination of the actors' nationality and gender, calculate the average rating, the number of ratings, the number of movie rentals, and the number of actors.

```sql
SELECT a.nationality,
       a.gender,
	   AVG(r.rating) AS avg_rating, -- The average rating
	   COUNT(r.rating) AS n_rating, -- The number of ratings
	   COUNT(*) AS n_rentals, -- The number of movie rentals
	   COUNT (DISTINCT a.actor_id) AS n_actors -- The number of actors
FROM renting AS r
LEFT JOIN actsin AS ai
ON ai.movie_id = r.movie_id
LEFT JOIN actors AS a
ON ai.actor_id = a.actor_id
WHERE r.movie_id IN ( 
	SELECT movie_id
	FROM renting
	GROUP BY movie_id
	HAVING COUNT(rating) >=4 )
AND r.date_renting >= '2018-04-01'
GROUP BY (a.nationality, a.gender); -- Report results for each combination of the actors' nationality and gender
```

- Provide results for all aggregation levels represented in a pivot table.

```sql
SELECT a.nationality,
       a.gender,
	   AVG(r.rating) AS avg_rating,
	   COUNT(r.rating) AS n_rating,
	   COUNT(*) AS n_rentals,
	   COUNT(DISTINCT a.actor_id) AS n_actors
FROM renting AS r
LEFT JOIN actsin AS ai
ON ai.movie_id = r.movie_id
LEFT JOIN actors AS a
ON ai.actor_id = a.actor_id
WHERE r.movie_id IN ( 
	SELECT movie_id
	FROM renting
	GROUP BY movie_id
	HAVING COUNT(rating) >= 4)
AND r.date_renting >= '2018-04-01'
GROUP BY GROUPING SETS ((a.nationality, a.gender), (a.nationality), (a.gender), ()); -- Provide results for all aggregation levels represented in a pivot table
```