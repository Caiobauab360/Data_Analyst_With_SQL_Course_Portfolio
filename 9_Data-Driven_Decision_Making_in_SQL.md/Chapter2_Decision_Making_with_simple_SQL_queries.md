# Decision Making with simple SQL queries

**Chapter description:**

More complex queries with GROUP BY, LEFT JOIN and sub-queries are used to gain insight into customer preferences.

# Grouping movies

**Personal notes:**

The use of the `GROUP BY` clause allows us to apply aggregations to groups within a table, providing a way to analyze the success and potential of a business by looking at groups of customers or groups of products together.

*GROUP BY Applications*

When analyzing data, it's often beneficial to group information based on specific columns. For instance, let's say we have a `movies` table, and we want to find the average rating for each genre. We can achieve this using the `GROUP BY` clause:

```sql
SELECT genre, AVG(rating) AS avg_rating
FROM movies
GROUP BY genre;
```

In this example, the result will show each unique genre along with its corresponding average rating.

*GROUP BY with HAVING*

The `HAVING` clause is used in combination with `GROUP BY` to filter the results based on aggregated values. It allows you to specify conditions for groups rather than individual rows.

For example, if we want to find genres with an average rating greater than 4.0:

```sql
SELECT genre, AVG(rating) AS avg_rating
FROM movies
GROUP BY genre
HAVING AVG(rating) > 4.0;
```

This query retrieves genres with an average rating higher than 4.0.

*GROUP BY and Aggregation Functions*

You can use various aggregation functions along with `GROUP BY` to extract valuable insights from your data. Here are a few examples:

- **Count the number of movies in each genre:*

  ```sql
  SELECT genre, COUNT(*) AS movie_count
  FROM movies
  GROUP BY genre;
  ```

- **Find the maximum and minimum ratings for each genre:*

  ```sql
  SELECT genre, MAX(rating) AS max_rating, MIN(rating) AS min_rating
  FROM movies
  GROUP BY genre;
  ```

- **Calculate the total revenue for each genre:*

  Assuming there's a `revenue` column in the `movies` table.

  ```sql
  SELECT genre, SUM(revenue) AS total_revenue
  FROM movies
  GROUP BY genre;
  ```

Mastering the use of `GROUP BY` in SQL queries allows you to analyze data at a more granular level, providing insights into specific groups and facilitating data-driven decision-making.

**Exercises:**

**First account for each country.**

Conduct an analysis to see when the first customer accounts were created for each country.

**Instructions:**

- Create a table with a row for each country and columns for the country name and the date when the first customer account was created.

- Use the alias first_account for the column with the dates.

- Order by date in ascending order.

```sql
SELECT country, -- For each country report the earliest date when an account was created
	MIN(date_account_start) AS first_account
FROM customers
GROUP BY country
ORDER BY first_account;
```

**Average movie ratings**

For each movie the average rating, the number of ratings and the number of views has to be reported. Generate a table with meaningful column names.

**Instructions:**

- Group the data in the table renting by movie_id and report the ID and the average rating.

```sql
SELECT movie_id, 
       AVG(rating)   -- Calculate average rating per movie
FROM renting
GROUP BY movie_id;
```

- Add two columns for the number of ratings and the number of movie rentals to the results table.

- Use alias names avg_rating, number_rating and number_renting for the corresponding columns.

```sql
SELECT movie_id, 
       AVG(rating) AS avg_rating, -- Use as alias avg_rating
       COUNT(rating) AS number_rating, -- Add column for number of ratings with alias number_rating
       COUNT(*) AS number_renting -- Add column for number of movie rentals with alias number_renting
FROM renting
GROUP BY movie_id;
```

- Order the rows of the table by the average rating such that it is in decreasing order.

- Observe what happens to NULL values.

```sql
SELECT movie_id, 
       AVG(rating) AS avg_rating,
       COUNT(rating) AS number_ratings,
       COUNT(*) AS number_renting
FROM renting
GROUP BY movie_id
ORDER BY avg_rating DESC; -- Order by average rating in decreasing order
```

- Question: Which statement is true for the movie with average rating null?

        "The average is null because all of the ratings of the movie are null."

**Average rating per customer**

Similar to what you just did, you will now look at the average movie ratings, this time for customers. So you will obtain a table with the average rating given by each customer. Further, you will include the number of ratings and the number of movie rentals per customer. You will report these summary statistics only for customers with more than 7 movie rentals and order them in ascending order by the average rating.

**Instructions:**

- Group the data in the table renting by customer_id and report the customer_id, the average rating, the number of ratings and the number of movie rentals.

- Select only customers with more than 7 movie rentals.

- Order the resulting table by the average rating in ascending order.

```sql
SELECT customer_id, -- Report the customer_id
      AVG(rating),  -- Report the average rating per customer
      COUNT(rating),  -- Report the number of ratings per customer
      COUNT(*)  -- Report the number of movie rentals per customer
FROM renting
GROUP BY customer_id
HAVING COUNT(*) > 7 -- Select only customers with more than 7 movie rentals
ORDER BY AVG(rating); -- Order by the average rating in ascending order
```

# Joining movie ratings with customer data

**Personal notes:**

SQL queries often involve combining or joining data from multiple tables. One common type of join is the `LEFT JOIN`, which retains all rows from the left table and includes matching rows from the right table. If there's no match, the result will contain NULL values for columns from the right table.

*LEFT JOIN Example*

Let's say we have two tables: `movies` and `ratings`. The `movies` table contains information about movies, and the `ratings` table contains customer ratings for those movies.

```sql
-- Example LEFT JOIN
SELECT movies.movie_id, movies.title, ratings.rating, ratings.customer_id
FROM movies
LEFT JOIN ratings ON movies.movie_id = ratings.movie_id;
```

In this query:

- `movies` is the left table, and `ratings` is the right table.
- We're selecting the movie ID, title, rating, and customer ID.
- The `LEFT JOIN` is based on the condition `movies.movie_id = ratings.movie_id`.

The result will include all movies from the `movies` table, and for each movie, it will show the rating given by customers if available. If there's no rating for a movie, the corresponding columns from the `ratings` table will be NULL.

*Use Case: Analyzing Movie Ratings with Customer Data*

You might use a `LEFT JOIN` when you want to analyze movie ratings along with customer information, even if some movies haven't been rated. For example, you could find the average rating for each customer, including customers who haven't rated any movies:

```sql
SELECT customers.customer_id, AVG(ratings.rating) AS avg_rating
FROM customers
LEFT JOIN ratings ON customers.customer_id = ratings.customer_id
GROUP BY customers.customer_id;
```

In this query, the `LEFT JOIN` ensures that all customers are included in the result, and it calculates the average rating for each customer. If a customer hasn't rated any movies, their average rating will be NULL.

Understanding and mastering different types of joins is crucial for efficiently combining data from multiple tables and extracting meaningful insights from relational databases.

**Exercises:**

**Join renting and customers**

For many analyses it is necessary to add customer information to the data in the table renting.

**Instructions:**

- Augment the table renting with all columns from the table customers with a LEFT JOIN.

- Use as alias' for the tables r and c respectively.

```sql
SELECT * -- Join renting with customers
FROM renting AS r
LEFT JOIN customers AS c
ON c.customer_id = r.customer_id;
```

- Select only records from customers coming from Belgium.

```sql
SELECT *
FROM renting AS r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
WHERE country = 'Belgium'; -- Select only records from customers coming from Belgium
```

- Average ratings of customers from Belgium.

```sql
SELECT AVG(rating) -- Average ratings of customers from Belgium
FROM renting AS r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
WHERE c.country='Belgium';
```

**Aggregating revenue, rentals and active customers**

The management of MovieNow wants to report key performance indicators (KPIs) for the performance of the company in 2018. They are interested in measuring the financial successes as well as user engagement. Important KPIs are, therefore, the revenue coming from movie rentals, the number of movie rentals and the number of active customers.

**Instructions:**

- First, you need to join movies on renting to include the renting_price from the movies table for each renting record.

- Use as alias' for the tables m and r respectively.

```sql
SELECT *
FROM renting AS r
LEFT JOIN movies AS m -- Choose the correct join statment
ON r.movie_id = m.movie_id;
```

- Calculate the revenue coming from movie rentals, the number of movie rentals and the number of customers who rented a movie.

```sql
SELECT 
	SUM(m.renting_price), -- Get the revenue from movie rentals
	COUNT(*), -- Count the number of rentals
	COUNT(DISTINCT customer_id)  -- Count the number of customers
FROM renting AS r
LEFT JOIN movies AS m
ON r.movie_id = m.movie_id;
```

- Now, you can report these values for the year 2018. Calculate the revenue in 2018, the number of movie rentals and the number of active customers in 2018. An active customer is a customer who rented at least one movie in 2018.

```sql
SELECT 
	SUM(m.renting_price), 
	COUNT(*), 
	COUNT(DISTINCT r.customer_id)
FROM renting AS r
LEFT JOIN movies AS m
ON r.movie_id = m.movie_id
-- Only look at movie rentals in 2018
WHERE date_renting BETWEEN '2018-01-01' AND '2018-12-31';
```

**Movies and actors**

You are asked to give an overview of which actors play in which movie.

**Instructions:**

-Create a list of actor names and movie titles in which they act. Make sure that each combination of actor and movie appears only once.

- Use as an alias for the table actsin the two letters ai.

```sql
SELECT DISTINCT a.name, -- Create a list of movie titles and actor names
       m.title
FROM actsin AS ai
LEFT JOIN movies AS m
ON m.movie_id = ai.movie_id
LEFT JOIN actors AS a
ON a.actor_id = ai.actor_id;
```

# Money spent per customer with sub-queries

**Personal notes:**

In this lesson, we'll explore how to calculate the amount of money each customer spent on movie rentals at MovieNow using subqueries.

*Subqueries in SELECT*

Subqueries in the `SELECT` clause allow us to perform calculations or retrieve single values from another table and include those results in the main query. This is often used when you want to use the result of a subquery as a column in the main query.

Let's consider an example where we want to find the total amount of money each customer spent on movie rentals. We have two tables: `customers` and `rentals`. The `rentals` table contains information about movie rentals, including the rental cost.

```sql
-- Example of using a subquery in SELECT
SELECT
  customer_id,
  (SELECT SUM(rental_cost) FROM rentals WHERE rentals.customer_id = customers.customer_id) AS total_spent
FROM
  customers;
```

In this query:

- We're selecting the `customer_id` from the `customers` table.
- The subquery `(SELECT SUM(rental_cost) FROM rentals WHERE rentals.customer_id = customers.customer_id)` calculates the total rental cost for each customer by summing the `rental_cost` from the `rentals` table where the customer IDs match.
- The result is a list of customer IDs along with the corresponding total amount spent by each customer.

*Use Case: Money Spent per Customer*

You might use subqueries in the `SELECT` clause when you need to perform calculations or retrieve specific values from related tables and include those results as columns in the main query. Calculating the total amount spent per customer is just one example of how subqueries in `SELECT` can be beneficial.

Understanding how to use subqueries effectively is essential for performing complex analyses and generating insightful reports from relational databases.

**Exercises:**

**Income from movies**

How much income did each movie generate? To answer this question subsequent SELECT statements can be used.

**Instructions:**

- Use a join to get the movie title and price for each movie rental.

```sql
SELECT m.title, -- Use a join to get the movie title and price for each movie rental
       m.renting_price
FROM renting AS r
LEFT JOIN movies AS m
ON r.movie_id = m.movie_id;
```

- Report the total income for each movie.

- Order the result by decreasing income.

```sql
SELECT rm.title, -- Report the income from movie rentals for each movie 
       SUM(rm.renting_price) AS income_movie
FROM
       (SELECT m.title, 
               m.renting_price
       FROM renting AS r
       LEFT JOIN movies AS m
       ON r.movie_id=m.movie_id) AS rm
GROUP BY rm.title
ORDER BY income_movie DESC; -- Order the result by decreasing income
```

- Question: Which statement about the movie 'Django Unchained' is NOT correct?

        "The income from 'Django Unchained' is higher than from 'Simone'."

**Age of actors from the USA**

Now you will explore the age of American actors and actresses. Report the date of birth of the oldest and youngest US actor and actress.

**Instructions:**

- Create a subsequent SELECT statements in the FROM clause to get all information about actors from the USA.

- Give the subsequent SELECT statement the alias a.

- Report for actors from the USA the year of birth of the oldest and the year of birth of the youngest actor and actress.

```sql
SELECT a.gender, -- Report for male and female actors from the USA 
       MAX(a.year_of_birth), -- The year of birth of the oldest actor
       MIN(a.year_of_birth) -- The year of birth of the youngest actor
FROM
   (SELECT * -- Use a subsequen SELECT to get all information about actors from the USA
   FROM actors
   WHERE nationality = 'USA') AS a -- Give the table the name a
GROUP BY a.gender;
```

# Identify favorite actors of customer groups

**Personal notes:**

In this lesson, we'll combine different SQL statements for complex queries to explore who the favorite actors are for certain groups of customers. We'll use a combination of `LEFT JOIN`, `WHERE`, `GROUP BY`, `HAVING`, and `ORDER BY` statements to answer this question.

*Example Query*

Let's assume we have three tables: `customers`, `rentals`, and `movies`. We want to find out who the favorite actors are for customers who spent a certain amount or more on movie rentals. We'll use a combination of statements to achieve this.

```sql
-- Example of combining SQL statements for complex queries
SELECT
  customers.customer_id,
  customers.customer_name,
  actors.actor_name,
  COUNT(*) AS movies_rented
FROM
  customers
LEFT JOIN rentals ON customers.customer_id = rentals.customer_id
LEFT JOIN movies ON rentals.movie_id = movies.movie_id
LEFT JOIN movie_actors ON movies.movie_id = movie_actors.movie_id
LEFT JOIN actors ON movie_actors.actor_id = actors.actor_id
WHERE
  rentals.rental_cost >= 20
GROUP BY
  customers.customer_id, actors.actor_id
HAVING
  COUNT(*) >= 2
ORDER BY
  customers.customer_id, movies_rented DESC;
```

In this query:

- We're selecting the `customer_id`, `customer_name`, `actor_name`, and the count of movies rented (`movies_rented`).
- We use `LEFT JOIN` to combine information from multiple tables (`customers`, `rentals`, `movies`, `movie_actors`, and `actors`).
- The `WHERE` clause filters the results to include only rentals with a rental cost of $20 or more.
- The `GROUP BY` clause groups the results by customer and actor.
- The `HAVING` clause filters the groups to include only those with a count of rented movies (`movies_rented`) greater than or equal to 2.
- The `ORDER BY` clause orders the results by customer ID and the count of movies rented in descending order.

*Use Case: Identifying Favorite Actors of Customer Groups*

This type of query can help the MovieNow platform understand which actors are popular among customers who are willing to spend a significant amount on movie rentals. It provides insights into customer preferences and can influence content acquisition decisions for the streaming platform.

Combining various SQL statements allows for the creation of powerful queries that address complex questions and provide valuable insights for data-driven decision-making.

**Exercises:**

**Identify favorite movies for a group of customers**

Which is the favorite movie on MovieNow? Answer this question for a specific group of customers: for all customers born in the 70s.

**Instructions:**

- Augment the table renting with customer information and information about the movies.

- For each join use the first letter of the table name as alias.

```sql
SELECT *
FROM renting AS r
LEFT JOIN customers AS c   -- Add customer information
ON c.customer_id = r.customer_id
LEFT JOIN movies AS m   -- Add movie information
ON m.movie_id = r.movie_id;
```

- Select only those records of customers born in the 70s.

```sql
SELECT *
FROM renting AS r
LEFT JOIN customers AS c
ON c.customer_id = r.customer_id
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE date_of_birth BETWEEN '1970-01-01' AND '1979-12-31'; -- Select customers born in the 70s
```

- For each movie, report the number of times it was rented, as well as the average rating. Limit your results to customers born in the 1970s.

```sql
SELECT m.title, 
COUNT(*), -- Report number of views per movie
AVG(r.rating) -- Add the average rating for each movie
FROM renting AS r
LEFT JOIN customers AS c
ON c.customer_id = r.customer_id
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE c.date_of_birth BETWEEN '1970-01-01' AND '1979-12-31'
GROUP BY m.title;
```

- Remove those movies from the table with only one rental.

- Order the result table such that movies with highest rating come first.

```sql
SELECT m.title, 
COUNT(*),
AVG(r.rating)
FROM renting AS r
LEFT JOIN customers AS c
ON c.customer_id = r.customer_id
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE c.date_of_birth BETWEEN '1970-01-01' AND '1979-12-31'
GROUP BY m.title
HAVING COUNT(*) > 1 -- Remove movies with only one rental
ORDER BY AVG(r.rating); -- Order with highest rating first
```

**Identify favorite actors for Spain**

You're now going to explore actor popularity in Spain. Use as alias the first letter of the table, except for the table actsin use ai instead.

**Instructions:**

- Augment the table renting with information about customers and actors.

```sql
SELECT *
FROM renting as r 
LEFT JOIN customers AS c  -- Augment table renting with information about customers 
ON r.customer_id = c.customer_id
LEFT JOIN actsin AS ai  -- Augment the table renting with the table actsin
ON r.movie_id = ai.movie_id 
LEFT JOIN actors AS a  -- Augment table renting with information about actors
ON ai.actor_id = a.actor_id;
```

- Report the number of movie rentals and the average rating for each actor, separately for male and female customers.

- Report only actors with more than 5 movie rentals.

```sql
SELECT a.name,  c.gender,
       COUNT(*) AS number_views, 
       AVG(r.rating) AS avg_rating
FROM renting as r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
LEFT JOIN actsin as ai
ON r.movie_id = ai.movie_id
LEFT JOIN actors as a
ON ai.actor_id = a.actor_id
GROUP BY a.name, c.gender -- For each actor, separately for male and female customers
HAVING AVG(r.rating) IS NOT NULL 
AND COUNT(*) > 5 -- Report only actors with more than 5 movie rentals
ORDER BY avg_rating DESC, number_views DESC;
```

- Now, report the favorite actors only for customers from Spain.

```sql
SELECT a.name,  c.gender,
       COUNT(*) AS number_views, 
       AVG(r.rating) AS avg_rating
FROM renting as r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
LEFT JOIN actsin as ai
ON r.movie_id = ai.movie_id
LEFT JOIN actors as a
ON ai.actor_id = a.actor_id
WHERE country ='Spain' -- Select only customers from Spain
GROUP BY a.name, c.gender
HAVING AVG(r.rating) IS NOT NULL 
  AND COUNT(*) > 5 
ORDER BY avg_rating DESC, number_views DESC;
```

**KPIs per country**

In chapter 1 you were asked to provide a report about the development of the company. This time you have to prepare a similar report with KPIs for each country separately. Your manager is interested in the total number of movie rentals, the average rating of all movies and the total revenue for each country since the beginning of 2019.

**Instructions:**

- Augment the table renting with information about customers and movies.

- Use as alias the first latter of the table name.

- Select only records about rentals since beginning of 2019.

```sql
SELECT *
FROM renting AS r -- Augment the table renting with information about customers
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
LEFT JOIN movies AS m -- Augment the table renting with information about movies
ON r.movie_id = m.movie_id
WHERE date_renting >= '2019-01-01'; -- Select only records about rentals since the beginning of 2019
```

- Calculate the number of movie rentals.

- Calculate the average rating.

- Calculate the revenue from movie rentals.

- Report these KPIs for each country.

```sql
SELECT 
	c.country,                    -- For each country report
	COUNT(r.movie_id) AS number_renting, -- The number of movie rentals
	AVG(r.rating) AS average_rating, -- The average rating
	SUM(renting_price) AS revenue         -- The revenue from movie rentals
FROM renting AS r
LEFT JOIN customers AS c
ON c.customer_id = r.customer_id
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE date_renting >= '2019-01-01'
GROUP BY c.country;
```