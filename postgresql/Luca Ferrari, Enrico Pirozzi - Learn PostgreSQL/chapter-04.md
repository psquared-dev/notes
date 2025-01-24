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





