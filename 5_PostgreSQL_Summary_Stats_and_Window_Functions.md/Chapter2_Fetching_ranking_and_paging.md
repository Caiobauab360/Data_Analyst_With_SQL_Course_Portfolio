# Fetching, ranking, and paging

**Chapter description:**

In this chapter, you'll learn three practical applications of window functions: fetching values from different parts of the table, ranking rows according to their values, and binning rows into different tables.

# Fetching

**Personal notes:**

**Fetching Functions: LEAD, FIRST_VALUE, and LAST_VALUE*

We have already seen the `LAG` function, which returns the value n rows before the current row. The `LEAD` clause is almost the same, except that it returns the value n rows AFTER the current row. `LAG` and `LEAD` are relative fetching functions because the value they retrieve is always relative to the current row.

The other two fetching functions are `FIRST_VALUE` and `LAST_VALUE`. `FIRST_VALUE` returns the first value in the table or partition, while `LAST_VALUE` returns the last value in the table or partition. These two functions are absolute fetching functions because what they return does not depend on the current row and is absolute in relation to the table or partition.

**LEAD Function Example*

This query retrieves the cities where each set of Olympic Games was held, as well as the next two cities. Passing 1 as n to the `LEAD` function will get the next city, and passing 2 as n will get the city after that. Similar to `LAG`, `LEAD` will return NULL once it runs out of following rows.

```sql
WITH Hosts AS (
	SELECT DISTINCT Year, City
	FROM Summer_Medals)

SELECT 
	Year, City,
	LEAD(City, 1) OVER (ORDER BY Year ASC) AS Next_City,
	LEAD(City, 2) OVER (ORDER BY Year ASC) AS After_Next_City
FROM Hosts
ORDER BY Year ASC;
```

In this query, the last row will have a `Next_City` of NULL since there are no rows after it to fetch its value, and the penultimate row will have an `After_Next_City` of NULL since `LEAD City, 2` looks for the value two rows after the current row, and this row for the penultimate row does not exist.

**FIRST_VALUE and LAST_VALUE Function Example*

This query is similar to the previous one, except that it retrieves the first and last cities where the Olympic Games were held in this table.

```sql
SELECT 
	Year, City,
	FIRST_VALUE(City) OVER (ORDER BY Year ASC) AS First_City,
	LAST_VALUE(City) OVER (ORDER BY Year ASC RANGE BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS Last_City
FROM Hosts
ORDER BY Year ASC;
```

**Exercises:**

**Future gold medalists**

Fetching functions allow you to get values from different parts of the table into one row. If you have time-ordered data, you can "peek into the future" with the LEAD fetching function. This is especially useful if you want to compare a current value to a future value.

**Instruction:**

- For each year, fetch the current gold medalist and the gold medalist 3 competitions ahead of the current row.

```sql
WITH Discus_Medalists AS (
SELECT DISTINCT
    Year,
    Athlete
FROM Summer_Medals
WHERE Medal = 'Gold'
    AND Event = 'Discus Throw'
    AND Gender = 'Women'
    AND Year >= 2000)

SELECT
-- For each year, fetch the current and future medalists
year,
Athlete,
LEAD(Athlete, 3) OVER (ORDER BY year ASC) AS Future_Champion
FROM Discus_Medalists
ORDER BY Year ASC;
```

**First athlete by name**

It's often useful to get the first or last value in a dataset to compare all other values to it. With absolute fetching functions like FIRST_VALUE, you can fetch a value at an absolute position in the table, like its beginning or end.

**Instruction:**

- Return all athletes and the first athlete ordered by alphabetical order.

```sql
WITH All_Male_Medalists AS (
SELECT DISTINCT
    Athlete
FROM Summer_Medals
WHERE Medal = 'Gold'
    AND Gender = 'Men')

SELECT
-- Fetch all athletes and the first athlete alphabetically
athlete,
FIRST_VALUE(athlete) OVER (
    ORDER BY athlete ASC
) AS First_Athlete
FROM All_Male_Medalists;
```

**Last country by name**

Just like you can get the first row's value in a dataset, you can get the last row's value. This is often useful when you want to compare the most recent value to previous values.

**Instructions:**

- Return the year and the city in which each Olympic games were held.

- Fetch the last city in which the Olympic games were held.

```sql
WITH Hosts AS (
SELECT DISTINCT Year, City
    FROM Summer_Medals)

SELECT
Year,
City,
-- Get the last city in which the Olympic games were held
LAST_VALUE(city) OVER (
ORDER BY Year ASC
RANGE BETWEEN
    UNBOUNDED PRECEDING AND
    UNBOUNDED FOLLOWING
) AS Last_City
FROM Hosts
ORDER BY Year ASC;
```

# Ranking

**Personal notes:**

**Ranking Functions: ROW_NUMBER, RANK, and DENSE_RANK*

Another common application of window functions is ranking rows based on their values. Let's learn about the three ranking functions in this lesson. Assigning numbers to rows is one way to rank them. `ROW_NUMBER` always assigns unique numbers, even if the values of two rows are equal. It picks some other metric to assign numbers if the value you're ordering by is the same.

Another ranking function is `RANK`. It assigns the same number to rows with identical values, skipping the next numbers in such cases. `DENSE_RANK` also assigns the same number to rows with identical values but doesn't skip the next numbers.

**Example 1*

This query returns the number of Olympic Games each of these countries participated in. Note that some countries, like France and Denmark, participated in the same number of games.

```sql
SELECT
	Country, COUNT(DISTINCT Year) AS Games
FROM Summer_Medals
WHERE
	Country IN ('GBR', 'DEN, 'FRA', 'ITA', 'AUT', 'BEL', 'NOR', 'POL', 'ESP')
GROUP BY Country
ORDER BY Games DESC;
```

Let's apply the three ranking functions to see how each one deals with these repeated values. After wrapping the previous query in a CTE and applying the ranking functions, we'll observe the differences in the query output.

```sql
WITH Country_Games AS (...)

SELECT
	Country, Games,
	ROW_NUMBER() OVER (ORDER BY Games DESC) AS Row_N
FROM Country_Games
ORDER BY Games DESC, Country ASC;
```

The output of this query using `ROW_NUMBER()` is familiar; it assigns a unique number to each row based on the provided order. If two rows have the same value, it will still assign unique numbers to them, but based on an internally selected order.

```sql
WITH Country_Games AS (...)

SELECT
	Country, Games,
	ROW_NUMBER() OVER (ORDER BY Games DESC) AS Row_N
	RANK() OVER (ORDER BY Games DESC) AS Rank_N
FROM Country_Games
ORDER BY Games DESC, Country ASC;
```

Using `RANK()`, it will assign the same number to two rows if their values are equal; Denmark and France are tied for second place. After the repeated rankings, it jumps to the next ranking and immediately goes to four for Italy. `RANK` always skips the next numbers if it assigns the same number to two or more rows.

```sql
WITH Country_Games AS (...)

SELECT
	Country, Games,
	ROW_NUMBER() OVER (ORDER BY Games DESC) AS Row_N
	RANK() OVER (ORDER BY Games DESC) AS Rank_N
	DENSE_RANK() OVER (ORDER BY Games DESC) AS Dense_Rank_N
FROM Country_Games
ORDER BY Games DESC, Country ASC;
```

`DENSE_RANK` also assigns 2 to Denmark and France, but instead of skipping the next ranking, it assigns 3 to Italy. It never skips rankings. `ROW_NUMBER` and `RANK` will have the same last ranking, the row count, while the last ranking of `DENSE_RANK` is the count of unique values being ranked.

**Example 2*

What if you have multiple groups you need to rank separately in your data? We can use ranking functions along with `PARTITION BY`.

```sql
WITH Country_Medals AS (...)

SELECT
	Country, Athlete
	DENSE_RANK()
		- OVER (PARTITION BY Country ORDER BY Medals DESC) AS Rank_N
FROM Country_Medals
ORDER BY Country ASC, Medals DESC;
```

After partitioning by country, Russian athletes are correctly assigned the second ranking because they are only being ranked in relation to the other athletes in their group or partition. If you have more than one group whose rows you want to rank, make sure to partition by the column that divides your rows into the correct groups.

**Exercises:**

**Ranking athletes by medals earned**

In chapter 1, you used ROW_NUMBER to rank athletes by awarded medals. However, ROW_NUMBER assigns different numbers to athletes with the same count of awarded medals, so it's not a useful ranking function; if two athletes earned the same number of medals, they should have the same rank.

**Instruction:**

- Rank each athlete by the number of medals they've earned -- the higher the count, the higher the rank -- with identical numbers in case of identical values.

```sql
WITH Athlete_Medals AS (
SELECT
    Athlete,
    COUNT(*) AS Medals
FROM Summer_Medals
GROUP BY Athlete)

SELECT
Athlete,
Medals,
-- Rank athletes by the medals they've won
RANK() OVER (ORDER BY Medals DESC) AS Rank_N
FROM Athlete_Medals
ORDER BY Medals DESC;
```

**Ranking athletes from multiple countries**

In the previous exercise, you used RANK to assign rankings to one group of athletes. In real-world data, however, you'll often find numerous groups within your data. Without partitioning your data, one group's values will influence the rankings of the others.

Also, while RANK skips numbers in case of identical values, the most natural way to assign rankings is not to skip numbers. If two countries are tied for second place, the country after them is considered to be third by most people.

**Instruction:**

- Rank each country's athletes by the count of medals they've earned -- the higher the count, the higher the rank -- without skipping numbers in case of identical values.

```sql
WITH Athlete_Medals AS (
SELECT
    Country, Athlete, COUNT(*) AS Medals
FROM Summer_Medals
WHERE
    Country IN ('JPN', 'KOR')
    AND Year >= 2000
GROUP BY Country, Athlete
HAVING COUNT(*) > 1)

SELECT
Country,
-- Rank athletes in each country by the medals they've won
Athlete,
DENSE_rank() OVER (PARTITION BY country
                ORDER BY Medals DESC) AS Rank_N
FROM Athlete_Medals
ORDER BY Country ASC, RANK_N ASC;
```

**DENSE_RANK's output**

You have the following table:

        | Country | Medals |
        |---------|--------|
        | IRN     | 23     |
        | IRQ     | 19     |
        | LBN     | 19     |
        | SYR     | 19     |
        | BHR     | 7      |
        | KSA     | 3      |

If you were to use DENSE_RANK to order the Medals column in descending order, what rank would BHR be assigned?

        Answer: 5

# Paging

**Personal notes:**

Paging involves dividing data into (approximately) equal blocks. APIs, which are interfaces for data exchange between different web platforms, often return data in pages to reduce the size of the data being sent and make it more on-demand. Additionally, when data is sorted by a metric, separating the data into quartiles or thirds can help assess performance, as you can see if a data point is in the top third, middle, or bottom.

In SQL, to achieve pagination, you can use `NTILE(n)`, which is a window function that takes n as input and then divides the data into approximately n equal pages.

**Paging - Source table*

This query returns all 67 unique disciplines. Splitting them into 15 approximately equal pages will result in about 4 or 5 rows on each page. The reason the number of rows per page is not constant is that 67 is not divisible by 15, so there will be overflow on some pages.

```sql
SELECT
	DISTINCT Discipline
FROM Summer_Medals;
```

Putting the above query into a CTE and using `NTILE`, passing n as 15, will result in dividing the rows from the query into 15 pages, with about four or five rows per page. For example, page 1 has 5 rows, but page 2 has 4, although the results are truncated here for space.

```sql
WITH Disciplines AS (
	SELECT
		DISTINCT Disciplines
	FROM Summer_Medals)

SELECT 
	Discipline, 
	NTILE(15) OVER() AS Page
FROM Disciplines
ORDER BY Page ASC;
```

**Top, Middle, and Bottom Thirds*

Another use of `NTILE` is to divide the data into thirds or quartiles. In this query, the CTE 'Country_Medals' counts the number of medals each country received overall in each set of Olympic Games. Using `NTILE` and passing n as 3, and ordering by the awarded medals in descending order, the query results will be divided into thirds, with the top 33% of countries by awarded medals in the top third (with the value of the third column being 1), the middle 33% in the middle third (with the value of the third column being 2), and the bottom 33% in the bottom third (with the value of the third column being 3). This way, you can easily label the top, middle, or bottom % of your data.

```sql
WITH Country_Medals AS (
	SELECT
		Country, COUNT(*) AS Medals
	FROM Summer_Medals
	GROUP BY Country),

SELECT 
	Country, Medals,
	NTILE(3) OVER (ORDER BY Medals DESC) AS Third
FROM Country_Medals;
```

**Exercises:**

**Paging events**

There are exactly 666 unique events in the Summer Medals Olympics dataset. If you want to chunk them up to analyze them piece by piece, you'll need to split the events into groups of approximately equal size.

**Instruction:**

- Split the distinct events into exactly 111 groups, ordered by event in alphabetical order.

```sql
WITH Events AS (
SELECT DISTINCT Event
FROM Summer_Medals)

SELECT
--- Split up the distinct events into 111 unique groups
Event,
NTILE(111) OVER (ORDER BY event ASC) AS Page
FROM Events
ORDER BY Event ASC;
```

**Top, middle, and bottom thirds**

Splitting your data into thirds or quartiles is often useful to understand how the values in your dataset are spread. Getting summary statistics (averages, sums, standard deviations, etc.) of the top, middle, and bottom thirds can help you determine what distribution your values follow.

**Instructions:**

- Split the athletes into top, middle, and bottom thirds based on their count of medals.

```sql
WITH Athlete_Medals AS (
SELECT Athlete, COUNT(*) AS Medals
FROM Summer_Medals
GROUP BY Athlete
HAVING COUNT(*) > 1)

SELECT
Athlete,
Medals,
-- Split athletes into thirds by their earned medals
NTILE(3) OVER (ORDER BY Medals DESC) AS Third
FROM Athlete_Medals
ORDER BY Medals DESC, Athlete ASC;
```

- Return the average of each third.

```sql
WITH Athlete_Medals AS (
SELECT Athlete, COUNT(*) AS Medals
FROM Summer_Medals
GROUP BY Athlete
HAVING COUNT(*) > 1),

Thirds AS (
SELECT
    Athlete,
    Medals,
    NTILE(3) OVER (ORDER BY Medals DESC) AS Third
FROM Athlete_Medals)

SELECT
-- Get the average medals earned in each third
Third,
AVG(Medals) AS Avg_Medals
FROM Thirds
GROUP BY Third
ORDER BY Third ASC;