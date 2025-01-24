## Introduction to users and groups

PostgreSQL distinguishes between users and groups of users: the former represents someone, either
a person or an application, that could connect to the cluster and perform activities; the latter
represents a collection of users that share some common properties, most commonly permissions
on cluster objects.

Database users are somewhat similar to operating system users: they have a username and an
(encrypted) password and are known to the PostgreSQL cluster. Similarly to operating system
users, database users can be grouped into user groups in order to make their management easier.

In SQL, and therefore also in PostgreSQL, the concepts of both a single user account and a group
of accounts are encompassed by the concept of a role.

A role can be a single account, a group of accounts, or even both depending on how you design it;
however, in order to make management easier, a role should express one and only one concept
at a time: that is, it should be either a single user or a single group, but not both.

A role represents a collection of database permissions and connection properties. The two elements are 
orthogonal.

It is important to understand that a role is defined at the cluster level, while permissions are
defined at the database level. This means that the same role can have different privileges and
properties depending on the database it is using (for instance, being allowed to connect to one
database and not to another).

Since a role is defined at the cluster level, it must have a unique name within the
entire cluster.

## Managing Roles

A role is identified by a string that represents the role name, or better, the account name of that
role. This name must be unique across the system, meaning that you cannot have two different
roles with identical names. Names must consist of letters, digits, and some symbols, such as
underscores.

### Creating new roles

```sql
CREATE ROLE name [ [ WITH ] option [ ... ] ]
```

The options that you can specify in the statement range from the account password, the ability
to log in interactively, and the superuser privileges. Please remember that, unlike other systems,
in PostgreSQL, you can have as many superusers as you want, and everyone has the same live-
or-die rights on the cluster.

Almost every option of the `CREATE ROLE` statement has a positive form that adds the ability to the
role, and a negative form (with a `NO` prefix) that excludes the ability from the role. As an example,
the `SUPERUSER` option adds the ability to act as a cluster superuser, while the `NOSUPERUSER` option
removes it from the role.


Here are some examples:

```sql
CREATE ROLE luca
WITH PASSWORD 'xxx';

CREATE ROLE luca
WITH PASSWORD 'xxx' LOGIN;

CREATE ROLE luca
WITH LOGIN PASSWORD 'xxx'
VALID UNTIL '2030-12-25 23:59:59';

CREATE ROLE developer 
WITH CREATEDB CREATEROLE LOGIN PASSWORD 'devpass';
```

### Using a role as a group

Usually, when you want to create a group, all you need to do is create a role without the LOGIN
option and then add all the members one after the other to the containing role. Adding a role to
a containing role makes the latter a group.

In order to create a role as a member of a specific group, the IN ROLE option can be used.

```sql
postgres=# CREATE ROLE book_authors
WITH NOLOGIN;
CREATE ROLE
postgres=#
postgres=#
postgres=# CREATE ROLE luca
WITH LOGIN PASSWORD 'xxx'
IN ROLE book_authors;
CREATE ROLE
postgres=#
postgres=#
postgres=# CREATE ROLE enrico
WITH LOGIN PASSWORD 'xxx'
IN ROLE book_authors;
CREATE ROLE
postgres=#
```

It is also possible to add members to a group using the special `GRANT` statement. The `GRANT` statement is the general SQL statement that allows fine privilege tuning (more on this in Chapter 10,
Users, Roles, and Database Security); PostgreSQL extends the SQL syntax allowing the granting of a
role to another role. When you grant a role to another, the latter becomes a member of the former.

The following adds the role `enrico` to the `book_authors` group:

```sql
postgres=# GRANT book_authors TO enrico;
```

Every group can have one or more admin members, which are allowed to add new members to
the group. The `ADMIN` option allows a user to specify the member that will be associated as an
administrator of the newly created group. For instance, in the following code block, you can see
the creation of the new group called book_reviewers with `luca` as administrator; this means
that the user `luca`, even if he is not a cluster superuser, will be able to add new members to the
`book_reviewers` group:

```sql
postgres=# CREATE ROLE book_reviewers
WITH NOLOGIN
ADMIN luca;
CREATE ROLE
```

The `GRANT` statement can also be used to achieve the same thing as above:

```sql
postgres=# GRANT book_reviewers
TO enrico
WITH ADMIN OPTION;
GRANT ROLE
```

What happens if a group role has the LOGIN option? The group will still be a role container, but it
can act also as a single user account with the ability to log in. While this is possible, it is a more
common practice to deny group roles access to log in to prevent confusion.

### Removing an existing role

```sql
DROP ROLE [ IF EXIST ] name [, ...]
```

What happens if you drop a group? Member roles will stay in place, but of course, the association
with the group will be lost (since the group has been deleted). In other words, deleting a group
does not cascade to its members.


### Inspecting existing roles

```sql
postgres=# SELECT current_role;
current_role
--------------
postgres
(1 row)
```

To view all the roles in the system execute following:

```sql
select * from pg_roles;
```

To check which roles are part of another role use `\drg`:

```sql
postgis_in_action=# \drg
                     List of role grants
 Role name |   Member of    |       Options       | Grantor  
-----------+----------------+---------------------+----------
 enrico    | book_authors   | INHERIT, SET        | postgres
 luca      | book_authors   | INHERIT, SET        | postgres
 luca      | book_reviewers | ADMIN, INHERIT, SET | postgres
(3 rows)
```

The password associated with the role is stored encrypted in `pg_authid` table.

```sql
postgis_in_action=# SELECT rolname, rolcanlogin, rolconnlimit, rolpassword
FROM pg_authid WHERE rolname = 'luca';
-[ RECORD 1 ]+------------------------------------------------------------------------------------
rolname      | luca
rolcanlogin  | t
rolconnlimit | -1
rolpassword  | SCRAM-SHA-256$4096:/7T3+4tGUarFJkHzWxg9Tg==$TIIQvi6l6vI/XIzKCkvcPSqam6FGHvX5yyqACgfMCZo=:66hr5IXX9dFUJm0OhKTQstrT0BJDNl7cOYQ55gJr7IQ=

postgis_in_action=# 
```

## Managing incoming connections at the role level

When a new connection is established to a cluster, PostgreSQL validates the incoming request
at the role level. The fact that the role has the LOGIN property is not enough for it to open a new
connection to any database within the cluster. This is because PostgreSQL checks the incoming
connection request against a kind of firewall table, formerly known as host-based access, that
is defined within the `pg_hba.conf` file.

If the table states that the role can open the connection to the specified database, the connection
is granted (assuming it has the LOGIN property); otherwise, it is rejected.

Every time you modify the `pg_hba.conf` file, you need to instruct the cluster to reload the new
rules via a `HUP` signal or by means of a reload command in `pg_ctl`.

It is worth noting that a superuser role can instrument the cluster to reload the configuration
by means of an SQL statement. Calling the special function `pg_reload_conf()` will perform the
same action as issuing a reload to `pg_ctl`:

```sql
postgres=# SELECT pg_reload_conf();
pg_reload_conf
----------------
t
```

### The syntax of pg_hba.conf

```text
<connection-type> <database> <role> <remote-machine> <auth-method>
```

### Order of rules in pg_hba.conf

The order by which the rules are listed in the pg_hba.conf file matters. The first rule that satisfies
the logic is applied, and the others are skipped.

### Inspecting pg_hba.conf rules

The `pg_hba.conf` file contains the rules applied to the incoming connections, but since this file
could be changed manually without making the cluster reload it, how can you be sure of which
rules are applied at the moment? PostgreSQL provides a special catalog named `pg_hba_file_rules`
that shows which rules have been applied to the cluster.

```sql
postgis_in_action=# SELECT line_number, type,
database, user_name,
address, auth_method
FROM pg_hba_file_rules;
 line_number | type  |   database    | user_name |  address  |  auth_method  
-------------+-------+---------------+-----------+-----------+---------------
         117 | local | {all}         | {all}     |           | trust
         119 | host  | {all}         | {all}     | 127.0.0.1 | trust
         121 | host  | {all}         | {all}     | ::1       | trust
         124 | local | {replication} | {all}     |           | trust
         125 | host  | {replication} | {all}     | 127.0.0.1 | trust
         126 | host  | {replication} | {all}     | ::1       | trust
         128 | host  | {all}         | {all}     | all       | scram-sha-256
(7 rows)

postgis_in_action=# 
```





