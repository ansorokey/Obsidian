## What is SQL?
SQL - *Structured Query Language* is not a database, but a *Relation DataBase Management System* (*RDBMS*). This is software that manages database fiels for us. In a RDBMS, data is stored in individual, organized locations that are able to reference one another to minimize repetition.

The data in SQL databases are store in *tables*. A table is essentially a spreadsheet that tracks the properties of items through columns and rows.

A single table should typically describe a single type of item (*entity*).
For example, if we wanted to store information about a person and their pets, we could create two seperate tables to track this information. One for the owner, and one for the pets owned - each with their own properties.

The properties in a table must be a certain data type determined upon creation. This tells the table how much memory to allocate for any new pieces of information. SQL data types include:
`NULL` for anything without a value.
`BOOLEAN` for true or false values. Stored as 0 for false and 1 for true.
`INTEGER` and `REAL` for numbers. Integer for integer values, and real for anything with a decimal/floating point value. We can set the decimal place of a decimal value using two numbers: the total number of values allowed in the column, and the total number of values allowed behind the decimal ``
`TEXT` for strings. A character limit can be set with `VARCHAR(#chars)`.
`BLOB` for large pieces of data that need to be interpreted as-is, like very long pieces of text or even images.
`NUMERIC` is a special data type that will auto-convert between the other values.

A column can be given constraints which will either accept or reject an attempt to insert information to a table if it does not meet its requirements. Constraints include:
`NOT NULL` will require an entry, meaning we cannot insert data with this field empty.
`DEFAULT`  will set a default value if one is not passed in when data is inserted. The default value comes after the default keyword.
`PRIMARY KEY` sets a field as the unique identifier for the entire table. This is data that cannot be held by or shared with any other entity in the table. 
`AUTOINCREMENT` automatically increases an integer value each time an entry is added.

Database contents are usually planned out in a *schema* - which is simply a mock-up of what tables and thier relationships should look like. They'll usually include the name of the tables,  columns, and data types/constraints.
Standard convention is to capitalize all SQL commands, while leaving the names of tables and properties in lowercase. 


SQL is the backbone to a wide variety of different interfaces. Since they all share the same backbone, they will be very similar. We will be working with SQLite3 as the RDBMS.

## SQLite3
SQLite3 is different than most SQL RDBMS because SQLite3 allows us to read from locally hosted files on a device instead of through a cloud or server.

## Creating Tables
When creating a data table, standard convention is to name the table and fields with *snake_case* using only lowercase letters, numbers, and underscores. And since a table represents information for multiple things, it will usually be named in the plural form.

Here is the general syntax.
```sql
CREATE TABLE table_name (
	column_name DATA TYPE CONSTRAINTS,
	column_name DATA TYPE CONSTRAINTS,
	column_name DATA TYPE CONSTRAINTS,
);

CREATE TABLE puppies (
id INTEGER PRIMARY KEY AUTOINCREMENT,
  name VARCHAR(50),
  age_yrs NUMERIC(3,1),
  breed VARCHAR(100),
  weight_lbs INTEGER,
  microchipped BOOLEAN
);
```
**Key points**
*capitalize SQL commands*
*snake_case everything else*
*end statements in a semicolon*
*wrap multi-line statements in parentheses*
