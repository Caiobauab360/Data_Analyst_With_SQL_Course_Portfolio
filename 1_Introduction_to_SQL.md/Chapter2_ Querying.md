# Querying

**Chapter description:**

Learn your first SQL keywords for selecting relevant data from database tables! After practicing querying skills in a database of books, you’ll customize query results using aliasing and save them as views so they can be shared. Finally, you’ll explore the differences between SQL flavors and databases such as PostgreSQL and SQL Server.

# Introducing queries

**Personal notes:**

**Introducing Queries**

In many organizations, SQL serves as a complement to other tools, such as spreadsheet applications. If the data of interest fits into a spreadsheet and has few relations with other relevant data, analyzing it within a spreadsheet might be sufficient. However, for extensive and diverse data, like those associated with a retail platform, organizing the data into a database proves to be a more effective approach.

**Keywords*

Keywords are reserved words used to indicate the operation we want our code to perform. The two most common keywords are SELECT and FROM. For instance, if we desire a list of all users in our library, the SELECT keyword specifies which fields should be selected, in this case, the "name" field.

```sql
SELECT name
```

The FROM keyword indicates the table where these fields are located; in this case, it is the "patrons" table.

```sql
FROM patrons;
```

**Our First Query*

Once the query is structured, the SELECT statement appears first, followed by the FROM statement in the next line. It is a best practice to conclude the query with a semicolon to signify its completion. Additionally, we capitalize keywords while keeping table and field names in lowercase.

```sql
SELECT name
FROM patrons;
```

The outcome of a query is referred to as the result set. It is essential to note that executing this query does not modify our database.

To select multiple fields, we can list various field names after the SELECT keyword, separated by commas.

```sql
SELECT card_num, name
FROM patrons;
```

To choose all fields, use an asterisk (*) instead of field names.

```sql
SELECT *
FROM patrons;
```

**Exercises:**

**SQL strengths**

Which of the below scenarios describes a situation in which using SQL would be useful?

        "All data needed to answer the business question is presented in a spreadsheet, and no complicated relationships exist between different data points."

**Querying the books table**

You're ready to practice writing your first SQL queries using the SELECT and FROM keywords. Recall from the video that SELECT is used to choose the fields that will be included in the result set, while FROM is used to pick the table in which the fields are listed.

Feel free to explore books in the exercise. Let's zoom in on this table in the database schema to see the fields and data types it contains.

Your task in this exercise is to practice selecting fields from books.

**Instructions:**

- Use SQL to return a result set of all book titles included in the books table.

```sql
-- Return all titles from the books table
SELECT *
FROM books
```

- Select both the title and author fields from books.

```sql
-- Select title and author from the books table
SELECT title, author
FROM books;
```

- Select all fields from the books table

```sql
-- Select all fields from the books table
SELECT *
FROM books;
```

# Writing queries

**Personal notes:**

**Aliasing*

It can be beneficial to rename columns in our result set, either for clarity or brevity. We can achieve this using aliasing. For example, if we want to select the name and hiring year for each record in the employees' table, we could alias the name column as "first_name" in the query by adding the AS keyword to indicate an alias for "first_name" after selecting the name field:

```sql
SELECT name AS first_name, year_hired
FROM employees;
```

Aliasing applies only to the result of that specific query; the field name in the employees' table itself remains as "name" and is not altered.

**Selecting Distinct Records*

To obtain a list of unique years without repeated values, we can add the DISTINCT keyword before the field name in the SELECT statement:

```sql
SELECT DISTINCT year_hired
FROM employees;
```

**Distinct with Multiple Fields*

It is possible to return unique combinations of multiple field values by listing several fields after the DISTINCT keyword:

```sql
SELECT DISTINCT dept_id, year_hired
FROM employees;
```

**Views*

Let's discuss how to save SQL result sets. In SQL, a view refers to a table that is the result of a saved SELECT SQL statement. Views are considered virtual tables, meaning the data they contain is generally not stored in the database. Instead, it is the query code that is stored for future use. A benefit of this is that whenever the view is accessed, it automatically updates the query results to consider any updates in the underlying database.

To create a view, we add a line of code before the SELECT statement. CREATE VIEW, then the name we'd like for the new view, followed by the AS keyword to assign the query results to the new view name:

```sql
CREATE VIEW employee_hire_years AS
SELECT id, name, year_hired
FROM employees;
```

There is no result set when creating a view. Once the view is created, however, we can query it just like we would a regular table, selecting FROM with the name of the created view:

```sql
SELECT id, name
FROM employee_hire_years;
```

**Exercises:**

**Making queries DISTINCT**

You've learned that the DISTINCT keyword can be used to return unique values in a field. In this exercise, you'll use this understanding to find out more about the books table!

There are 350 books in the books table, representing all of the books that our local library has available for checkout. But how many different authors are represented in these 350 books? The answer is surely less than 350. For example, J.K. Rowling wrote all seven Harry Potter books, so if our library has all Harry Potter books, seven books will be written by J.K Rowling. There are likely many more repeat authors!

**Instructions:**

- Completed task after requesting a hint or solution
Write SQL code that returns a result set with just one column listing the unique authors in the books table.

```sql
-- Select unique authors from the books table
SELECT DISTINCT author
FROM books
```

- Update the code to return the unique author and genre combinations in the books table.

```sql
-- Select unique authors and genre combinations from the books table
SELECT DISTINCT author, genre
FROM books;
```

**Aliasing**

While the default column names in a SQL result set come from the fields they are created from, you've learned that aliasing can be used to rename these result set columns. This can be helpful for clarifying the intent or contents of the column.

Your task in this exercise is to incorporate an alias into one of the SQL queries that you worked with in the previous exercise!

**Instructions:**

- Add an alias to the SQL query to rename the author column to unique_author in the result set.

```sql
-- Alias author so that it becomes unique_author
SELECT DISTINCT author AS unique_author
FROM books;
```

**VIEWing your query**

You've worked hard to create the below SQL query:


    SELECT DISTINCT author AS unique_author
    FROM books;

What if you'd like to be able to refer to it later, or allow others to access and use the results? The best way to do this is by creating a view. Recall that a view is a virtual table: it's very similar to a real table, but rather than the data itself being stored, the query code is stored for later use.

**Instructions:**

- Add a single line of code that saves the results of the written query as a view called library_authors.

```sql
-- Save the results of this query as a view called library_authors
CREATE VIEW library_authors AS
SELECT DISTINCT author AS unique_author
FROM books;
```

- Check that the view was created by selecting all columns from library_authors.

```sql
-- Your code to create the view:
CREATE VIEW library_authors AS
SELECT DISTINCT author AS unique_author
FROM books;

-- Select all columns from library_authors
SELECT *
FROM library_authors;
```


# SQL flavors

**Personal notes:**

SQL has several versions or different types. Some are free, while others have customer support and are designed to complement major databases like Microsoft's SQL Server or Oracle Database, which are used by many companies. All types of SQL are used with table-based relational databases, and the vast majority of keywords are shared among them. All types of SQL must follow universal standards defined by the International Organization for Standardization (ISO) and the American National Standards Institute (ANSI).

**Two Popular SQL Flavors*

PostgreSQL is a free and open-source relational database system that was originally created at the University of California and was sponsored by the renowned Defense Advanced Research Projects Agency (DARPA) in the U.S. The name PostgreSQL is used to refer to both the database system itself and the SQL flavor used with it.

SQL Server is also a relational database system that comes in free and enterprise versions. It was created by Microsoft, making it well-suited to work with other Microsoft products. T-SQL is Microsoft's proprietary SQL type used with SQL Server databases.

**Comparing PostgreSQL and SQL Server*

Think of SQL types as dialects of the same language. Here's an example of a slight difference between SQL Server and PostgreSQL: when we want to limit the number of returned records, we use the LIMIT keyword in PostgreSQL. Here, we limit the number of selected employee names and IDs to only the first two records.

PostgreSQL:
```sql
SELECT id, name
FROM employees
LIMIT 2;
```

The exact same results are achieved in SQL Server using the TOP keyword instead of LIMIT:

SQL Server:
```sql
SELECT TOP(2) id, name
FROM employees;
```

**Exercises:**

**Limiting results**

Let's take a look at a few of the genres represented in our library's books.

Recall that limiting results is useful when testing code since result sets can have thousands of results! Queries are often written with a LIMIT of just a few records to test out code before selecting thousands of results from the database.

Let's practice with LIMIT!

**Instructions:**

- Using PostgreSQL, select the genre field from the books table; limit the number of results to 10.

```sql
-- Select the first 10 genres from books using PostgreSQL
SELECT genre
FROM books
LIMIT 10;
```

**Translating between flavors**

In the previous exercise, you wrote the following code using PostgreSQL:

    SELECT genre
    FROM books
    LIMIT 10;

The database in this course is a PostgreSQL database, so you won't be able to run SQL Server code in any of the exercises. What if you did want to update the above query to work with SQL Server, though? How would you do that?

        "Replace FROM with TABLE"
