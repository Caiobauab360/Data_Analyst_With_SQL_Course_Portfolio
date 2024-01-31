# Parsing and Manipulating Text

**Chapter description:**

Learn how to manipulate string and text data by transforming case, parsing and truncating text and extracting substrings from larger strings.

# Reformatting string and character data

**Personal notes:**

In this chapter, we will learn how to manipulate and transform string and character data. In this lesson, we'll start by exploring functions and operators that allow us to reformat and analyze string and character data. We will also see how to calculate the length of a string or determine the position of a character within a string. Finally, we'll learn how to truncate and pad string data.

**The String Concatenation Operator*

String concatenation allows you to merge two or more strings to form a single combined string. In this example, you can see how we can combine two separate columns from the customer table, first_name and last_name, to create a new column called full_name.

```sql
SELECT
  first_name,
  last_name,
  first_name || ' ' || last_name AS full_name
FROM customer;
```

**String Concatenation with Functions*

Additionally, PostgreSQL also has a built-in function for string concatenation. The CONCAT() function accepts one or more parameters and returns the concatenated string as a result. Each parameter can be a column from a database or a literal value separated by a comma. In this example, we see how we can perform the same concatenation operation using this function instead of the || operator, yielding an identical result.

```sql
SELECT
  CONCAT(first_name, ' ', last_name) AS full_name
FROM customer;
```

**String Concatenation with a Non-String Input*

PostgreSQL also allows concatenating string and non-string data, as in the example below where we append the customer_id column to the first_name and last_name columns. Non-string data can be used in concatenation with both the || operator and the concat() function.

```sql
SELECT
  customer_id || ': ' || first_name || ' ' || last_name AS full_name
FROM customer;
```

**Changing the Case of String*

There will also be times when you want to reformat string data to uppercase, lowercase, or initial capitalization. This is useful when you want to standardize a field in your dataset for manipulation. The UPPER function allows reformatting a string to change each character to its uppercase equivalent. UPPER takes a string as a parameter and returns that string in uppercase letters.

```sql
SELECT
  UPPER(email)
FROM customer;
```

Similarly, the LOWER function is analogous to UPPER but converts the string to lowercase. It is used in the same way.

```sql
SELECT 
  LOWER(title)
FROM film;
```

Likewise, the INITCAP function will convert a string to initial capitalization. For example, a column with full names where the first letter of each name needs to be uppercase and the rest lowercase.

```sql
SELECT
  INITCAP(title)
FROM film;
```

**Replacing Characters in a String*

The REPLACE function will find a substring in a string and replace it with a different substring. For instance, if we need to correct a grammatical error within a table and replace all occurrences of "A Astounding" with the correct text "An Astounding," we can use the REPLACE function. The function takes three parameters: the original string, the substring to find in the original string, and the replacement string.

```sql
SELECT
  REPLACE(description, 'A Astounding', 'An Astounding') AS description
FROM film;
```

**Manipulating String Data with REVERSE*

The REVERSE function takes a string as its only parameter and returns the same string reversed.

```sql
SELECT
  title,
  REVERSE(title)
FROM film AS f;
```

**Exercises:**

**Concatenating strings**

In this exercise and the ones that follow, we are going to derive new fields from columns within the customer and film tables of the DVD rental database.

We'll start with the customer table and create a query to return the customers name and email address formatted such that we could use it as a "To" field in an email script or program. This format will look like the following:

Brian Piccolo <bpiccolo@datacamp.com>

In the first step of the exercise, use the || operator to do the string concatenation and in the second step, use the CONCAT() functions.

**Instructions:**

- Concatenate the first_name and last_name columns separated by a single space followed by email surrounded by < and >.

```sql
-- Concatenate the first_name and last_name and email 
SELECT first_name || ' ' || last_name || ' <' || email || '>' AS full_email 
FROM customer
```

- Now use the CONCAT() function to do the same operation as the previous step.

```sql
-- Concatenate the first_name and last_name and email
SELECT CONCAT(first_name, ' ', last_name, ' ', '<', email, '>') AS full_email 
FROM customer
```

**Changing the case of string data**

Now you are going to use the film and category tables to create a new field called film_category by concatenating the category name with the film's title. You will also format the result using functions you learned about in the video to transform the case of the fields you are selecting in the query; for example, the INITCAP() function which converts a string to title case.

**Instructions:**

- Convert the film category name to uppercase.

- Convert the first letter of each word in the film's title to upper case.

- Concatenate the converted category name and film title separated by a colon.

- Convert the description column to lowercase.

```sql
SELECT 
-- Concatenate the category name to coverted to uppercase
-- to the film title converted to title case
UPPER(name)  || ': ' || INITCAP(title) AS film_category, 
-- Convert the description column to lowercase
LOWER(description) AS description
FROM 
film AS f 
INNER JOIN film_category AS fc 
    ON f.film_id = fc.film_id 
INNER JOIN category AS c 
    ON fc.category_id = c.category_id;
```

**Replacing string data**

Sometimes you will need to make sure that the data you are extracting does not contain any whitespace. There are many different approaches you can take to cleanse and prepare your data for these situations. A common technique is to replace any whitespace with an underscore.

In this example, we are going to practice finding and replacing whitespace characters in the title column of the film table using the REPLACE() function.

**Instruction:**

- Replace all whitespace with an underscore.

```sql
SELECT 
-- Replace whitespace in the film title with an underscore
REPLACE(title, ' ', '_') AS title
FROM film; 
```

# Parsing string and character data

**Personal notes:**

In this lesson, we will learn about string functions in PostgreSQL that allow us to analyze and manipulate text data. We will also learn how to combine and nest functions to provide additional features.

**Determining the Length of a String*

First, let's examine the CHAR_LENGTH function. It can be used to determine the number of characters in a string. CHAR_LENGTH accepts a string as input and returns the number of characters in the string as an integer for output. In this example, we see the CHAR_LENGTH function used on the title column of the film table in the DVD rental database.

```sql
SELECT
  title,
  CHAR_LENGTH(title)
FROM film;
```

LENGTH is analogous to CHAR_LENGTH, accepting the same parameter and returning the same result as you can see in this example. These two functions can be used interchangeably depending on your preference.

```sql
SELECT
  title,
  LENGTH(title)
FROM film;
```

**Finding the Position of a Character in a String*

The POSITION function returns an integer representing the number of characters from left to right before the search string is found.

```sql
SELECT 
  email,
  POSITION('@' IN email)
FROM customer;
```

STRPOS is analogous to POSITION with slightly different syntax, as shown in the example below.

```sql
SELECT
  email,
  STRPOS(email, '@')
FROM customer;
```

**Parsing String Data*

Now, let's look at some functions that will help parse strings into substrings. The LEFT function allows you to extract the first n characters from a string. For example, in this query, we are extracting the first fifty characters from the description column of the film table in our DVD rental database.

```sql
SELECT
  LEFT(description, 50)
FROM film;
```

The RIGHT function is very similar to LEFT, but it extracts the last n characters from a string.

```sql
SELECT
  RIGHT(description, 50)
FROM film;
```

**Extracting Substring of Character Data*

SUBSTRING allows us to extract a substring of text data. The SUBSTRING function takes three parameters: the source string or column, an integer representing the starting position of the source string, and another integer to specify the length of the substring we want to extract.

```sql
SELECT
  SUBSTRING(description, 10, 50)
FROM film AS f;
```

SUBSTRING can be combined with other functions to provide additional features. For example, we can extract the text to the left of the at sign (@) in an email address using a slightly different set of parameters in the SUBSTRING function. The first parameter remains the same, but the second parameter is replaced by the keyword FROM followed by an integer representing the starting position, and the third parameter includes the keyword FOR followed by an integer representing the end position. In this example, we use the POSITION function as the second parameter in the SUBSTRING function.

```sql
SELECT
  SUBSTRING(email FROM 0 FOR POSITION('@' IN email))
FROM customer;
```

Now, we can use a different technique if we want to extract the characters to the right of the at sign in the email column. In this example, we use the POSITION function as the second parameter of SUBSTRING to determine the starting position of the string, and CHAR_LENGTH to determine the last position, which is a handy trick for finding the last position of a string. The POSITION function will return the integer value of the position of the at sign in the string. To exclude the at sign from the result, we need to add one to the starting position.

```sql
SELECT
  SUBSTRING(email FROM POSITION('@' IN email) + 1 FOR CHAR_LENGTH(email))
FROM customer;
```

SUBSTR is analogous to SUBSTRING but only allows the parameters to be separated by commas and does not allow the alternative syntax with the keywords FROM and FOR.

```sql
SELECT 
  SUBSTR(description, 10, 50)
FROM film AS f;
```

**Exercises:**

**Determining the length of strings**

Determining the number of characters in a string is something that you will use frequently when working with data in a SQL database. Many situations will require you to find the length of a string stored in your database. For example, you may need to limit the number of characters that are displayed in an application or you may need to ensure that a column in your dataset contains values that are all the same length. In this example, we are going to determine the length of the description column in the film table of the DVD Rental database.

**Instructions:**

- Select the title and description columns from the film table.

- Find the number of characters in the description column with the alias desc_len.

```sql
SELECT 
-- Select the title and description columns
title,
description,
-- Determine the length of the description column
LENGTH(description) AS desc_len
FROM film;
```

**Truncating strings**

In the previous exercise, you calculated the length of the description column and noticed that the number of characters varied but most of the results were over 75 characters. There will be many times when you need to truncate a text column to a certain length to meet specific criteria for an application. In this exercise, we will practice getting the first 50 characters of the description column.

**Instruction:**

- Select the first 50 characters of the description column with the alias short_desc

```sql
SELECT 
-- Select the first 50 characters of description
LEFT(description, 50) AS short_desc
FROM 
film AS f; 
```

**Extracting substrings from text data**

In this exercise, you are going to practice how to extract substrings from text columns. The Sakila database contains the address table which stores the street address for all the rental store locations. You need a list of all the street names where the stores are located but the address column also contains the street number. You'll use several functions that you've learned about in the video to manipulate the address column and return only the street address.

**Instructions:**

- Extract only the street address without the street number from the address column.

- Use functions to determine the starting and ending position parameters.

```sql
SELECT 
-- Select only the street name from the address table
SUBSTRING(address FROM POSITION(' ' IN address)+1 FOR CHAR_LENGTH(address))
FROM 
address;
```

**Combining functions for string manipulation**

In the next example, we are going to break apart the email column from the customer table into three new derived fields. Parsing a single column into multiple columns can be useful when you need to work with certain subsets of data. Email addresses have embedded information stored in them that can be parsed out to derive additional information about our data. For example, we can use the techniques we learned about in the video to determine how many of our customers use an email from a specific domain.

**Instructions:**

- Extract the characters to the left of the @ of the email column in the customer table and alias it as username.

- Now use SUBSTRING to extract the characters after the @ of the email column and alias the new derived field as domain.

```sql
SELECT
-- Extract the characters to the left of the '@'
SUBSTRING(email FROM 1 FOR POSITION('@' IN email) - 1) AS username,
-- Extract the characters to the right of the '@'
SUBSTRING(email FROM POSITION('@' IN email) + 1 FOR CHAR_LENGTH(email)) AS domain
FROM customer;
```

# Truncating and padding string data

**Personal notes:**

In the next section, we will look at how to truncate and replace or overwrite characters in a string.

**Removing Whitespace from Strings*

The first function we will look at is TRIM. The TRIM function will remove characters from either the beginning or end of the string or both, and it accepts three parameters. The first parameter is optional and specifies whether you want to remove characters from the beginning or end of a string or both. If this parameter is omitted, the default value is both. The second parameter, which is also optional, specifies the characters to be removed from the string. If this parameter is omitted, the default value is whitespace. Finally, the last parameter is the string you want to trim.

```sql
-- TRIM([leading | trailing | both] [characters] FROM string)
SELECT TRIM(' padded ');
```

Most of the time, we will use this function without the first two parameters, with only the string as the single parameter. This default behavior will remove all whitespace from the beginning and end of the string, as you can see in this example.

```sql
SELECT TRIM(' padded ');
```

The LTRIM and RTRIM functions are analogous to TRIM but only remove characters from the beginning or end of the string, not both. Very similar to TRIM, we will use these functions with their default behavior to trim whitespace. In this example, we see that LTRIM removes only the spaces at the beginning of the selected string, while we will see the opposite result using RTRIM, which removes only the spaces at the end of the selected string.

```sql
SELECT LTRIM(' padded ');
SELECT RTRIM(' padded ');
```

**Padding Strings with Character Data*

The LPAD function appends a character or string to another string for a specified number of characters. This is useful when you need a field to have the same length and want to fill the string with a specific character, such as a space or tab. In this example, we are padding the word 'padded' with the hash character so that the returned string has a character length equal to ten.

```sql
SELECT LPAD('padded', 10, '#');
```

If you omit the third parameter in the LPAD function, as you can see in the example below, the string will be filled with a space character by default. When the length parameter is smaller than the original length of the string, the returned result will be truncated, removing the last letter.

```sql
SELECT LPAD('padded', 10);
SELECT LPAD('padded', 5);
```

The RPAD function is analogous to LPAD but will pad the string with characters to the right. The syntax works in the same way:

```sql
SELECT RPAD('padded', 10, '#');
```

**Exercises:**

**Padding**

Padding strings is useful in many real-world situations. Earlier in this course, we learned about string concatenation and how to combine the customer's first and last name separated by a single blank space and also combined the customer's full name with their email address.

The padding functions that we learned about in the video are an alternative approach to do this task. To use this approach, you will need to combine and nest functions to determine the length of a string to produce the desired result. Remember when calculating the length of a string you often need to adjust the integer returned to get the proper length or position of a string.

Let's revisit the string concatenation exercise but use padding functions.

**Instructions:**

- Add a single space to the end or right of the first_name column using a padding function.

- Use the || operator to concatenate the padded first_name to the last_name column.

```sql
-- Concatenate the padded first_name and last_name 
SELECT 
    RPAD(first_name, LENGTH(first_name)+1) || last_name AS full_name
FROM customer;
```

- Now add a single space to the left or beginning of the last_name column using a different padding function than the first step.

- Use the || operator to concatenate the first_name column to the padded last_name.

```sql
-- Concatenate the first_name and last_name 
SELECT 
    first_name || LPAD(last_name, LENGTH(last_name)+1) AS full_name
FROM customer; 
```

- Add a single space to the right or end of the first_name column.

- Add the characters < to the right or end of last_name column.

- Finally, add the characters > to the right or end of the email column.

```sql
-- Concatenate the first_name and last_name 
SELECT 
    RPAD(first_name, LENGTH(first_name)+1) 
    || RPAD(last_name, LENGTH(last_name)+2, ' <') 
    || RPAD(email, LENGTH(email)+1, '>') AS full_email
FROM customer; 
```

**The TRIM function**

In this exercise, we are going to revisit and combine a couple of exercises from earlier in this chapter. If you recall, you used the LEFT() function to truncate the description column to 50 characters but saw that some words were cut off and/or had trailing whitespace. We can use trimming functions to eliminate the whitespace at the end of the string after it's been truncated.

**Instructions:**

- Convert the film category name to uppercase and use the CONCAT() concatenate it with the title.

- Truncate the description to the first 50 characters and make sure there is no leading or trailing whitespace after truncating.

```sql
-- Concatenate the uppercase category name and film title
SELECT 
CONCAT(UPPER(c.name), ': ', f.title) AS film_category, 
-- Truncate the description and remove trailing whitespace
TRIM(TRAILING ' ' FROM SUBSTRING(f.description FROM 1 FOR 50)) AS film_desc
FROM 
film AS f 
INNER JOIN film_category AS fc 
    ON f.film_id = fc.film_id 
INNER JOIN category AS c 
    ON fc.category_id = c.category_id;
```

**Putting it all together**

In this exercise, we are going to use the film and category tables to create a new field called film_category by concatenating the category name with the film's title. You will also practice how to truncate text fields like the film table's description column without cutting off a word.

To accomplish this we will use the REVERSE() function to help determine the position of the last whitespace character in the description before we reach 50 characters. This technique can be used to determine the position of the last character that you want to truncate and ensure that it is less than or equal to 50 characters AND does not cut off a word.

This is an advanced technique but I know you can do it! Let's dive in.

**Instructions:**

- Get the first 50 characters of the description column

- Determine the position of the last whitespace character of the truncated description column and subtract it from the number 50 as the second parameter in the first function above.

```sql
SELECT 
UPPER(c.name) || ': ' || f.title AS film_category, 
-- Truncate the description without cutting off a word
LEFT(description, 50 - 
-- Subtract the position of the first whitespace character
    POSITION(
' ' IN REVERSE(LEFT(description, 50))
    )
        ) 
FROM 
film AS f 
INNER JOIN film_category AS fc 
    ON f.film_id = fc.film_id 
INNER JOIN category AS c 
    ON fc.category_id = c.category_id;
```

