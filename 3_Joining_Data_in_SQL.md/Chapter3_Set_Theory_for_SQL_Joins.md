# Set Theory for SQL Joins

**Chapter description:**

In this chapter, you will learn about using set theory operations in SQL, with an introduction to UNION, UNION ALL, INTERSECT, and EXCEPT clauses. You’ll explore the predominant ways in which set theory operations differ from join operations.

# Set theory for SQL Joins

**Personal notes:**

SQL has three main set operations: `UNION`, `INTERSECT`, and `EXCEPT`.

**UNION*

The `UNION` operator takes two tables as input and returns all records from both tables. If two records are identical, `UNION` will return them only once. There is an additional operator for unions called `UNION ALL`. In contrast to `UNION`, given the same two tables, `UNION ALL` includes duplicate records.

**UNION and UNION ALL Syntax*

You perform a `SELECT` statement on our first table, a `SELECT` statement on our second table, and specify a set operation.

```sql
SELECT *
FROM left_table
UNION
SELECT *
FROM right_table;
```

```sql
SELECT *
FROM left_table
UNION ALL
SELECT *
FROM right_table;
```

Instead of comparing and merging tables left and right, they stack fields on top of each other.

For all set operations, the number of selected columns and their respective data types must be identical.

**Exercises:**

**UNION vs. UNION ALL**

Nice work learning all about UNION and UNION ALL!

Two tables, languages and currencies, are provided. Run the queries provided in the console and select the correct answer for the multiple-choice questions in this exercise.

**Instructions:**

- Question: What result will the following SQL query produce?

SELECT * 
FROM languages
UNION
SELECT * 
FROM currencies;

        "A SQL error, because languages and currencies do not have the same number of fields"

- Question: What result will the following SQL query produce?

SELECT code FROM
languages
UNION ALL
SELECT code FROM 
currencies;

        "An unordered list of each country code in languages and currencies, including duplicates"

- Question: What will the following SQL query produce?

SELECT code 
FROM languages
UNION
SELECT curr_id 
FROM currencies;

         "A SQL error, because code and curr_id are not of the same data type"

**Comparing global economies**

Are you ready to perform your first set operation?

In this exercise, you have two tables, economies2015 and economies2019, available to you under the tabs in the console. You'll perform a set operation to stack all records in these two tables on top of each other, excluding duplicates.

When drafting queries containing set operations, it is often helpful to write the queries on either side of the operation first, and then call the set operator. The instructions are ordered accordingly.

**Instructions:**

- Begin your query by selecting all fields from economies2015.

- Create a second query that selects all fields from economies2019.

- Perform a set operation to combine the two queries you just created, ensuring you do not return duplicates.

```sql
-- Select all fields from economies2015
SELECT *
FROM economies2015  
-- Set operation
UNION
-- Select all fields from economies2019
SELECT *
FROM economies2019
ORDER BY code, year;
```

**Comparing two set operations**

You learned in the video exercise that UNION ALL returns duplicates, whereas UNION does not. In this exercise, you will dive deeper into this, looking at cases for when UNION is appropriate compared to UNION ALL.

You will be looking at combinations of country code and year from the economies and populations tables.

**Instructions:**

- Perform an appropriate set operation that determines all pairs of country code and year (in that order) from economies and populations, excluding duplicates.
Order by country code and year.

```sql
-- Query that determines all pairs of code and year from economies and populations, without duplicates
SELECT code, year
FROM economies
UNION
SELECT country_code, year
FROM populations
```

- Amend the query to return all combinations (including duplicates) of country code and year in the economies or the populations tables.

```sql
SELECT code, year
FROM economies
-- Set theory clause
UNION ALL
SELECT country_code, year
FROM populations
ORDER BY code, year;
```

# At the INTERSECT

**Personal notes:**

`INTERSECT` takes two tables as input and returns only the records that exist in both tables.

**INTERSECT Syntax*

The syntax for this set operation is similar to that of `UNION` and `UNION ALL`. You perform a `SELECT` statement on our first table, a `SELECT` on the second table, and specify our set operator between them:

```sql
SELECT id, val
FROM left_table
INTERSECT
SELECT id, val
FROM right_table;
```

Similar to `UNION`, for a record to be returned, `INTERSECT` requires all fields to match because in set operations, we don't specify any fields to match. This is also why it requires the left and right tables to have the same number of columns for comparing records.

**Exercises:**

**Review UNION and INTERSECT**

Which of the following definitions of set operations is correct?

        "UNION: returns all records (potentially duplicates) in both tables"

# EXCEPT

**Personal notes:**

`EXCEPT` allows us to identify the records that are present in one table but not in another. More specifically, it retains only the records from the left table that are not present in the right table.

**EXCEPT Syntax*

For example, the SQL code shown in the lesson selects the `monarch` and `country` fields from the `monarchs` table and then looks for common entries in the `prime_minister` and `country` fields in the `prime_ministers` table, aiming to exclude those entries:

```sql
SELECT monarch, country
FROM monarchs
EXCEPT
SELECT prime_minister, country
FROM prime_ministers;
```

This operation is similar to a set difference, where it returns the records from the first table that are not found in the second table.

**Exercises:**

**You've got it, EXCEPT...**

Just as you were able to leverage INTERSECT to find the names of cities with the same names as countries, you can also do the reverse, using EXCEPT.

In this exercise, you will find the names of cities that do not have the same names as their countries.

**Instruction:** 

- Return all cities that do not have the same name as a country.

```sql
-- Return all cities that do not have the same name as a country
SELECT name
FROM cities
EXCEPT
SELECT name
FROM countries
ORDER BY name;
```
