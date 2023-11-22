# DataCamp_Data_Engineer_Introduction_to_Relational_Databases_in_SQL

Course Description
You’ve already used SQL to query data from databases. But did you know that there's a lot more you can do with databases? You can model different phenomena in your data, as well as the relationships between them. This gives your data structure and consistency, which results in better data quality. In this course, you'll experience this firsthand by working with a real-life dataset that was used to investigate questionable university affiliations. Column by column, table by table, you'll get to unlock and admire the full potential of databases. You'll learn how to create tables and specify their relationships, as well as how to enforce data integrity. You'll also discover other unique features of database systems, such as constraints.


### 1. Your first database

In this chapter, you'll create your very first database with a set of simple SQL commands. Next, you'll migrate data from existing flat tables into that database. You'll also learn how meta-information about a database can be queried.

- [ ] Introduction to relational databases



- [ ] Attributes of relational databases
- [X] Query information_schema with SELECT

information_schema is a meta-database that holds information about your current database. information_schema has multiple tables you can query with the known SELECT * FROM syntax:

tables: information about all tables in your current database
columns: information about all columns in all of the tables in your current database
…
In this exercise, you'll only need information from the 'public' schema, which is specified as the column table_schema of the tables and columns tables. The 'public' schema holds information about user-defined tables and databases. The other types of table_schema hold system information – for this course, you're only interested in user-defined stuff.

```ruby
-- Query the right table in information_schema
SELECT table_name 
FROM information_schema.tables
-- Specify the correct table_schema value
WHERE table_schema = 'public';
```

- [ ] Tables: At the core of every database



- [X] CREATE your first few TABLEs

You'll now start implementing a better database model. For this, you'll create tables for the professors and universities entity types. The other tables will be created for you.

The syntax for creating simple tables is as follows:

```ruby
CREATE TABLE table_name (
 column_a data_type,
 column_b data_type,
 column_c data_type
);
```

Attention: Table and columns names, as well as data types, don't need to be surrounded by quotation marks.

```ruby
-- Create a table for the universities entity type
CREATE TABLE universities (
    university_shortname text,
    university text,
    university_city text
);


-- Print the contents of this table
SELECT * 
FROM universities
```

- [X] ADD a COLUMN with ALTER TABLE

Oops! We forgot to add the university_shortname column to the professors table. You've probably already noticed:

![image](https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Introduction_to_Relational_Databases_in_SQL/assets/105381652/998dca27-aa0f-44c4-812a-4ab73c623dd1)

In chapter 4 of this course, you'll need this column for connecting the professors table with the universities table.
However, adding columns to existing tables is easy, especially if they're still empty.
To add columns you can use the following SQL query:

```ruby
ALTER TABLE table_name
ADD COLUMN column_name data_type;
```

```ruby
-- Add the university_shortname column
ALTER TABLE professors
ADD COLUMN university_shortname text;

-- Print the contents of this table
SELECT * 
FROM professors
```

- [ ] Update your database as the structure changes



- [X] RENAME and DROP COLUMNs in affiliations

As mentioned in the video, the still empty affiliations table has some flaws. In this exercise, you'll correct them as outlined in the video.

You'll use the following queries:

To rename columns:
```ruby
ALTER TABLE table_name
RENAME COLUMN old_name TO new_name;
```
To delete columns:
```ruby
ALTER TABLE table_name
DROP COLUMN column_name;
```

```ruby
-- Rename the organisation column
ALTER TABLE affiliations
RENAME COLUMN organisation TO organization;

-- Delete the university_shortname column
ALTER TABLE affiliations
DROP COLUMN university_shortname;
```

- [X] Migrate data with INSERT INTO SELECT DISTINCT

Now it's finally time to migrate the data into the new tables. You'll use the following pattern:
```
INSERT INTO ... 
SELECT DISTINCT ... 
FROM ...;
```
It can be broken up into two parts:

First part:
```
SELECT DISTINCT column_name1, column_name2, ... 
FROM table_a;
```
This selects all distinct values in table table_a – nothing new for you.

Second part:
```
INSERT INTO table_b ...;
```
Take this part and append it to the first, so it inserts all distinct rows from table_a into table_b.

One last thing: It is important that you run all of the code at the same time once you have filled out the blanks.

```ruby
-- Insert unique professors into the new table
INSERT INTO professors 
SELECT DISTINCT firstname, lastname, university_shortname 
FROM university_professors;

-- Doublecheck the contents of professors
SELECT * 
FROM professors;
```

- [X] Delete tables with DROP TABLE

```ruby
-- Delete the university_professors table
DROP TABLE university_professors;
```

### 2. Enforce data consistency with attribute constraints

After building a simple database, it's now time to make use of the features. You'll specify data types in columns, enforce column uniqueness, and disallow NULL values in this chapter.

- [ ] Better data quality with constraints


- [ ] Types of database constraints
- [X] Conforming with data types

For demonstration purposes, I created a fictional database table that only holds three records. The columns have the data types date, integer, and text, respectively.
```ruby
CREATE TABLE transactions (
 transaction_date date, 
 amount integer,
 fee text
);
```
Have a look at the contents of the transactions table.
The transaction_date accepts date values. According to the PostgreSQL documentation, it accepts values in the form of ``YYYY-MM-DD``, ``DD/MM/YY``, and so forth.
Both columns amount and fee appear to be numeric, however, the latter is modeled as text – which you will account for in the next exercise.
```ruby
-- Let's add a record to the table
INSERT INTO transactions (transaction_date, amount, fee) 
VALUES ('2018-09-24', 5454, '30');

-- Doublecheck the contents
SELECT *
FROM transactions;
```

- [X] Type CASTs

In the video, you saw that type casts are a possible solution for data type issues. If you know that a certain column stores numbers as text, you can cast the column to a numeric form, i.e. to integer.
```ruby
SELECT CAST(some_column AS integer)
FROM table;
```
Now, the some_column column is temporarily represented as integer instead of text, meaning that you can perform numeric calculations on the column.

```ruby
-- Calculate the net amount as amount + fee
SELECT transaction_date, amount + CAST(fee AS integer) AS net_amount 
FROM transactions;
```
- [ ] Working with data types


- [X] Change types with ALTER COLUMN

The syntax for changing the data type of a column is straightforward. The following code changes the data type of the column_name column in table_name to varchar(10):
```ruby
ALTER TABLE table_name
ALTER COLUMN column_name
TYPE varchar(10)
```
Now it's time to start adding constraints to your database.

```ruby
-- Select the university_shortname column
SELECT DISTINCT(university_shortname)
FROM professors;

-- Specify the correct fixed-length character type
ALTER TABLE professors
ALTER COLUMN university_shortname
TYPE char(3);

-- Change the type of firstname
ALTER TABLE professors
ALTER COLUMN firstname
TYPE VARCHAR(64);
```

- [X] Convert types USING a function

If you don't want to reserve too much space for a certain varchar column, you can truncate the values before converting its type.
For this, you can use the following syntax:
```ruby
ALTER TABLE table_name
ALTER COLUMN column_name
TYPE varchar(x)
USING SUBSTRING(column_name FROM 1 FOR x)
```
You should read it like this: Because you want to reserve only x characters for column_name, you have to retain a SUBSTRING of every value, i.e. the first x characters of it, and throw away the rest. This way, the values will fit the varchar(x) requirement.

```ruby
-- Convert the values in firstname to a max. of 16 characters
ALTER TABLE professors 
ALTER COLUMN firstname 
TYPE varchar(16)
USING SUBSTRING(firstname FROM 1 FOR 16)
```

- [ ] The not-null and unique constraints


- [X] Disallow NULL values with SET NOT NULL

The professors table is almost ready now. However, it still allows for NULLs to be entered. Although some information might be missing about some professors, there's certainly columns that always need to be specified.

```ruby
-- Disallow NULL values in firstname
ALTER TABLE professors 
ALTER COLUMN firstname SET NOT NULL;

-- Disallow NULL values in lastname
ALTER TABLE professors
ALTER COLUMN lastname SET NOT NULL;
```

- [ ] What happens if you try to enter NULLs?

- [ ] Make your columns UNIQUE with ADD CONSTRAINT

As seen in the video, you add the UNIQUE keyword after the column_name that should be unique. This, of course, only works for new tables:
```ruby
CREATE TABLE table_name (
 column_name UNIQUE
);
```
If you want to add a unique constraint to an existing table, you do it like that:
```ruby
ALTER TABLE table_name
ADD CONSTRAINT some_name UNIQUE(column_name);
```
Note that this is different from the ALTER COLUMN syntax for the not-null constraint. Also, you have to give the constraint a name some_name.

```ruby
-- Make universities.university_shortname unique
ALTER TABLE universities
ADD CONSTRAINT university_shortname_unq UNIQUE(university_shortname);

-- Make organizations.organization unique
ALTER TABLE organizations
ADD CONSTRAINT organization_unq UNIQUE(organization);
```

### 3. Uniquely identify records with key constraints

Now let’s get into the best practices of database engineering. It's time to add primary and foreign keys to the tables. These are two of the most important concepts in databases, and are the building blocks you’ll use to establish relationships between tables.

- [ ] Keys and superkeys

      
- [X] Get to know SELECT COUNT DISTINCT

Your database doesn't have any defined keys so far, and you don't know which columns or combinations of columns are suited as keys.
There's a simple way of finding out whether a certain column (or a combination) contains only unique values – and thus identifies the records in the table.
You already know the ``SELECT DISTINCT`` query from the first chapter. Now you just have to wrap everything within the ``COUNT()`` function and PostgreSQL will return the number of unique rows for the given columns:
```ruby
SELECT COUNT(DISTINCT(column_a, column_b, ...))
FROM table;
```
```ruby
-- Count the number of rows in universities
SELECT COUNT(*) 
FROM universities;

-- Count the number of distinct values in the university_city column
SELECT COUNT(DISTINCT(university_city)) 
FROM universities;
```

- [ ] Identify keys with SELECT COUNT DISTINCT

There's a very basic way of finding out what qualifies for a key in an existing, populated table:
Count the distinct records for all possible combinations of columns. If the resulting number x equals the number of all rows in the table for a combination, you have discovered a superkey.
Then remove one column after another until you can no longer remove columns without seeing the number x decrease. If that is the case, you have discovered a (candidate) key.
The table professors has 551 rows. It has only one possible candidate key, which is a combination of two attributes. You might want to try different combinations using the "Run code" button. Once you have found the solution, you can submit your answer.

```ruby
-- Try out different combinations
SELECT COUNT(DISTINCT(firstname, lastname)) 
FROM professors;
```


- [ ] Primary keys


- [ ] Identify the primary key


- [ ] ADD key CONSTRAINTs to the tables

Two of the tables in your database already have well-suited candidate keys consisting of one column each: organizations and universities with the organization and university_shortname columns, respectively.
In this exercise, you'll rename these columns to id using the RENAME COLUMN command and then specify primary key constraints for them. This is as straightforward as adding unique constraints (see the last exercise of Chapter 2):
```
ALTER TABLE table_name
ADD CONSTRAINT some_name PRIMARY KEY (column_name)
```
Note that you can also specify more than one column in the brackets.

```ruby
-- Rename the organization column to id
ALTER TABLE organizations
RENAME COLUMN organization TO id;

-- Make id a primary key
ALTER TABLE organizations
ADD CONSTRAINT organization_pk PRIMARY KEY (id);

-- Rename the university_shortname column to id
ALTER TABLE universities
RENAME COLUMN university_shortname TO id;

-- Make id a primary key
ALTER TABLE universities
ADD CONSTRAINT university_pk PRIMARY KEY (id);
```

- [ ] Surrogate keys
- [X] Add a SERIAL surrogate key

Since there's no single column candidate key in professors (only a composite key candidate consisting of firstname, lastname), you'll add a new column id to that table.

This column has a special data type serial, which turns the column into an auto-incrementing number. This means that, whenever you add a new professor to the table, it will automatically get an id that does not exist yet in the table: a perfect primary key!

```ruby
-- Add the new column to the table
ALTER TABLE professors 
ADD COLUMN id serial;

-- Make id a primary key
ALTER TABLE professors 
ADD CONSTRAINT professors_pkey PRIMARY KEY (id);

-- Have a look at the first 10 rows of professors
SELECT *
FROM professors
LIMIT 10;
```

- [X] CONCATenate columns to a surrogate key

Another strategy to add a surrogate key to an existing table is to concatenate existing columns with the CONCAT() function.
Let's think of the following example table:
```ruby
CREATE TABLE cars (
 make varchar(64) NOT NULL,
 model varchar(64) NOT NULL,
 mpg integer NOT NULL
)
```
The table is populated with 10 rows of completely fictional data.
Unfortunately, the table doesn't have a primary key yet. None of the columns consists of only unique values, so some columns can be combined to form a key.
In the course of the following exercises, you will combine make and model into such a surrogate key.

```ruby
-- Count the number of distinct rows with columns make, model
SELECT COUNT(DISTINCT(make, model)) 
FROM cars;

-- Add the id column
ALTER TABLE cars
ADD COLUMN id varchar(128);

-- Update id with make + model
UPDATE cars
SET id = CONCAT(make, model);

-- Make id a primary key
ALTER TABLE cars
ADD CONSTRAINT id_pk PRIMARY KEY (id);

-- Have a look at the table
SELECT * FROM cars;
```

- [X] Test your knowledge before advancing

Before you move on to the next chapter, let's quickly review what you've learned so far about attributes and key constraints. If you're unsure about the answer, please quickly review chapters 2 and 3, respectively.

Let's think of an entity type "student". A student has:

a last name consisting of up to 128 characters (required),
a unique social security number, consisting only of integers, that should serve as a key,
a phone number of fixed length 12, consisting of numbers and characters (but some students don't have one).

```ruby
-- Create the table
CREATE TABLE students (
  last_name varchar(128) NOT NULL,
  ssn integer PRIMARY KEY,
  phone_no char(12)
);
```

### 4. Glue together tables with foreign keys

In the final chapter, you'll leverage foreign keys to connect tables and establish relationships that will greatly benefit your data quality. And you'll run ad hoc analyses on your new database.

- [ ] Model 1N relationships with foreign keys
- [X] REFERENCE a table with a FOREIGN KEY

In your database, you want the professors table to reference the universities table. You can do that by specifying a column in professors table that references a column in the universities table.
As just shown in the video, the syntax for that looks like this:
```ruby
ALTER TABLE a 
ADD CONSTRAINT a_fkey FOREIGN KEY (b_id) REFERENCES b (id);
```
Table a should now refer to table b, via b_id, which points to id. a_fkey is, as usual, a constraint name you can choose on your own.
Pay attention to the naming convention employed here: Usually, a foreign key referencing another primary key with name id is named x_id, where x is the name of the referencing table in the singular form.

```ruby
-- Rename the university_shortname column
ALTER TABLE professors
RENAME COLUMN university_shortname TO university_id;

-- Add a foreign key on professors referencing universities
ALTER TABLE professors 
ADD CONSTRAINT professors_fkey FOREIGN KEY (university_id) REFERENCES universities (id);
```

- [X] Explore foreign key constraints

Foreign key constraints help you to keep order in your database mini-world. In your database, for instance, only professors belonging to Swiss universities should be allowed, as only Swiss universities are part of the universities table.

The foreign key on professors referencing universities you just created thus makes sure that only existing universities can be specified when inserting new data. Let's test this!

```ruby
-- Try to insert a new professor
INSERT INTO professors (firstname, lastname, university_id)
VALUES ('Albert', 'Einstein', 'UZH');
```

- [X] JOIN tables linked by a foreign key

Let's join these two tables to analyze the data further!
You might already know how SQL joins work from the Intro to SQL for Data Science course (last exercise) or from Joining Data in PostgreSQL.
Here's a quick recap on how joins generally work:
 ```ruby
SELECT ...
FROM table_a
JOIN table_b
ON ...
WHERE ...
```
While foreign keys and primary keys are not strictly necessary for join queries, they greatly help by telling you what to expect. For instance, you can be sure that records referenced from table A will always be present in table B – so a join from table A will always find something in table B. If not, the foreign key constraint would be violated.

```ruby
-- Select all professors working for universities in the city of Zurich
SELECT professors.lastname, universities.id, universities.university_city
FROM professors
JOIN universities
ON professors.university_id = universities.id
WHERE universities.university_city = 'Zurich';
```
- [ ] Model more complex relationships
- [X] Add foreign keys to the "affiliations" table

At the moment, the affiliations table has the structure {firstname, lastname, function, organization}, as you can see in the preview at the bottom right. In the next three exercises, you're going to turn this table into the form {professor_id, organization_id, function}, with professor_id and organization_id being foreign keys that point to the respective tables.

You're going to transform the affiliations table in-place, i.e., without creating a temporary table to cache your intermediate results.

```ruby
-- Add a professor_id column
ALTER TABLE affiliations
ADD COLUMN professor_id integer REFERENCES professors (id);

-- Rename the organization column to organization_id
ALTER TABLE affiliations
RENAME organization TO organization_id;

-- Add a foreign key on organization_id
ALTER TABLE affiliations
ADD CONSTRAINT affiliations_organization_fkey FOREIGN KEY (organization_id) REFERENCES organizations (id);
```

- [X] Populate the "professor_id" column

Now it's time to also populate professors_id. You'll take the ID directly from professors.

Here's a way to update columns of a table based on values in another table:
```ruby
UPDATE table_a
SET column_to_update = table_b.column_to_update_from
FROM table_b
WHERE condition1 AND condition2 AND ...;
```
This query does the following:

For each row in table_a, find the corresponding row in table_b where condition1, condition2, etc., are met.
Set the value of column_to_update to the value of column_to_update_from (from that corresponding row).
The conditions usually compare other columns of both tables, e.g. table_a.some_column = table_b.some_column. Of course, this query only makes sense if there is only one matching row in table_b.

```ruby
-- Update professor_id to professors.id where firstname, lastname correspond to rows in professors
UPDATE affiliations
SET professor_id = professors.id
FROM professors
WHERE affiliations.firstname = professors.firstname AND affiliations.lastname = professors.lastname;

-- Have a look at the 10 first rows of affiliations again
SELECT * 
FROM affiliations
LIMIT 10;
```

- [X] Drop "firstname" and "lastname"

The firstname and lastname columns of affiliations were used to establish a link to the professors table in the last exercise – so the appropriate professor IDs could be copied over. This only worked because there is exactly one corresponding professor for each row in affiliations. In other words: {firstname, lastname} is a candidate key of professors – a unique combination of columns.

It isn't one in affiliations though, because, as said in the video, professors can have more than one affiliation.

Because professors are referenced by professor_id now, the firstname and lastname columns are no longer needed, so it's time to drop them. After all, one of the goals of a database is to reduce redundancy where possible.

```ruby
-- Drop the firstname column
ALTER TABLE affiliations
DROP COLUMN firstname;

-- Drop the lastname column
ALTER TABLE affiliations
DROP COLUMN lastname;
```

- [ ] Referential integrity

- [ ] Referential integrity violations

- [X] Change the referential integrity behavior of a key

So far, you implemented three foreign key constraints:
```
professors.university_id to universities.id
affiliations.organization_id to organizations.id
affiliations.professor_id to professors.id
```
These foreign keys currently have the behavior ON DELETE NO ACTION. Here, you're going to change that behavior for the column referencing organizations from affiliations. If an organization is deleted, all its affiliations (by any professor) should also be deleted.
Altering a key constraint doesn't work with ALTER COLUMN. Instead, you have to DROP the key constraint and then ADD a new one with a different ON DELETE behavior.
For deleting constraints, though, you need to know their name. This information is also stored in information_schema.

```ruby
-- Identify the correct constraint name
SELECT constraint_name, table_name, constraint_type
FROM information_schema.table_constraints
WHERE constraint_type = 'FOREIGN KEY';

-- Drop the right foreign key constraint
ALTER TABLE affiliations
DROP CONSTRAINT affiliations_organization_id_fkey;

-- Add a new foreign key constraint from affiliations to organizations which cascades deletion
ALTER TABLE affiliations
ADD CONSTRAINT affiliations_organization_id_fkey FOREIGN KEY (organization_id) REFERENCES organizations (id) ON DELETE CASCADE;

-- Delete an organization 
DELETE FROM organizations 
WHERE id = 'CUREM';

-- Check that no more affiliations with this organization exist
SELECT * FROM affiliations
WHERE organization_id = 'CUREM';
```

- [ ] Roundup

- [X] Count affiliations per university

Now that your data is ready for analysis, let's run some exemplary SQL queries on the database. You'll now use already known concepts such as grouping by columns and joining tables.

In this exercise, you will find out which university has the most affiliations (through its professors). For that, you need both affiliations and professors tables, as the latter also holds the university_id.

As a quick repetition, remember that joins have the following structure:
```
SELECT table_a.column1, table_a.column2, table_b.column1, ... 
FROM table_a
JOIN table_b 
ON table_a.column = table_b.column
```
This results in a combination of table_a and table_b, but only with rows where table_a.column is equal to table_b.column.

```ruby
-- Count the total number of affiliations per university
SELECT COUNT(*), professors.university_id 
FROM affiliations
JOIN professors
ON affiliations.professor_id = professors.id
-- Group by the university ids of professors
GROUP BY professors.university_id 
ORDER BY count DESC;
```
- [X] Join all the tables together

In this last exercise, your task is to find the university city of the professor with the most affiliations in the sector "Media & communication".

For this,

you need to join all the tables,
group by some column,
and then use selection criteria to get only the rows in the correct sector.
Let's do this in three steps!

```ruby
-- Filter the table and sort it
SELECT COUNT(*), organizations.organization_sector, 
professors.id, universities.university_city
FROM affiliations
JOIN professors
ON affiliations.professor_id = professors.id
JOIN organizations
ON affiliations.organization_id = organizations.id
JOIN universities
ON professors.university_id = universities.id
WHERE organizations.organization_sector = 'Media & communication'
GROUP BY organizations.organization_sector, 
professors.id, universities.university_city
ORDER BY COUNT DESC;
```
