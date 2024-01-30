# Joining Data in SQL

**Course description:**

Joining data is an essential skill which enables us to draw information from separate tables together into a single, meaningful set of results. Learn to supercharge your queries using table joins and relational set theory in this course on joining data.

In this course, you’ll learn how to:

✓ Work with more than one table in SQL

✓ Use inner joins, outer joins and cross joins

✓ Leverage set theory, including unions, intersect, and except clauses

✓ Create nested queries

Every step is accompanied by exercises and opportunities to apply the theory and grow your confidence in SQL.

# Introducing Inner Joins

**Chapter description:**

In this chapter, you’ll be introduced to the concept of joining tables and will explore all the ways you can enrich your queries using joins—beginning with inner joins.

# The ins and outs of INNER JOIN

**Personal notes:**

Joining data is an essential skill that allows us to bring information from separate tables into a single, meaningful result set. A key is a single column or group of columns that uniquely identifies records in a table. With SQL joins, we can join on a key column or any other column.

The INNER JOIN shown in the lesson looks for records in both tables with the same values in the key column "id." It's common to start building the query with the join first and selecting the fields later. After FROM, we list the left table, followed by the INNER JOIN keyword and the right table. Then, we specify the field to match the tables, using the ON keyword. Finally, we add SELECT at the beginning of the query and choose the fields we want to return.

```sql
SELECT prime_ministers.country, prime_ministers.continent, prime_minister, president
FROM prime_ministers
INNER JOIN presidents
ON prime_ministers.country = presidents.country;
```

*When selecting columns that exist in both tables, such as "country" and "continent," the format `table.column_name` must be used to avoid an SQL error.

**Aliasing Tables*

In our INNER JOIN, we had to type "president" and "prime_minister" multiple times. Fortunately, we can alias table names using the same AS keyword used to alias column names. For example, we use the aliases p1 and p2 in our SELECT and ON clauses to shorten our query.

```sql
SELECT p1.country, p1.continent, prime_minister, president
FROM prime_ministers AS p1
INNER JOIN presidents AS p2
ON p1.country = p2.country;
```

**Using USING*

When joining on two identical column names, we can employ the USING command followed by the name of the shared column in parentheses. For example, since the join field is named "country" in both tables, we can use USING.

```sql
SELECT p1.country, p1.continent, prime_minister, president
FROM prime_ministers AS p1
INNER JOIN presidents AS p2
USING(country);
```

**Exercises:**

**Your first join**

Throughout this course, you'll be working with the countries database, which contains information about the most populous world cities in the world, along with country-level economic, population, and geographic data. The database also contains information on languages spoken in each country.

You can see the different tables in this database to get a sense of what they contain by clicking on the corresponding tabs. Click through them and familiarize yourself with the fields that seem to be shared across tables before you continue with the course.

In this exercise, you'll use the cities and countries tables to build your first inner join. You'll start off by selecting all columns in step 1, performing your join in step 2, and then refining your join to choose specific columns in step 3.

**Instructions:**

- Begin by selecting all columns from the cities table, using the SQL shortcut that selects all.

```sql
-- Select all columns from cities
SELECT *
FROM cities;
```

- Perform an inner join with the cities table on the left and the countries table on the right; do not alias tables here or in the next step.

- Identify the relevant column names to join ON by inspecting the cities and countries tabs in the console.

```sql
SELECT * 
FROM cities
INNER JOIN countries
on cities.country_code = countries.code
```

- Complete the SELECT statement to keep only the name of the city, the name of the country, and the region the country is located in (in the order specified).

- Alias the name of the city AS city and the name of the country AS country.

```sql
-- Select name fields (with alias) and region 
SELECT cities.name AS city, countries.name AS country, region
FROM cities 
INNER JOIN countries 
ON cities.country_code = countries.code;
```

**Joining with aliased tables**

Recall from the video that instead of writing full table names in queries, you can use table aliasing as a shortcut. The alias can be used in other parts of your query, such as the SELECT statement!

You also learned that when you SELECT fields, a field can be ambiguous. For example, imagine two tables, apples and oranges, both containing a column called color. You need to use the syntax apples.color or oranges.color in your SELECT statement to point SQL to the correct table. Without this, you would get the following error:

  column reference "color" is ambiguous

In this exercise, you'll practice joining with aliased tables. You'll use data from both the countries and economies tables to examine the inflation rate in 2010 and 2015.

When writing joins, many SQL users prefer to write the SELECT statement after writing the join code, in case the SELECT statement requires using table aliases.

**Instructions:**

- Start with your inner join in line 5; join the tables countries AS c (left) with economies (right), aliasing economies AS e.

- Next, use code as your joining field in line 7; do not use the USING command here.

- Lastly, select the following columns in order in line 2: code from the countries table (aliased as country_code), name, year, and inflation_rate.

```sql
-- Select fields with aliases
SELECT c.code AS country_code, name, year, inflation_rate
FROM countries AS c
-- Join to economies (alias e)
INNER JOIN economies AS e
-- Match on code field using table aliases
ON c.code = e.code;
```

**USING in action**

In the previous exercises, you performed your joins using the ON keyword. Recall that when both the field names being joined on are the same, you can take advantage of the USING clause.

You'll now explore the languages table from our database. Which languages are official languages, and which ones are unofficial?

You'll employ USING to simplify your query as you explore this question.

**Instructions:**

- Use the country code field to complete the INNER JOIN with USING; do not change any alias names.

```sql
SELECT c.name AS country, l.name AS language, official
FROM countries AS c
INNER JOIN languages AS l
-- Match using the code column
USING(code);
```

# Defining relationships

**Personal notes:**

**One-to-Many Relationships*

The first type of relationship we'll discuss is a one-to-many relationship. This is the most common type of relationship, where a single entity can be associated with multiple entities. For example, a university database might have a "Department" table (one) related to a "Professor" table (many) since one department can have multiple professors.

**One-to-One Relationships*

One-to-one relationships imply unique pairs between entities and are, therefore, less common. An example of such a relationship is a common premise in forensic science, where no two fingerprints are identical, so a specific fingerprint can only be generated by one person, one fingerprint for one finger.

**Many-to-Many Relationships*

Many-to-many relationships involve one entry being linked to several other entries, creating a web of interconnected relationships. This type of relationship often requires an intermediary table, known as a junction or associative table, to manage the connections between entities. For example, in a database for a bookstore, a "Book" table might be related to an "Author" table in a many-to-many relationship, as one book can have multiple authors, and one author can contribute to multiple books.

Understanding the nature of these relationships is crucial for designing a well-structured relational database, as it influences how tables are organized, keys are defined, and data integrity is maintained.

**Exercises:**

**Relationships in our database**

Now that you know more about the different types of relationships that can exist between tables, it's time to examine a few relationships in the countries database!

To answer questions about table relationships, you can explore the tables displayed as tabs in your console.

**Instructions:**

- Question: What best describes the relationship between code in the countries table and country_code in the cities table?

        "This is a one-to-many relationship."

- Question: Which of these options best describes the relationship between the countries table and the languages table?

        "This is a many-to-many relationship."

**Inspecting a relationship**

You've just identified that the countries table has a many-to-many relationship with the languages table. That is, many languages can be spoken in a country, and a language can be spoken in many countries.

This exercise looks at each of these in turn. First, what is the best way to query all the different languages spoken in a country? And second, how is this different from the best way to query all the countries that speak each language?

Recall that when writing joins, many users prefer to write SQL code out of order by writing the join first (along with any table aliases), and writing the SELECT statement at the end.

**Instructions:**

- Start with the join statement in line 6; perform an inner join with the countries table as c on the left with the languages table as l on the right.

- Make use of the USING keyword to join on code in line 8.

- Lastly, in line 2, select the country name, aliased as country, and the language name, aliased as language.

```sql
-- Select country and language names, aliased
SELECT c.name AS country, l.name AS language
-- From countries (aliased)
FROM countries AS c
-- Join to languages (aliased)
INNER JOIN languages as l
-- Use code as the joining field with the USING keyword
USING(code);
```

- Rearrange the SELECT statement so that the language column appears on the left and the country column on the right.

- Sort the results by language.

```sql
-- Rearrange SELECT statement, keeping aliases
SELECT l.name AS language, c.name AS country
FROM countries AS c
INNER JOIN languages AS l
USING(code)
-- Order the results by language
ORDER BY language;
```

- Question: Select the incorrect answer from the following options.

The query you generated in step 1 is provided below. Run this query (or the amendment you made in step 2) in the console to find the answer to the question.

SELECT c.name AS country, l.name AS language
FROM countries AS c
INNER JOIN languages AS l
USING(code)
ORDER BY country;

        "Alsatian is spoken in more than one country."

# Multiple joins

**Personal notes:**

**Joins on Joins*

A powerful feature of SQL is the ability to combine and execute multiple joins in a single query. We start with the same INNER JOIN as before and then chain another INNER JOIN to the result of our first INNER JOIN. Note that we use "left_table.id" in the last line of this example.

```sql
SELECT *
FROM left_table
INNER JOIN right_table
ON left_table.id = right_table.id
INNER JOIN another_table
ON left_table.id = another_table.id;
```

*If we want to perform the second join using the "id" field from "right_table" instead of "left_table," we can replace "left_table.id" with "right_table.id" in the final line.

Here's the final SQL query example for chaining a second INNER JOIN. Note that the syntax for the second join is exactly the same as the first join, but using "prime_minister" as the field to join:

```sql
SELECT
	p1.country,
	p1.continent,
	president,
	prime_minister,
	pm_start
FROM prime_ministers AS p1
INNER JOIN presidents AS p2
USING(country)
INNER JOIN prime_minister_terms as p3
USING(prime_minister);
```

*You could continue the chain and join as many tables as needed.

**Joining on Multiple Keys*

Another thing to know about joins is that not every value in the field being joined corresponds to exactly one record in the joining field of the right table. In the example shown, if we join on a field as we did before, the query will return multiple records from the right_table that correspond to the left_table on the id field. We can limit the returned records by providing an additional field to join by adding the AND keyword to our ON clause. In this example, we join on the date, a second column commonly used when joining on multiple fields.

```sql
SELECT *
FROM left_table
INNER JOIN right_table
ON left_table.id = right_table.id
    AND left_table.date = right_table.date;
```

**Exercises:**

**Joining multiple tables**

You've seen that the ability to combine multiple joins using a single query is a powerful feature of SQL.

Suppose you are interested in the relationship between fertility and unemployment rates. Your task in this exercise is to join tables to return the country name, year, fertility rate, and unemployment rate in a single result from the countries, populations and economies tables.

**Instructions:**

- Perform an inner join of countries AS c (left) with populations AS p (right), on code.
Select name, year and fertility_rate.

```sql
-- Select relevant fields
SELECT name, year, fertility_rate
-- Inner join countries and populations, aliased, on code
FROM countries AS c
INNER JOIN populations AS p
ON c.code = p.country_code;"
```
    
- Chain another inner join to your query with the economies table AS e, using code.
Select name, and using table aliases, select year and unemployment_rate from economies.

```sql
-- Select fields
SELECT name, e.year, fertility_rate, unemployment_rate
FROM countries AS c
INNER JOIN populations AS p
ON c.code = p.country_code
-- Join to economies (as e)
INNER JOIN economies AS e
-- Match on country code
ON p.country_code = e.code;
```

**Checking multi-table joins**

Have a look at the results for Albania from the previous query below. You can see that the 2015 fertility_rate has been paired with 2010 unemployment_rate, and vice versa.

        name	    year	fertility_rate	unemployment_rate

        Albania	    2015	    1.663	            17.1

        Albania	    2010	    1.663	            14

        Albania	    2015	    1.793	            17.1

        Albania	    2010	    1.793	            14

Instead of four records, the query should return two: one for each year. The last join was performed on c.code = e.code, without also joining on year. Your task in this exercise is to fix your query by explicitly stating that both the country code and year should match!

**Instruction:**

- Modify your query so that you are joining to economies on year as well as code.

```sql
SELECT name, e.year, fertility_rate, unemployment_rate
FROM countries AS c
INNER JOIN populations AS p
ON c.code = p.country_code
INNER JOIN economies AS e
ON c.code = e.code
        AND p.year = e.year;
-- Add an additional joining condition such that you are also joining on year
```