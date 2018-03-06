# Data Manipulation



## Objectives

* Web architecture
* Recap of JOINs
* `INSERT`
* `DELETE`
* `UPDATE`
* Transactions



## Front-end vs. Back-end

![Web Architecture Diagram](https://raw.githubusercontent.com/csula/cs1222-spring-2018/master/notes/imgs/webapp-architecture.png)



## JOINs Recap

* https://sqlbolt.com/
* http://sql-joins.leopard.in.ua/
* https://blog.codinghorror.com/a-visual-explanation-of-sql-joins/
* https://blog.jooq.org/2016/07/05/say-no-to-venn-diagrams-when-explaining-joins/



## INSERT


Syntax:

```sql
INSERT INTO
{table}
[(column, column, column)]
VALUES
(value, value, value);
```


Examples:

```sql
-- Add 'Hip-hop' as a new genre in the Genre table
INSERT INTO Genre
(Genre)
VALUES
('hip-hop');
```


```sql
--# Add new title as TitleID 8 that been recorded by Word (ArtistID 3)
--# called 'Word Live', recorded in StudioID 2 in the Genre 'pop'
INSERT INTO Titles
(TitleID, ArtistID, Title, StudioID, Genre)
VALUES
(8, 3, 'Word Live', 3, 'pop');
```


```sql
--# Add a new sales person as SalesID 5 named Breyce Sanders
--# with initials of bgs, a base salary of 200, and supervisor by SalesID 1
INSERT INTO SalesPeople
VALUES
(5, 'Bryce', 'Sanders', 'bgs', 200, 1);
```


### Rules

* Must apply values to all fiels except:
  * Those that auto-generate
  * Those with default values
  * Those that can have null values


* Foreign key constraints may further limit the kinds of values you can insert into tables
* To explicitly insert a Null value, use the word NULL without quotes
* Cannot use two commas together with nothing between them


### Insert with using a query


Syntax:

```sql
INSERT INTO {table1}
(column, column, column)
SELECT {column, colulm, column}
FROM {table2};
```


Examples:

```sql
-- create AudioFiles
create table AudioFiles (
	TitleID int,
	TrackNum int,
	AudioFormat varchar(50)
);
```


```sql
-- Populate the AudioFile table with all the MP3s from the Tracks table.
INSERT INTO AudioFiles
(TitleID, TrackNum, AudioFormat)
SELECT TitleID, TrackNum, 'mp3'
FROM Tracks
WHERE MP3 = 1;
```


```sql
-- Add a new sales person as SalesID 6, named Courtney Mulligan
-- with initials of cgm, a base salary of 200, and supervised by Bob Bentley
INSERT INTO SalesPeople
(SalesID, FirstName, LastName, Initials, Base, Supervisor)
SELECT 6, 'Courtney', 'Mulligan', 'cgm', 200, SalesID
FROM SalesPeople
WHERE FirstName = 'Bob' AND LastName = 'Bentley';
```



## Delete


Syntax:

```sql
DELETE FROM {table}
WHERE {condition};
```


Examples:

```sql
-- Delete the sales person with SalesID 6
DELETE FROM SalesPeople
WHERE SalesID = 6;

-- Delete the sales person named Courtney Mulligan
DELETE FROM SalesPeople
WHERE FirstName = 'Courtney' AND LastName = 'Mulligan';
```


```sql
-- Delete all records in the AudioFiles table related to the CD, Time Files
DELETE FROM AudioFiles
WHERE TitleID = (
  SELECT TitleID
  FROM Titles
  WHERE Title = 'Time Flies'
);
```



## Update

* `SET field = value`
* `SET field1 = value1, field2 = value2`
* `WHERE`


Syntax:

```sql
UPDATE {table}
SET {column} = {value}, {column}, {value}
WHERE {condition};
```


Examples:

```sql
-- Change the base for SalesID 3 to $200
UPDATE SalesPeople
SET Base = 200
WHERE SalesID = 3;
```


```sql
-- Give sales person Bob Bentley a $50 raise in Base
UPDATE SalesPeople
SET Base = Base + 50
WHERE FirstName = 'Bob' AND LastName = 'Bentley';
```


```sql
-- Member Frank Payne works from his home. Set his workphone number
-- to the value of his homephone number
UPDATE Members
SET WorkPhone = HomePhone
WHERE FirstName = 'Frank' AND LastName = 'Payne';
```


```sql
-- Change the record for the Time Flies to set the UPC to
-- 1828344222 and the Genre to 'pop'
UPDATE Titles
SET UPC = '1828344222', Genre = 'pop'
WHERE Title = 'Time Flies';
```


### Update (multiple-table)


Syntax:

```sql
UPDATE {table1}, {table2}
SET {column} = {value}, {column} = {value}
WHERE {table1.column} = {table2.column} AND {condition};
```


Examples:

```sql
-- All the members of the group confused now have email addresses
-- that are the members first name followed by an @confused.com
-- update the member records appropriately
UPDATE Members m, XRefArtistsMembers x, Artists a
SET Email = Concat(FirstName, '@confused.com')
WHERE m.MemberID = x.MemberID AND a.ArtistID = x.ArtistID AND
ArtistName = 'Confused'
```


```sql
-- All the members who live in Virginia and who have previously
-- been handled by sales person Scott Bull will now be handled
-- by Clint Sanchez
UPDATE Members, SalesPeople
SET SalesID = (
  SELECT SalesID
  FROM SalesPeople
  WHERE FirstName = 'Clint' AND LastName = 'Sanchez'
)
WHERE Members.SalesID = SalesPeople.SalesID AND
  Region = 'VA' AND
  SalesPeople.FirstName = 'Scott' AND
  SalesPeople.LastName = 'Bull';
```



## Transactions

All or nothing


## ACID Properties of Transactions

* **A** tomicity
* **C** onsistency
* **I** solation
* **D** urability


Syntax:

```sql
BEGIN TRANSACTION;
DELETE FROM Tracks WHERE TitleID = 44;
DELETE FROM Titles WHERE TitleID = 44;
COMMIT;
# OR ROLLBACK if there is any error
```



## Concurrency Problems

* Dirty reads
* Unrepeatable reads
* Phantoms
* Lost updates


### Locks help solve concurrency problems

* Automatic locking by database
* Front-end concurrency handling


### Deadlock

![Deadlock](https://raw.githubusercontent.com/csula/cs1222-spring-2018/master/notes/imgs/traffic-deadlock.jpg)


### Solving/Preventing Deadlock

* Some database have build-in procedures for solving deadlocks
* Better to prevent deadlocks (front-end & stored procedure programming)
