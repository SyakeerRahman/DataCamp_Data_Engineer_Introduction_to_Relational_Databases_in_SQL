# DataCamp_Data_Engineer_Introduction_to_Relational_Databases_in_SQL

Course Description
You’ve already used SQL to query data from databases. But did you know that there's a lot more you can do with databases? You can model different phenomena in your data, as well as the relationships between them. This gives your data structure and consistency, which results in better data quality. In this course, you'll experience this firsthand by working with a real-life dataset that was used to investigate questionable university affiliations. Column by column, table by table, you'll get to unlock and admire the full potential of databases. You'll learn how to create tables and specify their relationships, as well as how to enforce data integrity. You'll also discover other unique features of database systems, such as constraints.


### 1. Your first database

In this chapter, you'll create your very first database with a set of simple SQL commands. Next, you'll migrate data from existing flat tables into that database. You'll also learn how meta-information about a database can be queried.

- [ ] Introduction to relational databases
- [ ] Attributes of relational databases
- [ ] Query information_schema with SELECT
- [ ] Tables: At the core of every database
- [ ] CREATE your first few TABLEs
- [ ] ADD a COLUMN with ALTER TABLE
- [ ] Update your database as the structure changes
- [ ] RENAME and DROP COLUMNs in affiliations
- [ ] Migrate data with INSERT INTO SELECT DISTINCT
- [ ] Delete tables with DROP TABLE

### 2. Enforce data consistency with attribute constraints

After building a simple database, it's now time to make use of the features. You'll specify data types in columns, enforce column uniqueness, and disallow NULL values in this chapter.

- [ ] Better data quality with constraints
- [ ] Types of database constraints
- [ ] Conforming with data types
- [ ] Type CASTs
- [ ] Working with data types
- [ ] Change types with ALTER COLUMN
- [ ] Convert types USING a function
- [ ] The not-null and unique constraints
- [ ] Disallow NULL values with SET NOT NULL
- [ ] What happens if you try to enter NULLs?
- [ ] Make your columns UNIQUE with ADD CONSTRAINT

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
