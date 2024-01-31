# PostgreSQL Summary Stats and Window Functions

**Course description:**

Have you ever wondered how data professionals use SQL to solve real-world business problems, like generating rankings, calculating moving averages and running totals, deduplicating data, or performing time intelligence? If you already know how to select, filter, order, join and group data with SQL, this course is your next step. By the end, you will be writing queries like a pro! You will learn how to create queries for analytics and data engineering with window functions, the SQL secret weapon! Using flights data, you will discover how simple it is to use window functions, and how flexible and efficient they are.

# Introduction to window functions

**Chapter description:**

In this chapter, you'll learn what window functions are, and the two basic window function subclauses, ORDER BY and PARTITION BY.

# Introduction

**Personal notes:**

**Introduction to Window Functions*

Throughout this chapter, we will be using the dataset of the Summer Olympics, with each row representing a awarded medal. The columns include the year, city, sport, discipline, event, athlete's name, country, gender, and the type of medal awarded.

**Row Numbers*

The most basic thing you can do with a window function is assigning row numbers. Row numbers allow you to reference a row by its position or index rather than its values. Row number assigns a number to each row; in this query, it simply adds a column with the number or index of each row:

```sql
SELECT
	year, event, country,
	ROW_NUMBER() OVER() AS row_n
FROM summer_medals
WHERE medal = 'Gold';
```

The `OVER` clause indicates that a function is of the window type.

**Exercises:**

**Window functions vs GROUP BY**

Which of the following is FALSE?

        "Window functions can open a "window" to another table, whereas GROUP BY functions cannot."

**Numbering rows**

The simplest application for window functions is numbering rows. Numbering rows allows you to easily fetch the nth row. For example, it would be very difficult to get the 35th row in any given table if you didn't have a column with each row's number.

**Instruction:**

- Number each row in the dataset.
        
```sql
SELECT
*,
-- Assign numbers to each row
ROW_NUMBER() OVER() AS Row_N
FROM Summer_Medals
ORDER BY Row_N ASC;
```

**Numbering Olympic games in ascending order**

The Summer Olympics dataset contains the results of the games between 1896 and 2012. The first Summer Olympics were held in 1896, the second in 1900, and so on. What if you want to easily query the table to see in which year the 13th Summer Olympics were held? You'd need to number the rows for that.

**Instruction:**

- Assign a number to each year in which Summer Olympic games were held.

```sql      
SELECT
Year,

-- Assign numbers to each year
ROW_NUMBER() OVER() AS Row_N
FROM (
SELECT DISTINCT (year)
FROM Summer_Medals
ORDER BY Year ASC
) AS Years
ORDER BY Year ASC;
```
# ORDER BY

**Personal notes:**

**ORDER BY in Window Functions*

`ORDER BY` is a subsection of `OVER`. It sorts the rows related to the current row that the window function will use.

For example, taking the previous query, if you order by year in descending order within the `OVER` clause, the function will assign 1 to the first row of that window. This row will have the most recent year in the dataset:

```sql
SELECT
	year, event, country,
	ROW_NUMBER() OVER(ORDER BY Year DESC) AS row_n
FROM summer_medals
WHERE medal = 'Gold';
```

Adding `ORDER BY` to `OVER` changed the basis on which the `ROW NUMBER` function assigned numbers to rows, assigning lower numbers to more recent rows. And if you want to sort by both year and event? You can order by multiple columns in the `OVER` clause, just as you would normally outside it:

```sql
SELECT
	year, event, country,
	ROW_NUMBER() OVER(ORDER BY Year DESC, Event DESC) AS row_n
FROM summer_medals
WHERE medal = 'Gold';
```

You can use `ORDER BY` both inside and outside at the same time. For example, in this query, row numbers are assigned based on the year and event within `OVER`. After that, the `ORDER BY` outside `OVER` takes over and sorts the table results by country and row number. So, the `ORDER BY` inside `OVER` has an effect before the `ORDER BY` outside the `OVER` function:

```sql
SELECT
	year, event, country,
	ROW_NUMBER() OVER(ORDER BY Year DESC, Event DESC) AS row_n
FROM summer_medals
WHERE medal = 'Gold'
ORDER BY Country ASC, Row_n ASC;
```

**Reigning Champion*

One application of window functions is determining the reigning champion, which would be a champion who won in both the previous and the current years. To determine if a champion is the current champion, champions from the previous and current years need to be on the same row but in two different columns, so they can be compared. How do you get the value from a previous row without complex self-joins? Enter `LAG`.

`LAG` is a window function that takes a column and a number `n` and returns the value of the column `n` rows before the current row. For example, passing 1 as `n` returns the value from the previous row:

```sql
LAG(column, n) OVER()
```

This query returns the champions of the discus throw event. Getting the champion of each year is the first step to using `LAG` to obtain the previous champion of each year:

```sql
SELECT
	Year, Country AS champion
FROM Summer_medals
WHERE
	Year IN (1996, 2000, 2004, 2008, 2012)
	AND Gender = 'Men' AND medal = 'Gold'
	AND Event = 'Discus Throw';
```

After wrapping the previous query in a CTE, select the year and champion, and use `LAG` on the champion column with an `n` of 1, ordered by year in ascending order in `OVER`:

```sql
WITH Discus_Gold AS (
	SELECT
		Year, Country AS champion
	FROM Summer_Medals
	WHERE 
		Year IN (1996, 2000, 2004, 2008, 2012)
		AND Gender = 'Men' AND Medal = 'Gold'
		AND Event = 'Discus Throw'
)

SELECT
	Year, Champion,
	LAG(champion, 1) OVER (ORDER BY Year DESC) AS Last_Champion
FROM Discus_gold
ORDER BY Year ASC;
```

**Exercises:**

**Numbering Olympic games in descending order**

You've already numbered the rows in the Summer Medals dataset. What if you need to reverse the row numbers so that the most recent Olympic games' rows have a lower number?

**Instruction:**

- Assign a number to each year in which Summer Olympic games were held so that rows with the most recent years have lower row numbers.

```sql 
SELECT
Year,
-- Assign the lowest numbers to the most recent years
ROW_NUMBER() OVER(ORDER BY Year DESC) AS Row_N
FROM (
SELECT DISTINCT Year
FROM Summer_Medals
) AS Years
ORDER BY Year;
```

**Numbering Olympic athletes by medals earned**

Row numbering can also be used for ranking. For example, numbering rows and ordering by the count of medals each athlete earned in the OVER clause will assign 1 to the highest-earning medalist, 2 to the second highest-earning medalist, and so on.

**Instructions:**

- For each athlete, count the number of medals he or she has earned.

```sql
SELECT
-- Count the number of medals each athlete has earned
athlete,
COUNT(medal) AS Medals
FROM Summer_Medals
GROUP BY Athlete
ORDER BY Medals DESC;
```

- Having wrapped the previous query in the Athlete_Medals CTE, rank each athlete by the number of medals they've earned.

```sql
WITH Athlete_Medals AS (
SELECT
        -- Count the number of medals each athlete has earned
        Athlete,
        COUNT(*) AS Medals
FROM Summer_Medals
GROUP BY Athlete)

SELECT
-- Number each athlete by how many medals they've earned
athlete,
ROW_NUMBER() OVER (ORDER BY medals DESC) AS Row_N
FROM Athlete_Medals
ORDER BY Medals DESC;
```

**Reigning weightlifting champions**

A reigning champion is a champion who's won both the previous and current years' competitions. To determine if a champion is reigning, the previous and current years' results need to be in the same row, in two different columns.

**Instructions:**

- Return each year's gold medalists in the Men's 69KG weightlifting competition.

```sql
SELECT
-- Return each year's champions' countries
year,
country AS champion
FROM Summer_Medals
WHERE
Discipline = 'Weightlifting' AND
Event = '69KG' AND
Gender = 'Men' AND
Medal = 'Gold';
```

- Having wrapped the previous query in the Weightlifting_Gold CTE, get the previous year's champion for each year.

```sql
WITH Weightlifting_Gold AS (
SELECT
        -- Return each year's champions' countries
        Year,
        Country AS champion
FROM Summer_Medals
WHERE
        Discipline = 'Weightlifting' AND
        Event = '69KG' AND
        Gender = 'Men' AND
        Medal = 'Gold')

SELECT
Year, Champion,
-- Fetch the previous year's champion
LAG(Champion,1) OVER
        (ORDER BY year ASC) AS Last_Champion
FROM Weightlifting_Gold
ORDER BY Year ASC;
```

# PARTITION BY

**Personal notes:**

**PARTITION BY in Window Functions*

This `OVER` subclause divides the table into partitions based on the unique values of a column, similar to `GROUP BY`. Unlike `GROUP BY`, however, the results of a window function with `PARTITION BY` are not grouped into a single column. The partitions are operated on separately by the window function.

**Partitioning by One Column*

Adding `PARTITION BY event` to the `OVER` clause before `ORDER BY` will separate the table into two partitions, one for discus throw and another for triple jump, the two unique values in the event column. The only difference in the results is that the first row of triple jump champions has NULL as the last champion. This is because it is the first row in its partition:

```sql
WITH Discus_gold AS (
	-- Previous query for discus throw champions
)

SELECT
	Year, Event, Champion,
	LAG(Champion) OVER(PARTITION BY Event ORDER BY Event ASC, Year ASC) AS Last_champion
FROM Discus_Gold
ORDER BY Event ASC, Year ASC;
```

**Exercises:**

**Reigning champions by gender**

You've already fetched the previous year's champion for one event. However, if you have multiple events, genders, or other metrics as columns, you'll need to split your table into partitions to avoid having a champion from one event or gender appear as the previous champion of another event or gender.

**Instruction:**

- Return the previous champions of each year's event by gender.

```sql
WITH Tennis_Gold AS (
SELECT DISTINCT
        Gender, Year, Country
FROM Summer_Medals
WHERE
        Year >= 2000 AND
        Event = 'Javelin Throw' AND
        Medal = 'Gold')

SELECT
Gender, Year,
Country AS Champion,
-- Fetch the previous year's champion by gender
LAG(country) OVER (PARTITION BY gender
                ORDER BY gender ASC) AS Last_Champion
FROM Tennis_Gold
ORDER BY Gender ASC, Year ASC;
```

**Reigning champions by gender and event**

In the previous exercise, you partitioned by gender to ensure that data about one gender doesn't get mixed into data about the other gender. If you have multiple columns, however, partitioning by only one of them will still mix the results of the other columns.

**Instruction:**

- Return the previous champions of each year's events by gender and event.

```sql
WITH Athletics_Gold AS (
SELECT DISTINCT
        Gender, Year, Event, Country
FROM Summer_Medals
WHERE
        Year >= 2000 AND
        Discipline = 'Athletics' AND
        Event IN ('100M', '10000M') AND
        Medal = 'Gold')

SELECT
Gender, Year, Event,
Country AS Champion,
-- Fetch the previous year's champion by gender and event
LAG(Country) OVER (PARTITION BY gender, event
                ORDER BY Year ASC) AS Last_Champion
FROM Athletics_Gold
ORDER BY Event ASC, Gender ASC, Year ASC;
```

**Row numbers with partitioning**

If you run ROW_NUMBER() OVER (PARTITION BY Year ORDER BY Medals DESC) on the following table, what row number would the 2008 Iranian record have?

        | Year | Country | Medals |
        |------|---------|--------|
        | 2004 | IRN     | 32     |
        | 2004 | LBN     | 17     |
        | 2004 | KSA     | 4      |
        | 2008 | IRQ     | 29     |
        | 2008 | IRN     | 27     |
        | 2008 | UAE     | 12     

    Answer: 2