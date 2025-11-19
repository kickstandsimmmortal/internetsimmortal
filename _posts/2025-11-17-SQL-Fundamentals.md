--- 
title: SQL Fundamentals
date: 2025-11-17
categories: [SQL]
tags: [homelab, SQL] #tags should always be lowercase
---

I will just be documenting some fundamentals for SQL databases here. I will be creating a database for an inventory of books and organizing it into a table.

## Database and Table Statements

I organized my database table into the following columns:

`book_id` - The number assigned to each book and how we will interact with each book in the database

`book_name` - The name of the book

`publication_date` - The date the book was published

---

First I will launch `mysql` as root

`mysql -u root -p`

`-u root` tells `mysql` that I will be using the root account and `-p` is telling it that I want to provide a password

I then create a database using the following:

```SQL
create database <database_name>;
```

To show databases: 

```SQL
show databases;
```

To interact with a specific database: 

```SQL
use database <database_name>;
```

To create a table:

```SQL
create table <table_name> (
	example_column1 data_type,
	example_column2 data_type,
	example_column3 data_type,
);
```

In my case, a database of books, I entered the following:

```SQL
create table book_inventory (
	book_id INT AUTO_INCREMENT PRIMARY KEY,
	book_name VARCHAR(255) NOT NULL,
	publication_date DATE,
);
```

This statement creates a table with three columns. `book_id`, `book_name` and `publication_date`.

- `book_id` is an `INT` (integer) as it should only ever be a number, 

- `AUTO_INCREMENT` means the first book would be assigned `book_id` 1, the second `book_id` 2, etc, etc, as I add more books. 

- `PRIMARY KEY` sets `book_id` as the way we can identify a book record in my table. A primary must be present in a table.

- `book_name` has the data type `VARCHAR(255)`, which means it can use variable character with a character limit of 255. `NOT NULL` means this cannot be empty.

- `publication_date` includes the data type `DATE` which sets the date.

Once you've `USE`'d a database, you can show the tables within it.

```SQL
show tables;
```

And you can view what's in a table with the following:

```SQL
describe <table_name>;
```

If I need to change the dataset at any point, I can do this using the `ALTER` statement. In this example, I've decided I want to have a column in the book inventory that has the page count for each book:

```SQL
alter table book_inventory
add page_count INT;
```

This adds another row to the table called `page_count` and uses `INT` to ensure this is always a number.

To remove a table:

```SQL
drop table <table_name>;
```

To remove a database;

```SQL
drop database <database_name>;
```

## CRUD Operations

CRUD stands for **C**reate, **R**ead, **U**pdate, and **D**elete, which are considered basic operations in any data management system.

**Create Operation (INSERT)**

The **Create** operation creates new records in a table. This can be achieved by using the statement `INSERT INTO`. In my example, I used the following:

```SQL
INSERT INTO book_inventory (book_id, book_name, publication_date, page_count)
values (1, "Android Security Internals", "2014-10-14", 500);
```

You can see the pattern between the two lines. The `values` data must be organized into the same order as the `INSERT INTO` line.

**Read Operation (SELECT)**

The **Read** operation allows you to read or retrieve information from the table. We can do this with a column or all columns from a table with the `SELECT` statement:

```SQL
SELECT * FROM books;
```

The `*` symbol indicates to retrieve all columns from a table.

If I wanted to just grab the book id and book name I could use the following:

```SQL
SELECT book_id, book_name FROM book_inventory;
```

And it would only return those two columns.

**Update Operation (UPDATE)**

The **Update** operation modifies an existing record within a table

```SQL
UPDATE book_inventory
	SET page_count = 600
	WHERE book_id = 1;
```

This will update the `page_count` of the first book in the table from it's previously set count of 500, to 600.

**Delete Operation (DELETE)**

The **Delete** operation removes records from a table

```SQL
DELETE FROM book_inventory WHERE book_id = 1;
```

## Clauses

A clause is a part of a statement that specifies the criteria of the data being manipulated. They help define the type of data and how it should be retrieved or sorted.

The `FROM` and `WHERE` statements from earlier are examples of clauses.

Some more examples of Clauses:

- `DISTINCT`: Used to avoid duplicate records when doing a query, returns only unique values
	
Example: 

```SQL
SELECT DISTINCT book_name FROM book_inventory;
```

- `GROUP BY`: Aggregates data from multiple records and groups the results in columns.

Example: 

```SQL
SELECT book_name, COUNT(*)
    FROM books
	GROUP BY book_name;
```

>In this example, the records of the book_inventory table are regrouped by the result of 	the `COUNT` function. It will list each `book_name` and the `COUNT` of appearances in 	the 
table.		

- `ORDER BY`: Can be used to sort the records returned by a query in ascending or descending order, using the `ASC` or `DESC` functions to accomplish that.

Example: 

```SQL
SELECT *
	FROM books
	ORDER BY published_date ASC;
```

- `HAVING`: Used with other clauses to filter groups or results of records based on a condition. It evaluates the condition to `TRUE` or `FALSE`.

Example: 

```SQL
SELECT book_name, COUNT(*)
	FROM books
	GROUP BY book_name
	HAVING book_name LIKE '%HACK&';
```
In this example, the query would only return books with the names that contain the word "hack" and the proper count, which in this case is any count.

## Operators

**Logical Operators**

These operators test the truth of a condition and return a Boolean value or `TRUE` or `FALSE`

**LIKE Operator**

The `LIKE` operator is commonly used in conjuction with clauses like `WHERE` in order to filter for specific patterns within a column.

```SQL
SELECT *
FROM books
WHERE description LIKE "%guide%";
```

This query would return a list of books within the "books" table that contained the word "guide" in the description

**AND operator**

The `AND` operator uses multiple conditions with a query and returns `TRUE` if all of them are true.

```SQL
SELECT *
FROM books
WHERE category = "Offensive Security" AND name = "Bug Bounty Bootcamp";
```

This would return results with the two defined search parameters and only those results.

**OR Operator**

The `OR` operator combines multiple conditions within a search query and returns `TRUE` if at least one of these conditions is true.

```SQL
SELECT *
FROM books
WHERE name LIKE "%Android%" OR name LIKE "%iOS%";
```

This query would return books whose names include either Android or IOS.

**NOT Operator**

The `NOT` operator reverses the value of a Boolean operator, allowing us to exclude a specific condition.

```SQL
SELECT *
FROM books
WHERE NOT description LIKE "%guide%";
```

This would return results where the description does *NOT* contain the word guide.

**Between Operator**

The `BETWEEN` operator allows us to test if a value exists within a defined range

```SQL
SELECT *
FROM books
WHERE book_id BETWEEN 2 AND 4;
```

This query would return books whose `book_id` is between 2 and 4.

**Comparison Operators**

The `=`(Equal) operator compares two expressions and determines if they are equal, or it can check if a value matches another one in a specific column.

```SQL
SELECT *
FROM books
WHERE name = "Designing Secure Software";
```

This query would return the book with this exact name

---

The `!=`(not equal) operator compares expressions and tests if they are not equal. It also checks if a value differs from one within a column.

```SQL
SELECT *
FROM books
WHERE category != "Offensive Security";
```

This query would return all books except those whose category is "Offensive Security"

---

The `<`(less than) operator compares if the expression with a given value is lesser than the provided one.

```SQL
SELECT *
FROM books
WHERE published_date < "2020-01-01";
```

This query would return books that were published before January 1, 2020

---

The `>`(greater than) operator compares if the expression with a given value is greater than the provided one.

```SQL
SELECT *
FROM books
WHERE published_date > "2020-01-01";
```

This query would return only books published after January 1, 2020

--- 

The `<=`(less than or equal) operator compares if the expression with a given value is less than or equal to the provided one.

On the other hand, the `>=`(greater than or equal) operator compares if the expression with a given value is greater than or equal to the provided one.

```SQL
SELECT *
FROM books
WHERE published_date <= "2021-11-15";
```

Shows books published on or before November 15, 2021

```SQL
SELECT *
FROM books
WHERE published_date >= "2021-11-02";
```

Shows books that were published on or after November 2, 2021

## Functions

Functions can help us streamline queries and operations and manipulate data.

**String Functions**

String functions perform operations on a string, returning a value associated with it

**CONCAT() Function**

This function is used to add two or more strings together. It is useful to combine text from different columns

```SQL
SELECT CONCAT(name, " is a type of ", category " book.") AS book_info FROM books;
```

This query concatenates the `name` and `category` columns from the `books` table into a single one named `book_info`.

---

**GROUP_CONCAT() Function**

This function can help us to concatenate data from multiple rows into one field.

```SQL
SELECT category, GROUP_CONCAT(name SEPARATOR ", ") AS books
FROM books
GROUP by category;
```

This query groups the books by category and concatenates the titles of books within each category into a single string

---

**SUBSTRING() Function**

This function will retrieve a substring from a string within a query, starting at a determind position. The length of the substring can also be specified.

```SQL
SELECT SUBSTRINTG(published_date, 1, 4) AS published_year FROM books;
```

This query extracts the first four characters from the `published_date` column and stores them in the `published_year` column.

---

**LENGTH() Function**

This function returns the number of characters in a string. This includes spaces and punctuation.

```SQL
SELECT LENGTH(name) AS name_length FROM books;
```

This query calculates the length of the string within the `name` column and stores it in a column named `name_length`

---

**Aggregrate Functions**

These functions aggregate the value of multiple rows within one specified criteria in the query. It can combine multiple values into one result

---

**COUNT() Function**

This function returns the number of records within an expression

```SQL
SELECT COUNT(*) AS total_books FROM books;
```

This query counts the total number of rows in the `books` table.

---

**SUM() Function**

This function sums all values (not NULL) of a determind column

```SQL
SELECT SUM(price) AS total_price FROM books;
```

This query calculates the total sum of the `price` column. The result provides the aggregate price of all books in the column `total_price`

---

**MAX() Function**

This function calculates the maximum value within a provided column in an expression

```SQL
SELECT MAX(published_date) AS latest_book FROM books;
```

This query retrieves the latest publication date from the `books` table. The result would be stored in the `latest_book` column.

---

**MIN() Function**

This function calculates the minimum value within a provided column in an expression

```SQL
SELECT MIN(published_date) AS earliest_book FROM books;
```

This query retrieves the earliest publication datae from the `books` table and stores it in the `earliest_book` column.


