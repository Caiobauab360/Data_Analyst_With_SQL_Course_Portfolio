# Data Driven Decision Making with advanced SQL queries

**Chapter description:**

The concept of nested queries and correlated nested queries is introduced and the functions EXISTS and UNION are used to categorize customers, movies, actors, and more.

# Nested query

**Personal notes:**

A nested query, also known as a subquery, is a query that appears within the `WHERE` or `HAVING` clause of another query. The entire `SELECT` block of the nested query is embedded within the outer query. This subquery is typically executed first by the query optimizer, and its result (which can be a single value or multiple values) is used by the outer query.

Here's a brief summary of nested queries:

*Types of Nested Queries:*

1. **Single-Value Subqueries:*
   - Returns a single value.
   - Commonly used with comparison operators (e.g., `=`) to compare a value with the result of the subquery.

   ```sql
   SELECT column_name
   FROM table_name
   WHERE column_name = (SELECT ...);
   ```

2. **Multiple-Value Subqueries:*
   - Returns multiple values.
   - Commonly used with the `IN` or `NOT IN` operators to filter results based on the result set of the subquery.

   ```sql
   SELECT column_name
   FROM table_name
   WHERE column_name IN (SELECT ...);
   ```

3. **Correlated Subqueries:*
   - A subquery that depends on the outer query.
   - The subquery references columns from the outer query, and its execution is dependent on each row processed by the outer query.

   ```sql
   SELECT column_name
   FROM table_name AS outer
   WHERE column_name = (SELECT ... FROM table_name AS inner WHERE inner.column = outer.column);
   ```

*Example:*

Consider an example where you want to find customers who have rented more movies than the average number of rentals:

```sql
-- Example of a nested query to find customers with more rentals than average
SELECT customer_id, customer_name
FROM customers
WHERE rentals > (SELECT AVG(rentals) FROM customers);
```

In this query, the nested subquery `(SELECT AVG(rentals) FROM customers)` calculates the average number of rentals across all customers, and the outer query then selects customers who have rented more movies than this average.

*Use Case:*

Nested queries are valuable when you need to perform a calculation or comparison based on the result of another query. They are versatile and can be used in various scenarios to filter, aggregate, or compare data within the context of a larger query.

**Exercises:**

**Often rented movies**

Your manager wants you to make a list of movies excluding those which are hardly ever watched. This list of movies will be used for advertising. List all movies with more than 5 views using a nested query which is a powerful tool to implement selection conditions.

**Instructions:**

- Select all movie IDs which have more than 5 views.

```sql
SELECT DISTINCT movie_id -- Select movie IDs with more than 5 views
FROM renting
GROUP BY movie_id
HAVING COUNT(*) > 5;
```

- Select all information about movies with more than 5 views.

```sql
SELECT *
FROM movies
WHERE movie_id IN -- Select movie IDs from the inner query
	(SELECT movie_id
	FROM renting
	GROUP BY movie_id
	HAVING COUNT(*) > 5);
```

**Frequent customers**

Report a list of customers who frequently rent movies on MovieNow.

**Instruction:**

- List all customer information for customers who rented more than 10 movies.

```sql
SELECT *
FROM customers
WHERE customer_id IN -- Select all customers with more than 10 movie rentals
	(SELECT customer_id
	FROM renting
	GROUP BY customer_id
	HAVING COUNT(*) > 10);
```

**Movies with rating above average**

For the advertising campaign your manager also needs a list of popular movies with high ratings. Report a list of movies with rating above average.

**Instructions:**

- Calculate the average over all ratings.

```sql
SELECT AVG(rating) -- Calculate the total average rating
FROM renting;
```

- Select movie IDs and calculate the average rating of movies with rating above average.

```sql
SELECT movie_id, -- Select movie IDs and calculate the average rating 
       AVG(rating)
FROM renting
GROUP BY movie_id
HAVING AVG(rating) > -- Of movies with rating above average
	(SELECT AVG(rating)
	FROM renting);
```

- The advertising team only wants a list of movie titles. Report the movie titles of all movies with average rating higher than the total average.

```sql
SELECT title -- Report the movie titles of all movies with average rating higher than the total average
FROM movies
WHERE movie_id IN
	(SELECT movie_id
	 FROM renting
     GROUP BY movie_id
     HAVING AVG(rating) > 
		(SELECT AVG(rating)
		 FROM renting));
```

# Correlated nested queries

**Personal notes:**

In correlated nested queries, the inner subquery refers to a column from a table declared in the outer query. This creates a dependency between the inner and outer queries. The nested query is evaluated once for each row processed by the outer query, and the result of the inner query depends on the specific row being processed.

*Example:*

Consider an example where you want to find customers who have rented more movies than the average number of rentals in their city:

```sql
-- Example of a correlated nested query to find customers with more rentals than city average
SELECT customer_id, customer_name, city
FROM customers AS outer
WHERE rentals > (SELECT AVG(rentals) FROM customers WHERE city = outer.city);
```

In this query:
- The outer query (`SELECT customer_id, customer_name, city FROM customers AS outer`) processes each row in the `customers` table.
- The correlated subquery `(SELECT AVG(rentals) FROM customers WHERE city = outer.city)` calculates the average number of rentals for customers in the same city as the current row in the outer query.

The result is a list of customers who have rented more movies than the average in their respective cities.

*Use Case:*

Correlated nested queries are useful when you need to perform comparisons or calculations based on values related to each row in the outer query. They are often used in scenarios where the result of the subquery depends on the specific characteristics or conditions of each row being processed in the outer query.

**Exercises:**

**Analyzing customer behavior**

A new advertising campaign is going to focus on customers who rented fewer than 5 movies. Use a correlated query to extract all customer information for the customers of interest.

**Instructions:**

- First, count number of movie rentals for customer with customer_id=45. Give the table renting the alias r.

```sql
-- Count movie rentals of customer 45
SELECT COUNT(*)
FROM renting AS r
WHERE r.customer_id = 45;
```

- Now select all columns from the customer table where the number of movie rentals is smaller than 5.

```sql
-- Select customers with less than 5 movie rentals
SELECT *
FROM customers AS c
WHERE 5 > 
	(SELECT count(*)
	FROM renting AS r
	WHERE r.customer_id = c.customer_id);
```

**Customers who gave low ratings**

Identify customers who were not satisfied with movies they watched on MovieNow. Report a list of customers with minimum rating smaller than 4.

**Instructions:**

- Calculate the minimum rating of customer with ID 7. 

```sql
-- Calculate the minimum rating of customer with ID 7
SELECT MIN(rating)
FROM renting AS r
WHERE r.customer_id = 7;
```

- Select all customers with a minimum rating smaller than 4. Use the first letter of the table as an alias.

```sql
SELECT *
FROM customers AS c
WHERE 4 > -- Select all customers with a minimum rating smaller than 4 
	(SELECT MIN(rating)
	FROM renting AS r
	WHERE r.customer_id = c.customer_id);
```

**Movies and ratings with correlated queries**

Report a list of movies that received the most attention on the movie platform, (i.e. report all movies with more than 5 ratings and all movies with an average rating higher than 8).

**Instructions:**

- Select all movies with more than 5 ratings. Use the first letter of the table as an alias.

```sql
SELECT *
FROM movies AS m
WHERE 5 < -- Select all movies with more than 5 ratings
	(SELECT COUNT(rating)
	FROM renting AS r
	WHERE r.movie_id = m.movie_id);
```

- Select all movies with an average rating higher than 8.

```sql
SELECT *
FROM movies AS m
WHERE 8 < -- Select all movies with an average rating higher than 8
	(SELECT AVG(rating)
	FROM renting AS r
	WHERE r.movie_id = m.movie_id);
```

# Queries with EXISTS

**Personal notes:**

The `EXISTS` function is a special case of a correlated nested query that allows you to check whether the result of a correlated nested query is empty or not. It returns a boolean value, either `TRUE` or `FALSE`. If the result of the correlated nested query has at least one row, indicating that it is not empty, `EXISTS` returns `TRUE`. If the nested query returns an empty table, it returns `FALSE`. When `EXISTS` returns `TRUE`, the corresponding row from the outer query is selected.

*Example:*

```sql
-- Example using EXISTS to find customers who have rented movies
SELECT customer_id, customer_name
FROM customers AS outer
WHERE EXISTS (SELECT * FROM rentals WHERE customer_id = outer.customer_id);
```

In this query:
- The outer query (`SELECT customer_id, customer_name FROM customers AS outer`) processes each row in the `customers` table.
- The correlated subquery `(SELECT * FROM rentals WHERE customer_id = outer.customer_id)` checks if the current customer in the outer query has rented any movies.

If the result of the subquery is not empty (i.e., the customer has rented movies), the row from the outer query is selected.

*NOT EXISTS*

The `NOT EXISTS` function is used to check if a subquery returns an empty set. It returns `TRUE` if the subquery is empty (no rows returned) and `FALSE` otherwise. It is essentially the opposite of `EXISTS`.

*Example:*

```sql
-- Example using NOT EXISTS to find customers who have not rented movies
SELECT customer_id, customer_name
FROM customers AS outer
WHERE NOT EXISTS (SELECT * FROM rentals WHERE customer_id = outer.customer_id);
```

In this query, the result is a list of customers who have not rented any movies.

Both `EXISTS` and `NOT EXISTS` are powerful tools for filtering results based on the presence or absence of rows in a correlated nested query. They are especially useful in scenarios where you need to check for the existence of related records in another table.

**Exercises:**

**Customers with at least one rating**

Having active customers is a key performance indicator for MovieNow. Make a list of customers who gave at least one rating.

**Instructions:**

- Select all records of movie rentals from customer with ID 115.

```sql
-- Select all records of movie rentals from customer with ID 115
SELECT *
FROM renting as r
WHERE r.customer_id = 115;
```

- Select all records of movie rentals from the customer with ID 115 and exclude records with null ratings.

```sql
SELECT *
FROM renting
WHERE rating IS NOT NULL -- Exclude those with null ratings
AND customer_id = 115;
```

- Select all records of movie rentals from the customer with ID 1, excluding null ratings.

```sql
SELECT *
FROM renting
WHERE rating IS NOT NULL -- Exclude null ratings
AND customer_id = 1; -- Select all ratings from customer with ID 1
```

- Select all customers with at least one rating. Use the first letter of the table as an alias.

```sql
SELECT *
FROM customers AS c -- Select all customers with at least one rating
WHERE EXISTS 
	(SELECT *
	FROM renting AS r
	WHERE rating IS NOT NULL 
	AND r.customer_id = c.customer_id);
```

**Actors in comedies**

In order to analyze the diversity of actors in comedies, first, report a list of actors who play in comedies and then, the number of actors for each nationality playing in comedies.

**Instructions:**

- Select the records from the table actsin of all actors who play in a Comedy. Use the first letter of the table as an alias.

```sql
SELECT *  -- Select the records of all actors who play in a Comedy
FROM actsin AS ai
LEFT JOIN movies AS m
ON ai.movie_id = m.movie_id
WHERE m.genre = 'Comedy';
```

- Make a table of the records of actors who play in a Comedy and select only the actor with ID 1.

```sql
SELECT *
FROM actsin AS ai
LEFT JOIN movies AS m
ON m.movie_id = ai.movie_id
WHERE m.genre = 'Comedy'
AND ai.actor_id = 1; -- Select only the actor with ID 1
```

- Create a list of all actors who play in a Comedy. Use the first letter of the table as an alias.

```sql
SELECT *
FROM actors AS a
WHERE EXISTS
	(SELECT *
	 FROM actsin AS ai
	 LEFT JOIN movies AS m
	 ON m.movie_id = ai.movie_id
	 WHERE m.genre = 'Comedy'
	 AND ai.actor_id = a.actor_id);
```

- Report the nationality and the number of actors for each nationality.

```sql
SELECT a.nationality, COUNT(*) -- Report the nationality and the number of actors for each nationality
FROM actors AS a
WHERE EXISTS
	(SELECT ai.actor_id
	 FROM actsin AS ai
	 LEFT JOIN movies AS m
	 ON m.movie_id = ai.movie_id
	 WHERE m.genre = 'Comedy'
	 AND ai.actor_id = a.actor_id)
GROUP BY a.nationality;
```

# Queries with UNION and INTERSECT

**Personal notes:**

*UNION*

The `UNION` operator is used to combine the results of two or more `SELECT` statements. It returns a single result set that includes all the rows from the combined queries. The `UNION` operator eliminates duplicate rows from the result set.

**Example:*

```sql
-- Example using UNION to combine results from two tables
SELECT customer_id, customer_name FROM customers
UNION
SELECT customer_id, customer_name FROM other_customers;
```

In this example, the `UNION` operator combines the results of the two `SELECT` statements, removing duplicate rows.

*INTERSECT*

The `INTERSECT` operator is used to retrieve the common rows from two or more `SELECT` statements. It returns a result set containing only the rows that appear in all the specified queries.

**Example:*

```sql
-- Example using INTERSECT to find common rows in two tables
SELECT customer_id, customer_name FROM customers
INTERSECT
SELECT customer_id, customer_name FROM preferred_customers;
```

In this example, the `INTERSECT` operator returns only the rows that are common to both tables, eliminating rows that do not appear in both result sets.

Both `UNION` and `INTERSECT` are powerful tools for combining or finding common rows across multiple tables or queries. They provide flexibility in constructing more complex queries to meet specific requirements.

**Exercises:**

**Young actors not coming from the USA**

As you've just seen, the operators UNION and INTERSECT are powerful tools when you work with two or more tables. Identify actors who are not from the USA and actors who were born after 1990.

**Instructions:**

- Report the name, nationality and the year of birth of all actors who are not from the USA.

```sql
SELECT name,  -- Report the name, nationality and the year of birth
       nationality, 
       year_of_birth
FROM actors
WHERE nationality <> 'USA'; -- Of all actors who are not from the USA
```

- Report the name, nationality and the year of birth of all actors who were born after 1990.

```sql
SELECT name, 
       nationality, 
       year_of_birth
FROM actors
WHERE year_of_birth > '1990'; -- Born after 1990
```

- Select all actors who are not from the USA and all actors who are born after 1990.

```sql
SELECT name, 
       nationality, 
       year_of_birth
FROM actors
WHERE nationality <> 'USA'
UNION -- Select all actors who are not from the USA and all actors who are born after 1990
SELECT name, 
       nationality, 
       year_of_birth
FROM actors
WHERE year_of_birth > 1990;
```

- Select all actors who are not from the USA and who are also born after 1990.

```sql
SELECT name, 
       nationality, 
       year_of_birth
FROM actors
WHERE nationality <> 'USA'
INTERSECT -- Select all actors who are not from the USA and who are also born after 1990
SELECT name, 
       nationality, 
       year_of_birth
FROM actors
WHERE year_of_birth > 1990;
```

**Dramas with high ratings**

The advertising team has a new focus. They want to draw the attention of the customers to dramas. Make a list of all movies that are in the drama genre and have an average rating higher than 9.

**Instructions:**

- Select the IDs of all dramas.

```sql
SELECT movie_id -- Select the IDs of all dramas
FROM movies
WHERE genre = 'Drama';
```

- Select the IDs of all movies with average rating higher than 9.

```sql
SELECT movie_id -- Select the IDs of all movies with average rating higher than 9
FROM renting
GROUP BY movie_id
HAVING AVG(rating) > 9;
```

- Select the IDs of all dramas with average rating higher than 9.

```sql
SELECT movie_id
FROM movies
WHERE genre = 'Drama'
INTERSECT  -- Select the IDs of all dramas with average rating higher than 9
SELECT movie_id
FROM renting
GROUP BY movie_id
HAVING AVG(rating) > 9;
```

- Select all movies of in the drama genre with an average rating higher than 9.

```sql
SELECT *
FROM movies
WHERE movie_id IN -- Select all movies of genre drama with average rating higher than 9
   (SELECT movie_id
    FROM movies
    WHERE genre = 'Drama'
    INTERSECT
    SELECT movie_id
    FROM renting
    GROUP BY movie_id
    HAVING AVG(rating)>9);
```