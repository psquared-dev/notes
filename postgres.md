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

5. To create a UUID data type in PostgreSQL, you need a third part UUID algorithms to generate UUIDS e.g. `uuid-ossp`.

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


