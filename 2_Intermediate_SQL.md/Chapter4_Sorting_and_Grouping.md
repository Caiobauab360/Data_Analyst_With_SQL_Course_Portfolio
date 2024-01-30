# Sorting and Grouping

**Chapter description:**

This final chapter teaches you how to sort and group data. These skills will take your analyses to a new level by helping you uncover critical business insights and identify trends and performance. You'll get hands-on experience to determine which films performed the best and how movie durations and budgets changed over time.

# Sorting results

**Personal notes:**

To gain more insights from our data, we can sort and group our results to put our data in a specific order.

**ORDER BY*

In SQL, the ORDER BY keyword is used to sort the results by one or more fields. When used alone, it is written after the FROM statement. ORDER BY will sort in ascending order by default.
```sql
SELECT title, budget
FROM films
ORDER BY budget; 
```
Adding the ASC keyword to our query to clarify that we are sorting in ascending order. We can use DESC to sort the results in descending order.
```sql
SELECT title, budget
FROM films
ORDER BY budget DESC;
```
We can add a WHERE clause before ORDER BY to filter the budget field only for non-null values and improve our results.
```sql
SELECT title, budget
FROM films
WHERE budget IS NOT NULL
ORDER BY budget DESC;
```
Note that we don't need to select the field we are sorting. For example, here's a query where we sort by release year and only look at the title.
```sql
SELECT title
FROM films
ORDER BY release_year; 
```
However, it's a good idea to include the field we are sorting in the SELECT statement for clarity.

**ORDER BY Multiple Fields*

ORDER BY can also be used to sort by multiple fields. It will sort by the first specified field and then by the next one, and so on. To specify multiple fields, we separate the field names with a comma. The second field we sort by can be thought of as a tiebreaker when the first field is not decisive in determining the order.
```sql
SELECT title, wins
FROM best_movies
ORDER BY wins DESC;
```
We can break this tie by adding a second sorting field to see which movie has the most wins and the highest IMDb score.
```sql
SELECT title, wins, imdb_score
FROM best_movies
ORDER BY wins DESC, imdb_score DESC;
```
We can also select a different order for each field we are sorting:
```sql
SELECT birthdate, name
FROM people
ORDER BY birthdate, name DESC; 
```

**Order of Execution:*
The FROM statement is executed first, followed by WHERE, then SELECT, ORDER BY, and finally, LIMIT.

**Exercises:**

**Sorting text**

SQL provides you with the ORDER BY keyword to sort one or more fields from your data. It can do this multi-directionally and helps make results easy to interpret.

How does ORDER BY sort a column of text values by default?

        "Alphabetically (A-Z)"

**Sorting single fields**

Now that you understand how ORDER BY works, you'll put it into practice. In this exercise, you'll work on sorting single fields only. This can be helpful to extract quick insights such as the top-grossing or top-scoring film.

The following exercises will help you gain further insights into the film database.

**Instructions:**

-Select the name of each person in the people table, sorted alphabetically.

        -- Select name from people and sort alphabetically
        SELECT name
        FROM people
        ORDER BY name;

- Select the title and duration for every film, from longest duration to shortest.

        -- Select the title and duration from longest to shortest film
        SELECT title, duration
        FROM films
        ORDER BY duration DESC;

**Sorting multiple fields**

ORDER BY can also be used to sort on multiple fields. It will sort by the first field specified, then sort by the next, and so on. As an example, you may want to sort the people data by age and keep the names in alphabetical order.

Try using ORDER BY to sort multiple columns.

**Instructions:**

- Select the release_year, duration, and title of films ordered by their release year and duration, in that order.

        -- Select the release year, duration, and title sorted by release year and duration
        SELECT release_year, duration, title
        FROM films
        ORDER BY release_year, duration;

- Select the certification, release_year, and title from films ordered first by certification (alphabetically) and second by release year, starting with the most recent year.

        -- Select the certification, release year, and title sorted by certification and release year
        SELECT certification, release_year, title
        FROM films
        ORDER BY certification, release_year DESC;

# Grouping data

**Personal notes:**

Often, we need to summarize data from a specific group of results.

**GROUP BY Single Fields*

SQL allows us to group with the GROUP BY clause. It is commonly used with aggregate functions to provide summary statistics.
```sql
SELECT certification, COUNT(title) AS title_count
FROM films
GROUP BY certification;
```
SQL will return an error if we try to SELECT a field that is not in our GROUP BY clause. We need to fix this by adding an aggregate function around the title.

**GROUP BY Multiple Fields*

We can use GROUP BY on multiple fields similar to ORDER BY. The query here selects and groups by certification and language while aggregating the title count:
```sql
SELECT certification, language, COUNT(title) AS title_count
FROM films
GROUP BY certification, language; 
```

**GROUP BY with ORDER BY*

We can combine GROUP BY with ORDER BY to group our results, perform a calculation, and then sort our results. For example, we can refine one of our previous queries by sorting the results by the title count in descending order:
```sql
SELECT
	certification,
	COUNT(title) AS title_count
FROM films
GROUP BY certification
ORDER BY title_count DESC;
```
ORDER BY is always written after GROUP BY, and note that we can reference the alias in the query.

**Order of Execution:*

FROM - SELECT - GROUP BY - ORDER BY.

**Exercises:**

**GROUP BY single fields**

GROUP BY is a SQL keyword that allows you to group and summarize results with the additional use of aggregate functions. For example, films can be grouped by the certification and language before counting the film titles in each group. This allows you to see how many films had a particular certification and language grouping.

In the following steps, you'll summarize other groups of films to learn more about the films in your database.

**Instructions:**

- Select the release_year and count of films released in each year aliased as film_count.

        -- Find the release_year and film_count of each year
        SELECT release_year, COUNT(films) AS film_count
        FROM films
        GROUP BY release_year;

- Select the release_year and average duration aliased as avg_duration of all films, grouped by release_year.

        -- Find the release_year and average duration of films for each year
        SELECT release_year, AVG(duration) AS avg_duration
        FROM films
        GROUP BY release_year;

**GROUP BY multiple fields**

GROUP BY becomes more powerful when used across multiple fields or combined with ORDER BY and LIMIT.

Perhaps you're interested in learning about budget changes throughout the years in individual countries. You'll use grouping in this exercise to look at the maximum budget for each country in each year there is data available.

**Instruction:**

- Select the release_year, country, and the maximum budget aliased as max_budget for each year and each country; sort your results by release_year and country.

        -- Find the release_year, country, and max_budget, then group and order by release_year and country
        SELECT release_year, country, MAX(budget) AS max_budget
        FROM films
        GROUP BY release_year, country;

**Answering business questions**

In the real world, every SQL query starts with a business question. Then it is up to you to decide how to write the query that answers the question. Let's try this out.

Which release_year had the most language diversity?

Take your time to translate this question into code. We'll get you started then it's up to you to test your queries in the console.

"Most language diversity" can be interpreted as COUNT(DISTINCT ___). Now over to you.

        "2006"

# Filtering grouped data

**Personal notes:**

In SQL, we cannot filter aggregate functions with WHERE clauses. Groups have their own special filtering keyword: HAVING. The reason why groups have their own keyword for filtering comes down to the order of execution. In the lesson example, the order is FROM, WHERE, GROUP BY, HAVING, SELECT, ORDER BY, and LIMIT.

**HAVING vs WHERE*
WHERE filters individual records, while HAVING filters grouped records.

**Exercises:**

**Filter with HAVING***

Your final keyword is HAVING. It works similarly to WHERE in that it is a filtering clause, with the difference that HAVING filters grouped data.

Filtering grouped data can be especially handy when working with a large dataset. When working with thousands or even millions of rows, HAVING will allow you to filter for just the group of data you want, such as films over two hours in length!

Practice using HAVING to find out which countries (or country) have the most varied film certifications.

**Instructions:**

- Select country from the films table, and get the distinct count of certification aliased as certification_count.

- Group the results by country.

- Filter the unique count of certifications to only results greater than 10.

        SELECT country, COUNT(DISTINCT certification) AS certification_count
        FROM films
        GROUP BY country
        HAVING COUNT(DISTINCT certification) > 10;

**HAVING and sorting**

Filtering and sorting go hand in hand and gives you greater interpretability by ordering our results.

Let's see this magic at work by writing a query showing what countries have the highest average film budgets.

**Instructions:**

- Select the country and the average budget as average_budget, rounded to two decimal, from films.

- Group the results by country.

- Filter the results to countries with an average budget of more than one billion (1000000000).

- Sort by descending order of the average_budget.

        SELECT country, ROUND(AVG(budget),2) AS average_budget
        FROM films
        GROUP BY country
        HAVING AVG(budget) > 1000000000
        ORDER BY average_budget DESC;

**All together now**

It's time to use much of what you've learned in one query! This is good preparation for using SQL in the real world where you'll often be asked to write more complex queries since some of the basic queries can be answered by playing around in spreadsheet applications.

In this exercise, you'll write a query that returns the average budget and gross earnings for films each year after 1990 if the average budget is greater than 60 million.

This will be a big query, but you can handle it!

**Instructions:**

- Select the release_year for each film in the films table, filter for records released after 1990, and group by release_year.

        -- Select the release_year for films released after 1990 grouped by year
        SELECT release_year 
        FROM films
        WHERE release_year > 1990
        GROUP BY release_year;

- Modify the query to include the average budget aliased as avg_budget and average gross aliased as avg_gross for the results we have so far.

        -- Modify the query to also list the average budget and average gross
        SELECT release_year, AVG(budget) AS avg_budget, AVG(gross) AS avg_gross
        FROM films
        WHERE release_year > 1990
        GROUP BY release_year;

- Modify the query once more so that only years with an average budget of greater than 60 million are included.

        SELECT release_year, AVG(budget) AS avg_budget, AVG(gross) AS avg_gross
        FROM films
        WHERE release_year > 1990
        GROUP BY release_year
        -- Modify the query to see only years with an avg_budget of more than 60 million
        HAVING AVG(budget) > 60000000;

- Finally, order the results from the highest average gross and limit to one.

        SELECT release_year, AVG(budget) AS avg_budget, AVG(gross) AS avg_gross
        FROM films
        WHERE release_year > 1990
        GROUP BY release_year
        HAVING AVG(budget) > 60000000
        -- Order the results from highest to lowest average gross and limit to one
        ORDER BY avg_gross DESC
        LIMIT 1;
