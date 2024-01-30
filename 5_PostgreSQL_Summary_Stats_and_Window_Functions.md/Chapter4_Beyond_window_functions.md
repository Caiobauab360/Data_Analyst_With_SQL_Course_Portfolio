# Beyond window functions

**Chapter description:**

In this last chapter, you'll learn some techniques and functions that are useful when used together with window functions.

# Pivoting

**Personal notes:**

Pivoting transforms a table by creating columns with unique values from one of its columns. In general, pivot tables are easier to analyze, especially when pivoted on a column ordered chronologically.

**CROSSTAB*

CROSSTAB allows you to pivot a table on a specific column. You need to use the CREATE EXTENSION statement before using CROSSTAB. CREATE EXTENSION makes extra functions in an extension available for use. The "tablefunc" extension contains the CROSSTAB function.

```sql
CREATE EXTENSION IF NOT EXISTS tablefunc;
```

After that, put your source query between two pairs of dollar signs. Then, in the parentheses after ct, write the column names and types for your new pivot table. The column names are the non-pivoted column and the unique values from the pivoted column.

```sql
CREATE EXTENSION IF NOT EXISTS tablefunc;

SELECT * FROM CROSSTAB($$
    source_sql TEXT
$$) AS ct (column_1 DATA_TYPE_1,
          column_2 DATA_TYPE_2,
          ...,
          column_n DATA_TYPE_N);
```

For example, in the following query, the CTE counts the gold medals awarded to three countries in the 2004, 2008, and 2012 Olympics. Then, the ranking of each country is calculated based on who won the most gold medals in each of the Olympics. Convert RANK to integer to determine the column type for the pivoted columns.

```sql
WITH Country_Awards AS (
    SELECT 
        Country, Year, COUNT(*) AS Awards
    FROM Summer_Medals
    WHERE
        Country IN ('CHN', 'RUS', 'USA')
        AND Year IN (2004, 2008, 2012)
        AND Medal = 'Gold' AND Sport = 'Gymnastics'
    GROUP BY Country, Year
    ORDER BY Country ASC, Year ASC
)

SELECT
    Country, Year,
    RANK() OVER (PARTITION BY Year ORDER BY Awards DESC) :: INTEGER AS rank
FROM Country_Awards
ORDER BY Country ASC, Year ASC;
```

Looking at the result, wouldn't it be easier to read if the column was pivoted by year? To change that, replace the ellipses with the source query. Then, in the parentheses after ct, list the column names and types for the pivot table. Since you're pivoting on the year, and the values are in awards, the columns are 'country' (the non-pivoted column) and the unique values in the year (2004, 2008, 2012). Make sure to list the unique values in the correct order.

```sql
CREATE EXTENSION IF NOT EXISTS tablefunc;

SELECT * FROM CROSSTAB($$
    ...
$$) AS ct (Country VARCHAR,
           "2004" INTEGER,
           "2008" INTEGER,
           "2012" INTEGER)
ORDER BY Country ASC;
```

The result shows the rankings pivoted by country and year, making it easier to read. Pivoting is useful when preparing data for visualization and reports.

**Exercises:**

**A basic pivot**

You have the following table of Pole Vault gold medalist countries by gender in 2008 and 2012.

    | Gender | Year | Country |
    |--------|------|---------|
    | Men    | 2008 | AUS     |
    | Men    | 2012 | FRA     |
    | Women  | 2008 | RUS     |
    | Women  | 2012 | USA     |

Pivot it by Year to get the following reshaped, cleaner table.

    | Gender | 2008 | 2012 |
    |--------|------|------|
    | Men    | AUS  | FRA  |
    | Women  | RUS  | USA  |

**Instructions:**

- Create the correct extension.

- Fill in the column names of the pivoted table.

```sql
-- Create the correct extension to enable CROSSTAB
CREATE EXTENSION IF NOT EXISTS tablefunc;

SELECT * FROM CROSSTAB($$
SELECT
    Gender, Year, Country
FROM Summer_Medals
WHERE
    Year IN (2008, 2012)
    AND Medal = 'Gold'
    AND Event = 'Pole Vault'
ORDER By Gender ASC, Year ASC;
-- Fill in the correct column names for the pivoted table
$$) AS ct (Gender VARCHAR,
        "2008" VARCHAR,
        "2012" VARCHAR)

ORDER BY Gender ASC;
```

**Pivoting with ranking**

You want to produce an easy scannable table of the rankings of the three most populous EU countries by how many gold medals they've earned in the 2004 through 2012 Olympic games. The table needs to be in this format:

    | Country | 2004 | 2008 | 2012 |
    |---------|------|------|------|
    | FRA     | ...  | ...  | ...  |
    | GBR     | ...  | ...  | ...  |
    | GER     | ...  | ...  | ...  |

You'll need to count the gold medals each country has earned, produce the ranks of each country by medals earned, then pivot the table to this shape.

**Instructions:**

- Count the gold medals that France (FRA), the UK (GBR), and Germany (GER) have earned per country and year.

```sql
-- Count the gold medals per country and year
SELECT
Country,
Year,
COUNT(*) AS Awards
FROM Summer_Medals
WHERE
Country IN ('FRA', 'GBR', 'GER')
AND Year IN (2004, 2008, 2012)
AND Medal = 'Gold'
GROUP BY country, year
ORDER BY Country ASC, Year ASC
```

- Select the country and year columns, then rank the three countries by how many gold medals they earned per year.

```sql
WITH Country_Awards AS (
SELECT
    Country,
    Year,
    COUNT(*) AS Awards
FROM Summer_Medals
WHERE
    Country IN ('FRA', 'GBR', 'GER')
    AND Year IN (2004, 2008, 2012)
    AND Medal = 'Gold'
GROUP BY Country, Year)

SELECT
-- Select Country and Year
Country,
Year,
-- Rank by gold medals earned per year
RANK() OVER (PARTITION BY Year ORDER BY Awards DESC) :: INTEGER AS rank
FROM Country_Awards
ORDER BY Country ASC, Year ASC;
```

- Pivot the query's results by Year by filling in the new table's correct column names.

```sql
CREATE EXTENSION IF NOT EXISTS tablefunc;

SELECT * FROM CROSSTAB($$
WITH Country_Awards AS (
    SELECT
    Country,
    Year,
    COUNT(*) AS Awards
    FROM Summer_Medals
    WHERE
    Country IN ('FRA', 'GBR', 'GER')
    AND Year IN (2004, 2008, 2012)
    AND Medal = 'Gold'
    GROUP BY Country, Year)

SELECT
    Country,
    Year,
    RANK() OVER
    (PARTITION BY Year
    ORDER BY Awards DESC) :: INTEGER AS rank
FROM Country_Awards
ORDER BY Country ASC, Year ASC;
-- Fill in the correct column names for the pivoted table
$$) AS ct (Country VARCHAR,
        "2004" INTEGER,
        "2008" INTEGER,
        "2012" INTEGER)

Order by Country ASC;
```

# ROLLUP and CUBE

**Personal notes:**

ROLLUP is a GROUP BY subclause that includes extra rows for group-level aggregations. In this query, grouping by country and rolling up medal will count all countries and totals at the medal level, count only country-level totals, and fill 'medal' with nulls for those rows.

```sql
SELECT
    Country, Medal, COUNT(*) AS Awards
FROM Summer_Medals
WHERE
    Year = 2008 AND Country IN ('CHN', 'RUS')
GROUP BY Country, ROLLUP(Medal) 
ORDER BY Country ASC, Medal ASC;
```

You can also use ROLLUP to generate grand totals. If you use ROLLUP on all GROUP BY columns, you will get an additional row with the grand total. ROLLUP is hierarchical; the order of columns in the ROLLUP clause affects the output. If you use ROLLUP on country and medal, you'll get country-level totals, but if you reverse the columns, you'll get medal-level totals. Both will include the grand total, though.

```sql
SELECT
    Country, Medal, COUNT(*) AS Awards
FROM Summer_Medals
WHERE
    Year = 2008 AND Country IN ('CHN', 'RUS')
GROUP BY Country, ROLLUP(Country, Medal) 
ORDER BY Country ASC, Medal ASC;
```

Rows where country is filled but medal is null represent country-level totals; for example, 184 total medals were awarded to China in 2008. The row with nulls in both columns is the grand total. Note that there are no medal-level totals since you are using ROLLUP by country first and then by medal, not vice versa. And what if you also want the total medals?

Enter CUBE.

CUBE is much like ROLLUP, except it's not hierarchical. It generates all possible group-level aggregations. Putting country and medal in the CUBE subclause will count at the country level, medal level, and grand totals.

```sql
SELECT
    Country, Medal, COUNT(*) AS Awards
FROM Summer_Medals
WHERE
    Year = 2008 AND Country IN ('CHN', 'RUS')
GROUP BY CUBE(Country, Medal) 
ORDER BY Country ASC, Medal ASC;
```

Note in the query result that medal-level totals are included, as well as country-level totals. Medal-level totals are those where country is null but medal is not.

**ROLL UP vs CUBE*

Use ROLLUP when you have hierarchical data in your columns, such as date parts, because in such cases, only a few group-level aggregations make sense. Use CUBE when you want all possible group-level aggregations. With ROLLUP and CUBE, you can succinctly generate many group-level aggregations.

**Exercises:**

**Country-level subtotals**

You want to look at three Scandinavian countries' earned gold medals per country and gender in the year 2004. You're also interested in Country-level subtotals to get the total medals earned for each country, but Gender-level subtotals don't make much sense in this case, so disregard them.

**Instructions:**

- Count the gold medals awarded per country and gender.

- Generate Country-level gold award counts.

```sql
-- Count the gold medals per country and gender
SELECT
Country,
Gender,
COUNT(*) AS Gold_Awards
FROM Summer_Medals
WHERE
Year = 2004
AND Medal = 'Gold'
AND Country IN ('DEN', 'NOR', 'SWE')
-- Generate Country-level subtotals
GROUP BY Country, ROLLUP(Gender)
ORDER BY Country ASC, Gender ASC;
```

**All group-level subtotals**

You want to break down all medals awarded to Russia in the 2012 Olympic games per gender and medal type. Since the medals all belong to one country, Russia, it makes sense to generate all possible subtotals (Gender- and Medal-level subtotals), as well as a grand total.

Generate a breakdown of the medals awarded to Russia per country and medal type, including all group-level subtotals and a grand total.

**Instructions:**

- Count the medals awarded per gender and medal type.

- Generate all possible group-level counts (per gender and medal type subtotals and the grand total).

```sql
-- Count the medals per gender and medal type
SELECT
gender,
medal,
COUNT(*) AS Awards
FROM Summer_Medals
WHERE
Year = 2012
AND Country = 'RUS'
-- Get all possible group-level subtotals
GROUP BY CUBE(gender, medal)
ORDER BY Gender ASC, Medal ASC;
```

# A survey of useful functions

**Personal notes:**

**Enter COALESCE*

COALESCE takes a list of values and returns the first non-null value, going from left to right. COALESCE is useful when using SQL operations that return nulls, such as ROLLUP and CUBE. Other operations that return nulls are pivot operations (when some of your rows have no corresponding values for the new columns) and positional operations like LAG, which always returns a null for the first row because it has no previous row.

The only change to the example query used in the previous lesson that needs to be made is to wrap the two columns with COALESCE and pass the string you want the column to contain if its value is null. The country column is null when it's the grand total, so the string should be both countries, while the medal column is null when it's the count of all medals, so it should be all medals.

```sql
SELECT
    COALESCE(Country,  'Both Countries') AS Country,
    COALESCE(Medal, 'All medals') AS Medal,
    COUNT(*) AS Awards
FROM Summer_Medals
WHERE
    Year = 2008 AND Country IN ('CHN', 'RUS')
GROUP BY Country, ROLLUP(Country, Medal) 
ORDER BY Country ASC, Medal ASC;
```

Make sure to name the output columns as the columns you passed as your first argument for consistent results. As seen in the query result, the nulls have been replaced with what was passed to each COALESCE.

**Enter STRING_AGG*

STRING_AGG takes all the values in a column and concatenates them, with a separator between each value. It is useful when you need to reduce the number of returned rows.

```sql
STRING_AGG(Letter, ', ')
```

To use STRING_AGG, simply wrap the final query from the previous lesson in a CTE called Country_Ranks. Then, use STRING_AGG on the country column and pass a comma separator with a space after it.

```sql
WITH Country_Medals AS (...)

Country_Ranks AS (...)

SELECT STRING_AGG(Country, ', ')
FROM Country_Medals;
```

Using COALESCE and STRING_AGG, you can clean up and condense the results of your queries.

**Exercises:**

**Cleaning up results**

Returning to the breakdown of Scandinavian awards you previously made, you want to clean up the results by replacing the nulls with meaningful text.

**Instruction:**

- Turn the nulls in the Country column to All countries, and the nulls in the Gender column to All genders.

```sql
SELECT
-- Replace the nulls in the columns with meaningful text
COALESCE(Country, 'All countries') AS Country,
COALESCE(Gender, 'All genders') AS Gender,
COUNT(*) AS Awards
FROM Summer_Medals
WHERE
Year = 2004
AND Medal = 'Gold'
AND Country IN ('DEN', 'NOR', 'SWE')
GROUP BY ROLLUP(Country, Gender)
ORDER BY Country ASC, Gender ASC;
```

**Summarizing results**

After ranking each country in the 2000 Olympics by gold medals awarded, you want to return the top 3 countries in one row, as a comma-separated string. In other words, turn this:

    | Country | Rank |
    |---------|------|
    | USA     | 1    |
    | RUS     | 2    |
    | AUS     | 3    |
    | ...     | ...  |

into this:

USA, RUS, AUS

**Instructions:**

- Rank countries by the medals they've been awarded.

```sql
WITH Country_Medals AS (
SELECT
    Country,
    COUNT(*) AS Medals
FROM Summer_Medals
WHERE Year = 2000
    AND Medal = 'Gold'
GROUP BY Country)

SELECT
    Country,
    -- Rank countries by the medals awarded
RANK() OVER (ORDER BY medals DESC)  AS Rank
FROM Country_Medals
ORDER BY Rank ASC;
```

- Return the top 3 countries by medals awarded as one comma-separated string.

```sql
WITH Country_Medals AS (
SELECT
    Country,
    COUNT(*) AS Medals
FROM Summer_Medals
WHERE Year = 2000
    AND Medal = 'Gold'
GROUP BY Country),

Country_Ranks AS (
SELECT
    Country,
    RANK() OVER (ORDER BY Medals DESC) AS Rank
FROM Country_Medals
ORDER BY Rank ASC)

-- Compress the countries column
SELECT STRING_AGG(Country, ', ')
FROM Country_Ranks
-- Select only the top three ranks
WHERE Rank <= 3;
```