# Full-text Search and PostgresSQL Extensions

**Chapter description:**

An introduction into some more advanced capabilities of PostgreSQL like full-text search and extensions.

# Introduction to full-text search

**Personal notes:**

**The LIKE Operator*

The LIKE operator can be used in a WHERE clause to search for a pattern in a column. To do this, we use something called a wildcard as a placeholder for some other values. There are two wildcards we can use with LIKE. The first is the underscore (_) sign, which matches a single character. The second is the percent (%) sign, which will match zero or more characters of variable length.

In this example, we use the percent wildcard to match any title in the film table that starts with the word 'ELF' followed by zero or more characters.

```sql
SELECT title
FROM film
WHERE title LIKE 'ELF%';
```

Changing the position of the percent wildcard in our query produces a very different result. In this example, we use the percent wildcard to match a string that starts with one or more characters followed by the string 'ELF'.

```sql
SELECT title
FROM film
WHERE title LIKE '%ELF';
```

The LIKE operator is a great tool to use when searching for specific characters in a string. Sometimes, when conducting a text search, you will want to find variations of the characters you are searching for. For example, using LIKE to search the title column for any string that contains the word 'elf' in lowercase letters will return zero results.

```sql
SELECT title
FROM film
WHERE title LIKE '%elf';
```

**LIKE versus Full-Text Search*

However, consider this query that performs a full-text search using the 'to_tsvector' and 'to_tsquery' functions and the @@ match operator to search the 'title' column. Because full-text search is responsible for variations in the search string and is case-insensitive, we see that we get the expected results.

```sql
SELECT title, description
FROM film
WHERE to_tsvector(title) @@ to_tsquery('elf');
```

**What is Full-Text Search?*

Full-text search provides a means to perform natural language queries on text data using lemmatization (stemming), fuzzy string matching to handle spelling errors, and a mechanism to rank results by similarity to the search string.

**Full-Text Search Syntax*

Full-text search can be complex, but even a basic full-text search query can be a powerful tool. The example below is a basic technique to query a document, in this case, the title column, to match the characters 'elf.' The WHERE clause of the query uses the @@ match operator to compare the values returned by two built-in functions to perform the search, to_tsvector, and to_tsquery.

```sql
SELECT title, description
FROM film
WHERE to_tsvector(title) @@ to_tsquery('elf');
```

These functions convert text and string data into a tsvector data type, which is a sorted list of words that have been normalized into variants of the same word. These variants are called lexemes.

**Exercises:**

**A review of the LIKE operator**

The LIKE operator allows us to filter our queries by matching one or more characters in text data. By using the % wildcard we can match one or more characters in a string. This is useful when you want to return a result set that matches certain characteristics and can also be very helpful during exploratory data analysis or data cleansing tasks.

Let's explore how different usage of the % wildcard will return different results by looking at the film table of the Sakila DVD Rental database.

**Instructions:**

- Select all columns for all records that begin with the word GOLD.

```sql
-- Select all columns
SELECT *
FROM film
-- Select only records that begin with the word 'GOLD'
WHERE title LIKE 'GOLD%';
```

- Now select all records that end with the word GOLD.

```sql
SELECT *
FROM film
-- Select only records that end with the word 'GOLD'
WHERE title LIKE '%GOLD';
```

- Finally, select all records that contain the word 'GOLD'.

```sql
SELECT *
FROM film
-- Select only records that contain the word 'GOLD'
WHERE title LIKE '%GOLD%';
```

**What is a tsvector?**

You saw how to convert strings to tsvector and tsquery in the video and, in this exercise, we are going to dive deeper into what these functions actually return after converting a string to a tsvector. In this example, you will convert a text column from the film table to a tsvector and inspect the results. Understanding how full-text search works is the first step in more advanced machine learning and data science concepts like natural language processing.

**Instruction:**

- Select the film description and convert it to a tsvector data type.

```sql
-- Select the film description as a tsvector
SELECT to_tsvector(description)
FROM film;
```

**Basic full-text search**

Searching text will become something you do repeatedly when building applications or exploring data sets for data science. Full-text search is helpful when performing exploratory data analysis for a natural language processing model or building a search feature into your application.

In this exercise, you will practice searching a text column and match it against a string. The search will return the same result as a query that uses the LIKE operator with the % wildcard at the beginning and end of the string, but will perform much better and provide you with a foundation for more advanced full-text search queries. Let's dive in.

**Instructions:**

- Select the title and description columns from the film table.

- Perform a full-text search on the title column for the word elf.

```sql
-- Select the title and description
SELECT title, description
FROM film
-- Convert the title to a tsvector and match it against the tsquery 
WHERE to_tsvector(title) @@ to_tsquery('elf');
```

# Extending PostgreSQL

**Personal notes:**

**User-defined Data Types*

User-defined data types are created using the `CREATE TYPE` command, which registers the type in a system table and makes it available for use wherever PostgreSQL expects a type name.

Enumerated data types or ENUMs allow you to define a custom list of values that will never change, such as the days of the week. For example, a new data type called `dayofweek` is defined as an ENUM using the `CREATE TYPE` command with a comma-separated list of the days of the week.

```sql
CREATE TYPE dayofweek AS ENUM (
    'Monday',
    'Tuesday',
    'Wednesday',
    'Thursday',
    'Friday',
    'Saturday',
    'Sunday'
);
```

**Getting Information about User-defined Data Types*

Once your custom data type has been created, you can query the system table called `pg_type` to get information about all the data types available in your database, both user-defined and internal.

```sql
SELECT typname, typcategory
FROM pg_type
WHERE typname = 'dayofweek';
```

The query results return `dayofweek` for the type name of the data type we just created and 'E' for the category, where 'E' represents an ENUM type.

You can also use the INFORMATION_SCHEMA system database, as we learned earlier, to get information about user-defined data types. For example, querying `INFORMATION_SCHEMA.COLUMNS` and looking at the columns in the film table shows that the `rating` column is a user-defined data type with a `udt_name` of `mpaa_rating`.

```sql
SELECT column_name, data_type, udt_name
FROM INFORMATION_SCHEMA.COLUMNS
WHERE table_name = 'film';
```

**User-defined Functions*

Another way to extend the features of your PostgreSQL database is with user-defined functions. A user-defined function is PostgreSQL's equivalent to a stored procedure where you can group multiple queries and SQL statements together into a single package using the `CREATE FUNCTION` command.

In this example, we define the `squared` function, which takes an integer `i` as an input parameter and returns the square of that parameter as the result. The double dollar sign `$$` syntax specifies that the function will use SQL as the language.

```sql
CREATE FUNCTION squared(i integer) RETURNS integer AS $$
BEGIN
    RETURN i*i;
END;
$$ LANGUAGE plpgsql;
```

**User-defined Functions in the sakila Database*

The `get_customer_balance` function takes a `customer_id` and an `effective_date` as input parameters and calculates the current balance of a customer based on the `customer_id` and the timestamp.

The `inventory_held_by_customer` function takes an `inventory_id` as an input parameter and determines all rows that have a `return_date` equal to NULL, indicating that the customer still has the rental.

The `inventory_in_stock` function takes an `inventory_id` as an input parameter and determines if a specific `inventory_id` is in stock.

**Exercises:**

**User-defined data types**

ENUM or enumerated data types are great options to use in your database when you have a column where you want to store a fixed list of values that rarely change. Examples of when it would be appropriate to use an ENUM include days of the week and states or provinces in a country.

Another example can be the directions on a compass (i.e., north, south, east and west.) In this exercise, you are going to create a new ENUM data type called compass_position.

**Instructions:**

- Create a new enumerated data type called compass_position.

- Use the four positions of a compass as the values.

```sql
-- Create an enumerated data type, compass_position
CREATE TYPE compass_position AS ENUM (
  	-- Use the four cardinal directions
  	'North', 
  	'West',
  	'Est', 
  	'South'
);
```

- Select all columns from the pg_type table where the type name is equal to mpaa_rating.

```sql
SELECT *
FROM pg_type
WHERE typname='mpaa_rating'
```

**User-defined functions in Sakila**

If you were running a real-life DVD Rental store, there are many questions that you may need to answer repeatedly like whether a film is in stock at a particular store or the outstanding balance for a particular customer. These types of scenarios are where user-defined functions will come in very handy. The Sakila database has several user-defined functions pre-defined. These functions are available out-of-the-box and can be used in your queries like many of the built-in functions we've learned about in this course.

In this exercise, you will build a query step-by-step that can be used to produce a report to determine which film title is currently held by which customer using the inventory_held_by_customer() function.

**Instructions:**

- Select the title and inventory_id columns from the film and inventory tables in the database.

```sql
-- Select the film title and inventory ids
SELECT 
	f.title, 
    i.inventory_id
FROM film AS f 
	-- Join the film table to the inventory table
	INNER JOIN inventory AS i ON f.film_id=i.film_id
```

- inventory_id is currently held by a customer and alias the column as held_by_cust

```sql
-- Select the film title, rental and inventory ids
SELECT 
	f.title, 
    i.inventory_id,
    -- Determine whether the inventory is held by a customer
    inventory_held_by_customer(i.inventory_id) AS held_by_cust 
FROM film as f 
	-- Join the film table to the inventory table
	INNER JOIN inventory AS i ON f.film_id=i.film_id 
```

- Now filter your query to only return records where the inventory_held_by_customer() function returns a non-null value.

```sql
-- Select the film title and inventory ids
SELECT 
	f.title, 
    i.inventory_id,
    -- Determine whether the inventory is held by a customer
    inventory_held_by_customer(i.inventory_id) as held_by_cust
FROM film as f 
	INNER JOIN inventory AS i ON f.film_id=i.film_id 
WHERE
	-- Only include results where the held_by_cust is not null
    inventory_held_by_customer(i.inventory_id) IS NOT NULL
```

# Intro to PostgreSQL extensions

**Personal notes:**

**Commonly Used Extensions*

In this lesson, we will learn about some common extensions, namely fuzzystrmatch and pg_trgm, that enhance the full-text search capabilities of PostgreSQL. But first, let's delve into the PostgreSQL extension structure in more detail.

**Commonly Used Extensions*

Most PostgreSQL distributions come with a common set of widely used community-supported extensions that can be used by simply enabling them.

- **PostGIS:** Adds support to perform location queries in SQL.
- **PostPic:** Allows image processing within the database.
- **fuzzystrmatch and pg_trgm:** Provide functions that extend full-text search capabilities by finding similarities in strings.

**Querying Extension Metadata*

To discover which extensions are available in your specific PostgreSQL instance, you can query the `pg_available_extensions` system view, as shown in the example below, to determine a list of extensions that are available for installation and activation. The results return the names of the first two available extensions.

```sql
SELECT name
FROM pg_available_extensions;
```

A similar query on the `pg_extension` system table will tell you which extensions have already been activated in your database and are currently available for use.

```sql
SELECT extname
FROM pg_extension;
```

Any of the extensions returned from the `pg_available_extensions` system view can be loaded into your database and activated with a simple query using the `CREATE EXTENSION` command, an example of which is shown below. The `IF NOT EXISTS` clauses can be used to ensure that if the extension was previously enabled, the query will not generate an error message.

```sql
CREATE EXTENSION IF NOT EXISTS fuzzystrmatch;
```

**Using fuzzystrmatch or Fuzzy Searching*

When conducting a full-text search based on user input or seeking to perform natural language processing-based text data analysis and comparison, a function you will often use is `levenshtein` from the `fuzzystrmatch` extension.

The `levenshtein` function calculates the Levenshtein distance between two strings, which is the number of edits needed for the strings to be a perfect match.

```sql
SELECT levenshtein('GUMBO',  'GAMBOL');
```

**Compare Two Strings with pg_trgm*

The `pg_trgm` extension provides functions and operators to determine the similarity of two strings using trigram matches. Trigrams are groups of three consecutive characters in a string, and based on the number of matching trigrams in two strings, it provides a measure of how similar they are. This measurement can be calculated using the `similarity` function of this extension. The `similarity` function accepts two parameters, the first being the string you want to compare, and the second is the string you want to compare it to.

```sql
SELECT similarity('GUMBO', 'GAMBOL');
```

This function returns a number between 0 and 1, with 0 representing no matching trigrams and 1 representing a perfect match.

**Exercises:**

**Enabling extensions**

Before you can use the capabilities of an extension it must be enabled. As you have previously learned, most PostgreSQL distributions come pre-bundled with many useful extensions to help extend the native features of your database. You will be working with fuzzystrmatch and pg_trgm in upcoming exercises but before you can practice using the capabilities of these extensions you will need to first make sure they are enabled in our database. In this exercise you will enable the pg_trgm extension and confirm that the fuzzystrmatch extension, which was enabled in the video, is still enabled by querying the pg_extension system table.

**Instructions:**

- Enable the pg_trgm extension

```sql
-- Enable the pg_trgm extension
CREATE EXTENSION IF NOT EXISTS pg_trgm;
```

- Now confirm that both fuzzystrmatch and pg_trgm are enabled by selecting all rows from the appropriate system table.

```sql
-- Select all rows extensions
SELECT * 
FROM  pg_extension;
```

**Measuring similarity between two strings**

Now that you have enabled the fuzzystrmatch and pg_trgm extensions you can begin to explore their capabilities. First, we will measure the similarity between the title and description from the film table of the Sakila database.

**Instructions:**

- Select the film title and description.

- Calculate the similarity between the title and description.

```sql
-- Select the title and description columns
SELECT 
  title, 
  description, 
  -- Calculate the similarity
  similarity(title, description)
FROM 
  film
```

**Levenshtein distance examples**

Now let's take a closer look at how we can use the levenshtein function to match strings against text data. If you recall, the levenshtein distance represents the number of edits required to convert one string to another string being compared.

In a search application or when performing data analysis on any data that contains manual user input, you will always want to account for typos or incorrect spellings. The levenshtein function provides a great method for performing this task. In this exercise, we will perform a query against the film table using a search string with a misspelling and use the results from levenshtein to determine a match. Let's check it out.

**Instructions:**

- Select the film title and film description.

- Calculate the levenshtein distance for the film title with the string JET NEIGHBOR.

```sql
-- Select the title and description columns
SELECT  
  title, 
  description, 
  -- Calculate the levenshtein distance
  levenshtein(title, 'JET NEIGHBOR') AS distance
FROM 
  film
ORDER BY 3
```

**Putting it all together**

In this exercise, we are going to use many of the techniques and concepts we learned throughout the course to generate a data set that we could use to predict whether the words and phrases used to describe a film have an impact on the number of rentals.

First, you need to create a tsvector from the description column in the film table. You will match against a tsquery to determine if the phrase "Astounding Drama" leads to more rentals per month. Next, create a new column using the similarity function to rank the film descriptions based on this phrase.

**Istructions:**

- Select the title and description for all DVDs from the film table.

- Perform a full-text search by converting the description to a tsvector and match it to the phrase 'Astounding & Drama' using a tsquery in the WHERE clause.

```sql
-- Select the title and description columns
SELECT  
  title, 
  description 
FROM 
  film
WHERE 
  -- Match "Astounding Drama" in the description
 to_tsvector(description) @@ 
  to_tsquery('Astounding & Drama');
```

- Add a new column that calculates the similarity of the description with the phrase 'Astounding Drama'.

- Sort the results by the new similarity column in descending order.

```sql
SELECT 
  title, 
  description, 
  -- Calculate the similarity
  similarity(description,'Astounding Drama')
FROM 
  film 
WHERE 
  to_tsvector(description) @@ 
  to_tsquery('Astounding & Drama') 
ORDER BY 
	similarity(description,'Astounding Drama') DESC;
```