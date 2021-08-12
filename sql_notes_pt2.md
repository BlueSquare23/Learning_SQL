# Learning SQL - pt2 The Company Database

Notes taken from this freeCodeCamp video.

* [FreeCodeCamp Video](youtube.com/watch?v=HXV3zeQKqGY)

## A More Complex Database Schema 

Up until this point we've been using a single table database. We're now going
to switch to a much larger dataset. We'll be creating a hypothetical company
database. 

* Our New Dataset: [Company Database](https://www.mikedane.com/databases/sql/company-database.pdf)

Our company's database has the following tables.

* Employee
* Branch
* Client
* Works_With
* Branch Supplier

The PDF explains the relationships we're seeking to model here more clearly.
Essentially, there are a series of tables that are interrelated by Foreign and
Composite Keys. For example, the Employee table has two Foreign keys in it,
super_id and branch_id. These Foreign keys correspond to the Primary keys of a
subset of employees the supervisors table and the real Branch table
respectively.

## Creating A Company Database

* Employee Table:

```SQL
CREATE TABLE employee (
  emp_id INT PRIMARY KEY,
  first_name VARCHAR(40),
  last_name VARCHAR(40),
  birth_day DATE,
  sex VARCHAR(1),
  salary INT,
  super_id INT,
  branch_id INT
);
```

We can see all of the main columns defined there. The last two columns are
going to be foreign keys. However, they cant be set up as that yet because we
haven't created those other tables yet.

* Branch Table:

```SQL
CREATE TABLE branch (
  branch_id INT PRIMARY KEY,
  branch_name VARCHAR(40),
  mgr_id INT,
  mgr_start_date DATE,
  FOREIGN KEY(mgr_id) REFERENCES employee(emp_id) ON DELETE SET NULL
);
```

The branch table is interesting because we're able to setup our first foreign
key. As you can see we do this with the 'FOREIGN KEY(mgr_id)' statement and we
point it to the primary key of our employee table with the 'REFERENCES' verb.

* Adding Foreign Keys:

Now with the 'branch' table created we're able to create our second and third
foreign keys by altering the employee table.

```SQL
ALTER TABLE employee
ADD FOREIGN KEY(branch_id)
REFERENCES branch(branch_id)
ON DELETE SET NULL;

ALTER TABLE employee
ADD FOREIGN KEY(super_id)
REFERENCES employee(emp_id)
ON DELETE SET NULL;
```

Those two statements say, first alter the employee table and turn the
'branch_id' column into a foreign key that references the 'branch' table column
named 'branch_id' and then do the same thing for 'super_id' column, turning it
into a foreign key that references the 'emp_id' column of the employee table.

* Client Table:

```SQL
CREATE TABLE client (
  client_id INT PRIMARY KEY,
  client_name VARCHAR(40),
  branch_id INT,
  FOREIGN KEY(branch_id) REFERENCES branch(branch_id) ON DELETE SET NULL
);
```

With the client table we're able to setup our fourth and fifth foreign keys.
This 'FOREIGN KEY' defines the branch_id column into a foreign key that
references the 'branch_id' column of the branch table.

* Works_With Table:

```SQL
CREATE TABLE works_with (
  emp_id INT,
  client_id INT,
  total_sales INT,
  PRIMARY KEY(emp_id, client_id),
  FOREIGN KEY(emp_id) REFERENCES employee(emp_id) ON DELETE CASCADE,
  FOREIGN KEY(client_id) REFERENCES client(client_id) ON DELETE CASCADE
);
```

The works_with table our first table with a composite primary key. We can see
this is accomplished by just defining the primary key as both emp_id and
client_id.

Then both of those columns are defined as foreign keys referencing different
columns of different tables.

* Branch_Supplier Table:

```SQL
CREATE TABLE branch_supplier (
  branch_id INT,
  supplier_name VARCHAR(40),
  supply_type VARCHAR(40),
  PRIMARY KEY(branch_id, supplier_name),
  FOREIGN KEY(branch_id) REFERENCES branch(branch_id) ON DELETE CASCADE
);
```

Like the works_with table, the branch_supplier table has a composite primary
composed of a unique column and a foreign key from the branch table.

Now we have all of our tables set up!

```
mysql> show tables;
+------------------------+
| Tables_in_learning_sql |
+------------------------+
| branch                 |
| branch_supplier        |
| client                 |
| employee               |
| works_with             |
+------------------------+
```

## Entering Company Data

Just like table creation, data entry in SQL is very dependant on order. We're
going to add data to our database starting with the corporate branch.

* Corporate:

```SQL
INSERT INTO employee VALUES(100, 'David', 'Wallace', '1967-11-17', 'M', 250000, NULL, NULL);

INSERT INTO branch VALUES(1, 'Corporate', 100, '2006-02-09');

UPDATE employee
SET branch_id = 1
WHERE emp_id = 100;

INSERT INTO employee VALUES(101, 'Jan', 'Levinson', '1961-05-11', 'F', 110000, 100, 1);
```

The first employee entered is David Wallace. You'll notice when he's first
entered we leave his super_id and branch_id set to NULL. This is because we
don't have any values in the branch table yet.

Then we add some data to the branch table and define the Corporate branch.
After that point were able to update the employee table for Mr. Wallace and set
his branch_id to 1.

* Scranton:

```SQL
INSERT INTO employee VALUES(102, 'Michael', 'Scott', '1964-03-15', 'M', 75000, 100, NULL);

INSERT INTO branch VALUES(2, 'Scranton', 102, '1992-04-06');

UPDATE employee
SET branch_id = 2
WHERE emp_id = 102;

INSERT INTO employee VALUES(103, 'Angela', 'Martin', '1971-06-25', 'F', 63000, 102, 2);
INSERT INTO employee VALUES(104, 'Kelly', 'Kapoor', '1980-02-05', 'F', 55000, 102, 2);
INSERT INTO employee VALUES(105, 'Stanley', 'Hudson', '1958-02-19', 'M', 69000, 102, 2);
```

Just like with the Corporate branch data, we have to insert Michael with a NULL
in for branch_id because his branch has not yet been created in the branch
table. Then we add the Scranton branch to the branch table and then we can
update the branch_id for Michael. After that point we're free to add Angela,
Kelly, and Stanley.

* Stamford:

```SQL
INSERT INTO employee VALUES(106, 'Josh', 'Porter', '1969-09-05', 'M', 78000, 100, NULL);

INSERT INTO branch VALUES(3, 'Stamford', 106, '1998-02-13');

UPDATE employee
SET branch_id = 3
WHERE emp_id = 106;

INSERT INTO employee VALUES(107, 'Andy', 'Bernard', '1973-07-22', 'M', 65000, 106, 3);
INSERT INTO employee VALUES(108, 'Jim', 'Halpert', '1978-10-01', 'M', 71000, 106, 3);
```

And lastly of the branches, just like Corporate and Scranton, the Stamford
manager is entered with a NULL branch first. Then the branch info is entered in
the branch table and the manager is updated before the other branch personnel
are added.

* The Other Tables:

The rest of the tables can be populated using fairly straightforward INSERT
statements.

```SQL

-- BRANCH SUPPLIER
INSERT INTO branch_supplier VALUES(2, 'Hammer Mill', 'Paper');
INSERT INTO branch_supplier VALUES(2, 'Uni-ball', 'Writing Utensils');
INSERT INTO branch_supplier VALUES(3, 'Patriot Paper', 'Paper');
INSERT INTO branch_supplier VALUES(2, 'J.T. Forms & Labels', 'Custom Forms');
INSERT INTO branch_supplier VALUES(3, 'Uni-ball', 'Writing Utensils');
INSERT INTO branch_supplier VALUES(3, 'Hammer Mill', 'Paper');
INSERT INTO branch_supplier VALUES(3, 'Stamford Lables', 'Custom Forms');

-- CLIENT
INSERT INTO client VALUES(400, 'Dunmore Highschool', 2);
INSERT INTO client VALUES(401, 'Lackawana Country', 2);
INSERT INTO client VALUES(402, 'FedEx', 3);
INSERT INTO client VALUES(403, 'John Daly Law, LLC', 3);
INSERT INTO client VALUES(404, 'Scranton Whitepages', 2);
INSERT INTO client VALUES(405, 'Times Newspaper', 3);
INSERT INTO client VALUES(406, 'FedEx', 2);

-- WORKS_WITH
INSERT INTO works_with VALUES(105, 400, 55000);
INSERT INTO works_with VALUES(102, 401, 267000);
INSERT INTO works_with VALUES(108, 402, 22500);
INSERT INTO works_with VALUES(107, 403, 5000);
INSERT INTO works_with VALUES(108, 403, 12000);
INSERT INTO works_with VALUES(105, 404, 33000);
INSERT INTO works_with VALUES(107, 405, 26000);
INSERT INTO works_with VALUES(102, 406, 15000);
INSERT INTO works_with VALUES(105, 406, 130000);
```

Our company database is complete! We've got a series of interrelated tables
full of juicy data.

## Querying Company Database

It may not have been that obvious why we've created so many relationships
between all of our tables via foreign and composite keys. This may become
clearer as we start using more complex queries to access our database.

* Task 1: Find all employees

```SQL
SELECT *
FROM employee;
```

* Task 2: Find all employees ordered by salary

```SQL
SELECT *
FROM employee
ORDER BY salary DESC;
```

* Task 3: Find all employees ordered by sex then name

```SQL
SELECT *
FROM employee
ORDER BY sex, first_name, last_name;
```

* Task 4: Select the first and last names of all of the employees

```SQL
SELECT first_name, last_name
FROM employee;
```

* Side note: Use 'AS' verb to rewrite table headers

For example,

```SQL
SELECT first_name AS Forename, last_name AS Surname
FROM employee;
```

* Task 5: Find all of the unique genders

For this one we can use the 'DISTINCT' keyword.

```SQL
SELECT DISTINCT sex
FROM employee;
```

* Task 6: Find the number of employees

We'll want to use the 'COUNT()' SQL function here to count the number of rows
for some column.

```SQL
SELECT COUNT(emp_id)
FROM employee;
```

* Task 7: Find the number of female employees born after 1970

```SQL
SELECT COUNT(emp_id)
FROM employee
WHERE sex = 'F' AND birth_day > '1971-01-01';
```

* Task 8: Find the average of all male employee salaries

To accomplish this one we can use the AVG() SQL function.

```SQL
SELECT AVG(salary)
FROM employee
WHERE sex = 'M';
```

* Task 9: Find the sum of all employee salaries

We can use the SUM() function here.

```SQL
SELECT SUM(salary)
FROM employee;
```

* Task 10: Find out how many males and females there are

We can use the 'COUNT()' function here in conjunction with the 'GROUP BY'
statement. This is what is called aggregation.

```SQL
SELECT COUNT(sex), sex
FROM employee
GROUP BY sex;
```

* Task 11: Find the total sales of each salesmen

In this example of Aggregation we want to grab the sum of the total_sales
column from the works_with table if you group the sales by emp_id. That is, for
each unique emp_id we want to sum up their total_sales.

```SQL
SELECT SUM(total_sales), emp_id
FROM works_with
GROUP BY emp_id;
```

* Task 12: Find the total each client spent 

We'll we can do basically the same as Task 11 but with the client_id column
instead.

```SQL
SELECT SUM(total_sales), client_id 
FROM works_with
GROUP BY client_id;
```

## Wildcards

* Task 13: Find any client's who are an LLC

We can use the 'LIKE' keyword to search with SQL RegEx.

```SQL
-- % is any num chars, _ is one char
SELECT *
FROM client
WHERE client_name LIKE '%LLC';
```

* Task 14: Find any branch suppliers who are in the lable business

We can use a tigher % Regex to grab this one. The table data has a typo
otherwise this would work.

```SQL
-- % is any num chars, _ is one char
SELECT *
FROM branch_supplier
WHERE supplier_name LIKE '% Lable%';
```

* Task 15: Find any employee born in October

We can use the _ Regex char here to match the date format. The regex pattern
matches with any four chars, a hyphen, the number 10 (for Oct), and then
anything else %.

```SQL
-- % is any num chars, _ is one char
SELECT *
FROM employee
WHERE birth_day LIKE '____-10%';
```

* Task 16: Find any client that is a school

The syntax here is a good example of a find all sort of search in SQL.

```SQL
-- % is any num chars, _ is one char
SELECT *
FROM client 
WHERE client_name LIKE '%school%';
```

## Union

In SQL the 'UNION' operator is used to combine multiple 'SELECT' statements.

* Task 17: Find a list of employee and branch names

We can use the 'UNION' operator here. But first lets get just the employee
names first. We do that with a simple select first_name statement.

```SQL
SELECT first_name
FROM employee;
```

And we can do the same thing for branch_name from the branch table.

```SQL
SELECT branch_name 
FROM branch;
```

Now we can use the 'UNION' operator to combine both of those queries.

```SQL
SELECT first_name
FROM employee
UNION
SELECT branch_name 
FROM branch;
```

The output will come in the form of a single column.

There are a few rules for using 'UNION's though. For example, you have to
select the same number of columns in both select statements. You're not allowed
to select three columns from the first select statement and one from the
second. The selected columns also have to be of the same data-type. 

* Task 18: Find a list of all client and branch suplier names and branch ids

To do this we'll just want to select multiple columns from both the client_name
and supplier_name tables and then combine them with a 'UNION'. Because we'll be
selecting the branch_id column from both tables we'll want to use the
tablename.column syntax to differentiate them and make our SQL more readable.

```SQL
SELECT client_name, client.branch_id
FROM client
UNION
SELECT supplier_name, branch_supplier.branch_id
FROM branch_supplier;
```

## Joins

Before we introduce the idea of joins we have to insert another branch into our
branch table to help with demonstration.

```SQL
INSERT INTO branch VALUES(4, 'Buffalo', NULL, NULL);
```

Great now there's a Buffalo row!

```
mysql> SELECT * FROM branch;
+-----------+-------------+--------+----------------+
| branch_id | branch_name | mgr_id | mgr_start_date |
+-----------+-------------+--------+----------------+
|         1 | Corporate   |    100 | 2006-02-09     |
|         2 | Scranton    |    102 | 1992-04-06     |
|         3 | Stamford    |    106 | 1998-02-13     |
|         4 | Buffalo     |   NULL | NULL           |
+-----------+-------------+--------+----------------+
```

* Task 19: Find all branches and the names of their managers

```SQL
SELECT employee.emp_id, employee.first_name, branch.branch_name
FROM employee
JOIN branch
ON employee.emp_id = branch.mgr_id;
```

Lets break that statement down. First we're selecting three columns. However,
one of the columns we're selecting is from a different table. This is where the
'JOIN' syntax comes in. If we step back and look at the two tables in question
here we'll see that the mgr_id column is a foreign key to the employee.emp_id
column. 

Since the same values are in both table we can use the 'ON' comparatory-thingy
to sort of anchor our join statement around the emp_id column. Notice how the
'ON' statement is saying, only for employee.emp_id that equal a branch.mgr_id.
There are other emp_id's in the employee table. However, we only care about the
name of those employees who have an id in the branch table.

The output is a new table composed out the columns from two tables associated
via a common column. The output allows us to see the managers of each branch.
Notice how the 'NULL' mgr_id from the newly added Buffalo branch meant it was
ignored.

The above is an example of what is called an inner join. Lets look at another 
type of join.

## Left Join 

To see how a 'LEFT JOIN' works we'll use the above example with a left join
instead.

```SQL
SELECT employee.emp_id, employee.first_name, branch.branch_name
FROM employee
LEFT JOIN branch
ON employee.emp_id = branch.mgr_id;
```

With that example we see how everyone with a emp_id was printed, even if that
employee didn't have a corrisponding entry in the branch table. It litterally
joined the colums based off whatever the left column was.

## Right Join

Now lets look at the 'RIGHT JOIN'. Similar to the left join the right join
joins columns based off of whatever the right most column is. If we switch the
previous inner join to a right join we can see how it works.

```SQL
SELECT employee.emp_id, employee.first_name, branch.branch_name
FROM employee
RIGHT JOIN branch
ON employee.emp_id = branch.mgr_id;
```

Now in this case every entry from the branch.mgr_id column was printed even if
there is no corrisponding employee.emp_id or employee.first_name to go along
with it.

## Nested Queries 

* Task 20: Find the names of all employees who have sold over 30,000 to a single client.

We can use a nested query to accomplish this. First we'll write a query that
will fetch all of the emp_id's that have sold over 30k from the works_with
table.

```SQL
SELECT works_with.emp_id
FROM works_with
WHERE works_with.total_sales > 30000;
```

Great now we have all of the emp_id's we want. Lets write a second query to
gets the first and last name for those emp_id's. This is where we can use a
nested query.

```SQL
SELECT employee.first_name, employee.last_name
FROM employee
WHERE employee.emp_id IN (
	SELECT works_with.emp_id
	FROM works_with
	WHERE works_with.total_sales > 30000
);
```

We can use a 'WHERE' and 'IN' statement here in-conjunction with the parens ()
to nest our first query inside of our second. Now when the where statement does
its loop it will be looping over the values returned from the first query. 


* Task 21: Find all clients who are handled by the branch that Michael Scott
 manages. Assume you already know Michael's emp_id.

So again first we'll craft the inner query. We want to get the branch_id from
the branch table for Michael's mgr_id.

```SQL
SELECT branch.branch_id
FROM branch
WHERE branch.mgr_id = 102;
```

Then we can use that returned branch_id to find all of the client_id's for that
branch_id.

```SQL
SELECT client.client_name
FROM client 
WHERE client.branch_id = (
	SELECT branch.branch_id
	FROM branch
	WHERE branch.mgr_id = 102
	LIMIT 1
);
```

There are some extra parts of this one. First off the comparitor here is an
equals sign instead of an 'IN' statement. Because the equal sign only compares
against one value we'll end the inner query with a 'LIMIT 1' statement, making
sure the inner statement is limited to only ever returning one value so as to
not break the whole statement.


## On Delete 

This section covers removing entries from the database when they have foreign
keys associated with them. So lets say for example we wanted to delete Michael
Scott from the database. If we look at employee table we can see it has two
foreign key columns. So what happens to the value for mgr_id in the branch
table when Michael is deleted?

There are two ways that this can be handeled. Let look back at some of the SQL
syntax that was originally used to setup the tables.

```SQL
CREATE TABLE branch (
  branch_id INT PRIMARY KEY,
  branch_name VARCHAR(40),
  mgr_id INT,
  mgr_start_date DATE,
  FOREIGN KEY(mgr_id) REFERENCES employee(emp_id) ON DELETE SET NULL
);
```

We can see when we setup the branch table we set 'ON DELETE SET NULL' meaning
that on deletion of some employee.emp_id value the branch.mgr_id value gets set
to NULL.

So lets delete Michael!

```SQL
DELETE FROM employee
WHERE emp_id = 102;
```

And we see,

`Query OK, 1 row affected (0.20 sec)`

If we look at the branch table we'll see the mgr_id value for Scranton has been
set to NULL.

```
mysql> SELECT *
    -> FROM branch;
+-----------+-------------+--------+----------------+
| branch_id | branch_name | mgr_id | mgr_start_date |
+-----------+-------------+--------+----------------+
|         1 | Corporate   |    100 | 2006-02-09     |
|         2 | Scranton    |   NULL | 1992-04-06     |
|         3 | Stamford    |    106 | 1998-02-13     |
|         4 | Buffalo     |   NULL | NULL           |
+-----------+-------------+--------+----------------+
```

Likewise, if we look back at the employee table we'll see that a lot of the
supervisor id's have also been set to NULL for the same reason.

There is another way to handle foreign key deletion and it's called 'ON DELETE
CASCADE'. We used it when creating the branch_supplier table.

```SQL
CREATE TABLE branch_supplier (
  branch_id INT,
  supplier_name VARCHAR(40),
  supply_type VARCHAR(40),
  PRIMARY KEY(branch_id, supplier_name),
  FOREIGN KEY(branch_id) REFERENCES branch(branch_id) ON DELETE CASCADE
);
```

This delete action removes the entire row when a particular foreign key value
is removed. We can see this on in action if we delete a branch_id from the
branch table.

```SQL
DELETE FROM branch
WHERE branch_id = 2;
```

We can see that just wiped all of the branch_id = 2 rows from the
branch_supplier table.

```
mysql> SELECT *
    -> FROM branch_supplier;
+-----------+-----------------+------------------+
| branch_id | supplier_name   | supply_type      |
+-----------+-----------------+------------------+
|         3 | Hammer Mill     | Paper            |
|         3 | Patriot Paper   | Paper            |
|         3 | Stamford Lables | Custom Forms     |
|         3 | Uni-ball        | Writing Utensils |
+-----------+-----------------+------------------+
```

The reason we used 'ON DELETE SET NULL' for the branch table is because the
mgr_id is not a primary key there. In otherwords, its okay if the entry for
mgr_id is empty in the branch table. 

Likewise, this is the same reason we use 'ON DELETE CASCADE' for the
branch_supplier table. In that case, because branch_id is part of a primary
composite key, we have to have the table set to remove the whole row if the
primary key is deleted.


## Triggers

Triggers are just builtin traps that will detect some action and then preform
some other action accordingly.

To illustrate triggers lets first create a table.

```SQL
CREATE TABLE trigger_test (
	message VARCHAR(100)
);
```

Now we can start creating triggers. 

```SQL
DELIMITER $$
CREATE
	TRIGGER my_trigger BEFORE INSERT
	ON employee
	FOR EACH ROW BEGIN
		INSERT INTO trigger_test VALUES('added new employee');
	END$$
DELIMITER ;
```

In the example above we've reset the delimiter to be '$$'. That way we can have
a ';' inside of our trigger statement without prematurely ending it. This seems
to be a somewhat common practice on trigger statements.

The 'TRIGGER' statement itself is just used to automate something in the
database. In this case our trigger statement just adds an entry to the
trigger_test table with the string 'added new employee' whenever a new employee
is inserted into the employee table.

We start by defining our triggers name, `TRIGGER my_trigger`. Then we say when
its used, `BEFORE INSERT ON employee`. And finally what it does, `FOR EACH ROW
... INSERT INTO trigger_test VALUES('blah');` We cap off the loop with an 'END'
using our new delimiter '$$' and then finally were reset the delimiter
`DELIMITER ;`.

Now let's test it!

First off our new trigger_test table is empty,

```
mysql> SELECT * from trigger_test;
Empty set (0.00 sec)
```

Now lets add a new employee.

```SQL
INSERT INTO employee
VALUES(109, 'Oscar', 'Martinez', '1968-02-19', 'M', 69000, 106, 3);
```

Now if we check trigger_test we see,

```
mysql> SELECT * from trigger_test;
+--------------------+
| message            |
+--------------------+
| added new employee |
+--------------------+
```

Tada!


## Conditionals Within Triggers

We'll use the concept of triggers here to show some of the more advanced logic
that you can do within MySQL. Doing logic in MySQL is great because it means
less data has to be sent between the database and the client application and
less logic has to be put inside of the particular application that's accessing
the database. In the case below we'll simply be doing conditional logic using
the programatically typical 'IF', 'ELSEIF', 'ELSE', and 'END IF' statements.

```SQL
DELIMITER $$
CREATE
	TRIGGER my_new_trigger BEFORE INSERT
	ON employee
	FOR EACH ROW BEGIN
		IF NEW.sex = 'M' THEN
			INSERT INTO trigger_test VALUES('added male employee');
		ELSEIF NEW.sex = 'F' THEN
			INSERT INTO trigger_test VALUES('added female employee');
		ELSE
			INSERT INTO trigger_test VALUES('added other gender employee');
		END IF;
	END$$
DELIMITER ;
```

In this case we're checking the gender of newly added employee's and logging
that information in our trigger_test table. One thing worth noting is the
'NEW.sex' statement which just specifies the new entry.

With that new trigger set we can now test it by entering in a new employee.

```SQL
INSERT INTO employee
VALUES(111, 'Pam', 'Beesly', '1988-02-19', 'F', 69000, 106, 3);
```

Now if we check the trigger_test table we see,

```
mysql> SELECT * FROM trigger_test;
+-----------------------+
| message               |
+-----------------------+
| added new employee    |
| added new employee    |
| added female employee |
+-----------------------+
```

We see both my_trigger and my_new_trigger fired and so now we have two
additional lines in the trigger_test table;

We can get rid of a trigger using 'DROP'. For example,

```SQL
DROP TRIGGER my_trigger;
```

And now we only have one trigger set.


Notes taken from this freeCodeCamp video.

* [FreeCodeCamp Video](youtube.com/watch?v=HXV3zeQKqGY)

