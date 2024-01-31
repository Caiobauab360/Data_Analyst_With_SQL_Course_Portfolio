# Filtering Records

**Chapter description:**

Learn about how you can filter numerical and textual data with SQL. Filtering is an important use for this language. You’ll learn how to use new keywords and operators to help you narrow down your query to get results that meet your desired criteria and gain a better understanding of NULL values and how to handle them.

# Filtering numbers

**Personal notes:**

To filter, we need to use a new clause called WHERE, which allows us to focus only on the data relevant to our business questions.

**WHERE with Comparison Operators*

An example query where we filter to see only movies released after the year 1960 using the greater-than operator:
```sql
SELECT title
FROM films
WHERE release_year > 1960;
```

If we wanted to filter movies to see all releases EXCEPT those from the year 1960, we combine <> (not equal to), which is the default symbol in SQL that means "different from":
```sql
SELECT title
FROM films 
WHERE release_year <> 1960;
```

**Comparison Operators:*

">" Greater than or after;

"<" Less than or before;

"=" Equal to;

">=" Greater than or equal to;

"<=" Less than or equal to;

"<>" Not equal to.

**WHERE with Strings*

WHERE and the equals comparison operator can also be used with strings.
```sql
SELECT title
FROM films
WHERE country = 'Japan';
```

**Order of Execution*

Similar to LIMIT, this clause comes after the FROM statement when writing a query. If we use WHERE and LIMIT, the written order will be SELECT, FROM, WHERE, LIMIT.
```sql
SELECT item
FROM coats
WHERE color = 'green'
LIMIT 5;
```

**Exercises:**

**Filtering results**

The WHERE clause allows you to filter based on text and numeric values in a table using comparison operators.

What does the following query return?

SELECT title
FROM films
WHERE release_year > 2000;

        "Films released after the year 2000"

**Using WHERE with numbers**

Filtering with WHERE allows you to analyze your data better. You may have a dataset that includes a range of different movies, and you need to do a case study on the most notable films with the biggest budgets. In this case, you'll want to filter your data to a specific budget range.

Now it's your turn to use the WHERE clause to filter numeric values!

**Instructions:**

- Select the film_id and imdb_score from the reviews table and filter on scores higher than 7.0.

```sql
-- Select film_ids and imdb_score with an imdb_score over 7.0
SELECT film_id, imdb_score
FROM reviews
WHERE imdb_score > 7.0
```

- Select the film_id and facebook_likes of the first ten records with less than 1000 likes from the reviews table.

```sql
-- Select film_ids and facebook_likes for ten records with less than 1000 likes 
SELECT film_id, facebook_likes
FROM reviews
WHERE facebook_likes < 1000
LIMIT 10;
```

- Count how many records have a num_votes of at least 100,000; use the alias films_over_100K_votes.

```sql
-- Count the records with at least 100,000 votes
SELECT COUNT(*) AS films_over_100K_votes
FROM reviews
WHERE num_votes >= 100000;
```

**Using WHERE with text**

WHERE can also filter string values.

Imagine you are part of an organization that gives cinematography awards, and you have several international categories. Before you confirm an award for every language listed in your dataset, it may be worth seeing if there are enough films of a specific language to make it a fair competition. If there is only one movie or a significant skew, it may be worth considering a different way of giving international awards.

Let's try this out!

**Instructions:**

- Select and count the language field using the alias count_spanish.
- Apply a filter to select only Spanish from the language field.

```sql
-- Count the Spanish-language films
SELECT COUNT(language) as count_spanish
FROM films
WHERE language = 'Spanish'
```

# Multiple criteria

**Personal notes:**

Filtering with multiple criteria using the OR, AND, and BETWEEN operators.

**OR Operator*

OR is used when we want to filter by multiple criteria and only need to satisfy at least one condition.
Example using OR:
```sql
SELECT *
FROM coats 
WHERE colors = 'yellow' OR length = 'short';

SELECT title
FROM films
WHERE release_year = 1994 OR release_year = 2000;
```

**AND Operator*

If we want to satisfy all the criteria in our filter, we need to use AND with WHERE.
Example using AND:
```sql
SELECT *
FROM coats 
WHERE colors = 'yellow' AND length = 'short';

SELECT title
FROM films
WHERE release_year > 1994 AND release_year < 2000;
```

We can use AND and OR together. If a query has multiple filtering conditions, we need to put the individual clauses in parentheses to ensure the correct execution order; otherwise, we might not get the expected results.

**BETWEEN Operator*

Checking ranges is very common, and in SQL, the BETWEEN keyword provides a valuable shortcut for filtering values within a specified range.
Example using BETWEEN:
```sql
SELECT *
FROM coats 
WHERE buttons BETWEEN 1 AND 5;

SELECT title
FROM films
WHERE release_year 
	BETWEEN 1994 AND 2000;
```

**Exercises:**

**Using AND**

The following exercises combine AND and OR with the WHERE clause. Using these operators together strengthens your queries and analyses of data.

You will apply these new skills now on the films database.

**Instructions:**

- Select the title and release_year for all German-language films released before 2000.

```sql
-- Select the title and release_year for all German-language films released before 2000
SELECT title, release_year
FROM films
WHERE language = 'German' AND release_year < 2000;]
```

- Update the query from the previous step to show German-language films released after 2000 rather than before.

```sql
-- Update the query to see all German-language films released after 2000
SELECT title, release_year
FROM films
WHERE release_year > 2000
        AND language = 'German';
```

- Select all details for German-language films released after 2000 but before 2010 using only WHERE and AND.

```sql
-- Select all records for German-language films released after 2000 and before 2010
SELECT *
FROM films
WHERE release_year > 2000 AND release_year < 2010 AND language = 'German'
```

**Using OR**

This time you'll write a query to get the title and release_year of films released in 1990 or 1999, which were in English or Spanish and took in more than $2,000,000 gross.

It looks like a lot, but you can build the query up one step at a time to get comfortable with the underlying concept in each step. Let's go!

**Instructions:**

- Select the title and release_year for films released in 1990 or 1999 using only WHERE and OR.

```sql
-- Find the title and year of films from the 1990 or 1999
SELECT title, release_year
FROM films
WHERE release_year = 1990 OR release_year = 1999
```

- Filter the records to only include English or Spanish-language films.

```sql
SELECT title, release_year
FROM films
WHERE (release_year = 1990 OR release_year = 1999) AND 
        (language = 'English' OR language = 'Spanish');
-- Add a filter to see only English or Spanish-language films
```

- Finally, restrict the query to only return films worth more than $2,000,000 gross.

```sql
SELECT title, release_year
FROM films
WHERE (release_year = 1990 OR release_year = 1999)
        AND (language = 'English' OR language = 'Spanish')
-- Filter films with more than $2,000,000 gross
        AND (gross > 2000000);
```

**Using BETWEEN**

Let's use BETWEEN with AND on the films database to get the title and release_year of all Spanish-language films released between 1990 and 2000 (inclusive) with budgets over $100 million.

We have broken the problem into smaller steps so that you can build the query as you go along!

**Instructions:**

- Select the title and release_year of all films released between 1990 and 2000 (inclusive) using BETWEEN.

```sql
-- Select the title and release_year for films released between 1990 and 2000
SELECT title, release_year
FROM films
WHERE release_year
        BETWEEN 1990 AND 2000;
```

- Build on your previous query to select only films with a budget over $100 million.

```sql
SELECT title, release_year
FROM films
WHERE release_year BETWEEN 1990 AND 2000 
-- Narrow down your query to films with budgets > $100 million
        AND budget > 100000000;
```

- Now, restrict the query to only return Spanish-language films.

```sql
SELECT title, release_year
FROM films
WHERE release_year BETWEEN 1990 AND 2000
        AND budget > 100000000
-- Restrict the query to only Spanish-language films
        AND language = 'Spanish';
```

- Finally, amend the query to include all Spanish-language or French-language films with the same criteria.

```sql
SELECT title, release_year
FROM films
WHERE release_year BETWEEN 1990 AND 2000
        AND budget > 100000000
-- Amend the query to include Spanish or French-language films
        AND (language = 'Spanish' OR language = 'French');
```

# Filtering text

**Personal notes:**

Often, we'll want to search for a pattern rather than a specific text sequence in the real world. We'll be introducing three more SQL keywords into our vocabulary to help us achieve this: LIKE, NOT LIKE, and IN.

**LIKE*

In SQL, we can use the LIKE operator with a WHERE clause to search for a pattern in a field. We use a wildcard as a placeholder for some other values to do this. There are two wildcards in LIKE, the percentage sign (%) and the underscore (_):
- `%` - will match zero, one, or many characters in the text.
  ```sql
  SELECT name
  FROM people
  WHERE name LIKE 'Ade%';
  ```
- `_` - will match a single character.
  ```sql
  SELECT name
  FROM people
  WHERE name LIKE 'Ev_';
  ```

**NOT LIKE*

We can also use the NOT LIKE operator to find records that do not match the specified pattern. For example, in this query, we are finding records of people who do not have "A." as part of their first name:
```sql
SELECT name
FROM people
WHERE name NOT LIKE 'A.%';
```
*It's important to note that this operation is case-sensitive, so we need to be mindful of what we are querying.

**Wildcard Position*

We can find values that start, end, or contain characters at any position, as well as find records of a certain length. For example, this code will find all people whose name ends with "r":
```sql
SELECT name
FROM people
WHERE name LIKE '%r';
```
This code, on the other hand, will find records where the third character is "t":
```sql
SELECT name
FROM people
WHERE name LIKE '__t%';
```

**WHERE, IN*

What if we want to filter based on many conditions or within a range of numbers? The IN operator allows us to specify multiple values in a WHERE clause, making it easier and quicker to define numerous OR conditions.
```sql
SELECT title
FROM films 
WHERE release_year IN (1920, 1930, 1940);

SELECT title
FROM films
WHERE country IN ('Germany', 'France');
```

**Exercises:**

**LIKE and NOT LIKE**

The LIKE and NOT LIKE operators can be used to find records that either match or do not match a specified pattern, respectively. They can be coupled with the wildcards % and _. The % will match zero or many characters, and _ will match a single character.

This is useful when you want to filter text, but not to an exact word.

Do the following exercises to gain some practice with these keywords

**Instructions:**

- Select the names of all people whose names begin with 'B'.

```sql
-- Select the names that start with B
SELECT name
FROM people
WHERE name LIKE 'B%';
```

- Select the names of people whose names have 'r' as the second letter.

```sql
SELECT name
FROM people
-- Select the names that have r as the second letter
WHERE name LIKE '_r%'
```

- Select the names of people whose names don't start with 'A'.

```sql
SELECT name
FROM people
-- Select names that don't start with A
WHERE name NOT LIKE 'A%'
```

**WHERE IN**

You now know you can query multiple conditions using the IN operator and a set of parentheses. It is a valuable piece of code that helps us keep our queries clean and concise.

Try using the IN operator yourself!

**Instructions:**

- Select the title and release_year of all films released in 1990 or 2000 that were longer than two hours.

```sql
-- Find the title and release_year for all films over two hours in length released in 1990 and 2000
SELECT title, release_year
FROM films
WHERE release_year IN (1990, 2000)
        AND duration > 120
```

- Select the title and language of all films in English, Spanish, or French using IN.

```sql
-- Find the title and language of all films in English, Spanish, and French
SELECT title, language
FROM films
WHERE language IN ('English', 'Spanish', 'French')
```

- Select the title, certification and language of all films certified NC-17 or R that are in English, Italian, or Greek.

```sql
-- Find the title, certification, and language all films certified NC-17 or R that are in English, Italian, or Greek
SELECT title, certification, language
FROM films
WHERE certification IN ('NC-17', 'R') AND language IN ('English', 'Italian','Greek')
```

**Combining filtering and selecting**

Time for a little challenge. So far, your SQL vocabulary from this course includes COUNT(), DISTINCT, LIMIT, WHERE, OR, AND, BETWEEN, LIKE, NOT LIKE, and IN. In this exercise, you will try to use some of these together. Writing more complex queries will be standard for you as you become a qualified SQL programmer.

As this query will be a little more complicated than what you've seen so far, we've included a bit of code to get you started. You will be using DISTINCT here too because, surprise, there are two movies named 'Hamlet' in this dataset!

Follow the instructions to find out what 90's films we have in our dataset that would be suitable for English-speaking teens.

**Instructions:**

- Count the unique titles from the films database and use the alias provided.

- Filter to include only movies with a release_year from 1990 to 1999, inclusive.

- Add another filter narrowing your query down to English-language films.

- Add a final filter to select only films with 'G', 'PG', 'PG-13' certifications.

```sql
SELECT COUNT(DISTINCT title) AS nineties_english_films_for_teens
FROM films
WHERE release_year BETWEEN 1990 AND 1999
AND language = 'English'
AND certification IN('G', 'PG', 'PG-13');
```

# NULL values

**Personal notes:**

The COUNT keyword can be used to include or exclude non-null values depending on whether or not we use the asterisk in our query:
- COUNT(field_name) - Includes only non-null values;
- COUNT(*) - Includes null values.

In SQL, NULL represents a missing or unknown value. In the real world, our databases are likely to have empty fields due to human error or because the information is not available or unknown. Knowing how to handle these fields is essential, as they can affect any analysis we perform.

**IS NULL*

A quick way to see how many data are missing is to use IS NULL with the WHERE clause. Here's an example where we check which names do not have a recorded birthdate in our table:
```sql
SELECT name
FROM people
WHERE birthdate IS NULL;
```

**IS NOT NULL*

Here's an example of counting the missing birthdates in the people table:
```sql
SELECT COUNT(*) AS no_birthdates
FROM people
WHERE birthdate IS NULL;
```

Sometimes, we will want to filter out null values to get only results that are not null. To do this, we can use the IS NOT NULL operator. For example, this query provides the count of all people whose birthdates are not missing in the people table, giving us a new count:
```sql
SELECT COUNT(name) AS count_birthdates
FROM people
WHERE birthdate IS NOT NULL;
```

**COUNT() vs IS NOT NULL*

There is no difference in which one to use; both will count non-missing values.
Example using COUNT():
```sql
SELECT
  COUNT(certification) AS count_certification
FROM films;
```
Example using IS NOT NULL:
```sql
SELECT
  COUNT(certification) AS count_certification
FROM films
WHERE certification IS NOT NULL;
```

**Exercises:**

**What does NULL mean?**

I hope you were paying attention in the video. Pop quiz: What does NULL represent?

        "A missing value"

**Practice with NULLs**

Well done. Now that you know what NULL means and what it's used for, it's time for some more practice!

Let's explore the films table again to better understand what data you have.

**Instructions:**

- Select the title of every film that doesn't have a budget associated with it and use the alias no_budget_info.

```sql
-- List all film titles with missing budgets
SELECT title AS no_budget_info
FROM films
WHERE budget IS NULL;
```

- Count the number of films with a language associated with them and use the alias count_language_known.

```sql
-- Count the number of films we have language data for
SELECT COUNT(language) AS count_language_known
FROM films
```



