# Data Definition Language



## Objectives


* Lean how databases are created
* Save and use DDL in scripts
* Create and alter the field structure of tables
* Create and drop constraints


* Field types
* Create and drop indexes
* Create and drop views
* Prepare Statements



## Database Definition Language (DDL)


* SQL commands to create and modify database objects
* More control over database objects
* DDL script



## Scripts


* Set of SQL commands saved in a text file
    * Provision databases
    * Same for dev and prod env



## Using Scripts with MySQL


* `mysql database_name < path_and_script_name*`
* http://recordit.co/dHS4kCbl97
* Or as simple as just copy and paste the whole script content and execute them



## Naming tips


* Using *consistent* names
* Do not include spaces
* Avoid the use of SQL reserved words
* Keep names short, but long enough to be descriptive
* Be consistent in your use of abbreviations
* Avoid generic field names, such as ID or Date



## Databases


* MySQL server can contain multiple databases
* `Create Database {name};`
* `USE {name};`


### Overall hierarchy

```
Database > Table > Column > Record

+---Database-------------------------------+
|                                          |
|  +--Table-----------------------+        |
|  | Column | Column | Column     |+       |
|  +------------------------------+|+      |
|  | Vale   | Value  | Value      |||      |
|  | Vale   | Value  | Value      |||      |
|  | Vale   | Value  | Value      |||      |
|  +------------------------------+||      |
|   +------------------------------+|      |
|    +------------------------------+      |
+------------------------------------------+
```



## Create a Table


Syntax:

```mysql
CREATE TABLE {tableName} (
    {fieldName} datatype null | not null,
    {fieldName} datatype null | not null
);
```


Example:

```mysql
-- Create table called contracts with the ArtistID from the Artists table and a ContractDate field
CREATE TABLE Contracts (
    ArtistID Integer NOT NULL,
    ContractDate DateTime NOT NULL
);
```


### Dropping a table


Syntax:

```mysql
DROP TABLE {tableName};
```


Example:

```mysql
-- Remove the Contracts table from the database
DROP TABLE Contracts;
```



## Adding columns to an existing table


Syntax:

```mysql
ALTER TABLE {tableName}
ADD {columnName} {dataType} {null | not null};
```


Example:

```mysql
-- Add a text field called contract type with a length of 10 to the Contracts table
ALTER TABLE Contracts
ADD ContractType VarChar(10) NULL;
```


## Altering Existing Columns in a table


Syntax:

```mysql
ALTER TABLE {tableName}
MODIFY COLUMN {columnName} {dataType} {null | not null};
```


Example:

```mysql
-- Change the length on the ContractType field to 15 in the Contracts Table
ALTER TABLE Contracts 
MODIFY ContractType VarChar(15);
```



## Dropping a column


Syntax:

```mysql
ALTER TABLE {tableName}
DROP COLUMN {columnName};
```


Example:

```mysql
-- Remove the ContractType column from Contracts table
ALTER TABLE Contracts
DROP COLUMN ContractType;
```



## Identify Columns and Sequences


* Techniques to automatically generated a sequential number with each row that is inserted
* Never have to insert a value to it, but it will always be unique within the table
* Make excellent primary key in cases where there is no naturally occurring primary key


## Defining as Identify Column


```mysql
CREATE TABLE tbl (
    ID int AUTO_INCREMENT NOT NULL,
    anotherField int null,
    primary key(ID)
);
```


## Identity vs GUID columns


![Distributed database concept image](https://raw.githubusercontent.com/csula/cs1222-spring-2018/master/notes/imgs/distributed-dbs.png)



## Constraints


### Restriction

* Ensure data integrity


### Entity constraints

Ensure the value in a column meets some criteria compared to other rows in the table

* Primary key
* Unique


### Domain constraints

Ensure the value in a column meets some particular criteria without respect to another row in the table

* Default
* Check


### Referential integrity constraints

Ensure the value in a column matches a value in another column in a different table or (sometimes) the same table

* Foreign key


## Constraint Naming Conventions


* Consistency


Begin the constraints with two letters that indicate the type of constraint

* pk for primary key
* fk for foreign key
* df for default
* ck for check
* and un for unique


* Follow that two letters the table name
* If it is a check, default, or unique constraint, follow the table name with the column being constrained
* If it is a foreign key constraint, follow the table name with the name of the table it refers to.


## Primary Key Constraint (with alter table)


Syntax:

```mysql
ALTER TABLE {tableName}
ADD CONSTRAINT {constraintName} PRIMARY KEY ({columnname}, {columnname});
```


Example:

```mysql
-- Set the primary key of the Contracts table to ArtistID and ContractDate
ALTER TABLE Contracts
ADD CONSTRAINT pk_contracts PRIMARY KEY (ArtistID, ContractDate);

-- Set the primary key of tbl to the ID column
ALTER TABLE tbl
ADD CONSTRAINT pk_tbl PRIMARY KEY (ID);
```


## Primary key constraint with create table


Syntax:

```mysql
# Only allow one pk fields
CREATE TABLE {tableName} (
    {column} datattype null | not null PRIMARY KEY
);

CREATE TABLE tableName (
    column1 datatype not null,
    column2 datatype not null,
    CONSTRAINT pk_tableName PRIMARY KEY (column1, column2)
);
```


## Unique Constraint with alter table


* **alternate keys**


Syntax:

```mysql
ALTER TABLE {tableName}
ADD CONSTRAINT {constraintName} UNIQUE (column1, column2);
```


Example:

```mysql
-- Add a constraint to the Ttiles table so taht UPC is unique
ALTER TABLE Titles
ADD CONSTRAINT un_title_upc UNIQUE (UPC);

-- Add a constraint to the Titles table so that the 
-- combination of ArtistID and Title is unique
ALTER TABLE Titles
ADD CONSTRAINT un_titles_artistid_title UNIQUE (ArtistID, Title);
```


## Unique constraint with create table


Syntax:

```mysql
-- create a single column unique constraint
CREATE TABLE {tableName} (
    {column} datatype not null unique
);

-- Can be used to create combination of unique keys
CREATE TABLE {tableName} (
    {column1} datatype not null,
    {column2} datatype not null,
    CONSTRAINT {constraintName} UNIQUE (column1, column2)
);
```


## Default Constraint


Default value when inserted with `NULL`


### Default Constraints with alter table


Syntax:

```mysql
ALTER TABLE {tableName}
ALTER {column} SET DEFAULT {value};
```


### Default Constraint with create table


Syntax:

```mysql
CREATE TABLE {tableName} (
    {column2} datatype DEFAULT {value}
);
```


Example:

```mysql
CREATE TABLE tbl (
    ID int NOT NULL,
    anotehrColumn int DEFAULT 0 NOT NULL
);
```


## Check Constraints


* A check constraint requires that data match certain patterns or have certain values
* Virtually anything can go in a WHERE clause can be used in a check constraint
    * Test against specific values
    * Test against other columns
    * Cannot test against another table - need stored procedures
* MySQL does not support check constraints
    * In order to do `check` constraint, you will need to know how to use *triggers*


## Foreign Key Constraints


* Foreign key constraint requires that every CD_ID in Child table matches existing CD_ID in parent table
* Foreign key constraint requires the parent_column in parent_table to be primary key


### Foreign Key Constraints with alter table


Syntax:

```mysql
ALTER TABLE child_tablename
ADD CONSTRAINT constraintName FOREIGN KEY (child_column, child_column2) 
REFERENCES parent_tablename (parent_column, parent_column2);
```


Example:

```mysql
-- Create a foreign key relationship to enforce 
-- that StudioID of the Titles table refers to StudioID of the Studios table
ALTER TABLE Titles
ADD CONSTRAINT fk_titles_studios FOREIGN KEY (StudioID)
REFERENCES Studios (StudioID);
-- Note that above may not work until you have 
-- added primary key StudioID in Studios table
```


```mysql
-- Create a foreign key relationship between the 
-- Supervisor column of SalesPeople and SalesID 
-- column of SalesPeople such that Supervisor must be a valid SalesID
ALTER TABLE SalesPeople 
ADD CONSTRAINT fk_salespeople_salespeople FOREIGN KEY (Supervisor)
REFERENCES SalesPeople (SalesID);
-- Note that above may not work until you have added primary key SalesID in Sales table
```


## Foreign key constraint


Syntax:

```mysql
CREATE TABLE { table_name } (
    { column1 } { datatype },
    { column2 } { datatype },
    CONSTRAINT { constraintName } FOREIGN KEY ({ column1 })
    REFERENCES { parent_tablename } ({ parent_column })
);
```


Example:

```mysql
-- Create a table called constracts with the ArtistID
-- from the Artists table and a ContractDate field. 
-- Create a foreign key constraint between ArtistID 
-- and the ArtistID in the Artist table
CREATE TABLE Contracts (
    ArtistID Integer NOT NULL,
    ContractDate SmallDateTime NOT NULL,
    CONSTRAINT fk_contracts_artist FOREIGN KEY (ArtistID)
    REFERENCES Artists (ArtistID)
);
```


## Foreign Key Implications


* child matching parent
* no "orphan" records

* Avoid update error
    * Drop constraint before updating either parent or child
    * Or use `CASCADE UPDATE`



## Cascading update & deletes


* Automatically propagate changes


```mysql
CREATE TABLE Titles (
	TitleID int ,
	ArtistID int NOT NULL ,
	Title varchar (50) NULL ,
	StudioID int NULL ,
	UPC varchar (13) NULL ,
	Genre varchar (15) NULL ,
	CONSTRAINT fk_titles_artist FOREIGN KEY (ArtistID)
    REFERENCES Artists (ArtistID)
	ON DELETE CASCADE
	ON UPDATE CASCADE
);

ALTER TABLE Titles ADD CONSTRAINT fk_titles_studios 
FOREIGN KEY (StudioID) 
REFERENCES Studios (StudioID)
ON DELETE CASCADE;
```



## Dropping Constraints


Syntax:

```mysql
ALTER TABLE tableName
DROP PRIMARY KEY;

ALTER TABLE tableName
DROP CONSTRAINT constraintName;
```


Example:

```mysql
# Drop the fk_constracts_artist constaint on the Contracts table
ALTER TABLE Contracts
DROP FOREIGN KEY fk_contracts_artist;
```



## Field Types


* Number
    * bit
    * int
    * float
    * double


* String
    * varchar
    * text
    * enum


* Date/time
    * Date
    * Datetime
    * timestamp



## Indexes


* Speed up searches, sorts, and joins
* Primary keys are by definition indexes


* Other good index candidates
    * Foreign key columns
    * Non-foreign key columns used regularly to join tables
    * Columns regularly used in WHERE clauses
    * Columns regularly used in ORDER BY clauses


* Performance trade-off
    * Indexes require overhead
    * Sometimes delays to reorganize index
* Database "tuning" through analyzing and adjusting indexes


## Create an index


Syntax:

```mysql
CREATE INDEX indexname ON TableName (column1, column2);
```


Example:

```mysql
# Create an index on SalesID column in Members
CREATE INDEX ix_members_salesid ON Members(SalesID);
```


## Dropping an index


Syntax:

```mysql
DROP INDEX indexName ON tableName;
```


Example:

```mysql
DROP INDEX ix_members_salesid ON Members;
```



## Views


* Temporary table
* Created using `SELECT` statement
* Views reduce complexity for end users and front-end programmers
* Hide sensitive data
* Views can hide many of the differences between database systems


## Creating a view


Syntax:

```sql
CREATE VIEW viewname SQL select_statement;
```


Example:

```sql
# Create a view of just the ArtistName, WebAddress and LeadSource columns of Artists for just those Artists with a WebAddress
CREATE VIEW vwArtistWebs AS
SELECT ArtistName, WebAddress, LeadSource
FROM Artists WHERE WebAddress IS NOT NULL;
```


## Dropping a view


Syntax:

```sql
DROP VIEW viewName;
```


Example:

```sql
# Drop the view vwArtistWebs
DROP VIEW vwArtistWebs;
```


## Stored Procedures & Triggers


* "Functions" in SQL
* More details next week in advanced SQL
