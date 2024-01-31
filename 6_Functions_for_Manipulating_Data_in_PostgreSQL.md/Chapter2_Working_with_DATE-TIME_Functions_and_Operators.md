# Working with DATE/TIME Functions and Operators

**Chapter description:**

Explore how to manipulate and query date and time objects including how to use the current timestamp in your queries, extract subfields from existing date and time fields and what to expect when you perform date and time arithmetic.

# Overview of basic arithmetic operators

**Personal notes:**

**Adding and Subtracting Date/Time Data*

Performing basic arithmetic operations on date and time data will become a useful skill in practice, but it's important to understand how the return values for these operations vary depending on the type of date and time data you're working with. For example, when you subtract date values, the result returned is an integer data type.

You can also add integer values to date data types. In this example, we are adding the integer number three to a date value, and the result shows three days greater than the original date, in this case, '2005-09-14'. When adding integer numbers to date values, the implicit precision is days.

```sql
SELECT date '2005-09-11' + integer '3';
```

However, when we perform the same operation on timestamp date/time data, as in the example below, we get an INTERVAL as a result '1 day 12:00:00':

```sql
SELECT timestamp '2005-09-11 00:00:00' - timestamp '2005-09-09 12:00:00';
```

**Calculating Time Periods with AGE*

The AGE function allows us to calculate the difference between two timestamp values. The AGE function takes two timestamp arguments, subtracts the first argument from the second, and returns an INTERVAL as a result, similar to the output in the previous example.

```sql
SELECT AGE(timestamp '2005-09-11 00:00:00', timestamp '2005-09-09 12:00:00');
```

**Date/Time Arithmetic Using INTERVALs*

Learning to use INTERVALs for date and time calculations is a very useful skill for developing real-world applications. Using an INTERVAL is a great technique when you need to perform relative date and time calculations. We can perform multiplication and division on date and time data using intervals, which is another useful tool when dealing with relative date and time data. For example, let's say we want to add 21 days to a date value using a timestamp and an interval:

```sql
SELECT timestamp '2019-05-01' + 21 * INTERVAL '1 day';
```

**Exercises:**

**Adding and subtracting date and time values**

In this exercise, you will calculate the actual number of days rented as well as the true expected_return_date by using the rental_duration column from the film table along with the familiar rental_date from the rental table.

This will require that you dust off the skills you learned from prior courses on how to join two or more tables together. To select columns from both the film and rental tables in a single query, we'll need to use the inventory table to join these two tables together since there is no explicit relationship between them. Let's give it a try!

**Instructions:**

- Subtract the rental_date from the return_date to calculate the number of days_rented.

```sql
SELECT f.title, f.rental_duration,
    -- Calculate the number of days rented
    r.return_date - r.rental_date AS days_rented
FROM film AS f
    INNER JOIN inventory AS i ON f.film_id = i.film_id
    INNER JOIN rental AS r ON i.inventory_id = r.inventory_id
ORDER BY f.title;
```

- Now use the AGE() function to calculate the days_rented.

```sql
SELECT f.title, f.rental_duration,
    -- Calculate the number of days rented
    AGE(r.return_date, r.rental_date ) AS days_rented
FROM film AS f
    INNER JOIN inventory AS i ON f.film_id = i.film_id
    INNER JOIN rental AS r ON i.inventory_id = r.inventory_id
ORDER BY f.title;
```

**INTERVAL arithmetic**

If you were running a real DVD Rental store, there would be times when you would need to determine what film titles were currently out for rental with customers. In the previous exercise, we saw that some of the records in the results had a NULL value for the return_date. This is because the rental was still outstanding.

Each rental in the film table has an associated rental_duration column which represents the number of days that a DVD can be rented by a customer before it is considered late. In this example, you will exclude films that have a NULL value for the return_date and also convert the rental_duration to an INTERVAL type. Here's a reminder of one method for performing this conversion.

SELECT INTERVAL '1' day * timestamp '2019-04-10 12:34:56'

**Instructions:**

- Convert rental_duration by multiplying it with a 1 day INTERVAL

- Subtract the rental_date from the return_date to calculate the number of days_rented.

- Exclude rentals with a NULL value for return_date.

```sql
SELECT
    f.title,
    -- Convert the rental_duration to an interval
    INTERVAL '1' day * f.rental_duration,
    -- Calculate the days rented as we did previously
    r.return_date - r.rental_date AS days_rented
FROM film AS f
    INNER JOIN inventory AS i ON f.film_id = i.film_id
    INNER JOIN rental AS r ON i.inventory_id = r.inventory_id
-- Filter the query to exclude outstanding rentals
WHERE r.return_date IS NOT NULL
ORDER BY f.title;
```

**Calculating the expected return date**

So now that you've practiced how to add and subtract timestamps and perform relative calculations using intervals, let's use those new skills to calculate the actual expected return date of a specific rental. As you've seen in previous exercises, the rental_duration is the number of days allowed for a rental before it's considered late. To calculate the expected_return_date you will want to use the rental_duration and add it to the rental_date.

**Instructions:**

- Convert rental_duration by multiplying it with a 1-day INTERVAL.

- Add it to the rental date.

```sql
SELECT
    f.title,
    r.rental_date,
    f.rental_duration,
    -- Add the rental duration to the rental date
    INTERVAL '1' day * f.rental_duration + r.rental_date AS expected_return_date,
    r.return_date
FROM film AS f
    INNER JOIN inventory AS i ON f.film_id = i.film_id
    INNER JOIN rental AS r ON i.inventory_id = r.inventory_id
ORDER BY f.title;
```

# Functions for retrieving current date/time

**Personal notes:**

**Retrieving the Current Timestamp*

One of the most common techniques is to retrieve the current date and time value in your query. PostgreSQL provides various functions to do this, starting with the NOW() function. NOW() allows you to retrieve a timestamp value for the current date and time with microsecond precision and time zone.

```sql
SELECT NOW();
```

If you want to retrieve the current timestamp without the time zone, you can do that using CAST (Casting).

```sql
SELECT NOW()::timestamp;
```

Casting (conversion) allows you to convert one data type to another, so that columns stored in your database can be retrieved and cast as a different type. There are several ways to cast data in your queries. The above syntax using the double colon operator is specific to PostgreSQL and not compliant with the SQL standard. We can use the CAST() function to achieve the same result by specifying the column name or, in this case, the NOW() function followed by the type name.

```sql
SELECT CAST(NOW() AS timestamp);
```

PostgreSQL provides alternative methods to retrieve the current date and time values. The CURRENT_TIMESTAMP function returns the same result as the NOW() function, and both approaches can be used quite interchangeably in your queries.

```sql
SELECT CURRENT_TIMESTAMP;
```

One difference between CURRENT_TIMESTAMP and NOW() is that with CURRENT_TIMESTAMP, you can specify a precision parameter, as you see in the example, which will round the seconds to the specified number of fractional digits.

```sql
SELECT CURRENT_TIMESTAMP(2);
```

**Current Date and Time*

Sometimes, you may want to get the current date or time, but you don't need the precision of a timestamp. In these cases, you can use the CURRENT_DATE and/or CURRENT_TIME functions. In this example, the CURRENT_DATE function will return a date value without the time.

```sql
SELECT CURRENT_DATE;
```

Additionally, you can use the CURRENT_TIME function to get the time value with the time zone without the date value, as seen earlier.

**Exercises:**

**Current timestamp functions**

Use the console to explore the NOW(), CURRENT_TIMESTAMP, CURRENT_DATE and CURRENT_TIME functions and their outputs to determine which of the following is NOT correct?

        "CURRENT_TIMESTAMP returns the current timestamp without timezone."

**Working with the current date and time**

Because the Sakila database is a bit dated and most of the date and time values are from 2005 or 2006, you are going to practice using the current date and time in our queries without using Sakila. You'll get back into working with this database in the next video and throughout the remainder of the course. For now, let's practice the techniques you learned about so far in this chapter to work with the current date and time.

As you learned in the video, NOW() and CURRENT_TIMESTAMP can be used interchangeably.

**Instructions:**

- Use NOW() to select the current timestamp with timezone.

```sql
-- Select the current timestamp
SELECT NOW();
```

- Select the current date without any time value.

```sql
-- Select the current date
SELECT CURRENT_DATE;
```
 
- Now, let's use the CAST() function to eliminate the timezone from the current timestamp.

```sql
--Select the current timestamp without a timezone
SELECT CAST( NOW() AS timestamp )
```

- Finally, let's select the current date.
Use CAST() to retrieve the same result from the NOW() function.

```sql
SELECT 
    -- Select the current date
    CURRENT_DATE,
    -- CAST the result of the NOW() function to a date
    CAST( NOW() AS date )
```

**Manipulating the current date and time**

Most of the time when you work with the current date and time, you will want to transform, manipulate, or perform operations on the value in your queries. In this exercise, you will practice adding an INTERVAL to the current timestamp as well as perform some more advanced calculations.

Let's practice retrieving the current timestamp. For this exercise, please use CURRENT_TIMESTAMP instead of the NOW() function and if you need to convert a date or time value to a timestamp data type, please use the PostgreSQL specific casting rather than the CAST() function.

**Instructions:**

- Select the current timestamp without timezone and alias it as right_now.

```sql
--Select the current timestamp without timezone
SELECT CURRENT_TIMESTAMP::timestamp AS right_now;
```

- Now select a timestamp five days from now and alias it as five_days_from_now.

```sql
SELECT
    CURRENT_TIMESTAMP::timestamp AS right_now,
    INTERVAL '5 days' + CURRENT_TIMESTAMP AS five_days_from_now;
```

- Finally, let's use a second-level precision with no fractional digits for both the right_now and five_days_from_now fields.

```sql
SELECT
    CURRENT_TIMESTAMP(2)::timestamp AS right_now,
    interval '5 days' + CURRENT_TIMESTAMP(2) AS five_days_from_now;
```

# Extracting and transforming date/ time data

**Personal notes:**

In this lesson, we will explore additional built-in functions that will help us transform timestamp and interval data types, creating new fields to prepare data for analysis. Let's start by looking at how to use the EXTRACT, DATE_PART, and DATE_TRUNC functions to manipulate timestamp date/time data and create new columns by extracting subfields from existing date and time values. This type of data manipulation is useful when the precision of a timestamp is not needed for analysis, and you want to use date parts such as year or month in your query, but the underlying data contains only a standard timestamp value. You may also not care about certain precisions like the time of day in some analyses, and truncating timestamps may be necessary.

**EXTRACT and DATE_PART*

To use these functions in your queries, you need to pass two parameters: the field identifier and the source.

```sql
EXTRACT(field FROM source)
```

```sql
DATE_PART('field', source)
```

The field parameter is an identifier or string if you are using DATE_PART, indicating which subfield you want to extract from the source. Various field identifiers include year, month, quarter, day of the week, etc. The source parameter needs to be a valid timestamp or interval data type.

```sql
SELECT EXTRACT(quarter FROM timestamp '2005-01-24 05:12:00') AS quarter;
```

```sql
SELECT DATE_PART('quarter', timestamp '2005-01-24 05:12:00') AS quarter;
```

EXTRACT and DATE_PART produce identical results and can be used interchangeably, with only minor variations in how you pass the field and source parameters.

**Extracting Sub-Fields from Timestamp Data*

In our DVD rentals database, each customer rental has a corresponding record in the payment table, and each transaction is recorded with a timestamp in the 'payment_date' column. While this level of detail is necessary for an e-commerce application, there will undoubtedly be times when you want to aggregate this data for use in model training, reporting, and/or trend analysis.

For example, you might want to identify the highest revenue per quarter. To do this, we want to aggregate the payment amount column and use the EXTRACT function to extract the quarter and year subfields from the payment_date column. We also introduce a technique with the GROUP BY clause that allows us to specify fields in the SELECT clause using a numeric reference, which is helpful when using functions to derive new columns.

```sql
SELECT
  EXTRACT(quarter FROM payment_date) AS quarter,
  EXTRACT(year FROM payment_date) AS year,
  SUM(amount) AS total_payments
FROM
  payment
GROUP BY 1, 2;
```

**Truncating Timestamps using DATE_TRUNC()*

The DATE_TRUNC() function will truncate the timestamp or interval data type to return a timestamp or interval at a specified precision. Precision values are a subset of the field identifiers that can be used with the EXTRACT() and DATE_PART() functions. For example, to truncate a date to the year, we pass the year identifier as the first parameter to the DATE_TRUNC function, resulting in an interval or timestamp instead of a numeric value.

```sql
SELECT DATE_TRUNC('year', TIMESTAMP '2005-05-21 15:30:30');
```

**Exercises:**

**Using EXTRACT**

You can use EXTRACT() and DATE_PART() to easily create new fields in your queries by extracting sub-fields from a source timestamp field.

Now suppose you want to produce a predictive model that will help forecast DVD rental activity by day of the week. You could use the EXTRACT() function with the dow field identifier in our query to create a new field called dayofweek as a sub-field of the rental_date column from the rental table.

You can COUNT() the number of records in the rental table for a given date range and aggregate by the newly created dayofweek column.

**Instructions:**

- Get the day of the week from the rental_date column.

```sql
SELECT 
-- Extract day of week from rental_date
EXTRACT(dow FROM rental_date) AS dayofweek 
FROM rental 
LIMIT 100;
```

- Count the total number of rentals by day of the week.

```sql
-- Extract day of week from rental_date
SELECT 
EXTRACT(dow FROM rental_date) AS dayofweek, 
-- Count the number of rentals
COUNT(rental) as rentals 
FROM rental 
GROUP BY 1;
```

**Using DATE_TRUNC**

The DATE_TRUNC() function will truncate timestamp or interval data types to return a timestamp or interval at a specified precision. The precision values are a subset of the field identifiers that can be used with the EXTRACT() and DATE_PART() functions. DATE_TRUNC() will return an interval or timestamp rather than a number. For example

SELECT DATE_TRUNC('month', TIMESTAMP '2005-05-21 15:30:30');
Result: 2005-05-01 00;00:00

Now, let's experiment with different precisions and ultimately modify the queries from the previous exercises to aggregate rental activity.

**Instructions:**

- Truncate the rental_date field by year.

```sql
-- Truncate rental_date by year
SELECT DATE_TRUNC('year', rental_date) AS rental_year
FROM rental;
```

- Now modify the previous query to truncate the rental_date by month.

```sql
-- Truncate rental_date by month
SELECT DATE_TRUNC('month', rental_date) AS rental_month
FROM rental;
```

- Let's see what happens when we truncate by day of the month.

```sql
-- Truncate rental_date by day of the month 
SELECT DATE_TRUNC('day', rental_date) AS rental_day 
FROM rental;
```

- Finally, count the total number of rentals by rental_day and alias it as rentals.

```sql
SELECT 
DATE_TRUNC('day', rental_date) AS rental_day,
-- Count total number of rentals 
COUNT('rental_day') AS rentals
FROM rental
GROUP BY 1;
```

**Putting it all together**

Many of the techniques you've learned in this course will be useful when building queries to extract data for model training. Now let's use some date/time functions to extract and manipulate some DVD rentals data from our fictional DVD rental store.

In this exercise, you are going to extract a list of customers and their rental history over 90 days. You will be using the EXTRACT(), DATE_TRUNC(), and AGE() functions that you learned about during this chapter along with some general SQL skills from the prerequisites to extract a data set that could be used to determine what day of the week customers are most likely to rent a DVD and the likelihood that they will return the DVD late.

**Instructions:**

- Extract the day of the week from the rental_date column using the alias dayofweek.

- Use an INTERVAL in the WHERE clause to select records for the 90 day period starting on 5/1/2005.

```sql
SELECT 
-- Extract the day of week date part from the rental_date
EXTRACT(day FROM rental_date) AS dayofweek,
AGE(return_date, rental_date) AS rental_days
FROM rental AS r 
WHERE 
-- Use an INTERVAL for the upper bound of the rental_date 
rental_date BETWEEN CAST('2005-05-01' AS date)
AND CAST('2005-05-01' AS date) + INTERVAL '90 day';
```

- Finally, use a CASE statement and DATE_TRUNC() to create a new column called past_due which will be TRUE if the rental_days is greater than the rental_duration otherwise, it will be FALSE.

```sql
SELECT 
c.first_name || ' ' || c.last_name AS customer_name,
f.title,
r.rental_date,
-- Extract the day of week date part from the rental_date
EXTRACT(dow FROM r.rental_date) AS dayofweek,
AGE(r.return_date, r.rental_date) AS rental_days,
-- Use DATE_TRUNC to get days from the AGE function
CASE WHEN DATE_TRUNC('day', AGE(r.return_date, r.rental_date)) > 
-- Calculate number of d
    f.rental_duration * INTERVAL '1' day 
THEN TRUE 
ELSE FALSE END AS past_due 
FROM 
film AS f 
INNER JOIN inventory AS i 
    ON f.film_id = i.film_id 
INNER JOIN rental AS r 
    ON i.inventory_id = r.inventory_id 
INNER JOIN customer AS c 
    ON c.customer_id = r.customer_id 
WHERE 
-- Use an INTERVAL for the upper bound of the rental_date 
r.rental_date BETWEEN CAST('2005-05-01' AS DATE) 
AND CAST('2005-05-01' AS DATE) + INTERVAL '90 day';
```

