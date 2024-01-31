# Outer Joins, Cross Joins and Self Joins

**Chapter description:**

After familiarizing yourself with inner joins, you will come to grips with different kinds of outer joins. Next, you will learn about cross joins. Finally, you will learn about situations in which you might join a table with itself.

# LEFT and RIGHT JOINs

**Personal notes:**

In this chapter, we will cover outer joins, cross joins, and self-joins. Outer joins can retrieve records from other tables even if there are no matches for the field being joined.

**LEFT JOIN*

A LEFT JOIN will return all records in the left_table, and those records in the right_table that match the provided join field. An INNER JOIN returns only records matching the ids, while LEFT JOIN keeps all records in the left_table, as well as null values for right_val where there is no match in right_table. For example, let's say we want our query to include all countries with prime ministers, presidents if they happen to have them, and null values if they don't:

```sql
SELECT p1.country, prime_minister, president
FROM prime_ministers AS p1
LEFT JOIN presidents AS p2
USING(country);
```

*Note that LEFT JOIN can also be written as LEFT OUTER JOIN in SQL.

**RIGHT JOIN*

This is the second type of outer join and is much less common than LEFT JOIN, so it won't be extensively covered in this course. Instead of matching entries in the id column of left_table to the id column of right_table, a RIGHT JOIN does the reverse. All records are retained from right_table, even when id doesn't find a match in left_table. Null values are returned for the left_value field in records that don't find a match. An example of how RIGHT JOIN is performed; note that the order left_table and right_table is the same as we did in LEFT JOIN:

```sql
SELECT *
FROM left_table
RIGHT JOIN right_table
ON left_table.id = right_table.id;
```

*Note that RIGHT JOIN can also be written as RIGHT OUTER JOIN in SQL.

One of the main reasons RIGHT JOIN is not common is because it can always be rewritten as a LEFT JOIN. As we typically read from left to right, LEFT JOIN seems more intuitive for most users when constructing queries.

**Exercises:**

**This is a LEFT JOIN, right?**

Nice work getting to grips with the structure of joins! In this exercise, you'll explore the differences between INNER JOIN and LEFT JOIN. This will help you decide which type of join to use.

As before, you will be using the cities and countries tables.

You'll begin with an INNER JOIN with the cities table (left) and countries table (right). This helps if you are interested only in records where a country is present in both tables.

You'll then change to a LEFT JOIN. This helps if you're interested in returning all countries in the cities table, whether or not they have a match in the countries table.

**Instructions:**

- Perform an inner join with cities AS c1 on the left and countries as c2 on the right.
Use code as the field to merge your tables on.

```sql
SELECT 
        c1.name AS city,
        code,
        c2.name AS country,
        region,
        city_proper_pop
FROM cities AS c1
-- Perform an inner join with cities as c1 and countries as c2 on country code
INNER JOIN countries AS c2
ON c1.country_code = c2.code
ORDER BY code DESC;
```

- Change the code to perform a LEFT JOIN instead of an INNER JOIN.
After executing this query, have a look at how many records the query result contains.

```sql
SELECT 
        c1.name AS city, 
        code, 
        c2.name AS country,
        region, 
        city_proper_pop
FROM cities AS c1
-- Join right table (with alias)
LEFT JOIN countries AS c2
ON c1.country_code = c2.code
ORDER BY code DESC;
```

**Building on your LEFT JOIN**

You'll now revisit the use of the AVG() function introduced in a previous course.

Being able to build more than one SQL function into your query will enable you to write compact, supercharged queries.

You will use AVG() in combination with a LEFT JOIN to determine the average gross domestic product (GDP) per capita by region in 2010.

**Instructions:**

- Complete the LEFT JOIN with the countries table on the left and the economies table on the right on the code field.

- Filter the records from the year 2010.

```sql
SELECT name, region, gdp_percapita
FROM countries AS c
LEFT JOIN economies AS e
-- Match on code fields
USING(code)
-- Filter for the year 2010
WHERE year = 2010;
```

- To calculate per capita GDP per region, begin by grouping by region.

- After your GROUP BY, choose region in your SELECT statement, followed by average GDP per capita using the AVG() function, with AS avg_gdp as your alias.

```sql
-- Select region, and average gdp_percapita as avg_gdp
SELECT region, AVG(gdp_percapita) AS avg_gdp
FROM countries AS c
LEFT JOIN economies AS e
USING(code)
WHERE year = 2010
-- Group by region
GROUP BY region;
```

- Order the result set by the average GDP per capita from highest to lowest.

- Return only the first 10 records in your result.

```sql
SELECT region, AVG(gdp_percapita) AS avg_gdp
FROM countries AS c
LEFT JOIN economies AS e
USING(code)
WHERE year = 2010
GROUP BY region
-- Order by descending avg_gdp
ORDER BY avg_gdp DESC 
-- Return only first 10 records
LIMIT 10;
```

**Is this RIGHT?**

You learned that right joins are not used as commonly as left joins. A key reason for this is that right joins can always be re-written as left joins, and because joins are typically typed from left to right, joining from the left feels more intuitive when constructing queries.

It can be tricky to wrap one's head around when left and right joins return equivalent results. You'll explore this in this exercise!

**Instructions:**

- Write a new query using RIGHT JOIN that produces an identical result to the LEFT JOIN provided.

```sql
-- Modify this query to use RIGHT JOIN instead of LEFT JOIN
SELECT languages.name AS language, countries.name AS country, percent
FROM languages
RIGHT JOIN countries
USING(code)
ORDER BY countries;
```

# FULL JOINs

**Personal notes:**

FULL JOIN is the last of the three types of outer joins. A FULL JOIN combines a LEFT JOIN and a RIGHT JOIN. This type of join will return all ids because the FULL JOIN will return all ids, regardless of whether they have a match in the other table being joined. An example of a FULL JOIN:

```sql
SELECT 
	left_table.id AS L_id,
	right_table.id AS R_id,
	left_table.val AS L_val,
	right_table.val AS R_val
FROM left_table
FULL JOIN right_table
USING(id);
```

Note that the keyword FULL OUTER JOIN can also be used to return the same result in SQL.

For example, suppose we were interested in all countries in our database and wanted to check if they had a president, a prime minister, or both. Let's go through the code line by line to do this using a FULL JOIN. The SELECT statement begins by including the country as well as the prime_minister and president fields. Then, we specify prime_ministers as our left table and alias it as p1. Next, we add the FULL JOIN statement and add presidents as the right table, using the alias p2. Finally, the join is performed using the country as the join field in both tables.

```sql
SELECT p1.country AS country, prime_minister, president
FROM prime_minister AS p1
FULL JOIN presidents AS p2
ON p1.country = p2.country
LIMIT 10;
```

**Exercises:**

**Comparing joins**

In this exercise, you'll examine how results can differ when performing a full join compared to a left join and inner join by joining the countries and currencies tables. You'll be focusing on the North American region and records where the name of the country is missing.

You'll begin with a full join with countries on the left and currencies on the right. Recall the workings of a full join with the diagram below!

You'll then complete a similar left join and conclude with an inner join, observing the results you see along the way.

**Instructions:**

- Perform a full join with countries (left) and currencies (right).
Filter for the North America region or NULL country names.

```sql
SELECT name AS country, code, region, basic_unit
FROM countries
-- Join to currencies
FULL JOIN currencies
USING (code)
-- Where region is North America or name is null
WHERE region = 'North America'
        OR name is null
ORDER BY region;
```

- Repeat the same query as before, turning your full join into a left join with the currencies table.
Have a look at what has changed in the output by comparing it to the full join result.

```sql
SELECT name AS country, code, region, basic_unit
FROM countries
-- Join to currencies
LEFT JOIN currencies
USING (code)
WHERE region = 'North America' 
        OR name IS NULL
ORDER BY region;
```

- Repeat the same query again, this time performing an inner join of countries with currencies.
Have a look at what has changed in the output by comparing it to the full join and left join results!

```sql
SELECT name AS country, code, region, basic_unit
FROM countries
-- Join to currencies
INNER JOIN currencies
USING (code)
WHERE region = 'North America' 
        OR name IS NULL
ORDER BY region;
```

**Chaining FULL JOINs**

As you have seen in the previous chapter on INNER JOIN, it is possible to chain joins in SQL, such as when looking to connect data from more than two tables.

Suppose you are doing some research on Melanesia and Micronesia, and are interested in pulling information about languages and currencies into the data we see for these regions in the countries table. Since languages and currencies exist in separate tables, this will require two consecutive full joins involving the countries, languages and currencies tables.

**Instructions:**

- Complete the FULL JOIN with countries as c1 on the left and languages as l on the right, using code to perform this join.

- Next, chain this join with another FULL JOIN, placing currencies on the right, joining on code again.

```sql
SELECT 
        c1.name AS country, 
        region, 
        l.name AS language,
        basic_unit, 
        frac_unit
FROM countries as c1 
-- Full join with languages (alias as l)
FULL JOIN languages as l
USING(code)
-- Full join with currencies (alias as c2)
FULL JOIN currencies AS c2
USING(code)
WHERE region LIKE 'M%esia';
```

# Crossing into CROSS JOIN

**Personal notes:**

CROSS JOINs are different from the joins we've seen earlier: they create all possible combinations of two tables. Note that the syntax is minimal, and we don't specify ON or USING with CROSS JOIN:

```sql
SELECT id1, id2
FROM table1
CROSS JOIN table2;
```

For example, suppose all the prime ministers from Asia in our database have individual meetings scheduled with all the presidents from South America in our database, and we are journalists wanting to track all these meetings that will take place. We can create a query that returns all these combinations using a CROSS JOIN. We use a WHERE clause to focus only on prime ministers from Asia and presidents from South America.

```sql
SELECT prime_minister, president
FROM prime_ministers AS p1
CROSS JOIN presidents AS p2
WHERE p1.continent IN ('Asia')
	AND p2.continent IN ('South America');
```

**Exercises:**

**Histories and languages**

Well done getting to know all about CROSS JOIN! As you have learned, CROSS JOIN can be incredibly helpful when asking questions that involve looking at all possible combinations or pairings between two sets of data.

Imagine you are a researcher interested in the languages spoken in two countries: Pakistan and India. You are interested in asking:

What are the languages presently spoken in the two countries?
Given the shared history between the two countries, what languages could potentially have been spoken in either country over the course of their history?
In this exercise, we will explore how INNER JOIN and CROSS JOIN can help us answer these two questions, respectively.

**Instructions:**

- Complete the code to perform an INNER JOIN of countries AS c with languages AS l using the code field to obtain the languages currently spoken in the two countries.

```sql
SELECT c.name AS country, l.name AS language
-- Inner join countries as c with languages as l on code
FROM countries AS c
INNER JOIN languages AS l
USING(code)
WHERE c.code IN ('PAK','IND')
        AND l.code in ('PAK','IND');
```

- Change your INNER JOIN to a different kind of join to look at possible combinations of languages that could have been spoken in the two countries given their history.

- Observe the differences in output for both joins.

```sql
SELECT c.name AS country, l.name AS language
FROM countries AS c        
-- Perform a cross join to languages (alias as l)
CROSS JOIN languages AS l
WHERE c.code in ('PAK','IND')
        AND l.code in ('PAK','IND');
```

**Choosing your join**

Now that you're fully equipped to use joins, try a challenge problem to test your knowledge!

You will determine the names of the five countries and their respective regions with the lowest life expectancy for the year 2010. Use your knowledge about joins, filtering, sorting and limiting to create this list!

**Instructions:**

- Complete the join of countries AS c with populations as p.

- Filter on the year 2010.

- Sort your results by life expectancy in ascending order.

- Limit the result to five countries.

```sql
SELECT 
        c.name AS country,
        region,
        life_expectancy AS life_exp
FROM countries AS c
-- Join to populations (alias as p) using an appropriate join
LEFT JOIN populations as p
ON c.code = p.country_code
-- Filter for only results in the year 2010
WHERE year = 2010
-- Sort by life_exp
ORDER BY life_exp
-- Limit to five records
LIMIT 5;
```

# Self joins

**Personal notes:**

Self joins are automatic joins where a table is joined with itself. Self joins are used to compare values from part of a table with other values in the same table. For example, suppose all prime ministers are meeting in summits in their own continents. We want to create a new table showing all countries from the same continent as pairs.

Self joins don't have dedicated syntax like other joins we've seen. We can't simply write `SELF JOIN` in SQL code. Also, an alias is necessary for a self join. 

```sql
SELECT
    p1.country AS country1,
    p2.country AS country2,
    p1.continent
FROM prime_ministers AS p1
INNER JOIN prime_ministers AS p2
ON p1.continent = p2.continent
LIMIT 10;
```

In this case, the join will pair countries, including with themselves. To address this, we can use the `AND` clause to ensure multiple conditions are met in the `ON` clause. In our second condition, we use the not equal operator to exclude records that are identical.

```sql
SELECT
    p1.country AS country1,
    p2.country AS country2,
    p1.continent
FROM prime_ministers AS p1
INNER JOIN prime_ministers AS p2
ON p1.continent = p2.continent
    AND p1.country <> p2.country;
```

**Exercises:**

**Comparing a country to itself**

Self joins are very useful for comparing data from one part of a table with another part of the same table. Suppose you are interested in finding out how much the populations for each country changed from 2010 to 2015. You can visualize this change by performing a self join.

In this exercise, you'll work to answer this question by joining the populations table with itself. Recall that, with self joins, tables must be aliased. Use this as an opportunity to practice your aliasing!

Since you'll be joining the populations table to itself, you can alias populations first as p1 and again as p2. This is good practice whenever you are aliasing tables with the same first letter.

**Instructions:**

- Perform an inner join of populations with itself ON country_code, aliased p1 and p2 respectively.

- Select the country_code from p1 and the size field from both p1 and p2, aliasing p1.size as size2010 and p2.size as size2015 (in that order).

```sql
-- Select aliased fields from populations as p1
SELECT p1.country_Code, p1.size AS size2010, p2.size AS size2015
-- Join populations as p1 to itself, alias as p2, on country code
FROM populations AS p1
INNER JOIN populations AS p2
ON p1.country_code = p2.country_code;
```

- Since you want to compare records from 2010 and 2015, eliminate unwanted records by extending the WHERE statement to include only records where the p1.year matches p2.year - 5.

```sql
SELECT 
        p1.country_code, 
        p1.size AS size2010, 
        p2.size AS size2015
FROM populations AS p1
INNER JOIN populations AS p2
ON p1.country_code = p2.country_code
WHERE p1.year = 2010
-- Filter such that p1.year is always five years before p2.year
AND p1.year = p2.year-5;
```