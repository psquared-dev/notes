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

Three main types of CHARATER data:

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

* number types:

Decimals Numbers
1. Decimals represents a whole number plus a fraction of a whole number
2. The fraction is represented by digits following a decimal point

## Fixed-point numbers

numeric(precision, scale)

precision: Maximum number of digits to the left and right of the decimal point
scale: number of digits allowable on the right of the decimal point.

decimal (precision, scale)

e.g. numeric(10,2) will return two digits to the right of the decimal points

## Floating-point numbers

Two types are;

real: allows precision to six decimal digits
double: allows precision to 15 decimal points of precision

Unlike numeric, where we specify fixed precision and scale (e.g. numeric (10,2)), the decimal point in a given column can "float" depending on the number

Data type |  Storage Size | Storage type | Range
----------|---------------|--------------|----------
numeric, decimal|  variable | fixed point | Upto 131072 digits before the decimal point
                                            Upto 1638 digits after the decimal point

real  | 4 bytes | floating point | 6 decimal digits precision
double precision | 8 bytes | floating point |  15 decimal digits precision


lets create our numbers table:

CREATE TABLE table_numbers (
    col_numeric numeric (20.1(5),
    col_real real,
    col_double double precision
);

---------------------------------------------------------------------

Selecting Number data type
############################

1. Use integers whever possible.
2. Decimal data and calcuations needs to be exact, then use numeric or decimal
Float will save space, but be careful about inexactness
3. Choose a big enough number type by looking at your data.
4. With big whole numbers, use bigint only if columns values constrained to fit into either smaller integer or smallint types

---------------------------------------------------------------------

Date/Time data types
#######################

1. assigned to the variable that is supposed to store only the time value.
2. One of the most important types
3. Below is the date/time data types available

                    Stores                      low value       High value
Date                date only                   4713 BC         294276 AD
Time                time only                   4713 BC         5874897 AD
Timestamp           date and time               4713 BC         294276 AD
Timestamptz         date time and timestamp     4713 BC         294276 AD
Interval            store values

---------------------------------------------------------------------

DATE data type
##################

1. Store date values
2. Uses 4 bytes to store datte value
3. By default uses the format YYYY-MM-DD
4. Some good useful keywords
    CURRENT_DATE        stores current date
5. column_name DATE

-- Lets create a sample table with a DATE data type column

CREATE TABLE table_dates (
    id serial primary key,
    employee_name varchar(100) NOT NULL,
    hire date DATE NOT NULL,
    add_date DATE DEFAULT CURRENT_DATE
) ;


---------------------------------------------------------------------


TIME Data type
#####################


1. store the time of day values.
2. Take 8 bytes to store time
3. column_namer TIME (precision)
4. precision = number of fractional digits placed in the second field. precision up to 6    digits.
5. Common Formats

HH:MM               10:30
HH:MM:SS            10:60:25 23:59:59. 24:60:60
HHMMSS              103030

MM:SS.pppppp        03:59.99999
HH:MM:SS.pppppp     03:05:08.777777
HHMMSS.pppppp       030508.777777

Lets create a sample table:


CREATE TABLE table_time(
    id serial primary key,
    class_namee varchar (100) not null,
    start_time TIME NOT NULL,
    end_time TIME NOT NULL
);

Getting current time:
SELECT CURRENT_TIME;

Getting current time with precision:
SELECT CURRENT_TIME (2);

Airthematic operations:
04:00
10:00

SELECT time '12:00' time '04:00' as RESULT;

Using Interval:
interval 'n type'
n = number
type = second, minute, hours, day, mouth, year....

SELECT CURRENT_TIME + interval '2 hours' as result;

---------------------------------------------------------------------

UUID data type
#################


1. UUID Universal Unique Identifier
2. It is a 128-bit quantity generated by an algorithm that make it unique in the known universe using the
same algorithm.
3. Example:

    40e6215d-b5c6-4896-987c-f30f3678f608

32 digits
-hexadecimal digits
-separated by hyphens

4. UUID is much much better than the SERIAL data type when it comes to 'uniquness' across systems as SERIAL data type generates only unique values within a single database.

5. To create a UUID data type in PostgreSQL, you need a third part UUID algorithms to generate UUIDS e.g. uuid-ossp

1. Enable third part UUID extensions first e.g. uuid-ossp

CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

2. Lets generate a sample UUID value first

SELECT uuid_generate_v1();
"cde2bef6-3e50-11eb-92fb-3af9d35059d8"

SELECT uuid_generate_v4();
- 3. Lets crate a sample table 'table_uuid'

CREATE TABLE table_uuid(
    product_id UUID DEFAULT uuid_generate_v1(),
    product_name VARCHAR (100) not null
);

---------------------------------------------------------------------

Array data types
########## ########

1. Every data type has its own companion array type e.g.,
integer has an integer () array type,
character has character [] array type

2. An array data type is named by appending square brackets [()] to the data type name of the array elements

variable [ ]

Lets create a sample table:

CREATE TABLE table_array(
    id SERIAL,
    name varchar (100),
    phones text [] -- our array
) ;

## View the data

SELECT * FROM table_array

## insert some sample data

INSERT INTO table_array (name, phones)
VALUES ('Adam', ARRAY [ '(801)-123-4567',' (819)-555-2222']);

INSERT INTO table_array (name, phones)
VALUES ('Linda', ARRAY [ '(201)-123-4567',' (214)-222-3333']);

## Query data

SELECT * FROM table_array

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
    table_array

---------------------------------------------------------------------


