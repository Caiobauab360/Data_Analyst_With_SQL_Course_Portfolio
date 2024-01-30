# Aggregate Functions

**Chapter description:**

SQL allows you to zoom in and out to better understand an entire dataset, its subsets, and its individual records. You'll learn to summarize data using aggregate functions and perform basic arithmetic calculations inside queries to gain insights into what makes a successful film.

# Summarizing data

**Personal notes:**

One way to summarize data is by using SQL's aggregate functions. An aggregate function performs a calculation on multiple values and returns a single value. We've already seen the COUNT function, and now we'll explore functions used to calculate average, sum, minimum, and maximum for a specified field.

**Aggregate Functions:*
- AVG() - To find the average;

- SUM() - To perform the sum;

- MIN() - To find the minimum value;

- MAX() - To find the maximum value;

- COUNT() - Provides a total of non-null or non-missing records in a field.

These aggregate functions are used after the SELECT, just like we use COUNT(). The functions are used on the field (column) and not the records (rows).

**Non-Numerical Data:*

While these functions might seem mathematical, we can use many of them with both numerical and non-numerical fields. The average (AVG) and sum (SUM) are the two functions that we can only use on numerical fields since they involve arithmetic. We can use COUNT, MIN, and MAX with non-numerical fields. MIN and MAX will give the record that is figuratively the lowest or highest. The lowest might mean the letter A when dealing with strings or the oldest date when dealing with dates.

**Aliasing When Summarizing:*

Note how all the results in the query automatically updated the field name to the aggregate function used. It's a best practice to use an alias when summarizing data so that our results are clear to anyone reading our code.
```sql
SELECT MIN(country) AS min_country
FROM films;
```

**Exercises:**

**Practice with aggregate functions**

Now let's try extracting summary information from a table using these new aggregate functions. Summarizing is helpful in real life when extracting top-line details from your dataset. Perhaps you'd like to know how old the oldest film in the films table is, what the most expensive film is, or how many films you have listed.

Now it's your turn to get more insights about the films table!

**Instructions:**

- Use the SUM() function to calculate the total duration of all films and alias with total_duration.

```sql
-- Query the sum of film durations
SELECT SUM(duration) AS total_duration
FROM films;
```

- Calculate the average duration of all films and alias with average_duration.

```sql
-- Calculate the average duration of all films
SELECT AVG(duration) AS average_duration
FROM films;
```

- Find the most recent release_year in the films table, aliasing as latest_year.

```sql
-- Find the latest release_year
SELECT MAX(release_year) AS latest_year
FROM films;
```

- Find the duration of the shortest film and use the alias shortest_film.

```sql
-- Find the duration of the shortest film
SELECT MIN(duration) AS shortest_film
FROM films;
```

# Summarizing subsets

**Personal notes:**

**Using WHERE with Aggregate Functions:*

We can combine aggregate functions with the WHERE clause to gain more insights from our data. This is because the WHERE clause is executed before the SELECT statement.
```sql
SELECT AVG(budget) AS avg_budget
FROM films
WHERE release_year >= 2010;
```

**ROUND():*

In SQL, we can use ROUND() to round our number to a specified decimal place. There are two parameters for ROUND(): the number we want to round and the decimal place we want to round to. It can only be used with numerical fields.
```sql
-- Example: Recalculating the same average budget seen before using ROUND and specifying we want to round to two decimal places because we're dealing with currency:
SELECT ROUND(AVG(budget), 2) AS avg_budget
FROM films
WHERE release_year >= 2010;
```

**ROUND() to a Whole Number:*

The second parameter in our ROUND() function is optional, so we can omit it if we want to round to a whole number. Omitting the number or declaring it as 0 results in the same outcome.

**ROUND() using a Negative Parameter:*

We could also pass a negative number as the second parameter and still get a result. For example, here, the function is rounding to the left of the decimal point instead of the right.
```sql
SELECT ROUND(AVG(budget), -5) AS avg_budget
FROM films
WHERE release_year >= 2010;
```

**Exercises:**

**Combining aggregate functions with WHERE**

When combining aggregate functions with WHERE, you get a powerful tool that allows you to get more granular with your insights, for example, to get the total budget of movies made from the year 2010 onwards.

This combination is useful when you only want to summarize a subset of your data. In your film-industry role, as an example, you may like to summarize each certification category to compare how they each perform or if one certification has a higher average budget than another.

Let's see what insights you can gain about the financials in the dataset.

**Instructions:**

- Use SUM() to calculate the total gross for all films made in the year 2000 or later, and use the alias total_gross.

```sql
-- Calculate the sum of gross from the year 2000 or later
SELECT SUM(gross) AS total_gross
FROM films
WHERE release_year >= 2000;
```

- Calculate the average amount grossed by all films whose titles start with the letter 'A' and alias with avg_gross_A.

```sql
-- Calculate the average gross of films that start with A
SELECT AVG(gross) AS avg_gross_A
FROM films
WHERE title LIKE 'A%';
```

- Calculate the lowest gross film in 1994 and use the alias lowest_gross.
```sql
-- Calculate the lowest gross film in 1994
SELECT MIN(gross) AS lowest_gross
FROM films
WHERE release_year = 1994
```

- Calculate the highest gross film between 2000 and 2012, inclusive, and use the alias highest_gross.

```sql
-- Calculate the highest gross film released between 2000-2012
SELECT MAX(gross) AS highest_gross
FROM films
WHERE release_year between 2000 AND 2012;
```

**Using ROUND()**

Aggregate functions work great with numerical values; however, these results can sometimes get unwieldy when dealing with long decimal values. Luckily, SQL provides you with the ROUND() function to tame these long decimals.

If asked to give the average budget of your films, ten decimal places is not necessary. Instead, you can round to two decimal places to create results that make more sense for currency.

Now you try!

**Instructions:**

- Calculate the average facebook_likes to one decimal place and assign to the alias, avg_facebook_likes.

```sql
-- Round the average number of facebook_likes to one decimal place
SELECT ROUND(AVG(facebook_likes), 1) AS avg_facebook_likes
FROM reviews;
```

**ROUND() with a negative parameter**

A useful thing you can do with ROUND() is have a negative number as the decimal place parameter. This can come in handy if your manager only needs to know the average number of facebook_likes to the hundreds since granularity below one hundred likes won't impact decision making.

Social media plays a significant role in determining success. If a movie trailer is posted and barely gets any likes, the movie itself may not be successful. Remember how 2020's "Sonic the Hedgehog" movie got a revamp after the public saw the trailer?

Let's apply this to other parts of the dataset and see what the benchmark is for movie budgets so, in the future, it's clear whether the film is above or below budget.

**Instructions:**

- Calculate the average budget from the films table, aliased as avg_budget_thousands, and round to the nearest thousand.

```sql
-- Calculate the average budget rounded to the thousands
SELECT ROUND(AVG(budget), -3) AS avg_budget_thousands
FROM films;
```


# Aliasing and arithmetic

**Personal notes:**

We can perform basic arithmetic with symbols like plus, minus, multiplication, and division (+, -, *, /). Using parentheses with arithmetic indicates to the processor when the calculation needs to be executed. Some basic examples of how we can use arithmetic in SQL:
```sql
SELECT (4 + 3);
SELECT (4 - 3);
SELECT (4 * 3);
SELECT (4 / 3);
```
One needs to be careful with the division example. Similar to other programming languages, SQL assumes that we want to get an integer as a result if we divide an integer by another integer. For a correct division result, we can add decimals to our numbers. For example:
```sql
SELECT (4.0 / 3.0);
```
The main difference is that aggregate functions like SUM perform their operations on fields vertically, while arithmetic functions sum records horizontally.

We will always need to use an alias when summarizing data with both aggregate and arithmetic functions. For example:
```sql
SELECT MAX(budget) AS max_budget,
		MAX(duration) AS max_duration
FROM films;
```
*Aliases defined in the SELECT keyword cannot be used in the WHERE keyword due to the order of execution, causing an error when trying to run the code. It's important to remember the order of execution.

**Exercises:**

**Using arithmetic**

SQL arithmetic comes in handy when your table is missing a metric you want to review. Suppose you have some data on movie ticket sales, but the table only has fields for ticket price and discount. In that case, you could combine these by subtracting the discount from the ticket price to get the amount the film-goer paid.

You have seen that SQL can act strangely when dividing integers. What is the result if you divide a discount of two dollars by the paid_price of ten dollars to get the discount percentage?

        "0"

**Aliasing with functions**

Aliasing can be a lifesaver, especially as we start to do more complex SQL queries with multiple criteria. Aliases help you keep your code clean and readable. For example, if you want to find the MAX() value of several fields without aliasing, you'll end up with the result with several columns called max and no idea which is which. You can fix this with aliasing.

Now, it's over to you to clean up the following queries.

**Instructions:**

- Select the title and duration in hours for all films and alias as duration_hours; since the current durations are in minutes, you'll need to divide duration by 60.0.

```sql
-- Calculate the title and duration_hours from films
SELECT title, 
        (duration / 60.0) as duration_hours
FROM films;
```

- Calculate the percentage of people who are no longer alive and alias the result as percentage_dead.

```sql
-- Calculate the percentage of people who are no longer alive
SELECT COUNT(deathdate) * 100.0 / COUNT(*) AS percentage_dead
FROM people;
```

- Find how many decades (period of ten years) the films table covers by using MIN() and MAX(); alias as number_of_decades.

```sql
-- Find the number of decades in the films table
SELECT (MAX(release_year) - MIN(release_year)) / 10.0 AS number_of_decades
FROM films;
```

**Rounding results**

You found some valuable insights in the previous exercise, but many of the results were inconveniently long. We forgot to round! We won't make you redo them all; however, you'll update the worst offender in this exercise.

**Instructions:**

- Update the query by adding ROUND() around the calculation and round to two decimal places.

```sql
-- Round duration_hours to two decimal places
SELECT title, ROUND(duration / 60.0, 2) AS duration_hours
FROM films;
```