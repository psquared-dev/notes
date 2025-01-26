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


### Managing tables

#### Managing temporary tables

Later in this book, we will explore sessions, transactions, and concurrency in more depth. For
now, you simply need to know that a session is a set of transactions, each session is isolated, and
that a transaction is isolated from everything else. In other words, anything that happens inside
the transaction cannot be seen from outside the transaction until the transaction ends. Due to
this, we might need to create a data structure that is visible only within the transaction that is
running. In order to do this, we have to use the temp option.

We will now explore two possibilities. The first possibility is that we could have a table visible
only in the session where it was created. The second is that we might have a table visible in the
same transaction where it was created.

The following is an example of the first possibility where there is a table visible within the session:

```sql
forumdb=> create temp table if not exists temp_users
pk int GENERATED ALWAYS AS IDENTITY
,username text NOT NULL
,gecos text
,email text NOT NULL
,PRIMARY KEY( pk )
,UNIQUE ( username )
);
CREATE TABLE
```

The preceding command will create the `temp_users` table, which will only be visible within the
session where the table was created.

If instead we wanted to have a table visible only within our transaction, then we would have to
add the `on commit drop` options. To do this, we would have to do the following:

1. Start a new transaction.
1. Create the `temp_users` table.
1. Commit or roll back the transaction started in Step 1.

Let’s start with Step 1:

1. Start the transaction with the following code:

```sql
forumdb=> begin work;
BEGIN
forumdb=*>
```

The `*` symbol means that we are inside a transaction block.

2. Create a table visible only inside the transaction:

```sql
forumdb=*> create temp table if not exists temp_users_transaction (
pk int GENERATED ALWAYS AS IDENTITY
,username text NOT NULL
,gecos text
,email text NOT NULL
,PRIMARY KEY( pk )
,UNIQUE ( username )
) on commit drop;
CREATE TABLE
```

Now check that the table is present inside the transaction and not outside the transaction:

```sql
forumdb=*> \d temp_users_transaction
Table "pg_temp_3.temp_users_transaction"
Column| Type | Collation | Nullable | Default
----------+---------+-----------+----------+------------------------------
pk identity | integer |     | not null | generated always as  identity
username | text|    | not null | 
gecos| text|        |
email| text|        | not null ||
Indexes:
"temp_users_transaction_pkey" PRIMARY KEY, btree (pk)
"temp_users_transaction_username_key" UNIQUE CONSTRAINT, btree
(username)
```

3. You can see the structure of the temp_users_transaction table, so now commit the trans-
action:

```sql
forumdb=*> commit work;
COMMIT
```

If you re-execute the `DESCRIBE` command `\d temp_users_transaction`, PostgreSQL responds in this way:

```sql
forumdb=> \d temp_users_transaction
Did not find any relation named "temp_users_transaction".
```

This happens because the `on commit drop` option drops the table once the transaction is completed.


### Managing unlogged tables

We will now address the topic of unlogged tables. For now, we will simply note that unlogged
tables are much faster than classic tables (also known as logged tables) but are not crash-safe.
This means that the consistency of the data is not guaranteed in the event of a crash.

The following snippet shows how to create an unlogged table:

```sql
forumdb=> create unlogged table if not exists unlogged_users (
pk int GENERATED ALWAYS AS IDENTITY
,username text NOT NULL
,gecos text
,email text NOT NULL
,PRIMARY KEY( pk )
,UNIQUE ( username )
);
CREATE TABLE
```

Unlogged tables are a fast alternative to permanent and temporary tables. This per-
formance increase comes at the expense of losing data in the event of a server crash.
If the server crashes after the reboot, the table will be empty. This is something you
may be able to afford under certain circumstances.


### Creating a table

We will now explore what happens behind the scenes when a new table is created. Also, for tables,
PostgreSQL assigns an object identifier called an OID. We have already seen oid2name in Chapter
2, Getting to know your cluster. Now we will see something similar. An OID is simply a number that
internally identifies an object inside a PostgreSQL cluster. Let’s now see the relationship between
the tables created at the SQL level and what happens behind the scenes in the filesystem:

1. To do this, we will use the OIDs and a system table called pg_class, which collects in-
formation about all the tables that are present in the database. So, let’s run this query:

```sql
forumdb=> select oid,relname from pg_class where relname='users';
oid    | relname
-------+---------
16389  | users
(1 row)
```

Here, the `oid` field is the object identifier field, and `relname` represents the relation name
of the object. As seen here, the `forumdb` database is stored in the `16389` directory.

2. Now, let’s see where the `users` table is stored. To do this, go to the `16386` directory using
the following code:

```bash
postgres@learn_postgresql:~$ cd /var/lib/postgresql/16/main/
base/16386
```

3. Once here, execute the following command:

```bash
postgres@learn_postgresql:~/data/base/16386$ ls -l | grep 16389
-rw------- 1 postgres postgres 0 Jan 3 09:13 16389
```

As you can see, in the directory `16386`, there is a file called `16389`. In PostgreSQL, each table is
stored in one or more files. If the table size is less than 1 GB, then the table will be stored in a sin-
gle file. If the table has a size greater than 1 GB, then the table will be stored in two files and the
second file will be called `16389.1`. If the users table has a size greater than 2 GB, then the table
will be stored in three files, called `16389`, `16389.1`, and `16389.2`; the same thing happens for the
`users_username_key` index.

### Understanding basic table manipulation statements

#### Creating a table starting from another table

We will now examine how to create a new table using data from another table. To do this, you
need to create a temporary table with the data present in the categories table as follows:

```sql
forumdb=> create temp table temp_categories as select * from categories;
SELECT 6
```

This command creates a table called `temp_data` with the same data structure and data as the
table called `categories`.







