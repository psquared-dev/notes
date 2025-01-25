## Creating and managing databases

### Creating a database

To create a database named databasename from scratch, you will need to execute this simple
statement:

```sql
CREATE DATABASE databasename;
```

Now, let’s see what happens behind the scenes when we create a new database. PostgreSQL
performs the following steps:

1. Makes a physical copy of the template database, `template1`
1. Assigns the database name to the database just copied

The `template1` database is a database that is created by the `initdb` process during the initialization of the PostgreSQL cluster.

### PostgreSQL and the public schema

Starting from PostgreSQL 15, PostgreSQL has changed the way to manage the public schema. In
this section, we will see how it works. Before PostgreSQL 15, any user was able to perform any
DDL operation on the public schema. PostgreSQL 15 introduces the concept of removing global
privileges from the public schema.
Starting from PostgreSQL 15:

* A normal user will not be able to execute DDL on the public schema.
* A normal user will not be able to perform DML on the public schema unless they receive permission from a superuser.


### The search_path variable

PostgreSQL has many system variables. One of them is called `search_path`. The `search_path`
variable contains the sequence of schemas that PostgreSQL uses to find tables; the `search_path`
default value is `$user,public`. This means that first it will search all the tables in the schema that
have that name in the user table and then it will search the public schema.

For example, if we have a user called `forum`, and we want to show all the records that are present
in a table called `cities`, first PostgreSQL will search the `cities` table in the `forum` schema, and
if the `cities` table cannot be found in the `forum` schema, PostgreSQL will search for the `cities`
table in the public schema.

PostgreSQL also consults the `search_path` while creating the table. This means PostgreSQL will:

* First check for a schema with the same name as the current user.
* If no such schema exists, it defaults to the public schema.


### Making a new database from a modified template

Let’s start making the database using the following steps:

1. Connect to the `template1` database:

```bash
postgres@learn_postgresql:/$ psql template1
template1=#
```

2. As superuser, create a table called `dummytable`. For now, we don’t need to worry about
the exact syntax for creating tables; this will be explained in more detail later on:

```bash
template1=# create table dummytable (dummyfield integer not null
primary key);
CREATE TABLE
```

3. Use the `\dt` command to show a list of tables that are present in the `template1` database:

```bash
template1=# \dt
List of relations
Schema  | Name       | Type   | Owner
--------+------------+-------+----------
public  | dummytable | table | postgres
(1 row)
```

4. So, we have successfully added a new table to the `template1` database. Now let’s try to cre-
ate a new database called `dummydb` and make a list of all the tables in the `dummydb` database:

```bash
template1=# create database dummydb;
CREATE DATABASE
template1=# \c dummydb
You are now connected to database "dummydb" as user "postgres".
```

The `dummydb` database contains the following tables:

```bash
dummydb=# \dt
List of relations
Schema  | Name       | Type  | Owner
--------+------------+-------+----------
public | dummytable | table | postgres
(1 row)
```

As expected, in the `dummydb` database, we can see the table created previously in the `template1`
database.


### Making a database copy

Make a copy of the `forumdb` database on the same PostgreSQL cluster by performing the
following command:

```bash
template1=# create database forumdb2 template forumdb;
CREATE DATABASE
```

By using this command, you are simply telling PostgreSQL to create a new database called
forumdb2 using the forumdb database as a template.


### Confirming the database size

We are now going to address the question of how one can determine the real size of a database.
There are two methods you can use to do this: psql and SQL. Let’s compare the two in the following sections.


#### The psql method

```sql
dummydb2=# \l+ dummydb2
List of databases
-[ RECORD 1 ]-----+-----------
Name              | dummydb2
Owner             | postgres
Encoding          | UTF8
Locale Provider   | libc
Collate           | en_US.utf8
Ctype             | en_US.utf8
Locale            | 
ICU Rules         | 
Access privileges | 
Size              | 7579 kB
Tablespace        | pg_default
Description       | 

dummydb2=# 
```

#### The SQL method

```sql
dummydb2=# select pg_database_size('dummydb');
 pg_database_size 
------------------
          7744659
(1 row)

dummydb2=# 
dummydb2=# select pg_size_pretty(pg_database_size('dummydb'));
 pg_size_pretty 
----------------
 7563 kB
(1 row)

dummydb2=# 
```

The `pg_database_size(name)` function returns the disk space used by the database in bytes. To convert bytes into more readable form use `pg_size_pretty()`.

### Behind the scenes of database creation

`pg_database` is a system catalog table in PostgreSQL that contains information about all the databases in the current PostgreSQL instance (cluster). It provides metadata about each database, such as its name, owner, encoding, collation settings, and other configuration details.

```sql
dummydb2=# select * from pg_database where datname='dummydb';
-[ RECORD 1 ]--+-----------
oid            | 773428
datname        | dummydb
datdba         | 10
encoding       | 6
datlocprovider | c
datistemplate  | f
datallowconn   | t
dathasloginevt | f
datconnlimit   | -1
datfrozenxid   | 731
datminmxid     | 1
dattablespace  | 1663
datcollate     | en_US.utf8
datctype       | en_US.utf8
datlocale      | 
daticurules    | 
datcollversion | 2.31
datacl         | 

dummydb2=# 
```

This query gives us all the information about the `dummydb` database. The first field is an Object 
Identifier (OID), which is a number that uniquely identifies the database called `dummydb`.

```bash
postgres@716d46d91c8d:/$ ls -il /var/lib/postgresql/data/base/
total 124
30646346 drwx------ 2 postgres postgres  4096 Jan 25 01:12 1
30646360 drwx------ 2 postgres postgres 12288 Jan 24 01:26 101617
30646357 drwx------ 2 postgres postgres 12288 Dec 27 07:16 16384
30646359 drwx------ 2 postgres postgres 12288 Jan 24 01:26 19725
30646320 drwx------ 2 postgres postgres 12288 Jan 24 01:26 20883
30646358 drwx------ 2 postgres postgres 12288 Jan 24 01:26 33608
30646355 drwx------ 2 postgres postgres  4096 Jan 24 02:30 4
30646356 drwx------ 2 postgres postgres 12288 Jan 24 02:37 5
30728293 drwx------ 2 postgres postgres 12288 Jan 24 02:30 765180
30744986 drwx------ 2 postgres postgres  4096 Jan 18 03:50 773395
30769563 drwx------ 2 postgres postgres  4096 Jan 18 03:50 773396
30777755 drwx------ 2 postgres postgres  4096 Jan 18 03:51 773397
30785801 drwx------ 2 postgres postgres  4096 Jan 24 01:26 773401
30785650 drwx------ 2 postgres postgres  4096 Jan 24 01:26 773416
30818476 drwx------ 2 postgres postgres  4096 Jan 25 01:13 773428
30818649 drwx------ 2 postgres postgres  4096 Jan 25 01:30 773429
30646321 drwx------ 2 postgres postgres  4096 Jan 15 03:09 pgsql_tmp
postgres@716d46d91c8d:/$ 
```

As you can see, there is a directory called 773428; its name is exactly the same as the OID in the
`pg_database` catalog.




