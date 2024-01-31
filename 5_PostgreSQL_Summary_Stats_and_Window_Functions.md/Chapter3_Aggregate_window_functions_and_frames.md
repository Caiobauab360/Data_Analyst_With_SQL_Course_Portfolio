# Aggregate window functions and frames

**Chapter description:**

In this chapter, you'll learn how to use aggregate functions you're familiar with, like `AVG()` and `SUM()`, as window functions, as well as how to define frames to change a window function's output.

# Aggregate window functions

**Personal notes:**

You can use MAX and SUM, as well as other aggregate functions like COUNT, MIN, and AVG, as window functions. Using aggregate functions as window functions can be more efficient. Like any other window function, you can partition with aggregate functions.

**Example: Using MAX as a Window Function*

In this example, we want to find the highest number of medals awarded in a single discipline in each year. We can use the MAX function as a window function for this purpose. The result will show, for each year, the highest number of medals awarded in any discipline.

```sql
SELECT
	Year,
	Discipline,
	Medals,
	MAX(Medals) OVER (PARTITION BY Year) AS Max_Medals_In_Year
FROM Summer_Medals
ORDER BY Year, Medals DESC;
```

In this query, `MAX(Medals) OVER (PARTITION BY Year)` is used as a window function, and it calculates the maximum number of medals over each partition defined by the `Year`. This allows us to see the highest number of medals awarded in a discipline for each year.

**Example: Using SUM as a Window Function with PARTITION BY*

Now, let's say we want to find the total number of medals awarded in each discipline for each year. We can use the SUM function as a window function with the PARTITION BY clause to achieve this.

```sql
SELECT
	Year,
	Discipline,
	Medals,
	SUM(Medals) OVER (PARTITION BY Year, Discipline) AS Total_Medals_In_Discipline
FROM Summer_Medals
ORDER BY Year, Discipline;
```

In this query, `SUM(Medals) OVER (PARTITION BY Year, Discipline)` calculates the total number of medals for each discipline within each year. The PARTITION BY clause ensures that the summation is done separately for each combination of Year and Discipline.

You can similarly use other aggregate functions like COUNT, MIN, and AVG as window functions, and the PARTITION BY clause allows you to perform these calculations within specific partitions of your data.

**Exercises:**

**Running totals of athlete medals**

The running total (or cumulative sum) of a column helps you determine what each row's contribution is to the total sum.

**Instruction:**

- Return the athletes, the number of medals they earned, and the medals running total, ordered by the athletes' names in alphabetical order.

```sql
WITH Athlete_Medals AS (
SELECT
    Athlete, COUNT(*) AS Medals
FROM Summer_Medals
WHERE
    Country = 'USA' AND Medal = 'Gold'
    AND Year >= 2000
GROUP BY Athlete)

SELECT
-- Calculate the running total of athlete medals
athlete,
Medals,
SUM(medals) OVER (ORDER BY Athlete ASC) AS Max_Medals
FROM Athlete_Medals
ORDER BY Athlete ASC;
```

**Maximum country medals by year**

Getting the maximum of a country's earned medals so far helps you determine whether a country has broken its medals record by comparing the current year's earned medals and the maximum so far.

- Return the year, country, medals, and the maximum medals earned so far for each country, ordered by year in ascending order.

```sql
WITH Country_Medals AS (
SELECT
    Year, Country, COUNT(*) AS Medals
FROM Summer_Medals
WHERE
    Country IN ('CHN', 'KOR', 'JPN')
    AND Medal = 'Gold' AND Year >= 2000
GROUP BY Year, Country)

SELECT
-- Return the max medals earned so far per country
Year,
country,
medals,
MAX(medals) OVER (PARTITION BY country
                ORDER BY Year ASC) AS Max_Medals
FROM Country_Medals
ORDER BY Country ASC, Year ASC;
```

**Minimum country medals by year**

So far, you've seen MAX and SUM, aggregate functions normally used with GROUP BY, being used as window functions. You can also use the other aggregate functions, like MIN, as window functions.

**Instruction:**

- Return the year, medals earned, and minimum medals earned so far.

```sql
WITH France_Medals AS (
SELECT
    Year, COUNT(*) AS Medals
FROM Summer_Medals
WHERE
    Country = 'FRA'
    AND Medal = 'Gold' AND Year >= 2000
GROUP BY Year)

SELECT
Year,
Medals,
MIN(Medals) OVER (ORDER BY Year ASC) AS Min_Medals
FROM France_Medals
ORDER BY Year ASC;
```

# Frames

**Personal notes:**

Another way to alter the behavior of a Window function is by defining a frame. By default, a frame starts at the beginning of a table or partition and ends at the current row. Frames allow you to change the behavior of a window function by specifying which rows the function takes as input.

**ROWS BETWEEN Clause*

- `ROWS BETWEEN [START] AND [FINISH]`

A frame always starts with ROWS BETWEEN or RANGE BETWEEN. A frame always has a start and an end, which can be defined using one of the 3 clauses: PRECEDING, CURRENT ROW, and FOLLOWING.

- `n PRECEDING`: Defines the frame as starting or ending n rows before the current row.
- `CURRENT ROW`: Is used to define the start or end at the current row.
- `n FOLLOWING`: Is used to define n rows after the current row.

**Examples:*

- `ROWS BETWEEN 3 PRECEDING AND CURRENT ROW`

   This frame starts 3 rows before the current row and ends at the current row, so the frame has 4 rows.

- `ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING`

   This frame starts one row before the current row and ends one row after the current row, so the frame has 3 rows.

- `ROWS BETWEEN 5 PRECEDING AND 1 PRECEDING`

   This frame starts five rows before the current row and ends one row before the current row, so the frame has 5 rows.

**Exercises:**

**Number of rows in a frame**

How many rows does the following frame span?

ROWS BETWEEN 3 PRECEDING AND 2 FOLLOWING

        Answer: 6

**Moving maximum of Scandinavian athletes' medals**

Frames allow you to restrict the rows passed as input to your window function to a sliding window for you to define the start and finish.

Adding a frame to your window function allows you to calculate "moving" metrics, inputs of which slide from row to row.

**Instruction:**

- Return the year, medals earned, and the maximum medals earned, comparing only the current year and the next year.

```sql
WITH Scandinavian_Medals AS (
SELECT
    Year, COUNT(*) AS Medals
FROM Summer_Medals
WHERE
    Country IN ('DEN', 'NOR', 'FIN', 'SWE', 'ISL')
    AND Medal = 'Gold'
GROUP BY Year)

SELECT
-- Select each year's medals
year,
medals,
-- Get the max of the current and next years'  medals
MAX(medals) OVER (ORDER BY year ASC
            ROWS BETWEEN CURRENT ROW
            AND 1 FOLLOWING) AS Max_Medals
FROM Scandinavian_Medals
ORDER BY Year ASC;
```

**Moving maximum of Chinese athletes' medals**

Frames allow you to "peek" forwards or backward without first using the relative fetching functions, LAG and LEAD, to fetch previous rows' values into the current row.

- Return the athletes, medals earned, and the maximum medals earned, comparing only the last two and current athletes, ordering by athletes' names in alphabetical order.

```sql
WITH Chinese_Medals AS (
SELECT
    Athlete, COUNT(*) AS Medals
FROM Summer_Medals
WHERE
    Country = 'CHN' AND Medal = 'Gold'
    AND Year >= 2000
GROUP BY Athlete)

SELECT
-- Select the athletes and the medals they've earned
Athlete,
medals,
-- Get the max of the last two and current rows' medals 
MAX(medals) OVER (ORDER BY athlete ASC
            ROWS BETWEEN 2 PRECEDING
            AND CURRENT ROW) AS Max_Medals
FROM Chinese_Medals
ORDER BY Athlete ASC;
```

# Moving averages and totals

**Personal notes:**

A moving average (MA) is the average of the last n periods of values from a column. Moving averages are used in various industries; for example, in sales, a 10-day moving average is the average of units sold in the last ten days per day. It is used to indicate momentum and trends; if units sold per day are greater than their moving average, more units will be sold the next day. They are also useful for smoothing out seasonality, which would be the normal fluctuation in units sold per day.

On the other hand, a moving total is the sum of the last n periods of values from a column. For example, the sum of the last 3 medals from the Olympic Games for any set of Olympic Games. It is used to indicate recent performance; if the sum is decreasing, overall performance is decreasing, and vice versa.

**Examples* - The source table for the examples is the count of gold medals awarded to the United States after 1980.

**Moving Average*

Let's take the 3-year moving average, which is the average of medal gains in the last two and the current sets of Olympic Games for each year. Setting the window to start two rows before the current row and end at the current row, it passes these three rows as input to the AVG function. The first moving average is equivalent to the medals from the first year since there is no other value to calculate the average. The moving average of the second row is the average of its value and the value of the first row; the moving average of the third row is the average of its value and the two previous rows, and so on.

```sql
WITH US_Medals AS (
    -- Your initial query to get medal data
)

SELECT
    Year, Medals,
    AVG(Medals) OVER (ORDER BY Year ASC 
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS Medals_MA
FROM US_Medals
ORDER BY Year ASC;
```

**Moving Total*

A moving total works the same way as the moving average, but instead of the AVG function, you use the SUM function with the same frame. Thus, the moving total of the first row is the value of the first row, the value of the second row the moving total is the sum of the first and second row, and then for each row, the moving total is the sum of its value and the values of the two previous rows.

```sql
WITH US_Medals AS (
    -- Your initial query to get medal data
)

SELECT
    Year, Medals,
    SUM(Medals) OVER (ORDER BY Year ASC 
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS Medals_MT
FROM US_Medals
ORDER BY Year ASC;
```

**ROWS vs. RANGE*

In the previous video, we saw that the frame subclause of LAST_VALUE starts with RANGE BETWEEN, while the frames you've seen so far start with ROWS BETWEEN. RANGE BETWEEN works the same way as ROWS BETWEEN, with one significant difference. RANGE treats duplicates in the columns in the ORDER BY subclause as unique entities, while ROWS does not. In practice, ROWS BETWEEN is almost always used instead of RANGE.

**Exercises:**

**Moving average's frame**

If you want your moving average to cover the last 3 and current Olympic games, how would you define its frame?

        "ROWS BETWEEN 3 PRECEDING AND CURRENT ROW"

**Moving average of Russian medals**

Using frames with aggregate window functions allow you to calculate many common metrics, including moving averages and totals. These metrics track the change in performance over time.

**Instruction:**

- Calculate the 3-year moving average of medals earned.

```sql
WITH Russian_Medals AS (
SELECT
    Year, COUNT(*) AS Medals
FROM Summer_Medals
WHERE
    Country = 'RUS'
    AND Medal = 'Gold'
    AND Year >= 1980
GROUP BY Year)

SELECT
Year, Medals,
--- Calculate the 3-year moving average of medals earned
AVG(medals) OVER
    (ORDER BY Year ASC
    ROWS BETWEEN
    2 PRECEDING AND CURRENT ROW) AS Medals_MA
FROM Russian_Medals
ORDER BY Year ASC;
```

**Moving total of countries' medals**

What if your data is split into multiple groups spread over one or more columns in the table? Even with a defined frame, if you can't somehow separate the groups' data, one group's values will affect the average of another group's values.

**Instruction:**

- Calculate the 3-year moving sum of medals earned per country.

```sql
WITH Country_Medals AS (
SELECT
    Year, Country, COUNT(*) AS Medals
FROM Summer_Medals
GROUP BY Year, Country)

SELECT
Year, Country, Medals,
-- Calculate each country's 3-game moving total
SUM(Medals) OVER
    (PARTITION BY country
    ORDER BY Year ASC
    ROWS BETWEEN
    2 PRECEDING AND CURRENT ROW) AS Medals_MA
FROM Country_Medals
ORDER BY Country ASC, Year ASC;
```
