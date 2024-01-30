# Short and Simple Subqueries

**Chapter description:**

In this chapter, you will learn about subqueries in the SELECT, FROM, and WHERE clauses. You will gain an understanding of when subqueries are necessary to construct your dataset and where to best include them in your queries.

# WHERE are the Subqueries?

**Personal notes:**

A subquery is a nested query within another query. You can identify a subquery by looking for an additional SELECT statement enclosed in parentheses, surrounded by another complete SQL statement. Subqueries are used to retrieve the desired information when you need to perform some intermediate transformations on your data before selecting, filtering, or calculating information.

A subquery can be placed anywhere in your query, such as the SELECT, FROM, WHERE, or GROUP BY clause. The placement depends on how you want your final data to look. A subquery can return various information, such as scalar quantities or numbers. It can return a list to be used for filtering or joining information, or it can return a table to further extract and transform data.

**Why use subqueries?*

Subqueries allow you to compare summarized values with detailed data. They also enable you to structure or reshape your data for various purposes. Finally, they allow you to combine data from tables where a regular join might not be possible.

**Simple subqueries*

A simple subquery is a query nested within another query that can be executed on its own. For example, here's a subquery in the WHERE clause - if you copy the entire inner query, you could execute it on its own and get a result:

```sql
SELECT home_goal
FROM match
WHERE home_goal > (
    SELECT AVG(home_goal)
    FROM match
);
```

A simple subquery is evaluated once in the entire query. This means that SQL processes the information inside the subquery first, obtains the required information, and then moves on to process information in the outer query.

**Subqueries in the WHERE clause*

They are useful for filtering results based on information that you would have to calculate separately beforehand. For example, let's generate a list of games in the 2012/2013 season where the number of home goals scored was above the overall average.

```sql
SELECT date, hometeam_id, awayteam_id, home_goal, away_goal
FROM match
WHERE season = '2012/2013'
      AND home_goal > (SELECT AVG(home_goal) FROM match);
```

This way, you have one less manual step to perform before getting the necessary results.

**Subquery filtering list with IN*

Subqueries are also useful for generating a filtering list. For example, to answer the question, "Which teams are part of the league in Poland?" The 'team' table does not have the country IDs, but the 'match' table has both country IDs and team IDs. In this case, you generate a list to compare with the 'team_api_id' column in the WHERE clause.

```sql
SELECT
    team_long_name,
    team_short_name AS abbr
FROM team
WHERE
    team_api_id IN 
    (SELECT hometeam_id
     FROM match
     WHERE country_id = 15722);
```

**Exercises:**

**Filtering using scalar subqueries**

Subqueries are incredibly powerful for performing complex filters and transformations. You can filter data based on single, scalar values using a subquery in ways you cannot by using WHERE statements or joins. Subqueries can also be used for more advanced manipulation of your data set. You will likely encounter subqueries in any real-world setting that uses relational databases.

In this exercise, you will generate a list of matches where the total goals scored (for both teams in total) is more than 3 times the average for games in the matches_2013_2014 table, which includes all games played in the 2013/2014 season.

**Instructions:**

- Calculate triple the average home + away goals scored across all matches. This will become your subquery in the next step. Note that this column does not have an alias, so it will be called ?column? in your results.

```sql
-- Select the average of home + away goals, multiplied by 3
SELECT 
    3 * AVG(home_goal + away_goal)
FROM matches_2013_2014;
```

- Select the date, home goals, and away goals in the main query.

- Filter the main query for matches where the total goals scored exceed the value in the subquery.

```sql
SELECT 
    -- Select the date, home goals, and away goals scored
    date,
    home_goal,
    away_goal
FROM  matches_2013_2014
-- Filter for matches where total goals exceeds 3x the average
WHERE (home_goal + away_goal) > 
    (SELECT 3 * AVG(home_goal + away_goal)
        FROM matches_2013_2014); 
```

**Filtering using a subquery with a list**

Your goal in this exercise is to generate a list of teams that never played a game in their home city. Using a subquery, you will generate a list of unique hometeam_ID values from the unfiltered match table to exclude in the team table's team_api_ID column.

In addition to filtering using a single-value (scalar) subquery, you can create a list of values in a subquery to filter data based on a complex set of conditions. This type of subquery generates a one column reference list for the main query. As long as the values in your list match a column in your main query's table, you don't need to use a join -- even if the list is from a separate table.

**Instructions:**

- Create a subquery in the WHERE clause that retrieves all unique hometeam_ID values from the match table.

- Select the team_long_name and team_short_name from the team table. Exclude all values from the subquery in the main query.

```sql
SELECT 
    -- Select the team long and short names
    team_long_name,
    team_short_name
FROM team
-- Exclude all values from the subquery
WHERE team_api_id NOT IN
    (SELECT DISTINCT hometeam_ID  FROM match);
```

**Filtering with more complex subquery conditions**

In the previous exercise, you generated a list of teams that have no home matches listed in the soccer database using a subquery in WHERE. Let's do some further exploration in this database by creating a list of teams that scored 8 or more goals in a home match.

In order to do this, you will construct a subquery in the WHERE statement with its own filtering condition.

**Instructions:**

- Create a subquery in WHERE clause that retrieves all hometeam_ID values from match with a home_goal score greater than or equal to 8.

- Select the team_long_name and team_short_name from the team table. Include all values from the subquery in the main query.

```sql
SELECT
    -- Select the team long and short names
    team_long_name,
    team_short_name
FROM team
-- Filter for teams with 8 or more home goals
WHERE team_api_id IN
    (SELECT hometeam_ID 
    FROM match
    WHERE home_goal >= 8);
```

# Subqueries in FROM

**Personal notes:**

Subqueries in the FROM statement are a powerful tool for restructuring and transforming your data. Often, the data you need to answer a question is not yet in the required format for direct querying, and it requires some additional processing to prepare it for analysis. For example, you might want to reshape your data or pre-filter it before performing calculations. They are also useful when calculating aggregates of aggregated information.

**FROM subqueries*

Let's examine the average home goals for each team in the database. First, we create the query that will become your subquery, selecting the 'team_long_name' from the 'team' table and the AVG of the 'home_goal' column from the 'match' table. The 'team' table is joined with the 'match' table using a LEFT JOIN on 'hometeam_id,' which will give you the home team's ID. The query is then filtered by season and grouped by team.

```sql
SELECT
    t.team_long_name AS team,
    AVG(m.home_goal) AS home_avg
FROM match AS m
LEFT JOIN team AS t
ON m.hometeam_id = t.team_api_id
WHERE season = '2011/2012'
GROUP BY team;
```

To get only the top team as the final result, you simply place this query without the semicolon inside the FROM statement of an outer query, remembering to name an alias. Then add it to the main query, selecting the 'team' and 'home_avg' columns from the subquery. Finally, order by 'home_avg' in descending order and limit the query to 3 results.

```sql
SELECT team, home_avg
FROM (SELECT
        t.team_long_name AS team,
        AVG(m.home_goal) AS home_avg
      FROM match AS m
      LEFT JOIN team AS t
      ON m.hometeam_id = t.team_api_id
      WHERE season = '2011/2012'
      GROUP BY team) AS subquery
ORDER BY home_avg DESC
LIMIT 3;
```

**Important to remember*

There are a few important things to remember when using subqueries in the FROM statement. First, you have the ability to create more than one subquery in the FROM statement of any main query. When doing so, make sure to give each subquery an alias and ensure that you can join them to each other, just as you would when querying a table in your database. Second, you can join a subquery to any existing table in your database. However, you need to make sure you have a column in the subquery that you can use with the JOIN you want to perform.

**Exercises:**

**Joining Subqueries in FROM**

The match table in the European Soccer Database does not contain country or team names. You can get this information by joining it to the country table, and use this to aggregate information, such as the number of matches played in each country.

If you're interested in filtering data from one of these tables, you can also create a subquery from one of the tables, and then join it to an existing table in the database. A subquery in FROM is an effective way of answering detailed questions that requires filtering or transforming data before including it in your final results.

Your goal in this exercise is to generate a subquery using the match table, and then join that subquery to the country table to calculate information about matches with 10 or more goals in total!

**Instructions:**

- Create the subquery to be used in the next step, which selects the country ID and match ID (id) from the match table.

- Filter the query for matches with greater than or equal to 10 goals.

```sql
SELECT 
    -- Select the country ID and match ID
    country_id, 
    id 
FROM match
-- Filter for matches with 10 or more goals in total
WHERE (home_goal + away_goal) >= 10;
```

- Construct a subquery that selects only matches with 10 or more total goals.

- Inner join the subquery onto country in the main query.

- Select name from country and count the id column from match.

```sql
SELECT
    -- Select country name and the count match IDs
    name AS country_name,
    COUNT(sub.id) AS matches
FROM country AS c
-- Inner join the subquery onto country
-- Select the country id and match id columns
INNER JOIN (SELECT country_id, id 
        FROM match
        -- Filter the subquery by matches with 10+ goals
        WHERE (home_goal + away_goal) >= 10) AS sub
ON c.id = sub.country_id
GROUP BY country_name;
```

**Building on Subqueries in FROM**

In the previous exercise, you found that England, Netherlands, Germany and Spain were the only countries that had matches in the database where 10 or more goals were scored overall. Let's find out some more details about those matches -- when they were played, during which seasons, and how many of the goals were home versus away goals.

You'll notice that in this exercise, the table alias is excluded for every column selected in the main query. This is because the main query is extracting data from the subquery, which is treated as a single table.

**Instructions:**

- Complete the subquery inside the FROM clause. Select the country name from the country table, along with the date, the home goal, the away goal, and the total goals columns from the match table.

- Create a column in the subquery that adds home and away goals, called total_goals. This will be used to filter the main query.

- Select the country, date, home goals, and away goals in the main query.

- Filter the main query for games with 10 or more total goals.

```sql
SELECT
    -- Select country, date, home, and away goals from the subquery
    country,
    date,
    home_goal,
    away_goal
FROM 
    -- Select country name, date, home_goal, away_goal, and total goals in the subquery
    (SELECT name AS country, 
            m.date, 
            m.home_goal, 
            m.away_goal,
        (m.home_goal + m.away_goal) AS total_goals
    FROM match AS m
    LEFT JOIN country AS c
    ON m.country_id = c.id) AS subq
-- Filter by total goals scored in the main query
WHERE total_goals >= 10;
```

# Subqueries in SELECT

**Personal notes:**

Subqueries in the SELECT statement are used to return a single aggregated value. This can be helpful when you cannot include an aggregated value in an ungrouped SQL query. They are also useful for performing complex mathematical calculations on information in your dataset.

**Example:*

Let's say we want to create a column to compare the total number of matches played in each season to the overall total number of matches played. We can first calculate the overall match count, which is 12,837.

```sql
SELECT COUNT(id) FROM match;
```

We can then add this single number to the SELECT statement, producing the following results. Alternatively, we can skip this step and add the subquery directly to the SELECT statement to get identical results.

```sql
SELECT
    season,
    COUNT(id) AS matches,
    (SELECT COUNT(id) FROM match) AS total_matches
FROM match
GROUP BY season;
```

**SELECT subqueries for mathematical calculations*

The single value returned by a subquery in SELECT can be used to calculate information based on existing information in a database. For example, the overall average number of goals scored in a match in all seasons is 2. If you want to calculate the difference from the average in any match, you can calculate this number in advance in a separate query and insert the value into the SELECT statement. Alternatively, you can use a subquery that calculates this value in a subquery in the SELECT statement and subtract it from the total goals in that match. Overall, this second option can save a lot of time and errors in your work, and the results are identical to manually performed calculations.

```sql
SELECT 
    date,
    (home_goal + away_goal) AS goals,
    (home_goal + away_goal) -
        (SELECT AVG(home_goal + away_goal)
        FROM match
        WHERE season = '2011/2012') AS diff
FROM match
WHERE season = '2011/2012';
```

There are some unique considerations when working with subqueries in SELECT. The first is that the subquery needs to return a single value. If the result of your subquery returns multiple rows, your entire query will generate an error. This is because the information retrieved in a SELECT query is applied identically to each row in the dataset, and that's not possible if there is more than one unit of information. The second thing to keep an eye on is the correct placement of your data filters in the main query and the subquery. Since the subquery is processed before the main query, you'll need to include relevant filters in the subquery, just like in the main query.

**Exercises:**

**Add a subquery to the SELECT clause**

Subqueries in SELECT statements generate a single value that allow you to pass an aggregate value down a data frame. This is useful for performing calculations on data within your database.

In the following exercise, you will construct a query that calculates the average number of goals per match in each country's league.

- In the subquery, select the average total goals by adding home_goal and away_goal.

- Filter the results so that only the average of goals in the 2013/2014 season is calculated.

- In the main query, select the average total goals by adding home_goal and away_goal. This calculates the average goals for each league.

- Filter the results in the main query the same way you filtered the subquery. Group the query by the league name.

```sql
SELECT 
    l.name AS league,
    -- Select and round the league's total goals
    ROUND(AVG(m.home_goal + m.away_goal), 2) AS avg_goals,
    -- Select & round the average total goals for the season
    (SELECT ROUND(AVG(home_goal + away_goal), 2) 
    FROM match
    WHERE season = '2013/2014') AS overall_avg
FROM league AS l
LEFT JOIN match AS m
ON l.country_id = m.country_id
-- Filter for the 2013/2014 season
WHERE season = '2013/2014'
GROUP BY l.name;
```

**Subqueries in Select for Calculations**

Subqueries in SELECT are a useful way to create calculated columns in a query. A subquery in SELECT can be treated as a single numeric value to use in your calculations. When writing queries in SELECT, it's important to remember that filtering the main query does not filter the subquery -- and vice versa.

In the previous exercise, you created a column to compare each league's average total goals to the overall average goals in the 2013/2014 season. In this exercise, you will add a column that directly compares these values by subtracting the overall average from the subquery.

**Instructions:**

- Select the average goals scored in a match for each league in the main query.

- Select the average goals scored in a match overall for the 2013/2014 season in the subquery.

- Subtract the subquery from the average number of goals calculated for each league.

- Filter the main query so that only games from the 2013/2014 season are included.

```sql
SELECT
    -- Select the league name and average goals scored
    name AS league,
    ROUND(AVG(m.home_goal + m.away_goal),2) AS avg_goals,
    -- Subtract the overall average from the league average
    ROUND(AVG(m.home_goal + m.away_goal) - 
        (SELECT AVG(home_goal + away_goal)
        FROM match 
        WHERE season = '2013/2014'),2) AS diff
FROM league AS l
LEFT JOIN match AS m
ON l.country_id = m.country_id
-- Only include 2013/2014 results
WHERE season = '2013/2014'
GROUP BY l.name;
```

# Subqueries Everywhere! And Best Practices!

**Personal notes:**

In SQL, you can include as many simple subqueries as necessary in various clauses in your query. However, your queries can quickly become long and hard to read.

**Format your queries*

A best practice you can start with in your SQL journey is to format your queries correctly. It's essential to properly align your SELECT, FROM, GROUP BY, WHERE, and all the information contained in them. It's also considered a best practice to annotate your queries with comments to tell the user what it does—inside a slash, asterisk, and ending with an asterisk and a slash (/* comment */). You can also use inline comments using two dashes (-- comment). Each information after an inline comment is treated as text, even if it's a recognized SQL command.

**Indent your queries*

Make sure you correctly indent all the information contained in a subquery. This way, you can easily return to the query and understand what information is being processed first, where you need to apply changes, such as a date range, and what you can expect from the results if you make those changes. Ensure you clearly indent all information that is part of a single column, such as a long CASE statement or a complicated subquery in SELECT.

Overall, you can read the Holywell SQL Style Guide to get a sense of all the formatting conventions when working with SQL queries.

**Is that subquery necessary?*

When deciding whether or not you need a subquery, it's important to know that each added subquery requires additional computational power to generate its results. Depending on the size of your database and the number of records you extract in your query, you might significantly increase the time needed to execute it.

Finally, when building a main query with multiple subqueries, ensure that your filters are placed correctly in each subquery and in the main query to generate accurate results.

**Exercises:**

**ALL the subqueries EVERYWHERE**

In soccer leagues, games are played at different stages. Winning teams progress from one stage to the next, until they reach the final stage. In each stage, the stakes become higher than the previous one. The match table includes data about the different stages that each match took place in.

In this lesson, you will build a final query across 3 exercises that will contain three subqueries -- one in the SELECT clause, one in the FROM clause, and one in the WHERE clause. In the final exercise, your query will extract data examining the average goals scored in each stage of a match. Does the average number of goals scored change as the stakes get higher from one stage to the next?

**Instructions:**

- Extract the average number of home and away team goals in two SELECT subqueries.

- Calculate the average home and away goals for the specific stage in the main query.

- Filter both subqueries and the main query so that only data from the 2012/2013 season is included.

- Group the query by the m.stage column.

```sql
SELECT 
    -- Select the stage and average goals for each stage
    m.stage,
    ROUND(AVG(m.home_goal + m.away_goal),2) AS avg_goals,
    -- Select the average overall goals for the 2012/2013 season
    ROUND((SELECT AVG(home_goal + away_goal) 
        FROM match 
        WHERE season = '2012/2013'),2) AS overall
FROM match AS m
-- Filter for the 2012/2013 season
WHERE season = '2012/2013'
-- Group by stage
GROUP BY m.stage;
```

**Add a subquery in FROM**

In the previous exercise, you created a data set listing the average home and away goals in each match stage of the 2012/2013 match season.

In this next step, you will turn the main query into a subquery to extract a list of stages where the average home goals in a stage is higher than the overall average for home goals in a match.

**Instructions:**

- Calculate the average home goals and average away goals from the match table for each stage in the FROM clause subquery.

- Add a subquery to the WHERE clause that calculates the overall average home goals.

- Filter the main query for stages where the average home goals is higher than the overall average.

- Select the stage and avg_goals columns from the s subquery into the main query.

```sql
SELECT 
    -- Select the stage and average goals from the subquery
    stage,
    ROUND(avg_goals,2) AS avg_goals
FROM 
    -- Select the stage and average goals in 2012/2013
    (SELECT
        stage,
        AVG(home_goal + away_goal) AS avg_goals
    FROM match
    WHERE season = '2012/2013'
    GROUP BY stage) AS s
WHERE 
    -- Filter the main query using the subquery
    s.avg_goals > (SELECT AVG(home_goal + away_goal) 
                    FROM match WHERE season = '2012/2013');
```

**Add a subquery in SELECT**

In the previous exercise, you added a subquery to the FROM statement and selected the stages where the number of average goals in a stage exceeded the overall average number of goals in the 2012/2013 match season. In this final step, you will add a subquery in SELECT to compare the average number of goals scored in each stage to the total.

**Instructions:**

- Create a subquery in SELECT that yields the average goals scored in the 2012/2013 season. Name the new column overall_avg.

- Create a subquery in FROM that calculates the average goals scored in each stage during the 2012/2013 season.

- Filter the main query for stages where the average goals exceeds the overall average in 2012/2013.

```sql
SELECT 
    -- Select the stage and average goals from s
    stage,
    ROUND(s.avg_goals,2) AS avg_goal,
    -- Select the overall average for 2012/2013
    (SELECT AVG(home_goal + away_goal) FROM match WHERE season = '2012/2013') AS overall_avg
FROM 
    -- Select the stage and average goals in 2012/2013 from match
    (SELECT
        stage,
        AVG(home_goal + away_goal) AS avg_goals
    FROM match
    WHERE season = '2012/2013'
    GROUP BY stage) AS s
WHERE 
    -- Filter the main query using the subquery
    s.avg_goals > (SELECT AVG(home_goal + away_goal) 
                    FROM match WHERE season = '2012/2013');
```