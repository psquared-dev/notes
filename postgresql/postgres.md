1. `su - postgres` - to login as postgres inside the VPS
1. `createuser neil` - to create the user (in bash not psql)
1. Once logged in enter the following to enter psql:  `psql`
1. `create database bpsimple with owner neil` - to create the database with owner
1. `\i data/beg-dbs-w-postgresql-master/UNIX/Chapter03/create_tables-bpsimple.sql ` - to execute the SQL commands in the file.
1. `\c bpsimple neil` - connect to bpsimple db as neil user
1. `\l` - to list all the dbs
1. `\dt` - to list tables in the current db
1. `\conninfo` - to fetch the status
1. `\q` - to exit
1. `psql -U neil -d bpsimple` - connect to bpsimple as user neil.
1. `$ psql -h localhost -p 5432 -U neil -d bpsimple` - to connect to docker postgres from host.
1. `$ pgbench -c 10 -t 1000 -C -U neil -h localhost -p 6432 bpsimple` - 


1. column alias can't be used in the `where` clause but it can be used with `order by` clause:

```sql
select
	movie_id,
	movie_name,
	age_certificate as age
from
	movies m
where age_certificate = '18'	
order by age
```

1. The following is the order in which SQL clause are executed:

```sql
from | where | select | order by
```

1. `fetch` clause is an alternative to `limit` clause. Here is an example:

```sql
select
	*
from
	movies m
fetch first 10 rows only
```

this is same as:

```sql
select
	*
from
	movies m
limit 10 
```

We can also use `offset` caluse with `fetch` as follows:

```sql
select
	*
from
	movies m
fetch first 10 rows only
offset 5
```

`offset` can be written before or after the `fetch` clause.

---------------------------------------------

* `select 'hello' || null || 'world'` outputs `NULL` which is undesired. 

We can use `concat()` or `concat_ws()`.

```sql
select concat(null, '|', null) 
select concat_ws('|', null, null)
```

Both functions ignore the `null` values.

Output:

```bash
NULL
[EMPTY_STRING]
```

---------------------------------------------

## Boolean data type

PostgreSQL supports a single Boolean data type: BOOLEAN that can have three values:

- TRUE,
- FALSE and
- NULL.

Following are some valid literals for boolean values in PostgreSQL

-- must be enclosed in single quotes except for `true` and `false`.

TRUE | FALSE
---- |---------------
TRUE | FALSE
'true' | 'false'
't' | 'f'
'y' | 'n'
'yes' | 'no'
'1' | '0'

---------------------------------------------

## Character data type

Characters strings types are general-purpose types suitable for;

- text,
- numbers, and
- symbols

Three main types of CHARACTER data:

Character | Types Notes
----------|-----------------------
CHARACTER (n), CHAR(n) | fixed-length, blank padded
CHARACTER VARYING (n), VARCHAR(n) | variable-length with length limit
TEXT, VARCHAR | variable unlimited length

where `n` is number of characters that the column holds. If no value is specified then it default to `1`.

If the excessive characters are all spaces, PostgreSQL truncates the spaces to the maximum length (n) and stores the characters.

### CHARACTER (n), CHAR(n)

`char(10)` will store 10 character length. However if you insert less then 10 characters, PostgreSQL will pads the rest of the that column with spaces.

### CHARACTER VARYING (n), VARCHAR(n) (variable-length with length limit)

1. Useful if entries in a column can vary in length but you don't want PostgreSQL to pad the field with blanks.
2. Store exactly the number of characters provided. Save space! :)
3. No default value exist for this type.
4. In here means maximum number of characters


### TEXT variable length column, any size

1. variable-length column type I
2. Unlimited length (per PostgreSQL it say max approx. 1GB )
3. Not part of SQL standard, but similar types available in Microsoft SQL server and MySQL etc.

* use `char` when you know the column will contain fixed no of characters.  If characters are less than the specified lenght they are padded with spaces. If you use `varchar`, then it stores the exact no of characters provided without adding spaces.

---------------------------------------------

## Numbers Data types

1. Numbers columns can hold various type numbers, but not `NULL` values.
2. Math operations (adding, multiplying, divide etc.) can be performed on numbers data type
3. Two main types of Numbers data are:
    * Integers Whole numbers, both +ve and -ve
    * Fixed-point, floating point Two format of fractions of whole numbers

### Integers

1. Most common type
2. Three main types of integers

integer type | size | range
-------------|------|----------------
`smallint` | 2 bytes | -32768 to +32767
`integer` | 4 bytes | -2147483648 to +2147483647
`bigint` | 8 bytes | -9223372036854775808 to +9223372036854775807

3. `bigint` will be good enough for most of the situation if not all! Numbers larger than 2.1 billion
4. Database will give error if a number is outside of its data type range as per above table.
5. auto-increment `integer` type is `serial`.

For serial data type:

serial type | size | range
-------------|------|----------------
`smallserial` | 2 bytes | 1 to 32767
`serial` | 4 bytes | 1 to 2147483647
`bigserial` | 8 bytes | 1 to 9223372036854775807

----------------------------------------

## Decimal Numbers

Decimals Numbers
1. Decimals represents a whole number plus a fraction of a whole number
2. The fraction is represented by digits following a decimal point

### Fixed-point numbers

```bash
numeric(precision, scale)
```

`precision`: Maximum number of digits to the left and right of the decimal point
`scale`: number of digits allowable on the right of the decimal point.

```bash
decimal (precision, scale)
```

E.g: `numeric(10,2)` will return two digits to the right of the decimal points

The `numeric` and `decimal` types in PostgreSQL are functionally equivalent; they both represent fixed-point numbers with user-specified precision and scale. However, historically, they were different types in SQL standards.

### Floating-point numbers

Two types are;

* `real`: allows precision to six decimal digits
* `double precision`: allows precision to 15 decimal points of precision

Unlike numeric, where we specify fixed precision and scale (e.g. `numeric (10,2)`), the decimal point in a given column can "float" depending on the number

Data type |  Storage Size | Storage type | Range
----------|---------------|--------------|----------
`numeric`, `decimal`|  variable | fixed point | Upto 131072 digits before the decimal point Upto 1638 digits after the decimal point
`real`  | 4 bytes | floating point | 6 decimal digits precision
`double precision` | 8 bytes | floating point |  15 decimal digits precision

lets create our numbers table:

```sql
CREATE TABLE table_numbers (
    col_numeric numeric (20,5),
    col_real real,
    col_double double precision
);
```

### Selecting Number data type

1. Use integers whever possible.
2. Decimal data and calcuations needs to be exact, then use numeric or decimal
Float will save space, but be careful about inexactness
3. Choose a big enough number type by looking at your data.
4. With big whole numbers, use bigint only if columns values constrained to fit into either smaller integer or smallint types

---------------------------------------------------------------------

## Date/Time data types

1. assigned to the variable that is supposed to store only the time value.
2. One of the most important types
3. Below is the date/time data types available

Type        |         Stores           |           low value |       High value
------------|--------------------------|---------------------|----------------------------
Date        |         date only               |  4713 BC     |    294276 AD
Time        |         time only               |  4713 BC     |    5874897 AD
Timestamp   |         date and time           |  4713 BC     |    294276 AD
Timestamptz |       date, time and timezone |   4713 BC    |    294276 AD
Interval    |       store values            |      NA        | NA


## DATE data type

1. Store date values
2. Uses 4 bytes to store date value
3. By default uses the format YYYY-MM-DD
4. Some good useful keywords:

```bash
    CURRENT_DATE   -       stores current date
```

5. column_name `DATE`

Lets create a sample table with a `DATE` data type column:

```sql
CREATE TABLE table_dates (
    id serial primary key,
    employee_name varchar(100) NOT NULL,
    hire date DATE NOT NULL,
    add_date DATE DEFAULT CURRENT_DATE
) ;
```

To get current date use `current_date`. Similarly, to get current time and timestamp, use `current_time` and `now()` respectively.

We can also get the local time `localtime; 

### TIME Data type

1. store the time of day values.
2. Take 8 bytes to store time
3. column_namer `TIME(precision)`
4. precision = number of fractional digits placed in the second field. precision up to 6    digits.
5. Common Formats:

```bash
HH:MM               10:30
HH:MM:SS            10:60:25 23:59:59. 24:60:60
HHMMSS              103030

MM:SS.pppppp        03:59.99999
HH:MM:SS.pppppp     03:05:08.777777
HHMMSS.pppppp       030508.777777
```

Lets create a sample table:

```sql
CREATE TABLE table_time(
    id serial primary key,
    class_name varchar (100) not null,
    start_time TIME NOT NULL,
    end_time TIME NOT NULL
);
```


#### Arithematic operations:

```sql
SELECT time '12:00' - time '04:00' as RESULT; -- prints the diff b/w two times
```

#### Using Interval:

Syntax: `interval 'n type'`

```bash
n = number
type = second, minute, hours, day, mouth, year....
```

Example:

```sql
SELECT CURRENT_TIME + interval '2 hours' as result;
```

---------------------------------------------------------------------

## UUID data type

1. UUID - Universal Unique Identifier
2. It is a 128-bit quantity generated by an algorithm that make it unique in the known universe using the same algorithm.
3. Example:

```text
    40e6215d-b5c6-4896-987c-f30f3678f608

    32 digits    
    - hexadecimal digits
    - separated by hyphens
```

4. UUID is much much better than the SERIAL data type when it comes to 'uniquness' across systems as SERIAL data type generates only unique values within a single database.

5. To create a UUID data type in PostgreSQL, you need a third party UUID algorithms to generate UUIDS e.g. `uuid-ossp`.

1. Enable third part UUID extensions first e.g.`uuid-ossp`.

```sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
```

2. Lets generate a sample UUID value first

```sql
movies=> SELECT uuid_generate_v1();
           uuid_generate_v1           
--------------------------------------
 58e8eb7e-15b7-11ef-bc27-0242ac110002
(1 row)

movies=> SELECT uuid_generate_v4();
           uuid_generate_v4           
--------------------------------------
 34141161-91e0-4c94-a02a-be2147bec35e
(1 row)

movies=> 
```

3. Lets crate a sample table 'table_uuid'

```sql
CREATE TABLE table_uuid(
    product_id UUID DEFAULT uuid_generate_v1(),
    product_name VARCHAR (100) not null
);
```

---------------------------------------------------------------------

## Array data types

1. Every data type has its own companion array type. E.g.

```text
`integer` has an `integer[]` array type.
`character` has `character[]` array type.
```

2. An array data type is named by appending square brackets `[]` to the data type name of the array elements

```text
variable_type[]
```

Lets create a sample table:

```sql
CREATE TABLE table_array(
    id SERIAL,
    name varchar(100),
    phones text[] -- our array
);
```

### View the data

```sql
SELECT * FROM table_array
```

### insert some sample data

```sql
INSERT INTO table_array (name, phones)
VALUES ('Adam', ARRAY [ '(801)-123-4567',' (819)-555-2222']),
VALUES ('Linda', ARRAY [ '(201)-123-4567',' (214)-222-3333']);
```

### Query data

```sql
SELECT * FROM table_array;

SELECT
    name
FROM
    table_array
WHERE
    phones [2] = '(214)-222-3333';


SELECT
    name,
    phones [1]
FROM
    table_array;
```

---------------------------------------------------------------------


## hstore data type

1. hstore is a data type that store data into key-value pairs
2. The hstore module implements the hstore data type.
3. The keys and values are just text strings only.

1. Lets install hstore extensions first:

```sql
CREATE EXTENSION IF NOT EXIST hstore;
```

2. Lets create our sample table:

```sql
create table table_hstore(
    book_id serial primary key,
    title varchar(100) not null,
    book_info hstore;
)
```

3. Insert some rows:

```sql
insert
	into
	table_hstore (title,
	book_info)
values 
( 'title1',
'
	"publisher" => "ABC",
	"paper_cost" => "10.00",
	"e_cost" => "5.85",
'
),
( 'title2',
'
	"publisher" => "ABC",
	"paper_cost" => "102.00",
	"e_cost" => "10.85",
'
)
```

4. Selecting rows:

```sql
select
	book_info -> 'publisher',
	book_info -> 'e_cost'
from
	table_hstore th
where CAST( book_info -> 'e_cost' as DECIMAL ) > 6
```

----------------------------------------

### JSON data type

1. PostgreSQL has built-in support for JSON with a great range of processing functions and operators, and complete indexing support.

2. The JSON datatype is actually text under the hood, with a verification that the format is valid json input... much like XML.

3. The JSONB implemented a binary version of the JSON datatype.

4. The JSON datatype, being a text datatype, stores the data presentation exactly as it is sent to PostgreSQL, including whitespace and indentation, and also multiple-keys when present (no processing at all is done on the content, only form validation).

5. The JSONB datatype is an advanced binary storage format with full processing, indexing and searching capabilities, and as such pre-processes the JSON data to an internal format, which does include a single value per key; and also isn't sensible to extra whitespace or indentation.

Example:

1. Let's create a table:

```sql
create table table_json(
	id serial primary key,
	docs json
)
```

2. Insert some rows:

```sql
insert into table_json (docs) 
values 
('[1, 2, 3, 4, 5, 6]'),
('{"key1": "value1", "key2": "value2"}')
```

3. Select rows:

```sql
select
	*
from
	table_json tj
where  docs @> '2'
```

To use `@>` operator we must convert `json` to `jsonb` type.

-----------------------------

## Mofifying table structures

create table persons(
    person_id serial primary key,
    first_name varchar(20) not null,
    second_name varchar(20) not null,
)

Add age column:

```sql
alter table persons
add column age int not null
```

Add nationality column:

```sql
alter table persons
add column nationality varchar(20),
add column email varchar(100) unique
```

Rename a table:

```sql
alter table persons
rename to users
```

Rename a column:

```sql
alter table persons
rename column age to person_age
```

Drop a column:

```sql
alter table persons
drop column person_age
```

Change the datatype of a column:

```sql
alter table persons
alter column age type int
```

If `age` column is declared as `varchar`, then the above statement would throw an error because psql can't automatically convert `varchar` values to `int`.

To accomplish the conversion, we execute the query using `USING` clause as follows:

```sql
alter table persons
alter column age type int
using age::integer
```

Set a default value for a column:

```sql
alter table persons
add column is_enable varchar(1);

alter table persons
alter column is_enable set default 'Y';
```

Define a new table

```sql
create table web_links(
    link_id serial primary key,
    link_url varchar(255) not null,
    link_target varchar(20)
);
```

Adding unique constraint

```sql
alter table web_links
add constraint unique_web_url unique(link_url)
```

Add a new column

```sql
alter table web_links
add column is_enable varchar(2)
```

Set column to accept only defined values

```sql
alter table web_links
add check (is_enable in ('Y', 'N') )    
```

-----------------------------

## Type conversion

1. A data converted from its ORIGINAL data type to ANOTHER data type, it is called 'Type Conversion'.

2. Two type of conversions:

* Implicit data conversion is done AUTOMATICALLY
* Explicit data conversion is done via conversion functions' e.g `CAST` or `::`.

```sql
select * from movies    
where movie_id = 1           -- no conversion here
```

Implicity `string` to `int` conversion:

```sql
select * from movies    
where movie_id = '1'           
```

Here `'1'` is Implicity converted to `int` before comparision is done.

We can also force the conversion as follows:

```sql
select * from movies  where movie_id = integer '1';
```


###  CAST (expression AS target_data_type);

PostgreSQL CAST operator is used to convert a value of one type to another data type.

`expression`: Can be a constar, a table column, or an expression

target_data_type: 

* Boolean
* Character (char, varchar, text)
* Numeric (integer, floating point number)
* array
* JSON
* UUID
* hstore (stores as key/value pairs)
* Temporal type (date, time, timestamp, interval)
* special type (network address, geometric data)


String to integer conversion:

```sql
select cast('10' as int);
```

String to date conversion:

```sql
select 
    cast('2020-01-01' as date),
    cast('01-may-2020' as date);
```

String to boolean

```sql
select 
    cast('true' as boolean),
    cast('false' as boolean),
    cast('t' as boolean),
    cast('f' as boolean);

select 
    cast('0' as boolean),
    cast('1' as boolean);    
```

String to double:

```sql
select 
    cast('14.7888' as double precision);
```

You can also use the following syntax for conversion directly too:

```text
expression::type
```

```sql
SELECT
'10' INTEGER,
'2020-01-01':: DATE,
'01-01-2020':: DATE;
```

String to timestamp:

```sql
SELECT '2020-02-20 10:30:25.467':: TIMESTAMP;
```

with timezone:

```sql
SELECT '2020-02-20 10:300::2546 2 467' TIMESTAMPTZ;
```

String to Interval:

```sql
SELECT
'10 minute' :: interval,
'4 hour' :: interval,
'1 day':: interval,
'1 week':: interval,
'5 month':: interval;
```

Calculating factorial:

```sql
select factorial(5);
```

In older versions, `!` operator was used to calculate factorial. E.g: `select 5 !;`. In current versions, it is removed.

Rounding numbers:

```sql
select round(10, 4) as "results"
```

CAST with text:

```sql
select 
    substr('123456', 2) as "implicit",
    substr(cast('123456' as text), 2) as "explicit";
```


## Conversion Functions

## to_char()

1. PostgreSQL TO_CHAR() function converts
- a timestamp,
- an interval,
- an integer,
- a double precision, or
- a numeric value

to a string.

2. `TO_CHAR (expression, format)`
3. Valid format options for numbers

Format | Description
-------|--------------------------
9       | Numeric value with the specified number of digits
0       | Numeric value with leading zeros
.(period) | decimal point
D | decimal point that uses locale
, (comma) | group (thousand) separator
FM | Fill mode, which suppresses padding blanks and leading zeroes.
PR | Negative value in angle brackets.
S | Sign anchored to a number that uses locale
L | Currency symbol that uses locale
G | Group separator that uses locale
MI | Minus sign in the specified position for numbers that are less than 0.
PL | Plus sign in the specified position for numbers that are greater than 0.
SG | Plusminus sign in the specified position
RN | Roman numeral that ranges from 1 to 3999
TH or th | Upper case or lower case ordinal number suffix


Valid timestamp format options:

Pattern | Description
--------|-------------------------------
Y, YYY  | year in 4 digits with comma
YYYY    | year in 4 digits
YYY     | last 3 digits of year
YY      | last 2 digits of year
Y       |The last digit of year
IYYY    | ISO 8601 week-numbering year (4 or more digits)
IYY     | Last 3 digits of ISO 8601 week-numbering year
IY      | Last 2 digits of ISO 8601 week-numbering year
I       | Last digit of ISO 8601 week-numbering year
BC, bc, AD or ad | Era indicator without periods
B.C., b.c., A.D. ora.d. | Era indicator with periods
MONTH    | English month name in uppercase
Month    | Full capitalized English month name
month    | Full lowercase English month name
ΜΟΝ      | Abbreviated uppercase month name e.g., JAN, FEB, etc.
Mon      | Abbreviated capitalized month name e.g, Jan, Feb, etc.
mon      | Abbreviated lowercase month name e.g., jan, feb, etc.
MM       | month number from 01 to 12
DAY      | Full uppercase day name
Day      | Full capitalized day name
day      | Full lowercase day name
DY       | Abbreviated uppercase day name
Dy       | Abbreviated capitalized day name
dy       | Abbreviated lowercase day name.
DDD      | Day of year (001-366)
IDDD    | Day of ISO 8601 week-numbering year (001-371%; day 1 of the year is Monday of the first ISO week)
DD      | Day of month (01-31)
D       | Day of the week, Sunday (1) to Saturday (7)
ID      | ISO 8601 day of the week, Monday (1) to Sunday (7)
W       | Week of month (1-5) (the first week starts on the first day of the month)
WW      | Week number of year (1-53) (the first week starts en the first day of the year)
IW      | Week number of ISO 8601 week-numbering year (01-53%; the first Thursday of the year is in week 1)
CC      | Century e.g, 21, 22, etc.
J       | Julian Day (nteger days since November 24, 4714 BC at midnight UTC)
RM      | Month in upper case Roman numerals (I-XII; >
rm      | Month in lower ase Roman numerals (1-xti; >
HH      | Hour of day (0-12)
HH12    | Hour of day (0-12)
HH24    | Hour of day (0-23)
ΜΙ      | Minute (0-59)
SS      | Second (0-59)
MS      | Millisecond (000-9999)
US      | Microsecond (000000-999999)
SSSS    | Seconds past midnight (0-86399)
AM, am, PM or pm | Meridiem indicator (without periods)


4. return value will always be `TEXT` data type.

Examples:


```sql
select to_char(100870, '9,99999') 

select
	m.release_date,
	to_char(m.release_date, 'Dy, DD-Mon-YYYY')
from
	movies m 

select
	to_char(timestamp '2020-01-01 10:30:35', 'HH24:MI:SS') 

select
	mr.movie_id,
	mr.revenues_domestic,
	to_char(mr.revenues_domestic, '$999D99') 
from
	movies_revenues mr
```


### to__number

1. PostgreSQL `TO_NUMBER()` function converts a character string to a numeric value.
2. TO_NUMBER (Etring, format)
3. format options are:

Format | Description
-------|-------------------------------
9      | Numeric value with the specified number of digits
0      | Numeric value with leading zeros
. (period)     |  decimal point
D      | decimal point that uses locale
, (comma) | group (thousand) separator
FM | Fill mode, which suppresses padding blanks and leading zeroes.
PR | Negative value in angle brackets.
S | Sign anchored to a number that uses locale
L | Currency symbol that uses locale
G | Group separator that uses locale
ΜΙ | Minus sign in the specified position for numbers that are less than 0.
PL | Plus sign in the specified position for numbers that are greater than 0.
SG | Plus/minus sign in the specified position
RN | Roman numeral that ranges from 1 to 3999
TH or th | Upper case or lower case ordinal number suffix

Examples:

```sql
movies=> select to_number('1420.89', '9999.');
 to_number 
-----------
      1420
(1 row)

movies=> select to_number('$1,420.89', 'L9G999.99');
 to_number 
-----------
   1420.89
(1 row)

movies=> select to_number('1,234,567.89', '9G999g999.99');
 to_number  
------------
 1234567.89
(1 row)

movies=> select to_number('$1,978,299.78', 'L9G999g999.99');
 to_number  
------------
 1978299.78
(1 row)

movies=> 
```

### to_date()

1. PostgreSQL `TO_DATE()` function that helps you convert a string to a date.
2. `TO_DATE(text, format)`
3. Valid format options are:

| Pattern | Description                                                                                             |
|---------|---------------------------------------------------------------------------------------------------------|
| Y, YYY  | Year in 4 digits with comma                                                                            |
| YYYY    | Year in 4 digits                                                                                        |
| YYY     | Last 3 digits of year                                                                                   |
| YY      | Last 2 digits of year                                                                                   |
| Y       | The last digit of year                                                                                  |
| IYYY    | ISO 8601 week-numbering year (4 or more digits)                                                         |
| IYY     | Last 3 digits of ISO 8601 week-numbering year                                                           |
| IY      | Last 2 digits of ISO 8601 week-numbering year                                                           |
| I       | Last digit of ISO 8601 week-numbering year                                                              |
| BC      | Era indicator without periods                                                                          |
| bc      | Era indicator without periods                                                                          |
| AD      | Era indicator with periods                                                                             |
| ad      | Era indicator with periods                                                                             |
| MONTH   | English month name in uppercase                                                                        |
| Month   | Full capitalized English month name                                                                    |
| month   | Full lowercase English month name                                                                      |
| ΜΟΝ     | Abbreviated uppercase month name (e.g., JAN, FEB, etc.)                                                |
| Mon     | Abbreviated capitalized month name (e.g., Jan, Feb, etc.)                                              |
| mon     | Abbreviated lowercase month name (e.g., jan, feb, etc.)                                                |
| MM      | Month number from 01 to 12                                                                             |
| DAY     | Full uppercase day name                                                                                |
| Day     | Full capitalized day name                                                                              |
| day     | Full lowercase day name                                                                                |
| DY      | Abbreviated uppercase day name                                                                         |
| Dy      | Abbreviated capitalized day name                                                                       |
| dy      | Abbreviated lowercase day name                                                                         |
| DDD     | Day of year (001-366)                                                                                  |
| IDDD    | Day of ISO 8601 week-numbering year (001-371; day 1 of the year is Monday of the first ISO week)       |
| DD      | Day of month (01-31)                                                                                   |
| D       | Day of the week, Sunday (1) to Saturday (7)                                                            |
| ID      | ISO 8601 day of the week, Monday (1) to Sunday (7)                                                     |
| W       | Week of month (1-5) (the first week starts on the first day of the month)                              |
| WW      | Week number of year (1-53) (the first week starts on the first day of the year)                         |
| IW      | Week number of ISO 8601 week-numbering year (01-53; the first Thursday of the year is in week 1)        |
| CC      | Century e.g., 21, 22, etc.                                                                             |
| J       | Julian Day (integer days since November 24, 4714 BC at midnight UTC).                                  |
| RM      | Month in upper case Roman numerals (I-XII)                                                              |
| rm      | Month in lowercase Roman numerals (i-xii)                                                               |
| HH      | Hour of day (0-12)                                                                                      |
| HH12    | Hour of day (0-12)                                                                                      |
| HH24    | Hour of day (0-23)                                                                                      |
| ΜΙ      | Minute (0-59)                                                                                           |
| SS      | Second (0-59)                                                                                           |
| MS      | Millisecond (000-9999)                                                                                 |
| US      | Microsecond (000000-999999)                                                                            |
| SSSS    | Seconds past midnight (0-86399)                                                                        |
| AM      | Meridiem indicator (without periods)                                                                   |
| am      | Meridiem indicator (without periods)                                                                   |
| PM      | Meridiem indicator (with periods)                                                                      |
| pm      | Meridiem indicator (with periods)                                                                      |

Examples:

```sql
movies=> select to_date('2010/10/22', 'YYYY/MM/DD');
  to_date   
------------
 2010-10-22
(1 row)

movies=> select to_date('022199', 'MMDDYY');
  to_date   
------------
 1999-02-21
(1 row)

movies=> select to_date('March 07, 2019', 'Month DD, YYYY');
  to_date   
------------
 2019-03-07
(1 row)

movies=> 
movies=> select to_date('2022/02/30', 'YYYY/MM/DD');
ERROR:  date/time field value out of range: "2022/02/30"
movies=> 
```

### to_timestamp()



| Pattern | Description                                                                                           |
|---------|-------------------------------------------------------------------------------------------------------|
| Y, YYY  | Year in 4 digits with comma                                                                          |
| YYYY    | Year in 4 digits                                                                                      |
| YYY     | Last 3 digits of year                                                                                 |
| YY      | Last 2 digits of year                                                                                 |
| Y       | The last digit of year                                                                                |
| IYYY    | ISO 8601 week-numbering year (4 or more digits)                                                       |
| IYY     | Last 3 digits of ISO 8601 week-numbering year                                                         |
| IY      | Last 2 digits of ISO 8601 week-numbering year                                                         |
| I       | Last digit of ISO 8601 week-numbering year                                                            |
| BC      | Era indicator without periods                                                                         |
| bc      | Era indicator without periods                                                                         |
| AD      | Era indicator with periods                                                                            |
| ad      | Era indicator with periods                                                                            |
| MONTH   | English month name in uppercase                                                                       |
| Month   | Full capitalized English month name                                                                   |
| month   | Full lowercase English month name                                                                     |
| ΜΟΝ     | Abbreviated uppercase month name (e.g., JAN, FEB, etc.)                                               |
| DD      | Day of month (01-31)                                                                                  |
| D       | Day of the week, Sunday (1) to Saturday (7)                                                           |
| ID      | ISO 8601 day of the week, Monday (1) to Sunday (7)                                                    |
| W       | Week of month (1-5) (the first week starts on the first day of the month)                              |
| WW      | Week number of year (1-53) (the first week starts on the first day of the year)                         |
| IW      | Week number of ISO 8601 week-numbering year (01-53%; the first Thursday of the year is in week 1)       |
| CC      | Century e.g., 21, 22, etc.                                                                            |
| J       | Julian Day (integer days since November 24, 4714 BC at midnight UTC)                                   |
| RM      | Month in upper case Roman numerals (I-XII)                                                             |
| rm      | Month in lowercase Roman numerals (i-xii)                                                              |
| HH      | Hour of day (0-12)                                                                                     |
| HH12    | Hour of day (0-12)                                                                                     |
| HH24    | Hour of day (0-23)                                                                                     |
| ΜΙ      | Minute (0-59)                                                                                          |
| SS      | Second (0-59)                                                                                          |
| MS      | Millisecond (000-9999)                                                                                 |
| US      | Microsecond (000000-999999)                                                                            |
| SSSS    | Seconds past midnight (0-86399)                                                                        |
| AM      | Meridiem indicator (without periods)                                                                   |
| am      | Meridiem indicator (without periods)                                                                   |
| PM      | Meridiem indicator (with periods)                                                                      |
| pm      | Meridiem indicator (with periods)                                                                      |


Examples:

```sql
movies=> select to_timestamp('2020-10-28 10:30:25', 'YYYY-MM-DD');
      to_timestamp      
------------------------
 2020-10-28 00:00:00+00
(1 row)

movies=> 
movies=> select to_timestamp('2020-10-28 10:30:25', 'YYYY-MM-DD HH:MI');
      to_timestamp      
------------------------
 2020-10-28 10:30:00+00
(1 row)


movies=> select to_timestamp('2020-10-28 22:30:25', 'YYYY-MM-DD HH24:MI');
      to_timestamp      
------------------------
 2020-10-28 22:30:00+00
(1 row)

movies=> select to_timestamp('2020-10-28 22:30:25', 'YYYY-MM-DD HH:MI');
ERROR:  hour "22" is invalid for the 12-hour clock
HINT:  Use the 24-hour clock, or give an hour between 1 and 12.
movies=> 
movies=> select to_timestamp('2020-10-28 22:30:25', 'YYYY-MM-DD HH24:MI');
      to_timestamp      
------------------------
 2020-10-28 22:30:00+00
(1 row)

movies=> 
```

## User defined data types


## CREATE DOMAIN data type

1. `CREATE DOMAIN` statement creates a user-defined data type with a range, optional DEFAULT, NOT NULL and CHECK constraint
2. They have to be unique within a schema scope. Cannot be re-use outside of scope where they are defined.
3. Help to STANDERDIZE your database data types in one place.
4. A domain data type is a COMMON data type and can be RE-USE in multiple columns. Write once and share
5. NULL is default
6. Composite Type: Only Single Value return

Syntax to create new domain is follows:

```sql
CREATE DOMAIN name AS data_type constraint
```

**Example 1:**

```sql
create domain addr varchar(100) not null;


create table locations(
    address addr
);

insert into values (address) values ('123 london');

select * from locations;
```

**Exmaple 2: Create a domain `positive_numeric` which accepts only positive values.**

```sql
create domain positive_numeric int not null check (value > 0);

create table sample(
    sample_id serial primary key,
    value_num positive_numeric
);

insert into sample (value_num) values (10);

select * from sample;
```

**Exmaple 3: Create a domain `us_postal_code` which accepts only valid is postal code.**

```sql
create domain us_postal_code text 
check (
	value ~ '^\d{5}$'
	or value ~ '^\D{5}-\d{4}$'
);

create table addresses(
	address_id serial primary key,
	postal_code us_postal_code
);

insert into addresses (postal_code) values ('10000');

select * from address;
```

**Exmaple 4: Create a domain `proper_email` which accepts only valid email.**

```sql
create domain proper_email varchar(150) 
check ( value ~ '^[a-zA-Z0-9]+@[a-zA-Z0-9]\.[a-zA-Z]{2,5}$' );

create table clients_names
(
	client_name_id serial primary key,
	mail proper_email
);

insert into clients_names  (mail)
values
('aabbb@m.com12');

select * from clients_names;
```

**Exmaple 5: Create an enumeration type domain**

```sql
create domain valid_color varchar(10)
check ( value in ('red', 'green', 'blue'));

create table colors(
	color valid_color
);

insert into colors
values
('red'),
('green');

select * from colors;
```

### To get a list of all domain types in the schema:

```sql
SELECT typname
FROM pg_catalog.pg_type
JOIN pg_catalog.pg_namespace
ON pg_namespace. oid = pg_type.typnamespace
WHERE
typtype = 'd' and nspname = 'public';  -- d is for domain type and nspname for schema
```

### Dropping domain type

Dropping domain can be done as follows:

```sql
drop domain positive_numeric;
```

However, if domain is associated with any table, the above statement would throw an error.

If you still want to proceed with the deletion use the `cascade` keyword as follows:

```sql
drop domain positive_numeric cascade;
```

This will drop the domain as well as the column from assoicated table.


### Composite data types

1. List of field names with corresponding data types
2. Used in a table as a 'column'
3. Used in functions or procedures
4. Can return multiple values, its a composite data type

Syntax:

```sql
CREATE TYPE name AS (fields columns_properties)
```

**Example 1: Create an address composte data type**

```sql
create type address as (
    city varchar(50),
    country varchar(20)
);

create table companies(
    comp_id serial primary key,
    addresses address
);

insert into companies (address) values (row('london', 'uk'));

-- 
-- fetching data
-- (composite_column).field_name
-- when you're dealing with multiple tables use
-- (tablename.composite_column).field_name
-- 

select (address).country from companies;
```

**Example 2: Create a composite inventory_item data type**

```sql
create type inventory_item as (
    product_name varchar(200),
    supplier_id int,
    price numeric
);

create table inventory(
    inventory_id serial primary key,
    item inventory_item
);


insert into table_inventory (item)
values
( row('pen', 10, 4.99) )

select * from inventory_item;

select (item).product_name from inventory_item;
```

**Example 3: Create a currency ENUM data type with currency data**

```sql
create type currency as enum ('usd', 'eur', 'gbp');

-- to access a single value from enum
select 'usd'::currency;

-- add a new value to enum
alter type currency add value 'CHF' after 'GBP';

-- create table using currency type
create table stocks(
	stock_id serial primary key,
	stock_currency currency
);

insert into stocks (stock_currency)
values ('USD');

-- create temporary sample_type
create type sample_type as enum ('ABC', '123');

-- drop the sample_type
drop type sample_type;
```

**Example 4: Alter composite type** 

```sql
create type myaddress(
    city varchar(50),
    country varchar(20)
);

-- rename the type
alter type myaddress rename to my_address;

-- change the owner
alter type my_address owner to postgres;

-- change schema
alter type my_address set schema test_scm;

-- adding an attrbute (means adding a new column)
alter type my_address add attribute street_address varchar (150);
```

**Example 5: Alter an enum data type**

```sql
-- create a enum type
create type mycolors as enum ('green', 'red', 'blue');

-- update a value
alter type mycolors rename value 'red' to 'orange';

-- list all enum values
select enum_range(null::mycolors);

-- adding a new value
alter type mycolors add value 'red' before 'green';
```

**Example 6: An enum type with default value**

```sql
-- first create an enum type
create type status as enum ('pending', 'approved', 'declined');

-- create a table assign default value to the column
create table cron_jobs(
    cron_job_id int,
    status status default 'pending'
);

-- testing with insert
insert into cron_job (cron_job_id) values (1);
```

## Constraint

1. Constraints are like 'gate keepers'
1. Controls the kind of data goes into the database
1. Constraints are the rules enforced on data columns on table
1. These are used to prevent invalid data from being entered into the database.
1. They ensures the accuracy and reliability of the data in the database
1. Constraints can be added on:
    Table: table level constraints are applied to the whole table
    Coloum: column level constraints are applied only to one column


### Types of constraints

Type | Desc
-----|--------------------------------------
`NOT NULL` | Field must have values
`UNIQUE` | Only unique values are allowed
`DEFAULT` | Ability to set DEFAULT values
`PRIMARY KEY` | Uniquely identifies each _ row/record in a database table
`FOREIGN KEY` | Constrains data based on columns in other tables
`CHECK` | Checks all values meet specific criterias

### NOT NULL constraints

1. `NULL` represents unknown or information missing.
1. `NULL` is not the same as an empty string or the number zero.
1. To check if a value is `NULL` or not, you use the `IS NULL` boolean operator
1. You create `NOT NULL` constraint on a table column as follows;

```sql
CREATE TABLE table_name (
    column_name data_type NOT NULL,
) ;
```

Example:

```sql
--  create table
create table table_m(
    id serial primary key,
    tag text not null
);

select * from table_m;

insert into table_n (tag) values ('ADAM');

-- this will result in error
insert into table_n (tag) values (null);

-- empty string is allowed
insert into table_n (tag) values ('');
```

**Example 2: Adding not null constraint to an existing table**

```sql
create table table_nn2(
    id serial primary key,
    tag text
);

alter table table_nn2
alter column tag set not null;
```

### UNIQUE constraints

1. make sure that values stored in a column or a group of columns are unique
1. INSERT: When a `UNIQUE` constraint is in place, every time you insert a new row, it checks if the value is already in the table.
1. UPDATE:  The same process is carried out for updating existing data.
1. You create `UNIQUE` constraint on a single column, we use the following syntax;

**Example 1: Creating column with `Unique`  constraint**

```sql
CREATE TABLE table_emails (
    id serial primary key,
    email text unique
);
```

**Example 2: Creating `Unique` constraint on multiple columns**

```sql
CREATE TABLE table_products (
    id serial primary key,
    product_code varchar(10),
    product_name text,
);


alter table table_products
add constraint unique_product_code unique (product_code, product_name);
```

### DEFAULT constraint

**Example 1: Seta default value on a new table**

```sql
create table employees(
    employee_id serial primary key,
    first_name varchar(50),
    second_name varchar(50),
    is_enable varchar(2) default 'Y',
);
```

**Example 2: Seta default value on existing table**

```sql
create table employees(
    employee_id serial primary key,
    first_name varchar(50),
    second_name varchar(50),
    is_enable varchar(2) default 'Y',
);

-- set column, assuming default doesn't exist in the is_enable column
alter table employees
alter column is_enable set default 'N';

alter table employees
alter column is_enable drop default;
```

### PRIMARY KEY Constraint

1. uniquely identifies each record in a database table
1. There can be more UNIQUE columns, but only one primary key in a table
1. A primary key is a field in a table, which uniquely identifies each row/record in a database table
1. Primary keys must contain unique values
1. A table can have only one primary key, which may consist of single or multiple fields
1. When multiple fields are used as a primary key, they are called a composite key
1. They are same as `UNIQUE NOT NULL`


**Example 1: Creating Primary Key on a single column**

```sql
CREATE TABLE table_items (
    item_id INTEGER PRIMARY KEY,
    item_name VARCHAR (100) NOT NULL
);
```

This creates a primary key with the constraint name:

```text
table_items_pkey
```

**Example 2: Dropping Primary Key on a table**

```sql
alter table table_items
drop constraint table_items_pkey;
```

**Example 3: Adding primary key to existing table**

```sql
alter table table_items
add primary key (item_id);

--  we can also add multiple columns to the primary key
-- this is called composite key
alter table table_items
add primary key (item_id, item_name);

-- we can also define composite key while defining the table
CREATE TABLE table_items (
    item_id INTEGER,
    item_name VARCHAR (100) NOT NULL,
    primary key (item_id, item_name)
);
```

### Foreign Key Constraint

1. Foreign key play a vital most important role in PostgreSQL.
1. A foreign key is a column or a group of columns in a table that reference the primary key of another table
1. Also in general words, foreign key in PostgreSQL is defined as the first table that has
reference to the primary key of the second table.

The following is the syntax to create foreign key constraint:

```sql
CREATE TABLE child_table_name (
    columnname data_type PRIMARY KEY,
    foreign key (some_column_name) references parent_table (column_name)
);
```

**Example 1: Create foreign key**

```sql
-- parent table
CREATE TABLE t_suppliers(
    supplier_id INT PRIMARY KEY,
    supplier_name VARCHAR (100) NOT NULL
);

-- child table
CREATE TABLE t_products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR (100) NOT NULL,
    supplier_id INT NOT NULL,
    FOREIGN KEY (supplier_id) REFRENCES t_suppliers (supplier_id)
);
```

The foreign key constraint name have the following convention:

```text
tablename_columnname_fkey
```

We can delete the foreign key constraint as follows:

```sql
alter table t_products
drop constraint t_products_supplier_id_fkey;
```

**Example 2: Adding foreign key to an existing table**

```sql
ALTER TABLE t_products
ADD CONSTRAINT t_products_supplier_id_fkey FOREIGN KEY (supplier_id) REFERENCES t_suppliers (supplier_id);
```


### Check constraint

1. A CHECK constraint is a kind of constraint that allows you to specify if values in a column must meet a specific requirement.
1. The CHECK constraint uses a Boolean expression to evaluate the values before they are inserted or updated to the column.
1. If the values pass the check, PostgreSQL will insert or update these values to the column. Otherwise, PostgreSQL will reject the changes and issue a constraint violation error.


**Example 1: Creating check constraint on new table**

```sql
CREATE TABLE staff (
    staff_id SERIAL PRIMARY KEY,
    first_name VARCHAR (50),
    last_name VARCHAR (50),
    birth_date DATE CHECK (birth_date > '1900-01-01'),
    joined_date DATE CHECK (joined_date > birth_date),
    salary numeric CHECK (salary > 0)
);
```

**Example 2: Creating check constraint on existing table**

```sql

-- Define CHECK constraint for existing tables
CREATE TABLE prices (
    price_id SERIAL PRIMARY KEY,
    product_id INT NOT NULL,
    price NUMERIC NOT NULL,
    discount NUMERIC not null,
    valid from DATE not null
);

-- adding check constraint
alter table prices
add constraint price_check 
check (
    price > 0
    and discount >= 0
    and price > discount
);

-- renaming constraint
alter table prices
rename constraint price_check to price_discount_check;

-- dropping constraint
alter table prices
drop constraint price_discount_check;
```

## Sequences

Sequences:


**Example 1: Create a sequence**

```sql
create sequence if not exists test_seq;
```

**Example: 2: Advance sequence and return new value**

```sql
select nextval('test_seq');
```

**Example 3: Return most current value of the sequence**


```sql
select currval('test_seq');
```

**Example 4: Set a sequence value**

```sql
select setval('test_seq', 100) 
```

The `setval()` accepts a third argument called `is_called`. The `is_called` is a boolean value indicating whether the sequence's `last_value` should be set to the new value. If `is_called` is `true`, the sequence's `last_value` will be set to the `new_value`. If is_called is `false`, the sequence's `last_value` will not be changed.

**Example 5: Create sequence with start value**
create sequence if not exists test_seq_2 start with 200

### Altering Sequences

**Example 1: Restarting Sequence**

```sql
alter sequence test_seq restart with 200;
```

**Example 2: Renaming Sequence**

```sql
alter sequence test_seq rename to new_test_seq;
```


### Creating Sequence with START WITH, INCREMENT, MINVALUE, MAXVALUE

```sql
create sequence if not exists test_seq3
increment 50
minvalue 400
maxvalue 5000
start with 500
```

### Creating a sequence using a specific data type

Specify the data type of a sequence (`SMALLINT` | `INT` | `BIGINT`). Default is `BIGINT`.

```sql
create sequence if not exists test_seq_smallint as smallint;

create sequence if not exists test_seq_int as int;
```

### Creating a descending sequence

```sql
create sequence seq_desc
increment -1
minvalue 1
maxvalue 3
start 3
cycle
```

* `CYCLE` in Ascending Sequence: When reaches `MAXVALUE`, reset to `MINVALUE`
* `CYCLE` in Descending Sequence: When reaches `MINVALUE`, reset to `MAXVALUE`


### Deleting Sequence

```sql
drop sequence test_seq;
```

### Attach Sequence to table

```sql
-- create the table
create table users2(
    user_id int primary key,
    user_name varchar(50)
);

create sequence users2_user_id start with 100 owned by users2.user_id;

alter table users2 
alter column user_id set default nextval('users2_user_id');
```

The `OWNED BY` clause ensures that the sequence is automatically dropped when the owning column or table is dropped.


### Listing all sequence in a database

```sql
SELECT relname sequence_name
FROM pg_class
WHERE
relkind = 'S'
```

### Sharing Sequence

```sql
-- create a sequence
create sequence common_fruit_seq start with 100;

-- create table
create table apples(
    fruit_id int default nextval('common_fruit_seq') not null,
    fruit_name varchar(50)
);

-- create another table
create table mangoes(
    fruit_id int default nextval('common_fruit_seq') not null,
    fruit_name varchar(50)
);
```

### Creating Alphanumeric Sequence

```sql
create sequence alpha_seq;

create table contacts(
	contact_id text not null default ('ID' || nextval('alpha_seq')),
	contact_name varchar(150)
);

insert into contacts (contact_name)
values ('aa');
```

## String Functions

```sql
-- returns n characters from the left
select left('abcdef', 2)

-- returns everything except the last two chatacters
select left('abcdef', -2)

-- returns everything except the first two chatacters
select right('abcd', -2)

-- returns n characters from the right
select right('abcd', 1)

-- convert to uppercase
select  upper('aaaBBcc aabb')

-- convert to lowercase
select lower('aaaBBBcccc Abbc')

-- capitalize first char of every word in the string
select initcap('aaabbbbcc bbcc') 

-- reverse the string
select reverse('abcdf') 

-- PostgreSQL SPLIT_PART() function splits a string on a specified delimiter and returns the nth substring.
-- SPLIT PART (string, delimiter, position)
select split_part('a,b,c', ',', 2) -- output b

select split_part('one|two|three', '|', 1)

-- note that conversion from date to varchar is required, otherwise an error will be thrown
select split_part(current_date::varchar, '-', 1)

-- trim from left
select ltrim('         abcdef')

-- trim from right
select rtrim('abcdef          ')

-- trim from both side
select btrim('         abcdef          ')

-- additional characters to trip can be specifies using the second argument
-- trim space and char 'a'
select ltrim('         abcdef', ' a'); 


-- #########################
/*
LPAD function pads a string on the left to a specified length with a sequence of characters.
RPAD function pads a string on the right to a specified length with a sequence of characters.
*/

--LPAD (string, length[, fill])
--RPAD (string, length[, fill])

--The fill argument is optional. If you omit the fill argument, its default value is a space.

select lpad('db', 15, '*');

      lpad       
-----------------
 *************db
(1 row)



select rpad('db', 15, '*');

      rpad       
-----------------
 db*************
(1 row)


--length() and char_length()  both returns the number of charcters in the string

SELECT LENGTH('こんにちは');
SELECT char_LENGTH('こんにちは');

-- this differs from other dbs like MySQL, where length() returns 
-- length of the string measured in bytes. 
-- and CHAR_LENGTH() returns the length of the string measured in characters.

-- POSITION function
-- ###########################
/*
1. PostgreSQL POSITION() function returns the location of a substring in a string.
2. POSITION (substring in string)
3. returns an integer that represents the location of the substring within the string
4. returns the first instance of the substring in the string.
5. searches for the substring case-insensitively.
I
*/

select position ('po' in 'postgres');

 position 
----------
        1
(1 row)


select position ('ipo' in 'postgres');

 position 
----------
        0
(1 row)


select strpos('postgres', 'po');

 strpos 
--------
      1
(1 row)

--Difference between STRPOS and POSITION function
--1. Those functions do the exactly same thing and differ only in syntax. Documentation for strpos() says:
--Location of specified substring (same as position (substring in string), but note the reversed argument order)
--2. Reason why they both exist and differ only in syntax is that POSITION (str1 IN str2) is defined by ANSI SQL
--standard.
--If PostgreSQL had only strpos() it wouldn't be able to run ANSI SQL queries and scripts.


--  extract a substring from a string.
select substring('abc def', 1, 2);

 substring 
-----------
 ab
(1 row)


select repeat('*', 10);

   repeat   
------------
 **********
(1 row)


select replace('aa bb cc dd ae', 'a', '$');

    replace     
----------------
 $$ bb cc dd $e
(1 row)
```

## Aggregate functions

```sql
select
	count(*)
from
	movies m 
	
	
select
	count(movie_length)
from
	movies m	
	

select
	count( distinct movie_lang)
from
	movies m	

select count(director_id ) from movies m 

select count(distinct  director_id ) from movies m 

select sum(revenues_domestic) from movies_revenues mr 

select min(movie_length)  from movies m 

select max(movie_length)  from movies m 

select greatest(10, 20, 40)

select least (10, 20, 40)

select avg(m.movie_length)::numeric(10,2) from movies m 

select round(avg(m.movie_length), 2) from movies m 
```

p         |  c         |  pk
----------|------------|--------------------
veg       | veg        | 1
fruits    | fruits     | 2


select tt.c as p, items.name as c, items.pk, from items
inner join tt
on tt.pk = items.parent

p         |  c         |  pk
----------|------------|--------------------
fruits    | apple      | 3
fruits    | banana     | 4


p         |  c                |  pk
----------|-------------------|--------------------
apple    | apple red         | 5
apple    | apple orange      | 6