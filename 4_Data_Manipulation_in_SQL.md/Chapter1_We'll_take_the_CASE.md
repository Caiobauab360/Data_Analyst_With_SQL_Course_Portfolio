# Data Manipulation in SQL

**Course Description**

So you've learned how to aggregate and join data from tables in your database—now what? How do you manipulate, transform, and make the most sense of your data? This intermediate-level course will teach you several key functions necessary to wrangle, filter, and categorize information in a relational database, expand your SQL toolkit, and answer complex questions. You will learn the robust use of CASE statements, subqueries, and window functions—all while discovering some interesting facts about soccer using the European Soccer Database.

# We'll take the CASE

**Chapter Description:**

In this chapter, you will learn how to use the CASE WHEN statement to create categorical variables, aggregate data into a single column with multiple filtering conditions, and calculate counts and percentages.

# We'll take the CASE

**Personal notes:**

In this course, we'll be using the European Soccer Database—a relational database containing data on over 25,000 matches, 300 teams, and 10,000 players from Europe between 2008 and 2016. The data is contained in four tables—country, league, team, and match.

**CASE Statements*

CASE statements are SQL's version of an "IF this THEN that" statement. CASE statements have three parts—a WHEN clause, a THEN clause, and an ELSE clause.

- **WHEN:* Tests a specific condition. If this condition is true, it will return the item specified after the THEN clause. You can create multiple conditions by listing the WHEN and THEN statements in the same CASE statement.
- **THEN:* Specifies the value to be returned if the corresponding WHEN condition is true.
- **ELSE:* Returns a specified value if none of the WHEN conditions are true.

After you've completed the statement, you need to include the term `END` and give it an alias.

```sql
CASE 
  WHEN x = 1 THEN 'a'
  WHEN x = 2 THEN 'b'
  ELSE 'c' 
END AS new_column
```

**Example:*

Using a CASE statement to create a new variable that identifies matches as home team wins, away team wins, or ties. A new column is created with appropriate text for each match based on the result.

```sql
SELECT
  id,
  home_goal,
  away_goal,
  CASE 
    WHEN home_goal > away_goal THEN 'Home Team Win'
    WHEN home_goal < away_goal THEN 'Away Team Win'
    ELSE 'Tie' 
  END AS outcome
FROM match
WHERE season = '2013/2014';
```

**Exercises:**

**Basic CASE statements**

What is your favorite team?

The European Soccer Database contains data about 12,800 matches from 11 countries played between 2011-2015! Throughout this course, you will be shown filtered versions of the tables in this database in order to better explore their contents.

In this exercise, you will identify matches played between FC Schalke 04 and FC Bayern Munich. There are 2 teams identified in each match in the hometeam_id and awayteam_id columns, available to you in the filtered matches_germany table. ID can join to the team_api_id column in the teams_germany table, but you cannot perform a join on both at the same time.

However, you can perform this operation using a CASE statement once you've identified the team_api_id associated with each team!

**Instructions:**

- Select the team's long name and API id from the teams_germany table.

- Filter the query for FC Schalke 04 and FC Bayern Munich using IN, giving you the team_api_IDs needed for the next step.

```sql
SELECT
    -- Select the team long name and team API id
    team_long_name,
    team_api_id
FROM teams_germany
-- Only include FC Schalke 04 and FC Bayern Munich
WHERE team_long_name IN ('FC Schalke 04', 'FC Bayern Munich');
```

- Create a CASE statement that identifies whether a match in Germany included FC Bayern Munich, FC Schalke 04, or neither as the home team.

- Group the query by the CASE statement alias, home_team.

```sql
-- Identify the home team as Bayern Munich, Schalke 04, or neither
SELECT 
    CASE WHEN hometeam_id = 10189 THEN 'FC Schalke 04'
        WHEN hometeam_id = 9823 THEN 'FC Bayern Munich'
        ELSE 'Other' END AS home_team,
    COUNT(id) AS total_matches
FROM matches_germany
-- Group by the CASE statement alias
GROUP BY home_team;
```

**CASE statements comparing column values**

Barcelona is considered one of the strongest teams in Spain's soccer league.

In this exercise, you will be creating a list of matches in the 2011/2012 season where Barcelona was the home team. You will do this using a CASE statement that compares the values of two columns to create a new group -- wins, losses, and ties.

In 3 steps, you will build a query that identifies a match's winner, identifies the identity of the opponent, and finally filters for Barcelona as the home team. Completing a query in this order will allow you to watch your results take shape with each new piece of information.

The matches_spain table currently contains Barcelona's matches from the 2011/2012 season, and has two key columns, hometeam_id and awayteam_id, that can be joined with the teams_spain table. However, you can only join teams_spain to one column at a time.

**Instructions:**

- Select the date of the match and create a CASE statement to identify matches as home wins, home losses, or ties.

```sql
SELECT 
    -- Select the date of the match
    date,
    -- Identify home wins, losses, or ties
    CASE WHEN home_goal > away_goal THEN 'Home win!'
        WHEN home_goal < away_goal THEN 'Home loss :(' 
        ELSE 'Tie' END AS outcome
FROM matches_spain;
```

- Left join the teams_spain table team_api_id column to the matches_spain table awayteam_id. This allows us to retrieve the away team's identity.

- Select team_long_name from teams_spain as opponent and complete the CASE statement from Step 1.

```sql
SELECT 
    m.date,
    --Select the team long name column and call it 'opponent'
    t.team_long_name AS opponent, 
    -- Complete the CASE statement with an alias
    CASE WHEN m.home_goal > away_goal THEN 'Home win!'
        WHEN m.home_goal < away_goal THEN 'Home loss :('
        ELSE 'Tie' END AS outcome
FROM matches_spain AS m
-- Left join teams_spain onto matches_spain
LEFT JOIN teams_spain AS t
ON m.awayteam_id = t.team_api_id;
```

- Complete the same CASE statement as the previous steps.

- Filter for matches where the home team is FC Barcelona (id = 8634).

```sql
SELECT 
    m.date,
    t.team_long_name AS opponent,
    -- Complete the CASE statement with an alias
    CASE WHEN m.home_goal > m.away_goal  THEN 'Barcelona win!'
        WHEN m.home_goal < m.away_goal THEN 'Barcelona loss :(' 
        ELSE 'Tie' END AS outcome 
FROM matches_spain AS m
LEFT JOIN teams_spain AS t 
ON m.awayteam_id = t.team_api_id
-- Filter for Barcelona as the home team
WHERE m.hometeam_id = 8634; 
```

**CASE statements comparing two column values part 2**

Similar to the previous exercise, you will construct a query to determine the outcome of Barcelona's matches where they played as the away team. You will learn how to combine these two queries in chapters 2 and 3.

Did their performance differ from the matches where they were the home team?

**Instructions:**

- Complete the CASE statement to identify Barcelona's away team games (id = 8634) as wins, losses, or ties.

- Left join the teams_spain table team_api_id column on the matches_spain table hometeam_id column. This retrieves the identity of the home team opponent.

- Filter the query to only include matches where Barcelona was the away team.

```sql
-- Select matches where Barcelona was the away team
SELECT  
    m.date,
    t.team_long_name AS opponent,
    CASE WHEN home_goal < away_goal THEN 'Barcelona win!'
        WHEN home_goal > away_goal THEN 'Barcelona loss :(' 
        ELSE 'Tie' END AS outcome
FROM matches_spain AS m
-- Join teams_spain to matches_spain
LEFT JOIN teams_spain AS t 
ON m.hometeam_id = t.team_api_id
WHERE m.awayteam_id = 8634;
```

# In CASE things get more complex

**Personal notes:**

If you want to test multiple logical conditions in a CASE statement, you can use `AND` within your WHEN clause. For example, let's check if each match was played and won by the Chelsea team.

```sql
SELECT date, hometeam_id, awayteam_id,
  CASE 
    WHEN hometeam_id = 8455 AND home_goal > away_goal THEN 'Chelsea home win!'
    WHEN awayteam_id = 8455 AND home_goal < away_goal THEN 'Chelsea away win!'
    ELSE 'Loss or tie' 
  END AS outcome
FROM match
WHERE hometeam_id = 8455 OR awayteam_id = 8455;
```

To filter a query by a CASE statement, you can include the entire CASE statement, except its alias, within the WHERE clause. You can, for example, exclude null values using `END IS NOT NULL`.

```sql
SELECT date, season, 
  CASE 
    WHEN hometeam_id = 8455 AND home_goal > away_goal THEN 'Chelsea home win!'
    WHEN awayteam_id = 8455 AND home_goal < away_goal THEN 'Chelsea away win!'  
  END AS outcome
FROM match
WHERE 
  CASE 
    WHEN hometeam_id = 8455 AND home_goal > away_goal THEN 'Chelsea home win!'
    WHEN awayteam_id = 8455 AND home_goal < away_goal THEN 'Chelsea away win!'  
  END IS NOT NULL;
```

**Exercises:**

**In CASE of rivalry**

Barcelona and Real Madrid have been rival teams for more than 80 years. Matches between these two teams are given the name El Clásico (The Classic). In this exercise, you will query a list of matches played between these two rivals.

You will notice in Step 2 that when you have multiple logical conditions in a CASE statement, you may quickly end up with a large number of WHEN clauses to logically test every outcome you are interested in. It's important to make sure you don't accidentally exclude key information in your ELSE clause.

In this exercise, you will retrieve information about matches played between Barcelona (id = 8634) and Real Madrid (id = 8633). Note that the query you are provided with already identifies the Clásico matches using a filter in the WHERE clause.

**Instructions:**

- Complete the first CASE statement, identifying Barcelona or Real Madrid as the home team using the hometeam_id column.

- Complete the second CASE statement in the same way, using awayteam_id.

```sql
SELECT 
    date,
    -- Identify the home team as Barcelona or Real Madrid
    CASE WHEN hometeam_id = 8634 THEN 'FC Barcelona' 
        ELSE 'Real Madrid CF' END AS home,
    -- Identify the away team as Barcelona or Real Madrid
    CASE WHEN awayteam_id = 8634 THEN 'FC Barcelona' 
        ELSE 'Real Madrid CF' END AS away
FROM matches_spain
WHERE (awayteam_id = 8634 OR hometeam_id = 8634)
    AND (awayteam_id = 8633 OR hometeam_id = 8633);
```

- Construct the final CASE statement identifying who won each match. Note there are 3 possible outcomes, but 5 conditions that you need to identify.

- Fill in the logical operators to identify Barcelona or Real Madrid as the winner.

```sql
SELECT 
    date,
    CASE WHEN hometeam_id = 8634 THEN 'FC Barcelona' 
        ELSE 'Real Madrid CF' END as home,
    CASE WHEN awayteam_id = 8634 THEN 'FC Barcelona' 
        ELSE 'Real Madrid CF' END as away,
    -- Identify all possible match outcomes
    CASE WHEN home_goal > away_goal AND hometeam_id = 8634 THEN 'Barcelona win!'
        WHEN home_goal > away_goal AND hometeam_id = 8633 THEN 'Real Madrid win!'
        WHEN home_goal < away_goal AND awayteam_id = 8634 THEN 'Barcelona win!'
        WHEN home_goal < away_goal AND awayteam_id = 8633 THEN 'Real Madrid win!'
        ELSE 'Tie!' END AS outcome
FROM matches_spain
WHERE (awayteam_id = 8634 OR hometeam_id = 8634)
    AND (awayteam_id = 8633 OR hometeam_id = 8633);
```

**Filtering your CASE statement**

Let's generate a list of matches won by Italy's Bologna team! There are quite a few additional teams in the two tables, so a key part of generating a usable query will be using your CASE statement as a filter in the WHERE clause.

CASE statements allow you to categorize data that you're interested in -- and exclude data you're not interested in. In order to do this, you can use a CASE statement as a filter in the WHERE statement to remove output you don't want to see.

Here is how you might set that up:
```sql
SELECT *
FROM table
WHERE 
    CASE WHEN a > 5 THEN 'Keep'
         WHEN a <= 5 THEN 'Exclude' END = 'Keep';
```
In essence, you can use the CASE statement as a filtering column like any other column in your database. The only difference is that you don't alias the statement in WHERE.

**Instructions:**

- Identify Bologna's team ID listed in the teams_italy table by selecting the team_long_name and team_api_id.

```sql
-- Select team_long_name and team_api_id from team
SELECT
    team_long_name,
    team_api_id
FROM teams_italy
-- Filter for team long name
WHERE team_long_name = 'Bologna';
```

- Select the season and date that a match was played.

- Complete the CASE statement so that only Bologna's home and away wins are identified.

```sql
-- Select the season and date columns
SELECT 
  season,
  date,
  -- Identify when Bologna won a match
  CASE WHEN hometeam_id = 9857 
      AND home_goal > away_goal 
      THEN 'Bologna Win'
      WHEN awayteam_id = 9857 
      AND away_goal > home_goal 
      THEN 'Bologna Win' 
      END AS outcome
FROM matches_italy;
```

- Select the home_goal and away_goal for each match.

- Use the CASE statement in the WHERE clause to filter all NULL values generated by the statement in the previous step.

```sql
-- Select the season, date, home_goal, and away_goal columns
SELECT 
    season,
    date,
    home_goal,
    away_goal
FROM matches_italy
WHERE 
-- Exclude games not won by Bologna
    CASE WHEN hometeam_id = 9857 AND home_goal > away_goal THEN 'Bologna Win'
        WHEN awayteam_id = 9857 AND away_goal > home_goal THEN 'Bologna Win' 
        END IS NOT NULL;
```

# CASE WHEN with aggregate functions

**Personal notes:**

CASE statements can be used to create columns to categorize data and filter your data in the WHERE clause. You can also use CASE to aggregate data based on the result of a logical test.

**CASE WHEN with COUNT*

CASE statements are like any other column in your query, so you can include them inside an aggregate function. The difference begins in its THEN clause. Instead of returning a text string, you return the column that identifies the unique matching ID. When this CASE statement is inside the COUNT function, it counts each id returned by this CASE statement.

```sql
SELECT 
  season,
  COUNT(CASE WHEN hometeam_id = 8650 AND home_goal > away_goal THEN id END) AS home_wins
FROM match
GROUP BY season;
```

You can add a second CASE statement for the away team and group the query by season. When counting information in a CASE statement, you can return whatever you want - a number, text string, or any column in the table. SQL is counting the number of rows returned by the CASE statement.

```sql
SELECT 
  season,
  COUNT(CASE WHEN hometeam_id = 8650 AND home_goal > away_goal THEN id END) AS home_wins,
  COUNT(CASE WHEN awayteam_id = 8650 AND away_goal > home_goal THEN 'some random text' END) AS away_wins
FROM match
GROUP BY season;
```

**CASE WHEN with SUM*

Similarly, you can use the SUM function to calculate a total of any value. For example, let's say we are interested in the number of home and away goals that Liverpool scored in each season.

```sql
SELECT
  season,
  SUM(CASE WHEN hometeam_id = 8650 THEN home_goal END) AS home_goals,
  SUM(CASE WHEN awayteam_id = 8650 THEN away_goal END) AS away_goals
FROM match
GROUP BY season;
```

**CASE with AVG*

You can use the AVG function along with the CASE in two ways. First, you can calculate an average of data. This can be done using CASE in the same way we used the SUM function. You can also use it along with the ROUND function, placing it outside the aggregated CASE statement and including the number of decimal places at the end for easier reading of the values.

```sql
SELECT
  season,
  ROUND(AVG(CASE WHEN hometeam_id = 8650 THEN home_goal END), 2) AS avg_homegoals,
  ROUND(AVG(CASE WHEN awayteam_id = 8650 THEN away_goal END), 2) AS avg_awaygoals
FROM match
GROUP BY season;
```

The second key application of CASE with AVG is in percentage calculation. This requires a specific structure for your calculation to be accurate. For example, let's answer: What percentage of Liverpool's games did they win in each season?

```sql
SELECT
  season,
  AVG(CASE WHEN hometeam_id = 8455 AND home_goal > away_goal THEN 1
           WHEN hometeam_id = 8455 AND home_goal < away_goal THEN 0 END) AS pct_homewins,
  AVG(CASE WHEN awayteam_id = 8455 AND away_goal > home_goal THEN 1
           WHEN awayteam_id = 8455 AND away_goal < home_goal THEN 0 END) AS pct_awaywins
FROM match
GROUP BY season;
```

*You can add the ROUND function in this case as well to facilitate the reading of the results.

**Exercises:**

**COUNT using CASE WHEN**

Do the number of soccer matches played in a given European country differ across seasons? We will use the European Soccer Database to answer this question.

You will examine the number of matches played in 3 seasons within each country listed in the database. This is much easier to explore with each season's matches in separate columns. Using the country and unfiltered match table, you will count the number of matches played in each country during the 2012/2013, 2013/2014, and 2014/2015 match seasons.

**Instructions:**

- Create a CASE statement that identifies the id of matches played in the 2012/2013 season. Specify that you want ELSE values to be NULL.

- Wrap the CASE statement in a COUNT function and group the query by the country alias.

```sql
SELECT 
  c.name AS country,
  -- Count games from the 2012/2013 season
  COUNT(CASE WHEN m.season = '2012/2013' 
          THEN m.id ELSE NULL END) AS matches_2012_2013
FROM country AS c
LEFT JOIN match AS m
ON c.id = m.country_id
-- Group by country name alias
GROUP BY country;
```

- Create 3 CASE WHEN statements counting the matches played in each country across the 3 seasons.

- END your CASE statement without an ELSE clause.

```sql
SELECT 
  c.name AS country,
  -- Count matches in each of the 3 seasons
  COUNT(CASE WHEN m.season = '2012/2013' THEN m.id END) AS matches_2012_2013,
  COUNT(CASE WHEN m.season = '2013/2014' THEN m.id END) AS matches_2013_2014,
  COUNT(CASE WHEN m.season = '2014/2015' THEN m.id END) AS matches_2014_2015
FROM country AS c
LEFT JOIN match AS m
ON c.id = m.country_id
-- Group by country name alias
GROUP BY country;
```

**COUNT and CASE WHEN with multiple conditions**

In R or Python, you have the ability to calculate a SUM of logical values (i.e., TRUE/FALSE) directly. In SQL, you have to convert these values into 1 and 0 before calculating a sum. This can be done using a CASE statement.

There's one key difference when using SUM to aggregate logical values compared to using COUNT in the previous exercise --

Your goal here is to use the country and match table to determine the total number of matches won by the home team in each country during the 2012/2013, 2013/2014, and 2014/2015 seasons.

**Instructions:**

- Create 3 CASE statements to "count" matches in the '2012/2013', '2013/2014', and '2014/2015' seasons, respectively.

- Have each CASE statement return a 1 for every match you want to include, and a 0 for every match to exclude.

- Wrap the CASE statement in a SUM to return the total matches played in each season.

- Group the query by the country name alias.

```sql
SELECT 
    c.name AS country,
    -- Sum the total records in each season where the home team won
    SUM(CASE WHEN m.season = '2012/2013' AND m.home_goal > m.away_goal 
        THEN 1 ELSE 0 END) AS matches_2012_2013,
    SUM(CASE WHEN m.season = '2013/2014' AND m.home_goal > m.away_goal 
        THEN 1 ELSE 0 END) AS matches_2013_2014,
    SUM(CASE WHEN m.season = '2014/2015' AND m.home_goal > m.away_goal 
        THEN 1 ELSE 0 END) AS matches_2014_2015
FROM country AS c
LEFT JOIN match AS m
ON c.id = m.country_id
-- Group by country name alias
GROUP BY country;
```

**Calculating percent with CASE and AVG**

CASE statements will return any value you specify in your THEN clause. This is an incredibly powerful tool for robust calculations and data manipulation when used in conjunction with an aggregate statement. One key task you can perform is using CASE inside an AVG function to calculate a percentage of information in your database.

Here's an example of how you set that up:

AVG(CASE WHEN condition_is_met THEN 1
         WHEN condition_is_not_met THEN 0 END)
With this approach, it's important to accurately specify which records count as 0, otherwise your calculations may not be correct!

Your task is to examine the number of wins, losses, and ties in each country. The matches table is filtered to include all matches from the 2013/2014 and 2014/2015 seasons.

**Instructions:**

- Create 3 CASE statements to COUNT the total number of home team wins, away team wins, and ties, which will allow you to examine the total number of records.

```sql
SELECT 
    c.name AS country,
    -- Count the home wins, away wins, and ties in each country
    COUNT(CASE WHEN m.home_goal > m.away_goal THEN m.id 
        END) AS home_wins,
    COUNT(CASE WHEN  m.home_goal < m.away_goal THEN m.id 
        END) AS away_wins,
    COUNT(CASE WHEN  m.home_goal = m.away_goal THEN m.id 
        END) AS ties
FROM country AS c
LEFT JOIN matches AS m
ON c.id = m.country_id
GROUP BY country;
```

- Calculate the percentage of matches tied using a CASE statement inside AVG.

- Fill in the logical operators for each statement. Alias your columns as ties_2013_2014 and ties_2014_2015, respectively.

```sql
SELECT 
c.name AS country,
-- Calculate the percentage of tied games in each season
  AVG(CASE WHEN m.season='2013/2014' AND m.home_goal = m.away_goal THEN 1
        WHEN m.season='2013/2014' AND m.home_goal != m.away_goal THEN 0
        END) AS ties_2013_2014,
  AVG(CASE WHEN m.season='2014/2015' AND m.home_goal = m.away_goal THEN 1
        WHEN m.season='2014/2015' AND m.home_goal != m.away_goal THEN 0
        END) AS ties_2014_2015
FROM country AS c
LEFT JOIN matches AS m
ON c.id = m.country_id
GROUP BY country;
```

- The previous "ties" columns returned values with 14 decimal points, which is not easy to interpret. Use the ROUND function to round to 2 decimal points.

```sql
SELECT 
c.name AS country,
-- Round the percentage of tied games to 2 decimal points
  ROUND(AVG(CASE WHEN m.season='2013/2014' AND m.home_goal = m.away_goal THEN 1
        WHEN m.season='2013/2014' AND m.home_goal != m.away_goal THEN 0
        END),2) AS pct_ties_2013_2014,
  ROUND(AVG(CASE WHEN m.season='2014/2015' AND m.home_goal = m.away_goal THEN 1
        WHEN m.season='2014/2015' AND m.home_goal != m.away_goal THEN 0
        END),2) AS pct_ties_2014_2015
FROM country AS c
LEFT JOIN matches AS m
ON c.id = m.country_id
GROUP BY country;
```