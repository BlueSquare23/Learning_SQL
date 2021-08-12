# Learning SQL - pt1 Introduction to SQL

Notes taken from this freeCodeCamp video.

* [FreeCodeCamp Video](youtube.com/watch?v=HXV3zeQKqGY)

## What is a Database?

Databases come in two forms. Relational databases (SQL) and Non-Relational
Databases (Non-SQL). Relational databases run on software platforms called
RDBMS's (Relational Database Management Systems). MySQL and MariaDB are two
examples of RDBMS platforms. Non-Relational databases are just data stored in
another format. For example, if a dataset is stored in JSON files or key value
pairs then its best managed programmatically or through a non-relational
database management system.

Relational databases store information in objects called tables. Tables are
composed of rows (horizontal) and columns (vertical). The following is an
example of a table.

```
ID	Name	Major
1	Katie	Biology
2	Sam	Electronics
3	Nick	IT
4	Alex	Pharm
5	Sam 	RocknRoll
6	John 	Art
7	Isaac	Electronics
```

#### Primary Keys

In the above table the 'ID' is considered the primary key. It is an identifier
that is always going to be unique for each row in the table. So in the above
case, even through there are two Sam's we can uniquely identify them by their
primary key. The primary key will be unique even if all other attributes of the
row are identical.

Primary keys can either be surrogate keys or natural keys. Surrogate keys are
arbitrary and have no relation to any real world value. Natural keys are the
opposite and have a direct real world relation to the entry in the row. An
example of a surrogate key is an arbitrary employee ID number hash generated
when the entry is created in the database. An example of a natural key is a
social security number or a unique Email address.

#### Foreign Keys

Another type of key found in relational databases is the foreign key. Foreign
keys are the unique primary keys of another table in the same database. Below
is an example of a foreign keys usage. 

```
Employee table:

ID	Name	Sex	Branch ID
100	Jan	F	1
101	Mike	M	2
102	Andy	M	3
103	Pam	F	2

Branch table:

Branch ID 	Name		Manager ID
1		Corporate	96
2		Scranton	101
3		Stamford	98
```

In the above example the Branch ID and Manager ID columns are examples of
foreign keys. The Branch ID references the primary key of the Branch table and
the Manager ID column references the primary keys in the Employee table.

#### Composite Keys

Sometimes primary keys can be whats called Composite Keys. In this case the
primary key is composed of two columns of information. This is needed in cases
where neither key alone could uniquely identify the row.

```
Branch	Supplier	Type
2	HammerMill	Paper
2	Uni-Ball	Office Supplies
3	HammerMill	Paper
```

In the above example both Branch and Supplier columns compose the primary
'composite' key. 

## What is SQL?

SQL stands for Structured Query Language. Its used for communicating with
relational database management systems. It can be used to do CRUD (Create,
Read, Update, Delete) operations on the database. It can also be used to manage
and do administrative actions on the database. 

## Using MySQL

#### Creating MySQL User

* Username: bluesql
* Password: HZSv############

```
mysql> CREATE USER 'bluesql'@'localhost' IDENTIFIED BY 'HZSv############';
Query OK, 0 rows affected (0.18 sec)

mysql> GRANT ALL ON *.* TO 'bluesql'@'localhost';
Query OK, 0 rows affected (0.14 sec)
```

#### Creating MySQL Database

* DB Name: learning_sql

```
mysql> create database learning_sql;
```

```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
...
| learning_sql       |
+--------------------+
```

#### Accessing the DB via the MySQL CLI

```
blue@server:$~> mysql -u bluesql -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 8.0.25-0ubuntu0.20.04.1 (Ubuntu)
...
mysql> use learning_sql;
Database changed
mysql> show tables;
Empty set (0.00 sec)
```

## SQL Syntax

#### Formatting Notes and Conventions

Before we do anything in SQL we need to be aware of some common SQL formatting
conventions. By tradition, SQL operator words such as, 'SELECT', 'INSERT',
'DROP', etc. are capitalized. They don't have to be. MySQL will understand all
lowercase. But this choice is made to help distinguish the SQL parts from the
data parts.

Two things that are not convention are the use of semicolons at the end of
statements; and the use of plain ascii 'single quotes'. SQL does not like
double quotes or stupid special unicode ‘single quotes’!

#### Basic SQL Datatypes

```
INT		-- Whole Numbers
DECIMAL(M,N)	-- Decimal Numbers - Exact Values
VARCHAR(1)	-- String of text of length 1
BLOB		-- Binary Large Object, Stores large data
DATE		-- 'YYYY-MM-DD'
TIMESTAMP	-- 'YYYY-MM-DD HH:MM:SS' 

	and more ...
```

#### Our Data

We've going to be entering the Student Major's data below into our database.

| ID |	Name   |   Major	|
|----|---------|----------------|
| 1  |	Katie  |   Biology 	|
| 2  |	Sam    |   Electronics  |
| 3  |	Nick   |   IT 		|
| 4  |	Alex   |   Pharm 	|
| 5  |	Sam    |   RocknRoll 	|
| 6  |	John   |   Art  	|

### Structure

#### Creating a table

To do that we have to first create a table. We'll create a table called student
and define the three columns.

```SQL
CREATE TABLE student (
	student_id INT PRIMARY KEY,
	name VARCHAR(20),
	major VARCHAR(20)
);
```

And it created our table!

```
mysql> show tables;
+------------------------+
| Tables_in_learning_sql |
+------------------------+
| student                |
+------------------------+
1 row in set (0.01 sec)
```

Let's get some more information about the table.

```
mysql> DESCRIBE student;
+------------+-------------+------+-----+---------+-------+
| Field      | Type        | Null | Key | Default | Extra |
+------------+-------------+------+-----+---------+-------+
| student_id | int         | NO   | PRI | NULL    |       |
| name       | varchar(20) | YES  |     | NULL    |       |
| major      | varchar(20) | YES  |     | NULL    |       |
+------------+-------------+------+-----+---------+-------+
3 rows in set (0.01 sec)
```

We can see the datatype and primary key information we entered there.

#### Dropping a table 

To remove a table and start over use the 'DROP TABLE' statement.

```
mysql> DROP TABLE student;
Query OK, 0 rows affected (0.60 sec)

mysql> show tables;
Empty set (0.01 sec)
```

#### Modifying a Table

Let's say we want to add a column to the database. We also want to keep track
of the students GPA. We can do so with the 'ALTER TABLE' statement.

```SQL
ALTER TABLE student ADD gpa DECIMAL(3, 2);
```

GPA's are normally setup on a 4.0 scale, so we want a decimal number with three
total digits, two of the digits after the decimal point (For example, 3.85 GPA).

We can use 'ALTER' in conjunction with the 'DROP' statement to delete a
particular column.

```SQL
ALTER TABLE student DROP COLUMN gpa;
```

### Content

#### Inserting Data

We'll use the 'INSERT' verb to put data into our table.

```SQL
INSERT INTO student VALUES(1, 'Katie', 'Biology');
```

Ordering of the columns matters when we're inserting data. First we enter the
student_id which is 1 and the primary key. Next we enter the student name and
finally the student major as per the order they were defined in when we created
the table.

When we execute the 'INSERT' command on the MySQL CLI the following is
reported.

```
mysql> INSERT INTO student VALUES(1, 'Katie', 'Biology');
Query OK, 1 row affected (0.13 sec)
```

One row has been affected because that is the data we just inserted.

We can add more students to our table as well.

```
INSERT INTO student VALUES(2, 'Sam', 'Electronics');
INSERT INTO student VALUES(3, 'Nick', 'IT');
```

#### Select Table Contents

Now we can look at the table contents with the 'SELECT' command and we'll see
the information entered.

```
mysql> SELECT * FROM student;
+------------+-------+-------------+
| student_id | name  | major       |
+------------+-------+-------------+
|          1 | Katie | Biology     |
|          2 | Sam   | Electronics |
|          3 | Nick  | IT          |
+------------+-------+-------------+

```

#### Inserting Partial Data

But what happens when we get to John? He doesn't have a real major so we can't
enter anything in for that column. In this case we can specify in the 'INSERT'
statement what vaules we are putting in place.

```SQL
INSERT INTO student(student_id, name) VALUES(6, 'John');
```

Now when we 'SELECT' everything from the table we'll see John's major is 'NULL'.

```
+------------+-------+-------------+
| student_id | name  | major       |
+------------+-------+-------------+
|          1 | Katie | Biology     |
|          2 | Sam   | Electronics |
|          3 | Nick  | IT          |
|          6 | John  | NULL        |
+------------+-------+-------------+
```

### Constraints

There are a number of constraints in SQL that can be used to modify the way
data can be entered into a column of our table.

#### Not Null

When creating a table it's possible to specify that a certain column is not
allowed to be entered as a NULL. For example, with our data we can't have an
empty student name. Maybe a student hasn't picked a major yet. But at our
university you have to have a name.

We can specify this when creating our table with the 'NOT NULL' keyword.

```SQL
CREATE TABLE student (
	student_id INT PRIMARY KEY,
	name VARCHAR(20) NOT NULL,
	major VARCHAR(20)
);
```

If we try to insert a NULL now MySQL will not allow it.

```
mysql> INSERT INTO student VALUES(4, NULL, 'Hax');
ERROR 1048 (23000): Column 'name' cannot be null
```

#### Unique

Similar to Primary Keys, the 'UNIQUE' keyword specifies that a field has to be
unique. We can use the 'UNIQUE' keyword when creating a table to specify that
an attribute cannot be entered twice. So let's say we have a study group that
we want to only be composed of people with unique majors.

```SQL
CREATE TABLE student (
        student_id INT PRIMARY KEY,
        name VARCHAR(20),
        major VARCHAR(20) UNIQUE
);
```

Now if a new person tries to join our study group with a duplicate major
they're rejected.

```
mysql> INSERT INTO student VALUES(2, 'Sam', 'Electronics');
Query OK, 1 row affected (0.26 sec)

mysql> INSERT INTO student VALUES(8, 'Isaac', 'Electronics');
ERROR 1062 (23000): Duplicate entry 'Electronics' for key 'student.major'
```

We can see now how a private key is composed of these two attributes. A private
key is just a column that is both 'UNIQUE' and 'NOT NULL'.

#### Default 

We can also specify a default value if we want a column to be auto filled in
the event it is left unspecified. We do so with the 'DEFAULT' keyword.

```SQL
CREATE TABLE student (
        student_id INT PRIMARY KEY,
        name VARCHAR(20),
        major VARCHAR(20) DEFAULT 'undecided'
);
```

Now if we enter a student without a major it will set the major to 'undecided'.

```
mysql> INSERT INTO student(student_id, name) VALUES(6, 'John');
Query OK, 1 row affected (0.14 sec)

mysql> SELECT * FROM student;
+------------+------+-----------+
| student_id | name | major     |
+------------+------+-----------+
|          6 | John | undecided |
+------------+------+-----------+
1 row in set (0.00 sec)

```

If we use 'DEFAULT' in conjunction with 'NOT NULL' the table can never be NULL
even if you try to force a NULL.

```
mysql> CREATE TABLE student ( 
    ->         student_id INT PRIMARY KEY,
    ->         name VARCHAR(20), 
    ->         major VARCHAR(20) DEFAULT 'undecided' NOT NULL
    -> );
Query OK, 0 rows affected (1.38 sec)

mysql> DESCRIBE student;
+------------+-------------+------+-----+-----------+-------+
| Field      | Type        | Null | Key | Default   | Extra |
+------------+-------------+------+-----+-----------+-------+
| student_id | int         | NO   | PRI | NULL      |       |
| name       | varchar(20) | YES  |     | NULL      |       |
| major      | varchar(20) | NO   |     | undecided |       |
+------------+-------------+------+-----+-----------+-------+
3 rows in set (0.00 sec)

mysql> INSERT INTO student VALUES(2, 'Sam', NULL);
ERROR 1048 (23000): Column 'major' cannot be null
mysql> INSERT INTO student(student_id, name) VALUES(2, 'Sam');
Query OK, 1 row affected (0.12 sec)

mysql> SELECT * FROM student;
+------------+------+-----------+
| student_id | name | major     |
+------------+------+-----------+
|          2 | Sam  | undecided |
+------------+------+-----------+
1 row in set (0.00 sec)
```

#### Auto Increment Primary Key

We've been manually entering the primary key values this whole time. However,
SQL also has the ability to auto increment a value for a particular column.

```SQL
CREATE TABLE student (
        student_id INT PRIMARY KEY AUTO_INCREMENT,
        name VARCHAR(20),
        major VARCHAR(20)
);
```

Then we can just leave off the student_id column when entering our data.

```SQL
INSERT INTO student(name, major) VALUES('Katie', 'Biology');
INSERT INTO student(name, major) VALUES('Sam', 'Electronics');
INSERT INTO student(name, major) VALUES('John', NULL);
```

And our data is automatically incremented!

```
mysql> SELECT * FROM student;
+------------+-------+-------------+
| student_id | name  | major       |
+------------+-------+-------------+
|          1 | Katie | Biology     |
|          2 | Sam   | Electronics |
|          3 | John  | NULL        |
+------------+-------+-------------+
```

#### Updating Table Contents

Let's say we have the following populated database table.

```
mysql> SELECT * FROM student;
+------------+-------+-------------+
| student_id | name  | major       |
+------------+-------+-------------+
|          1 | Katie | Biology     |
|          2 | Sam   | Electronics |
|          3 | Nick  | IT          |
|          4 | Alex  | Pharm       |
|          5 | John  | Art         |
|          6 | Isaac | Electronics |
+------------+-------+-------------+
```

There is no major called 'Electronics.' Technically the major is 'Electrical
Engineering.' Let's update the database to convert the major's name from
'Electronics' to 'EE' for Electrical Engineering. 

We can do so with the 'UPDATE' and 'SET' and 'WHERE' verbs.

```SQL
UPDATE student
SET major = 'EE'
WHERE major = 'Electronics';
```

That SQL statement uses the 'WHERE' statement as a conditional. In this case
the condition is if the major is already set to 'Electronics.'

When run we get,

`Rows matched: 2  Changed: 2  Warnings: 0`

And the table has been updated. The 'Electronics' majors have been updated to
be called 'EE' majors instead.

```
mysql> SELECT * FROM student;
+------------+-------+---------+
| student_id | name  | major   |
+------------+-------+---------+
|          1 | Katie | Biology |
|          2 | Sam   | EE      |
|          3 | Nick  | IT      |
|          4 | Alex  | Pharm   |
|          5 | John  | Art     |
|          6 | Isaac | EE      |
+------------+-------+---------+
```

We can update other parts of the table this way as well. Say Katie transitions
and wants to go by Chad now. We can update the name cell by addressing Katie's
unique student_id private key.

```SQL
UPDATE student
SET name = 'Chad'
WHERE student_id = 1;
```

And afterwards,

```
mysql> SELECT * FROM student;
+------------+-------+---------+
| student_id | name  | major   |
+------------+-------+---------+
|          1 | Chad  | Biology |
|          2 | Sam   | EE      |
|          3 | Nick  | IT      |
|          4 | Alex  | Pharm   |
|          5 | John  | Art     |
|          6 | Isaac | EE      |
+------------+-------+---------+
```

Likewise, we can create more complex 'WHERE' statements with the 'OR' and 'AND'
operators to have more control over the 'UPDATE' statement.

For Example,

```SQL
UPDATE student
SET major = 'Waste of Money'
WHERE major = 'Art' OR major = 'Biology';
```

And after,

```
mysql> SELECT * FROM student;
+------------+-------+----------------+
| student_id | name  | major          |
+------------+-------+----------------+
|          1 | Chad  | Waste of Money |
|          2 | Sam   | EE             |
|          3 | Nick  | IT             |
|          4 | Alex  | Pharm          |
|          5 | John  | Waste of Money |
|          6 | Isaac | EE             |
+------------+-------+----------------+
```

#### SQL Comparison Ops

```
	=  : equals
	<> : not equals
	>  : greater than
	<  : less than
	>= : greater than or equal to
	<= : less than or equal to
```

#### Deleting Table Contents

We can use the following 'DELETE' statement to wipe our table contents without
dropping our table schema. 

`DELETE FROM student;`

However, that alone is not very useful. Instead, its best to combine the
'DELETE' statement with a 'WHERE' statement to better narrow down what you're
removing.

For example, say our university is facing hard times and so it has to cut its
'Waste of Money' and 'Electrical Engineering' programs. As such those students
will be expelled and our rosters purged.

```SQL
DELETE FROM student
WHERE major = 'Waste of Money' OR major = 'EE';
```

Now only our best students remain!

```
mysql> SELECT * FROM student;
+------------+------+-------+
| student_id | name | major |
+------------+------+-------+
|          3 | Nick | IT    |
|          4 | Alex | Pharm |
+------------+------+-------+
```

#### Basic Queries

A 'query' is just a request for information. The 'SELECT' statement we saw
earlier is a simple example of an SQL query. 

`SELECT * FROM student;`

The 'SELECT' keyword tells the RDBMS we're requesting info. In the example
above '*' is a glob meaning get every column. However, we can specify what
information we want.

```SQL
SELECT name, major 
FROM student;
```

```
+-------+-------------+
| name  | major       |
+-------+-------------+
| Katie | Biology     |
| Sam   | Electronics |
| Alex  | Pharm       |
| John  | Art         |
| Isaac | Electronics |
+-------+-------------+
```

In the above example we're getting name and major but we're not getting the
primary key, student_id.

Its not uncommon to prepend the table name to the columns in the select
statement. For example,

```SQL
SELECT student.name, student.major 
FROM student;
```

This helps to keep the SQL query specific when there are more tables involved
so be aware of this notation.

#### Order By

We can add some sortation to our above query using an 'ORDER BY' statement. For
example,

```SQL
SELECT student.name, student.major 
FROM student
ORDER BY name DESC;
```

The above SQL will order the output by the name column in DESCending
alphabetical order.

```
+-------+-------------+
| name  | major       |
+-------+-------------+
| Sam   | Electronics |
| Katie | Biology     |
| John  | Art         |
| Isaac | Electronics |
| Alex  | Pharm       |
+-------+-------------+
```

We can also order by student_id even though we never selected that column.

```SQL
SELECT student.name, student.major 
FROM student
ORDER BY student_id DESC;
```

```
+-------+-------------+
| name  | major       |
+-------+-------------+
| Isaac | Electronics |
| John  | Art         |
| Alex  | Pharm       |
| Sam   | Electronics |
| Katie | Biology     |
+-------+-------------+
```

#### Limit

We can also limit the number of results that are returned with the 'LIMIT'
statement. Think of 'LIMIT' as equivalent to 'head' command in the UNIX world.

For Example,

```SQL
SELECT student.name, student.major 
FROM student
ORDER BY student_id DESC
LIMIT 2;
```

```
+-------+-------------+
| name  | major       |
+-------+-------------+
| Isaac | Electronics |
| John  | Art         |
+-------+-------------+
```

We get back the top two students ordered by descending student_id.

#### Where

Once again, we can use the 'WHERE' keyword to help narrow down the rows we want
returned in our queries.

```SQL
SELECT student.name, student.major 
FROM student
WHERE major = 'Electronics';
```

```
+-------+-------------+
| name  | major       |
+-------+-------------+
| Sam   | Electronics |
| Isaac | Electronics |
+-------+-------------+
```

Let's craft a more complex 'WHERE' statement.

Lets get students with ID's greater than or equal to 9 who are not named Isaac.

```SQL
SELECT *
FROM student
WHERE student_id >= 9 AND name <> 'Isaac';
```

```
+------------+-------+-------------+
| student_id | name  | major       |
+------------+-------+-------------+
|          9 | Alex  | Pharm       |
|         10 | John  | Art         |
+------------+-------+-------------+
```

#### In 

We can use the 'IN' keyword in conjunction with 'WHERE' statements to craft
even tighter queries.

```SQL
SELECT name, major
FROM student
WHERE name IN ('Alex', 'Sam', 'Katie');
```

In the above statement, 'IN' is used with an array of comma separated names in
parentheses.

```
+-------+-------------+
| name  | major       |
+-------+-------------+
| Katie | Biology     |
| Sam   | Electronics |
| Alex  | Pharm       |
+-------+-------------+
```

Continued with Company DB in pt2...

Notes taken from this freeCodeCamp video.

* [FreeCodeCamp Video](youtube.com/watch?v=HXV3zeQKqGY)
