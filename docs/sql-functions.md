# SQL functions


Database specific functions to do things where SQL standard didn't define.



## Objectives

* Concatenate text
* Text manipulation
* Date manipulation
* Test for and manipulate null values
* Convert data from one data type to another
* Write SQL with If and Choose logic


## Metrics

* Able to perform SQL functions in MySQL



### Concatenation

* Concatenation is the combination of two or more text values into one text value
* You can concatenate multiple fields or fields with constant text


Syntax:

```sql
SELECT CONCAT({column}, {column})
FROM {table};
-- OR
SELECT CONCAT('Constant Text', {column})
FROM {table};
-- OR
SELECT CONCAT_WS({seperator}, {column1}, {column2}, ...)
FROM {table};
```


Examples:

```sql
-- List the full name of each member
SELECT CONCAT_WS(' ', FirstName, LastName) AS FullName
FROM Members;

-- Report the city, state, and zip of each member,
-- formatted as City, State Zip.
SELECT CONCAT(City, ', ', Region, ' ', PostalCode) AS MailAddr
FROM Members;

-- Report each lead source preceded by the text, 'source: ' along
-- with a count of the number of artists leads from that source
SELECT CONCAT('Source: ', LeadSource) AS Source,
  COUNT(*) AS NumLead
FROM Artists
GROUP BY Source;
```



### Text functions in MySQL

* LENGTH(expression)
* RIGHT(expression, length)
* SUBSTRING(expression, start, length)
* LOCATE(searchText, text, start)


* TRIM(expression), LTRIM(expression), RTRIM(expression)
* UPPER(expression), LOWER(expression)
* LPAD(expression, num_char, pad_chars), RPAD(expression, num_char, pad_chars)



### Date manipulation

* NOW(), CURDATE(), CURTIME()
* DAYOFMONTH(expression), MONTH(expression), YEAR(expression)
* ADDDATE(expression, interval expression type), SUBDATE(expression, interval expression type)
* TO_DAYS(date)
* LAST_DAY(date)



### Data type conversions


Syntax in MySQL:

```sql
SELECT CAST({expression} AS {data type});
SELECT CONVERT({data type}, {expression});
SELECT TRUNCATE({number}, {num_decimal_places});
-- data type must be one of the following:
-- binary, date, datetime, singed {integer}, time, unsigned {integer}
```



### Handling nulls in MySQL

* ISNULL(column)
* IFNULL(column, defaultValue)


Example syntax in MySQL:

```sql
-- List the web address of each artist and whether or not the web address is null
SELECT WebAddress, ISNULL(WebAddress) AS IS_IT_NULL
FROM Artists;

-- List the web address of each artist. If the web address is null, list 'no website'
SELECT IFNULL(WebAddress, 'No website')
FROM Artists;
```



### Miscellaneous functions


Syntax:

```sql
SELECT GREATEST(number1, number2, number3);
SELECT LEAST(number1, number2, number3);
FORMAT(number, num_decimal_places);
```


Examples

```sql
-- List the largest of the following numbers: 3, 5, 7, and 9
SELECT GREATEST(3, 5, 7, 9);

-- List any tracks that lack either an MP3 or Real Audio recording.
SELECT TrackTitle
FROM Tracks
WHERE LEAST(MP3, RealAud) = 0;

-- Report the name and yearly base of each salesperson formatted with comma and two decimal places
SELECT CONCAT(FirstName, LastName), FORMAT(Base * 52, 2)
FROM SalesPeople;
```



### Formatting date


```sql
SELECT ArtistName, DATE_FORMAT(entrydate, '%m-%d-%y') AS Entered
FROM Artists;
```



### Control flow


Syntax:

```sql
-- switch case
SELECT CASE {value}
  WHEN {condition1} THEN {result}
  WHEN {condition2} THEN {result}
  ELSE {else_result}
END;
```


```sql
-- if statement
SELECT IF({condition}, {true_result}, {false_result});
```


Example:

```sql
SELECT CASE 1
  WHEN 1 THEN 'one'
  WHEN 0 THEN 'zero'
  ELSE 'NaN'
END;

SELECT IF(1 < 2, '1 is less than 2', 'wtf');
```
