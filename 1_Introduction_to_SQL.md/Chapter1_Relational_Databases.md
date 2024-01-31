# Introduction to SQL

**Course description:**

Much of the world's raw data—from electronic medical records to customer transaction histories—lives in organized collections of tables called relational databases. Being able to wrangle and extract data from these databases using SQL is an essential skill within the data industry and in increasing demand.

In this two-hour introduction to SQL, you'll get to know the theory and the practice through bite-sized videos and interactive exercises where you can put your new-found skills to the test.

SQL is an essential language for building and maintaining relational databases, which opens the door to a range of careers in the data industry and beyond. You’ll start this course by covering data organization, tables, and best practices for database construction.

The second half of this course looks at creating SQL queries for selecting data that you need from your database. You’ll have the chance to practice your querying skills before moving on to customizing and saving your results.

PostgreSQL and SQL Server are two of the most popular SQL flavors. You’ll finish off this course by looking at the differences, benefits, and applications of each. By the end of the course you’ll have some hands-on experience in learning SQL and the grounding to start applying it on projects or continue your learning in a more specialized direction.

# Relational Databases

**Chapter description:**

Before writing any SQL queries, it’s important to understand the underlying data. In this chapter, we’ll discover the role of SQL in creating and querying relational databases. Using a database for a local library, we will explore database and table organization, data types and storage, and best practices for database construction.

# Databases 

**Personal notes:**

Databases are electronic systems designed to store and organize data efficiently. It is possible to configure a database containing information, as illustrated in the lesson's example, about clients, books, and transactions. This information is stored in objects called tables, with data organized in rows and columns. The lesson's database comprises a user table, a book table, and a checkout table.

A relational database establishes connections between data tables within the database. Through these relationships, we can draw conclusions about data stored in separate tables within the same database.

In contrast to data visualization tools like Excel, databases can store a significantly larger amount of data, and their storage is more secure due to encryption. The primary advantage of a database lies in its ability to allow multiple users to write queries simultaneously, collecting insights from the data. When a database is queried, the stored data remains unchanged; instead, the database information is accessed and presented based on the query instructions.

SQL - Structured Query Language, is the most widely used programming language for creating, querying, and updating relational databases.

**Exercises:**

**What are the advantages of databases?**

Which of the following are advantages of storing data in a database, rather than using traditional formats like spreadsheets?

        "All of the above"

**Data organization**

Understanding the organization of a database is an important first step when using SQL.

Take a look at the database below. Which of the following statements correctly describes its organization?

        "This is a relational database containing three tables: employees, job_levels, and departments."       

# Tables

**Personal notes:**

Tables contain related data on a specific subject within a database. They are organized in rows and columns, with rows commonly referred to as records and columns as fields. The fields in a table are constrained to those defined when the database was created, but the number of rows is unlimited.

Good table manners:

Table names should be in lowercase and cannot include spaces; underscores (_) are used instead. Ideally, a table name refers to a collective group, and it is acceptable to use plural names.

A record is a row in a table, containing data about an individual observation. A field is a column in a table, providing information about all observations in the table. As field names must be typed when querying a database with SQL, proper field nomenclature is crucial.

Field names should be in lowercase, without spaces, and in the singular form, referring to the information in that field for a single record. Two fields in a table cannot share the same name and should never have a name identical to the table in which they reside.

A unique identifier, sometimes called a "key," is a distinctive value that identifies a record, allowing it to be distinguished from other records in the same table. This value is often a number.


**Exercises:**

**Picking a unique ID**

A unique identifier is a value that distinguishes a record from others in the same table.

In the employees table, which fields do you believe is the most suitable choice for a unique identifier?

        'id'

**Setting the table in style**

Here are two versions of a table taken from a database.

Which table uses the correct naming format?

![Image 1](https://github.com/Caiobauab360/Data_Analyst_With_SQL_Course_Portfolio/assets/127256295/5ee9214e-e025-438d-8e1c-23ee63ecba2c)

# Data

**Personal notes:**

When creating a table, specifying a data type for each field is crucial. The chosen data type depends on the nature of the data the field will contain, as different data types are stored and processed differently, occupying varying amounts of storage space. Furthermore, specific operations may apply only to certain data types. Here are some commonly used data types:

VARCHAR: This is the most flexible data type and can store strings of varying lengths, from small to large – even up to tens of thousands of characters.

INT: A common integer data type capable of storing numbers ranging from just under two billion negative to just over two billion positive.

NUMERIC: This data type can store floating-point numbers with up to 38 digits, including those before and after the decimal point.

Schemas

Schemas are often referred to as the "blueprints" of a database. A schema illustrates the design of a database, showcasing included tables and any relationships among them. It also outlines the type of data each field can contain, providing a comprehensive overview of the database structure.

Database Storage

The data we encounter in a database table is physically stored on the hard drive of a server. Servers, centralized computers that perform services through network requests, handle tasks such as data access in our context. While any computer can serve as a server when configured to provide a service, servers are typically large and powerful machines equipped to manage high volumes of data requests. They play a crucial role not only in data access but also in accessing websites or files stored on the server.


**Exercises:**

**At your service**

Now that you know more about how data is stored, it's time to test those skills!

Select the statement about database storage that is false.

        "Servers are usually personal computers such as laptops."

**Finding data types**

Imagine that you are starting a new job and have just started getting to know your new employer's database. You know that it's important to know the data type—such as VARCHAR, INT, or NUMERIC—corresponding to each field in a table. Where could you find this information?

        "You can find this information by looking at a database schema."
