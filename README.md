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
- [ ] Get to know SELECT COUNT DISTINCT
- [ ] Identify keys with SELECT COUNT DISTINCT
- [ ] Primary keys
- [ ] Identify the primary key
- [ ] ADD key CONSTRAINTs to the tables
- [ ] Surrogate keys
- [ ] Add a SERIAL surrogate key
- [ ] CONCATenate columns to a surrogate key
- [ ] Test your knowledge before advancing

### 4. Glue together tables with foreign keys

In the final chapter, you'll leverage foreign keys to connect tables and establish relationships that will greatly benefit your data quality. And you'll run ad hoc analyses on your new database.

- [ ] Model 1N relationships with foreign keys
- [ ] REFERENCE a table with a FOREIGN KEY
- [ ] Explore foreign key constraints
- [ ] JOIN tables linked by a foreign key
- [ ] Model more complex relationships
- [ ] Add foreign keys to the "affiliations" table
- [ ] Populate the "professor_id" column
- [ ] Drop "firstname" and "lastname"
- [ ] Referential integrity
- [ ] Referential integrity violations
- [ ] Change the referential integrity behavior of a key
- [ ] Roundup
- [ ] Count affiliations per university
- [ ] Join all the tables together
