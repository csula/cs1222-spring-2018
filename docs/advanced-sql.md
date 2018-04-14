# Advanced topic in SQL



## Objectives

* Prepared statements
* Store Procedures
* Triggers
* Data Modeling (UML, ER Diagram)



## Prepared Statements

```sql
SELECT *
FROM Artists
WHERE ArtistID = ?;
```


```mysql
# Start by creating preapred statement
PREPARE stmt FROM 'SELECT *
                   FROM Artists
                   WHERE ArtistID = ?';

# In MySQL you can use following syntax to create variable
SET @aid = '1';

# Execute statement with variable
EXECUTE stmt USING @aid;

DEALLOCATE PREPARE stmt;
```



## Store Procedures


### Defining procedure

```mysql
# We start by defining the delimiter so that the SQL command doesn't end with ";"
DELIMITER //
CREATE PROCEDURE GetAllArtists()
  BEGIN
  SELECT * FROM Artists;
  END //
DELIMITER ;
```


### Debugging procedure

```mysql
SHOW PROCEDURE STATUS WHERE db = 'lyric';
```

```mysql
SHOW CREATE PROCEDURE GetAllArtists;
```


### Variables

```mysql
# Syntax to create new variable
DECLARE variable_name datatype(size) DEFAULT default_value;

# Example of createing varialbe and plug SQL result in
DECLARE total_products INT DEFAULT 0;

SELECT COUNT(*) INTO total_products
FROM products
```


### Procedure parameter modes


#### IN

```mysql
DELIMITER //
CREATE PROCEDURE GetArtistsByCity(IN cityName VARCHAR(25))
 BEGIN
 SELECT *
 FROM Artists
 WHERE city = cityName;
 END //
DELIMITER ;
```


```mysql
CALL GetArtistsByCity('London');
CALL GetArtistsByCity('Alverez');
```


#### OUT


```mysql
DELIMITER $$
CREATE PROCEDURE CountArtistsByCity(
 IN cityName VARCHAR(25),
 OUT total INT)
BEGIN
 SELECT count(*)
 INTO total
 FROM Artists
 WHERE city = cityName;
END$$
DELIMITER ;
```


```mysql
CALL CountArtistsByCity('London', @total);
SELECT @total;
```


#### INOUT


```mysql
DELIMITER $$
CREATE PROCEDURE set_counter(INOUT count INT(4),IN inc INT(4))
BEGIN
 SET count = count + inc;
END$$
DELIMITER ;
```


```mysql
SET @counter = 1;
CALL set_counter(@counter,1); -- 2
CALL set_counter(@counter,1); -- 3
CALL set_counter(@counter,5); -- 8
SELECT @counter; -- 8
```



## Triggers


```sql
CREATE TRIGGER `event_name` {BEFORE|AFTER} {INSERT|UPDATE|DELETE}
ON `table_name`
FOR EACH ROW BEGIN
    -- trigger body
    -- this code is applied to every
    -- insert, update, delete row (according to above)
END;
```


```sql
CREATE TABLE Audit(
    ArtistID int NOT NULL,
    ChangeTimeStamp DateTime DEFAULT CURRENT_TIMESTAMP
);
```


```sql
CREATE TRIGGER `artist_audit` AFTER INSERT
ON `Artists`
FOR EACH ROW INSERT INTO audit (ArtistId) VALUES (NEW.ArtistID);
```


### To drop trigger

```sql
DROP TRIGGER `artist_audit`;
```


```sql
delimiter //
CREATE TRIGGER `artist_audit` AFTER INSERT
ON `Artists`
FOR EACH ROW
BEGIN
    INSERT INTO audit (ArtistId) VALUES (NEW.ArtistID);
END;//
delimiter ;
```



## Data Modeling

1. Normalization
2. One to many vs one to one vs many to many
3. Think ahead on queries


### [Normalization](https://en.wikipedia.org/wiki/Database_normalization)

It's a process of organizing columns and tables to reduce data redundancy and improve data integrity.


```
Student {
    id,
    name,
}

Teacher {
    id,
    name,
}

Class {
    id,
    name,
}

Grade {
    classId,
    teacherId,
    studentId,
    name,
    grade,
}
```


### One to many vs one to one vs many to many

```
one to one -> same table

one to many -> additional table with reference key

many to many -> additional reference table
```


### Think ahead!

What would be the common queries?



## JSON data

```json
{
    "name": "Eric",
    "gpa": 3.9,
    "students": [
        "Alice",
        "Bob",
        "Eve"
    ],
    "skills": {
        "mysql": "good",
        "nosql": "good",
        "javascript": "very good"
    }
}
```


### JSON Concepts

* `{}` indicates an **object** like *skills* or the entire object
    * In object, you have *key* and *value* like `"key": "value"`


* boolean like `true` or `false`
* strings like `"string"`
* numbers like `123`, or even `12.33`
* nested object `{"name": "value"}`
* array in object like below
* *null* – empty object


* `[]` indicates a **list** like *students*
* can nest as many levels you want and as many attributes as you want


### JSON in MySQL

```sql
CREATE TABLE students (
    id int,
    data JSON
);
```


```sql
INSERT INTO students VALUES (1, '{
    "name": "Eric",
    "gpa": 3.9,
    "students": [
        "Alice",
        "Bob",
        "Eve"
    ],
    "skills": {
        "mysql": "good",
        "nosql": "good",
        "javascript": "very good"
    }
}');
```


```sql
SELECT `data` -> "$.name" AS Name
FROM students
WHERE id = 1;

SELECT `data` -> "$.students" AS Name
FROM students
WHERE id = 1;

SELECT `data` -> "$.skills.mysql" AS Name
FROM students
WHERE id = 1;
```
